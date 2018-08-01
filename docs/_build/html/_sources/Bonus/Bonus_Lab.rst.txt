Bonus Lab – Traffic groups, iApps and Active-Active
===================================================

If you have time, this is a bonus lab. Here you will create a new
traffic group. You will use iApps to create a new HTTP application that
reside in that address group and you will create a floating IP address
that will be used as the default gateway that also resides in that
traffic group.

Building a new traffic group and floating IP.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1. On you **Active** BIG-IP, go to **Device Management >> Traffic
   Groups** and hit **Create**

   a. Use the f5.http template, which was designed for general web
      services

      i.  **Name**: iapp_tg

      ii. Take the defaults for the rest.

2. Add a floating Self-IP to the **server_vlan**. Go to **Network >>
   Self IP**

   a. **Name:** server_gateway

   b. **IP Address:**\ 10.1.20.240

   c. **Netmask:** 255.255.255.0

   d. **VLAN/Tunnel:** server_vlan

   e. **Traffic Group:** iapp_tg (floating)

Building an HTTP application using an iApp template.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1. Go to **iApp >> Application** **Services** and hit **Create**.

   a. Use the f5.http template, which was designed for general web
      services

      i.   Set the **Template Selection** to **Advanced**

      ii.  **Name**: my_new_iapp

      iii. **Traffic Group:** iapp_tg (floating)

           1. You will have to uncheck the **Inherit traffic group from
              current partition / path**.

      iv.  Under **Template Options**

1. Select the **Advanced – Configure advanced options** for the
   configuration mode

   v. Under the **Network** tab

1. **How have you configured routing on your web servers?** Servers have
   a route to the clients through the BIG-IP system

   a. In other words, the BIG-IP is the default gateway for the servers

   b. Otherwise the template would use SNAT by default

   vi. Under **Virtual Server and Pools**

1. Your virtual server IP is **10.1.10.110**

2. Your hostname will be
   `www.f5agility.com <http://www.f5agility.com>`__ because you have to
   put one in.

3. Create a new pool with the members **10.1.20.14:80** and
   **10.1.20.15:80**

   c. **If you hit add after the last pool member and have a new row,
      you will need to delete the row prior to finishing**

   vii. Hit **Finished** at the bottom of the page

2. Go to **iApp >> Application Services** and select the new application
   you created

   b. Select **Components** from the top bar

      viii. Here you will see all the configuration items created by the
            iApp

      ix.   Do you see anything created that you weren’t asked about?

3. Remember the concept of strictness? Let’s test that out\`

a. Go to **Local Traffic >> Pools >> Pool List**

i.  Select the pool created by your iApp: **my_new_iapp_pool**

ii. Attempt to add **10.15.11.13:80** to your **my_new_iapp_pool**

    2. Did it fail?

b. Go to your iApp and select **Reconfigure** from the top bar

i.  Now attempt to add your new pool member

ii. You can check the Components tab to verify your success

**SYNCHRONIZE YOUR CHANGES**

Active-Active Setup
~~~~~~~~~~~~~~~~~~~

1. Now, let’s make our sync-failover group active-active. On the
   **Active** BIG-IP:

   a. Go to **Device Management >> Traffic Groups**

      i.  Go to you **iapp_tg** traffic group.

      ii. Under **Advanced Setup Options**

          1. You are going to set up **iapp_tg** to prefer to run on
             **bigip02.f5agility.com** and auto failback to **bigip02**
             if **bigip02** should go down and come back up later.

          3. Is this normally a good idea?

      iii. **Failover Method:** HA Order

      iv.  **Auto Failback:** <checked>

      v.   **Failover Order:** **bigip102.f5agility.com** then
           **bigip01.f5agility.com**

      vi.  Ensure you synchronized the change to the other BIG-IP

1. If the traffic group is active on the wrong BIG-IP initially you will
   have to do a Force to Standby on the traffic group to make it active
   on the BIG-IP you want it on by default

   a. What is the ONLINE status of each of your BIG-IPs?

   b. Reboot the BIG-IP with your second traffic group on it. Watch to
      see if the application becomes active on the other BIG-IP during
      the reboot and if it falls back to the Default Device once the
      BIG-IP has come back up.

   c. You can verify this by checking your traffic groups or going to
      the web server and looking at the client IP
