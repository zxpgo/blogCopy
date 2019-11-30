---

title: Content Providers简介
date: 2018-12-04 18:52:14
tags: APP开发
categories: APP开发

---

如果直接调用SQLite数据库，很容易引入错误，假如Activity中有错别字，就会将无效输入插入到数据库中。可以通过引入一个名为Content Provider的概念，作为数据库和活动之间的一个层。使用其有很多好处，可以用它确保输入数据库中的数据是有效的。介绍它，必须介绍URI、UriMatcher、Content Resolver。<!--more-->

### Content Providers存在的必要性

Content Providers可帮助我们管理对有结构的数据集的访问，它可以作为数据源和UI代码之间很好的抽象层。这个抽象层可以添加数据验证，帮助我们修改数据存储的方式，而UI代码始终保持不变。

![](https://i.imgur.com/PGBjPre.png)

Content Providers可以与其他框架类完美结合。

为了更清楚说明这一点，继续讨论宠物列表。当宠物列表添加或被删除，我们希望UI显示最新消息时，我们每次都必须调用query和insert方法。取代这种繁琐工作的方法就是，利用一个名为Cursor Loader框架类。每当有宠物添加到列表或被删除时，列表会借助Cursor Loader始终处于更新状态。

因为Cursor Loader会在数据发生变更时自动进行检查，并在确定发生了数据变更后自动更新列表。Cursor Loader能与我们前面讨论的ListView和Cursor Adapter协作。从而在列表中显示所做的更改。而实现Cursor Loader需要用到Content Provider。所以Content Provider和Cursor Loader一起为我们省了很多工作，使我们不需要在发生数据更改时，一次次手动执行查询。

它还能与主屏幕小部件搭配使用。这些部件的名称为同步适配器，能将我们的数据同步到云，并为应用提供搜索建议。

另一个重要的优势是，分享数据。当应用中存在文本数据或文件时，其他应用是无法访问的。不过，可以通过Content Provider将我们的数据暴露给其他应用。这样，其他应用可以使用querty、insert、update和delete方法与provider交互从而访问我们的信息。

当然，这样做存在一些隐患。因为可能不希望某个应用访问和修改所有的应用数据，而且遇到恶意应用或许会删掉整个数据库表。所以，向其他应用暴露数据还需谨慎，处理不当可能会有危险。不过，开发者不同担心，Content Provider能以非常安全的方式管理数据访问，如果想要访问其他应用，提示用户授权访问数据的特定权限Content Provider可以帮助予以执行，而未获得权限的其他应用将无法访问数据。

总之，Content Providers有三大优点：

- 作为数据源和UI代码之间的抽象层，可以很好地进行数据验证；
- 帮助我们与其他框架类完美协助；
- 帮助我们轻松与其他应用分享数据。


一个应用向其他应用请求特定的数据，它会参考contacts contract，并创建特定内容URI请求。内容URI唯一标识一组数据，类似于Web URI地址唯一标识网络上的唯一网站。

![](https://i.imgur.com/VVm5Srq.png)


Android系统根据我没在内容URI中传入的信息，知道Google+要向contacts app通信。给定这些信息，它会判断哪个content provider和应用可以处理请求。


Android上还有一个东西，叫做content resolver，它以URI为输入，来决定应该由哪种content provider处理我们的请求，判断秘诀就在于内容主机名。内容主机名是URI的一部分，它会告诉content resolver应由哪个provider来处理请求，内容主机名与网络URL的部分相似。

![](https://i.imgur.com/iNXhq2J.png)

所以，可以得到所有这些其他应用实际上直接与content resolver通信，而非provider。content resovler会暴露这些方法：query()、insert()、update()和delete()。它对provider调用适当的方法，后者相应地调用适当的provider方法对数据库进行操作。

在一个应用中的实现：

![](https://i.imgur.com/NTDjimI.png)


其中Content Provider是一个抽象类。



### URI


与Provider交流时，需要告诉它两件事，第一，你想要执行什么操作；第二，在这个操作中使用什么数据。

URI代表同一资源标识符。它可以标识出我们感兴趣的资源的名称、位置。URL是URI的子集，URL代表统一资源定位符，它会给出某个文件或数据在网站上的具体位置。内容URI主要用来标识Provider中的数据，它可以指向数据库的某个部分，比如单个行、单个表或一组表，它也可以指向文件，比如文本文件、照片或其他媒体文件。

![](https://i.imgur.com/smL8VqY.png)

内容URI由三个部分组成：

- scheme：`content://`，这是Android应用中URI的标准开头。
- 主机名：最重要的部分，它指定我们要使用的content Provider，称为内容主机名，它的主要作用是指定我们要进行通信的content Provider类。
- 数据类型：指定了我们要执行操作的数据。

存在两种：

- 对于整个表进行操作：这时数据类型为表名，如下：

![](https://i.imgur.com/1XJKbpK.png)

- 对某行进行操作：使用另一种URI，在表名后面跟上具体行的ID，如下：

![](https://i.imgur.com/AHCV6sg.png)

汇总：

![](https://i.imgur.com/rbG8DM8.png)


### URI Matcher

Provider为了决定如何处理Content Resolver的请求，会使用URI matcher。URI matcher会确定传递给它的URI属于哪种类型。然后，我们可以用这个信息决定要进入的分支，比如，第一分支要对整个表进行操作；另一个分支，对某个行禁止操作。这样，我们就确切地知道了需要操作的数据库表。

![](https://i.imgur.com/a0QZ6j0.png)

![](https://i.imgur.com/j2Lj591.png)


URI Matcher用于将URI与整数代码相匹配，使用URI matcher需要实现的两个步骤：

- 第一步是用Content Provider可接受的URI模式设置UriMatcher，并向每个模式分配一个整数代码；
- 第二步需要调用一个UriMatcher.match(Uri)方法传入一个Uri，返回对应的整数代码（前提有与之匹配的模式），如果不匹配它会给我们提示。

实现示例：

![](https://i.imgur.com/e8FpgWv.png)

其中CONTACTS和CONTACTS_ID是全局常量。sUriMatcher是一个静态全局UriMatcher对象。

	private static final UriMatcher sUriMatcher = new UriMatcher(UriMatcher.NO_MATCH);
:

    private static final int PETS = 100;
    private static final int PETS_ID = 101;

    private static final UriMatcher sUriMatcher = new UriMatcher(UriMatcher.NO_MATCH);

    static {
        sUriMatcher.addURI(PetContract.CONTENT_AUTHORITY,PetContract.PATH_PETS, PETS);

        sUriMatcher.addURI(PetContract.CONTENT_AUTHORITY,PetContract.PATH_PETS + "/#", PETS_ID);
    }


### 查询query()


![](https://i.imgur.com/NMAq3qI.png)


query方法共分为三部分：

- 首先，需要获得数据库对象；
- 将URI发送到URI matcher;
- 帮我们决定选择哪条分支。

![](https://i.imgur.com/Vnx0lcK.png)

具体实现代码：

![](https://i.imgur.com/Oib8Er5.png)


代码：

 	@Override
    public Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs,
                        String sortOrder) {
        SQLiteDatabase database = mDbHelper.getReadableDatabase();

        Cursor cursor;

        int match = sUriMatcher.match(uri);
        switch (match){
            case PETS:
                cursor = database.query(PetContract.PetEntry.TABLE_NAME, projection,selection,selectionArgs,null, null,sortOrder);
                break;
            case PETS_ID:
                selection = PetContract.PetEntry._ID + "=?";
                selectionArgs = new String[] {String.valueOf(ContentUris.parseId(uri))};
                cursor = database.query(PetContract.PetEntry.TABLE_NAME, projection, selection, selectionArgs,
                        null, null, sortOrder);
                break;
                default:
                    throw new IllegalArgumentException("Cannot query unknown URI + " + uri);
        }

        return cursor;
    }

该方法的调用：

  	Cursor cursor = getContentResolver().query(PetContract.CONTENT_URI,
								                projection,
								                null,
								                null,
								                null);

### 插入insert 


![](https://i.imgur.com/MxUs9g9.png)

实现步骤：

![](https://i.imgur.com/cwuH84y.png)

实现代码：

![](https://i.imgur.com/nyUlqb1.png)


### 更新Update


根据来自UriMatcher的结果，PETS和PET_ID case均支持。在PETS case中，调用者想要按照selection和selectionArgs 更新 pets 表中的多个行。 在PET_ID case中，调用者想要更新特定宠物。要写入数据库中的新值包含在传入方法的 ContentValues 对象中。

![](https://i.imgur.com/6kVNAVI.png)


实现流程：

![](https://i.imgur.com/PcSVuUW.png)



### 删除delete




### gettype

getType(Uri uri) 方法。此方法的用途是返回描述输入 URI 中存储的数据类型的字符串。该字符串为 MIME 类型，也称为内容类型。