# Android Notifications
An Android notification is a message that can be displayed outside the application's UI. These notifications can be used to notify a user of an event within the app which has taken place. For example a notification could be displayed if a timer has ended in a game. These notifications can be seen on the lock screen, the pull down menu, and generally they will be displayed in a temporary drop down at the top of the screen if the device is on and unlocked (*"Notifications overview"*, 2022).

There are two main types of notifications. Local notifications and push notifications. Local notifications are from within the device and push notifications require a server which the notification is pushed from.

Ideas on how this could be implimented into my travelling app:
- Reminder notifications to encourage users to use the app (e.g "Check the top 5 attractions in New Zealand").
- Reminder to rate a country that the user has not rated yet.
- Notify the user about updates to countries they liked.

## Code Snippets
### Dependencies
Add androidx to your dependencies if its not already there.
```
val core_version = "1.6.0"
dependencies {
    implementation("androidx.core:core-ktx:$core_version")
}
```

### Notification builder
This is where you can set the contents of the notification as well as the style.
```
var builder = NotificationCompat.Builder(this, CHANNEL_ID)
        .setSmallIcon(R.drawable.notification_icon)
        .setContentTitle(textTitle)
        .setContentText(textContent)
        .setPriority(NotificationCompat.PRIORITY_DEFAULT)
```
You can put text over multiple lines by using `setStyle()` Here is an example:
```
var builder = NotificationCompat.Builder(this, CHANNEL_ID)
        .setSmallIcon(R.drawable.notification_icon)
        .setContentTitle("My notification")
        .setContentText("Much longer text that cannot fit one line...")
        .setStyle(NotificationCompat.BigTextStyle()
                .bigText("Much longer text that cannot fit one line..."))
        .setPriority(NotificationCompat.PRIORITY_DEFAULT)
```
### Create a channel and set the importance
Here you will create a notification channel and set the importance of the notification.
```
private fun createNotificationChannel() {
    // Create the NotificationChannel, but only on API 26+ because
    // the NotificationChannel class is new and not in the support library
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
        val name = getString(R.string.channel_name)
        val descriptionText = getString(R.string.channel_description)
        val importance = NotificationManager.IMPORTANCE_DEFAULT
        val channel = NotificationChannel(CHANNEL_ID, name, importance).apply {
            description = descriptionText
        }
        // Register the channel with the system
        val notificationManager: NotificationManager =
            getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
        notificationManager.createNotificationChannel(channel)
    }
}
```

### Notification tap action
This is how you set the action which will be run when the user taps on the notificaion.
```
// Create an explicit intent for an Activity in your app
val intent = Intent(this, AlertDetails::class.java).apply {
    flags = Intent.FLAG_ACTIVITY_NEW_TASK or Intent.FLAG_ACTIVITY_CLEAR_TASK
}
val pendingIntent: PendingIntent = PendingIntent.getActivity(this, 0, intent, PendingIntent.FLAG_IMMUTABLE)

val builder = NotificationCompat.Builder(this, CHANNEL_ID)
        .setSmallIcon(R.drawable.notification_icon)
        .setContentTitle("My notification")
        .setContentText("Hello World!")
        .setPriority(NotificationCompat.PRIORITY_DEFAULT)
        // Set the intent that will fire when the user taps the notification
        .setContentIntent(pendingIntent)
        .setAutoCancel(true)
```

### Removing the notification
All andoird notifications will stay displayed unless the one of the following happens:
- The user closes the notificaion.
- The user clicks the notificaion and `setAutoCancel()`  is called.
- A timeout us set using `setTimeoutAfter`.

### Showing the notificaion
Now you can tell the app to display the notification.
```
with(NotificationManagerCompat.from(this)) {
    // notificationId is a unique int for each notification that you must define
    notify(notificationId, builder.build())
}
```

### Urgent notification
If you want an urgent notificaion you can add the following code to the notification builder:
```
.setFullScreenIntent(fullScreenPendingIntent, true)
```

### Lock screen visibility
The visability of the notificaion on the devices lock screen can be changed to be public, private and secret.
- public: Can be seen on the lock screen with full content
- Private: Will show limited contents of the notification on the lock screen.
- Secret: No information will be displayed on the lock screen.

This can be done by adding the following to the notificaion builder:
```
 .setVisibility(VISIBILITY_PUBLIC)
 .setVisibility(VISIBILITY_PRIVATE)
 .setVisibility(VISIBILITY_SECRET)
```
All code was gathered from (*"Create a notification"*, 2022)

## Conclusion
Notificaions are a great way to bring back users to your app which in turn could possibly increase profits from many applications. They are an important part of a successful application and have many options to customise most aspects of the notificaion.

## References
1. Notifications overview. (2022, November 9). Android Developers. https://developer.android.com/develop/ui/views/notifications
2. Create a notification. (2022, November 9). Android Developers. https://developer.android.com/develop/ui/views/notifications/build-notification
