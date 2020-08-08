# repo for quexs docker image used at work

Original code:
- https://master.dl.sourceforge.net/project/quexs/quexs/quexs-1.16.5/quexs-1.16.5.zip
- https://github.com/adamzammit/quexs-docker
- https://hub.docker.com/r/acspri/quexs


Changes:
1. Minor changes to some of the quexs display settings (i.e. enable the alternate interface option and hide some tabs).
2. Force https in the config
3. Added ssl functionality for our external (Azure) database. 


Ad 2.

Force https is enabled in our Azure Web App. Without the config change quexs ends up in a redirect loop at the login page, because after the POST request https:// is changed back to http:// .


Ad 3.

The 1.x branche from quexs uses a database class library, ADOdb, but the mysqli-driver ("./include/limesurvey/classes/adodb/drivers/adodb-mysqli.inc.php") is kind of old and does not support ssl. 
So I copy/pasted the 'mysqli_ssl_set' part from the latest version of this driver (https://github.com/ADOdb/ADOdb/blob/d5ad74c4a7dfd9d43e4623fad8654fe4a2403d67/drivers/adodb-mysqli.inc.php) and configured it as described here
https://docs.microsoft.com/en-us/azure/mysql/howto-configure-ssl. The required certificate is downloaded during the build of the image (see Dockerfile).


# What is queXS? 

[queXS](https://quexs.acspri.org.au/) is a free and open source Computer Assisted Telephone Interviewing (CATI) system based on PHP and MySQL, including LimeSurvey. 

