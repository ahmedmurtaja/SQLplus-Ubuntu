# SQLplus-Ubuntu-

##### 1 - Go To the following URL to download the package with the Oracle installer

` https://mab55.com/prog/linux/oracle/oracle-xe-11.2.0-1.0.x86_64.rpm.zip `

##### 2- move the downloaded package to the desktop

###### 3- right click then open Desktop in Terminal or ` cd /home/your-account/Desktop `

##### 4-We unzip the package with the following command:

`unzip oracle-xe-11.2.0-1.0.x86_64.rpm.zip `

##### 5-Convert the package from .rpm to .deb

>In Ubuntu there is a package called alien that allows us to convert packages with extension .rpm to .dev in an easy, simple and fast way (depending on the power of your computer).

##### We will start by installing the following packages:

`sudo apt-get install alien libaio1 unixodbc`

##### We move to the /Disk1 folder that has been created.

`cd ./Disk1`

##### We convert the file from .rpm to .deb with the following command:

`sudo alien --scripts -d oracle-xe-11.2.0-1.0.x86_64.rpm`

#### Previous configuration
>Before starting with the installation of Oracle in our Ubuntu 20.04, it is necessary to previously carry out the following configuration:

##### We create the script chkconfig.
`sudo pico /sbin/chkconfig`
>Inside the file that we just created in step 1 we insert the following code:

```
#!/bin/bash
# Oracle 11gR2 XE installer chkconfig hack for Ubuntu
file=/etc/init.d/oracle-xe
if [[ ! `tail -n1 $file | grep INIT` ]]; then
echo >> $file
echo '### BEGIN INIT INFO' >> $file
echo '# Provides: OracleXE' >> $file
echo '# Required-Start: $remote_fs $syslog' >> $file
echo '# Required-Stop: $remote_fs $syslog' >> $file
echo '# Default-Start: 2 3 4 5' >> $file
echo '# Default-Stop: 0 1 6' >> $file
echo '# Short-Description: Oracle 11g Express Edition' >> $file
echo '### END INIT INFO' >> $file
fi
update-rc.d oracle-xe defaults 80 01
```
>We save the changes made `(Ctrl + O)` + Enter and exit the document `(Ctrl + X).`

##### We give all the permissions to the file we just created:
`sudo chmod 777 /sbin/chkconfig  `
##### We enter the following file to set the Kernel parameters to Oracle.
`sudo nano /etc/sysctl.d/60-oracle.conf`
##### Paste the following code into the document:
```
# Oracle 11g XE kernel parameters  
fs.file-max=6815744  
net.ipv4.ip_local_port_range=9000 65000  
kernel.sem=250 32000 100 128 
kernel.shmmax=536870912 
```
>We save the changes made `(Ctrl + O) + Enter` and exit the document `(Ctrl + X)`

##### We start the procps service.
`sudo service procps start`
##### We create a mount point /dev/shm for Oracle.
`sudo nano /etc/rc2.d/S01shm_load`
>Inside the file, paste the following code:

```
#!/bin/sh
case "$1" in
start) mkdir /var/lock/subsys 2>/dev/null
       touch /var/lock/subsys/listener
       rm /dev/shm 2>/dev/null
       mkdir /dev/shm 2>/dev/null
       mount -t tmpfs shmfs -o size=2048m /dev/shm ;;
*) echo error
   exit 1 ;;
esac

```
>We save the changes made `(Ctrl + O)` + Enter and exit the document `(Ctrl + X).`
