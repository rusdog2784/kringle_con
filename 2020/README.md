# KRINGLE CON 2020 README



## Objectives

### 1: Uncover Santa's Gift List (folder: objective01)
There is a photo of Santa's Desk on that billboard with his personal gift list. What gift is Santa planning on getting Josh Wright for the holidays? Talk to Jingle Ringford at the bottom of the mountain for advice.

* Hints: 
	* Online image tool, Photopea (https://www.photopea.com/).

* Steps:
	1. Downloaded the billboard image (https://2020.kringlecon.com/textures/billboard.png).
	2. Noticed a piece of the image that was swirled. It was the Gift List containing Josh Wright's gift.
	3. Cropped out the swirled image and saved it as a new image 'billboard-swirl.png'.
	4. Used the online tool, GifGit (https://www.gifgit.com/image/swirl-image), to unswirl 'billboard-swirl.png'.
	5. Unswirled the image 'enough' to where I could see what John Wright wanted for christmas.

* Answer:
	```
	proxmark	(it's a RFID swiss-army tool)
	```


### 2: Investigate S3 Bucket (folder: objective02)
When you unwrap the over-wrapped file, what text string is inside the package? Talk to Shinny Upatree in front of the castle for hints on this challenge.

* Hints:
	* Santa's Wrapper3000 is pretty buggy. It uses several compression tools, binary to ASCII conversion, and other tools to wrap packages.

* Steps:
	1. Inside the terminal, navigate to /home/elf/bucket_finder.
	2. Create wordlist, titled 'words' with the words: 'Wrapper3000' and 'wrapper3000' inside of the directory.
	3. Run this command to verify the wrapper3000 bucket exists: `./bucket_finder.rb words`
		* This tells us that the bucket, wrapper3000, is public and available for download.
	4. Run this command to download the wrapper3000 bucket: `./bucket_finder.rb --download words`
		* This downloads the bucket into the current directory under the sub-directory, wrapper3000.
		* Alternatively, you could just paste the bucket address into your browser and download it onto your local machine. I did this so I could use my own tools.
	5. The only content in the bucket was a file called 'package' that contained the following text:
		```
		UEsDBAoAAAAAAIAwhFEbRT8anwEAAJ8BAAAcABwAcGFja2FnZS50eHQuWi54ei54eGQudGFyLmJ6MlVUCQADoBfKX6AXyl91eAsAAQT2AQAABBQAAABCWmg5MUFZJlNZ2ktivwABHv+Q3hASgGSn//AvBxDwf/xe0gQAAAgwAVmkYRTKe1PVM9U0ekMg2poAAAGgPUPUGqehhCMSgaBoAD1NNAAAAyEmJpR5QGg0bSPU/VA0eo9IaHqBkxw2YZK2NUASOegDIzwMXMHBCFACgIEvQ2Jrg8V50tDjh61Pt3Q8CmgpFFunc1Ipui+SqsYB04M/gWKKc0Vs2DXkzeJmiktINqjo3JjKAA4dLgLtPN15oADLe80tnfLGXhIWaJMiEeSX992uxodRJ6EAzIFzqSbWtnNqCTEDML9AK7HHSzyyBYKwCFBVJh17T636a6YgyjX0eE0IsCbjcBkRPgkKz6q0okb1sWicMaky2Mgsqw2nUm5ayPHUeIktnBIvkiUWxYEiRs5nFOM8MTk8SitV7lcxOKst2QedSxZ851ceDQexsLsJ3C89Z/gQ6Xn6KBKqFsKyTkaqO+1FgmImtHKoJkMctd2B9JkcwvMr+hWIEcIQjAZGhSKYNPxHJFqJ3t32Vjgn/OGdQJiIHv4u5IpwoSG0lsV+UEsBAh4DCgAAAAAAgDCEURtFPxqfAQAAnwEAABwAGAAAAAAAAAAAAKSBAAAAAHBhY2thZ2UudHh0LloueHoueHhkLnRhci5iejJVVAUAA6AXyl91eAsAAQT2AQAABBQAAABQSwUGAAAAAAEAAQBiAAAA9QEAAAAA
		```
	6. The text is clearly encoded so to decode it, follow these steps:
		1. Decode using Base64: `base64 -d package > package-base64`
			* This decodes what is in 'package' and places the decoded value into a new file, 'package-base64'.
		2. Check the file type of the newly created file: `file package-base64`
			* Output: Zip archive data, at least v1.0 to extract.
		3. Unzip 'package-base64' file: `unzip package-base64`
			* Creates 'package.txt.Z.xz.xxd.tar.bz2', which is bzip2 file (used the file command).
		4. Unzip (bzip2) 'package.txt.Z.xz.xxd.tar.bz2': `bzip2 -d package.txt.Z.xz.xxd.tar.bz2`
			* Creates 'package.txt.Z.xz.xxd.tar', which is a tar file (used the file command).
		5. Unzip (tar) 'package.txt.Z.xz.xxd.tar': `tar -xf package.txt.Z.xz.xxd.tar`
			* Creates 'package.txt.Z.xz.xxd', which contains ASCII text (used the file command).
		6. Decode Hex into binary (xxd reversed): `xxd -r package.txt.Z.xz.xxd > package.txt.Z.xz`
			* This decodes what is in 'package.txt.Z.xz.xxd' file and places the decoded value into a new file, 'package.txt.Z.xz'.
			* Check the new file's type (use the file command): XZ compressed data.
		7. Unzip (xz) 'package.txt.Z.xz': `xz -d package.txt.Z.xz`
			* Creates 'package.txt.Z', which is a compress'd data 16 bits file (used the file command).
		8. Uncompress 'package.txt.Z': `uncompress package.txt.Z`
			* Created 'package.txt', which is a normal text file.
		9. Read what is inside 'package.txt': `cat package.txt`
			* Output: 'North Pole: The Frostiest Place on Earth'

* Answer:
	```
	North Pole: The Frostiest Place on Earth
	```

### 3: Point-of-Sale Password Recovery (folder: objective03)
Help Sugarplum Mary in the Courtyard find the supervisor password for the point-of-sale terminal. What's the password?

* Hints:
	* It's possible to extract the source code from an Electron app.
	* https://medium.com/how-to-electron/how-to-get-source-code-of-any-electron-application-cbb5c7726c37

* Steps:
	1. Download the offline version of "Santa Shop" (a.k.a.: click on 'Download offline version to inspect').
	2. Extract 'app-64.7z' into a directory of your choosing (work within this directory for the next steps).
	3. Unzip 'app-64.7z' using 7z with the command: `7z x app-64.7z`
	4. From the extracted/unzipped files, only keep the 'resources' folder.
	5. Install the node module, asar, globally: `npm install -g asar`
	6. Make a new directory called 'extracted-source-code': `mkdir extracted-source-code`
	7. Extract 'resources/app.asar' into the 'extracted-source-code' directory using the asar command: `asar extract resources/app.asar extracted-source-code`
	8. Cd into the 'extracted-source-code' directory and use the following linux commands to search for the password:
		1. Search for any instances of 'password': `grep -rnw . -e "password"`
			* Yielded a few results. One of which was:
			```
			./main.js:126:  return (password === SANTA_PASSWORD);
			```
		2. Search for any instance of 'SANTA_PASSWORD': `grep -rnw . -e "SANTA_PASSWORD"`
			* Yielded: 
			```
			./main.js:5:const SANTA_PASSWORD = 'santapass';
			```

* Answer:
	```
	santapass
	```

### 4: Operate the Santavator
Talk to Pepper Minstix in the entryway to get some hints about the Santavator.

* Hints:
	* It's really more art than science. The goal is to put the right colored light into the receivers on the left and top of the panel.
	* 

* Steps:
	1. Need to find (at minimum) the following items:
		* Green Bulb
		* Hex Nut
		* Elevator Service Key

* Answer:
	```
	Change the flow to green with the Green Bulb then direct the green flow into the green opening.
	```


### 5: Open HID Lock
Open the HID lock in the Workshop. Talk to Bushy Evergreen near the talk tracks for hints on this challenge. You may also visit Fitzy Shortstack in the kitchen for tips.

* Hints:
	* The Proxmark is a multi-function RFID device, capable of capturing and replaying RFID events.
	* You can use a Proxmark to capture the facility code and ID value of HID ProxCard badge by running `lf hid read` when you are close enough to someone with a badge.

* Steps:
	1. 

* Answer:
	```
	```


## Tasks

### Unescape Tmux
* Answer:
	```
	tmux attach
	```


### Kringle Kiosk
* Steps:
	1. Opened the 'Welcome' kiosk (the one by Shinny Upatree that allows you to print your badge).
	2. Ran option #4 (print badge).
	3. When it asks you to enter your name, I entered: `&& /bin/bash -p`, which gave me access to shinny's terminal.


### Speaker UNPrep
* Hints:
	* The strings command is common in Linux and available in Windows as part of SysInternals.
	* While you have to use the lights program in /home/elf/ to turn the lights on, you can delete parts in /home/elf/lab/.

* Steps:
	1. Open the terminal to the left of Bushy Evergreen.
	2. Type: `strings door | grep "password"`
		* The door password is 'Op3nTheD00r'

* Answer:
	* door: `Op3nTheD00r`
	* lights: 

