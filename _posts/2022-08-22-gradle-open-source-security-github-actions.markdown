---
layout: post
title:  "Detect open source vulnerabilities in Gradle projects with Github actions"
date:   2022-09-11 19:00:00 +0300
categories: appsec
---


Software Composition Analysis (SCA) is the automated process of analyzing libraries and open source software with the aim of identifying publicly disclosed vulnerabilities, as well as license usage for compliance reasons.

Open source vulnerabilities are published in several online repositories and firstly the automated tool performing the SCA analysis clones this public information into its own knowledge database. Then it typically tries to match vulnerable versions of open source components used in the codebase being analysed. As an example, the tool can detect that log4j-core version 2.12.3 is being used and therefore generate a warning with information about the critical exploitable flaws. Of course, the analysis performed by SCA tools may be more detailed, especially if they are proprietary tools, but on a high level this is how the basic open source security analyzer works.

OWASP Dependency Check

OWASP provides a wonderful open source SCA analyzer called OWASP Dependency Check that "attempts to detect publicly disclosed vulnerabilities contained within a project’s dependencies."

In the simplest scenario, the command line flavor of the tool can be downloaded and used to check for open source vulnerabilities in a locally cloned repository. Simply download the tool and run: ./dependency-check.sh -project "name of your project" -scan "path to source code". To analyze Gradle projects you would have to follow the instructions here.

However, the command line version does offer plenty of flexibility or ease of sharing the analysis results. A better approach would be to automate the analysis centrally from your source code management tool, which is more structured and also a natural place for code metrics and security analysis. If you are using Github then Github Actions is the way to go.

What about dependabot?

Github provides dependabot for SCA analysis which is free and amazing. However it comes with limitations when analyzing Java applications built with Gradle. OWASP Dependency Check has no problem analyzing Gradle projects, but the analysis process needs to be automated with Github actions so that it's usable by the development teams.

I do not suggest that OWASP Dependency Check is better or worse, but I do like the support for Gradle and also the fact that it is an amazing open source project from OWASP. So let's see how to run an SCA analysis for Gradle using OWASP Dependency Check with Github actions!

Reusable workflow

If you are not familiar with reusable workflows you should first read up here first. A reusable workflow is simply a set of actions that can be reused by other projects, simply by referencing the workflow. So in a work environment you could have a central repository where your reusable workflow reside and then you reference those from any other repository you wish to analyze.

The reusable workflow looks like this:

name: SCA reusable workflow for Gradle Projects

# Is needed to make the workflow reusable
on:
  workflow_call:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3
      
      # Setup python
      - name: Setup python
        uses: actions/setup-python@v2
        with:
          python-version: 3.10.7

      # Inject plugin code into build.gradle to enable the analysis
      - name: Execute python script
        run: |
            import re
            with open('build.gradle','r+') as build_file:
                lines_write = ['\nbuildscript {\n','\n\trepositories {\n','\n\t\tmavenCentral()\n','\n\t}\n','\n\tdependencies {\n',"\n\t\tclasspath 'org.owasp:dependency-check-gradle:7.1.2'\n",'\n\t}\n','\n}\n\n']
                contents = build_file.read()
                build_file.seek(0,0)
                build_file.writelines(lines_write)
                build_file.write(contents)
                build_file.write("\napply plugin: 'org.owasp.dependencycheck'\n")
                build_file.close()
        shell: python
        
      - name: run the dependency check analysis
        run: ./gradlew dependencyCheckAnalyze
        
      - uses: actions/upload-artifact@v3
        with:
          name: security-analysis-gradle
          path: build/reports/dependency-check-report.html

In order to analyze Gradle we need to install the necessary plugin into build.gradle as explained here.
OWASP Dependency Check plugin for Gradle
OWASP Dependency Check plugin for Gradle

To do that while running the analysis in Github Actions we use python to inject these lines of code at run time. This is done at the step with the name: "Execute python script".

The analysis is then run using "./gradlew dependencyCheckAnalyze". This command generates a dependency check report under build/reports. To make it easier to find the report when the action is executed, an artifact is created as well. In this way, anyone with access to the repo can download the report at a later time under Actions in Github.

Running the analysis

Finally, to run the analysis you simply need to reference you workflow from a Github action. For example you can configure the analysis to run everyday at midnight like this:


# Name of this GitHub Actions workflow.
name: Open source security analysis for this Gradle Project using Owasp Dependency check

on:
  schedule:
      0 0 * * *

  pull_request:
    branches:
      - main

jobs:
  gradle-open-source-security-analysis:
    uses: path/to-your-project-where-the-reusable-workflow-is/.github/workflows/name-of-workflow.yml@versiontag

As a result you can find your report in Actions as an artifact

![image]({{site.baseurl}}/docs/assets/images/image-1.png)

Github artifact with OWASP dependency check report
Github artifact with OWASP dependency check report
