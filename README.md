# MICScan

MICScan is a project created by Vincent Huang, Andrew Boychuck and Chris Zielin as students in the Master of Information and Cybersecurity (MICS) program at UC Berkeley. The project began as an effort to see if we could take a dataset of known vulnerable code labelled by CWE ID and train a machine learning model to recognize vulnerabilities. The model was able to achieve 99.8% accuracy in classifying vulnerabilities in the [NIST SARD](https://samate.nist.gov/SARD/). 

Additional datasets were sought to see if we could generalize and expand on these initial promising results. However, finding additional labelled vulnerable code beyond the SARD proved difficult. To attempt to solve this problem we expanded the project to build a tool that allows developers to share known vulnerable code. Any dataset compiled with these tool will be used to not only improve the MICScan tool, but will be shared back with the NIST SARD with the intention of improving other scanning tools as well. 

## Projects within this solution

**Classify**

This project contains a single python script that takes as input a json file with a list of scanned files and the features for each that have been generated by the **Scan** application. It uses the model generated by the **ModelGenerator** to classify each of the files based on their features. It saves the output, including the class of each file, to another json file for consumption by the **Scan** appplication.

**Data**

This is project that uses Entity Framework to define the database schema used by the **Web** project for the MICScan API.

**DevOpsExtension**

This project contains the content of the Azure DevOps extension. The extension allows the MICScan **Scan** application to be added to a DevOps pipeline in order to scan for vulnerabalities during a build. It can be configured to either raise a warning when a vulnerability is detected, or fail the build altogether. There is a *build.ps1* file that can be used to build and package the extension when updates are made.

![Find DevOps Extension](https://github.com/czielin/MICScan/blob/master/images/DevOpsPipelineFindExtension.png?raw=true)

![DevOps Pipeline Settings](https://github.com/czielin/MICScan/blob/master/images/DevOpsPipelineSettings.png?raw=true)

**ModelGenerator**

This project contains the code that pre processes the training dataset (initially just the SARD) then builds a model for later consumption by the **Scan** and **Classify** projects. The code is preprocessed by using Microsoft's Roslyn to parse the source code and generate features. Initially only C# is supported. The model is generated using scikit-learn in the *SARD Vulnerability Classification* python notebook. Several different algorithms were attempted. Logistic regression has given the most accurate results so far.

The SARD dataset is fairly large and is not included in this repository. It must be downloaded separately, extracted and referenced using the `sardRoot` variable in Program.cs.

**Scan**

This is a command line application that takes a project file as input. It takes every C# file referenced in the project file preprocesses it reusing the labelling functionality from **ModelGenerator** and then classifies it using the python script in the **Classify** project.

**TestApplication**

This is a simple fake test application that we have used to introduce mock bugs while testing the **Scan** and **DevOpsExtension** features.

**VisualStudioExtension**

This project contains a Visual Studio extension that allows sharing of vulnerable commits through the MICScan API in the **Web** project. This is done by right clicking on a commit in the Git history within Visual Studio and selecting the option to share with MICScan.

![Share Vulnerability Context Menu](https://github.com/czielin/MICScan/blob/master/images/GitHistory.png?raw=true)

![Share Vulnerability Dialog](https://github.com/czielin/MICScan/blob/master/images/ShareDialog.png?raw=true)

**Web**

This is an ASP.NET Web API project whose sole purpose right now is to collect submissions of vulnerable code shared from the Visual Studio extension in the **VisualStudioExtension** project.
