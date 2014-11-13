Gradle scripts for Android projects
===================================

**build-processApplicationId.gradle**

Emulates manifest placeholder `${applicationId}` in xml resource files.

This is useful when you want to be able to install all your build variants on the same device at the same time. You will not only have to declare a different applicationId in the AndroidManifest.xml, but if you are also using `ContentProvider` and/or  `AccountManager`then you will need to declare different authorities and account types per build variant. There is out-of-the-box support for the ${applicationId} manifest placeholder in AndroidManifest.xml, but not for other xml resource files. This gradle script provides that missing support.

Every file in the res/xml/ directory is searched for occurrences of '${applicationId}', which is replaced by the actual applicationId string of the build variant that is being built.

Improvements and extensions are welcome.

*Usage*

Copy the file into your project next to the module build.gradle file and add an apply statement to build.gradle to invoke it. The top of the module build.gradle should look like this:

    apply plugin: 'com.android.application'
    apply from: './build-processApplicationId.gradle'

You can now use the applicationId placeholder in your xml resource files like you already could in the manifest file. For example, declare your ContentProvider like this in AndroidManifest.xml:

    <provider
        android:name=".provider.DatabaseProvider"
        android:authorities="${applicationId}.DatabaseProvider"
        android:exported="false" />

Note the `${applicationId}` bit. This is replaced at build time with the actual applicationId for the build variant that is beign built. Your ContentProvider needs to construct the authority string in code. It can use `BuildConfig.APPLICATION_ID`.

    public class DatabaseContract {
        /** The authority for the database provider */
        public static final String AUTHORITY = BuildConfig.APPLICATION_ID + ".DatabaseProvider";
        // ...
    }

The above is already possible in code and the manifest file but not in other xml resource files. You must provide the meta-data for a Sync Adapter and Account Authenticator in xml resource files and with this gradle script you can declare the authority and account type in an identical way:

    <sync-adapter xmlns:android="http://schemas.android.com/apk/res/android"
        android:accountType="${applicationId}"
        android:allowParallelSyncs="false"
        android:contentAuthority="${applicationId}.DatabaseProvider"
        android:isAlwaysSyncable="true"
        android:supportsUploading="true"
        android:userVisible="true" />

    <account-authenticator xmlns:android="http://schemas.android.com/apk/res/android"
        android:accountType="${applicationId}"
        android:icon="@drawable/ic_launcher"
        android:label="@string/account_authenticator_label"
        android:smallIcon="@drawable/ic_launcher" />

Again, note the `${applicationId}` bit. Before, you had to duplicate these files into different build variant source directories. Now it is much simpler. Just maintain a single version in the main source directory using a placeholder.
