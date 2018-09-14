![Image](assets/jetpack.jpg)

# Ultimate Android Jetpack Reference

![Android](https://img.shields.io/badge/Android-Jetpack-green.svg?longCache=true&style=flat-square)   ![ARCore](https://img.shields.io/badge/Android-Reference-green.svg?longCache=true&style=flat-square)   ![In Progress](https://img.shields.io/badge/In--progress-true-green.svg?longCache=true&style=flat-square) <br />

> What's Android Jetpack

Android Jetpack is the next generation of components and tools along with Architectural guidance designed to accelerate Android Development. The main reasons are:
* Build high quality, robust apps
* Eliminate boilerplate code
* Accelerate development

#### Show Some :heart: by

[![GitHub stars](https://img.shields.io/github/stars/SyamSundarKirubakaran/Android-Jetpack.svg?style=social&label=Star)](https://github.com/SyamSundarKirubakaran/Android-Jetpack) [![GitHub followers](https://img.shields.io/github/followers/SyamSundarKirubakaran.svg?style=social&label=Follow)](https://github.com/SyamSundarKirubakaran) [![Twitter Follow](https://img.shields.io/twitter/follow/bugscripter.svg?style=social)](https://twitter.com/bugscripter)

## Categories

* [Architecture components](#architecture-components)
  - [Room](#room)
  - [Data Binding](#data-binding)
  - [Lifecycles](#Lifecycles)
  - [LiveData](#LiveData)
  - [Navigation](#Navigation)
  - [Paging](#Paging)
  - [ViewModel](#ViewModel)
  - [WorkManager](#WorkManager)
* Foundation components
  - AppCompat
  - Android KTX
  - Multidex
  - Test
* Behavior Components
  - Download manager
  - Media & playback
  - Notifications
  - Permissions
  - Sharing
  - Slices
* UI components
  - Animation & transitions
  - Auto
  - Emoji
  - Fragment
  - Layout
  - Palette
  - TV
  - Wear OS by Google
  
## Architecture Components

* <b>Room</b>
    - Fluent SQLite database access using Object Mapping Technique.
    - Need:
        - Less Boiler Plate code .i.e., Sends data to the SQLite database as Objects and not as Content Values and returns the result of executing a query as Objects as Cursors. 
        - Compile time validation of SQLite Queries.
        - Support for Observations like Live Data and RxJava
    - Makes use of Annotations for it's functioning and makes the API simple and neat to use.
    - Annotations:
        - <b>@Entity:</b> Defines the schema of the database table.
        - <b>@dao:</b> Stands for Database Access Object, used to perform read or write operations on the database
        - <b>@database:</b> As a database Holder. Creates a new Database based on supplied conditions, if it already exists it establishes a connection to the pre-existing database.
    - Code:
        - <b>@Entity:</b>
          ```java
          @Entity(tableName = "word_table")
          public class Word {
                @PrimaryKey
                @NonNull
                @ColumnInfo(name = "word")
                private String mWord;
                public Word(String word) {this.mWord = word;}
                public String getWord(){return this.mWord;}
           }
          ```
          Most of the annotations are self-explanatory where the <b>@Entity</b> takes in and declares the tableName that's about to be created (if tableName is not mentioned the table takes up the name of the class, here in this case : Word) and the class also has a number of optional attributes like setting the column name(if node specified it takes up the name of the varibale), It's important to note that the all the declarations inside the entity class are considered as columns of the database.(Here "one" .i.e., mWord). You can make use of <b>@Ignore</b> annotation to ignore a declaration as a column in your database. 
        - Room should have access to the member varaibles of the Entity class .i.e., it should be able to set or reset or update the value stored in a member variable and hence note that though the member variable is declared as private there is a public constructor and a getter to access the member variable.
        - <b>@dao:</b>
            ```java
                @Dao
                public interface WordDao {
                    @Insert
                    void insert(Word word);
                    @Query("DELETE FROM word_table")
                    void deleteAll();
                    @Query("SELECT * from word_table ORDER BY word ASC")
                    List<Word> getAllWords();
                }
            ```
            Note that Dao should be an <b>abstract class</b> or <b>an interface</b>. The remaining are self explanatory and as said earlier notice that the querying out from the database returns a list of objects in this case .i.e., List<Word> and no a Cursor.<br>
        - If you replace `List<Word>` by `LiveData<List<Word>>` then it happens to return an updated list of objects for every change in the database. This is one of the prime principles of <b>LiveData</b>
        - <b>@database:</b>
            ```java
                @Database(entities = {Word.class}, version = 1)
                public abstract class WordRoomDatabase extends RoomDatabase {
                    public abstract WordDao wordDao();
                }
            ```
            Note that this has to be <b>abstract</b> and extend the class <b>RoomDatabase</b>. Entities contain the database tables that has to be created inside the database and there should be an abstract getter method for <b>@doa</b>
        - <b>Database Connection:</b>
            ```java
                WordRoomDatabase db = Room.databaseBuilder(context.getApplicationContext(),
                            WordRoomDatabase.class, "word_database")
                            .fallbackToDistructiveMigration()
                            .build();
            ```
            Here, `.fallbackToDistructiveMigration()` is an optional attribute that signifies if the database schema is changed wipe out all the data existing in the currently existing database.
            Creating multiple instances of the database is expensive and is not recommended and hence keeping it as a singleton(which can have only one instance of the class) can come in handy.
        
          
        
        

