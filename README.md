# VNC Server Configuration
VNC [(Virtual Network Computing)](https://en.wikipedia.org/wiki/Virtual_Network_Computing) is a visual connection system that enables you to interact with the graphical desktop environment of a remote PC using a mouse and a keyboard.
If you have worked with Microsoft Remote Desktop Protocol (RDP) before, think of VNC as an open-source alternative.
VNC is quite a lifesaver for many who are not comfortable working from the command-line and need to manage files, install software and configure settings on a remote server.
This section details installing and configuring the VNC Server on an Ubuntu 20.04 machine, and we’ll look at how we can connect to it via VNC desktop client.

## VNC Server Installation ##
* Step 1: Install a Desktop Environment
   - For this particular instance, we will install the [XFCE](https://xfce.org/) desktop environment. It is fast, stable, and easy to use.

         $ sudo apt update 
         $ sudo apt install xfce4 xfce4-goodies

   - xfce4-goodies is an additional package for XFCE Desktop Environment, which brings a lot more advanced enhancement. You will see a prompt to select a display manager for your newly installed XFCE Desktop Environment during the installation. Select any display manager and press Enter.

* Step 2: Install VNC server 
   - After successfully installing the XFCE desktop environment, we can proceed to install the VNC server. There are several VNC servers available for Linux today. They include TightVNC, x11VNC, and TigerVNC. [TigerVNC](https://tigervnc.org/) server is the most stable across all desktop environments, so let's install TigerVNC on Ubuntu

         $ sudo apt install tigervnc-standalone-server
  
* Step 3: Configure VNC settings 
   - After a successful installation, we need to perform the initial VNC configuration, set up a VNC access password and initialize the VNC server. Execute the command below to initialize the VNC server instance and set up a password. The password must be between 6-8 characters long. The system will automatically truncate any password loger than eight characters.

         $ vncserver

   - After setting up the password, you will get a prompt to set a View-Only password. That means anybody who accesses the VNC server with a view-only password will not be able to VNC desktop with either Mouse or Keyboard. We won’t set up a view-only password. I will just type N and hit Enter.

   - We can see this process lists the connection information for our VNC server from the message above. Additionally, it started the VNC server at port 5901, which is the display port. This port (5901) is referred to as :1. If you create additional VNC server instances, they will be presented as port 5902 as :2, port 5903 as :3, and so on.
   - Up to this point, our VNC server is up and running. However, it cannot give us access to a graphical interface since it’s not configured to launch our XFCE desktop environment.

   - If you ever want to change the VNC password or the view-only password, execute the command below:

         $ vncpasswd

* Step 4: Configure VNC server to load graphical interface of our choosing

   - One main goal is telling VNC which Desktop Environment to connect and use – in this case, XFCE. To get started, let’s kill the running VNC instance that we launched in Step 2 above running on port 5901. Execute the command below:

         $ vncserver -kill :1

   - If another instance were running on another port, say 5902 or 5903, we would execute the commands vcnserver -kill :2 and vncserver -kill :3, respectively.

   - To configure VNC, we will need to create a file called xstartup file in the .vnc folder under the home directory (~/.vnc/xstartup). This is where we’ll configure what desktop environment we want the VNC server to use.

         $ nano ~/.vnc/xstartup

   - Add the below lines

          #!/bin/sh
          xrdb $HOME/.Xresources
          startxfce4 &

   - #!/bin/sh: This line, commonly referred to as shebang, tells the system which interpreter we will be using. In this case, it’s the bash interpreter. Other Linux shells include Almquist shell, KornShell, etc.
   - xrdb $HOME/.Xresources: This line tells the VNC server to read the .Xresources file where users can make changes on the graphical desktop general settings like fonts, color, etc.
   - startxfce4 &: This line tells VNC which Desktop Environment to launch. startxfce4 is a script responsible for starting an Xfce session.

   - Make the startup file executable

         $ chmod +x ~/.vnc/xstartup

   - Restart the VNC server for the changes to take effect

         $ vncserver -localhost no :1

   - The above command launches a VNC server instance on port 5901. We added -localhost no to be able to access the server via VNC from the outside.
