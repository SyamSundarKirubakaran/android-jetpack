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
  - [ViewModel](#ViewModel)
  - [LiveData](#LiveData)
  - [Lifecycles](#Lifecycles)
  - [Data Binding](#data-binding)
  - [Paging](#Paging)
  - [Navigation](#Navigation)
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
    - <b>Need:</b>
        - Less Boiler Plate code .i.e., Sends data to the SQLite database as Objects and not as Content Values and returns the result of executing a query as Objects as Cursors. 
        - Compile time validation of SQLite Queries.
        - Support for Observations like Live Data and RxJava
    - Makes use of Annotations for it's functioning and makes the API simple and neat to use.
    - <b>Annotations:</b>
        - <b>@Entity:</b> Defines the schema of the database table.
        - <b>@dao:</b> Stands for Database Access Object, used to perform read or write operations on the database
        - <b>@database:</b> As a database Holder. Creates a new Database based on supplied conditions, if it already exists it establishes a connection to the pre-existing database.
    - <b>Code:</b>
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
          Most of the annotations are self-explanatory where the <b>@Entity</b> takes in and declares the tableName of the table that's about to be created (if tableName is not mentioned the table takes up the name of the class, here in this case : Word) and the class also has a number of optional attributes like setting the column name(if node specified it takes up the name of the varibale), It's important to note that the all the declarations inside the entity class are considered as columns of the database.(Here "one" .i.e., mWord). You can make use of <b>@Ignore</b> annotation to ignore a declaration as a column in your database. 
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
        - <b>Additional Hints</b>
            - Use `@PrimayKey(autoGenerate = true)` to auto generate the primary key, it's usually applied to Integer values and hence starts from 1 and keeps incrementing itself.
            - To pass a value of a member variable into the query make use of `:` .i.e.,
            `@Query("SELECT * from word_table ORDER BY word LIKE :current_word")`
            - Create and Make use of a <b>Repository class</b> to handle the Data Operations. You can handle custom logics such as if you're fetching data from the network but there is no existing connection to the internet, then you'll have to fetch and display the cached data or from dao.
        
* <b>View Model</b>
    - Manage UI-related data in a lifecycle-conscious way.
    - Since it survives configuration changes it has the potential to replace AsyncTask Loaders.
    - <b>Need:</b>
        - High decoupling of UI and Data.
        - No gaint activities with too many responsibilities.
        - UI Controller displays data.
        - ViewModel holds the UI Data.
    - <b>Reason:</b>
    - Say for an instance you're having an Activity that has a number of UI elements and that will have it's state updated based on the user inputs and now when the device undergoes configuration change the data will be lost since the activity has been recreated due to the configuration change. Of course, you can make use of `onSavedInstanceState()` but having forgotten a single field shall lead to inconsistency in the app, here is where <b>ViewModel</b> comes in to play things smart.
    - Here in ViewModel since the UI data is decoupled from the activity lifecycle it can out-live the impact made by configuration changes on the data. Therefore, after recreation of the activity the UI Data can be fetched from the ViewModel to update the UI respectively.
    - <b>Representation:</b><br>
        <p align="center">
            <img src="assets/viewmodel.png">
        </p>
    - <b>Code:</b>
        - Extend from `ViewModel` or `AndroidViewModel`
            ```java
                public class MyViewModel extends ViewModel {
                    // Should contain all the UI Data (As LiveData if possible)
                    // Getters and Setters
                }
            ```
        - Linking `ViewModel` to the Activity
            ```java
                public class MyActivity extends AppCompatActivity {
                    public void onCreate(Bundle savedInstanceState) {
                        // Create a ViewModel the first time the system calls an activity's onCreate() method.
                        // Re-created activities receive the same MyViewModel instance created by the first activity.
                        // ViewModelProvider provides ViewModel for a given lifecycle scope.
                        MyViewModel model = ViewModelProviders.of(this).get(MyViewModel.class);
                        model.getUsers().observe(this, users -> {
                            // update UI
                        });
                    }
                }
            ```
        - <b>Important Note:</b>
            - `View Model` can survive:
                - Configuration Changes
            - `View Model` doesn't survive:
                - Pressing Back .i.e., Destroying the activity.
                - Killing the Activity through Multi-tasking.
            - `View Model` is not a replacement for:
                - Presistence
                - onSavedInstanceState
            - Linked to the `Activity lifecycle` and <b>not</b> with the `App lifecycle`.
            
* <b>Live Data:</b>
    - Notify views when underlying database changes
    - <b>Need:</b>
        - Reactive UI.
        - Updates only on reaching `onStart()` and `onResume()`.
        - Cleans up itself automatically.
        - Allows communication with Database to the UI without knowing about it.
    - <b>Representation:</b><br>
        <p align="center">
            <img src="assets/livedata.png">
        </p>
    - <b>Code:</b>
        - LiveData can be made possible for `String` using `MutableLiveData<String>`
        - To update the UI with real-time data from Live-Data we can make use of a pattern similar to the Observer Pattern.
            ```java
                mModel.getCurrentName().observe(this, new Observer<String>() {
                    @Override
                    public void onChanged(@Nullable final String newName) {
                        // Update the UI, in this case, a TextView.
                        mTextView.setText(newName);
                    }
                });
            ```
        - Here, `mModel` is the ViewModel instance and assume `getCurrentName()` returns a `MutableLiveData<String>` and the observer is set over it which invocates `onChanged()` when the data in the MutableLiveData changes.
        - <b>Arguments:</b>
            - `this` : Specifies the activity that it has the work on .i.e., <b>LifeCycleOwner</b>
            - `Observer<type>` : <b>type</b> depends on the return type of the Live Data.

* <b>Life Cycle</b>
    - <b>Terms:</b>
        - <b>Lifecycle:</b> An Object that defines Android Lifecycle.
        - <b>Lifecycle Owner:</b> It's an interface:
            - Objects with Lifecycles
            - eg. Activities and Fragments.
        - <b>LifecycleObserver:</b> Observes LifecycleOwner.
    - <b>Code:</b>
        - Let's say we want a Listener that has to be Lifecycle aware:
            ```java
                public class MyObserver implements LifecycleObserver {
                    @OnLifecycleEvent(Lifecycle.Event.ON_RESUME)
                    public void connectListener() {
                        ...
                    }
                    @OnLifecycleEvent(Lifecycle.Event.ON_PAUSE)
                    public void disconnectListener() {
                        ...
                    }
                }
                lifecycleOwner.getLifecycle().addObserver(new MyObserver());
            ```
        - Now perform action based on the current state of the Activity or Fragment.
            ```java
                public class MyObserver implements LifecycleObserver {
                    public void enable(){
                        enable = true;
                        if(lifecycle.getState().isAtleast(STARTED)){
                            // action after onStart is reached
                        }
                }
            ```
    - <b>Important Note:</b>
        - Initially Lifecycle Library was kept optional but now it has become a fundamental part of Android development and the AppCompatActivity and Fragment class inherit from the Lifecycle Library out of the box.
            ```kotlin
                class AppCompatActivity : LifecycleOwner
                class Fragment : LifecycleOwner
            ```
            
* <b>Data Binding</b>
    - Declaratively bind observable data to UI elements.
    - It's a proven solution for boiler plate free UIs.
    - <b>Code:</b><br>
        ```kotlin
            data class User(val name: String, ... )
        ```
        ```xml
            <layout>
            <data>
                <variable name="user" type="User"/>
            </data>
            <TextView android:text="@{user.name}"/>
            </layout>
        ```
        - Here the code is self-explanator, Consider you're having a `data  class` in Kotlin that has a number of fields that has the real-time data that has to be updated in the UI then it can be directly parsed in the XML document of the UI by creating an appropriate variable for accessing the <b>data class</b> as shown above.
    - <b>Important Note:</b>
        - They have native support for LiveData and hence LiveData can be used similar to the above shown instance.
        - But this is not enough to observe it since data binding doesn't have a lifecycle. This can be fixed by adding a LifecycleOwner while creating a binding instance.
            ```kotlin
                binding.setLifecycleOwner(lifecycleOwner)
            ```
        - This can help keep the data in the UI upto date with the LiveData.
    
* <b>Paging</b>
    - Gradually load information on demand from your data source.
    - In other words, Lazy loading for Recycler View.
    - <b>Need</b>
        - Convenience
        - Multiple Layer
        - Performance
        - Lifecycle aware
        - Flexible with different background API and Data Structures.
    - If there is a very large list of data to be loaded it's very in-efficient in handling memory       constraints also the source of data can be from different locations like some from <b>cloud</b>    and others from <b>database</b> if the network in not available and so on.<br>  
    - Paging library comes with a `PagedList` class which is a Java List implementation that works       with a data source Asynchronously.
    - Every time you access an item in the PagedList it pulls data from the data source Lazily .i.e.,   on scrolling brings in more data from the data source.
    - One of the most common problems is that, if the data is fetched from data source that has         thousands of rows returned as a result of query these thousand words are kept ready to inflate      it in the UI even though the user can see only 8 - 10 rows of the query result. This is in-         efficient(Note that the recycler view can only help in handling the resorces by recycling the     list item resources and it has nothing to do with the data). Here is where the Paging stands      up by providing data on-demand (when the user scrolls) to the recycler view.
    - <b>Code:</b>
        - Works great if `Room` is used.
        - Paging is a bit compicated to explain with simple code samples since it involves a recycler   view. But we would highly recommend you going through the [Codelab]                           (https://github.com/googlesamples/android-architecture-                                       components/tree/master/PagingWithNetworkSample) for paging and checkout the paging [sample     app](https://github.com/googlesamples/android-architecture-                                   components/tree/master/PagingWithNetworkSample) so that you get a much more clear picture of   it. 
        - It is highly similar to creating a recycler view and involves some changes in the Adapter     and LiveData.
    - <b>Important Note:</b>
        - Paging supports <b>RxJava.</b>
        - <b>ListAdapter</b>
            ListAdapter is a RecyclerView Adapter thet displays a list. 
            ```kotlin
                // Call 1
                listAdapter.submitList(listOf(1,2,3,4))
                // Call 2
                listAdapter.submitList(listOf(1,3,4))
                // when second statement is executed after executing 1st it simply removes '2' because it finds the difference between the two updates in the background thread and perform UI update correspondingly. This makes it efficient.
            ```
        
            
    

    
    
    
          
        
        

