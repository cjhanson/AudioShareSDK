AudioShare SDK
==============

This SDK will let you export audio to, or import audio from, the AudioShare app for iPhone, iPad and iPod touch.

AudioShare - audio document manager, is a simple tool to manage all your sounds in a single
place on your device, with easy transferring of sounds between AudioShare and other apps or
your computer. Read more about it here: http://kymatica.com/audioshare

Usage
-----
First, copy the files `AudioShareSDK.h` and `AudioShareSDK.m` into your project.

Don't forget to import the header:

    #import "AudioShareSDK.h"

For ARC-enabled projects, such as new Xcode 6 projects
------------------------------------------------------
You must disable ARC for the `AudioShareSDK.m` file, or else your project will return errors and fail to build and run.

1. Select the project in the left-hand Project Navigator tree.

2. Click on the "Build Phases" tab at the top.

3. Click on "Compile Sources" category to show the enclosed files.

4. Select to highlight the `AudioShareSDK.m` file.

5. Double-click on the right-hand side of the selected row, under the "Compiler Flags" column.

6. Type: `-fno-objc-arc` into the field, and press the return key to commit the new compiler flag.

Export to AudioShare
--------------------
The typical usage is to have a button or menu item labelled "Export to AudioShare" that
transfers a soundfile from your own app into AudioShare. Just put the following line in
the code that gets called when the user taps the button:

    [[AudioShare sharedInstance] addSoundFromPath:thePathToYourFile withName:@"My Sound"];

In case you have your sound in memory, you can send an NSData instead:

    [[AudioShare sharedInstance] addSoundFromData:yourSoundData withName:@"My Sound"];

These methods will check that a recent enough version of AudioShare is installed, and otherwise
present the user with the option to view the app on the App Store. If AudioShare was installed,
it will open AudioShare and import the audio, and return YES if successfull.

Import from AudioShare
----------------------
Since AudioShare version 2.5, you can also easily import sound from AudioShare into your own app.

1. First, declare a new URL type in your Info.plist with the scheme `yourAppName.audioshare`. Replace yourAppName with a unique name for your app, but keep the `.audioshare` suffix.

2. Then, in your app delegates openURL handler method, call `checkPendingImport:withBlock:` to handle the import:

        if([[AudioShare sharedInstance] checkPendingImport:url withBlock:^(NSString *path) {

          // Move the temporary file into our Documents folder
          NSArray *paths = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);
          NSString *documentsDirectory = [paths objectAtIndex:0];
          NSString *destination = [documentsDirectory stringByAppendingPathComponent:[path lastPathComponent]];
          [[NSFileManager defaultManager] moveItemAtPath:path toPath:destination error:nil];

          // Load the imported file
          [mySoundEngine loadSample:destination];

        }]) {
          return YES;
        }

3. To initiate the import from your app, add a button named "Import from AudioShare" which simply calls:

        [[AudioShare sharedInstance] initiateSoundImport];
    
This will launch AudioShare (if version 2.5 or later is installed), which will display an "Import into app: YourAppName" button. When the user taps this button in AudioShare, it will launch your application where the call to `checkPendingImport:withBlock:` will grab the imported soundfile.

Multithreading
--------------
The addSoundFrom* and initiateSoundImport methods should always be called from the main thread. If you need to call it from another thread, you must wrap the call in a block dispatched on the main thread. Example:

        dispatch_async(dispatch_get_main_queue(), ^(void) {
            [[AudioShare sharedInstance] addSoundFromURL:theUrlToYourFile
                                                withName:@"My Sound"];
        });

Supported formats
-----------------

AudioShare supports all soundfile formats, bit depths and rates, that has built-in support in iOS: AIFF, AIFC, WAVE, SoundDesigner2, Next, MP3, MP2, MP1, AC3, AAC_ADTS, MPEG4, M4A, CAF, 3GP, 3GP2, AMR. Additionally, it supports standard MIDI files.

License
-------

You are free to incorporate this code in your own application, for the purpose of launching
and/or transferring sounds to/from the AudioShare app.

If you do, I would appreciate if you drop me a message at lijon@kymatica.com

Copyright (C)2012-2014 Jonatan Liljedahl

http://kymatica.com
