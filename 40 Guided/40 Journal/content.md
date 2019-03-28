# Journal

## Objectives

- Practice with SQLite as a database
- Create nice and intuitive layouts
- Use the Floating Action Button


## Background

For this project, you will create an app that represents a journal. In this app, you should be able to view a list of your journal entries, and of course also add new ones. A journal entry should typically consist of at least a few fields, such as the date/timestamp of the entry, the entry contents and some addition, such as an emoji reprenting your mood at the time of the entry. 

For this app, we will make use of the [Floating Action Button](https://material.io/guidelines/components/buttons-floating-action-button.html), which is in accordance with [Material Design](https://material.io/) standards for the Android Platform. The app will consist of three activities, one that holds a list with a possibility to add a new entry. Upon clicking the Floating Action button, the user should be directed to a second activity containing fields that will allow them to add a new journal entry. Finally, a third activity will show details of the selected journal entry.

You are not forced to use the exact same images (or even restrict yourself to just 4 possible moods), but try and do something nice instead of just plain text to represent the user's mood. 

![App Example](journal-app1.png) ![App Example](journal-app2.png) ![App Example](journal-app3.png)

## Getting started

1.  Create a new Android studio project called **Journal**, using these settings:
    - Choose API 24 (Nougat) unless your own phone has an older operating system
    - Start with an Empty Activity which is called `MainActivity`
    - Leave all other settings unchanged

2.  Create a new, empty repository on the Github website. Name your repository `Journal`.

3.  Now, add a git repository to the project on your computer. Go to Android Studio, and in the menu choose **VCS -> Enable Version Control Integration**. Choose **git** as the type and confirm. This will not change much, but sets us up for the next steps.

    Note: if you get a popup to ask whether you would like to add some file to the repository, answer "No" for now. If you answer "Yes", things may get complicated later on.

4.  Link the local repository to your Github project. Choose **VCS -> Git -> Remotes...**. Add a remote with name "origin".

5.  Android Studio has generated quite a few files for your project already. To add these, let's **commit** and **push** those files to Github. Press **Cmd-K** or **Ctrl-K** to show the Commit Changes screen. There, you should see a long list of "unversioned files". Make sure all checkboxes are selected, enter a commit message `Initial project` and then press the **commit** button. Turn off code analysis.

6.  Press **Cmd-Shift-K** or **Ctrl-Shift-K** to show the Push Commits dialog. Press the **Push** button to send everything to Github.

Your project files should now be visible on Github. If not, ask for help!

## Architecture

Here's a general overview of the app architecture. There will be three activities, as well as a couple of classes that handle database access. Specifically, there's a model class, which represents the core concept of this app: a **journal entry**.

![](journal.png)


## Creating the basic user interface

-   `MainActivity` will contain the list of items and a floating action button.

    -   Change the root layout of the `activity_main.xml` file to a `CoordinatorLayout` as this allows us to add the few components we need and position those in a very simple way. Do this by replacing the opening tag of the root layout (which is probably a `ConstraintLayout`) with `android.support.design.widget.CoordinatorLayout`. If this layout type is not recognised by Android Studio, you might have to add a dependency to your `build.gradle` (Module: app) file located in your Gradle Scripts folder in order to be able to use the `CoordinatorLayout`. At the bottom of the `build.gradle` (Module: app) file, in the `dependencies` section, add: `implementation 'com.android.support:design:27.1.1'` and rebuild your project. The `CoordinatorLayout` should now be available for you to use.

    -   Our journal entries will be contained in a list, so add a `ListView`, to be found under the **Container** section of the palette.
    
    -   Then, add a floating action button, which is listed under the **Design** section of the palette. After you have added it, the button is most likely hovering in the upper left corner. Set the `layout_gravity` to `bottom` &#124; `end` to attach it to the bottom right corner.

> Setting gravity to `end` instead of `right` ensures that the button will actually be attached to the *left* if used on a phone that is set to a language that is read from right to left, like Arabic. Useful!

-   We need to show items in our `ListView` later, and we'll create a separate layout for those.

    -   Create a new layout resource file (remember how?), called `entry_row.xml`, and add the views that will need to be shown for each journal entry.

    -   Think about what your list should show and how. Probably the title of the journal entry is most important. Will you show the "mood", too? And the timestamp?

-   The second activity, called `InputActivity`, should allow the user to input the contents of the journal entry. You might want to change the root layout to a more appropriate choice. Add several `EditText` elements, as well as a button to allow for submission of the entry.

-   The third activity, called `DetailActivity`, should show the full contents of a journal entry in a visually pleasing way. Build the layout as you like, as long as all four attributes of the entry are represented on-screen.


## Handling user interaction

Let's now add the listeners needed to handle user interactions. Make sure that your listeners are never anonymous. Either have them use their own inner class (remember how?), or simply define a method to be called via the `onClick` attribute of the layout.

-   Add a regular `onClick` listener to the floating action button. Use an `Intent` to direct the user to another activity to create a journal entry.

-   Create another listener for the confirmation button in this activity, but leave its body empty for now as we do not have our database implemented yet.

-   Add an `OnItemClickListener` to the `ListView`, as well as an `OnItemLongClickListener`. Again you can leave the actual functionality of the listeners blank for now, as we will implement this later on, when our database is all set up!

> Note that the method implemented by `OnItemLongClickListener` returns a `boolean`. This boolean indicates whether any further actions should be taken on this layout item after the long click, in other words, whether the regular click should trigger as well. Since we do not want this, we should return `true` at the end of the method implementation, indicating the click was handled by the `onItemLongClick` method and no further action is needed.

## Model the journal entries

To hold the data of our journal entries, we will create a new class that represents them. This class will be called `JournalEntry` and should have the following fields:

- `id`
- `title`
- `content`
- `mood`
- `timestamp`

Also generate a constructor, getters and setters for your class using **Cmd+N**/**Alt+Ins**. (For more detailed instructions, have a look at "Modeling friends" in the [Friendsr](https://apps.mprog.nl/guided/friendsr) project.)

Also, since we want to pass instances of this class using an `Intent`, we want to make the class `implement Serializable` to facilitate this. 


## Creating a database helper

To store our journal entries, we will make use of a SQLite database. Let's create a database helper class for this purpose:

-   Create a new Java class named `EntryDatabase` using the superclass `android.database.sqlite.SQLiteOpenHelper`. This superclass provides a lot of database functionality, but we will need to provide some, too.

-   The file will not compile currently, because we haven't provided implementations for all required methods. Press **CTRL-I** to open up the **Implement Methods** dialog. Two methods are already selected: press OK to add them both.

-   Finally, we need to create the right constructor for our class. Then press **CTRL-O** to open up the **Override Methods** dialog. Choose the simplest constructor (the topmost) and press OK to create it.


## Writing database creation methods

We now have the very basic functionality of our database class, but we still need to define what `onCreate()` will do. Since we are going to store our `Entry` objects in the database, we need a table that represents these fields accurately. Our table should look like the example below, with columns that represent each field of our `Entry` model.

| _id | title | content | mood | timestamp |
|-----|-------|---------|------|-----------|
| 1   | ...   | ...     | ...  | ...       |
| 2   | ...   | ...     | ...  | ...       |
| 3   | ...   | ...     | ...  | ...       |

Now that we know what our database structure should look like, we can create the appropriate SQL query to generate the table. A proper SQL query to create a table should contain the name of the table and the names and [data types](https://www.sqlite.org/datatype3.html) of the columns in the table. 

        create table TABLE_NAME (COLUMN1_NAME COLUMN1_TYPE, COLUMN2_NAME COLUMN2_TYPE, COLUMN3_NAME COLUMN3_TYPE);

Since we want to keep track of a timestamp, it's practical to make our table automatically generate a timestamp for each journal entry when it's inserted into the database. It should also auto-increment the entry `_id`, since each row should have a unique identifier to be able to retrieve it. 

> Note that due to the structure of the query, you should avoid spaces in your column names. Protected words that denote data types or SQL keywords (such as JOIN, ADD, ACTION, CROSS) should also be avoided as column names, as these might cause your query to be wrongly interpreted. 

If you are unsure about your query, you can verify it using services like [sqlfiddle](http://sqlfiddle.com/#!5) which allows you to check whether your query is syntactically correct. 

- Implement `onCreate()`: write code that creates a table called `entries` with columns `title`, `content`, `mood` and `timestamp`. Also add an `_id` column of type `INTEGER PRIMARY KEY`. You can create a variable of type `String` that holds your SQL query and then execute the SQL query using the `sqliteDatabase.execSQL()` method.

- Implement `onUpgrade()`: write code that drops the entries table (if it exists) and recreates it by calling `onCreate()`. This is so we can start with a clean slate in case we want to, or if we want to change the schema (table structure) of our database later on. 

- Finally, to your `onCreate()`, add some code that creates sample items, so that we can use these to test. 

> The code in the `onCreate()` method will only be executed the first time the database is created. if you make changes to the schema of your database (like adding columns or changing their names) or changes to the `onCreate()` content, you need to either reinstall your app, delete the app's data from the phone or force a call to `onUpgrade()`.

## Transform the helper into a singleton

We'll now convert the `EntryDatabase` class into a Singleton. This means that only one instance of the class can exist at the same time: there can never be multiple instances of the `EntryDatabase` class. Instead of calling the constructor directly, we ask whether there is currently an instance of `EntryDatabase` that exists. If so, this instance will be returned to us. Only if it does not exist yet, it will be created. 

-   First, make the constructor `private` instead of `public`.

-   Then, add a private **static** variable called `instance` of type `EntryDatabase`. This is where the unique instance of the class is stored, once made.

-   Then, add a public **static** method called `getInstance()` which should accept a parameter of type `Context`. This method should return the value of `instance` if available, and otherwise call the constructor that is now private, providing the right parameters (see [SQLite](https://apps.mprog.nl/android-reference/sqlite)), and storing that in `instance`.

-   To ensure that everything is in order, place the following line at the bottom of your `MainActivity`'s `onCreate()` method:

~~~ java
        EntryDatabase db = EntryDatabase.getInstance(getApplicationContext());
~~~

You project should now compile and run successfully, though data is not yet displayed.


## Write the select method

- Write a method called `selectAll()` in `EntryDatabase`. First, use `getWritableDatabase()` to open up the connection with the database. Use the method `rawQuery` from that object to run a `SELECT * FROM entries` query and `return` the `Cursor`. 

The `rawQuery` method takes two arguments, the first one is the query with placeholders, the second a string array with the strings that should go in place of the placeholders:

        rawQuery("SELECT id, example_column FROM table WHERE name = ? AND example_column = ?", new String[] {"2", "column_value"});

Of course since we are selecting everything, we have no placeholders or arguments, so the second argument of `rawQuery` can be `null`!

- Use your custom `entry_row.xml` and create a new class `EntryAdapter` inheriting from `ResourceCursorAdapter`. Using the override dialog (`CTRL+O`) implement a constructor `public EntryAdapter(Context context, Cursor cursor)`. Call `super` and pass on the `context` and the `cursor`, and also the `id` of the layout that you just made. Tip: layout IDs start with `R.layout` and not `R.id`!

- Also override and implement the method `bindView()`, which takes a `View` and fills the right elements with data from the cursor. 

     - You can use `yourCursor.getColumnIndex(name)` to get the column index for a column named `name`.
     - You can use `yourCursor.getInt(columnIndex)` to retrieve the value of a column that you know the index of as an integer.
     - You can use `yourCursor.getString(columnIndex)` to retrieve the value of a column that you know the index of as a string.
     - You can call `view.findViewById()` to get references to the controls in `entry_row.xml`.

If unsure how the adapter works, take a look at earlier projects and their references. While syntax is sometimes different, the general pattern of an adapter is always similar. 

> If you get compiler issues because of the auto generated `@NonNull` or `@Nullable` annotation, you can remove the `@androidx.annotation.@NonNull` part to fix the compiler errors. It is not necessary for the code, merely a flag like `@Override`. 
     
- In the `onCreate()` of the `MainActivity`, use the `EntryDatabase` to get all records from the database, make a new `EntryAdapter` and link the `ListView` to the adapter.

The app should now display all example entries from the database! Think about how you want to represent the mood that is associated with the journal entry. It is stored in the database in plaintext, but in the adapter you can think of a way to show something else, like an image/emoji representing the mood. 


## Write the insert method

- First, link the confirmation button in the `InputActivity` to a new method called `addEntry` through its onClick attribute in the XML. 
- In the database class, add a public method `insert()` which accepts a `JournalEntry` object as its parameter. 
- In that method, open a connection to the database (see the instructions for select all), and create a new `ContentValues` object. Use the `put` method to add values for `title`, `content` and `mood`. Then, call `insert` on the database connection, passing in the right parameters (`nullColumnHack` may simply be `null`). 

> The [ContentValues](https://developer.android.com/reference/android/content/ContentValues.html) class offers an easy way to bind values to columns for SQLite. It also prevents user input from directly appearing in the SQL string unescaped and unchecked, making your application less vulnerable to SQL injection.

- Now go back to the activity, use `EntryDatabase.getInstance()` to get to the database instance, and call the `insert` method that we just defined.

Your app should now allow you to insert new entries into the database, which should also show up in your `MainActivity` when you go back to it. 


## Write the delete method

- In your database class, write a method that accepts a `long id` as a parameter. 
- Using this parameter, call a query that removes the entry with that id from the database. Why do you think we are removing by id and not by title, for example?
- For Android, delete actions are usually tied to long clicking items. Add code to your `OnItemLongClickListener` class from `MainActivity` that deletes the selected item from the database. If unsure how to retrieve what item was clicked, refer to the section 'Extract what actually was clicked on' from the [Friendsr](https://apps.mprog.nl/guided/friendsr) project. 


Since we have a list of `Cursor` objects in our adapter, the item returned by `getItemAtPosition` is of the type `Cursor`. Then, when you have the cursor object, you can extract the values of the columns like you did in the `bindView()` method of your adapter. 


## Make sure the interface stays up to date

To make sure that the list view always displays the most up-to-date information from the database, we are going to update it every time we change something. Since we cannot edit items, this mostly applies to deleting and adding items. 

- In `MainActivity`, create a `private` method called `updateData()`.

- You will need access to the database, as well as to the adapter. Add private instance variables to your class: `EntryDatabase db` and `EntryAdapter adapter`.

- In your `onCreate()` you already create an instance of the `EntryDatabase` and of the `EntryAdapter`. Change the code to save these instances to the instance variables that we just created.

> When determining the scope of your variables, always ask yourself if the scope in which they are available matches the scope in which they are needed. While sometimes it's efficient to declare a variable for the whole class to use, it's certainly not always necessary.

- Now we can write the body for the method `updateData()`. You can use the method `swapCursor()` on the adapter to put in a new cursor for the updated data. Where do you get that new cursor? Just call `selectAll()` on the database again, as you did in `onCreate()`.

- Call your new method right after calling `delete()` from the `OnItemLongClick` implementation, so the new list is rendered, without the deleted item.

- When adding items, we are coming back from the previous activity, so we probably need to do something in the `MainActivity`'s `onResume()`!


## Accessing a journal entry's details

Finally, we want to be able to access the content of a journal entry, and not just its title, timestamp and associated mood. 

- In the `onItemClick` of your `ListView`, write code that fires an `Intent` to the third activity that shows the entry details.
- Add the instance of `Entry` that was clicked on to the intent using a `Bundle`. If unsure how to do this, again refer to the 'Extract what actually was clicked on' section from the [Friendsr](https://apps.mprog.nl/guided/friendsr) project. 
- In the third activity, retrieve the `Intent` and the associated `Entry` object and show the contents of the entry in the appropriate views.

## Finishing up

- Have a look at the class diagram at the top of this assignment. Are all classes present in your project? What's the difference?

- As always, consider this week's assessment criteria and make sure your app works well and the code looks nice.


## Some ideas

- Make sure the app remembers at which point the user was in the listview, so they don't have to rescroll upon rotation or coming back to the list.

- Allow the user to mark certain entries as 'favorites'.

- Implement state restoration in the other activities as well, so that the selected mood/emoji and contents don't disappear upon rotation or leaving the app.