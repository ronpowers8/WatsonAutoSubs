# WatsonAutoSubs
Guide and .py script for generating subtitles from Audio/Video files for free. I tried to make it as user friendly as possible for someone with next-to-zero knowledge of CLI environments. 

This methodology is comprised of 3 parts
1. Extract the audio from video files to prepare them for part 2. This step is optional but saves significant time and bandwith in step 2
2. Generate text-based json output of audio input using IBM Watson speech-to-text
3. Convert json to .srt

# Notes
- Parts 1 and 2 are executed in Linux via Windows/WSL2. Some commands could be slightly different if working from PowerShell or Linux directly.
- Part 3 is executed in Windows.
- IBM provides free-account holders up to 500 minutes per month of the speech-to-text service. Big thank you to IBM for being cool. 
- The original json_to_srt.py was grabbed from https://gist.github.com/tansautn and modified to be portable in a Windows environment. Thank you @tansautn for doing the heavy lifting!
- Anywhere you see something in [brackets] you'll need to specify the information yourself

# Prerequisites:
- ffmpeg, installed and accessible on system PATH
- python3, installed and accessible on system PATH
- curl
- [IBM Cloud account](https://cloud.ibm.com)
- On IBM Cloud website, provision text-to-speech service. For more detail watch IBM's how-to video [here](https://cloud.ibm.com/docs/speech-to-text?topic=speech-to-text-gettingStarted#getting-started-tutorial)

# Part 1 - Extract Audio from Video
a) In a terminal, navigate to the folder containing your video file

b) run

	ffmpeg.exe -i [VideoFileName.extension] -vn [audio].mp3

c) ffmpeg will save [audio].mp3 in the current working folder

# Part 2 - Generate JSON outfile using Watson speech-to-text
a) Go to cloud.ibm.com and 

    i.   Generate a 1 time passcode by clicking top right corner -> Log in to CLI and API
         # steps ii to iv are a copypaste from the IBM Cloud website (https://cloud.ibm.com/docs/speech-to-text?topic=speech-to-text-gettingStarted#getting-started-tutorial):
    ii.  From the IBM Cloud Resource list (https://cloud.ibm.com/resources), click on your Speech to Text service instance to go to the Speech to Text service dashboard page.
    iii. On the Manage page, click Show Credentials to view your credentials.
    iv.  Copy the API Key and URL values. They are needed for step e) below

b) From terminal, bang in

	ibmcloud login -a https://cloud.ibm.com -u passcode -p [ CODE GENERATED IN Part 2a)i ]

c) Choose your region. For me it's 9 - us-south

d) Add resource group

	ibmcloud target -g Default

e) Generate json transcript. Note that it will take a while for Watson to do its thing:

        curl -X POST -u "apikey:[APIkey]" \
        --header "Content-Type: audio/mp3" \ 
        --data-binary @[audio].mp3 \ 
        "[URL]/v1/recognize?timestamps=true" > [outputFile].json
	
f) curl will save [outputFile].json in the current working folder

# Step 3
a) Grab json_to_srt.py any way you like. Github can be a bit confusing for beginners (I include myself here); the easiest thing to do would be to copy-paste the code into a text editor like notepad++ and save it with extension .py

b) Execute json_to_srt.py. If you have python installed correctly you can double click the file from explorer and it will pop up a terminal window with the next step. Alternatively from a terminal you can enter 

	python.exe .\json_to_srt.py
or simply 

	json_to_srt.py
	
c) The first output line of the .py file shows the current directory it's working in. This directory must be the same place your .json is located

d) The .py file will prompt you to enter the filename. Note that input is cAse sEnsiTIve

e) Shortly thereafter, a .srt file with the same name as your .json will appear in the directory

f) Watch subtitled video and enjoy Watson's (mostly) accurate interpretations
