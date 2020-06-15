**Introduction:**
=================

This is a quick start guideline to how to implement deep security agent SaaS
with Squid proxy in AWS.

This document is divided into two sections:

-   **Setup your environment:**

-   This section contains how to setup your environment and walkthrough in every
    step using AWS.

    ![alt text](https://github.com/ds-amea/squid-setup-guide/blob/master/assets/1.jpg)

    

-   **Cloud one workload security:**

-   This section contains a guideline for how to install deep security agent in
    cloud on workload security dashboard.

**Setup your enviroment**
=========================

1.  **Virtual private cloud (VPC):**

-   Create VPC and assign any private IP address ranges:

![alt text](https://github.com/ds-amea/squid-setup-guide/blob/master/assets/2.jpg)

Subnet sizing for private IPv4 in AWS:

| **IP ranges**                   | **Prefix**          |
|---------------------------------|---------------------|
| 10.0. 0.0 - 10.255. 255.255     | (10/8 prefix)       |
| 172.16. 0.0 - 172.31. 255.255   | (172.16/12 prefix)  |
| 192.168. 0.0 - 192.168. 255.255 | (192.168/16 prefix) |

1.  **Internet gateway:**

-   Internet gateway is acting as bridge connection between internet and VPC.

![alt text](https://github.com/ds-amea/squid-setup-guide/blob/master/assets/3.jpg)

After you create internet gateway, you need to associate it with VPC.

1.  **Subnets:**

-   you need to create two subnets which are:

-   **Public Subnet:**

-   It will configure to access the internet through the internet gateway if the
    traffic is going to the internet.

-   This subnet will be used for Squid proxy instance.



-   **Private Subnet:**

![alt text](https://github.com/ds-amea/squid-setup-guide/blob/master/assets/4.jpg)

It will configure to access to internet through Squid proxy if the traffic is
going to the internet.

1.  **Squid proxy:**

-   You need to connect to proxy server ec2-user running on Linux OS by using
    putty:

-   Open putty On session section –\<Write (username \@Public IP address) and
    choose on connection type SSH.

    ![alt text](https://github.com/ds-amea/squid-setup-guide/blob/master/assets/5.jpg)

-   Open Puttygen program then choose load then select key pair file that you
    assign it with this ec2 instance then save private key.

  

-   On putty interface choose SSH section then Auth browse your private key file
    that you saved from Puttygen then click open to connect ec2-instance.

    ![alt text](https://github.com/ds-amea/squid-setup-guide/blob/master/assets/6.jpg)

-   write sudo yum install -y squid –\< to install squid proxy

    ![alt text](https://github.com/ds-amea/squid-setup-guide/blob/master/assets/7.jpg)

    sudo service squid start

-   sudo chkconfig squid on –\< to start squid proxy

-   sudo vim /etc/squid/squid.conf –\< Edit the SQUID Configurations file to
    allow TrendMicro and AWS domains only

-   add the following commands in squid.conf:

    **\#\# Restrict TrendMicro Access**

    **acl GOOD dstdomain .trendmicro.com .amazonaws.com**

    **http_access allow GOOD**

    **http_access deny all**

![alt text](https://github.com/ds-amea/squid-setup-guide/blob/master/assets/8.jpg)

After you add these commands Press Esc key and type: wq to save changes to
a file and exit from vim.

-   sudo service squid restart–\< restart Squid proxy.

1.  **Route tables:**

-   You need to create two route tables which are:

-   **Private route table:**

-   Once you create private route table, the default route will navigate local
    traffic  
    through local network and it needs to add new route which navigate any
    traffic to go through Squid proxy if the traffic is going to internet (you
    need to associate this route within private subnet).

   ![alt text](https://github.com/ds-amea/squid-setup-guide/blob/master/assets/9.jpg)

-   **Public route table:**

![alt text](https://github.com/ds-amea/squid-setup-guide/blob/master/assets/10.jpg)

Once you create public route table, the default route will navigate local
traffic to go through local network and it needs to add new route which navigate
any traffic to go through internet gateway if the traffic is going to internet
(you need to associate this route within public subnet).

1.  **Security Group:**

-   Aws security group let you limit and control inbound & outbound traffic in
    EC2 instances level.

-   **Security group Squid proxy in public subnet**:

-   Inbound SSH port: Allowing SSH traffic to accept inbound traffic from
    authorized external device to squid proxy, (**Note:** specify your own
    public IP to prevent an authorized connection to squid proxy (optional)).

![alt text](https://github.com/ds-amea/squid-setup-guide/blob/master/assets/11.jpg)

Inbound squid proxy port: allowing 3128 port traffic from internet to squid
proxy.

![alt text](https://github.com/ds-amea/squid-setup-guide/blob/master/assets/12.jpg)

Outbound HTTPS:allowing HTTPS outbound traffic and it is used for various Deep
Security cloud services, you can restrict IP addresses in HTTPS port ,for more
info please check this
URL:[https://help.deepsecurity.trendmicro.com/10/0/Manage-Components/ports.html\#Deep2](https://help.deepsecurity.trendmicro.com/10/0/Manage-Components/ports.html%23Deep2)

-   **Security group in private subnet:**

-   Inbound squid proxy port: allowing 3128 port traffic from squid proxy to
    Deep security agent that has been installed in ec2 instance.
(media/acfcccf7f3908cd0356d055f2f7d9b8d.jpg)

![alt text](https://github.com/ds-amea/squid-setup-guide/blob/master/assets/13.jpg)

Outbound squid proxy port: allowing 3128 port traffic from ec2 instance that has
been installed Deep security agent to squid proxy server.

-   **Note:** you need to assign private IP squid proxy in inbound/outbound ec2
    instances in private subnet.

**Install deep security agent**
===============================

1.  **Signup/Login Dashboard:**

![alt text](https://github.com/ds-amea/squid-setup-guide/blob/master/assets/14.jpg)

You can sign up or login by using this URL: <https://cloudone.trendmicro.com/>

![alt text](https://github.com/ds-amea/squid-setup-guide/blob/master/assets/15.jpg)

After successfully registering, login your account, you will see cloud one
dashboard click on workload security.

1.  **Add AWS account:**

-   Cloud one workload security dashboard will be opened click on computers tab
    then on left side right click on computers then choose add AWS account.

   ![alt text](https://github.com/ds-amea/squid-setup-guide/blob/master/assets/16.jpg)

1.  **Setup type:**

-   You can add AWS account by using quick & Advanced.

    ![alt text](https://github.com/ds-amea/squid-setup-guide/blob/master/assets/17.jpg)

1.  **Add squid proxy server in workload security:**

![alt text](https://github.com/ds-amea/squid-setup-guide/blob/master/assets/18.jpg)

Go to administration then click system setting, click new to add new proxy.

-   In new proxy properties interface, write the private IP squid proxy with
    port.

    ![alt text](https://github.com/ds-amea/squid-setup-guide/blob/master/assets/19.jpg)

1.  **Deploy deep security agent on server:**

![alt text](https://github.com/ds-amea/squid-setup-guide/blob/master/assets/20.jpg)

In cloud one workload dashboard, click on support and you will see two ways of
deployment.

-   Download agents:

-   If you want to download Agent package installer and install it manually.

-   installation guide for manually install deep security
    agent:<https://help.deepsecurity.trendmicro.com/10_2/aws/Get-Started/Install/install-dsa.html>

    ![alt text](https://github.com/ds-amea/squid-setup-guide/blob/master/assets/21.jpg)

-   Deployment script:

-   This option will install agent by using script.

-   Installation guide for install deep security agent using deployment script:
    <https://help.deepsecurity.trendmicro.com/10_2/aws/Add-Computers/ug-add-dep-scripts.html>

-   In proxy to contact deep security manager & proxy to contact relay options:

    ![alt text](https://github.com/ds-amea/squid-setup-guide/blob/master/assets/22.jpg)

    you need to select squid proxy that you already added in previous step.

1.  **Check the state of deep security agent after deployment:**

-   Go to computer and see the status of instance.

![alt text](https://github.com/ds-amea/squid-setup-guide/blob/master/assets/23.jpg)
