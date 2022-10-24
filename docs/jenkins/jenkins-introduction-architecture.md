# Introduction and Architecture Jenkins


## Introduction
Jenkins is a Continous Integration / Contiouse Deployment tool is used for automation and allows all the developers to build, test and deploy software. Continuous Integration is a process where in developers commit changes to source code from a shared repository, and all the changes to the source code are integrated continuously. 

Jenkins is one of the top DevOps tools because it is free, open-source, and can integrate with pretty much every other DevOps tool out there. There are over a thousand plugins that you can use to extend Jenkins capabilities and make it more user-specific. Jenkins is a self-contained Java program that is agnostic of the platform on which it is installed. It is available for almost all the popular operating systems such as Windows, different flavors of Unix, and Mac OS.

## Architecture

![trigger-jenkins](/images/ci-architecture.png)

- Developers do the necessary modifications in the source code and commit the changes to the repository.

- The Jenkins CI server checks the repository at regular intervals or immediately after code is pushed and pulls any newly available code.

- The Build Server tests and  builds the code to the specified requirements. In case the build fails.

- If successful, jenkins can deploy the application to the required destinations. If at any stage of the pipeline a step fails; then notifications can be sent out.