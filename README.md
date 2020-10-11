# babysoother
Documenting the journey to build a baby soother with raspberry pi and PS3 Eye Toy

Aim:
1. Observe baby motion, and Baby Cry with PS3 Eye Toy and its microphone array,
2. private network observation platform, non IoT for now.
3. play soothing music if baby cry.

Required Materials:
1. Raspberry Pi 3 or newer with Raspbian OS installed on SD Card, 
2. PS3 Eye toy or any other Raspberry Pi compatible webcam
3. Microphone array
4. Sound output equipment, 3.5mm connected Speaker, HDMI out and so on.

Steps
1. Get Raspberry Pi ready with Raspbian OS installed and installed all recent updates with sudo apt-get update && upgrade
2. Configure the PS3 Eye toy with the following files:
Configuring ALSA for Sony Play Station Eye (Raspberry PI) :

We have the Sony Playstation eye setup in udev to find it reliably:

/etc/udev/rules.d/70-alsa-permanent.rules

SUBSYSTEM!="sound", GOTO="my_usb_audio_end"
ACTION!="add", GOTO="my_usb_audio_end"

ATTRS{idVendor}=="1415", ATTRS{idProduct}=="2000", ATTR{id}="VOICE"

Now – in .asoundrc , we’ll have to address two issues: The down mixing of the 4 separate microphone streams and the adjustment of the recording levels.

That’s the device we’re using:

pcm.array {
 type hw
 card VOICE
}

We route the signal through the softvolume stage that allows us to increase or decrease the recording volume via the simple “Mic Gain” control

pcm.array_gain {
 type softvol
   slave {
   pcm "array"
   }
 control {
   name "Mic Gain"
   count 2
   card 0
   }
 min_dB -40.0
 max_dB 10.0
 resolution 80
}

Finally we use a third stage to combine all 4 microphone streams into a single one.

pcm.cap {
 type plug
 slave {
   pcm "array_gain"
   channels 4
   }
 route_policy sum
}

You can now record from “cap” or define it as default.

Source: https://blog.michaelamerz.com/wordpress/trying-respeaker-mic-array-v2-0/
