# PSAppDeployToolkit: WASGuide (Winget As System)
## Deploy Winget apps in SYSTEM context with Intune

### Why Using PSAppDeployToolkit to Install Winget Apps in the System Context is Beneficial

**Windows Package Manager (winget)** has emerged as a powerful tool for managing applications on Windows. It simplifies the process of installing, updating, and removing applications, offering a centralized repository for packages and a consistent command-line interface. However, winget is primarily designed for user-level applications and doesn't natively support system-level installations.

This is where **PowerShell App Deployment Toolkit (PSAppDeployToolkit)** comes into play. PSAppDeployToolkit is a powerful and commonly used framework that provides a comprehensive set of well-defined functions for handling common deployment tasks, including installation, uninstallation, and updating applications. It also offers user interface elements for seamless end-user interactions during the deployment process.

In enterprise environments, deploying applications can be a complex task. PSAppDeployToolkit streamlines this process and adds flexibility. It simplifies the complex scripting challenges of deploying applications in the enterprise, provides a consistent deployment experience, and improves installation success rates.

When it comes to deploying winget apps in the system context, PSAppDeployToolkit provides a robust wrapper architecture. It allows you to resolve the path to the winget.exe to use it in the system context. This is particularly beneficial as most users are standard users without admin rights, and system-level installations often require tasks like modifying system files or registry settings.

In summary, using PSAppDeployToolkit to install winget apps in the system context is beneficial because it enhances the functionality of winget, provides additional features for deploying packages, and simplifies the deployment process in enterprise environments. It's a valuable skill to have, especially for IT professionals managing software deployments on Windows.


## **Overview**

Use this guide to get started with familiarizing yourself with how to
use Winget and the PSAppDeployToolkit to create app packages for Intune.
In this guide you will learn how to package and deploy Notepad++


## **winget**

- winget is the Windows Package Manager command-line tool that
    allows users to discover, install, upgrade, remove, and configure
    applications on Windows 10 and Windows 11 systems1.
- It's designed to provide a Linux-style package management
    experience in Windows, where you can install applications with a
    single command, like winget install \<appname\>2.
- winget is included in the Windows App Installer and is available
    on modern versions of Windows 10 and Windows 111.

## **PSAppDeployToolkit**

-   \- The PSAppDeployToolkit is a framework for deploying applications
    in a business or corporate environment6.
-   \- It offers a set of functions for common application deployment
    tasks and user interface elements for end-user interaction during a
    deployment6.
-   \- The toolkit aims to simplify the complex scripting challenges of
    deploying applications and to provide a consistent deployment
    experience9.


Both tools are aimed at improving the software management lifecycle on
Windows platforms, making it easier for administrators and users to
handle applications.


## **Obtain winget IDs**

Each winget package has a unique identifier called a package ID. You can
use this ID to easily install or manage the application.


### **Search winget ID with Windows PowerShell**

To search for a winget package using PowerShell, follow these steps:


Open PowerShell terminal. (You don't need to be an admin.) Enter the
following command, replacing \<application name\> with the name of the
application you\'re looking for:

<div>

``` {contenteditable="false" code-brush="Generic Language" rel="highlighter"}
winget search "<application name>"
```

</div>


### **Obtain winget IDs Via the winget catalog**

In addition to using PowerShell, you can obtain winget package IDs by
searching the winget catalog. The catalog is a publicly accessible
repository of packages that provides detailed information about each
package, including its name, publisher, description, package ID, and
system requirements.


1.  Open your web browser, and go to the winget catalog website:
    https://winget.run/
2.  In the search bar, enter the name of the application you're looking
    for. The search results will display a list of matching packages,
    including their package IDs.
3.  Once you've found the package you're looking for, note down its
    package ID.


## **Configure PSAppDeployToolkit Installation** 

As mentioned, PSAppDeployToolkit streamlines and adds flexibility to
common deployment tasks, including installation, uninstallation, and
updating applications.


To further enhance customization and flexibility, PSAppDeployToolkit
provides a robust wrapper architecture. This wrapper can be tailored to
accommodate specific requirements, such as branding, custom user
interface elements, and integration with third-party tools.


### **Download and customize PSAppDeployToolkit**

Download PSAppDeployToolkit at
[https://github.com/PSAppDeployToolkit/PSAppDeployToolkit/releases/](https://github.com/PSAppDeployToolkit/PSAppDeployToolkit/releases/).


Extract and save the contents to a location of your choosing. You will
always want to copy the contents to new folder for your app. Keep the
original folder and its contents as a template.


### **Modify the script**

Open the PowerShell file **Deploy-Application.ps1**
in your PSAppDeployToolkit (here referred to as PSADT) folder. Use an
editor like VS Code or Notepadd++ to edit the file. Define the package
details. As you can see in the screenshot, you can enter every variable,
but you do not have to declare every variable if you do not know all
variables.


![](https://s3.amazonaws.com/cdn.freshdesk.com/data/helpdesk/attachments/production/153008899728/original/WKRVJdrMmdHenrIulfKxq_mj51Ri2xKiCQ.jpg?1714014386)


Scroll down a bit and locate the comment **## <Perform Installation
tasks here\>**. Below that, insert the installation command:


<div>

``` 
# Resolve winget.exe
$winget_exe = Resolve-Path "C:\Program Files\WindowsApps\Microsoft.DesktopAppInstaller_*_*__8wekyb3d8bbwe\winget.exe"
if ($winget_exe.count -gt 1){
        $winget_exe = $winget_exe[-1].Path
}
if (!$winget_exe){Write-Error "Winget not installed"}
# Installation via winget
& $winget_exe install "AppID.Here" --silent --accept-source-agreements --accept-package-agreements
```

</div>

If you go even further, there is the **## <Perform Uninstallation tasks here\>**

Below that, paste the uninstallation command:


<div>

```
# Resolve winget.exe
$winget_exe = Resolve-Path "C:\Program Files\WindowsApps\Microsoft.DesktopAppInstaller_*_*__8wekyb3d8bbwe\winget.exe"
if ($winget_exe.count -gt 1){
        $winget_exe = $winget_exe[-1].Path
}
if (!$winget_exe){Write-Error "Winget not installed"}
# Installation via WinGet
& $winget_exe uninstall "AppID.Here" --silent
```

</div>


That is all that you need to do to prepare the PSADT configuration.


``` 
The install parameters may vary from app to app. If the default --silent argument does not work you will have to research the app you are deploying.
```



### **Pacakge app for Intune with the Win32 Content Prep Tool**

First, create the intunewin file to upload to Intune:

1.  Download the latest [Microsoft Win32 Content Prep
    Tool](https://github.com/microsoft/Microsoft-Win32-Content-Prep-Tool/blob/master/IntuneWinAppUtil.exe)
2.  Open the downloaded \"**IntuneWinAppUtil.exe**\"
3.  Provide the path to your package.
4.  Enter the installation file: **Deploy-Application.exe**
5.  Enter the path to save the intunewin file.
6.  For the catalog folder question, choose **(No)**.

You have now created the .intunewin file that will be uploaded to
Intune.



## **Upload to Intune and configure as Windows (Win32) app**

Log into the Intune admin portal at
[https://intune.microsoft.com/](https://intune.microsoft.com/)
and navigate to **Apps** > **Windows**. Choose **+ Add** and select the **Windows app (Win32)** option to get started.



**![](https://s3.amazonaws.com/cdn.freshdesk.com/data/helpdesk/attachments/production/153010641467/original/a68ikDQN7aodvfNuDCr46dCUP8gYol784g.jpg?1716131325)**



### **Upload .intunewin file**

Now you can upload the .intunewin file you prepared previously. In the
App information section, define the **Name**, **Description**, and
**Publisher**. In addition, you should add a logo whenever possible, if
the app will be visible in the Company Portal.


![](https://s3.amazonaws.com/cdn.freshdesk.com/data/helpdesk/attachments/production/153010641691/original/TRCX8DPjYHqBNSxCvfz-2AffjdhLCQL3ww.jpg?1716131400)



### **Define program commands**

In the **Program** section you will need to define the commands for
installing and uninstalling the app. Most of the time you will use these
same commands on most apps.


<div>

``` 
# Install command
Deploy-Application.exe -DeploymentType "Install" -DeployMode "Silent"

# Install Command w/ ServiceUI
.\ServiceUI.exe -Process:explorer.exe Deploy-Application.exe -DeploymentType "Install" -DeployMode "Silent"

# Uninstall command
Deploy-Application.exe -DeploymentType "Uninstall" -DeployMode "Silent"
```



Set to allow available uninstall and leave the rest of the fields at
their default values.


![](https://s3.amazonaws.com/cdn.freshdesk.com/data/helpdesk/attachments/production/153010641879/original/-eaWj6aKeeZecCSWzQDRfTTNhSrT5rm9Pg.jpg?1716131469)



### **Set requirements for app**

In the **Requirements** section, I recommend using the following for the
required fields:


-   **Operating system architecture:** 64-bit
-   **Minimum operating system:** Windows 10 2004




![](https://s3.amazonaws.com/cdn.freshdesk.com/data/helpdesk/attachments/production/153010642085/original/Q0cQJlzcTlK_DOQoVZwcAWCcxTxHM1XsgA.jpg?1716131533)



### **Set up detection rules or script**

In the **Detection rules┬á**section, you can upload a detection script or
create custom detection rules for insallation and uninstallation of the
app.


You have 2 options to choose from, **Use a custom detection script** or
**Manually configure detection rules.** This guide will cover using
custom rules. If you are knowledgeable with scripting, you can create
your own detection script and upload that rather than using rules.



Click **+ Add** to create your rule. There are 3 rule types you can
create:

-   MSI
-   File
-   Registry

For this rule, select **File** for the rule type and define the rule
parameters.


![](https://s3.amazonaws.com/cdn.freshdesk.com/data/helpdesk/attachments/production/153010642366/original/G9is5a5OysE3d95wIOab5ZM2L28C_hXMJA.jpg?1716131608)



### **Review save & assign**

Once you have finished configuring your app review your configurations
and save. Now you can assign the app to all users or specific groups of
users.



It is good practice to assign it to a test group first and test that
your configuration can successfully install and uninstall the app before
assigning to a broad scope.

</div>

