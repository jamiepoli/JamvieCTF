---
title: "Trend Micro CTF: Raimund Genes Quals 2020"
date: 2020-10-04T13:12:06-06:00
draft: false
comments: false
images:
tags:
- reversing
- android
- OSINT
- crypto
---

Last year, before I became an active participant in Maple Bacon, the team went on to participate in and land a top 10 position in Trend Micro 2019 Qualifiers, later going on to compete in the finals of that year. When the 2020 serving came up this weekend, I wanted to keep with tradition and maintain the pattern - and so we landed a top 10 position and will be going on ahead to the 2020 finals :)
<!-- more -->

During the event, I was focused on one problem alongside my teammate Arctic, our resident crypto expert. Special thanks to Robert as well for assisting me throughout the challenge! The problem we were focused on was the RF-Mobile challenge known as "Keybox". 

## Let's Begin!

Keybox is an android app. The zip file of the challenge contained a single APK file, which I popped into android studio to poke around in, and used ``jadx`` to manually look through the files as well. In the APK, the ``AndroidManifest.xml`` file was far more informative than it regularly would be, defining several unique intents as well as new behaviour for other ones. The app itself, "Keybox", was essentially a psuedo-spyware program - based on what you did while the app was open, it would listen in on any intents you made and act accordingly. 

{{< image src="/images/AppView.png" alt="AppView" position="center" style="border-radius: 8px;" >}}

The app itself was straightforward enough. The flag was in it, but encoded in rc4, with a specific key that was split across 5 values that themselves were encoded (also in rc4) with unique keys of their own. The idea was that you had to "solve" 5 levels in order to have the app decode the key fragment for you. There were hints (also encoded, but with the same key) that gave you a clue as to what to do to decrypt the key fragment. Once you decrypted all 5 keys, you combine it and use it as the key to decrypt the flag.

So, let's quickly review:

- the flag is encoded in rc4. The key for the flag's encoding algorithm is split into 5 other key bits.

- those key bits are also encoded in rc4, each key bit having their own unique encoding key. However, there are hints as to how to unlock them.

- those hints are encoded too. Luckily, the hints all share the same decryption key. 

Great! So we know nothing. Let's just jump into it.

### Key 0
First up, key 0. The hint was already decoded for us:

{{< image src="/images/Key0Hints.png" alt="Login" position="center" style="border-radius: 8px;" >}}

But the actual key bit itself was still unknown. So I poked around in the ``AndroidManifest.xml`` file and saw this: 

```xml
       <activity
            android:theme="@ref/0x7f0e0008"
            android:label="@ref/0x7f0d003a"
            android:name="com.trendmicro.keybox.KEY1HintActivity"
            android:exported="true"
            android:hint="Unlocking the hints requires sending the appropriate intent : adb shell am start-activity -a com.trendmicro.keybox.UNLOCK_HINT -n com.trendmicro.keybox/.KEY1HintActivity -e hintkey1 $PASSWORD"
            android:parentActivityName="com.trendmicro.keybox.KeyboxMainActivity">

            <intent-filter>

                <action
                    android:name="com.trendmicro.keybox.UNLOCK_HINT" />

                <category
                    android:name="android.intent.category.LAUNCHER" />
            </intent-filter>

            <meta-data
                android:name="android.support.PARENT_ACTIVITY"
                android:value="com.trendmicro.keybox.KeyboxMainActivity" />
        </activity>
        
```

This was to unlock the hint for key 1. It was an app-specific intent that required a password. The intent itself would actually decrypt the hint, with the password you provided it as the key (am I using 'key' too much here?). Since the actual "hint" in key 0 didn't really deliver information to me about _how_ to unlock key 0, I figured I would request the hint again. But I didn't know the password value(more on this later). So Arctic and I bounced a few ideas back and forth until I thought to sanity check and use the same password provided to us to unzip the challenge file.

{{< image src="/images/Key0Decrypt.png" alt="Login" position="center" style="border-radius: 8px;" >}}

I'm not sure why that worked. I'm not totally sure this was intended or not. But regardless, the key for key 0's encryption process was the password that was used to unzip the challenge file. Later on, Arctic mentioned that the real hint for key 0 we maybe never found out, and we ended up just correctly guessing the key to decode key0. 

### Key 1

So on a bit of lucky streak, I continued on to key 1, while Arctic decrypted the hints. Because we still didn't know the encryption key for the hints, Arctic used a scoring function to decode as much of the hints in the ciphertexts as possible. The bits and pieces she recovered for key1's hint was:

```
To unlock Key 1, you must call Trend Micro.
```

It was at this point that I was examining the other classes in the APK that I realized that the encryption key was hidden in the singleton class - ``TrendMicro``. 

Another teammate and mentor Robert quickly spun up a python script called ``decrypt.py`` to automate much of the decrypting process, now that we figured out the password. 
But the full hint for key 1 didn't reveal anything else. That was the whole hint. 

So now I had to look at and see how the app listens in for incoming and outgoing calls.

I spent a good portion of my time calling up all possible Trend Micro office numbers through my emulator, and simulating incoming calls from them, to no avail. And so I thought about it a bit more: 

In the observer class in the APK file, they instantiated a listener to look for incoming calls (the CALL_STATE android intent). The hint suggested to call the office, but the code itself seemed to only be paying attention to incoming callers, not calls you make. Wether or not the intention was to do both, I thought about how this would work. 

Since the app was listening in on call history, how would it then know when to decrypt KEY1? What is the encryption key it's looking for? Well, since the hint and the code all pointed to the act of making and recieving calls, then the encryption key to decode KEY1 must be a phone number. And wether or not I was having luck with dialing all possible offices to see if the app's listener would take notice, the components were all there. The encryption key had to be a phone number. We knew that KEY1 bit was encoded in rc4. So, couldn't we just take the .enc file and give it a couple Trend Micro phone numbers as a key, and run it through the rc4 decryption process? 

It turned out that the phone number for Trend Micro's Japan HQ was the key to decrypt KEY1. 

{{< image src="/images/key1Decrypt.png" alt="Login" position="center" style="border-radius: 8px;" >}}

### Key 2 

With that being said, key 2 was way easier to decrypt. The hint for it was simple: 

```
To unlock KEY2, send the secret code.
```

Which is sufficiently vague, but "secret codes" are meaningful in Android developement. 

Secret codes are specific sequences of numbers that could perform special actions. For example, the code ``232338`` would reveal your android phone's MAC address. Applications can also use them to perform secret tasks or unlock hidden features. If you wanted to test this out on your android device, open up your dialer and input a secret code as so: 
``*#*#123456#*#*``.

In the ```AndroidManifest.xml``` file, the SECRET_CODE intent is defined: 

```xml
            <intent-filter>
                <action android:name="android.provider.Telephony.SECRET_CODE"/>
                <data android:host="\ 8736364276" android:scheme="android_secret_code"/>
                <data android:host="\ 8736364275" android:scheme="android_secret_code"/>
            </intent-filter>
```

So the secret code in the app is the encryption key for KEY2!

You can either use ``abd`` to send the ``android.provider.Telephony.SECRET_CODE`` intent or just use the dialer in the emulator, as the app has a listener on the call function. Either way, you get KEY2.


{{< image src="/images/Key2Decrypt.png" alt="Login" position="center" style="border-radius: 8px;" >}}

## Key 3 

The hint for Key3 was: 

```
Unlock KEY3 by sending the right text message. 
```

This requires some explanation on the architecture of text messages in an android phone. 

SMS messages are kept in a relational database - specifically, sqlite. In the APK's observer class, we see some interesting checking going on:

```java
    invoke-interface {v5, v2}, Landroid/database/Cursor;->getColumnName(I)Ljava/lang/String;

    move-result-object v5

    invoke-virtual {v4, v5}, Ljava/lang/String;->equals(Ljava/lang/Object;)Z

    move-result v4

    if-eqz v4, :cond_a8

    iget-object v4, p0, Lcom/trendmicro/keybox/Observer;->cursor:Landroid/database/Cursor;

    iget-object v5, p0, Lcom/trendmicro/keybox/Observer;->cursor:Landroid/database/Cursor;

    const-string v6, "type"

    invoke-interface {v5, v6}, Landroid/database/Cursor;->getColumnIndex(Ljava/lang/String;)I

    move-result v5

    invoke-interface {v4, v5}, Landroid/database/Cursor;->getInt(I)I
```

What's happening here? Well, long story short, when we send an SMS message, the app will access the SMS sqlite database and iterate through the columns of that database, and match the content of the text message to the column name. If there's a match, then the app will unlock the 3rd key for us. This must mean that the string needed to decrypt the third key bit must be one of the column names! 

{{< image src="/images/Key3Decrypt.png" alt="key3" position="center" style="border-radius: 8px;" >}}

The column name that decrypted KEY3 turned out to be 'body'.


## Key 4
Despite KEY4 having the longest hint, and arguably the most amount of time to do, KEY4 was remarkably simple. The details are in the function of the ciphertext:

```js
// why actually calculate distance when you can just fake it ;)
        if(Math.abs(location_latitude - singleton_latitude) < 0.001 && Math.abs(location_longitude - singleton_longitude) < 0.001 ) {
            Log("Matched Location " + location.location);
            var SHA1 = new Hashes.SHA1;
            hash = SHA1.hex(location.location)
            if( hash == location.hash) {
                location_match = "Welcome to " + location.location;
            } else {
                location_match = "Welcome to " + location.location;
                singleton.push(location.hash);
            }
            break;
        } else {
            location_match = ""
        }
    }


    titleView.setText("Key Four Hints");
    textView.setText(
    "Visit " + /* all three of */ "the headquarters to unlock Key 4" + "\n\n" +
    location_match
    );

    return(true)`
```
 The hint specified to visit only the 3 headquarters, which were in the USA, Canada and Japan (Irving, Ontario and Tokyo respectively). While you can simulate your location in the emulator to pretend as if you visited all 3 locations, what it was looking for was the hash of the lat-lon location. It would check if that hash was among the list of accepted ones (which are only the hashes of the 3 HQs), and then append those hashes and use it as the key to decrypt KEY4. 

So, we needed to just append the hashes of the 3 headquarters, and input that as our decryption key to key4.enc. 

{{< image src="/images/Key4Decrypt.png" alt="Login" position="center" style="border-radius: 8px;" >}}

## The Flag

With all 5 keys decrypted, all that was left was to combine them all and decrypt the flag. 

``TMCTF{pzDbkfWGcE}``

Special thanks to my teammates, rctcwyvern and Robert for helping me out :) I'm thankful I spent my earlier CS years playing around in Android Studio. I unknowingly gained some valuable skills needed to reverse engineer the APK. Goes to show how every little thing counts! :P

### Vie