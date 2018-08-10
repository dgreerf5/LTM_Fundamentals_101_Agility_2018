Bonus Lab – Traffic groups, iApps and Active-Active
===================================================

If you have time, this is a bonus lab. Here you will create a new
traffic group. You will use iApps to create a new HTTP application that
reside in that address group and you will create a floating IP address
that will be used as the default gateway that also resides in that
traffic group.

Building a new traffic group and floating IP.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#. On your **Active** BIG-IP, go to **Device Management >> Traffic
   Groups** and hit **Create**
   
      #. Use the f5.http template, which was designed for general web
          services

      #.  **Name**: iapp_tg

      #.  Take the defaults for the rest.

#. Add a floating Self-IP to the **server_vlan**. Go to **Network >>
   Self IP**

   #. **Name:**  server_gateway

   #. **IP Address:**  10.1.20.240

   #. **Netmask:**  255.255.255.0

   #. **VLAN/Tunnel:**  server_vlan

   #. **Traffic Group:**  iapp_tg (floating)

Building an HTTP application using an iApp template.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1. Go to **iApp >> Application** **Services** and hit **Create**

   1a. Use the f5.http template, which was designed for general web
      services

      1.1a.  Set the **Template Selection** to **Advanced**

      1.1b.  **Name**: my_new_iapp

      1.1c.  **Traffic Group:** iapp_tg (floating)

           1. You will have to uncheck the **Inherit traffic group from
              current partition / path**.

      1.1d.  Under **Template Options**

           1. Select the **Advanced – Configure advanced options** for the
              configuration mode

      1.1e. Under the **Network** tab

           1. **How have you configured routing on your web servers?** Servers have
              a route to the clients through the BIG-IP system

                  a. In other words, the BIG-IP is the default gateway for the servers

                  b. Otherwise the template would use SNAT by default

      1.1f. Under **Virtual Server and Pools**

            a. Your virtual server IP is **10.1.10.110**

            b. Your hostname will be
               `www.f5agility.com <http://www.f5agility.com>`__ because you have to
               put one in.

            c. Create a new pool with the members **10.1.20.14:80** and
               **10.1.20.15:80**

                  1. **If you hit add after the last pool member and have a new row,
                     you will need to delete the row prior to finishing**

      1.1g. Hit **Finished** at the bottom of the page

2. Go to **iApp >> Application Services** and select the new application
   you created

   2a. Select **Components** from the top bar

      2.1a. Here you will see all the configuration items created by the
            iApp

      2.1b. Do you see anything created that you weren’t asked about?

3. Remember the concept of strictness? Let’s test that out

   3a. Go to **Local Traffic >> Pools >> Pool List**

       3.1a. Select the pool created by your iApp: **my_new_iapp_pool**

       3.1b. Attempt to add **10.15.11.13:80** to your **my_new_iapp_pool**

             1. Did it fail?

   3b. Go to your iApp and select **Reconfigure** from the top bar

       3.2a. Now attempt to add your new pool member

       3.2b. You can check the Components tab to verify your success

**SYNCHRONIZE YOUR CHANGES**

Active-Active Setup
~~~~~~~~~~~~~~~~~~~

1. Now, let’s make our sync-failover group active-active. On the
   **Active** BIG-IP:

   1a. Go to **Device Management >> Traffic Groups**

      1.1a. Go to you **iapp_tg** traffic group.

      1.1b. Under **Advanced Setup Options**

          1. You are going to set up **iapp_tg** to prefer to run on
             **bigip02.f5agility.com** and auto failback to **bigip02**
             if **bigip02** should go down and come back up later.

          2. Is this normally a good idea?

      1.1c. **Failover Method:** HA Order

      1.1d. **Auto Failback:** <checked>

      1.1e. **Failover Order:** **bigip102.f5agility.com** then
            **bigip01.f5agility.com**

      1.1f.  Ensure you synchronized the change to the other BIG-IP

2. If the traffic group is active on the wrong BIG-IP initially you will
   have to do a Force to Standby on the traffic group to make it active
   on the BIG-IP you want it on by default

   2a. What is the ONLINE status of each of your BIG-IPs?

   2b. Reboot the BIG-IP with your second traffic group on it. Watch to
      see if the application becomes active on the other BIG-IP during
      the reboot and if it falls back to the Default Device once the
      BIG-IP has come back up.

   2c. You can verify this by checking your traffic groups or going to
      the web server and looking at the client IP
