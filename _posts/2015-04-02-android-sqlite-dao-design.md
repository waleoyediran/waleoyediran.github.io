---
layout: post
title: Android SQLite DAO Design
author: Oyewale Oyediran
feature-image: tux-mail-1ty.gif
tags:
- android-dev
- android
- java
- sqlite
- database
- architecture
- mobile
---

In this post, i will be describing a pattern of designing a good SQLite data-access layer for Android.

When your application needs to offer complex data to other applications, the appriopriate approach is to use [Content Providers]. But many simple applications do not need to copy data to other applications. After using various approaches to data-access design over the years, I have been highly influenced by the approach used by the [Ushahidi Android] client.

Consider you want to store rows of Users in SQLite with the POJO described below:

```java
public class User {
   public int id
   public String username;
   public String email;
   public Date createdDate;
}
```

We create an interface class that basically contains the Database schema definition. It contains table name, column names and table creation schema.

```java
public interface IUserSchema {
   String USER_TABLE = "users";
   String COLUMN_ID = "_id";
   String COLUMN_USER_NAME = "user_name";
   String COLUMN_EMAIL = "email";
   String COLUMN_DATE = "created_date";
   String USER_TABLE_CREATE = "CREATE TABLE IF NOT EXISTS "
       + USER_TABLE
       + " ("
       + COLUMN_ID
       + " INTEGER PRIMARY KEY, "
       + COLUMN_USER_NAME
       + " TEXT NOT NULL, "
       + COLUMN_EMAIL
       + " TEXT,"
       + COLUMN_DATE
       + "BIGINT "
   + ")";

   String[] USER_COLUMNS = new String[] { COLUMN_ID, 
      COLUMN_USER_NAME, COLUMN_EMAIL, COLUMN_DATE };
}
```


The CRUD functionality is abstracted in a DbContentProvider class which is inherited by the UserDao class

```java
public abstract class DbContentProvider {
    public SQLiteDatabase mDb;

    public int delete(String tableName, String selection, 
      String[] selectionArgs) {
        return mDb.delete(tableName, selection, selectionArgs);
    }

    public long insert(String tableName, ContentValues values) {
        return mDb.insert(tableName, null, values);
    }

    protected abstract <T> T cursorToEntity(Cursor cursor);

    public DbContentProvider(SQLiteDatabase db) {
       this.mDb = db;
    }

    public Cursor query(String tableName, String[] columns, 
      String selection, String[] selectionArgs, String sortOrder) {

       final Cursor cursor = mDb.query(tableName, columns, 
        selection, selectionArgs, null, null, sortOrder);

       return cursor;
    }

    public Cursor query(String tableName, String[] columns, 
      String selection, String[] selectionArgs, String sortOrder, 
      String limit) {

       return mDb.query(tableName, columns, selection, 
        selectionArgs, null, null, sortOrder, limit);
    }

    public Cursor query(String tableName, String[] columns, 
        String selection, String[] selectionArgs, String groupBy, 
        String having, String orderBy, String limit) {

        return mDb.query(tableName, columns, selection, 
            selectionArgs, groupBy, having, orderBy, limit);
    }

    public int update(String tableName, ContentValues values,
        String selection, String[] selectionArgs) {
        return mDb.update(tableName, values, selection, 
          selectionArgs);
    }

    public Cursor rawQuery(String sql, String[] selectionArgs) {
        return mDb.rawQuery(sql, selectionArgs);
    }
}
```

The basic functions of the User data-access layer could optionally be described in an Interface such as this.

```java
public interface IUserDao {
   public User fetchUserById(int userId);
   public List<User> fetchAllUsers();
   // add user
   public boolean addUser(User user);
   // add users in bulk
   public boolean addUsers(List<User> users);
   public boolean deleteAllUsers();
}
```

Now we create our User Data Access class.

```java
public class UserDao extends DbContentProvider 
   implements IUserSchema, IUserDao {
   
   private Cursor cursor;
   private ContentValues initialValues;
   public UserDao(SQLiteDatabase db) {
      super(db);
   }
   public User fetchUserByID(int id) {
   final String selectionArgs[] = { String.valueOf(id) };
   final String selection = ID + " = ?";
   User user = new User();
   cursor = super.query(USER_TABLE, USER_COLUMNS, selection,
           selectionArgs, COLUMN_ID);
   if (cursor != null) {
       cursor.moveToFirst();
       while (!cursor.isAfterLast()) {
           user = cursorToEntity(cursor);
           cursor.moveToNext();
       }
       cursor.close();
   }

   return user;
   }

   public List<User> fetchAllUsers() {
       List<User> userList = new ArrayList<User>();
       cursor = super.query(USER_TABLE, USER_COLUMNS, null,
               null, COLUMN_ID);
    
       if (cursor != null) {
           cursor.moveToFirst();
           while (!cursor.isAfterLast()) {
               User user = cursorToEntity(cursor);
               userList.add(user);
               cursor.moveToNext();
           }
           cursor.close();
       }
    
       return userList;
    }
    
    public boolean addUser(User user) {
       // set values
       setContentValue(user);
       try {
           return super.insert(USER_TABLE, getContentValue()) > 0;
       } catch (SQLiteConstraintException ex){
           Log.w("Database", ex.getMessage());
           return false;
       }
    }
    
    protected User cursorToEntity(Cursor cursor) {
    
       User user = new User();
    
       int idIndex;
       int userNameIndex;
       int emailIndex;
       int dateIndex;
    
       if (cursor != null) {
           if (cursor.getColumnIndex(COLUMN_ID) != -1) {
               idIndex = cursor.getColumnIndexOrThrow(COLUMN_ID);
               user.id = cursor.getInt(idIndex);
           }
           if (cursor.getColumnIndex(COLUMN_USER_NAME) != -1) {
               userNameIndex = cursor.getColumnIndexOrThrow(
                COLUMN_USER_NAME);
               user.username = cursor.getString(userNameIndex);
           }
           if (cursor.getColumnIndex(COLUMN_EMAIL) != -1) {
               emailIndex = cursor.getColumnIndexOrThrow(
                COLUMN_EMAIL);
               user.email = cursor.getString(emailIndex);
           }
           if (cursor.getColumnIndex(COLUMN_DATE) != -1) {
               dateIndex = cursor.getColumnIndexOrThrow(COLUMN_DATE);
               user.createdDate = new Date(cursor.getLong(dateIndex));
           }
    
       }
       return user;
    }
    
    private void setContentValue(User user) {
       initialValues = new ContentValues();
       initialValues.put(COLUMN_ID, user.id);
       initialValues.put(COLUMN_USER_NAME, user.username);
       initialValues.put(COLUMN_EMAIL, user.email);
       initialValues.put(COLUMN_DATE, user.createdDate.getTime());
    }
    
    private ContentValues getContentValue() {
       return initialValues;
    }

}
```

Having our Data Access Object class setup, we then proceed to create our Database Helper class that provides an handle to the database resource. Its important to increment the DatabaseVersion on every schema change.

```java
public class Database {
   private static final String TAG = "MyDatabase";
   private static final String DATABASE_NAME = "my_database.db";
   private DatabaseHelper mDbHelper;
   // Increment DB Version on any schema change
   private static final int DATABASE_VERSION = 1;
   private final Context mContext
   public static UserDao mUserDao;



   public Database open() throws SQLException {
       mDbHelper = new DatabaseHelper(mContext);
       SQLiteDatabase mDb = mDbHelper.getWritableDatabase();

       mUserDao = new UserDao(mDb);

       return this;
   }

   public void close() {
       mDbHelper.close();
   }

   public Database(Context context) {
       this.mContext = context;
   }


   private static class DatabaseHelper extends SQLiteOpenHelper {
       DatabaseHelper(Context context) {
           super(context, DATABASE_NAME, null, DATABASE_VERSION);
       }

       @Override
       public void onCreate(SQLiteDatabase db) {
           db.execSQL(IUserSchema.USER_TABLE_CREATE);
       }

       @Override
       public void onUpgrade(SQLiteDatabase db, int oldVersion,
          int newVersion) {
           Log.w(TAG, "Upgrading database from version " 
                + oldVersion + " to "
                + newVersion + " which destroys all old data");

           db.execSQL("DROP TABLE IF EXISTS " 
                + IUserSchema.USER_TABLE);
           onCreate(db);

       }
   }

}
```

The Database helper class creates an instance of the DAO class on opening the DB resource
The pattern aslo encourages having one static handle on the database  which could be created in a Application subclass. The DB is opened onCreate of the application and closed onTerminate to avoid leaks.

```java
public class MainApplication extends Application {
   public static final String TAG = MainApplication.class.getSimpleName();
   public static Database mDb;


   @Override
   public void onCreate() {
       super.onCreate();
       mDb = new Database(this);
       mDb.open();
   }

   @Override
   public void onTerminate() {
       mDb.close();
       super.onTerminate();
   }

}
```


Now in any part of your application, you can grab a user by his id like this:

```java
UserEntity user = Database.mUserDao.fetchUserByID(userId);
```

and insert a user into the Database

```java
boolean isSaved = Database.mUserDao.addUser(user)
```

###Credit
Much of the code here is adapted from the Ushahidi Android Client



[Ushahidi Android]:https://github.com/ushahidi/Ushahidi_Android
[Content Providers]:http://developer.android.com/guide/topics/providers/content-provider-creating.html

