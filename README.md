<p align="center">
<img src="https://i.imgur.com/Clzj7Xs.png" alt="osTicket logo"/>
</p>

# osTicket - Installing a Modern Ticketing System from Scratch
This tutorial outlines the prerequisites and installation of the open-source help desk ticketing system osTicket.

*Documentation written assuming Windows 11 as base Operarting System*

<h2>Video Demonstration</h2>

- ### [YouTube: How To Install osTicket with Prerequisites](https://www.youtube.com)

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Internet Information Services (IIS)

## Operating Systems Used

- Windows 11 Pro (25H2)

## Installation Steps

1. [Create a Virtual Machine (VM)](https://github.com/davidstavin/osticket-prereqs/blob/main/README.md#step-1-create-a-virtual-machine)
2. [Download osTicekt Files & Dependencies](https://github.com/davidstavin/osticket-prereqs/blob/main/README.md#step-2-download-osticket-files--dependencies)
3. [Enable IIS with CGI in Windows](https://github.com/davidstavin/osticket-prereqs/tree/main?tab=readme-ov-file#step-3-enable-iis-with-cgi)
4. [Install the Dependencies](https://github.com/davidstavin/osticket-prereqs/tree/main?tab=readme-ov-file#step-4-install-the-dependencies)
5. [Register PHP within IIS](https://github.com/davidstavin/osticket-prereqs/tree/main#step-5-register-php-within-iis)
6. [Install osTicket](https://github.com/davidstavin/osticket-prereqs/tree/main#step-6-install-osticket)
7. [Setup osTicket in the WebUI](https://github.com/davidstavin/osticket-prereqs/tree/main#step-7-setup-osticket-in-the-webui)
8. [Install HeidiSQL](https://github.com/davidstavin/osticket-prereqs/tree/main#step-8-install-heidisql)  

## Step 1: Create a Virtual Machine

Start by creating a VM in MS Azure
<img width="7680" height="4141" alt="osTicket_vm-creation" src="https://github.com/user-attachments/assets/40d9e748-afe8-4023-8b80-d9b384fdbb2a" />

Copy the Public IP of the VM
<img width="7680" height="1698" alt="osTicket_grab-ip" src="https://github.com/user-attachments/assets/679a8073-1a19-479e-adf5-b8c81ffcfe0a" />


## Step 2: Download osTicket Files & Dependencies
After completing step 1, and signing into the VM using Remote Desktop, proceed with downloading the neccessary dependencies for osTicket
The following urls are the dependencies, listed in the order we will be installing them. Please download them to your computer.

### Dependencies

[PHP](https://www.php.net/downloads.php)
- General Purpose FOSS Scripting Language for web-development. Allows for the creation of interactive websites

[PHP Manager for IIS](https://www.iis.net/downloads/community/2018/05/php-manager-150-for-iis-10) (If the download url is broken, download from [Github](https://github.com/RonaldCarter/PHPManager/releases)
- Useful tool for managing PHP Installations (e.g., registration, settings)

[IIS URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite)
- Allows adminstrators to create URL rules such as redirects, HTTP requests, and URL simplification for search-engine indexing and memorizability

[Visual C++ Reistributable](https://learn.microsoft.com/en-us/cpp/windows/latest-supported-vc-redist?view=msvc-170#latest-supported-redistributable-version)
- Reequired package to run applications developed with Visual C++

[MySQL Server (Community Edition)](https://dev.mysql.com/downloads/mysql/) (if you building for production: [MySQL Server (Enterprise Edition)](https://www.mysql.com/downloads/)
- Leading Database for Web Applications. Uses a relational database to organize, structure, and manage data. (e.g., storing and retrievingh)

[osTicket](https://osticket.com/download/)
- WebUI-based Helpdesk Ticketing Application, common in customer service environments for issue tracking and resolution

[HeidiSQL](https://www.heidisql.com/download.php)
- FOSS administration tool for working on databases like MaraDB and MySQL. Provides a GUI and is extremely popular

Your downloads folder should appear as such
<img width="2103" height="1193" alt="osTicket_downloads-fixed" src="https://github.com/user-attachments/assets/6a6a3aa0-c61e-427b-b4d3-1282708db900" />

## Step 3: Enable IIS with CGI

Within the VM, Press `Windows + R` then type `optionalfeatures` and hit enter. Make sure that the task is run with adminstrator privleges as depicted. 

*Alternatively*, you can search for `Turn Windows features on or off` in the start menu, right-click and press "Run as Adminstrator"
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



## Step 7: Setup osTicket in the WebUI



## Step 8: Install HeidiSQL
