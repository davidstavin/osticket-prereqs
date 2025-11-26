<p align="center">
<img src="https://i.imgur.com/Clzj7Xs.png" alt="osTicket logo"/>
</p>

<h1>osTicket - Installing a Modern Ticketing System from Scratch</h1>
This tutorial outlines the prerequisites and installation of the open-source help desk ticketing system osTicket.<br />


<h2>Video Demonstration</h2>

- ### [YouTube: How To Install osTicket with Prerequisites](https://www.youtube.com)

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Internet Information Services (IIS)

<h2>Operating Systems Used </h2>

- Windows 11 Pro (25H2)</b>

## Installation Steps

1. Create a Virtual Machine (VM)
2. Download osTicekt Files & Dependencies
3. Enable IIS with CGI in Windows
4. Install the Dependencies
5. Register PHP
6. Install osTicket
7. Setup osTicket from a webUI
8. Install HeidiSQL  

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
