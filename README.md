in this topic, I wrote a small patch for the native Android keyboard in more than a little trick to edit the textInput of ofxUI

http://forum.openframeworks.cc/t/solved-implement-keyboard-ofxandroid/14962

I hope it is useful.

dario

----------------------------------------------------------------------------------------------------

I'm trying to implement within ofxAndroid the launch of the
keyboard.
Below is what I did, and all the files I touched:

This piece takes care of launching the system keyboard in android sdk.
ofxAndroid->ofAndroidLib->OFAndroid.java:

```
public void dyna_keyboardShow(){
		try{
		    final InputMethodManager inputMgr = (InputMethodManager)ofActivity.getSystemService(Context.INPUT_METHOD_SERVICE);
                    inputMgr.toggleSoftInput(InputMethodManager.SHOW_FORCED, 0);
                    System.out.println("!!!!call soft keyboard!!!!");
		}catch(Exception e){
		    System.out.println("catch:"+e);
		}
	}
```

here the jni for communication with the method java
ofxAndroid->src->ofAppAndroidWindow.cpp:

<code>
void ofAppAndroidWindow::dyna_keyBoardShow() {
   ofLog(OF_LOG_NOTICE,"start keyboard show");
   jclass javaClass = ofGetJNIEnv()->FindClass("cc/openframeworks/OFAndroid");
   if(javaClass==0){
      ofLogError("ofAppAndroidWindow") << "dyna_keyboardShow(): couldn't find OFAndroid java class";
      return;
      }
   jmethodID method = ofGetJNIEnv()->GetMethodID(javaClass, "dyna_keyboardShow","()V");
   if(!method){
      ofLogError("ofAppAndroidWindow") << "dyna_keyboardShow(): couldn't find OFAndroid dyna_keyboardShowHide method";
      return;
      }
   else{
      ofGetJNIEnv()->NewObject(javaClass,method);
      }
   }
</code>

function declaration
ofAppAndroidWindow.h:

<code>
void dyna_keyBoardShow();
</code>

to make available the method level ofAppRunner I did this:
ofAppRunner.cpp:

<code>
void dynaKeyShow() {
   window->dyna_keyBoardShow();
}
</code>

function declaration ofAppRunner.h:

<code>
void dynaKeyShow();
</code>

Then I added the virtual in ofAppBaseWindow.h

<code>
virtual void dyna_keyBoardShow(){};
</code>

I noticed the keyPressed basis of OF succeeds in catching keyboard events
Android without the use of a "displathkeyevent" jni side, so I decided to unleash the keyboard on a ofxUITextInput, I have to do this trivially added to the inside of the method mousePressed between ifdef OFX_UI_TARGET_TOUCH of ofxUITextInput.cpp this:

<code>
dynaKeyShow();
ofLog(OF_LOG_NOTICE,"pressed UI");
</code>

I hope it is useful for someone smile
I will update the topic when I can to impute the TextInput
