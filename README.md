# omnis-pkg
This is a template for building .pkg installers for Omnis Studio-based projects on macOS.

# Prerequisites
* Your [Omnis Studio](http://www.omnis.net)-based application
* [Packages](http://s.sudre.free.fr/Software/Packages/about.html)
* A [Developer ID](https://developer.apple.com/library/content/documentation/IDEs/Conceptual/AppDistributionGuide/DistributingApplicationsOutside/DistributingApplicationsOutside.html) code-signing certificate from Apple (required for code signing)

# Compatibility
This example is build against Omnis Studio 8.0.2 but should work with any recent version of Omnis Studio.

# Customizing the template
Prepare a copy of your application in a folder other than `/Applications`. The installer will be built from this copy.

Download or clone a copy of this repository. For example, this command will clone a copy of the project to a `omnis-pkg` folder in `~/Documents`.
```bash
git clone git@github.com:barkingfoodog/omnis-pkg.git ~/Documents/omnis-pkg
```

Rename the `Omnis.pkgproj` file to match your application. Open this file with Packages and edit these settings:
 1. **Project** -> **Settings** -> **Name**
 1. **Project** -> **Presentation** -> **Title** -> **Distribution Title**
 1. **Packages** -> Right-click **Omnis** -> **Rename** -> Enter your application name
 1. **Packages** -> **[Your Package]** -> **Settings** -> **Tag** -> **Identifier** -> Enter `com.[your company].pkg.[your application]`

Replace the placeholder Omnis Studio Runtime with your application.
 1. Go to **Packages** -> **[Your Package]** -> **Payload** -> **Contents** -> **Applications**
 1. Right-click **Omnis Studio Runtime 8.0.2 x64.app** and Choose **Delete...**
 1. Confirm the delete
 1. Right-click **Applications** and choose **Add Files...**
 1. Select your source application

You can store the absolute path to your application or make it relative to the Packages files or its containing folder.

Change the permissions on the app to:
 |        | Read | Write | Exec |
 | ------ | ---- | ----- | ---- |
 | Owner  | X    | X     | X    |
 | Group  | X    |       | X    |
 | Others | X    |       | X    |
Leave the Bit unset for all.

Edit the `postinstall` script and set `APPLICATION_ROOT` to the path to your application. This will allow the script to set permissions on your app properly. 

# Optional customizations

## preinstall script
You can edit the `preinstall` script to ensure your app is shutdown or make any other pre-requisite checks.

## postinstall script
You can extend this script to make changes to the system after your app is installed. This script will be invoked with root privileges via sudo.

## License
Edit the `license.rtf` to add your own license agreement that will be displayed during installation.

## Excluded files
In Packages, edit the **Project** -> **Settings** -> **Exclusions** list to exclude any files in your source application from the installer. For example, if you manage your installer in Subversion or git, exclude the `.svn` or `.git` folders.

## Installer steps
You can add an introduction, read me, or other steps to the install process under **Project** -> **Presentation**. Review the [Packages resources](http://s.sudre.free.fr/Software/Packages/resources.html) and linked sites for more information.

## System Requirements
You can set minimum system requirements for running the installer under **Project** -> **Requirements & Resouces**. Double-click a requirement to edit it, and use the + and - buttons to add and remove additional requirements. This template is set to require:
* 2 GB RAM
* 1 64-bit Intel core
* macOS 10.9 and later

# Building a package
## GUI
Open your .pkgproj file with Packages. Go to **Build** -> **Build**.

## CLI
```bash
/usr/local/bin/packagesbuild -v "[path to your .pkgproj]"
```

# Code signing
Install your Developer ID private key and certificate on the build machine. Code signing takes an un-signed package file and creates a signed package file. If your package is `omnis.pkg`, rename it to `omnis_temp.pkg`.

```bash
# If you're automating this process, run this command to unlock the keychain so the Developer ID certificate can be accessed
security unlock-keychain -p "[your macOS user password]"

# Sign your temporary package with your Developer ID Installer certificate
productsign --sign "Developer ID Installer: [Your company name]" "[Your package]_temp.pkg" "[Your package].pkg"

# Verify the signature
spctl -a -v --type install "[Your packages].pkg"
```

# Contributions
Pull requests are most welcome!