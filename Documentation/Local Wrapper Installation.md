
## Setup
---

In order for the SigRepo application to be installed correctly, it needs an empty folder on your local machine. This will house the database and all of the data. 

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdyc1y2-9MBdJf-5RoVjopJKEdclivjHdm4PospbofVMu5XUZL8aRQ8dGo4WUJNLYu-61yM7ivC_rlXbIHZn_x0DSBN_tH9Uvu-CjIiDQj2Iga_n1PXbvRgIXUTnMoQ_wOOilx2?key=42fWDVfth32izpp91ZSzKA)

In the Wrapper Script this will be designated as {BASE_INSTALL_DIR} and the path to the empty folder will be an argument you pass through the directory. *THIS ARGUMENT IS REQUIRED*

  
  
## Running the Script

---


1. First, you must clone the SigRepo Repository, [https://github.com/montilab/SigRepo](https://github.com/montilab/SigRepo)
    
2. Open a terminal window and navigate to the SigRepo main project directory. 
    
3. The script that you will be running is sigrepo_setup.sh, in order to run this you need to type in;
    

  
./ sigrepo_setup.sh {BASE_INSTALL_DIR}

  

To reiterate, {BASE_INSTALL_DIR} is the path to the empty folder that you created.

  

4. You will be prompted to create a SQL user and password. This will be the super user of the application that has access to sudo privileges in the database. NOTE: the super user password will also be the root user password
    
5. The default port being used is 3306, if this port is not available on your local machine, then the wrapper will find an available port and prompt the user if this is okay. If the user confirms, then the wrapper will proceed with installation.
    

  

 It also takes a bit to fully install the application (5-10 minutes)

