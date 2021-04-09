# antidote-2 notes
This is the place to dump all notes, ideas, feature requirements, potential blockers and tech challenges for a new version of the learning tool based on NRE Labs.
 ## Goal
 Create a STEM learning platform for k-12 students that is internet dependent to the school, but able to generate take home lessons for students that require no internet at home.
 
 ## Top Level Requirements
 - support modern browsers - Firefox, Chrome, Safari, Edge
 - probably also need to arrive at a min spec requirement for a student machine, and possible work-arounds for that limitation?
 - need to develop a lesson specification that supports live and offline lessons and can handle various topics and disciplines. Also likely different lesson styles - aka video centric vs text centric vs terminals/remote desktops on a VM/image.
     - As we get further down the road and explore 'offline' support more thoroughly, there may be a need to identify two (or more) different types of curriculum.  One that is 'standard' and one that is 'lite', aka certified for use in the offline mode because it has been designed to be as minimalist as possible.
 - need a scoring/grading capability to integrate with lessons, also supporting offline recording that 'syncs up' with any CTF server (see below) when back online
 - user testing can be handled by the schools themselves
    - as we get to MVP I will reach out to GA DoE and ask for some testers
 - The Project will handle all the tech up to the school level, teachers will handle assisting students to select and save their lesson for take home work.
 - Antidote-core is poised to switch to a plug-in style model, allowing it support things like OpenStack WMs as well as the current Kubernetes based images on the backend. Other back end sources and their plug-ins possible.
    - I see AWS/Azure/GCP/etc. being the next logical supported backend(s)

## Add Video Lessons
Expand video support in lessons
- currently 1 youtube video per lesson, and 1 youtube video per stage within a lesson
- support other url based sources and file types. 
-  HTML 5 player supports .webm, .mp4, and .ogg.
-  YouTube player supports 3GPP, AVI, FLV, MOV, MPEG4 (mp4), MPEGPS, WebM and WMV.
- should we allow for live stream video urls, or do we need to assume that all video assets will be stored in VM, docker container, or local filesystem to allow take home lessons to access them? why not both?
    - We can allow for both, as long as we figure out a way to ensure that live stream URLs can be 'downloaded' via a CI for the offline version, and that stuff stored in a object store is similarly downloaded
- should be able to parse the url/extension to select a correct player widget (assuming we need more than one)
  
## Remote Desktop viewing via SpiceHTML5 or NoVNC
-  there are intern devs working on a Spice repo here https://gitlab.com/gacybercenter/spice-html5 that will likely be merged upstream to the main [Spice-HTML repo](https://github.com/freedesktop/spice-html5). These updates would integrate with OpenStack providing the remote desktop solution to the front end.
-  we could implement a performant remote desktop using JS/Canvas/WebSocket based NoVNC if needed outside of OpenStack if needed as well.
-  How would a student take home an OpenStack VM for a lesson that utilizes the remote desktop feature? 
    - [DevStack](https://docs.openstack.org/devstack/latest/) is the upstream AIO strategy for this, but there are others from Red Hat and Canonical (I believe they will be heavier, but worth a look anyway)
-  Would they instead just run lesson required services on their machine directly, or in a lighter container of some kind?
    - I think the take-home setup would need to include a virtualbox VM that's pre-configured with antidote, antidote-core, the curriculum, and the necessary backends.
    - Dovetailing off of the comment about a 'lite' curriculum above, maybe we can have a few different release trains that are managed automatically based on how many resources it will need to run correctly, so very basic lessons with a container or two are packaged with a 'lite' standalone release, and stuff that uses devstack, etc. are distributed separately. 

## Add a CTF server
- something like this? https://ctfd.io/
- current 'grading' style in NRE Labs is more of a sandbox environment to follow along on a lesson and try things out, but no grading or scoring. 
- competition style scoring, aka CTF, might not make sense in this context. possible better to have the lesson platform allow for Author scripts that test the state of a container/vm to check if 'complete'. so maybe initially pass/fail?
    - So I use the term gamification very loosely - really just a way for the system to validate that the learner did/did not do something correctly.  I've used ctfd in the past as a simple gating mechanism to validate whether or not a learner did the task correctly by having them paste their answer(s) into a box.  This meant that we didn't have to deal with integrating autograding in the lesson itself, which lowered the burder on content creators significantly and gave them more flexibility.  If we decide that we want antidote to be able to reach into environments and check student work, that sort of necessitates that there must be a TCP/IP connection from antidote itself into the environment, which may not always be desirable or possible, especially if the lesson is centered around ICS/SCADA and TCP/IP isn't a thing.
- more nuanced grading and scoring or at least better feedback at each part or step of the lesson, rather than just a final pass/fail added as time goes on

## Allow all lessons to be offline homework
- likely the most difficult feature
- need to fit containers, software, and lesson assets on a take home thumb-drive, which could be very large depending on the lesson and the tech used for it
- home computer hardware requirements might be higher than some or most folks have currently, requiring upgrades or homework loaners perhaps?
- VMs would be evn more difficult on home machine requirements
- software/techinical requirements on end users would also be higher depending on pre-req tooling to run lessons
- if home users need to have docker locally, that setup might be involved - changing bios settings, setting up hyper-v, installing new windows components
- docker may not run at all on older machines without the virtualization instruction set
    - For all the hardware needs, as long as we are clear on what is required we will be OK.  It's a tradeoff - no internet means you need beefier computers.  The districts we are targeting generally are very well funded IT-wise because of the grants they receive.  I'm not super concerned about this piece, but we can revisit when we get closer and start figuring out what it takes to run this stuff locally.
- could have some licensing requirements depending on required support software, for example lesson VMs that run Windows 10 desktop.
    - I think we can separate this out for the time being by falling under the state blanket agreements with Microsoft (for example), will double check to make sure we don't run afoul of anything.  I would say in general though that content writers should try and avoid making content that requires licenses, and if they do, they need to state so upfront so those running antidote w/ their content can do what they need to do.
- lesson authors could theoretically 'break' the ability to do the lesson at home (for example using only youtube urls for video assets), and controlling for that could be expensive on the code side, or be a likely impact on lesson/authoring flexibility.
- maybe an offline ready! validation check for lessons?
    - This is a great idea, we can try and build a lesson and do some basic functions testing to ensure that it will work properly.
