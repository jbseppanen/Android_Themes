# Android_Theming

## Introduction

For this project, pick one of your favorite apps you have worked on and improve the resource usage, create
icons and allow users to select themes

## Instructions

### Part 1 - Custom Theme
Typically apps have a light and a dark theme. Customize one of those themes for your app.

### Part 2 - Migrate all Hard Coded Data to Resources Files
Make sure to use at least 10 values each for String resources and Attr resources.
1. Go through all of your code (java and xml) and find any hard coded string and extract them all to your strings.xml file
2. Go through your xml layout files and note all hard coded values (any numbers), what they are and how they are used
3. This is the hard part, you need to decide how to organize these constant values. You'll want to find the right level of granularity.
> Example: Do you just have one value called title_padding which is the top/bottom/left/right padding values for all titles in your app. Do you need different values for each?
> A good way to do this, write down all the values with their use as names as if you were declaring them in Java code example:
> ​	list_title_padding_top = 10dp
> ​	list_title_color = #00000000
> ​	details_label_size = 24sp
> ​	drawable_ic_settings = @drawable/ic_settings_white_24dp
> ​	...
> Then look over it and combine those that can be combined based on their values and usage. Remember, just because some things have the same value, doesn't mean they should be combined. Values and usage must be similar

4. Now that you have this, create or go to the attr.xml file and attributes for all of your final variables.
5. Then create resources for each of these values in their respective files. Dimensions in `dimens.xml` and colors in `colors.xml`
6. Now, in the `styles.xml` file, attach the attr variables to the resource values in your parent theme.
7. Finally, go back through your layout files and replace the hardcoded values with the variables attributes you defined using `?attr/variable_name`

### Part 3 - Build Custom Launcher and Other Icons
1. Right click the res directory and select New -> Image Asset
2. First, create a custom launcher icon, you can use your own image or a clip art provided by android studio. However, if you use your own image the launcher should follow the material design guidelines found here https://material.io/design/iconography/product-icons.html
3. Go to your `AndroidManifest` file and make sure your new icon is being used as the launcher and round icons
4. Return to the Asset Studio and select "Action Bar and Tab Icons" from the dropdown menu. Create versions of all the icons in your app which with work with both of your themes by selecting the theme at the bottom of the window. You can make them dark, light or any custom color to go with your theme.
5. Add variables for these icons to your `attr.xml` and then attach them in the `styles.xml`
6. Build and test to make sure everything works.

### Part 4 - Allow Users to Select their Theme
#### Build Another Theme
Now we'll build a second theme for the app. In this situation, we're just going to give the user a toggleable mode like a dark mode or a colorblind mode.
1. Build a second theme, you can either have another Android provided theme or your defined theme as the parent
> If you use an Android Theme as your parent, be sure to provide values for all of the items in your `attr.xml` page

2. Define new values for at least 10 attributes. These should be a combination of any of your attributes defined in the previous part, standard attributes like `colorPrimary` and `colorAccent`, and images. Be sure to include at least one of each

#### Allow Users to Select a Theme and Store the Result
1. Create a new activity called `SettingsActivity` this will use a `Settings Activity` as the base activity
2. Make sure you can get to the `SettingsActivity` from your `Launcher` or `Main Activity`, this can either be done with a button or an action bar menu
3. Open the file `res/xml/pref_general.xml` which was generated by Android when you created your Settings Activity
4. Add a `SwitchPreference` to the top of the page. Give it a `defaultValue`, a `key`, and a `title`. All but the `defaultValue` must be string resources
> Look at other Preferences on this page and match the pattern for string resource names and values
> Be clear to the user what kind of theme they'll be selecting. If it is a dark mode, a large text mode, or some other mode

#### Write a Utility Class to Manage the Themes
1. Create a Java class called `ThemeUtils`
2. Write a static method called `getCurrentTheme` which accepts an `Activity` object and returns an int value for the id of the selected theme
	a. use `PreferenceManager.getDefaultSharedPreferences(activity.getApplicationContext())` to get a handle on the right `SharedPreferences` object
	b. get the `boolean` value for the theme setting the user selected
	c. use this value to determine if your special theme mode is enabled
	d. return the resource id value for the seleted theme (`R.style.ThemeName`)
3. Write a static method called `onActivityCreateSetTheme` which accepts an `Activity` object. All it does it call the `Activity`'s `setTheme` method and passes it the result from the `getCurrentTheme` method.
4. Write a static method called `refreshActivity` which accepts an `Activity` object and restarts it.
	a. call the `Activity`'s `getIntent` method to get a handle on the `Intent` that started the `Activity`.
	b.  call the `Activity`'s `finish` method to stop it
	c. call the `Activity`'s `startActivity` method and pass it the `Intent` to start it again
	
#### Bolt It Together
Now that we have the pieces, let's power the system up. For this step, you'll need to think about how your app will flow and how that meshes with your understanding of the app lifecycle. The goal is that whenever a user enters or returns to an activity after the theme has been changed.
With that out of the way, this isn't too complicated.
1. All activities must include call the `ThemeUtils.onActivityCreateSetTheme` method, passing in the `this` object in the `onCreate` method **before** the `super.onCreate` method is called.
2. If you will ever return to an activity from another activity, you'll need to check to see if the theme the activity is currently using matches the one stored in preferences. If not, it should restart the activity.
	a. create a private data member called `themeId`
	b. override the `setTheme(int themeId)` method to store the `themeId` in your data member and then call the `super.setTheme(themeId)` method
	c. write a method called `checkTheme` which will compare your `themeId` to the one from `ThemeUtils.getCurrentTheme`. If they don't match, call `ThemeUtils.refreshActivity`
	d. override the `onStart` and `onResume` methods to run the `checkTheme` method befor they run `super...`
3. Analyze the use cases of your apps and determine which needs to go where and apply this code to all the activities as necessary.
4. Test your app. Make sure to keep an eye on Logcat to see any errors that pop up.

## Challenge
In your testing, find bugs and small feature improvements that can improve the polish of your app.