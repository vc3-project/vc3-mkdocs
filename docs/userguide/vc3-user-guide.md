# VC3 User Guide

### Building Your First Virtual Cluster
**Prerequisites**

In order to use VC3, you’ll need an allocation or account with with a supported target resource. These include, but are not limited to:

  - University of Chicago - Research Computing Center
  - University of Notre Dame - Center for Research Computing
  - Brookhaven National Laboratory - Scientific Data & Computing Center
  - Syracuse University - Research Computing
  - Texas Advanced Computing Center
  - NERSC
  - Amazon Web Services
  - Open Science Grid
  - and more!

Institutions and resources are added frequently - be sure to subscribe to our
newsletter and visit [Virtual Clusters](https://www.virtualclusters.org/community)!

### Login or Create Account

When you first visit [Virtual Clusters](https://www.virtualclusters.org), you’ll
be presented with a Login link in the top right of the screen. Click “Login” -
this will take you to a [Globus](https://globus.org) sign-in site.

![step1](../img/screenshot_272.png)

### Sign in to Globus

You will then be asked to sign in with your institutional identity, or your
Globus ID. If you are using the former, simply type in the name of your
institution and click “Continue”. Proceed to Step 3a.

Otherwise, click “Sign in with Globus ID” and proceed to the alternate Step 3b.

![step2](../img/screenshot_273.png)

## Login with your institutional ID

You should be presented with a login page for your institutional ID, with your
institution’s branding. Go ahead and sign-in now. Note that your password is not
sent to the VC3 or Globus web portals. Continue to step 4.

![step3a](../img/screenshot_275.png)

## Login with your Globus ID

(_Alternative step - if you do not have an institutional ID supported by Globus_)

<– Globus ID page –>

## Complete or update your VC3 profile

Once you have signed in, you’ll be asked to update or complete your VC3 profile
with information such as your Institution and any other information we cannot
directly extract from your Globus account. Click “Update Profile” once done.

![step4](../img/01_profile_edit.png)


## Resources

The VC3 team curates an ever-expanding list of resources for end-users, with a
focus on Campus Clusters, HPC centers, and Cloud resources. You can find these
resources by clicking the “Resources” link on the left panel.

You can also click an individual resource and see expanded information, such as
batch system type, links to documentation, etc.

![](../img/00_resources.png)

## Connect an Allocation

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

This token allows the VC3 system to SSH into a cluster as yourself and submit
jobs on your, or your project’s, behalf.

## Defining a Project

VC3, as a platform for cooperative scientific computing, allows you create
projects to share your allocations and virtual clusters with trusted members of
your group, laboratory, or collaboration. To start a new project, click
“Projects” on the sidebar, then click “+ New Project”.

![step6](../img/06_list_projects.png)

You may give your project an aribtrary name and choose initial project members and/or allocations.
Once finished, click “Create Project”.

![step6a](../img/07_create_project.png)

You should be returned to the Projects page, where you will be able to see all
of your projects and memberships.

![step6b](../img/08_return_list_projects.png)

## Creating a Cluster Template

VC3 allows users to create “Cluster Templates” that describe the components of
their virtual cluster, including number of head nodes, worker nodes, etc. We
currently support HTCondor and WorkQueue clusters with dynamic worker nodes,
and fixed head nodes.

To define a new template, click the “Cluster Templates” link on the left panel.
You’ll be able to give your cluster a name, select framework, and number of
workers. Click “Define Cluster” to finish creating the template.

![step7](../img/09_create_cluster_template.png)

## Launching a Virtual Cluster

Once you are apart of a project, which has associated allocations,
you may launch a Virtual Cluster. In order to do so, click on
"New Virtual Cluster", found in the Virtual Clusters tab from the left nav-menu.

![step8](../img/11_list_vc.png)

From here, you are prompted to select the project that you would like to use in
order to launch a Virtual Cluster. Once selected, press 'Next'.

![step9](../img/12_new_vc_init.png)

Give your Virtual Cluster a name, select the cluster template and allocation.
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
