There are two approved methods for remote access. Everyone can request remote access - and you should do so if you're planning to work remotely, want to access data at conferences, or check on jobs in the evening/weekend! If you don't have a VPN and have not spoken with a staff member about requesting one, please skip lower down to see instructions about accessing Jupyterhub, Rstudio, and the QC dashboard remotely. 

## VPN Access
### 1. Getting MobilePass access
First, you will need a 'soft VPN token' from IMG. To get this, fill out the form at `/projects/admin/vpn-account-request-form.doc` and get it back to staff (it needs to be signed by Dr. Voineskos and ultimately sent to Research IT/IMG). 

Then someone from IMG will send you a link and credentials to configure the MobilePASS app on your smartphone, which is used for two-factor authentication. Your username will be set for you by IMG, and should be identical to your CAMH username (usally firstname_lastname). You will need to set up your own 4-digit password. 

If you don't already have [Citrix Receiver](https://www.citrix.com/products/receiver/), you will need to install it. You simply need to download and agree to the license agreement (i.e., install), and can stop before entering the server and domain information it requests at latter stages).

### 2. Getting MobaXTerm Access
Once you have completed the first step go to `insite.camh.net` (if you're outside of camh while setting this up, you can log in to 'Basic Access' at `remote.camh.ca` to open insite). On the left side bar there is an 'IMG request form' link. Fill out the form with your info, selecting 'Myself' in the 'Who is this request for?' field and 'New non-clinical accounts' in the 'What are you requesting?' field. Ensure that your info is in the 'You' and 'Recipient' columns of the form as well. The 'approver' should be set to your manager, which for Kimel is Dielle Miranda. 

On the next page select 'User account' in the 'Non-clinical' field and select 'Other accounts and special requests' and enter 'Requesting access to MobaXTerm'. And then click send. Your request needs to be approved by both your manager and David Rotenberg (and they should both be notified automatically once the form is sent). If there are any delays you may need to follow up with them.

### 3. Logging in remotely
After you have MobilePASS and approval to use MobaXTerm and have Citrix set up on your machine navigate to [remote.camh.ca](remote.camh.ca). Select `VPN Access - CAMH Laptops Only`, enter your CAMH username and password, and the 8-digit code you generate with the MobilePASS app. 

If everything works, you should now see an `Administration` folder, in addition to the regular 'Clinical' and 'Non-Clinical' folders. Click `Administration`, and then `Remote Connections` and then `MobaXTerm`. This will launch a new window. Click `New session`. You can now shell into your favorite computer by entering either its name or IP address in the `Remote host` dialogue box, and your lab username in the `Specify username` box. Once you enter your lab password, a terminal window will open. 

And, you're in!

## Jupyterhub Access
If you have a Kimel lab account (the one used to log into our machines), a CAMH account (the one used to access your email) **and permission to access the SCC with your kimel account** [requested here](http://info2.camh.net/scc/index.php/Account) you can get remote access through the jupyterhub. This option only gives you access through a terminal so if you need a GUI for anything you should set up the VPN instead/in addition.

Once you have both accounts and SCC access set up you can access our system remotely by doing the following:

1. Go to remote.camh.ca
2. Log in with your CAMH account
3. Once in, in the 'non-clinical' folder you'll find a shortcut for 'Campus'. If you open this and have the [Citrix Receiver](https://www.citrix.com/products/receiver/) installed in your browser it should open an instance of Chrome that exists on CAMH's internal network for you.
4. In this Chrome window go to https://jupyter.camh.ca/hub/login
5. Log in with your SCC/TIGRlab account
6. Once you're in you should see a 'new' button on the top right. If you click this, at the bottom of the menu there is a 'terminal' option. Clicking this will open a terminal on the SCC.
![](../_images/jupyter_terminal.png)
7. From here you can work on the SCC or you can ssh into your lab computer with `ssh yourkimelusername@yourmachinename`

NOTE: If you ever need to view files graphically (eg. html files from a QC pipeline's output), you can navigate to your home directory and symlink them there. 
Eg. 
```
cd . #(without being SSH-ed into a kimel lab computer) 
ln -s /KIMEL/tigrlab/scratch/kimel_user/copied_folder . 
```
Now you will be able to view the files in your home directory on the jupyter hub sidebar. You can now open them in a new browser tab to look at them. 

## Dashboard Access 

Follow steps 1 through 3 above. Go to http://srv-dashboard.camhres.ca/. Congrats, you're in! You do not need an SCC account to access this.

## RStudio Access

Follow steps 1 through 3 above. Then navigate to rstudio.camh.ca. 

NOTE: You can follow the same symlinking procedure as in the Jupyterhub access through the "terminal" tab on rstudio to copy your files over to the current directory so you can work on them. 