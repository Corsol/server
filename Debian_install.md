[![](http://getmangos.eu/wiki/icons/home.gif)](http://getmangos.eu/wiki/Home.md) 
[![](http://getmangos.eu/wiki/icons/back.gif)](http://getmangos.eu/wiki/Installation%20Guides/Installation%20Guides.md) 

----------

This guide is for ALL Debian-based distributions. (made by Corsol install experience)

##Intro
This guide is for those who would prefer to use the superior stability and resource management of Linux, or simply cannot use Windows for various reasons.
While the current Linux sticky guide is a great source of information, there are a few nuances it fails to mentions and the aim of this thread is to
address those. This guide will be largely similar, but will hopefully be more up to date with a few of the changes in the repositories. This guide
is written with the complete beginner in mind, and as such might state the obvious on occasion.


   
###1. Fetching the dependencies.
Mangos needs a small number of packages to be able to compile and run properly. Getting those is easy with a simple application of the apt package manager. Just run the following line as super user:
    
    sudo apt-get update
    sudo apt-get install cmake cmake-qt-gui git g++ gcc make libace-ssl-dev libace-dev libbz2-dev libmysql++-dev libmysqlclient-dev libssl-dev
    sudo apt-get install mysql-client mysql-common mysql-server zlib1g-dev vim autoconf libtool screen bash
    
mysql-server will run a short and simple installation daemon of its own and ask for a root password among other things. Make sure to remember it.

###2. The mangos user (NOT optional)
You MUST create a new user with which to run your mangos server on. It is not advised to run the server (and a lot of other things) as root.
We are going to use the username 'mangos' for an example, you can use any username you want but you will need to replace mangos with your username in all paths.
    
    adduser mangos
    
This will create a new user with the name mangos under the folder /home/mangos by default.
If you would prefer your private server to run in another folder, make sure to give your user privileges to that folder.
Example:
    
    chown -R mangos:mangos /path/to/folder
    
And when you're done setting that up, you're going to switch to the user so we can begin the install process.
    
    su - mangos
    
###3. Getting the Mangos source and compile it
There are several mangos versions to choose from.

This guide will use MaNGOS Three (Cataclysm). If you choose to use another version, you can simply just change the cloning URL to the version you would like and look on it's subrepos needed.
This step is documented at the page: [**Cloning the Repos**](http://getmangos.eu/wiki/Installation%20Guides/General/Cloning%20the%20Repos.md)

Actually there are two main branch of Three server:
  * **Develop21**: this branch is used for develop new functions and merge developed structure from Zero server. This is very unstable server that need knowledge of C++ programming language, cmake compilator and other little stuff. So try this solution with caution.
  * **Master**: this is the main brach that it's working. It's not bug free, but it's playable. Stable develops from Develop21 brach will be merged into this one when they are good. For a ready-to-use server this is a good choice.

I'll do a guide for both brach so you can see the difference and try both if you want.
###3.1. Develop21

**NOTE**: Use this guide with caution. Changes can be made and not reported here!

If you want to use the development repository (that can contain errors and other stuff non fully working) you can clone the "develop" branc with this commands. To separate this brach from other installation i've supposed to have my local copy into *server-dev21* folder and use the *build-dev21* folder to compile and buil sources. Finally i place binaries into *run-dev21* folder.
Let's clone the repository locally:

    git clone --recursive develop21 -c https://github.com/mangosthree/database.git database-dev21
    git clone -b develop21 https://github.com/mangosthree/database.git database-dev21
    cd database-dev21
    git clone --recursive https://github.com/mangos/Realm_DB.git Realm
    cd ..
    git clone -b develop21 https://github.com/mangosthree/server.git server-dev21
    cd server-dev21
    git clone --recursive -b Rel21_StormlibForM3 https://github.com/mangos/mangosDeps.git dep
    cd src
    git clone --recursive -b Rel21_Revision https://github.com/mangos/realmd.git realmd
    cd ../../..

###3.1.1. **OPTIONAL** ScripDev 3
If you want to try the new ScriptDev3 library this steps will help you on cloning it.

    cd /src/modules
    git clone https://github.com/mangos/ScriptDev3.git SD3

To ensure the compilation and installation of this new library you need to modify some *CMakeLists.txt* files. Here a lists of location to look and edit:
  * **/server-dev21/CMakeLists.txt**: In this file you need to add a *SCRIPT_LIB_SD3* parameter where neede to configure **cmake** command as needed;
  * **/server-dev21/src/module/CMakeLists.txt**: In this file you need to put this line code "*if(SCRIPT_LIB_SD3) add_subdirectory(SD3) endif()*" after the **SCRIPT_LIB_SD2** include section;
  * **/server-dev21/src/mangosd/CMakeLists.txt**: In this file you need to put this line code "*if(SCRIPT_LIB_SD3) include_directories(${CMAKE_SOURCE_DIR}/src/modules/SD3) endif()*" after the **SCRIPT_LIB_SD2** include section;
  
After this git clone you need to modify the *CMakeLists.txt* into *server-dev21* and *server-dev21/src/module*
to add the *SCRIPT_LIB_SD3* parameter and all it's references like

    if(SCRIPT_LIB_SD3)
        message(STATUS "Script engine SD3     : Yes ")
        add_definitions(-DENABLE_SD3)
    else()
        message(STATUS "Script engine SD3     : No (default)")
    endif()


Now, after all imports you can use the build tool to compile and install the server.

    mkdir build-dev21
    mkdir run-dev21
    cd build-dev21
    cmake ../server-dev21/ -DCMAKE_INSTALL_PREFIX=/full/path/to/run-dev21
    make install

**NOTE**: If you wish to use submodules in your server, you can enable or disable them during the cmake step by adding the following before your PREFIX: (See details at [CMAKE options](https://github.com/mangosthree/server/blob/develop21/CMakeLists.txt))
(0=disabled 1=enabled)
    
    -DBUILD_TOOLS=1 - to build the Extraction Tools
    -DENABLE_LIB_SD2=1 - to Enable SD2
    -DENABLE_LIB_ELUNA=1 - to Enable Eluna
    -DSOAP=1 - to Enable SOAP


###3.2. Master
If you **do not** want to use the development repository (that can contain errors and other stuff non fully working) you can clone the "master" branc with this commands. I've supposed to have my local copy into *server* folder and use the *build* folder to compile and buil sources. Finally i place binaries into *run* folder.

    git clone --recursive https://github.com/mangosthree/database.git database
    git clone --recursive https://github.com/mangosthree/server.git server
    cd server/src/bindings
    git clone --recursive https://github.com/mangosthree/scripts.git
    cd ../../..

Now, after all imports you can use the build tool to compile and install the server.
	
    mkdir build
    mkdir run
    cd build
    cmake ../server/ -DCMAKE_INSTALL_PREFIX=/full/path/to/run -DINCLUDE_BINDINGS_DIR=scripts
    make install
	
**NOTE 1**: If you wish to use submodules with your server, you can enable or disable them during the cmake step by adding the following before your PREFIX: (See details at [CMAKE options](https://github.com/mangosthree/server/blob/master/CMakeLists.txt))
(0=disabled 1=enabled)
    
    -DINCLUDE_BINDINGS_DIR=scripts - to Enable SD2
    
**NOTE 2**: If you have more than 1 CPU core, you can compile quicker by adding the amount of cores you want to use in the compile. You can do this by adding -j # to the make command.
Example with 4 CPU cores: *make -j 4*

###5. Extracting the game assets
This step is documented at the page: [**Extract Game Assets**](http://getmangos.eu/wiki/Installation%20Guides/General/Extracting-Game-Assets.md)

###6. Importing the MySQL Database
Now we can import the databases for realmd, mangos and characters. This can be done easily through a preconfigured shell script that need the ROOT password that was setup earlier on MySQL setup.

    ./install_linux.sh

Process ask for main information on MySQL server instance, username and password needed to import data.

###7. Configuration
This step is documented at the page: [**Configuration Files**](http://getmangos.eu/wiki/Installation%20Guides/General/Configuration-Files.md)

###8. Running the server and creating an Admin account
Your binaries will be located in the bin folder of your server's root directory. Those are mangosd realmd.
mangosd is the world server and realmd is the realm server. Typically you only need one instance of realmd running, and one mangosd instance for every realm you want in your realmlist.

If you would like your server to run on auto restart scripts, then you will need to copy the restart scripts from the server source.
        
	mv /home/mangos/server/src/tools/restart-scripts/*.sh /home/mangos/zero/
	
Be sure to change /home/mangos/zero with your used path and chmod +x each file. Then you will just run ./realmd and ./mangos in that directory.

You need to create your account and give it admin privileges. So if you used the auto restart scripts, you can screen back to it using:
    
	screen -x mangos
	
Then run these commands in the mangos command shell.
    
    account create "username" "password"
    account set gmlevel "username" 3
    account set addon "username" 3
    
After you're done, you can detach from the screen using CTRL A+D
Be sure you detach from it and not exit the screen.

That's it! A functional, partially scripted private server for you to mess around with. Have fun.
If you encounter any issues with the tutorial, please join our forums and explain the problem.
