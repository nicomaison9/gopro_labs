# Full List the Action Commands

<script src="../../jquery.min.js"></script>
<script src="../../qrcodeborder.js"></script>
<style>
        #qrcode{
            height: 380px;
            width: 380px;
            margin: 0px;
        }
        div{
            height: 380px;
            width: 380px;
            margin: 0px;
            display: inline-block;
        }
</style>     

* **!**time**S** - Start at exactly **time** if in hh:mm form, or after n seconds. 
* **!**time**SQ** - Start Quickly at approximately **time** if in hh:mm form, or after n seconds.  
* **!**time**E** - End at **time** if in hh:mm form, or after n seconds.
* **!**time**R** - Repeat the whole command.
* **!**time**N** - No Action until exact **time**, useful if you just need a pause.
* **!**time**NQ** - No Action until at approximately **time**.
* **!**time**O** - Off, shutdown the camera.
* **!**time**OR** - shutdown and restart the camera.
* **!**time**U** - Attempt a network contention and Upload <sup>9/10/11</sup>.
* **!**time**W** - Connect to a network, requires JOIN metadata to have been previous stored <sup>9/10/11</sup>.
* **!**time**G(S or M or L)(C)** - Start Live-streaming, **S** - 480p, **M** - 720p, **L** - 1080p, optional **C** for capture a local file, requires a previously successful **!W** command <sup>8/9/10/11</sup>
* **!**time**Dx** - (H11) !Dx wait for GPS lock with a DOP less than x, then sync time. For time only locks !D is fine. 

## Capture Triggers

* **!**time**SMs-t** - Start Motion detection with start sensitivity 's' and stop sensitivity 't' -- 't' field is optional.
* **!**time**SMsDdMmHh** - Start Motion detection with sensitivity 's', Delay of 'd', Mask of 'm' and Hold for 'h' seconds (D,M and H fields are all optional.)
* **!**time**SKs-t** - Start Speed capture at threshold 's' km/h and stop at 't' km/h -- 't' field is optional.
* **!**time**SKsHh** - Start Speed capture at threshold 's' km/h and Hold for 'h' seconds (H fields are all optional.)
* **!**time**SAs-tDdHh** - Accelerometer Trigger
* **!**time**SGs-tDdHh** - Gyro Trigger
* **!**time**SIs-tDdHh** - IMU Trigger (both Accerometer and Gyro)
* **!**time**SDs-tDdHh** - Speed Pressure Level Trigger, 's' and 't' values are in dB.

## examples:

* **!S** - start now  
* **!07:00S** - start at precisely 7am  
* **!300SQ** - start in approximately 300 seconds.  
* **!60E** - end after 60 seconds (End commands are not used for photos.)
* **!2R** - repeat after 2 seconds
* **!s-01:00S** - start one hour before sunset
* **!nS** - start at true noon.
* **!s1200E** - end 1200 seconds after sunset
* **!r-600S** - start 600 seconds before sunrise
* **!17:00W!GLC** - start Live-streaming 1080p at 5pm, saving a local copy

## Storing metadata (Permanent, survives power off)

* **!M**fourcc**???string???**
* **!M**fourcc**=Number metadata**

Example for display the owner???s name
**!MOWNR=???Joe Bloggs???**

All tags between **OWNA** and **OWNZ** will be displayed and stored in **GoPro-owner.txt**.

All tags between **OWNa** and **OWNz** will be only stored in the **GoPro-owner.txt**

Any four character code can be used for store other information. You can also store numeric data examples: 
* **!MCAMR=53** - metadata CAMR will be 53 
* **!MABCD=45.234** - metadata ABCD will be floating point 45.234
* **!MUNIT=-1723** - metadata UNIT will be -1723 

## Storing metadata (Temporarily, until power off)

* **oM**fourcc**=???string???**  
* **oM**fourcc**=Number metadata**


## Reset Actions ##

* **!RESET** - erase all your permanent metadata (anything that used !Mxxxx command.)  (requires user confirmation.)
* **!FORMAT**<sup>10/11</sup> - for SD formatting via QR Code (requires user confirmation.)
* **!FRESET**<sup>10/11</sup> - Factory reset, erase everything, except QR code metadata (requires user confirmation.)
* **!PRESET**<sup>10/11</sup> - Presets reset, restore the default presets (requires user confirmation.)
* **!WRESET**<sup>10/11</sup> - WiFi Credentials reset, erase all your BLE and WiFi configurations (requires user confirmation.)

## Scripting

The geek factor is highest in this section.  This is not a Turing-complete language, but it can get many interesting capture control jobs done.  There are save and load commands, additive metadata and clock time conditionals

* **!SAVEname=script**  e.g. !SAVEdaily=dPmP!12:00S!Ldaily - a save script called ???daily??? that repeatedly shots one photo every day at noon. 
* **!Lname**  e.g. !LnightLapse - load add run a script called nightLapse
* **oAxxxx=1** e.g. oAMETA=1  --  to implement a basic counter in metadata
* **"any text"** e.g. mV"Video Mode"!S!5E!4NmP"Photo Mode"!S!5R - this will display "Video Mode" and "Photo Mode" when switch to those modes.  

### Conditional Based on Time

* **\<**time**CMD**  e.g. \<09:00!30R!Lother - if current time is less than 9am, wait 30mins and loop, otherwise load script called ???other???.
* **\>**time**CMD** e.g. !SM9\>22:00!R - do motion detection until 10PM, then stop
* **\>**timeA**\<**timeB**cmdTrue~cmdFalse** e.g. mP>06:00<20:00!180SQ~!06:00S!R - If time is between 06:00 and 20:00 take a photo in 180 seconds else start a 6am, repeat.
* **\>**timeA**\<**timeB**cmdTrue+cmdTrue~cmdFalse+cmdFalse** e.g. mP>06:00<20:00"wait 3m"+!180SQ~"wait tomorrow"+!06:00S!R - The '+' characters allow you to extend the conditional command, like changing mode before the action.

### Conditionals Based on Camera Status

Coming soon (HERO11), new conditional commands. Now \>x and/or \<x can be used to test camera states, where 'x' is the camera state to test:

* **a** accelerationValue - **\>aValue**CMD if(acceleration \> Value) then CMD, units in milli-Gs
* **b** batteryLevel - **\>bValue**CMD if(battery \> Value) then CMD, units in percentage
* **c** CoordDistance - **\>cDist**CMD or **\>cXDist**CMD if(distance \> Value) then CMD, units in meters. There are up to 27 pre-stored GPS locations oMFRMA=latt,long thru oMFRMZ.  If nothing in FRMx, initialize with current location.
* **d** GPS DOP - **\<dValue**CMD - if(DOP \< Value) then CMD, units in 100x DOP. GPS location precision.
* **e** random - **\<eValue**CMD \<e50 - 50% true \<e90 - 90% true.
* **g** gyroValue - **\>gValue**CMD if(gryo \> Value) CMD, numbers are in degrees per second.
* **h** heightValue - **\>hValue**CMD if(height \> Value) CMD, numbers are in meters above sealevel.
* **i** isoValue - **\>iValue**CMD - testing ISO where ISO ranges from 100 to 6400
* **k** speedValue - **\>kValue**CMD if(gps Speed \> Value) CMD e.g. >k45!S, numbers are in km/h.
* **l** loopNumValue - **\<lValue**CMD if(loop_count \< Value) CMD e.g. \<l45!R, this is the loop count for !R repeat, since last QR scan or boot.
* **p** soundpressureValue - **\>pValue**CMD if(spl \> Value) CMD, numbers are in dB
* **r** recording - **\>r**CMD1~CMD2 if(Recording) then CMD1 else CMD2 
* **s** shutterValue - **\>sValue**CMD - testing shutter, where 1/Value is used for shutter speed
* **tX** timedate - **\>tXValue**CMD - where X: Y-Year M-Month D-Day H-Hour N-miNute S-second W-day_of_the_Week B-seconds_since_Boot Q-seconds_since_Qrcode
* **u** USB power - **\>u**CMD1~CMD2 if(power is on USB) then CMD1 else CMD2


# Experiment Here

## Typing-in Your Custom Action:


<div id="qrcode"></div>
Custom Mode: <input type="text" id="tryit" value=""><br>


## updated 2023 Jan 25 
[BACK](..)


<script>
var once = true;
var qrcode;
var cmd = "";

function makeQR() {	
  if(once === true)
  {
    qrcode = new QRCode(document.getElementById("qrcode"), 
    {
      text : "\"Try It\"",
      width : 400,
      height : 400,
      correctLevel : QRCode.CorrectLevel.M
    });
    once = false;
  }
}

function timeLoop()
{
  if(document.getElementById("tryit") !== null)
  {
    cmd = document.getElementById("tryit").value;
	if(cmd.length === 0)
	{
		cmd = "\"Try It\"";
	}
  }
  else
  {
    cmd = "\"Try It\"";
  }

  qrcode.clear(); 
  qrcode.makeCode(cmd);
  var t = setTimeout(timeLoop, 50);
}

function myReloadFunction() {
  location.reload();
}

makeQR();
timeLoop();

</script>

