---
title: "CMS Data Analysis School Pre-Exercises - Third Set"
teaching: 0
exercises: 240
questions:
- "How do I do an analysis with so much data that I cannot run it
interactively on my computer?"
- "What is CRAB? How do I use it to run an analysis
on the grid?"
- "How do configuration files look like?"
- "How do I extract the luminosity of the dataset I analyzed?"
objectives:
- "Become familiar with the basic Grid tools used in CMS for user
analysis"
- "Learn about grid certificate usage"
- "Know what CRAB is and how to use it for your analysis"
- "Know how to use BRILcalc to extract luminosities"
keypoints:
- "Use and validate your grid certificate."
- "Setting up your CRAB configuration and run jobs over the CMS grid."
- "Publish your CRAB datasets."
- "Calculate the luminosities of the datasets processed via CRAB."
---

# Introduction
 This is the third set of CMSDAS exercises. The purpose of these exercises are for the workshop attendees to become familiar with the basic Grid tools used in CMS for user analysis. Please run and complete each of these exercises. However, unlike the previous sets of exercises, **this set will take considerably longer**. Having your storage space set up may take several days, Grid jobs run with some latency, and there can be problems. You should **set aside about a week** to complete these five exercises. The actual effort required is not the whole week but a few hours (more than the previous two sets). If, at any time problems are encountered with the exercise please e-mail [cmsdas-cern-organizers@cern.ch](mailto:cmsdas-cern-organizers@cern.ch) with a detailed description of your problem. For CRAB questions unrelated to passing these exercises, to send feedback and ask for support in case of CRAB related problems, please consult the [CRAB troubleshooting twiki](https://twiki.cern.ch/twiki/bin/view/CMSPublic/CRAB3Troubleshoot). All CRAB users should subscribe to the [Announcement tag of O&C CMS-Talk](https://cms-talk.web.cern.ch/tag/announcements), it is a low traffic forum which contains other useful announcements besides CRAB ones.

> ## Note
> **This section assumes that you have access to lxplus at CERN.** Learn more about lxplus
> [here](https://information-technology.web.cern.ch/services/lxplus-service)
> and the [lxplus knowledge guide](https://lxplusdoc.web.cern.ch/).
>
> Later on, you can check with your university contact for Tier 2 or Tier 3 storage area. Once you are granted the write permission to the specified site, for later analysis you can use CRAB as the below exercise but store the output to your Tier 2 or Tier 3 storage area.
>
> AGAIN: To perform this set of exercises, lxplus access, Grid Certificate, and CMS VO membership are required. You should already have these things, but if not, follow these instructions from the [first set of exercises]({{ page.root }}{% link _episodes/01-CMSDataAnalysisSchoolPreExerciseFirstSet.md %}).
>
{: .callout}

> ## Question
> Questions for each exercise are in boxes such as this. <br>
For CMSDAS@CERN {{ site.year }} please submit your answers for the [CMSDAS@CERN Google Form third set][Set3_form].
{: .challenge}


> ## Support
> There is a dedicated Mattermost team, called CMSDAS@CERN {{ site.year }},
> setup to facilitate communication and discussions via live chat
> (which is also archived). You will need your CERN login credentials
> (SSO) and you will need to join the private CMSDAS@CERN {{ site.year }}
> team in order to be able to see (or find using the search
> channels functionality) the channels setup for communications
> related to the school. The sign-up link is
> [here](https://mattermost.web.cern.ch/signup_user_complete/?id=4bfb4547abnwjfq3kn6uk4ms5h&md=link&sbr=su)
> and the Pre-exercises channel can be found [here](https://mattermost.web.cern.ch/cmsdascern2023/channels/pre-exercises).
>
{: .testimonial}
<!-- {: .support} -->

# Exercise 10 - Verify your grid certificate is OK

This exercise depends on obtaining a grid certificate and VOMS membership, but does not depend on any previous exercises.
After you've installed your grid certificate, you need to verify it has all the information needed.

Login to **lxplus.cern.ch** and initialize your proxy:
```shell
voms-proxy-init -voms cms
```
{: .source}

Then run the following command:
```shell
voms-proxy-info -all | grep -Ei "role|subject"
```
{: .source}

The response should look like this:
```
subject   : /DC=ch/DC=cern/OU=Organic Units/OU=Users/CN=vmilosev/CN=757854/CN=Vukasin Milosevic/CN=40175424
subject   : /DC=ch/DC=cern/OU=Organic Units/OU=Users/CN=vmilosev/CN=757854/CN=Vukasin Milosevic
attribute : /cms/Role=NULL/Capability=NULL
attribute : /cms/country/Role=NULL/Capability=NULL
attribute : /cms/country/ch/Role=NULL/Capability=NULL
```
{: .output}
If you do not have the first attribute line listed above, you have not completed the VO registration above and you must complete it before continuing.

> ## Question 10
> Copy the output corresponding to the text in the output box above. <br>
> For CMSDAS@CERN {{ site.year }} please submit your answers for the [CMSDAS@CERN {{ site.year }} Google Form third set][Set3_form].
{: .challenge}



# Exercise 11 - Obtain a /store/user area and setup CRAB

## Obtain a /store/user area

This exercise depends on **successfully completing Exercise 10**. Completion of this exercise requires a users to have `/store/user/YourCERNUserName` in Tier2 or Tier3 site. (ex, `eos` area at lxplus). and a user should get this automatically once they have a lxplus account.

## CRAB Introduction

In this exercise, you will learn an important tool [CRAB](https://twiki.cern.ch/twiki/bin/view/CMSPublic/SWGuideCrab), which is used
in all the data analysis at CMS. CRAB (CMS Remote Analysis Builder) is a utility to submit CMSSW jobs to distributed computing resources. By using CRAB you will be able to access CMS data and Monte-Carlo which are distributed to CMS aligned centres worldwide and exploit the CPU and storage resources at CMS aligned centres. You will also test your grid certificate and your cms EOS storage element which will be useful during CMSDAS@CERN{{ site.year }}.

*Help* or *questions* about CRAB: Follow the [FAQ](https://twiki.cern.ch/twiki/bin/view/CMSPublic/SWGuideCrab#Getting_support) to get help with CRAB.

The most recent CRAB3 tutorial is always in the [WorkBook](https://twiki.cern.ch/twiki/bin/view/CMSPublic/WorkBook) under [WorkBookCRABTutorial](https://twiki.cern.ch/twiki/bin/view/CMSPublic/WorkBookCRAB3Tutorial). This tutorial provides complete instructions for beginner and expert user to use CRAB in their studies. We strongly recommend you to learn the CRAB tutorial after you finish these exercises. In this exercise, you will use CRAB to generate a MC sample yourself and publish it to the DAS.

## Setup CRAB

In this exercise, we will use `CMSSW_10_6_18`.

You can follow the same instructions from [Exercise 3]({{ page.root }}{% link _episodes/01-CMSDataAnalysisSchoolPreExerciseFirstSet.md %}#exercise-3---setup-a-cmssw-release-area). The instructions are reproduced here:

```shell
cd ~/YOURWORKINGAREA

export SCRAM_ARCH=slc7_amd64_gcc700
### If you are using the default tcsh shell (or csh shell)
setenv SCRAM_ARCH slc7_amd64_gcc700
###

cmsrel CMSSW_10_6_18
cd CMSSW_10_6_18/src
cmsenv
git cms-init
```
{: .source}


After setting up the CMSSW environment via `cmsenv`, you'll have access to the latest version of CRAB. It is possible to use CRAB from any directory after setup. One can check that the crab command is indeed available and the version being used by executing:

```shell
which crab
```
{: .source}

```
/cvmfs/cms.cern.ch/common/crab
```
{: .output}

or

```shell
crab --version
```
{: .source}

```
CRAB client v3.230404
```
{: .output}

The `/store/user` area is commonly used for output storage from CRAB. When you complete *Exercise 11*, you can follow these instructions to make sure you can read from and write to your space using CRAB command.

Initialize your proxy:

```shell
voms-proxy-init -voms cms
```
{: .source}

Check if you can write to the `/store/user/` area. The crab checkwrite command can be used by a user to check if he/she has write permission in a given CERN eos directory path (by default `/store/user/<HN-username>/`) in a given site. The syntax to be used is:

```shell
crab checkwrite --site= <site-name>
```
{: .source}

For example:

```shell
crab checkwrite --site=T3_CH_CERNBOX
```
{: .source}

 The output should look like this:

> ## Show/Hide
> > ```
> > Will check write permission in the default location /store/user/<username>
> > Validating LFN /store/user/vmilosev...
> > LFN /store/user/vmilosev is valid.
> > Will use `gfal-copy`, `gfal-rm` commands for checking write permissions
> > Will check write permission in /store/user/vmilosev on site T3_CH_CERNBOX
> > Will use PFN: davs://eosuserhttp.cern.ch:443//eos/user/v/vmilosev/crab3checkwrite_20230421_105013/crab3checkwrite_20230421_105013.tmp
> > 
> > Attempting to create (dummy) directory crab3checkwrite_20230421_105013 and copy (dummy) file crab3checkwrite_20230421_105013.tmp to /store/user/vmilosev
> > 
> > Executing command: which scram >/dev/null 2>&1 && eval `scram unsetenv -sh`; gfal-copy -p -v -t 180 file:///afs/cern.ch/user/v/vmilosev/Test_CMSDAS_Crab/CMSSW_10_6_18/src/crab3checkwrite_20230421_105013.tmp 'davs://eosuserhttp.cern.ch:443//eos/user/v/vmilosev/crab3checkwrite_20230421_105013/crab3checkwrite_20230421_105013.tmp'
> > Please wait...
> > 
> > Successfully created directory crab3checkwrite_20230421_105013 and copied file crab3checkwrite_20230421_105013.tmp to /store/user/vmilosev
> > 
> > Attempting to delete file davs://eosuserhttp.cern.ch:443//eos/user/v/vmilosev/crab3checkwrite_20230421_105013/crab3checkwrite_20230421_105013.tmp
> > 
> > Executing command: which scram >/dev/null 2>&1 && eval `scram unsetenv -sh`; gfal-rm -v -t 180 'davs://eosuserhttp.cern.ch:443//eos/user/v/vmilosev/crab3checkwrite_20230421_105013/crab3checkwrite_20230421_105013.tmp'
> > Please wait...
> > 
> > Successfully deleted file davs://eosuserhttp.cern.ch:443//eos/user/v/vmilosev/crab3checkwrite_20230421_105013/crab3checkwrite_20230421_105013.tmp
> > 
> > Attempting to delete directory davs://eosuserhttp.cern.ch:443//eos/user/v/vmilosev/crab3checkwrite_20230421_105013/
> > 
> > Executing command: which scram >/dev/null 2>&1 && eval `scram unsetenv -sh`; gfal-rm -r -v -t 180 'davs://eosuserhttp.cern.ch:443//eos/user/v/vmilosev/crab3checkwrite_20230421_105013/'
> > Please wait...
> > 
> > Successfully deleted directory davs://eosuserhttp.cern.ch:443//eos/user/v/vmilosev/crab3checkwrite_20230421_105013/
> > 
> > Checkwrite Result:
> > Success: Able to write in /store/user/vmilosev on site T3_CH_CERNBOX
> > ```
> {: .output}
{: .solution}

 Choosing the `T3_CH_CERNBOX` "site" allows you to have the option of outputing crab jobs to your *EOS area*, providing you with an easy way to access produced files. However this does not allow for publishing of produced samples as CERNBOX is NOT a CMS storage, and files in there can not be listed in DBS. For more details about crab output options, visit the following [link](https://twiki.cern.ch/twiki/bin/view/CMSPublic/CRAB3FAQ).

> ## Question 11
> What is the name of your directory name in eos? <br>
> For CMSDAS@CERN{{ site.year }} please submit your answers for the [CMSDAS@CERN{{ site.year }} Google Form third set][Set3_form].
{: .challenge}


# Exercise 12 - Generate (and publish) a minimum bias dataset with CRAB

## CMSSW configuration file to generate MC events

 In this section we provide an example of a CMSSW parameter-set configuration file to generate minimum bias events with the Pythia MC generator. We call it `CMSDAS_MC_generation.py`. Using CRAB to generate MC events requires some special settings in the CRAB configuration file, as we will show later.

We use the cmsDriver tool to generate our configuration file:

```shell
cmsDriver.py MinBias_13TeV_pythia8_TuneCUETP8M1_cfi  --conditions auto:run2_mc -n 10 --era Run2_2018 --eventcontent FEVTDEBUG --relval 100000,300 -s GEN,SIM --datatier GEN-SIM --beamspot Realistic25ns13TeVEarly2018Collision --fileout file:step1.root --no_exec --python_filename CMSDAS_MC_generation.py
```
{: .source}

If successful, `cmsDriver` will return the following
```
We have determined that this is simulation (if not, rerun cmsDriver.py with --data)
Step: GEN Spec:
Loading generator fragment from Configuration.Generator.MinBias_13TeV_pythia8_TuneCUETP8M1_cfi
Step: SIM Spec:
Step: ENDJOB Spec:
Config file CMSDAS_MC_generation.py created
```
{: .output}

 Feel free to investigate (look at) the newly outputted
 `CMSDAS_MC_generation.py`.

## Generating MC events locally

We want to test this Configuration file locally for a small number of events before we submit to CRAB for massive generation. To test this file, we can run

```shell
cmsRun CMSDAS_MC_generation.py
```
{: .source}

This MC generation code will then produce an EDM output file called `step1.root` with the content of a GEN-SIM data tier for 10 generated events.


> ## Show/Hide
>
> ```
> *------------------------------------------------------------------------------------* 
> |                                                                                    | 
> |  *------------------------------------------------------------------------------*  | 
> |  |                                                                              |  | 
> |  |                                                                              |  | 
> |  |   PPP   Y   Y  TTTTT  H   H  III    A      Welcome to the Lund Monte Carlo!  |  | 
> |  |   P  P   Y Y     T    H   H   I    A A     This is PYTHIA version 8.240      |  | 
> |  |   PPP     Y      T    HHHHH   I   AAAAA    Last date of change: 20 Dec 2018  |  | 
> |  |   P       Y      T    H   H   I   A   A                                      |  | 
> |  |   P       Y      T    H   H  III  A   A    Now is 21 Apr 2023 at 11:32:03    |  | 
> |  |                                                                              |  | 
> |  |   Christian Bierlich;  Department of Astronomy and Theoretical Physics,      |  | 
> |  |      Lund University, Solvegatan 14A, SE-223 62 Lund, Sweden;                |  | 
> |  |      e-mail: christian.bierlich@thep.lu.se                                   |  | 
> |  |   Nishita Desai;  Department of Theoretical Physics, Tata Institute,         |  | 
> |  |      Homi Bhabha Road, Mumbai 400005, India;                                 |  | 
> |  |      e-mail: desai@theory.tifr.res.in                                        |  | 
> |  |   Ilkka Helenius;  Department of Physics, University of Jyvaskyla,           |  | 
> |  |      P.O. Box 35, FI-40014 University of Jyvaskyla, Finland;                 |  | 
> |  |      e-mail: ilkka.m.helenius@jyu.fi                                         |  | 
> |  |   Philip Ilten;  School of Physics and Astronomy,                            |  | 
> |  |      University of Birmingham, Birmingham, B152 2TT, UK;                     |  | 
> |  |      e-mail: philten@cern.ch                                                 |  | 
> |  |   Leif Lonnblad;  Department of Astronomy and Theoretical Physics,           |  | 
> |  |      Lund University, Solvegatan 14A, SE-223 62 Lund, Sweden;                |  | 
> |  |      e-mail: leif.lonnblad@thep.lu.se                                        |  | 
> |  |   Stephen Mrenna;  Computing Division, Simulations Group,                    |  | 
> |  |      Fermi National Accelerator Laboratory, MS 234, Batavia, IL 60510, USA;  |  | 
> |  |      e-mail: mrenna@fnal.gov                                                 |  | 
> |  |   Stefan Prestel;  Department of Astronomy and Theoretical Physics,          |  | 
> |  |      Lund University, Solvegatan 14A, SE-223 62 Lund, Sweden;                |  | 
> |  |      e-mail: stefan.prestel@thep.lu.se                                       |  | 
> |  |   Christine O. Rasmussen;  Department of Astronomy and Theoretical Physics,  |  | 
> |  |      Lund University, Solvegatan 14A, SE-223 62 Lund, Sweden;                |  | 
> |  |      e-mail: christine.rasmussen@thep.lu.se                                  |  | 
> |  |   Torbjorn Sjostrand;  Department of Astronomy and Theoretical Physics,      |  | 
> |  |      Lund University, Solvegatan 14A, SE-223 62 Lund, Sweden;                |  | 
> |  |      e-mail: torbjorn@thep.lu.se                                             |  | 
> |  |   Peter Skands;  School of Physics,                                          |  | 
> |  |      Monash University, PO Box 27, 3800 Melbourne, Australia;                |  | 
> |  |      e-mail: peter.skands@monash.edu                                         |  | 
> |  |                                                                              |  | 
> |  |   The main program reference is 'An Introduction to PYTHIA 8.2',             |  | 
> |  |   T. Sjostrand et al, Comput. Phys. Commun. 191 (2015) 159                   |  | 
> |  |   [arXiv:1410.3012 [hep-ph]]                                                 |  | 
> |  |                                                                              |  | 
> |  |   The main physics reference is the 'PYTHIA 6.4 Physics and Manual',         |  | 
> |  |   T. Sjostrand, S. Mrenna and P. Skands, JHEP05 (2006) 026 [hep-ph/0603175]  |  | 
> |  |                                                                              |  | 
> |  |   An archive of program versions and documentation is found on the web:      |  | 
> |  |   http://www.thep.lu.se/Pythia                                               |  | 
> |  |                                                                              |  | 
> |  |   This program is released under the GNU General Public Licence version 2.   |  | 
> |  |   Please respect the MCnet Guidelines for Event Generator Authors and Users. |  | 
> |  |                                                                              |  | 
> |  |   Disclaimer: this program comes without any guarantees.                     |  | 
> |  |   Beware of errors and use common sense when interpreting results.           |  | 
> |  |                                                                              |  | 
> |  |   Copyright (C) 2018 Torbjorn Sjostrand                                      |  | 
> |  |                                                                              |  | 
> |  |                                                                              |  | 
> |  *------------------------------------------------------------------------------*  | 
> |                                                                                    | 
> *------------------------------------------------------------------------------------* 
>
>
> *------------------------------------------------------------------------------------* 
> |                                                                                    | 
> |  *------------------------------------------------------------------------------*  | 
> |  |                                                                              |  | 
> |  |                                                                              |  | 
> |  |   PPP   Y   Y  TTTTT  H   H  III    A      Welcome to the Lund Monte Carlo!  |  | 
> |  |   P  P   Y Y     T    H   H   I    A A     This is PYTHIA version 8.240      |  | 
> |  |   PPP     Y      T    HHHHH   I   AAAAA    Last date of change: 20 Dec 2018  |  | 
> |  |   P       Y      T    H   H   I   A   A                                      |  | 
> |  |   P       Y      T    H   H  III  A   A    Now is 21 Apr 2023 at 11:32:03    |  | 
> |  |                                                                              |  | 
> |  |   Christian Bierlich;  Department of Astronomy and Theoretical Physics,      |  | 
> |  |      Lund University, Solvegatan 14A, SE-223 62 Lund, Sweden;                |  | 
> |  |      e-mail: christian.bierlich@thep.lu.se                                   |  | 
> |  |   Nishita Desai;  Department of Theoretical Physics, Tata Institute,         |  | 
> |  |      Homi Bhabha Road, Mumbai 400005, India;                                 |  | 
> |  |      e-mail: desai@theory.tifr.res.in                                        |  | 
> |  |   Ilkka Helenius;  Department of Physics, University of Jyvaskyla,           |  | 
> |  |      P.O. Box 35, FI-40014 University of Jyvaskyla, Finland;                 |  | 
> |  |      e-mail: ilkka.m.helenius@jyu.fi                                         |  | 
> |  |   Philip Ilten;  School of Physics and Astronomy,                            |  | 
> |  |      University of Birmingham, Birmingham, B152 2TT, UK;                     |  | 
> |  |      e-mail: philten@cern.ch                                                 |  | 
> |  |   Leif Lonnblad;  Department of Astronomy and Theoretical Physics,           |  | 
> |  |      Lund University, Solvegatan 14A, SE-223 62 Lund, Sweden;                |  | 
> |  |      e-mail: leif.lonnblad@thep.lu.se                                        |  | 
> |  |   Stephen Mrenna;  Computing Division, Simulations Group,                    |  | 
> |  |      Fermi National Accelerator Laboratory, MS 234, Batavia, IL 60510, USA;  |  | 
> |  |      e-mail: mrenna@fnal.gov                                                 |  | 
> |  |   Stefan Prestel;  Department of Astronomy and Theoretical Physics,          |  | 
> |  |      Lund University, Solvegatan 14A, SE-223 62 Lund, Sweden;                |  | 
> |  |      e-mail: stefan.prestel@thep.lu.se                                       |  | 
> |  |   Christine O. Rasmussen;  Department of Astronomy and Theoretical Physics,  |  | 
> |  |      Lund University, Solvegatan 14A, SE-223 62 Lund, Sweden;                |  | 
> |  |      e-mail: christine.rasmussen@thep.lu.se                                  |  | 
> |  |   Torbjorn Sjostrand;  Department of Astronomy and Theoretical Physics,      |  | 
> |  |      Lund University, Solvegatan 14A, SE-223 62 Lund, Sweden;                |  | 
> |  |      e-mail: torbjorn@thep.lu.se                                             |  | 
> |  |   Peter Skands;  School of Physics,                                          |  | 
> |  |      Monash University, PO Box 27, 3800 Melbourne, Australia;                |  | 
> |  |      e-mail: peter.skands@monash.edu                                         |  | 
> |  |                                                                              |  | 
> |  |   The main program reference is 'An Introduction to PYTHIA 8.2',             |  | 
> |  |   T. Sjostrand et al, Comput. Phys. Commun. 191 (2015) 159                   |  | 
> |  |   [arXiv:1410.3012 [hep-ph]]                                                 |  | 
> |  |                                                                              |  | 
> |  |   The main physics reference is the 'PYTHIA 6.4 Physics and Manual',         |  | 
> |  |   T. Sjostrand, S. Mrenna and P. Skands, JHEP05 (2006) 026 [hep-ph/0603175]  |  | 
> |  |                                                                              |  | 
> |  |   An archive of program versions and documentation is found on the web:      |  | 
> |  |   http://www.thep.lu.se/Pythia                                               |  | 
> |  |                                                                              |  | 
> |  |   This program is released under the GNU General Public Licence version 2.   |  | 
> |  |   Please respect the MCnet Guidelines for Event Generator Authors and Users. |  | 
> |  |                                                                              |  | 
> |  |   Disclaimer: this program comes without any guarantees.                     |  | 
> |  |   Beware of errors and use common sense when interpreting results.           |  | 
> |  |                                                                              |  | 
> |  |   Copyright (C) 2018 Torbjorn Sjostrand                                      |  | 
> |  |                                                                              |  | 
> |  |                                                                              |  | 
> |  *------------------------------------------------------------------------------*  | 
> |                                                                                    | 
> *------------------------------------------------------------------------------------* 
>
>
> *-------  PYTHIA Process Initialization  --------------------------*
> |                                                                  |
> | We collide p+ with p+ at a CM energy of 1.300e+04 GeV            |
> |                                                                  |
> |------------------------------------------------------------------|
> |                                                    |             |
> | Subprocess                                    Code |   Estimated |
> |                                                    |    max (mb) |
> |                                                    |             |
> |------------------------------------------------------------------|
> |                                                    |             |
> | non-diffractive                                101 |   5.642e+01 |
> | A B -> X B single diffractive                  103 |   6.416e+00 |
> | A B -> A X single diffractive                  104 |   6.416e+00 |
> | A B -> X X double diffractive                  105 |   8.798e+00 |
> |                                                                  |
> *-------  End PYTHIA Process Initialization -----------------------*
>
> *-------  PYTHIA Multiparton Interactions Initialization  ---------* 
> |                                                                  | 
> |                   sigmaNonDiffractive =    56.42 mb              | 
> |                                                                  | 
> |    pT0 =  2.81 gives sigmaInteraction =   267.96 mb: accepted    | 
> |                                                                  | 
> *-------  End PYTHIA Multiparton Interactions Initialization  -----* 
> PYTHIA Warning in MultipartonInteractions::init: maximum increased by factor 1.055
> 
> *-------  PYTHIA Multiparton Interactions Initialization  ---------* 
> |                                                                  | 
> |                          diffraction XB                          | 
> |                                                                  | 
> |   diffractive mass = 1.00e+01 GeV and sigmaNorm =    10.00 mb    | 
> |    pT0 =  0.46 gives sigmaInteraction =    54.25 mb: accepted    | 
> |   diffractive mass = 6.00e+01 GeV and sigmaNorm =    10.00 mb    | 
> |    pT0 =  0.72 gives sigmaInteraction =    28.53 mb: accepted    | 
> |   diffractive mass = 3.61e+02 GeV and sigmaNorm =    10.00 mb    | 
> |    pT0 =  1.14 gives sigmaInteraction =    20.25 mb: accepted    | 
> |   diffractive mass = 2.16e+03 GeV and sigmaNorm =    10.00 mb    | 
> |    pT0 =  1.79 gives sigmaInteraction =    30.44 mb: accepted    | 
> |   diffractive mass = 1.30e+04 GeV and sigmaNorm =    10.00 mb    | 
> |    pT0 =  2.81 gives sigmaInteraction =    52.87 mb: accepted    | 
> |                                                                  | 
> *-------  End PYTHIA Multiparton Interactions Initialization  -----* 
>
> *-------  PYTHIA Multiparton Interactions Initialization  ---------* 
> |                                                                  | 
> |                          diffraction AX                          | 
> |                                                                  | 
> |   diffractive mass = 1.00e+01 GeV and sigmaNorm =    10.00 mb    | 
> |    pT0 =  0.46 gives sigmaInteraction =    54.35 mb: accepted    | 
> |   diffractive mass = 6.00e+01 GeV and sigmaNorm =    10.00 mb    | 
> |    pT0 =  0.72 gives sigmaInteraction =    28.27 mb: accepted    | 
> |   diffractive mass = 3.61e+02 GeV and sigmaNorm =    10.00 mb    | 
> |    pT0 =  1.14 gives sigmaInteraction =    20.31 mb: accepted    | 
> |   diffractive mass = 2.16e+03 GeV and sigmaNorm =    10.00 mb    | 
> |    pT0 =  1.79 gives sigmaInteraction =    30.66 mb: accepted    | 
> |   diffractive mass = 1.30e+04 GeV and sigmaNorm =    10.00 mb    | 
> |    pT0 =  2.81 gives sigmaInteraction =    52.96 mb: accepted    | 
> |                                                                  | 
> *-------  End PYTHIA Multiparton Interactions Initialization  -----* 
>
> *-------  PYTHIA Flag + Mode + Parm + Word + FVec + MVec + PVec + WVec Settings (changes only)  ------------------* 
> |                                                                                                                 | 
> | Name                                          |                      Now |      Default         Min         Max | 
> |                                               |                          |                                      | 
> | Beams:eCM                                     |                13000.000 |    14000.000    10.00000             | 
> | Check:epTolErr                                |                0.0100000 |   1.0000e-04                         | 
> | Main:timesAllowErrors                         |                    10000 |           10           0             | 
> | MultipartonInteractions:ecmPow                |                  0.25208 |      0.21500         0.0     0.50000 | 
> | MultipartonInteractions:expPow                |                  1.60000 |      1.85000     0.40000    10.00000 | 
> | MultipartonInteractions:pT0Ref                |                  2.40240 |      2.28000     0.50000    10.00000 | 
> | Next:numberShowEvent                          |                        0 |            1           0             | 
> | ParticleDecays:allowPhotonRadiation           |                       on |          off                         | 
> | ParticleDecays:limitTau0                      |                       on |          off                         | 
> | SLHA:minMassSM                                |                 1000.000 |    100.00000                         | 
> | SoftQCD:doubleDiffractive                     |                       on |          off                         | 
> | SoftQCD:nonDiffractive                        |                       on |          off                         | 
> | SoftQCD:singleDiffractive                     |                       on |          off                         | 
> | Tune:preferLHAPDF                             |                        2 |            1           0           2 | 
> |                                                                                                                 | 
> *-------  End PYTHIA Flag + Mode + Parm + Word + FVec + MVec + PVec + WVec Settings  -----------------------------* 
>
> --------  PYTHIA Particle Data Table (changed only)  ------------------------------------------------------------------------------
> 
>      id   name            antiName         spn chg col      m0        mWidth      mMin       mMax       tau0    res dec ext vis wid
>             no onMode   bRatio   meMode     products 
>
>  no particle data has been changed from its default value 
>
> --------  End PYTHIA Particle Data Table  -----------------------------------------------------------------------------------------
>
>
> *-------  PYTHIA Flag + Mode + Parm + Word + FVec + MVec + PVec + WVec Settings (changes only)  ------------------* 
> |                                                                                                                 | 
> | Name                                          |                      Now |      Default         Min         Max | 
> |                                               |                          |                                      | 
> | Next:numberShowEvent                          |                        0 |            1           0             | 
> | ParticleDecays:allowPhotonRadiation           |                       on |          off                         | 
> | ParticleDecays:limitTau0                      |                       on |          off                         | 
> | ProcessLevel:all                              |                      off |           on                         | 
> |                                                                                                                 | 
> *-------  End PYTHIA Flag + Mode + Parm + Word + FVec + MVec + PVec + WVec Settings  -----------------------------* 
>
> --------  PYTHIA Particle Data Table (changed only)  ------------------------------------------------------------------------------
> 
>      id   name            antiName         spn chg col      m0        mWidth      mMin       mMax       tau0    res dec ext vis wid
>             no onMode   bRatio   meMode     products 
>
> no particle data has been changed from its default value 
>
> --------  End PYTHIA Particle Data Table  -----------------------------------------------------------------------------------------
>
> Begin processing the 1st record. Run 1, Event 1, LumiSection 1 on stream 0 at 21-Apr-2023 11:32:06.079 CEST
>
> --------  PYTHIA Info Listing  ---------------------------------------- 
> 
> Beam A: id =   2212, pz =  6.500e+03, e =  6.500e+03, m =  9.383e-01.
> Beam B: id =   2212, pz = -6.500e+03, e =  6.500e+03, m =  9.383e-01.
>
> In 1: id =    3, x =  5.935e-05, pdf =  4.937e-01 at Q2 =  3.474e+00.
> In 2: id =    1, x =  1.439e-03, pdf =  4.936e-01 at same Q2.
>
> Process non-diffractive with code 101 is 2 -> 2.
> Subprocess q q(bar)' -> q q(bar)' with code 114 is 2 -> 2.
> It has sHat =  1.443e+01,    tHat = -5.823e+00,    uHat = -8.610e+00,
>       pTHat =  1.864e+00,   m3Hat =  0.000e+00,   m4Hat =  0.000e+00,
>    thetaHat =  1.376e+00,  phiHat =  2.086e+00.
>     alphaEM =  7.539e-03,  alphaS =  2.754e-01    at Q2 =  1.136e+01.
>
> Impact parameter b =  1.874e+00 gives enhancement factor =  1.343e-02.
> Max pT scale for MPI =  1.864e+00, ISR =  1.864e+00, FSR =  1.864e+00.
> Number of MPI =     1, ISR =     2, FSRproc =     0, FSRreson =     0.
>
> --------  End PYTHIA Info Listing  ------------------------------------
>
> --------  PYTHIA Event Listing  (hard process)  -----------------------------------------------------------------------------------
>  
>     no         id  name            status     mothers   daughters     colours      p_x        p_y        p_z         e          m 
>      0         90  (system)           -11     0     0     0     0     0     0      0.000      0.000      0.000  13000.000  13000.000
>      1       2212  (p+)               -12     0     0     3     0     0     0      0.000      0.000   6500.000   6500.000      0.938
>      2       2212  (p+)               -12     0     0     4     0     0     0      0.000      0.000  -6500.000   6500.000      0.938
>      3          3  (s)                -21     1     0     5     6   101     0      0.000      0.000      0.386      0.386      0.000
>      4          1  (d)                -21     2     0     5     6   102     0      0.000      0.000     -9.353      9.353      0.000
>      5          3  s                   23     3     4     0     0   102     0      1.581     -0.895     -3.611      4.073      0.500
>      6          1  d                   23     3     4     0     0   101     0     -1.581      0.895     -5.356      5.666      0.330
>                                    Charge sum: -0.667           Momentum sum:      0.000      0.000     -8.967      9.739      3.799
> 
>  --------  End PYTHIA Event Listing  -----------------------------------------------------------------------------------------------
> Begin processing the 2nd record. Run 1, Event 2, LumiSection 1 on stream 0 at 21-Apr-2023 11:32:09.990 CEST
> Begin processing the 3rd record. Run 1, Event 3, LumiSection 1 on stream 0 at 21-Apr-2023 11:32:11.147 CEST
> Begin processing the 4th record. Run 1, Event 4, LumiSection 1 on stream 0 at 21-Apr-2023 11:32:15.916 CEST
> Begin processing the 5th record. Run 1, Event 5, LumiSection 1 on stream 0 at 21-Apr-2023 11:32:15.918 CEST
> Begin processing the 6th record. Run 1, Event 6, LumiSection 1 on stream 0 at 21-Apr-2023 11:32:22.698 CEST
> Begin processing the 7th record. Run 1, Event 7, LumiSection 1 on stream 0 at 21-Apr-2023 11:32:22.858 CEST
> Begin processing the 8th record. Run 1, Event 8, LumiSection 1 on stream 0 at 21-Apr-2023 11:32:25.345 CEST
> Begin processing the 9th record. Run 1, Event 9, LumiSection 1 on stream 0 at 21-Apr-2023 11:32:26.413 CEST
> Begin processing the 10th record. Run 1, Event 10, LumiSection 1 on stream 0 at 21-Apr-2023 11:32:39.373 CEST
> 
> *-------  PYTHIA Event and Cross Section Statistics  -------------------------------------------------------------*
> |                                                                                                                 |
> | Subprocess                                    Code |            Number of events       |      sigma +- delta    |
> |                                                    |       Tried   Selected   Accepted |     (estimated) (mb)   |
> |                                                    |                                   |                        |
> |-----------------------------------------------------------------------------------------------------------------|
> |                                                    |                                   |                        |
> | non-diffractive                                101 |           7          7          7 |   5.642e+01  0.000e+00 |
> | A B -> X B single diffractive                  103 |           1          1          1 |   6.416e+00  6.416e+00 |
> | A B -> A X single diffractive                  104 |           1          1          1 |   6.416e+00  6.416e+00 |
> | A B -> X X double diffractive                  105 |           1          1          1 |   8.798e+00  8.798e+00 |
> |                                                    |                                   |                        |
> | sum                                                |          10         10         10 |   7.805e+01  1.264e+01 |
> |                                                                                                                 |
> *-------  End PYTHIA Event and Cross Section Statistics ----------------------------------------------------------*
>
> *-------  PYTHIA Error and Warning Messages Statistics  ----------------------------------------------------------* 
> |                                                                                                                 | 
> |  times   message                                                                                                | 
> |                                                                                                                 | 
> |      3   Warning in MultipartonInteractions::init: maximum increased                                            | 
> |                                                                                                                 | 
> *-------  End PYTHIA Error and Warning Messages Statistics  ------------------------------------------------------* 
>
> *-------  PYTHIA Event and Cross Section Statistics  -------------------------------------------------------------*
> |                                                                                                                 |
> | Subprocess                                    Code |            Number of events       |      sigma +- delta    |
> |                                                    |       Tried   Selected   Accepted |     (estimated) (mb)   |
> |                                                    |                                   |                        |
> |-----------------------------------------------------------------------------------------------------------------|
> |                                                    |                                   |                        |
> | non-diffractive                                101 |           7          7          7 |   5.642e+01  0.000e+00 |
> | A B -> X B single diffractive                  103 |           1          1          1 |   6.416e+00  6.416e+00 |
> | A B -> A X single diffractive                  104 |           1          1          1 |   6.416e+00  6.416e+00 |
> | A B -> X X double diffractive                  105 |           1          1          1 |   8.798e+00  8.798e+00 |
> |                                                    |                                   |                        |
> | sum                                                |          10         10         10 |   7.805e+01  1.264e+01 |
> |                                                                                                                 |
> *-------  End PYTHIA Event and Cross Section Statistics ----------------------------------------------------------*
>
> *-------  PYTHIA Error and Warning Messages Statistics  ----------------------------------------------------------* 
> |                                                                                                                 | 
> |  times   message                                                                                                | 
> |                                                                                                                 | 
> |      3   Warning in MultipartonInteractions::init: maximum increased                                            | 
> |                                                                                                                 | 
> *-------  End PYTHIA Error and Warning Messages Statistics  ------------------------------------------------------* 
>
> ------------------------------------
> GenXsecAnalyzer:
> ------------------------------------
> Before Filter: total cross section = 7.805e+10 +- 1.264e+10 pb
> Filter efficiency (taking into account weights)= (10) / (10) = 1.000e+00 +- 0.000e+00
> Filter efficiency (event-level)= (10) / (10) = 1.000e+00 +- 0.000e+00    [TO BE USED IN MCM]
> 
> After filter: final cross section = 7.805e+10 +- 1.264e+10 pb
> After filter: final fraction of events with negative weights = 0.000e+00 +- 0.000e+00
> After filter: final equivalent lumi for 1M events (1/fb) = 1.281e-08 +- 2.075e-09
> 
> ============================================= 
> ```
{: .solution}


> ## Question 12.1
> What is the file size of `step1.root`? <br>
> For CMSDAS@CERN{{ site.year }} please submit your answers for the [CMSDAS@CERN{{ site.year }} Google Form third set][Set3_form].
{: .challenge}

## Generate MC dataset using CRAB

CRAB is handled by a *configuration file*. In CRAB3, the configuration file is in Python language. Here we give an example CRAB configuration file to run the `CMSDAS_MC_generation.py` MC event generation code. You can download a copy of [crabConfig_MC_generation.py]({{ page.root }}{% link code/crabConfig_MC_generation.py %}).

Below you also find the file:
> ## Show/Hide
>
> ```
> from WMCore.Configuration import Configuration
> config = Configuration()
>
> config.section_("General")
> config.General.requestName = 'CMSDAS_MC_generation_test0'
> config.General.workArea = 'crab_projects'
>
> config.section_("JobType")
> config.JobType.pluginName = 'PrivateMC'
> config.JobType.psetName = 'CMSDAS_MC_generation.py'
> config.JobType.allowUndistributedCMSSW = True
>
> config.section_("Data")
> config.Data.outputPrimaryDataset = 'MinBias'
> config.Data.splitting = 'EventBased'
> config.Data.unitsPerJob = 10
> NJOBS = 10  # This is not a configuration parameter, but an auxiliary variable that we use in the next line.
> config.Data.totalUnits = config.Data.unitsPerJob * NJOBS
> config.Data.publication = True
> config.Data.outputDatasetTag = 'CMSDAS2023_CRAB3_MC_generation_test0'
>
> config.section_("Site")
> config.Site.storageSite = 'T3_CH_CERNBOX'
> ```
{: .solution}
Put the copy of  `crabConfig_MC_generation.py` under `YOURWORKINGAREA/CMSSW_10_6_18/src`.


All available CRAB configuration parameters are defined at [CRAB3ConfigurationFile](https://twiki.cern.ch/twiki/bin/view/CMSPublic/CRAB3ConfigurationFile).

Now let us try to submit this job via crab by

```shell
crab submit -c crabConfig_MC_generation.py
```
{: .source}

For the detail of the crab command, you can find them from [CRABCommands](https://twiki.cern.ch/twiki/bin/view/CMSPublic/WorkBookCRAB3Tutorial#CRAB_commands). You will be requested to enter your grid certificate password. <br>
Then you should get an output similar to this:
```
Will use CRAB configuration file crabConfig_MC_generation.py
Enter GRID pass phrase for this identity:
Importing CMSSW configuration CMSDAS_MC_generation.py
Finished importing CMSSW configuration CMSDAS_MC_generation.py
Sending the request to the server at cmsweb.cern.ch
Success: Your task has been delivered to the prod CRAB3 server.
Task name: 230421_132846:vmilosev_crab_CMSDAS_MC_generation_test0
Project dir: crab_projects/crab_CMSDAS_MC_generation_test0
Please use ' crab status -d crab_projects/crab_CMSDAS_MC_generation_test0 ' to check how the submission process proceeds.
Log file is /afs/cern.ch/user/v/vmilosev/CMSDAS2023/Pre-exercises/CMSSW_10_6_18/src/crab_projects/crab_CMSDAS_MC_generation_test0/crab.log
```
{: .output}

 Now you might notice a directory called `crab_projects` is created under `CMSSW_10_6_18/src/`. *See what is under that directory.* After you submitted the job successfully (give it a few moments), you can check the status of a task by executing the following CRAB command:

 ```shell
 crab status [-t] <CRAB-project-directory>
 ```
 {: .source}
 In our case, we run:

 ```shell
crab status crab_projects/crab_CMSDAS_MC_generation_test0
```
{: .source}

The `crab status` command will produce an output containing the task name, the status of the task as a whole, the details of how many jobs are in which state (submitted, running, transfering, finished, cooloff, etc.) and the location of the CRAB log (`crab.log`) file. It will also print the URLs of two web pages that one can use to monitor the jobs. In summary, it should look something like this:

```
CRAB project directory:		/afs/cern.ch/user/v/vmilosev/CMSDAS2023/Pre-exercises/CMSSW_10_6_18/src/crab_projects/crab_CMSDAS_MC_generation_test0
Task name:			230421_132846:vmilosev_crab_CMSDAS_MC_generation_test0
Grid scheduler - Task Worker:	crab3@vocms0196.cern.ch - crab-prod-tw01
Status on the CRAB server:	SUBMITTED
Task URL to use for HELP:	https://cmsweb.cern.ch/crabserver/ui/task/230421_132846%3Avmilosev_crab_CMSDAS_MC_generation_test0
Dashboard monitoring URL:	https://monit-grafana.cern.ch/d/cmsTMDetail/cms-task-monitoring-task-view?orgId=11&var-user=vmilosev&var-task=230421_132846%3Avmilosev_crab_CMSDAS_MC_generation_test0&from=1682080126000&to=now
Task bootstrapped at 2023-04-21 13:29:37 UTC. 19 seconds ago
Status information will be available within a few minutes
Log file is /afs/cern.ch/user/v/vmilosev/CMSDAS2023/Pre-exercises/CMSSW_10_6_18/src/crab_projects/crab_CMSDAS_MC_generation_test0/crab.log
```
{: .output}

 Now you can take a break and have some fun. Come back after couple hours or so and check the status again.

```
[vmilosev@lxplus700 src]$ crab status crab_projects/crab_CMSDAS_MC_generation_test0/
CRAB project directory:		/afs/cern.ch/user/v/vmilosev/CMSDAS2023/Pre-exercises/CMSSW_10_6_18/src/crab_projects/crab_CMSDAS_MC_generation_test0
Task name:			230421_132846:vmilosev_crab_CMSDAS_MC_generation_test0
Grid scheduler - Task Worker:	crab3@vocms0196.cern.ch - crab-prod-tw01
Status on the CRAB server:	SUBMITTED
Task URL to use for HELP:	https://cmsweb.cern.ch/crabserver/ui/task/230421_132846%3Avmilosev_crab_CMSDAS_MC_generation_test0
Dashboard monitoring URL:	https://monit-grafana.cern.ch/d/cmsTMDetail/cms-task-monitoring-task-view?orgId=11&var-user=vmilosev&var-task=230421_132846%3Avmilosev_crab_CMSDAS_MC_generation_test0&from=1682080126000&to=now
Status on the scheduler:	COMPLETED

Jobs status:                    finished     		100.0% (10/10)

Publication status of 1 dataset(s):	done         		100.0% (10/10)
(from CRAB internal bookkeeping in transferdb)

Output dataset:			/MinBias/vmilosev-CMSDAS2023_CRAB3_MC_generation_test0-67359df6f8a0ef3c567d7c8fea38a809/USER
Output dataset DAS URL:		https://cmsweb.cern.ch/das/request?input=%2FMinBias%2Fvmilosev-CMSDAS2023_CRAB3_MC_generation_test0-67359df6f8a0ef3c567d7c8fea38a809%2FUSER&instance=prod%2Fphys03

Warning: the max jobs runtime is less than 30% of the task requested value (1250 min), please consider to request a lower value for failed jobs (allowed through crab resubmit) and/or improve the jobs splitting (e.g. config.Data.splitting = 'Automatic') in a new task.

Warning: the average jobs CPU efficiency is less than 50%, please consider to improve the jobs splitting (e.g. config.Data.splitting = 'Automatic') in a new task

Summary of run jobs:
 * Memory: 26MB min, 66MB max, 40MB ave
 * Runtime: 0:04:34 min, 0:05:05 max, 0:04:41 ave
 * CPU eff: 14% min, 58% max, 33% ave
 * Waste: 1:17:58 (62% of total)

Log file is /afs/cern.ch/user/v/vmilosev/CMSDAS2023/Pre-exercises/CMSSW_10_6_18/src/crab_projects/crab_CMSDAS_MC_generation_test0/crab.log
```
{: .output}

**Note**: If at `lxplus`, it will write out to your eos area. You can access them from `/eos/user/$U/$USER/SUBDIR` with `SUBDIR` being the subdirectory name you provided. Take a look at that directory. (In our example we looked at `MinBias` and named the task `CMSDAS2021_CRAB3_MC_generation_test0`. The subsequent date string depends when you started your task.)

From the bottom of the output, you can see the name of the dataset and the DAS link to it. Congratulations! This is the your first CMS dataset.

> ## Question 12.2
> What is the name of the dataset you produced? <br>
> For CMSDAS@CERN{{ site.year }} please submit your answers for the [CMSDAS@CERN{{ site.year }} Google Form third set][Set3_form].
{: .challenge}

# Exercise 13 - Running on a dataset with CRAB

Now we're going to apply what you've learned using CRAB to the `MiniAOD` exercises you've been working on in the first two sets of exercises. Make sure that you finished and still have the scripts from [Exercise 7]({{ page.root }}{% link _episodes/02-CMSDataAnalysisSchoolPreExerciseSecondSet.md %}#exercise-7---slim-miniaod-sample-to-reduce-its-size-by-keeping-only-muon-and-electron-branches) under the `YOURWORKINGAREA/CMSSW_10_6_18/src`.

##  Set up CRAB to run your MiniAOD jobs

If you forget, go back to the `YOURWORKINGAREA/CMSSW_10_6_18/src` and setup crab.

```shell
cmsenv
```
{: .source}

We will make another CRAB config file: `crabConfig_data_slimMiniAOD.py`. Copy it from here: [crabConfig_data_generation.py]({{ page.root }}{% link code/crabConfig_data_generation.py %}) and find it below:

> ## Show/Hide
>
> ```
> from WMCore.Configuration import Configuration
> config = Configuration()
>
> config.section_("General")
> config.General.requestName = 'CMSDAS_Data_analysis_test0'
> config.General.workArea = 'crab_projects'
>
> config.section_("JobType")
> config.JobType.pluginName = 'Analysis'
> config.JobType.psetName = 'slimMiniAOD_data_MuEle_cfg.py'
> config.JobType.allowUndistributedCMSSW = True
>
> config.section_("Data")
> config.Data.inputDataset = '/DoubleMuon/Run2016C-03Feb2017-v1/MINIAOD'
> config.Data.inputDBS = 'global'
> config.Data.splitting = 'LumiBased'
> config.Data.unitsPerJob = 50
> config.Data.lumiMask = 'https://cms-service-dqmdc.web.cern.ch/CAF/certification/Collisions16/13TeV/Cert_271036-275783_13TeV_PromptReco_Collisions16_JSON.txt'
> config.Data.runRange = '275776-275782'
>
> config.section_("Site")
> config.Site.storageSite = 'T3_CH_CERNBOX'
> ```
{: .solution}

 Most of this file should be familiar by now, but a few things may be new. The `runRange` parameter is used to further limit your jobs to a range of what is in the `lumiMask` file. This is needed if your two input datasets overlap. That way you can control which events come from which datasets. Instructions how to do this are at [https://twiki.cern.ch/twiki/bin/viewauth/CMS/PdmVAnalysisSummaryTable](https://twiki.cern.ch/twiki/bin/viewauth/CMS/PdmVAnalysisSummaryTable). You can find the year specific instructions by clicking any of the links at the bottom.

## Run CRAB

 Now go through the same process for this config file. You submit it with

 ```shell
 crab submit -c crabConfig_data_slimMiniAOD.py
 ```
 {: .source}

 and check the status with

 ```shell
 crab status
 ```
 {: .source}

 After a while, you should see something like below:

```
CRAB project directory:		/afs/cern.ch/user/v/vmilosev/CMSDAS2023/Pre-exercises/CMSSW_10_6_18/src/crab_projects/crab_CMSDAS_Data_analysis_test0
Task name:			230421_160319:vmilosev_crab_CMSDAS_Data_analysis_test0
Grid scheduler - Task Worker:	crab3@vocms0199.cern.ch - crab-prod-tw01
Status on the CRAB server:	SUBMITTED
Task URL to use for HELP:	https://cmsweb.cern.ch/crabserver/ui/task/230421_160319%3Avmilosev_crab_CMSDAS_Data_analysis_test0
Dashboard monitoring URL:	https://monit-grafana.cern.ch/d/cmsTMDetail/cms-task-monitoring-task-view?orgId=11&var-user=vmilosev&var-task=230421_160319%3Avmilosev_crab_CMSDAS_Data_analysis_test0&from=1682089399000&to=now
Status on the scheduler:	COMPLETED

Jobs status:                    finished     		100.0% (31/31)

Publication status of 1 dataset(s):	done         		100.0% (31/31)
(from CRAB internal bookkeeping in transferdb)

Output dataset:			/DoubleMuon/vmilosev-crab_CMSDAS_Data_analysis_test0-dfbd2918d11fceef1aa67bdee18b8002/USER
Output dataset DAS URL:		https://cmsweb.cern.ch/das/request?input=%2FDoubleMuon%2Fvmilosev-crab_CMSDAS_Data_analysis_test0-dfbd2918d11fceef1aa67bdee18b8002%2FUSER&instance=prod%2Fphys03

Warning: the max jobs runtime is less than 30% of the task requested value (1250 min), please consider to request a lower value for failed jobs (allowed through crab resubmit) and/or improve the jobs splitting (e.g. config.Data.splitting = 'Automatic') in a new task.

Summary of run jobs:
 * Memory: 153MB min, 914MB max, 578MB ave
 * Runtime: 0:03:25 min, 0:17:22 max, 0:07:30 ave
 * CPU eff: 22% min, 77% max, 56% ave
 * Waste: 0:04:15 (2% of total)

Log file is /afs/cern.ch/user/v/vmilosev/CMSDAS2023/Pre-exercises/CMSSW_10_6_18/src/crab_projects/crab_CMSDAS_Data_analysis_test0/crab.log
```
{: .output}

## Create reports of data analyzed

Once all jobs are **finished** (see `crab status` above) you can report:

```shell
crab report
````
{: .source}

You'll get something like this
```
Running crab status first to fetch necessary information.
Will save lumi files into output directory /afs/cern.ch/user/v/vmilosev/CMSDAS2023/Pre-exercises/CMSSW_10_6_18/src/crab_projects/crab_CMSDAS_Data_analysis_test0/results
Summary from jobs in status 'finished':
  Number of files processed: 64
  Number of events read: X
  Number of events written in EDM files: X
  Number of events written in TFileService files: 0
  Number of events written in other type of files: 0
  Processed lumis written to processedLumis.json
Summary from output datasets in DBS:
  Number of events:
    /DoubleMuon/vmilosev-crab_CMSDAS_Data_analysis_test0-dfbd2918d11fceef1aa67bdee18b8002/USER: 2167324
  Output datasets lumis written to outputDatasetsLumis.json
Additional report lumi files:
  Input dataset lumis (from DBS, at task submission time) written to inputDatasetLumis.json
  Lumis to process written to lumisToProcess.json
Log file is /afs/cern.ch/user/v/vmilosev/CMSDAS2023/Pre-exercises/CMSSW_10_6_18/src/crab_projects/crab_CMSDAS_Data_analysis_test0/crab.log
```
{: .output}

 `crab report` prints to the screen how many events were analyzed.


> ## Question 13
> How many events were analyzed? (n.b. the number in the above example were replaced with `X`) <BR>
> For CMSDAS@CERN{{ site.year }} please submit your answers for the [CMSDAS@CERN{{ site.year }} Google Form third set][Set3_form].
{: .challenge}

## Optional: View the reconstructed Z peak in the combined data

> ## Note
> You will be doing a short analysis later when going to [exercise set number four]({{ page.root }}{% link _episodes/04-CMSDataAnalysisSchoolPreExerciseFourthSet.md %}).
>
{: .callout}

Use the `FWLiteHistograms` executable you were using in the previous exercises to aggregate the data from all the CRAB output files. The root files created in the above step have been kept at the directory below: `/eos/user/$U/$USER/DoubleMuon/crab_CMSDAS_Data_analysis_test0/` One can use the command:

``` shell
FWLiteHistograms inputFiles=File1,File2,File3,... outputFile=ZPeak_data.root maxEvents=-1 outputEvery=100
```
{: .source}

In my case, `File1=/eos/user/v/vmilosev/DoubleMuon/crab_CMSDAS_Data_analysis_test0/230421_160319/0000/slimMiniAOD_data_MuEle_1.root` etc.. Make sure there is no space in `File1,File2,File3,...`

You may look at `ZPeak_data.root` using `TBrowser`.

# Exercise 14  - Combining the data and calculating luminosity

> ## Note
> This last exercise in this set is done on **lxplus**.
>
{: .callout}

## Install the BRIL Work Suite

We will use the BRIL work suite, a commandline toolkit for CMS Beam Radiation Instrumentation and Luminosity to calculate the total luminosity of the data we ran over.

Refer to [the documentation](https://cmslumi.web.cern.ch/) for further information on BRIL.

Enter the following command:

``` shell
/cvmfs/cms-bril.cern.ch/brilconda3/bin/python3 -m pip install --user --upgrade brilws
```
{: .source}

When running `crab report`, the report will give you the location of a **JSON-formatted file** containing the luminosity information
```
Will save lumi files into output directory /afs/cern.ch/user/v/vmilosev/CMSDAS2023/Pre-exercises/CMSSW_10_6_18/src/crab_projects/crab_CMSDAS_Data_analysis_test0/results
```
{: .output}

 This directory contains various luminosity files. Let's figure out how much luminosity was run on by our jobs.

First step is to copy the `processedLumis.json` file to your `.local/bin/` folder:
``` shell
cp [lumi directory]/processedLumis.json ~/.local/bin/
```
{: .source}

Here, `[lumi directory]` is the directory reported by `crab report`.

## Find the luminosity for the dataset

We now let `brilcalc` calculate the luminosity we processed with our jobs using the json file by typing following commands:
``` shell
cd ~/.local/bin/
./brilcalc lumi -b "STABLE BEAMS" --normtag /afs/cern.ch/user/l/lumipro/public/Normtags/normtag_DATACERT.json -i processedLumis.json -u /fb
```
if the above does not work, try instead:
```shell
./brilcalc lumi -b "STABLE BEAMS" --normtag /afs/cern.ch/user/l/lumipro/public/Normtags/normtag_DATACERT.json -i processedLumis.json -c /cvmfs/cms.cern.ch/SITECONF/T0_CH_CERN/JobConfig/site-local-config.xml -u /fb
```

 The end of the output should look similar to this (note this example summary is for a different json file):
 ```
 #Summary:
 +-------+------+-------+-------+-------------------+------------------+
 | nfill | nrun | nls   | ncms  | totdelivered(/fb) | totrecorded(/fb) |
 +-------+------+-------+-------+-------------------+------------------+
 | 9     | 37   | 17377 | 17377 | 2.761             | 2.646            |
 +-------+------+-------+-------+-------------------+------------------+
 #Check JSON:
 #(run,ls) in json but not in results: [(275890, 721)]
 ```
 {: .output}

In the example of that other json file, the total recorded luminosity for those CRAB jobs is 2.6 fb<sup>-1</sup>.

> ## Question 14
>  What is the reported number of inverse femtobarns analyzed? (n.b. it is not the same sample as listed above with luminosity 2.6<sup>-1</sup>. )
> For CMSDAS@CERN{{ site.year }} please submit your answers for the [CMSDAS@CERN{{ site.year }} Google Form third set][Set3_form].
{: .challenge}

#  Where to find more on CRAB

- [CRAB Home](https://twiki.cern.ch/twiki/bin/view/CMSPublic/SWGuideCrab) <br>
- [CRAB FAQ](https://twiki.cern.ch/twiki/bin/view/CMSPublic/CRAB3FAQ) <br>
- [CRAB troubleshooting guide](https://twiki.cern.ch/twiki/bin/view/CMSPublic/CRAB3Troubleshoot): Steps to address the problems you experience with CRAB and how to ask for support. <br>
- [CMS Computing Tools mailing list](https://hypernews.cern.ch/HyperNews/CMS/get/computing-tools.html), where to send feedback and ask support in case of jobs problem (please send to us your crab task HELP URL from crab status output). <br>

Note also that all CMS members using the Grid subscribe to the [Grid Annoucements CMS HyperNews forum](https://hypernews.cern.ch/HyperNews/CMS/get/gridAnnounce.html). Important CRAB announcements will be announced on the [CERN Computing Announcement HyperNews forum](https://hypernews.cern.ch/HyperNews/CMS/get/cernCompAnnounce.html). <br>

<br><br>
_Last reviewed: 2023/04/20 by Vukasin Milosevic
<br>

{% include links.md %}

[Set3_form]: https://forms.gle/RgtCub5P5kwkoCCy8
