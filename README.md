<p align="center">
<img src="https://i.imgur.com/Clzj7Xs.png" alt="osTicket logo"/>
</p>

# osTicket - Installation and Deployment on Windows 11 using Azure, IIS, PHP, and MySQL
osTicket is a free, open-source, enterprise-grade helpdesk ticket system built with PHP, that enables organizations to manage, track, and resolve support requests. It features a user-friendly web interface for both users and agents, automated reponses, filter-based ticket routing, agent collision avoidance, advanced SLA management, role-based permissions, thread actions, detailed ticket histories, and built-in analytics for performance tracking and reporting.

This repository provides a step-by-step guide for installing and deploying osTicket on a fresh Windows 11 system. The walkthrough covers the full process: from deploying an Azure Virtual Machine (VM), to installing dependencies (e.g., URL Rewrite, MySQL), enabling Internet Information Services (IIS) and the Common Gateway Interface (CGI), configuring PHP and required extensions, creating a MySQL database using HeidiSQL and finally deploying osTicket.

## Environments and Technologies Used
- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Internet Information Services (IIS)
- MySQL Server
- PHP

## Operating Systems Used
*Documentation written assuming Windows 11 as host Operarting System*
- Remote Computer: Windows 11 Pro (25H2)
- Host Computer: Windows 11 Home (25H2)

## Installation Steps

1. [Create a Virtual Machine (VM)](https://github.com/davidstavin/osticket-prereqs/blob/main/README.md#step-1-create-a-virtual-machine)
2. [Download osTicket Files & Dependencies](https://github.com/davidstavin/osticket-prereqs/blob/main/README.md#step-2-download-osticket-files--dependencies)
3. [Enable IIS with CGI in Windows](https://github.com/davidstavin/osticket-prereqs/tree/main?tab=readme-ov-file#step-3-enable-iis-with-cgi)
4. [Install the Dependencies](https://github.com/davidstavin/osticket-prereqs/tree/main?tab=readme-ov-file#step-4-install-the-dependencies)
5. [Register PHP](https://github.com/davidstavin/osticket-prereqs/tree/main#step-5-register-php-within-iis)
6. [Install osTicket](https://github.com/davidstavin/osticket-prereqs/tree/main#step-6-install-osticket)
7. [Setup osTicket WebUI and HeidiSQL](https://github.com/davidstavin/osticket-prereqs/tree/main#step-7-setup-osticket-webui-and-heidisql)
8. [Post-Install Cleanup](https://github.com/davidstavin/osticket-prereqs/tree/main#step-8-post-install-cleanup)


## Step 1: Create a Virtual Machine


Start by creating a VM in MS Azure with at least 4 vCPUs. Below is the summary of the VM used in this tutorial. (NOTE UPDATE TO 4 vCPU image)
<img width="7680" height="4141" alt="osTicket_vm-creation" src="https://github.com/user-attachments/assets/40d9e748-afe8-4023-8b80-d9b384fdbb2a" />

After deployment, navigate to the VM dashboard within Azure and copy the Public IP of the new VM `CTRL + C`
<img width="7680" height="1698" alt="osTicket_grab-ip" src="https://github.com/user-attachments/assets/679a8073-1a19-479e-adf5-b8c81ffcfe0a" />

Using the Windows Start Menu search for `Remote Desktop Connection` or `RDC` and paste the VM's IP Address `CTRL + V`
<img width="805" height="472" alt="osTicket-rdc-anno" src="https://github.com/user-attachments/assets/c17ca01c-38be-48fb-bab9-794995849124" />

Then sign into the VM using the security credentials assigned during its creation

img...


## Step 2: Download osTicket Files & Dependencies


After completing Step 1, and signing into the VM via Remote Desktop, the next step is to download the osTicket files and dependencies to the remote VM.
The URLs below list the dependencies in the order they will be installed. Please download them, but do not install them at this moment, installation will be done in Step 4.

_Note._ Windows 11 uses a x64 architecture, and does not support x86 (32-bit) software. Thus the downloaded dependencies should be in the x64 format.

### Dependencies

[PHP](https://www.php.net/downloads.php)
- General Purpose FOSS Scripting Language for web-development. Allows for the creation of interactive websites

[PHP Manager for IIS](https://www.iis.net/downloads/community/2018/05/php-manager-150-for-iis-10) (If the download url is broken, download from [Github](https://github.com/RonaldCarter/PHPManager/releases)
- Useful tool for managing PHP Installations, such as settings, extensions, and PHP registration

[IIS URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite)
- Allows adminstrators to create URL rules such as redirects, HTTP requests, and URL simplification for search-engine indexing and memorizability

[Visual C++ Reistributable](https://learn.microsoft.com/en-us/cpp/windows/latest-supported-vc-redist?view=msvc-170#latest-supported-redistributable-version)
- Required package to run applications developed with Visual C++

[MySQL Server (Community Edition)](https://dev.mysql.com/downloads/mysql/) (if you building for production: [MySQL Server (Enterprise Edition)](https://www.mysql.com/downloads/)
- Leading Database for Web Applications. Uses a relational database to organize, structure, and manage data. (e.g., storing and retrieving)

[osTicket](https://osticket.com/download/)
- WebUI-based Helpdesk Ticketing Application, common in customer service environments for issue tracking and resolution

[HeidiSQL](https://www.heidisql.com/download.php)
- FOSS administration tool for working on databases like MariaDB and MySQL. Provides a GUI and is extremely popular

Below is an image of the downloads folder containing all the (x64) dependencies used in this tutorial.
<img width="2100" height="1187" alt="osTicket_downloads-fixed-template" src="https://github.com/user-attachments/assets/b8d1bc17-b572-4b11-8dbe-bb1546d62c3a" />


## Step 3: Enable IIS with CGI


Before proceeding with dependency installation, IIS for Windows needs to be enabled. This will allow osTicket to run as a Webserver.

Within the Remote VM, Press `Windows + R` then type `optionalfeatures`. and hit `Ctrl + Shift + Enter` to run the program with Adminstrator Privileges.
*Alternatively*, use the Windows Start Menu, search for `Turn Windows features on or off`, right-click and press "Run as Adminstrator"

<img width="764" height="476" alt="osTicket-WinR" src="https://github.com/user-attachments/assets/ed28c96c-d235-4281-a655-691c61eb5f60" /> &nbsp;

In the new "Windows Features" screen scroll down until you find "Internet Information Services" (IIS) and enable it by checking the box as depicted.

<img width="903" height="784" alt="osTicket_IIS-on-anno" src="https://github.com/user-attachments/assets/1339a204-d7ee-47a5-976f-f5c49f23b189" /> &nbsp;

Next, to enable CGI. Click the `+` symbol adjacent the IIS checkbox, to expand its nested options. Then find and expand "World Wide Services", and then "Application Development Features". Scroll down until you find "CGI" and enable it.
<img width="899" height="780" alt="osTicket_CGI-on-anno" src="https://github.com/user-attachments/assets/3fcba6bd-73ff-4aa0-8cfe-df461a86c032" /> &nbsp;

After clicking `OK`, wait for the features to be applied, then close the window
<img width="1217" height="457" alt="osTicket-changes-applied-cropped" src="https://github.com/user-attachments/assets/9dcbff20-2f55-4256-8f1d-ef2758204afb" />


## Step 4: Install the Dependencies


After enabling the IIS Webserver and CGI via Windows Features, return to the downloads folder containing the dependencies obtained during Step 2

### i. Install PHP Manager for IIS
Open the installer for PHP Manager `PHPManagerforIIS_V1.5.0.msi` and follow the prompts to install the program.
<img width="2101" height="1185" alt="osTicket_install_phpmanager-anno_num" src="https://github.com/user-attachments/assets/0f6afff3-34eb-4c70-b616-7e87de5f5cbf" />

### ii. Install IIS URL Rewrite
Next Install IIS URL Rewrite `rewrite_amd64_en-US.msi`, accept the license agreement and follow the installation prompts once again.
<img width="2101" height="1185" alt="osTicket_install_URL-Rewrite-anno_num" src="https://github.com/user-attachments/assets/26c2ae08-5819-454b-a1e4-c4d33b8b5768" />

### iii. Extract PHP files to C:\PHP
For the next set of dependencies PHP will need to be installed.
Right-click and select `Extract All...` on the compressed PHP folder `php-8.5.0-nts-Win32-vs17-x64.zip`, 
<img width="2164" height="1528" alt="osTicket-ExtractPHP-annotated" src="https://github.com/user-attachments/assets/b2eef2fd-00a1-410f-92dd-fecfd95bb682" />

Specify `C:\PHP` as the extract path, to automatically create a new folder containing the extracted files. Then click "Extract"
<img width="1144" height="665" alt="osTicket-PHPExtractDirectory-cropped_anno" src="https://github.com/user-attachments/assets/97893165-284d-4f83-a639-b628a73b4923" />

### iv. Install Microsoft Visual Studio C++ Redistributable
After extracting PHP as described, return to the remaining dependencies. Find and install the Visual Studio C++ Redistributable `VC_redist.x64.exe`, Accept the Terms and Conditions, and follow the prompts to install the package.
<img width="2101" height="1185" alt="osTicket_install_vcRedist-anno_num" src="https://github.com/user-attachments/assets/240a22a1-e8bf-4ffc-a340-10739d1b877b" />

### v. Install MySQL
Next MySQL Server will need to be installed. Open `mysql-9.5.0-winx64.msi`, select "Next" and follow the prompts once again.
<img width="2101" height="1185" alt="osTicket_install_MySQL-anno_num" src="https://github.com/user-attachments/assets/c611c031-2324-4791-b585-ccc54b4e070e" />

When prompted to choose an installation setup type, choose "Typical" and select next.
<img width="984" height="766" alt="osTicket-mySQL-Typical-anno" src="https://github.com/user-attachments/assets/17b708f5-09bf-4789-8a4d-0271222c6b4c" />

Once installed, enable the option to Run the MySQL Configurator, as depicted and click finish.
<img width="986" height="766" alt="mySQL-Launch-configurator-anno" src="https://github.com/user-attachments/assets/db54be8a-0057-43e3-8692-031686904ed2" />

Within the configurator keep all the default options (or modify them according to your use case), and set a strong root password for the MySQL Database
<img width="1569" height="1226" alt="mySQL-set root password-anno" src="https://github.com/user-attachments/assets/7e49cd0a-7b29-404d-8557-6677ea20753e" />

After proceeding through all the configurator options, review and apply the configurations. Once applied successfully, click next to close the configurator.
<img width="1566" height="1224" alt="mySQL config complete-anno" src="https://github.com/user-attachments/assets/79056087-ead1-4ebb-aa27-b2b2ed2bd531" />

At this point you may have noticed that HeidiSQL has not been installed. This is fine, you will be directed to install it near the end of this tutorial.


## Step 5: Register PHP within IIS


In order to proceed any further, the PHP installation that was extracted earlier—into `C:\PHP`—will need to be registered within IIS Manager. This will inform the webserver that PHP is installed and ready to run PHP-based programs (i.e., osTicket). 
Specifically, the file for registration is `PHP-cgi.exe`, an executable responsible for processing PHP scripts. Without this executable, osTicket would fail to run on the IIS Server.

To register PHP, begin by pressing `Windows + R`, type `inetmgr` and hit `Ctrl + Shift + Enter` to run the program with Adminstrator Privileges.
*Alternatively*, use the Windows Start Menu to search for `IIS`, right-clicking and press "Run as Adminstrator"

<img width="764" height="475" alt="Win+R IIS-Manager" src="https://github.com/user-attachments/assets/aad9e055-29b1-41a0-ac3b-60fe5ca36310" />

Inside the now opened IIS Manager, find the widget for PHP Manager as depicted, and double-click to enter its configuration menu.
<img width="2604" height="1125" alt="iismanager-opened-annotated" src="https://github.com/user-attachments/assets/b20a1f99-73e0-4c5c-8244-e898d3cdca6c" />

Now, click "Register New PHP Version"
<img width="2604" height="1632" alt="register-new-php version-anno" src="https://github.com/user-attachments/assets/09293c77-8cd2-44d4-964c-67ad294e2edc" />

Type `C:\PHP\php-cgi.exe` as the file path, and press "Okay".
_Alternatively_, navigate to it manually by clicking `...` on the right-most side to browse to the executable using the GUI (See the next image).
<img width="1023" height="446" alt="phpversiondirectory" src="https://github.com/user-attachments/assets/0d69a1f0-f495-4ecd-9a40-b730d7162683" />

(The exe file depicted through 'browse')
<img width="2365" height="1419" alt="phpversionnav" src="https://github.com/user-attachments/assets/e14cffc4-2d61-4f65-aece-d8419cdbec57" />

After providing the executable's file path, and you will be returned to the PHP Manager which will no longer display the warning banner "PHP is not enabled", and the options for "Change PHP version" and "Check phpinfo" will be interactable.
<img width="2604" height="1632" alt="php-registered" src="https://github.com/user-attachments/assets/60bee006-523e-4624-8c27-0443fe140986" />

Next, to apply these changes, the webserver will need to be restarted. Return to the IIS Manager's dashboard by clicking the remote VM's name (in this case "osTicket-VM) under the "Connections" panel, and then hit `STOP` under the "Actions" panel.
<img width="2604" height="1101" alt="click-stop-anno" src="https://github.com/user-attachments/assets/ff153f88-ec56-4da1-830d-f71d72733d4c" />

Stopping the server may take a few seconds, but once completed, hit the `START` button, to relaunch the IIS webserver.
<img width="2604" height="1124" alt="click-start-anno" src="https://github.com/user-attachments/assets/37a4c284-ab57-49b2-8e3d-26d89bc8f7c1" />


## Step 6: Install osTicket


After registering PHP and restarting the webserver, the osTicket files downloaded from earlier need to be unpacked and "uploaded" (i.e., installed) to the server.

To install osTicket, begin by extracting the `.zip` containing the files, as described earlier. This time, however, the files can just be extracted to the same downloads folder as the other dependencies.
Once extracted, enter the osTicket directory and move the `Upload` folder by cutting it `CTRL + X` or using the GUI
<img width="2361" height="1380" alt="cut-upload-anno" src="https://github.com/user-attachments/assets/7906f96d-cfa6-469e-8f02-20ff84ce1aca" />

Next, navigate to your C:\ drive, and open the `inetpub` folder
<img width="2349" height="1375" alt="c-driectory-anno" src="https://github.com/user-attachments/assets/c3e56543-3013-4ddf-93cc-9077382b13a4" />

Then, open the `wwwroot` folder and paste the `Upload` folder `CTRL + V` or using the GUI.
<img width="2360" height="1373" alt="paste-osticket-anno" src="https://github.com/user-attachments/assets/50a127e7-3992-4cb6-a479-ebb355a76579" />

Now rename the `Upload` folder to `osTicket`. Although, technically a folder was moved into another directory within the same drive, this has effectively "uploaded" the osTicket setup files into the IIS webserver.
<img width="2360" height="1367" alt="rename file-anno" src="https://github.com/user-attachments/assets/7c468902-39d6-4518-b4bf-90a4a9fdabfb" />

Below is an image of the uploaded file renamed to osTicket
<img width="2351" height="1371" alt="file renamed-anno" src="https://github.com/user-attachments/assets/23d20620-adbf-4d7f-ba62-635397912a2c" />


## Step 7: Configure osTicket WebUI and HeidiSQL


With the dependencies now fully installed, and the osTicket files uploaded to the server, the next step is to configure the web interface and assign the database that osTicket will use. This will require configuring permissions, extensions and deploying the MySQL database.

First, verify that osticket is working. Within the VM, open a web browser and enter `http://localhost/osTicket/setup/` to the URL field. If working, you should be redirected to the setup page for osTicket (as seen below).
<img width="2506" height="1972" alt="osTicket page (after stop-starting IIS)" src="https://github.com/user-attachments/assets/a05c99f8-ebcf-4c4c-b311-5e2f5773f67a" />

Next, return to the PHP Manager within the Windows IIS Manager (as described in Step 3). &nbsp;
Once inside the PHP Manager, select "Enable or disable an extension" under the PHP Extensions header.
<img width="2604" height="1703" alt="php-manager-extensions-anno" src="https://github.com/user-attachments/assets/77c7fae6-4658-4103-a6b2-33557f4da4fb" />

Within the PHP extensions menu find and select an extension called `php_intl.dll` and then select "enable" under the Actions menu in the upper right.
This extension allows osTicket to support multiple languages and formats based on end-user locales (e.g., translation, transliteration)

_Note_. As of PHP 8.4 the imap extension is deprecated and no longer required to run osTicket (As referenced [here](https://www.php.net/manual/en/imap.installation.php) and [here)](https://forum.osticket.com/d/106559-php-84-drops-built-in-php-imap-support)
<img width="2604" height="1705" alt="phpmanager - enable phpintl-anno" src="https://github.com/user-attachments/assets/442a584d-b5e9-4526-a779-af658b0de3ed" />

With the extensions configured, refreshing the osTicket website should now redirect to another setup screen with a warning about a missing configuration file.
<img width="2494" height="1795" alt="missing config" src="https://github.com/user-attachments/assets/5a685436-cd98-4d3a-b49e-ea3c7699a539" />

To fix this warning, open your file explorer, and navigate to `C:\inetpub\wwwroot\osTicket\include` and rename the file called `ost-sampleconfig.php` to `ost-config.php`
<img width="2477" height="1784" alt="rename ost-sample-config-anno" src="https://github.com/user-attachments/assets/7be3039a-f236-412a-85b8-22eb7eca06d6" /> &nbsp;

Here is an image of the file appropriately renamed.
<img width="1357" height="51" alt="ost-configphp renamed" src="https://github.com/user-attachments/assets/9632244c-b9ef-4aa2-9a22-1e8285891195" />

Next, the security settings of the file must be reconfigured so osTicket can read and interact with it.
Select the file, and open the "Properties" menu by hitting `Alt + Enter` or by using the GUI as depicted.
<img width="1706" height="1003" alt="open properites-anno" src="https://github.com/user-attachments/assets/e2c1869d-9729-4cf3-bef6-72e2114d78db" />

Once opened, select the "Security" tab, followed by the "Advanced" option.
<img width="809" height="977" alt="open security and advanced-anno" src="https://github.com/user-attachments/assets/bc75e646-9668-4fe0-93d0-18af2574d2f9" />

In the new window, select "Disable Inheritance" and then "Remove all inherited permissions from this object" when prompted. 
This will remove all Read, Write, and Execute permissions previously attributed to the file

<img width="1530" height="997" alt="disable all inherited permissions-anno" src="https://github.com/user-attachments/assets/d7f9f00e-f01e-486b-aee9-cd45b14843f8" />

Once the previous permissions have been removed, Select "Add" to assign a new set of permissions, followed by "Select a Principal" in the next window.
<img width="2485" height="1147" alt="add permission and select principal-anno-back" src="https://github.com/user-attachments/assets/687ea54e-ade6-4111-85e4-0d4d875d0f24" />

In the new dialog, type `Everyone` in the object names textbox, then select `Check Names` to fix any syntax errors, and press `Okay`. This will assign literally **everyone** (i.e., users and admins) as the authorized entity for this file.

_Note_. Assigning permissions in this manner is fine for this tutorial, but is seriously unwise for production environments.
<img width="2484" height="1147" alt="everyone, check names, okay-anno-back" src="https://github.com/user-attachments/assets/a0447ce0-0a9f-449d-966a-64220bc718bc" />

Once the prinicpal has been configured, assign the permissions `Full Control`, `Modify`, and `Write` by filling the adjacent checkboxes, and selecting "Okay". Based on the security principal this will effectively grant adminstrator privileges to *everyone*
<img width="2485" height="1147" alt="enable fc, mod, wr-anno-back" src="https://github.com/user-attachments/assets/067185fc-11ca-4904-a8cd-ee99755f327c" />

With the permissions assigned, press `Apply` to load the new security settings, and then `Okay` to close the window.
<img width="1529" height="996" alt="apply and okay-anno" src="https://github.com/user-attachments/assets/cafd29e1-1a07-4a9b-b748-f3c69a138e12" />

Return to the browser and refresh the osTicket webpage. A new installer page with a first-time setup form, as seen below, should be displayed.
<img width="2502" height="1795" alt="new osTicket page" src="https://github.com/user-attachments/assets/4fae2405-8c0f-460e-9309-0a0fb380fd5a" />

Fill out most of the form but do not install, leave the database settings blank for now, those will be filled after the next step
<img width="2548" height="2549" alt="form" src="https://github.com/user-attachments/assets/77a91d44-3c76-489e-a5c3-a19d9b828188" />

To fill out the database information, HeidiSQL will need to be installed, to configure the MySQL database that osTicket will use.
In the downloads folder with the dependencies, Run the HeidiSQL installer and select `Install for all users`. Then follow the prompts to install the program.
<img width="2485" height="1504" alt="install heidi-comp-anno" src="https://github.com/user-attachments/assets/349b2e84-537a-49f1-994c-31160b2a2a9d" />

After installation, open HeidiSQL. Select `New` and fill out the security info for the MySQL database that was created earlier. Then click `Open`
<img width="1372" height="965" alt="fill heidi database settings-anno" src="https://github.com/user-attachments/assets/f94bbf03-79c8-4088-82f3-98e15b8144c2" />

In the new menu, right click on the unnamed session. Click `Create New` then `Database`
<img width="2176" height="1436" alt="create new database-anno" src="https://github.com/user-attachments/assets/86510c86-c9d8-4ca0-a26c-186e05ae344e" />

In the new dialog, name the database `osTicket`, then click `Okay`.

<img width="646" height="523" alt="name the database" src="https://github.com/user-attachments/assets/4f3ede54-741c-4c37-8c82-7a6fe190718f" /> &nbsp;

Return to the osTicket form in the webUI. And fill in the database settings, with the name of the database createdd in HeidiSQL (i.e., `osTicket`) and the MySQL security credentials to authorize the database connection, then click `Install Now`
<img width="1625" height="841" alt="osTicket fill out database settings-anno" src="https://github.com/user-attachments/assets/ff0344b8-f4d7-4e4e-9d2e-b8badebbdc23" />

The webserver will begin running the installation. When finished, osTicket should be successfully installed!
<img width="1646" height="2328" alt="osTicket doing things" src="https://github.com/user-attachments/assets/80a72737-0e4f-4b74-bc92-dd1f25711924" />


## Step 8: Post-Install Cleanup


After installation, the webpage will reload to confirm that osTicket is set up, and display instructions for hardening application security.
<img width="2545" height="1660" alt="task to delete things" src="https://github.com/user-attachments/assets/f59cfb7e-e79f-480e-9293-8b52c6a439bd" />

First, navigate to the  `C:\inetpub\wwwroot\osTicket` directory and delete the `setup` folder. This is a security best practice that reduces the available attack surface, and many organizations may mandate this for production environments.
<img width="2792" height="1738" alt="delete the setup folder-anno" src="https://github.com/user-attachments/assets/0ae493a4-a976-4d1f-a06b-e87a0d7f618e" />

Second, return to the `ost-config.php` file found in the `C:\inetpub\wwwroot\osTicket\include` directory. Reopen the security permissions tab as described in Step 7. Change the permissions to disable `Full Control`, `Modify`, and `Write`. 
This will remove the adminstrator privileges previously granted to *Everyone*.
<img width="2219" height="1260" alt="new permissions-anno" src="https://github.com/user-attachments/assets/d87b9c89-beb2-4b64-86ed-68cf6e397e28" />

With the cleanup actions taken, confirm the functionality of both the Agent Portal `http://localhost/osTicket/scp/login.php` and End User Support Center `http://localhost/osTicket/` pages by logging into the Agent Portal, and visting the Support Center

Image of the Tickets Dashboard within Agent Portal
<img width="2548" height="937" alt="osTicket install confirmed ticket-anno" src="https://github.com/user-attachments/assets/b14c4117-1cdb-4671-91c4-9c868d58ac48" />

Image of the End User Support Center
<img width="2549" height="1242" alt="user screen" src="https://github.com/user-attachments/assets/05645581-04ab-4bf3-ad11-53c46bf7311d" />

## Congratulations, osTicket is now fully installed and deployed!
