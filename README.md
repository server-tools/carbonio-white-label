Carbonio is pretty and modern but you’ll like to have it into your style, with a different background and your logo. Me too!

I’ll show you how to do it, juts follow the steps below.

This way you can have different wallpapers and logo for each domain hosted on your server.

ALERT: all this changes will not survive a version upgrade. So you’ll have to do it after every upgrade

Files specifications
==

Background wallpaper
--

Type: JPG
Dimensions: 1920×1080 pixels
Filename: wallpaper.jpg


Company Logo on Login screen
--

Type: PNG
Dimensions: 602×108 pixels
Filename: login.png
Background color: white


Company Logo inside webmail
--

Type: PNG
Dimensions: 602×108 pixels
Filename: inside_logo.png
Background color: transparent


Directories structure
==

This hack requires you to create a specific directory structure, like this:
```
/opt/zextras/web/logos/example.com/
/opt/zextras/web/logos/otherdomain.com/
```
Once each domain hosted on your server have it’s own directory copy it’s files into it

Be aware to make links of all virtualhosts you’re using to the domain on that structure. So let’s say you access it using webmail.example.com create a link for it. Like that:
```
cd /opt/zextras/web/logos/
ln -s example.com webmail.example.com
ln -s example.com mail.example.com
```
The very same applies to use multiple domains each with it’s own login and wallpaper images. Just create a folder with the name of each domain and then the links for each virtualhost. Like this:
```
cd /opt/zextras/web/logos/

mkdir domainflowers.com
ln -s domainflowers.com webmail.domainflowers.com

mkdir domaincars.com
ln -s domaincars.com webmail.domaincars.com
```
Logo and wallpaper images for each domain goes into respectively domain folder.

Customizing login screen
==

Make a backup
--
```
cp -a /opt/zextras/web/login /opt/zextras/web/login.orig
```
Finding the right file make the changes
Run the command below to figure which file has the images on it:
```
grep -l "8b90fe7b942c6f389f1ddd01103d3b0e.jpg" /opt/zextras/web/login/*.js
```
The result will be something like:
```
/opt/zextras/web/login/137.js
```
This is the file you need to use on the commands below, so fix it accordingly.

Fixing login javascript file
```
sed -i '2 i const multidomain = window.location.hostname.toString();' /opt/zextras/web/login/137.js
sed -i s@assets/8b90fe7b942c6f389f1ddd01103d3b0e.jpg@'../logos/"+multidomain+"/wallpaper.jpg'@g /opt/zextras/web/login/137.js
sed -i s@assets/a2ca34c391de073172d480fe7977954a.jpg@'../logos/"+multidomain+"/wallpaper.jpg'@g /opt/zextras/web/login/137.js
sed -i s@assets/c469e23959fd19cc40fbb5e56c083c86.png@'../logos/"+multidomain+"/login.png'@g /opt/zextras/web/login/137.js
```
Fixing in webmail javascript file
Carbonio webmail theme is called Iris and it has many parts. We want to fix a javascript file in there, but we don’t know which one because that “CARBONIO” logo is a svg code inside some file. Run the command below to figure which one needs to be fixed:
```
grep "M306.721 72.44c-2.884-5.599" /opt/zextras/web/iris/carbonio-shell-ui/* -rl
```
This will reveal two files: one “js.map” and one “.js“. We want the one that’s just “.js and it will have a name like “index.415ef93c.js”. This number in the file name and it’s path changes accordingly with Carbonio version. In this example I’ll use this file:

/opt/zextras/web/iris/carbonio-shell-ui/bbb2a6e88fd7f7507ae3a6dfad2c8be9c16651d8/752.348ccb23.chunk.js
Do not forget to make a copy of it, so you can restore it if needed.

Replacing “svg” entry by “img” entry
To fix that file to replace Carbonio logo by the one you are using on the login page run the command below, replacing the file name by yours.
```
sed -i '2 i const multidomain = window.location.hostname.toString();' /opt/zextras/web/iris/carbonio-shell-ui/bbb2a6e88fd7f7507ae3a6dfad2c8be9c16651d8/752.348ccb23.chunk.js
sed -i s@createElement\(\"svg\".*402-35.626\"@'createElement("img",(({src:"/static/logos/" + multidomain + "/inside_logo.png",height:"30"'@g /opt/zextras/web/iris/carbonio-shell-ui/bbb2a6e88fd7f7507ae3a6dfad2c8be9c16651d8/752.348ccb23.chunk.js
```
Fixing browser tab title
Run the command below to figure which file has the title on it:
```
grep "Carbonio Client" /opt/zextras/web/iris/carbonio-shell-ui/* -rl
```
This will reveal two files: one “js.map” and one “.js“. We want the one that’s just “.js” and it will have a name like “251.e224b936.chunk.js”. This number in the file name and it’s path changes accordingly with Carbonio version. In this example I’ll use this file:
```
/opt/zextras/web/iris/carbonio-shell-ui/529d97b7f2ac385f0036766b233969818688426f/251.e224b936.chunk.js
```
Do not forget to make a copy of it, so you can restore it if needed.

Now that we know what file needs to be fixed and we have a copy of it, run te command below replacing “YourSiteName” by the name you want to see on that tab:
```
sed -i s/"Carbonio Client"/"YourSiteName"/g /opt/zextras/web/iris/carbonio-shell-ui/529d97b7f2ac385f0036766b233969818688426f/251.e224b936.chunk.js
```
Reload and enjoy!

Contributions
==

Below you’ll find a quite nice contribution from Maicon Radeschi who decided to automate the thing and make it easier to implement. Thank you very much Maicon!

