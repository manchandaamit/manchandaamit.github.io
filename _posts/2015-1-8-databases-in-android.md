---
layout: post
title: Databases in Android
tagline: A way to store and retrieve persistent data in android
visible: 1
---

All of you must be done with the first part of the challenge, that is, to complete the form which will be used to take the input data from the user. So now the question you might be thinking will be – how to store this data? Well, it seems there is one very efficient way to do this – Database.
##What is a Database?
With the increasing usability and complexity of the current applications, storing useful data has evolved from being an alternative to a necessity for developers. A database is a collection of information that is organized so that it can easily be accessed, managed, and updated. In computing, the most prevalent approach is the [relational database](https://www.google.co.in/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0CB8QFjAA&url=http://en.wikipedia.org/wiki/Relational_database&ei=3YCuVL6uM4OPuATViIKYAw&usg=AFQjCNEnTGY8PBHbHDpPw9B4YR6kdukcLg&sig2=vNAeN_Fy_rv0cunxXXJKMQ&bvm=bv.83339334,d.c2E), a tabular database in which data is defined so that it can be reorganized and accessed.

 
##What is SQLite ?

SQLite is an Open Source database. SQLite supports standard relational database features like SQL syntax, transactions and prepared statements. SQLite supports the data types **TEXT** (similar to String in Java), **INTEGER** (similar to long in Java) and **REAL** (similar to double in Java). All other types must be converted into one of these fields before getting saved in the database.

(If you are not familiar with basic SQL syntax, a quick reference can be found [here](http://www.w3schools.com/sql/)) 
  
## SQLite in Android 

 SQLite is embedded into every Android device. Using an SQLite database in Android does not require a setup procedure or administration of the database. Access to an SQLite database involves accessing the file system. This can be slow. Therefore it is recommended to perform database operations asynchronously.
If your application creates a database, this database is by default saved in the directory *DATA/data/APP_NAME/databases/FILENAME*.
The **android.database** package contains all necessary classes for working with databases. The android.database.sqlite package contains the SQLite specific classes.

## [The Cursor Class](http://developer.android.com/reference/android/database/Cursor.html) ##

A query in android returns a Cursor object. A Cursor represents the result of a query and basically points to one row of the query result. This way Android can buffer the query results efficiently; as it does not have to load all data into memory.

 - To get the number of elements of the resulting query use the **getCount()** method.
 - To move between individual data rows, you can use the **moveToFirst()** and **moveToNext()** methods. 
 - The **isAfterLast()** method allows to check if the end of the query result has been reached.
 - A Cursor needs to be closed with the **close()** method call. It is a good practice to close a cursor object once you have retrieved the values for that particular object inside your method.  
 
## [SQLiteDatabase Class](http://developer.android.com/reference/android/database/sqlite/SQLiteDatabase.html) ##

SQLiteDatabase is the base class for working with a SQLite database in Android and provides methods to open, query, update and close the database. An object of this class is used to call methods like insert(), update()  delete() rawQuery() query() etc.
In addition it provides the execSQL() method, which allows to execute an SQL statement directly.

##Some important methods of SQLiteDatabase Class : ##

 - [execSQL()](http://developer.android.com/reference/android/database/sqlite/SQLiteDatabase.html#execSQL%28java.lang.String%29) method:
 
 This method takes an SQL query in the form of a String as parameter and executes it on the current database object.
Syntax :

        databaseObject.execSQL(String SQLQuery);

 - [rawQuery()](http://developer.android.com/reference/android/database/sqlite/SQLiteDatabase.html#rawQuery%28java.lang.String,%20java.lang.String%5B%5D%29) method:

 The rawQuery method takes an SQL query in it’s original syntax and a String array specifying the ‘Where’ parameters as input. All parameter places should be designated with '?'.This method runs the sql query and returns the selected rows in a Cursor object. The following gives the syntax of a rawQuery() call to fetch rows from a table 'books' with given id:
 
        Cursor cursor = databaseObject.rawQuery("SELECT * FROM books 
    WHERE id = ?",new String[] { id });

The following piece of code allows you to test whether the query returned an empty set  and it moves the cursor to the first result (when the set is not empty). This code should be always added while executing rawQueries

        if(cursor!=null)
        {
            cursor.moveToFirst();
        }

 - [query()](http://developer.android.com/reference/android/database/sqlite/SQLiteDatabase.html#query%28java.lang.String,%20java.lang.String%5B%5D,%20java.lang.String,%20java.lang.String%5B%5D,%20java.lang.String,%20java.lang.String,%20java.lang.String,%20java.lang.String%29) method:
  
  This method is analogous to the rawQuery() method but requires a particular set of values to be passed as parameters. The following gives an example of a query() call. Query method is preferred as compared to rawQuery method as it protects you from SQL injections, builds the query for you and prevents having too many string concatenations.
  
  An example of selecting a row with given id and title from the table 'books' using query method :
  
        Cursor cursor = databaseObject.query(books, new String[] { KEY_ID, KEY_TITLE,  }, null, null, null, null, null);

##[The ContentValues Class](http://developer.android.com/reference/android/content/ContentValues.html) ##

The ContentValues class is used to store a set of values  and is required for passing and receiving data variables of our table columns. 

Syntax :

        ContentValues values = new ContentValues();
        values.put(KEY_1, abc );
        values.put(KEY_2, xyz); 


## Implementation in Android app ##

The first step towards creating and upgrading a database in your android app is to create a subclass of the SQLiteOpenHelper class.  In the constructor of your subclass you call the super() method of SQLiteOpenHelper, specifying the database name and the current database version.
Let us understand the procedure to create an Android database by creating a table books which stores title and author of a book as data along with an id assigned to each.
In this class you need to override the following methods to create and update your database:

 - 	**onCreate()** - is called by the framework, if the database is accessed but not yet created. The basic purpose of this method is to create the table with the required data fields using the execSQL method.
 
 - 	**onUpgrade()** - called, if the database version is increased in your application code. This method allows you to update an existing database schema or to drop the existing database and recreate it via the onCreate() method.

Now let's understand how to implement the methods with an example of a database containing a table storing information regarding books, namely title of the book and its author. 

###Adding a book  ###

For adding a book we store the values of title and author in a ContentValues object and insert it using the insert method of SQLiteDatabase class.

        ContentValues values = new ContentValues();
        values.put("title", String_Title); 
        values.put("author", String_Author); 
        db.insert(TABLE_BOOKS, null,   values);

### Fetching a book ###

The rawQuery method of the SQLiteDatabase Class is invoked to select a book title with given id.

        Cursor cursor = db.rawQuery("SELECT title, author FROM books 
    WHERE ID = ?",new String[] { id });

### Updating a book ###

We again use the rawQuery method with proper syntax to update our column values of a book with a given id.

        db.rawQuery("UPDATE books SET title = ?, author = ?,
    WHERE id = ?,new String[] { title,author,id });

### Deleting a book ###

The delete method of SQLiteDatabase class is invoked to delete a book with the corresponding id.

        db.delete(TABLE_BOOKS, KEY_ID+" = ?",  new String[] { BookId});


This completes our discussion of databases and it's implementation in android applications.
For more resources and detailed explanation you can always refer to the following links:

1) [Vogella](http://www.vogella.com/tutorials/AndroidSQLite/article.html)

2) [Android Developers](http://developer.android.com/training/basics/data-storage/databases.html)

3) [StackOverflow](http://stackoverflow.com/questions/3037767/create-sqlite-database-in-android) 




