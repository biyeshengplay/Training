# 保存数据到数据库
对于重复或者结构化的数据保存到数据库中是最适合的，例如联系人信息。课程默认你已经对普通的SQL数据库很熟悉了，然后帮助你入门
Android上的SQLite数据库。你需要使用的Android上数据库的api在android.database.sqlite包下都可以找到。

## 定义 Schema(架构) 和 Contract
数据库的主要原则之一就是Schema:关于数据库改怎样被组织的正式的声明。 schema反应在你用于创建数据库的sql语句中。你会发现给Schema
创建一个伙伴类是有帮助的，它被称为Contract类，它会以系统的自我记录的方式明确指定schema的布局。

一个contract类是一个用来定义URI名、表名、列名的容器。contract类允许你在同包下的所有其他类中使用。这使你可以在一个地方修改
列名时将改动的生效范围贯穿你的代码。

组织一个contract类的一个好方法是把会在数据库全局有效的定义放在类的根级。然后为每个表创建内部类来列举它的列。

注释：通过实现BaseColumns接口，你的内部类能够继承一个主键字段叫做 _ID,一些Android类例如cursor adaptors会希望含有这一
字段。这个字段不是强制规定要有的，但是它能够帮你的数据库可以和Android框架层和谐工作。

例如，这个代码片段为一个表定义了表名和列名

public final class FeedReaderContract {
    //为了阻止某些意外的实例化contract类的行为，给它一个空构造方法。
    public FeedReaderContract() {}

    /* 定义表内容的内部类 */
    public static abstract class FeedEntry implements BaseColumns {
        public static final String TABLE_NAME = "entry";
        public static final String COLUMN_NAME_ENTRY_ID = "entryid";
        public static final String COLUMN_NAME_TITLE = "title";
        public static final String COLUMN_NAME_SUBTITLE = "subtitle";
        ...
    }
}
## 使用SQL Helper定义数据库
一旦你已经定义了你的数据库的样子，你应该实现创建和维护数据库和表的方法。这有一些创建和删除表的标准语句：

private static final String TEXT_TYPE = " TEXT";
private static final String COMMA_SEP = ",";
private static final String SQL_CREATE_ENTRIES =
    "CREATE TABLE " + FeedEntry.TABLE_NAME + " (" +
    FeedEntry._ID + " INTEGER PRIMARY KEY," +
    FeedEntry.COLUMN_NAME_ENTRY_ID + TEXT_TYPE + COMMA_SEP +
    FeedEntry.COLUMN_NAME_TITLE + TEXT_TYPE + COMMA_SEP +
    ... // CREATE 命令的任何其他选项
    " )";

private static final String SQL_DELETE_ENTRIES =
    "DROP TABLE IF EXISTS " + FeedEntry.TABLE_NAME;

就像你保存在设备的内部存储的文件一样，Android把数据库存储在和应用相关的私有磁盘空间中。你的数据库是安全的，因为默认情况下
这个区域对其他的应用是不可访问的。

一个有用的api集合可以从SQLiteOpenHelper类中获得。当你使用这个类来获取你的数据库的引用时，系统只会在被需要而不是应用启动时，
执行可能会长时间运行的创建和更新数据库的操作。所有你需要做的就是调用getWritableDatabase() 或者 getReadableDatabase()。

注释：因为他们能够长时间运行，所以要确保在后台线程（例如AsyncTask 或者 IntentService）中调用getWritableDatabase()或
getReadableDatabase()方法。

为了使用SQLiteOpenHelper，创建一个子类重写onCreate(), onUpgrade() and onOpen()回调方法。你可能也想实现一下onDowngrade()
方法，但这个不强制要求。

例如，这就是一个使用了上面显示的命令的SQLiteOpenHelper的实现：

public class FeedReaderDbHelper extends SQLiteOpenHelper {
    // 如果要修改数据的schema（结构）, 必须增加数据库版本数
    public static final int DATABASE_VERSION = 1;
    public static final String DATABASE_NAME = "FeedReader.db";

    public FeedReaderDbHelper(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }
    public void onCreate(SQLiteDatabase db) {
        db.execSQL(SQL_CREATE_ENTRIES);
    }
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        //这个数据库只是线上数据的一个缓存，所以它的升级策略就是简单的丢弃原来数据重新开始
        db.execSQL(SQL_DELETE_ENTRIES);
        onCreate(db);
    }
    public void onDowngrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        onUpgrade(db, oldVersion, newVersion);
    }
}

为了访问数据库，要实例化SQLiteOpenHelper的子类：

FeedReaderDbHelper mDbHelper = new FeedReaderDbHelper(getContext());


## 把信息放入数据库

通过传递一个ContentValues对象给 insert()方法来插入数据到数据库中：

// 以写模式获取数据仓库
SQLiteDatabase db = mDbHelper.getWritableDatabase();

// 创建一个新的map values,map的key就是列名
ContentValues values = new ContentValues();
values.put(FeedEntry.COLUMN_NAME_ENTRY_ID, id);
values.put(FeedEntry.COLUMN_NAME_TITLE, title);
values.put(FeedEntry.COLUMN_NAME_CONTENT, content);

// 插入一个新的行，返回就是新行的主键值
long newRowId;
newRowId = db.insert(
         FeedEntry.TABLE_NAME,
         FeedEntry.COLUMN_NAME_NULLABLE,
         values);

insert()方法的第一个参数指的是表名。第二个参数提供了一个列名，这个列名表示了Android框架在ContentValues为空的情况下可以
这一列插入NULL（如果你把这个参数设置为“null”，那么在没有values时框架将不会插入行）。

## 从数据库中读取信息
从数据库中读取要用query（）方法，参数为你的selection具体的或者期望的列。这个方法结合了insert()和update（）方法的元素，除了
你定义的列名是你想要获取的列表，而不是要插入的数据。查询的结果是返回一个Cursor对象。

SQLiteDatabase db = mDbHelper.getReadableDatabase();

// projection声明指定了数据库中哪些列会在查询后真正被使用
String[] projection = {
    FeedEntry._ID,
    FeedEntry.COLUMN_NAME_TITLE,
    FeedEntry.COLUMN_NAME_UPDATED,
    ...
    };

// 你希望结果在返回的Cursor中以怎样的方式排序
String sortOrder =
    FeedEntry.COLUMN_NAME_UPDATED + " DESC";

Cursor c = db.query(
    FeedEntry.TABLE_NAME,  // The table to query
    projection,                               // 需要返回的列
    selection,                                // 用于Where条件的列
    selectionArgs,                            // 用于Where条件的值
    null,                                     // 不用重新分组行数据
    null,                                     // 行数据的分组过滤条件为空
    sortOrder                                 // 排列顺序
    );

为了查看cursor中的行数据，需要使用Cursor移动的方法，这是在你开始读数据之前必须要调用的。通常你应该通过调用moveToFirst()
方法开始，这个方法会把阅读点放在查询结果的第一个条目。对于每一个行，你都可以通过调用Cursor的get方法（例如getString() 或 getLong()）
中的一个来读取列值。对于每一个get方法，你必须传递一个你想要查询的列的索引位置值给它，这个索引值你可以通过调用getColumnIndex()
或者getColumnIndexOrThrow()方法来获得。例如：

cursor.moveToFirst();
long itemId = cursor.getLong(
    cursor.getColumnIndexOrThrow(FeedEntry._ID)
);


## 从数据库中删除信息
从表中删除行，你需要提供精确标识行的selection语句。数据库api 提供了精确创建selection语句的机制来防止sql注入攻击。
这个机制将selection格式分成一个selection条件和一个selection参数。条件定义了列的样子，它也允许你结合一些列测试。参数
是要绑定到条件的测试值。因为这个结果的处理不会和对普通的sql语句的处理一样，所以不会受到sql注入攻击。

//定义查询语句的where 部分
String selection = FeedEntry.COLUMN_NAME_ENTRY_ID + " LIKE ?";
//按照占位的顺序指定参数值
String[] selectionArgs = { String.valueOf(rowId) };
//发布 SQL 语句.
db.delete(table_name, selection, selectionArgs);


## 更新数据库
当你想要修改数据库值得一个子集时，使用update（）方法。

表的更新结合了insert（）中的content values 语法和delete（）方法中的where语法。

SQLiteDatabase db = mDbHelper.getReadableDatabase();

// 某一列的新值
ContentValues values = new ContentValues();
values.put(FeedEntry.COLUMN_NAME_TITLE, title);

// 哪一行需要更新，基于这个ID
String selection = FeedEntry.COLUMN_NAME_ENTRY_ID + " LIKE ?";
String[] selectionArgs = { String.valueOf(rowId) };

int count = db.update(
    FeedReaderDbHelper.FeedEntry.TABLE_NAME,
    values,
    selection,
    selectionArgs);