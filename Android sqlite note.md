### Android sqlite operations

This is a personal note about andriod sqlite operations. If you read this note and get some help, that's cool. 
Note that, this is not a sqlite tutorial, any questions please let me know.
#### Concepts

Schema : Schema simply can be considered as a structure that defines the objects in the database, 
         describes how real-world entities are modeled in the database.
         
Contract : Contract class, class of schema management. To define a contract class and let it be the only entry 
           of a certain schema, let tables colums etc easy to be managed since you can change at one place and
           apply it everywhere. Best practice is creating a contract class for the whole database and inner class
           for each table.
           
#### Operations

##### Schema definition example from android developer
```
public final class FeedReaderContract {
    // To prevent someone from accidentally instantiating the contract class,
    // make the constructor private.
    private FeedReaderContract() {}

    /* Inner class that defines the table contents */
    public static class FeedEntry implements BaseColumns {
        public static final String TABLE_NAME = "entry";
        public static final String COLUMN_NAME_TITLE = "title";
        public static final String COLUMN_NAME_SUBTITLE = "subtitle";
    }
}
```

##### The SQL Helper

Android provide Sqlite API for developers that is SQLiteOpenHelper. You can use this class to obtain references to your database except
during app startup(because it may be long-run, so you alse need use AsyncTask or IntentService).

Use "getWritableDatabase()" to get object for db write stuff and the "getReadableDatabase()" for read stuff.

You need implement SQLiteOpenHelper for your own class to operate db, here is an example :
```
public class FeedReaderDbHelper extends SQLiteOpenHelper {
    // If you change the database schema, you must increment the database version.
    public static final int DATABASE_VERSION = 1;
    public static final String DATABASE_NAME = "FeedReader.db";

    public FeedReaderDbHelper(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }
    public void onCreate(SQLiteDatabase db) {
        db.execSQL(SQL_CREATE_ENTRIES);
    }
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        // This database is only a cache for online data, so its upgrade policy is
        // to simply to discard the data and start over
        db.execSQL(SQL_DELETE_ENTRIES);
        onCreate(db);
    }
    public void onDowngrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        onUpgrade(db, oldVersion, newVersion);
    }
}
```
onCreate(), onUpgrade() and onOpen() callback method are needed to be overrided and onDowngrade() is useful but it is optional.

To access to db, just do this:
```
FeedReaderDbHelper mDbHelper = new FeedReaderDbHelper(getContext());
```

##### Create and delete example from android developer

```
private static final String TEXT_TYPE = " TEXT";
private static final String COMMA_SEP = ",";
private static final String SQL_CREATE_ENTRIES =
    "CREATE TABLE " + FeedEntry.TABLE_NAME + " (" +
    FeedEntry._ID + " INTEGER PRIMARY KEY," +
    FeedEntry.COLUMN_NAME_TITLE + TEXT_TYPE + COMMA_SEP +
    FeedEntry.COLUMN_NAME_SUBTITLE + TEXT_TYPE + " )";

private static final String SQL_DELETE_ENTRIES =
    "DROP TABLE IF EXISTS " + FeedEntry.TABLE_NAME;
```
##### 
