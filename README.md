# appium-device-management

Some Appium features have to do with the device itself, rather than any particular app. You may or may not find that you need these features in the course of your testing, as they go beyond simple UI automation. But whatever the case, it's important to know what you can do with Appium.

The first set of features has to do with managing files.

[Device File Interaction](https://github.com/lana-20/appium-device-file-interaction)

[Android File System](https://github.com/lana-20/adb-shell#file-system)

Another aspect of mobile devices that differs from web browsers is that we can lock and unlock them. In fact, it's often important to be sure that, after we unlock a device, our app behaves the same as it did before locking it. The mobile operating systems often put apps to sleep, and so they need to be designed in such a way that when they wake back up, they don't crash or do unexpected things.

The way to use Appium to lock a device is to call, simply enough, <code>driver.lock()</code>. If you call it without any parameters, the device will simply lock, and that's it. Or, it can be called with a numeric parameter, denoting a certain number of seconds. After this amount of time has elapsed, Appium will attempt to unlock the device automatically. This latter feature can be useful for testing the case where your app comes back after the device is unlocked. Of course, you can also manage this in your own test code, using the next method we'll discuss.

And that is <code>driver.unlock()</code>, which as you can guess will attempt to unlock the device. There are a few limitations to this command. It works just as expected on emulators and simulators, but real devices are more complex. Real devices can be secured with various types of protection, and obviously Appium is not able to get around device lock screens in any way that a user themselves wouldn't be able to do. So if you are automating a real device, and it has some kind of security set on the lock screen, on iOS you're completely out of luck. On Android, however, it's possible to actually automate the lock screen, as long as we already know the password or pin.


