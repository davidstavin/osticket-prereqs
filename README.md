<p align="center">
<img src="https://i.imgur.com/Clzj7Xs.png" alt="osTicket logo"/>
</p>

# osTicket - Installing a Modern Ticketing System from Scratch
This tutorial outlines the prerequisites and installation of the open-source help desk ticketing system osTicket.

...osTicket is a ...... placeholder summary

## Environments and Technologies Used

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Internet Information Services (IIS)

## Operating Systems Used

- Remote Computer: Windows 11 Pro (25H2)
- Host Computer: Windows 11 Home (25H2)
*Documentation written assuming Windows 11 as host Operarting System*

## Installation Steps

1. [Create a Virtual Machine (VM)](https://github.com/davidstavin/osticket-prereqs/blob/main/README.md#step-1-create-a-virtual-machine)
2. [Download osTicekt Files & Dependencies](https://github.com/davidstavin/osticket-prereqs/blob/main/README.md#step-2-download-osticket-files--dependencies)
3. [Enable IIS with CGI in Windows](https://github.com/davidstavin/osticket-prereqs/tree/main?tab=readme-ov-file#step-3-enable-iis-with-cgi)
4. [Install the Dependencies](https://github.com/davidstavin/osticket-prereqs/tree/main?tab=readme-ov-file#step-4-install-the-dependencies)
5. [Register PHP within IIS](https://github.com/davidstavin/osticket-prereqs/tree/main#step-5-register-php-within-iis)
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
After completing step 1, and signing into the VM using Remote Desktop, proceed with downloading the neccessary dependencies for osTicket
The following urls are the dependencies, listed in the order we will be installing them. Please download them to the remote computer.

_Note._ Windows 11 uses a x64 architecture, and is not compatabile with x86 (32-bit) software. Thus all the dependencies should be in the x64 format.

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
<img width="2103" height="1193" alt="osTicket_downloads-fixed" src="https://github.com/user-attachments/assets/6a6a3aa0-c61e-427b-b4d3-1282708db900" />

## Step 3: Enable IIS with CGI
Before installing the dependencies IIS for Windows needs to be enabled.

Within the Remote VM, Press `Windows + R` then type `optionalfeatures`, ensure "run with adminstrator privleges" is depicted and hit `Enter` (Use `Ctrl + Shift + Enter` if not depicted)

*Alternatively*, you can search for `Turn Windows features on or off` in the Windows start menu, right-click and press "Run as Adminstrator"
<img width="771" height="484" alt="osTicket-WinR" src="https://github.com/user-attachments/assets/ec64a789-99a8-4076-9394-4297e914032f" />

Within the next Window scroll down until you find "Internet Information Services" andd enable it by checking the box.
<img width="908" height="794" alt="osTicket_IIS-on" src="https://github.com/user-attachments/assets/910da700-b45f-4f61-830f-f12bb23a53c5" />

Next, we will enable CGI. Click the `+` symbol adjacent to the checkbox to expand the folder's options. In the same manner, expand World Wide Services, and then Application Development Features. Scroll down until you find CGI and enable it.
<img width="899" height="780" alt="osTicket_CGI-on" src="https://github.com/user-attachments/assets/88e45827-9d48-4380-9312-7e806aef09a7" />\

Once completed, Click `OK` and wait for the features to be applied.

<img width="1227" height="989" alt="osTicket-applying-changes" src="https://github.com/user-attachments/assets/6aedd442-e781-4975-9af2-c48e65188a73" />

Close the Window
<img width="1217" height="993" alt="osTicket-changes-applied" src="https://github.com/user-attachments/assets/d629a96f-c124-462b-9c57-20f510556b18" />

## Step 4: Install the Dependencies

### i. Install PHP Manager for IIS

Return to the downloads folder. Open the installer for PHP Manager, as depicted and follow the prompts to install the package.
<img width="2103" height="1193" alt="osTicket_downloads-fixed-PHPManager" src="https://github.com/user-attachments/assets/4cbdbe0a-1335-4cb2-8723-d41b64604a05" />
<img width="1002" height="814" alt="osTicket-PHP-ManagerForIIS" src="https://github.com/user-attachments/assets/8bc2468c-cada-4326-a464-901c8e01142e" />

### ii. Install IIS URL Rewrite
Install Rewrite
<img width="2103" height="1193" alt="osTicket_downloads-fixed-Rewrite" src="https://github.com/user-attachments/assets/a7b4f375-9cd8-446e-8457-533a45949f55" />
<img width="992" height="768" alt="osTicket-Rewrite" src="https://github.com/user-attachments/assets/4051d2b9-eb0c-429c-b515-6f2143c66345" />

### iii. Extract PHP files to C:\PHP
Extract the `.zip` containing the PHP files, specifiying `C:\PHP` as the extract path
<img width="2164" height="1528" alt="osTicket-ExtractPHP-annotated" src="https://github.com/user-attachments/assets/b2eef2fd-00a1-410f-92dd-fecfd95bb682" />

Extracting the files this way, will automatically create a new folder at the destination path
<img width="1146" height="942" alt="osTicket-PHPExtactDirectory" src="https://github.com/user-attachments/assets/4ad3f907-74cc-4234-ad93-c7af095701e8" />

### iv. Install Microsoft Visual Studio C++ Redistributable
Open vcRedist. Follow the prompts to install the package.
<img width="941" height="589" alt="osTicket-vcRedist-Install" src="https://github.com/user-attachments/assets/654f555c-6010-451d-9d64-0a16640d29d7" />

### v. Install MySQL
after installing vcRedist, install the MySQL server
<img width="993" height="771" alt="osTicket-mySQL-install" src="https://github.com/user-attachments/assets/047abb19-8c5d-4435-bd68-14ac1a757f45" />

select 'typical'
<img width="984" height="771" alt="osTicket-mySQL-Typical" src="https://github.com/user-attachments/assets/af7339e6-31ca-4063-9c41-0dca49ebb72d" />

open the launch configurator after installation
<img width="993" height="773" alt="mySQL-Launch-configurator" src="https://github.com/user-attachments/assets/fc9a5c0c-ff08-43c8-b7fe-45d1526b7707" />

within the configurator set your root password
<img width="1570" height="1229" alt="mySQL-set root password" src="https://github.com/user-attachments/assets/9cd82fad-4cab-481f-bfa9-31b719d471a3" />

after selecting the options suited for your use case, click next and allow the progrom to apply its changes.
<img width="1568" height="1229" alt="mySQL config complete" src="https://github.com/user-attachments/assets/e54b818b-b773-4346-81af-38886179efb0" />

## Step 5: Register PHP within IIS
Now we are going to register the PHP directory within our IIS Manager

Press `Windows + R`, type `inetmgr` and hit enter
<img width="768" height="479" alt="Win+R IIS-Manager" src="https://github.com/user-attachments/assets/d7950929-77df-4b6b-854b-2e25e2614ce3" />

within the new window, open PHP Manager
<img width="2614" height="1649" alt="iismanager-opened-annotated" src="https://github.com/user-attachments/assets/cfbdc66a-5f73-4858-8974-647b947bf05e" />

Click "Register New PHP Version"
<img width="2619" height="1647" alt="register-new-php version-anno" src="https://github.com/user-attachments/assets/793f4316-6855-4773-92e4-93f1c579c64b" />

Type `C:\PHP\php-cgi.exe` or navigate to it manually by clicking browse
<img width="1023" height="446" alt="phpversiondirectory" src="https://github.com/user-attachments/assets/0d69a1f0-f495-4ecd-9a40-b730d7162683" />

(The exe file depicted through 'browse')
<img width="2365" height="1424" alt="phpversionnav" src="https://github.com/user-attachments/assets/622b3685-47af-4e6e-9018-1d81320a5764" />

Click OK, and you will be returned to the PHP Manager which should have the options "change php version" now interactable
<img width="2614" height="1644" alt="php-registered" src="https://github.com/user-attachments/assets/eaa28ce8-dcc2-445a-900b-407be7a7e3c4" />

Next to apply these changes, hit STOP on the action side panel.
<img width="2611" height="1644" alt="click-stop-anno" src="https://github.com/user-attachments/assets/a79a40df-0a35-4910-9469-8a0d7d4d8247" />

Followed by START, once complete.
<img width="2615" height="1718" alt="click-start-anno" src="https://github.com/user-attachments/assets/f8a19bf3-8747-4fdc-aa97-f5271b5f0e67" />

## Step 6: Install osTicket

To install osTicket extract the .zip containing the files.

Once extracted, move the `Upload` folder by cutting it `CTRL + X` or using the GUI
<img width="2361" height="1380" alt="cut-upload-anno" src="https://github.com/user-attachments/assets/7906f96d-cfa6-469e-8f02-20ff84ce1aca" />

Navigate to your C:/ drive, and open the `inetpub` folder
<img width="2349" height="1375" alt="c-driectory-anno" src="https://github.com/user-attachments/assets/c3e56543-3013-4ddf-93cc-9077382b13a4" />

Open the `wwwroot` folder and paste the `Upload` folder `CTRL + V` or using the GUI
<img width="2360" height="1373" alt="paste-osticket-anno" src="https://github.com/user-attachments/assets/50a127e7-3992-4cb6-a479-ebb355a76579" />

Now rename the `Upload` folder to `osTicket`
<img width="2360" height="1367" alt="rename file-anno" src="https://github.com/user-attachments/assets/7c468902-39d6-4518-b4bf-90a4a9fdabfb" />
<img width="2351" height="1371" alt="file renamed-anno" src="https://github.com/user-attachments/assets/23d20620-adbf-4d7f-ba62-635397912a2c" />

## Step 7: Setup osTicket WebUI and HeidiSQL

In the VMs web browser, enter `http://localhost/osTIcket/setup/` to the url field and you should be redirected to the osTicket setup page. This confirms that osTicket is installed and working.
<img width="2514" height="1981" alt="osTicket page (after stop-starting IIS)" src="https://github.com/user-attachments/assets/69b1ea9e-44a7-4f7a-92eb-2376f93dcbf3" />

Enable the extensions, by opening IIS manager, from Step 3

Within PHP Manager click "enable or disable extensions"
<img width="2617" height="1713" alt="php-manager-extensions-anno" src="https://github.com/user-attachments/assets/17422319-43e4-4496-8199-fab69c428f04" />

Find and select an extension called `php_intl.dll`, and enable it using the action menu in the upper right.

_Note_. As of PHP 8.4 the imap extension is deprecated and no longer required to run osTicket (As referenced [here](https://www.php.net/manual/en/imap.installation.php) and [here)](https://forum.osticket.com/d/106559-php-84-drops-built-in-php-imap-support)
<img width="2611" height="1711" alt="phpmanager - enable phpintl-anno" src="https://github.com/user-attachments/assets/8ca739e3-3aa1-4325-939e-11c71adda50a" />

refreshing the osTicket website should now redirect you to the "missing config file" screen
<img width="2500" height="1805" alt="missing config" src="https://github.com/user-attachments/assets/20b5797c-846c-4a3c-9eda-5e7c749df189" />

To fix this navigate to `C:\inetpub\wwwroot\osTicket\include` and rename a file called `ost-sampleconfig.php` to `ost-config.php`
<img width="2484" height="1791" alt="rename ost-sample-config-anno" src="https://github.com/user-attachments/assets/2192148c-44b5-4782-bac8-eeb79e21ba0e" />
As depicted here
<img width="1357" height="51" alt="ost-configphp renamed" src="https://github.com/user-attachments/assets/9632244c-b9ef-4aa2-9a22-1e8285891195" />

Next open the config file's properties `Alt + Enter` or by using the GUI
<img width="1706" height="1003" alt="open properites-anno" src="https://github.com/user-attachments/assets/e2c1869d-9729-4cf3-bef6-72e2114d78db" />

Open "Security" 
<img width="818" height="994" alt="open security and advanced-anno" src="https://github.com/user-attachments/assets/97d8c888-6673-4960-91de-2a61cf037fd8" />

disable inheritance + all
<img width="1536" height="1004" alt="disable all inherited permissions-anno" src="https://github.com/user-attachments/assets/e4ab2ed3-a5f0-4abf-8482-98ce2fe5c5d0" />

add new permssion
<img width="2493" height="1158" alt="add permission and select principal-anno" src="https://github.com/user-attachments/assets/50081a8a-964b-4701-ad1e-6c51d8c3d219" />

Type `Everyone` select "check Names" to fix any syntax errors, and press `Okay`
_Note_. For this tutorial 'Everyone' will have adminsistrator privileges. This is unwise for production environments, andd the actual permissions will be dependent on your use case
<img width="2487" height="1153" alt="everyone, check names, okay-anno" src="https://github.com/user-attachments/assets/32c05f1a-a526-41f4-854b-02ff95ad1e26" />

enable full control, modify, and write. Then press okay
<img width="2493" height="1158" alt="enable fc, mod, wr-anno" src="https://github.com/user-attachments/assets/01ec9a0e-312d-43f8-9adf-d8a5f6d74a83" />

On the next screen press `Apply` then `Okay`
<img width="1535" height="1003" alt="apply and okay-anno" src="https://github.com/user-attachments/assets/6ceca7d6-1fb1-4adb-aceb-c2f03e7f8be9" />

The osticket page should now look like this upon refreshing
<img width="2507" height="1802" alt="new osTicket page" src="https://github.com/user-attachments/assets/99db8e53-1bd1-4e39-b883-beb3931c875c" />

Fill out most of the form but do not install, leave the database settings blank for now, those will be filled after the next step
<img width="2554" height="2559" alt="form" src="https://github.com/user-attachments/assets/65766943-3289-47c8-83a5-a84670a816d3" />

Install HeidiSQL. Select install for all users (again this is dependent on your use case, but generally this setting is unwise for production)
<img width="2485" height="1504" alt="install heidi-anno" src="https://github.com/user-attachments/assets/81dc6dc6-4b53-48e2-94b2-73a9012dc1dc" />

Follow the prompts to install the software.
<img width="1199" height="899" alt="Screenshot 2025-12-01 202912" src="https://github.com/user-attachments/assets/f1cbda35-1f4c-4d54-b2ba-fa88101bb330" />

Within HeidiSQL. Select "New" and then fill out the security info for the mySQL database you created earlier. Then click "Open"
<img width="1377" height="970" alt="fill heidi database settings-anno" src="https://github.com/user-attachments/assets/3819bd36-73b1-4388-94f4-357dc85ead01" />

In the new menu, right click on the unamed session. Click "create-new" then "database"
<img width="2183" height="1441" alt="create new database-anno" src="https://github.com/user-attachments/assets/ad5c300e-0c72-4f3c-9ff1-fc07b2b159fb" />

Name the database `osTicket`, then click `Okay`
<img width="646" height="523" alt="name the database" src="https://github.com/user-attachments/assets/4f3ede54-741c-4c37-8c82-7a6fe190718f" />

Return to the osTicket form in the webUI. And fill in the database settings, with the info from heidisql and mysql, then click `Install Now`
<img width="1625" height="841" alt="osTicket fill out database settings-anno" src="https://github.com/user-attachments/assets/ff0344b8-f4d7-4e4e-9d2e-b8badebbdc23" />

the page will begin running the install
<img width="1646" height="2328" alt="osTicket doing things" src="https://github.com/user-attachments/assets/80a72737-0e4f-4b74-bc92-dd1f25711924" />

## Step 8: Post-Install Cleanup

After the install is complete, the page will redirect to this confirmation screen, and instructions to cleanup config settings
<img width="2545" height="1660" alt="task to delete things" src="https://github.com/user-attachments/assets/f59cfb7e-e79f-480e-9293-8b52c6a439bd" />

delete the setup folder...
<img width="2792" height="1738" alt="delete the setup folder-anno" src="https://github.com/user-attachments/assets/0ae493a4-a976-4d1f-a06b-e87a0d7f618e" />

find the ost-config file from earlier
img...

change permsissions...
<img width="2219" height="1260" alt="new permissions-anno" src="https://github.com/user-attachments/assets/d87b9c89-beb2-4b64-86ed-68cf6e397e28" />

Confirm the agent and end-user panels both work.
<img width="2548" height="937" alt="osTicket install confirmed ticket-anno" src="https://github.com/user-attachments/assets/b14c4117-1cdb-4671-91c4-9c868d58ac48" />

