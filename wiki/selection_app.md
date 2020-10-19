# Selection Application

The Nets Selection Application serves as the tie in between the ECR application
and the different supported payment methods.

Depending on the provided payment methods, it will display a selection screen
on payment where the customer can select the method desired.

## Installing the Selection Application for testing

When testing, the Selection Application can be downloaded from the 
[release](https://creationlab.visualstudio.com/_git/SmartPOS%20Feed?path=%2Frelease)
folder of this repository.

Once downloaded, load up the `Loader.exe` utility from Castles, select the
correct COM port, and navigate to the directory you've downloaded the Selection
Application to. There, pick the `debug.mci` file. On the SmartPOS, load the
`loader` application, and then click "Download" in the `Loader.exe` utility once
the application is ready. Once the application is installed, you can open it
from the launcher ("Nets Payment Selection"), and check that you have the newest
version, as well as set test amounts and currency to run test flows.
