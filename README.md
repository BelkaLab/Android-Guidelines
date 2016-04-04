# Android Guidelines

Avoid reinventing the wheel by following these guidelines.
The following guidelines comes out from [Google's Code Style Rules](https://source.android.com/source/code-style.html) on [Android](http://developer.android.com/index.html) development and many years of experience on it.

## 1. Project Structure

```
├─ library-foobar
├─ app
│  ├─ libs
│  ├─ src
│  │  ├─ androidTest
│  │  │  └─ java
│  │  │     └─ us/belka/project
│  │  └─ main
│  │     ├─ java
│  │     │  └─ us/belka/project
│  │     │		├─ activities
│  │     │		├─ adapters
│  │     │		├─ fragments
│  │     │		├─ interfaces
│  │     │		├─ models
│  │     │		├─ notifications
│  │     │		├─ server
│  │     │		├─ services
│  │     │		├─ utils
│  │     │     	│	├─ Constants
│  │     │     	│	├─ DLog
│  │     │     	│	├─ Preferences
│  │     │     	│	└─ Utils
│  │     │		└─ widgets
│  │     ├─ res
│  │     └─ AndroidManifest.xml
│  ├─ build.gradle
│  └─ proguard-rules.pro
├─ build.gradle
└─ settings.gradle
```

This is the standard project structure on Android Studio. 

* **Activities**, **Adapters**, **Fragments**, **Interfaces**, **Notifications**, **Services**: these packages are self-explained in such a way they contain all the common `Android` components built for the application.
* **Models**: this package contains the astractions of the model used within the application.
* **Server**: this package contains all the network stuff in order to establish a correct communication with the server/s.
* **Utils**: this package contains a collection of useful stuff to quickly and easily develop the application.
* **Widgets**: this package contains all the custom UI-Components built in this application.



## 2. Naming


### 2.1 Class Files
Class names are written in [UpperCamelCase](https://en.wikipedia.org/wiki/CamelCase).

For classes that extend an Android component, the name of the class should end with the name of the component.
For example: `SignInActivity`, `SignInFragment`, `ImageUploaderService`, `ChangePasswordDialog`.

### 2.2 Variables
Variables are defined in [lowerCamelCase](http://c2.com/cgi/wiki?LowerCamelCase).
For example: `pageSize`, `eventsCompleted`.

### 2.3 Constants
Constants are defined in **UPPERCASE_UNDERSCORE**.
For example: `NUM_EVENTS`, `CONTENT_ID`.

### 2.4 Resources files and ids

Resources files and ids are written in **lowercase_underscore**.
To see some example, please check the layout file names in the section below.

#### 2.4.1 Layout files

Layout files should match the name of the Android components that they are intended for but moving the top level component name to the beginning. For example, if we are creating a layout for the `SignInActivity`, the name of the layout file should be `activity_sign_in.xml`.

| Component        | Class Name             | Layout Name                   |
| ---------------- | ---------------------- | ----------------------------- |
| Activity         | `UserProfileActivity`  | `activity_user_profile.xml`   |
| Fragment         | `SignUpFragment`       | `fragment_sign_up.xml`        |
| Dialog           | `ChangePasswordDialog` | `dialog_change_password.xml`  |
| AdapterView item | ---                    | `item_person.xml`             |
| Partial layout   | ---                    | `partial_stats_bar.xml`       |
| Custom Widget    | `MyCustomInputWidget`  | `widget_my_custom_input.xml`  |


## 3. Logging

Use the logging methods provided by the `Log` class to print out error messages or other information that may be useful for developers to identify issues:

* `Log.v(String tag, String msg)` (verbose)
* `Log.d(String tag, String msg)` (debug)
* `Log.i(String tag, String msg)` (information)
* `Log.w(String tag, String msg)` (warning)
* `Log.e(String tag, String msg)` (error)

In every project we use a wrapper of standard `Log` class that disable logging on release builds.

```java
public class DLog {
	
    public static void d(String tag, String message){
        if(BuildConfig.DEBUG)
            Log.d(tag, message);
    }
    ...
}
```

## 4. Resources

### 4.1 XML Layout
All the layouts are written using the following convention:

* One attribute per line, indented by 4 spaces
* `android:id` as the first attribute always
* `android:layout_****` attributes at the top
* `style` attribute at the bottom
* Tag closer `/>` on its own line, to facilitate ordering and adding attributes.
* Rather than hard coding `android:text`, consider using [Designtime attributes](http://tools.android.com/tips/layout-designtime-attributes) available for Android Studio.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    >

    <TextView
        android:id="@+id/name"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignParentRight="true"
        android:text="@string/name"
        style="@style/FancyText"
        />

    <include layout="@layout/reusable_part" />

</LinearLayout>
```

### 4.2 Styles
Almost every project needs to properly use styles, because it is very common to have a repeated appearance for a view. At least you should have a common style for most text content in the application.
For example:

```xml
<style name="BigButton">
	<item name="android:layout_width">match_parent</item>
    <item name="android:layout_height">wrap_content</item>
    <item name="android:layout_alignParentBottom">true</item>
    <item name="android:textSize">20sp</item>
</style>
```

#### 4.2.1 Naming
The name of the style is written in [UpperCamelCase](https://en.wikipedia.org/wiki/CamelCase).

#### 4.2.2 Split a large style file into other files 
You don't need to have a single `styles.xml` file. Android SDK supports other files out of the box, there is nothing magical about the name styles, what matters are the XML tags `<style>` inside the file. Hence you can have files `styles.xml`, `styles_home.xml`, `styles_item_details.xml`, `styles_forms.xml`. 

### 4.2 Colors
There should be nothing else in your `colors.xml` than just a mapping from a color name to an RGBA value. Do not use it to define RGBA values for different types of buttons.

**DON'T DO THIS!**

```xml
<resources>
    <color name="button_foreground">#FFFFFF</color>
    <color name="button_background">#2A91BD</color>
    <color name="comment_background_inactive">#5F5F5F</color>
    <color name="comment_background_active">#939393</color>
</resources>
```

You can easily start repeating RGBA values in this format, and that makes it complicated to change a basic color if needed. Also, those definitions are related to some context, like "button" or "comment", and should live in a button style, not in `colors.xml`.

**DO THIS!**

```xml
<resources>
	<color name="white"     >#FFFFFF</color>
    <color name="gray_light">#DBDBDB</color>
    <color name="gray"      >#939393</color>
    <color name="gray_dark" >#5F5F5F</color>
    <color name="black"     >#323232</color>
</resources>
```
Ask for this palette from the designer of the application. The names do not need to be color names as "green", "blue", etc. Names such as "brand_primary", "brand_secondary", "brand_negative" are totally acceptable as well. Formatting colors as such will make it easy to change or refactor colors, and also will make it explicit how many different colors are being used. Normally for a aesthetic UI, it is important to reduce the variety of colors being used.


### 4.3 Dimens
They should be treated as `colors.xml` defining a "palette" of typical spacing and font sizes, for basically the same purposes as for colors. A good example of a **dimens** file:

```xml
<resources>

    <!-- font sizes -->
    <dimen name="font_larger">22sp</dimen>
    <dimen name="font_large">18sp</dimen>
    <dimen name="font_normal">15sp</dimen>
    <dimen name="font_small">12sp</dimen>

    <!-- typical spacing between two views -->
    <dimen name="spacing_huge">40dp</dimen>
    <dimen name="spacing_large">24dp</dimen>
    <dimen name="spacing_normal">14dp</dimen>
    <dimen name="spacing_small">10dp</dimen>
    <dimen name="spacing_tiny">4dp</dimen>
</resources>
```

### 4.4 Strings

#### 4.4.1 Naming
Name your strings with keys that resemble namespaces, and don't be afraid of repeating a value for two or more keys. Languages are complex, so namespaces are necessary to bring context and break ambiguity.
**DON'T DO THIS!**
```xml
<string name="network_error">Network error</string>
<string name="call_failed">Call failed</string>
<string name="map_failed">Map loading failed</string>
```
**DO THIS!**
```xml
<string name="error.message.network">Network error</string>
<string name="error.message.call">Call failed</string>
<string name="error.message.map">Map loading failed</string>
```

#### 4.4.2 Values
Don't write string values in all uppercase. Stick to normal text conventions (e.g., capitalize first character). If you need to display the string in all caps, then do that using for instance the attribute [textAllCaps](http://developer.android.com/reference/android/widget/TextView.html#attr_android:textAllCaps) on a TextView, or modify the string programmatically.

**DON'T DO THIS!**
```xml
<string name="error.message.call">CALL FAILED</string>
```

**DO THIS!**
```xml
<string name="error.message.call">Call failed</string>
```

#### 4.2.3 Split a large strings file into other files 
Don't keep all the strings in a unique file since they too many to be easily managed if the project is too big. 
Depending on context you can split the `strings.xml` in many files like: `strings_errors.xml`, `strings_successes.xml`, `strings_cars.xml` etc.

## 5. Libraries
#### 5.1 [GSON](https://github.com/google/gson) / JSON Serialization - Deserialization
Gson is a Java library that can be used to convert Java Objects into their JSON representation. It can also be used to convert a JSON string to an equivalent Java object. Gson can work with arbitrary Java objects including pre-existing objects that you do not have source-code of. This is really easy to use and [well documented](https://github.com/google/gson/blob/master/UserGuide.md).

Having written your own Java model, you can easily serialize or deserialize an instance of that model using GSON:

```java
public class Person{
	private String firstName;
	private String lastName;
	
// Continue with getters and setters...
}

Gson gson = new GsonBuilder().create()
Person person = new Person("Mario", "Rossi");

// Serialization
String personJSON = gson.toJson(person);

// Deserialization
Person person = gson.fromJson(jsonObject.toString(), Person.class);
```

#### 5.2 [Glide](https://github.com/bumptech/glide) / Image Loader
Glide is a fast and efficient open source media management and image loading framework for Android that wraps media decoding, memory and disk caching, and resource pooling into a simple and easy to use interface.
Load an image becomes really easy:
```java
Glide.with(this).load("http://goo.gl/gEgYUd").into(imageView);

// It is also possible to easily set many other options.
Glide.with(myFragment)
        .load(url)
        .override(200, 200)
        .centerCrop()
        .placeholder(R.drawable.loading_spinner)
        .crossFade()
        .into(myImageView);
```

#### 5.3 [Volley](http://developer.android.com/training/volley/simple.html) / Send HTTP Requests

Volley is an HTTP library that makes networking for Android apps easier and most importantly, faster. Volley is available through the open AOSP repository.

Volley offers the following benefits:

* Automatic scheduling of network requests.
* Multiple concurrent network connections.
* Transparent disk and memory response caching with standard HTTP cache coherence.
* Support for request prioritization.
* Cancellation request API. You can cancel a single request, or you can set blocks or scopes of requests to cancel.
* Ease of customization, for example, for retry and backoff.
* Strong ordering that makes it easy to correctly populate your UI with data fetched asynchronously from the network.

The library performs the request in a separated thread in background providing the result to the UI Thread.
Sending a request with `Volley` become really simple:
```java
// Instantiate the RequestQueue.
RequestQueue queue = Volley.newRequestQueue(Activity.this);
String url ="http://www.google.com";

// Request a string response from the provided URL.
JsonObjectRequest jsonObjectRequest = new JsonObjectRequest(Request.Method.GET, url,
            new Response.Listener<String>() {
    @Override
    public void onResponse(JsonObjectRequest response) {
        DLog.w("RESPONSE", response.toString());
    }
}, new Response.ErrorListener() {
    @Override
    public void onErrorResponse(VolleyError error) {
        DLog.w("RESPONSE", "Error!");
    }
});
// Add the request to the RequestQueue.
queue.add(jsonObjectRequest);
```

##### 5.3.1 Write your own wrapper that supports your API requirements
It is strongly raccomended to extend the JsonObjectRequest class setting only once:
* Endpoint
* HTTP Header Params
* Parse the response
* ...

## 5. Best Practices
* Questione View Holder anche in activity e fragments!!
* Network nei modelli con interfacce che chiamano activity e fragments

## 6. Testing
[Robotium](https://github.com/robotiumtech/robotium) makes writing UI tests easy. You do not need Robotium for running connected tests for UI cases, but it will probably be beneficial to you because of its many helpers to get and analyse views, and control the screen. Test cases will look as simple as:
```java
solo.sendKey(Solo.MENU);
solo.clickOnText("More"); // searches for the first occurrence of "More" and clicks on it
solo.clickOnText("Preferences");
solo.clickOnText("Edit File Extensions");
Assert.assertTrue(solo.searchText("rtf"));
```

## 7. Documentation
