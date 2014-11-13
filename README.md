Gradle scripts for Android projects
===================================

**build-processApplicationId.gradle**

Emulates manifest placeholder `${applicationId}` in xml resource files.

This is useful when you want to be able to install all your build variants on the same device at the same time. You will not only have to declare a different applicationId in the AndroidManifest.xml, but if you are also using `ContentProvider` and/or  `AccountManager`then you will need to declare different authorities and account types per build variant. There is out-of-the-box support for the ${applicationId} manifest placeholder in AndroidManifest.xml, but not for other xml resource files. This gradle script provides that missing support.

Every file in the res/xml/ directory is searched for occurrences of '${applicationId}', which is replaced by the actual applicationId string of the build variant that is being built.

*Usage*

Copy the file into your project next to the module build.gradle file and add an apply statement to build.gradle to invoke it. The top of the module build.gradle should look like this:

    apply plugin: 'com.android.application'
    apply from: './build_processApplicationId.gradle'

You can now use the applicationId placeholder in your xml resource files like you already could in the manifest file. For example, declare your ContentProvider like this in AndroidManifest.xml:

    <provider
        android:name=".provider.DatabaseProvider"
        android:authorities="${applicationId}.DatabaseProvider"
        android:exported="false" />

Provide the Sync Adapter and Account Authenticator meta-data in a similar way:

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

Do not duplicate these files into different build variant source directories; it's much simpler maintaining a single version in the main source directory, and now you can.
