# Hortonworks Sandbox Setup

This project utilizes the VirtualBox edition of the HDP 2.4 
[Hortonworks Sandbox](http://hortonworks.com/products/hortonworks-sandbox/ "Hortonworks Sandbox").

## Setup Passwords

As documented on the <http://127.0.0.1:8888> _splash screen_, you will need
to follow the instructions shown after clicking on the _View Advanced Options_ 
to enable the `admin` user to log into the Ambari console at 
<http://127.0.0.1:8080>.  Below is an output of what that activity should 
look like.  NOTE: The initial `root` password is `hadoop`, but you are 
required to change it. 

```
HW10653-2:~ lmartin$ ssh root@127.0.0.1 -p 2222
root@127.0.0.1's password: 
You are required to change your password immediately (root enforced)
Last login: Tue Mar  1 21:05:47 2016 from 10.0.2.2
Changing password for root.
(current) UNIX password: 
New password: 
Retype new password: 
[root@sandbox ~]# ambari-admin-password-reset
Please set the password for admin: 
Please retype the password for admin: 

The admin password has been set.
Restarting ambari-server to make the password change effective...

Using python  /usr/bin/python2
Restarting ambari-server
Using python  /usr/bin/python2
Stopping ambari-server
Ambari Server stopped
Using python  /usr/bin/python2
Starting ambari-server
Ambari Server running with administrator privileges.
Organizing resource files at /var/lib/ambari-server/resources...
Server PID at: /var/run/ambari-server/ambari-server.pid
Server out at: /var/log/ambari-server/ambari-server.out
Server log at: /var/log/ambari-server/ambari-server.log
Waiting for server start....................
Ambari Server 'start' completed successfully.
```

It is suggested that you set the `admin` Ambari user's password
to `admin` for consistency with the other web UIs.

## Allow Access to Tez View

Once logged into Ambari as `admin`, click on:

* Pulldown in upper-right corner that has user name in it and select _Manage Ambari_
* _Manage Permissions_ button
* _Views_ link in the _Views_ widget on left-side nav
* _TEZ_ item in _View Name_ column
* _Tez View_ link that became visible

Then in the _Permissions_ widget box click inside the grey box
underneath the _Grant permissions to these groups_ text which
will then allow you to add the `views` group.  This should look like
the following screenshot (click on the _blue checkbox_ to save).

![alt text](./GrantView.png "grant access")