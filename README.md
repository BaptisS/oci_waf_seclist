# oci_waf_seclist
Quick way to lockdown OCI WAF Origin 

### Lockdown OCI WAF Origin using CloudShell (SecList) ###

Once you created and configured your new OCI WAF Policy, it is important to apply a set of security rules around your Web Application to block all requests, which are not passing through the WAF service.

OCI WAF stand in front of your web application to detect and block unwanted/malicious access. . In most cases, your Web Application itself sits behind a Load Balancer. Depending on your architecture and preferences, you may want to assign security rules to your Load Balancer or Web Application subnet by using a Security List, or alternatively you may prefer to assign the security rules to your Load Balancer or Web Application Network Interfaces by using a Network Security Group.

This document will guide you through the steps needed to create and assign a ***Security List*** containing a list of OCI WAF public IP addresses used to send the traffic to your load balancer / Web Application. 




***Important Note:*** 
If you are using a Load Balancer in front of your Web Application, Security rules must be applied to your Load Balancer subnet (if using Security Lists) or Load Balancer Network Interfaces (if using network Security Groups).


***Prerequisites:***
-	At least one OCI WAF Policy already created (and in the ‘Available' state)  (https://docs.cloud.oracle.com/iaas/Content/WAF/Tasks/managingwaf.htm)
-	An OCI user account with sufficient permissions to create and assign Security Lists in the desired Compartment / VCN. 
 
 
 
### 1- Create a New (empty) Security List.    

1.1-	Sign-in to the OCI web console with your OCI user account.

1.2-	Open the OCI Menu (top left), expand the ***‘Networking’*** section and click on ***‘Virtual Cloud Networks’***. 

1.3-	Click on your VCN then select the ***‘Security Lists’*** Resources type.  

![PMScreens](/img/01.jpg)

1.4-	Click on the ***‘Create Security List’*** button. 

![PMScreens](/img/02.jpg)

1.5-	Provide a meaningful name for this new security list. (Ie. ‘OCIWAF-SL’)

1.6-	Select proper compartment, then click on ***‘Create Security List’*** button. 

1.7-	Highlight the newly created Security list and select ***‘Copy OCID’*** in its right menu. 

![PMScreens](/img/03.jpg)
 
### 2-    Import Security Rules using Cloud Shell commands.

2.1-	Start your OCI Cloud Shell session. In the OCI Console top right section, click on the Cloud Shell icon:  

![PMScreens](/img/04.jpg)

2.2-	Wait few seconds for your Cloud Shell instance to be started and ready to use.

![PMScreens](/img/05.jpg)

2.3-	Copy and Paste (CTRL+SHIFT+’V’) the command below in your Cloud Shell session.

**wafseclist=ocid1.securitylist.oc1.eu-frankfurt-1.aaaaaaaxxxxx**

(Replace ‘ocid1.securitylist.oc1.eu-frankfurt-1.aaaaaaaxxxxx’ by your Security List OCID copied in the previous step.)

![PMScreens](/img/06.jpg)

2.4-	Import Security rules. 

2.4.1-	***[OPTION 1]*** Allow incoming HTTP (TCP80) and HTTPS (TCP443) traffic, copy and Paste (CTRL+SHIFT+V) the commands below in your Cloud Shell session.

**wget https://objectstorage.eu-frankfurt-1.oraclecloud.com/p/Mkpk6FPW9_BovdrajqNzKzH7M2UMhIJN5vEg-QLF6Vo/n/oracsmemeaspec/b/FraBuck01/o/wafseclist-import-80-443.json**

**oci network security-list update --security-list-id $wafseclist --ingress-security-rules file://wafseclist-import-80-443.json**

2.4.2- 	***[OPTION 2]*** Allow incoming HTTPS (TCP443) traffic only, copy and Paste (CTRL+SHIFT+V) the commands below in your Cloud Shell session.

**wget https://objectstorage.eu-frankfurt-1.oraclecloud.com/p/V-w5R9aNlzszQZeAbWDl6gyZ7x6cvIgv0162qhLLEoI/n/oracsmemeaspec/b/FraBuck01/o/wafseclist-import-443.json**

**oci network security-list update --security-list-id $wafseclist --ingress-security-rules file://wafseclist-import-443.json**


### 3-    Review the security list content. 

![PMScreens](/img/07.jpg)

3.1-	Ensure the security List has been populated successfully. 

### 4-   Assign the Security List to the desired subnet.
4.1-	Go to your VCN dashboard, and then select the ***‘Subnets’*** Resources section. 

![PMScreens](/img/08.jpg)

4.2-	Click on the desired subnet name (LBaaS/WebApp subnet). 

![PMScreens](/img/09.jpg)

4.3-	Click on ***‘Add Security List’*** button.  

4.4-	Select the newly created Security List (Ie. OCIWAF-SL)  

![PMScreens](/img/10.jpg)

4.5-	Click ***‘Add Security List’*** button to assign the Security to the subnet.  

### 5-   Remove any permissive rules 
5.1-	Click ***‘Add Security List’*** button to assign the Security to the subnet.  
