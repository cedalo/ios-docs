# MQTT Connect
## Overview
This iOS App serves mainly as a client for users to interact with the Cedalo Streamsheet. The connection to the Streamsheet is established over MQTT. However the app is not limited to the use as a Streamsheet client as it can also be controlled by any other MQTT connections.
It can display information user friendly and also ask the user to enter values and select options which can then be sent back to a certain topic. Also it is possible to send sensor values of the iPhone either in a set interval or on every incoming message. Furthermore the content of QR-codes, messages stored in NFC tags or pictures shot on the iPhone's camera can also be published.

## MQTT
MQTT stands for Message Queuing Telemetry Transport. It is a network protocol based on the publish-subscribe pattern. For the time being it is the only messaging protocol supported by MQTT Connect. It requires a server to communicate over - a so-called broker.
Every client can choose a topic to publish messages to and also subscribe topics to receive messages from. A topic could look like this: 'company/department/colleague'. To include all subtopics you can simply use a wild card(#). Subscribing to the topics of all colleagues of a certain department can be done like this: 'company/department/#'.

## First Steps
In order to establish an MQTT connection simply click on the settings icon and fill in the required parameters. Which are required include to a URL and a port and might also be complemented by a username and password if required by the MQTT broker.

## Sending Messages
Immediately after having received a message in the correct JSON format the App will switch to the "messages" tab and show the current message. Messages may include a title, a subtitle, a comment, an image, options to choose from and input fields which can be filled out by the user. It can also be specified whether only one option or multiple can be selected. For further actions the message can also contain the name of a tab the App should switch to after a given amount of time. Please have a look at the accepted JSON format below.

## Scanning QR codes
QR-codes can be scanned in the "QR" tab. It will open up a camera view which automatically detects any QR-codes or data matrices. If a QR-code got detected the App will send it's content will be sent to the topic specified in settings. In case the QR-code holds a valid JSON it will not be converted to a String but also be represented as a object. Please note that your iPhone's privacy settings must allow MQTT Connect to access your camera.

## Scanning NFC tags
In order to search for NFC tags proceed to the "NFC" tab. The "Start Scanning" button will initiate a search session which is indicated by a pop-up view with further instructions. If an NFC tag is being detected it's content is published to the topic selected. Just as with QR-codes contents consisting of valid JSON are being detected and retained as such.

## Sending User-Made Pictures
Pictures made by the user can also be sent in the "Camera" tab. After the shutter release was triggered a picture is taken and will be published instantaneously. Pictures will be decoded as base64.

## Sending sensor data of your iPhone
iPhone sensor values can be sent either regularly or triggered by an incoming message. The regular publishing of sensor values can be enable on the home screen in the top right corner. The time interval can be specified in Settings. You will also find the switch to toggle information sending on incoming message. Sensor values include rotation, acceleration, proximity, GPS, magneticfield, orientation, battery percentage and gravity. For further information regarding on the JSON encoding please read the JSON format below.

## Modifying Preferences Remotely
All the settings on iPhone can also be change with a so-called settings message. This settings message can contain a change to every single value set in settings. However, please be aware that all values changed by such a message are only temporarily changed an will be set back to their previous settings when disconnecting. After the new settings have been set a receipt will be sent back with an updated list of all the settings.

## Session Object
A session object is a JSON object which once set will be attached to every message published. It's purpose is to store values needed for further computation in the Streamsheet. It can be set by adding it to any kind of message sent to the iPhone on the first level of the JSON with the name "SessionObject". Setting a session object will override the one previously saved.

## JSON Formats
### Dialog Message [RECEIVES]
```
{
    "DialogMessage": {
        "Maintitle": "Sample Maintitle",
        "Subtitle": "Sample Subtitle",
        "Image": "data:image/jpeg;base64…",
        "Comment": "This is a  sample comment",
        "InputFields": [
            {
                "Label": "Alter",
                "Key": "Age",
                "Type": "NumberField",
                "DefaultValue": "42",
                "InstantSending":true,
                "Required":false
            },
            {
                "Label": "Text",
                "Key": "Text",
                "Type": "TextView",
                "DefaultValue": "Don't panic!",
                "InstantSending":false,
                "Required":false
            },
            {
                "Label": "Brille",
                "Key": "Glasses",
                "Type": "Switch",
                "DefaultValue": "False",
                "InstantSending":false,
                "Required":false
            },
            {
                "Label": "Liter",
                "Key": "Liter",
                "Type": "Slider",
                "MinimumValue":0,
                "MaximumValue":1,
                "DefaultValue": 0.5,
                "InstantSending":false,
                "Required":false
            },
            {
                "Label": "Monitore",
                "Key": "Displays",
                "Type": "Stepper",
                "MinimumValue":1,
                "MaximumValue":5,
                "DefaultValue":2,
                "InstantSending":false,
                "Required":false
            },
            {
                "Label": "Name",
                "Key": "Name",
                "Type": "TextField",
                "DefaultValue": "Kristian",
                "InstantSending":false,
                
                "Required":true
            }
        ],
        "Options": [
            {
                "Label": "SampleOption1",
                "Topic": "optional/topic/toSend/replyTo/ifnot/set/use/default",
                "ReplyObject": {
                    "TestAttr1": {
                        "Testattr11": "",
                        "Testattr12": ""
                    },
                    "TestAttr2": {}
                }
            },
            {
                "Label": "SampleOption2",
                "ReplyObject": {
                    "TestAttr3": {
                        "Testattr31": "",
                        "Testattr32": ""
                    },
                    "TestAttr4": {}
                }
            },
            {
                "Label": "SampleOption3",
                "ReplyObject": {
                    "TestAttr5": {},
                    "TestAttr6": {
                        "TestAttr7": ""
                    }
                }
            }
        ],
        "OptionsMode": "None/Single/Multiple",
        "SwitchToTab": "QR/NFC/Camera",
        "SwitchDelay": 1000,
        "AlertCode": 23,
        "FlashDuration": 0
    }
}
```

### Settings Message [RECEIVES]
```
{
    "iPhoneID": "9A093D8F-D1A2-4E2B-8E20-04DA0897578A",
    "Settings": {
        "SubscribeTopic": [
            "hello/world",
            "hello/mars"
        ],
        "PublishTopic": "cedalo/iPhone",
        "OnIncoming": false,
        "OnInterval": false,
        "IntervalOnChange": false,
        "OnThreshold": false,
        "IntervalCycle": 500,
        "ThresholdValue": 100
    }
}
```

### Sensor Values [SENDS]
```
{
  "Timestamp": 123456,
  "Proximity": false,
  "Battery": -100,
  "Acceleration": {
	  "LinearX": 0.0,
	  "LinearY": 0.0,
	  "LinearZ": 0.0
  },
  "Beacons": [
  	  "Beacon1": {
	    ...
	    ...
            ...
          },
	  "Beacon2": {
	    ...
	    ...
	    ...
	  }
  ],
  "Altitude": {
	  "Pressure": 0.0,
	  "Relative": 0.0
  },
  "GPS": {
    "Longitude": 0.0,
    "Latitude": 0.0,
    "Altitude": 0.0
  },
  "Gravity": {
	  "GravityX": 0.0,
	  "GravityY": 0.0,
	  "GravityZ": 0.0
  },
  "Magneticfield": {
	  "AxisX": 0.0,
	  "AxisY": 0.0,
	  "AxisZ": 0.0
  },
  "Orientation": {
    "Roll": 0.0,
    "Yaw": 0.0,
    "Pitch": 0.0
  },
  "Rotation": {
	  "RotationRoll": 0.0,
	  "RotationYaw": 0.0,
	  "RotationPitch": 0.0
  }
}
```


### QR-Code Content [SENDS]
```
{
    "iPhoneID": "9A093D8F-D1A2-4E2B-8E20-04DA0897578A",
    "iPhoneName": "Cedalo iPhone",
    "Model": "iPhone8,4",
    "QRContent": "cedalo.com",
    "Rotation": {
        "RotationRoll": 3,
        "RotationPitch": -2,
        "RotationYaw": 2
    },
    "Acceleration": {
        "LinearX": 0,
        "LinearY": 0,
        "LinearZ": -1
    },
    "Proximity": false,
    "GPS": {
        "Altitude": 269.8218,
        "Latitude": 47.9942,
        "Longitude": 7.8397
    },
    "Magneticfield": {
        "AxisX": 0,
        "AxisY": 0,
        "AxisZ": 0
    },
    "Orientation": {
        "Yaw": 0,
        "Pitch": 53,
        "Roll": -4
    },
    "Battery": 50,
    "Gravity": {
        "GravityX": -2,
        "GravityY": -45,
        "GravityZ": -34
    }
}
```
