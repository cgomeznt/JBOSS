Agregar Usuario Administrador al JBOSS EAP
==========================================


Información optenida de https://access.redhat.com/documentation/en-us/red_hat_jboss_data_virtualization/6.2/html/administration_and_configuration_guide/sect-adding_a_jboss_eap_management_user


Add the User for the Management Interfaces
++++++++++++++++++++++++

The following procedure documents how to create the initial administrative user, in the event such a user is not created by the chosen installation method. This initial administrative user can use the web-based Management Console and remote instances of the Management CLI to configure and administer JBoss EAP 6 from remote systems.

**Procedure 3.1. Create the Initial Administrative User for the Remote Management Interfaces**

Run the **add-user.sh** or **add-user.bat** script.

Change to the EAP_HOME/bin/ directory. Invoke the appropriate script for your operating system.

Red Hat Enterprise Linux::

	[user@host bin]$ ./add-user.sh

Microsoft Windows Server::

	C:\bin>  add-user.bat

**Choose to add a Management user.**
Press ENTER to select the default option a to add a Management user.

This user is added to the ManagementRealm and is authorized to perform management operations using the web-based Management Console or command-line based Management CLI. The other choice, b, adds a user to the ApplicationRealm, and provides no particular permissions. That realm is provided for use with applications.

**Enter the desired username and password.**
When prompted, enter the username and password. You will be prompted to confirm the password.

**Enter group information.**

Add the group or groups to which the user belongs. If the user belongs to multiple groups, enter a comma-separated list. Leave it blank if you do not want the user to belong to any groups.

**Review the information and confirm.**
You are prompted to confirm the information. If you are satisfied, type yes.

**Choose whether the user represents a remote JBoss EAP 6 server instance.**
Besides administrators, the other type of user which occasionally needs to be added to JBoss EAP 6 in the ManagementRealm is a user representing another instance of JBoss EAP 6, which must be able to authenticate to join a cluster as a member. The next prompt allows you to designate your added user for this purpose. If you select yes, you will be given a hashed secret value, representing the user's password, which would need to be added to a different configuration file. For the purposes of this task, answer no to this question.

