## Task Description:
Now that's a BIG plane! I wonder where it is. Flag format: uiuctf{plane type, coordinates of the aircraft} Example: uiuctf{Airbus A380-800, 40.036, -88.264}

For coordinates, just omit the digits, do not round up. Precision is the same as the one in the example. The aircraft name is the same as Wikipedia page title. You can extract enough information from this image to answer this. You DO NOT need to register any accounts, all the information is public.

Flag format clarification: The last digit of the first coordinate is even, and the last digit of the second coordinate is odd.
![[chal.jpg]]

## Solution

#### What do we see
So the subject is clearly the Globemaster (description calls it a BIG plane)
Zooming in, its fleet number can be seen on the tail as: 77182
some mountain-like vegetation can also be seen (this will help later)
also we know which runways we're seeing the plane from because of the runway markers

#### What do we not see
running `exiftool` on the image, we get
```
[manogya@cntr]-[~/Desktop/comps/UIUCTF]
 > exiftool chal.jpg      
ExifTool Version Number         : 12.76
File Name                       : chal.jpg
Directory                       : .
File Size                       : 3.4 MB
File Modification Date/Time     : 2024:07:07 20:49:50+05:30
File Access Date/Time           : 2024:07:07 20:49:52+05:30
File Inode Change Date/Time     : 2024:07:07 20:58:38+05:30
File Permissions                : -rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
Exif Byte Order                 : Little-endian (Intel, II)
Make                            : samsung
Camera Model Name               : Galaxy S24+
Orientation                     : Horizontal (normal)
X Resolution                    : 72
Y Resolution                    : 72
Resolution Unit                 : inches
Software                        : S926U1UEU1AXCB
Modify Date                     : 2024:05:11 16:44:28
Y Cb Cr Positioning             : Centered
Exposure Time                   : 1/1245
F Number                        : 2.4
Exposure Program                : Program AE
ISO                             : 25
Exif Version                    : 0220
Date/Time Original              : 2024:05:11 16:44:28
Create Date                     : 2024:05:11 16:44:28
Offset Time                     : -07:00
Offset Time Original            : -07:00
Shutter Speed Value             : 1
Aperture Value                  : 2.4
Brightness Value                : 9.78
Exposure Compensation           : 0
Max Aperture Value              : 2.4
Metering Mode                   : Center-weighted average
Flash                           : No Flash
Focal Length                    : 7.0 mm
Sub Sec Time                    : 107
Sub Sec Time Original           : 107
Sub Sec Time Digitized          : 107
Flashpix Version                : 0100
Color Space                     : Uncalibrated
Exif Image Width                : 4000
Exif Image Height               : 3000
Exposure Mode                   : Auto
White Balance                   : Auto
Digital Zoom Ratio              : 3
Focal Length In 35mm Format     : 69 mm
Scene Capture Type              : Standard
Image Unique ID                 : F11XLPE00SM
Compression                     : JPEG (old-style)
Thumbnail Offset                : 866
Thumbnail Length                : 44373
Profile CMM Type                : 
Profile Version                 : 4.3.0
Profile Class                   : Display Device Profile
Color Space Data                : RGB
Profile Connection Space        : XYZ
Profile Date Time               : 2022:07:01 00:00:00
Profile File Signature          : acsp
Primary Platform                : Unknown (SEC)
CMM Flags                       : Not Embedded, Independent
Device Manufacturer             : Unknown (SEC)
Device Model                    : 
Device Attributes               : Reflective, Glossy, Positive, Color
Rendering Intent                : Perceptual
Connection Space Illuminant     : 0.9642 1 0.82491
Profile Creator                 : Unknown (SEC)
Profile ID                      : 0
Profile Description             : DCI-P3 D65 Gamut with sRGB Transfer
Profile Copyright               : Copyright (c) 2022 Samsung Electronics Co., Ltd.
Media White Point               : 0.9642 1 0.82491
Chromatic Adaptation            : 1.04781 0.02289 -0.05013 0.02954 0.99048 -0.01704 -0.00923 0.01505 0.75214
Red Matrix Column               : 0.51508 0.24117 -0.00105
Green Matrix Column             : 0.29195 0.69223 0.04189
Blue Matrix Column              : 0.15718 0.06659 0.78455
Red Tone Reproduction Curve     : (Binary data 32 bytes, use -b option to extract)
Green Tone Reproduction Curve   : (Binary data 32 bytes, use -b option to extract)
Blue Tone Reproduction Curve    : (Binary data 32 bytes, use -b option to extract)
XMP Toolkit                     : Adobe XMP Core 5.1.2
Version                         : 1.0
Directory Item Semantic         : Primary
Directory Item Mime             : image/jpeg
Directory Item Length           : 37993
MPF Version                     : 0100
Number Of Images                : 2
MP Image Flags                  : (none)
MP Image Format                 : JPEG
MP Image Type                   : Undefined
MP Image Length                 : 37993
MP Image Start                  : 3351163
Dependent Image 1 Entry Number  : 0
Dependent Image 2 Entry Number  : 0
Image Width                     : 4000
Image Height                    : 3000
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Time Stamp                      : 2024:05:12 05:14:28.107+05:30
MCC Data                        : United States / Guam (310)
Aperture                        : 2.4
Image Size                      : 4000x3000
Megapixels                      : 12.0
Scale Factor To 35 mm Equivalent: 9.9
Shutter Speed                   : 1/1245
Create Date                     : 2024:05:11 16:44:28.107
Date/Time Original              : 2024:05:11 16:44:28.107-07:00
Modify Date                     : 2024:05:11 16:44:28.107-07:00
Thumbnail Image                 : (Binary data 44373 bytes, use -b option to extract)
MP Image 2                      : (Binary data 37993 bytes, use -b option to extract)
Circle Of Confusion             : 0.003 mm
Field Of View                   : 29.2 deg
Focal Length                    : 7.0 mm (35 mm equivalent: 69.0 mm)
Hyperfocal Distance             : 6.70 m
Light Value                     : 14.8

```

That's an ocean of data but the most relevant thing here is
` Create Date                      : 2024:05:11 16:44:28`
` Offset Time                      : -07:00`

So we now have both the time of creation and the time zone where this photo was taken.

#### What more can be known?
The Fleet Number is basically *the* treasure trove here. We can use any open source aircraft tracker to find exactly where it has been at any point in history.

Here let's try searching on `globe.adsbexchange.com`
Entering the fleet number and the date, we get
![[Pasted image 20240707210825.png]]

Considering time zones, by 16:44 local time it would have landed on Seattle-Tacoma
So we now know which airport this photo is from, the next step is simple, open Google Earth and try to match the exact spot. Thankfully there are some "hooks" which we can look for.
![[Pasted image 20240707212431.png]]
We can try to look for this text
![[Pasted image 20240707212505.png]]
This color scheme may also help
#### Pinpointing the location

![[Pasted image 20240707212728.png]]
This one was literally the first building from the left, looking from the runways
The other buildings with the striped pattern are also visible

![[Pasted image 20240707213010.png]]
Recreating the view, the plane seems to be have been where the mark is.

Now we'll convert the coordinates of the mark from DMS to DD
![[Pasted image 20240707213231.png]]
truncating it, we find our coordinates and therefore our flag: `UIUCTF{47.462, -122.303}`
