# antidote-2 notes
This is the place to dump all notes, ideas, feature requirements, potential blockers and tech challenges for a new version of the learning tool based on NRE Labs.
 ## Goal
 Create a STEM learning platform for k-12 students that is internet dependent to the school, but able to generate take home lessons for students that require no internet at home.
 
 ## Top Level Requirements
 - support modern browsers - Firefox, Chrome, Safari, Edge
 - probably also need to arrive at a min spec requirement for a student machine, and possible work-arounds for that limitation?
 - need to develop a lesson specification that supports live and offline lessons and can handle various topics and disciplines. Also likely different lesson styles - aka video centric vs text centric vs terminals/remote desktops on a VM/image.
 - need a scoring/grading capability to integrate with lessons, also supporting offline recording that 'syncs up' with any CTF server (see below) when back online
 - user testing can be handled by the schools themselves
 - The Project will handle all the tech up to the school level, teachers will handle assisting students to select and save their lesson for take home work.
 - Antidote-core is poised to switch to a plug-in style model, allowing it support things like OpenStack WMs as well as the current Kubernetes based images on the backend. Other back end sources and their plug-ins possible.

## Add Video Lessons
Expand video support in lessons
- currently 1 youtube video per lesson, and 1 youtube video per stage within a lesson
- support other url based sources and file types. 
-  HTML 5 player supports .webm, .mp4, and .ogg.
-  YouTube player supports 3GPP, AVI, FLV, MOV, MPEG4 (mp4), MPEGPS, WebM and WMV.
- should we allow for live stream video urls, or do we need to assume that all video assets will be stored in VM, docker container, or local filesystem to allow take home lessons to access them? why not both?
- should be able to parse the url/extension to select a correct player widget (assuming we need more than one)
  
## Remote Desktop viewing via SpiceHTML5 or NoVNC
-  there are intern devs working on a Spice repo here https://gitlab.com/gacybercenter/spice-html5 that will likely be merged upstream to the main [Spice-HTML repo](https://github.com/freedesktop/spice-html5). These updates would integrate with OpenStack providing the remote desktop solution to the front end.
-  we could implement a performant remote desktop using JS/Canvas/WebSocket based NoVNC if needed outside of OpenStack if needed as well.
-  How would a student take home an OpenStack VM for a lesson that utilizes the remote desktop feature? 
-  Would they instead just run lesson required services on their machine directly, or in a lighter container of some kind?

## Add a CTF server
- something like this? https://ctfd.io/
- current 'grading' style in NRE Labs is more of a sandbox environment to follow along on a lesson and try things out, but no grading or scoring. 
- competition style scoring, aka CTF, might not make sense in this context. possible better to have the lesson platform allow for Author scripts that test the state of a container/vm to check if 'complete'. so maybe initially pass/fail?
- more nuanced grading and scoring or at least better feedback at each part or step of the lesson, rather than just a final pass/fail added as time goes on

## Allow all lessons to be offline homework
- likely the most difficult feature
- need to fit containers, software, and lesson assets on a take home thumb-drive, which could be very large depending on the lesson and the tech used for it
- home computer hardware requirements might be higher than some or most folks have currently, requiring upgrades or homework loaners perhaps?
- VMs would be evn more difficult on home machine requirements
- software/techinical requirements on end users would also be higher depending on pre-req tooling to run lessons
- if home users need to have docker locally, that setup might be involved - changing bios settings, setting up hyper-v, installing new windows components
- docker may not run at all on older machines without the virtualization instruction set
- could have some licensing requirements depending on required support software, for example lesson VMs that run Windows 10 desktop.
- lesson authors could theoretically 'break' the ability to do the lesson at home (for example using only youtube urls for video assets), and controlling for that could be expensive on the code side, or be a likely impact on lesson/authoring flexibility.
- maybe an offline ready! validation check for lessons?
