There are two approved methods for remote access. Everyone can request
remote access - and you should do so if you're planning to work
remotely, want to access data at conferences, or check on jobs in the
evening/weekend!

VPN Access
==========

First, you will need a 'soft VPN token' from IMG. To get this, fill out
the form at ``/projects/admin/vpn-account-request-form.doc`` and get it
back to staff (it needs to be signed by Dr. Voineskos and ultimately
sent to Research IT/IMG). You should made an explicit note on the form
that you want access to MobaXTerm (which allows for the opening of
external displays via the terminal) as opposed to puTTy, which is the
default, and does not.

Then, someone from IMG will send you a link and credentials to configure
the MobilePASS app on your smartphone, which is used for two-factor
authentication. Your username will be set for you by IMG, and should be
identical to your CAMH username (usally firstname\_lastname). You will
need to set up your own 4-digit password.

If you don't already have `Citrix
Receiver <https://www.citrix.com/products/receiver/>`__, you will need
to install it. You simply need to download and agree to the license
agreement (i.e., install), and can stop before entering the server and
domain information it requests at latter stages).

After you have both Citrix and the MobilePASS app, navigate to
`remote.camh.ca <remote.camh.ca>`__. Select
``VPN Access - CAMH Laptops Only``, enter your CAMH username and
password, and the 8-digit code you generate with the MobilePASS app.

If everything works, you should now see an ``Administration`` folder, in
addition to the regular 'Clinical' and 'Non-Clinical' folders. Click
``Administration``, and then ``Remote Connections`` and then
``MobaXTerm``. This will launch a new window. Click ``New session``. You
can now shell into your favorite computer, by entering either its name
or IP address in the ``Remote host`` dialogue box, and your lab username
in the ``Specify username`` box. Once you enter your lab password, a
terminal window will open.

And, you're in!

Jupyterhub Access
=================

If you have a Kimel lab account (the one used to log into our machines),
a CAMH account (the one used to access your email) and an SCC account
`requested here <http://info2.camh.net/scc/index.php/Account>`__ you can
get remote access through the jupyterhub. This option only gives you
access through a terminal so if you need a GUI for anything you should
set up the VPN instead/in addition.

Once you have all three login accounts set up you can access our system
remotely by doing the following:

1. Go to remote.camh.ca
2. Log in with your CAMH account
3. Once in, in the 'non-clinical' folder you'll find a shortcut for
   Insite. If you open this and have the `Citrix
   Receiver <https://www.citrix.com/products/receiver/>`__ installed in
   your browser it should open internet explorer (yuck) for you.
4. In the Internet Explorer window go to
   https://jupyter.camh.ca/hub/login
5. Log in with your SCC account
6. Once you're in you should see a 'new' button on the top right. If you
   click this, at the bottom of the menu there is a 'terminal' option.
   Clicking this will open a terminal on the SCC.
   [[https://github.com/TIGRLab/documentation/blob/master/remote\_access/jupyter\_terminal.png]]
7. From here you can work on the SCC or you can ssh into your lab
   computer with ``ssh yourkimelusername@yourmachinename``
