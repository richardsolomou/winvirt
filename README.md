WinVirt - Windows Dynamic Virtual Hosting
=========================================

Inspired by  [Dynamic Virtual Hosting](https://github.com/ear1grey/dyvirt) by @ear1grey.

Dynamic Virtual Hosting on OS X is a breeze to set up, whether you are using MAMP or XAMPP, as long as you use dnsmasq right.
However, there's little to no solutions available on the web for having the same functionality on Windows, unless you are using some dirty hacks.

This is my solution to this problem using Apache Virtual Hosting and DNS Management.

Apache Virtual Hosting
----------------------
On Windows, go to the main directory of your XAMPP installation and go to `apache/conf`. Open up `httpd.conf`, and uncomment these two lines:

    Include "conf/extra/httpd-vhosts.conf"
    LoadModule vhost_alias_module modules/mod_vhost_alias.so
  
Next, navigate to `apache/conf/extra`, proceed with commenting out or deleting the current content or replacing it with the below:

    <VirtualHost *:80>
       ServerName dev
       UseCanonicalName Off
       VirtualDocumentRoot "/full/path/to/htdocs/%-2+"
    </VirtualHost>

This will make Apache Virtual Hosting possible. You'll notice I used a `VirtualHost` block and a `ServerName` when I could simply just state the `UseCanonicalName` and `VirtualDocumentRoot`. The reason for this is that once this is done, `localhost` stops working. To get full functionality of `http://localhost/` and `http://127.0.0.1` after this, add these lines:

    <VirtualHost *:80>
      ServerName localhost
      VirtualDocumentRoot none
      DocumentRoot "/full/path/to/htdocs"
    </VirtualHost>
    
    <VirtualHost *:80>
      ServerName 127.0.0.1
      VirtualDocumentRoot none
      DocumentRoot "/full/path/to/htdocs"
    </VirtualHost>

DNS
---
While you could easily go to the `hosts` file located in `/windows/system32/drivers/etc/` and add individual records for each project you want to use, there's an easier, more universal way.

First off, download [Acrylic DNS Proxy](http://sourceforge.net/projects/acrylic/) and after the installation is finished, go to `Start > All Programs > Acrylic DNS Proxy > Config > Edit Custom Hosts File` and add the following line:

    127.0.0.1 *.dev

Next, restart the DNS service by going to `Start > All Programs > Acrylic DNS Proxy > Config > Restart Acrylic Service`.
Open up the Control Panel and proceed to `Network and Internet` and click on Network Connections. In Windows 8, this is called the Network and Sharing Center. Right click on your current network adapter and click on Properties, select TCP/IPv4 and click Properties again. Finally choose to use the `127.0.0.1` IP number as your DNS server address.

If everything was configured and setup correctly, you should now be able to use the folder names from your `htdocs` folder with the `.dev` extension in your browser to resolve to the localhost and display the files in each respective folder.
