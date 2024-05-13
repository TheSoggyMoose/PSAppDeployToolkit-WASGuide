# PSAppDeployToolkit: WAS-Guide (Winget As System)
## Deploy Winget apps in SYSTEM context with Intune

## **Overview**

Use this guide to get started with familiarizing yourself with how to use Winget and the PSAppDeployToolkit to create app packages for Intune. In this guide you will learn how to package and deploy Notepad++

## **winget**

- winget is the Windows Package Manager command-line tool that allows users to discover, install, upgrade, remove, and configure applications on Windows 10 and Windows 11 systems.
- It's designed to provide a Linux-style package management experience in Windows, where you can install applications with a single command, like 
```
winget install <appname>
```


- winget is included in the Windows App Installer and is available on modern versions of Windows 10 and Windows 11.

## **PSAppDeployToolkit**

- The PSAppDeployToolkit is a framework for deploying applications in a business or corporate environment.
- It offers a set of functions for common application deployment tasks and user interface elements for end-user interaction during a deployment.
- The toolkit aims to simplify the complex scripting challenges of deploying applications and to provide a consistent deployment experience.

## **Obtain winget IDs**

Each winget package has a unique identifier called a package ID. You can use this ID to easily install or manage the application.


### **Obtain winget IDs Via the winget catalog**

In addition to using PowerShell, you can obtain winget package IDs by searching the winget catalog. The catalog is a publicly accessible repository of packages that provides detailed information about each package, including its name, publisher, description, package ID, and system requirements.

1. Open your web browser, and go to the winget catalog website: winget.run
2. In the search bar, enter the name of the application you're looking for. The search results will display a list of matching packages, including their package IDs.
3. Once you've found the package you're looking for, note down its package ID.

## **Configure PSAppDeployToolkit Installation**

As mentioned, PSAppDeployToolkit streamlines and adds flexibility to common deployment tasks, including installation, uninstallation, and updating applications.

To further enhance customization and flexibility, PSAppDeployToolkit provides a robust wrapper architecture. This wrapper can be tailored to accommodate specific requirements, such as branding, custom user interface elements, and integration with third-party tools.

### **Download and customize PSAppDeployToolkit**

Download PSAppDeployToolkit at the official GitHub releases page.

Extract and save the contents to a location of your choosing. You will always want to copy the contents to a new folder for your app. Keep the original folder and its contents as a template.

### **Modify the script**

Open the PowerShell file **Deploy-Application.ps1** in your PSAppDeployToolkit (PSADT) folder. Use an editor like VS Code or Notepad++ to edit the file. Define the package details. As you can see in the screenshot, you can enter every variable, but you do not have to declare every variable if you do not know all variables.

![Image](https://s3.amazonaws.com/cdn.freshdesk.com/data/helpdesk/attachments/production/153008899728/original/WKRVJdrMmdHenrIulfKxq_mj51Ri2xKiCQ.jpg)

Scroll down a bit and locate the comment:

 `## <Perform Installation tasks here>`
 
Below that, insert the installation command:

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

If you go even further, there is the: 

`## <Perform Uninstallation tasks here>`

Below that, paste the uninstallation command:

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

