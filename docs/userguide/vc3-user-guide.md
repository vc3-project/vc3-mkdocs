# VC3 User Guide

### Building Your First Virtual Cluster
**Prerequisites**

In order to use VC3, you’ll need an allocation or account in a target resource that is supported by VC3. These include, but are not limited to:

  - University of Chicago - Research Computing Center
  - University of Notre Dame - Center for Research Computing
  - National Energy Research Scientific Computing Center (NERSC)
  - Pittsburgh Supercomputing Center (Bridges)
  - Open Science Grid
  - and more!

Institutions and resources are added frequently. Check the [Resources](https://www.virtualclusters.org/resources) page for the full list of connected resources in the service.

### 1. Login or Create Account

When you first visit [Virtual Clusters](https://www.virtualclusters.org), you’ll
be presented with a Login link in the top right of the screen. Click “Login” -
this will take you to a [Globus](https://globus.org) sign-in site.

![step1](../img/screenshot_272.png)

### 2. Sign in to Globus

You will then be asked to sign in with your institutional identity, or your
Globus ID. If you are using the former, simply type in the name of your
institution and click "Continue". Proceed to [Step 3a](#3a-login-with-your-institutional-id).

Otherwise, click "Globus ID to sign in" and proceed to the alternate [Step 3b](#3b-login-with-your-globus-id).

![step2](../img/screenshot_273.png)

### 3a. Login with your institutional ID

You should be presented with a login page for your institutional ID, with your
institution’s branding. Go ahead and sign-in now. Note that your password is not
sent to the VC3 or Globus web portals. Continue to step 4.

![step3a](../img/screenshot_275.png)

### 3b. Login with your Globus ID

Once you have clicked on the "Globus ID to sign in" link, you will be presented with the page below. Please, proceed to log in if you already have a globus account, or click on the upper right link to create a new globus account.

![step3b](../img/screenshot_275b.png)

If you are creating a new account, a short form to fill up will be shown next (see below).

![step3b1](../img/screenshot_275c.png)

 After you have completed this form, a confirmation e-mail with a verification code will be sent. Copy and paste such code and click on the "verify" button.
 
![step3b2](../img/screenshot_275d.png)

After your account is verified, you will be presented with the page below. Click on "Continue" to finalize the sign up process.

![step3b3](../img/screenshot_275e.png)

### 4. Complete or update your VC3 profile

Once you have signed in, you’ll be asked to update or complete your VC3 profile
with information such as your Institution and any other information we cannot
directly extract from your Globus account. Click “Update Profile” once done.

Note that other actions are not allowed until the profile is complete. 

The "SSH KEY" box can be used to upload your public ssh key. 
This will allow you to access your future clusters in the VC3 infrastructure.
Remeber: it is the **public** key what needs to be uploaded. 

If you do not have a public ssh key, 
instructions to create it can be found in the link at the top-right corner. 

![step4](../img/01_profile_edit.png)


### 5. Resources

The VC3 team curates an ever-expanding list of resources for end-users, with a
focus on Campus Clusters, HPC centers, and Cloud resources. You can find these
resources by clicking the “Resources” link on the left panel.

You can also click an individual resource and see expanded information, such as
batch system type, links to documentation, etc.

![](../img/00_resources.png)

### 6. Connect an Allocation

After updating your profile, you can connect an allocation to the VC3 service.
An allocation, in VC3, is defined as combination of a username and resource
target that consumes some type of compute unit - regardless of whether it is
billed as Service Units (many HPC centers), dollars (AWS, GCE), or priority
(HTCondor and other opportunistic systems).

Clicking My Allocations on the left shows all allocations currently associated
with your account. You may select a new one by clicking + New Allocation.

![step5](../img/02_list_allocations.png)

You will be able to select a resource target from the drop down menu, and enter
an account name for the resource. This is the same account name that you use to
SSH to the remote system.

![step5a](../img/03_connect_allocation.png)

Once you’ve connected your allocation, the system will provide a set of instructions you must follow in order to validate it.

![step5b](../img/04_validate_allocation.png)


In order to create a virtual cluster, the VC3 software needs to be able to SSH
to the remote resource. If you click your allocation, you should see a section
titled Public Token.

<!-- ![step5c](../img/05_validated_allocation.png) -->

You will need to add this token to your Unix account, in the file
~/.ssh/authorized_keys. You can either edit this file with your favorite editor
(such as nano, vim, or emacs), or use the echo command to append it to the
authorized keys file.

![step5d](../img/screenshot_282.png)

Note some resources might have special instructions 
in order to add public tokens to your account, in which case a link to 
the resource instructions to follow will be displayed in this section.

This token allows the VC3 system to SSH into a cluster as yourself and submit
jobs on your, or your project’s, behalf.

Once the ssh key has been placed in the resource, click on the "validate" button (step 4).
You will notice the progress bar at the top changes color to green with a message "Ready"
when VC3 has complete the validation. Now the allocation is ready to be used. 

### 7. Defining a Project

VC3, as a platform for cooperative scientific computing, allows you create
projects to share your allocations and virtual clusters with trusted members of
your group, laboratory, or collaboration. To start a new project, click
“Projects” on the sidebar, then click “+ New Project”.

![step6](../img/06_list_projects.png)

You may give your project an aribtrary name and choose initial project members and/or allocations.
You are, by default, member of all your projects.
Once finished, click “Create Project”.

![step6a](../img/07_create_project.png)

You should be returned to the Projects page, where you will be able to see all
of your projects and memberships.
You can access the configuration of each project in the table by clicking on its name.
This way you can add or remove members and/or allocations to a given project.

![step6b](../img/08_return_list_projects.png)

### 8. Creating a Cluster Template

VC3 allows users to create “Cluster Templates” that describe the components of
their virtual cluster, including number of head nodes, worker nodes, etc. We
currently support HTCondor and WorkQueue clusters with dynamic worker nodes,
and fixed head nodes.

To define a new template, click the “Cluster Templates” link on the left panel.
You’ll be able to give your cluster a name, select framework, and number of
workers. Click “Define Cluster” to finish creating the template.

You should be returned to the Cluster Templates page, where you will be able to see all of your templates.
You can access the configuration of each cluster template in the table by clicking on its name.

![step7](../img/09_create_cluster_template.png)

### 9. Environments

An Enviroment is a collection of software packages to be included in a virtual cluster. 
Click on "+New Environment" to create new ones. 
From the "PACKAGE LIST" menu you can select one or more packages to be included in your new Enviroment.

From the second menu you can choose, if needed, the Operative System. If the chosen Operative System
is not available natively, the system will attempt to provide it via containers.
Please, look into the [resources page](https://www.virtualclusters.org/resource) to check if the
resource you are planning to use with this environment has container options available in the 
"features" section (E.g: singularity).

![step8](../img/18_vc_env.png)

Once done, click on the "Create New Enviroment" button.
You should be returned to the Enviroments page, where you will be able to see all of your environments.
You can access the configuration of each environment in the table by clicking on its name.

### 10. Launching a Virtual Cluster

Once you are part of a project, which has associated allocations,
you may launch a Virtual Cluster. In order to do so, click on
"New Virtual Cluster", found in the Virtual Clusters tab from the left nav-menu.

![step8](../img/11_list_vc.png)

From here, you are prompted to select the project that you would like to use in
order to launch a Virtual Cluster. Once selected, press 'Next'.

![step9](../img/12_new_vc_init.png)

Give your Virtual Cluster a name, select the cluster template, environment (optional) and allocation.
You may specify how long you would like your Virtual Cluster to run for. If not
specified, the expiration defaults to 72 hours. Which means that after 72 hours,
your Virtual Cluster will automatically begin to terminate itself.

![step10](../img/13_new_vc_launch.png)

Once launched, you will be redirected to your Virtual Cluster's detailed page,
where you may track the status if your Virtual Cluster. The head node should take
a few moments to configure. Once complete, you will be prompted with instructions
on how to access the head node.

![step11](../img/15_new_vc_profile_pending.png)

![step12](../img/16_new_vc_running.png)

![step13](../img/17_list_vc_running.png)
