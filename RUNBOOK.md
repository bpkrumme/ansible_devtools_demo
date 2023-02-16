# ansible_devtools_demo

<p>This is a demo which provides an overview of the developer tools included with Ansible Automation platform 2.3+<br>
The following sections are a suggested runbook for performing the demonstration</p>

## VS Code Extension and Ansible Lint

### Demonstrate installation of the Ansible VS Code extension

To get started with the ansible developer tools, it makes sense to start with the VS Code extension, which is available in the extensions menu of Visual Studio code.  To install the extension, all we need to do is open the extensions menu by clicking the left pane of VS Code, or with Ctrl+Shift+X

The Ansible VS Code extension expects ansible-lint to be installed on your system, so we should install that also.

To install ansible-lint on Red Hat Enterprise Linux with the Ansible Automation Platform subscription:

    sh$ sudo dnf -y install ansible-lint

Alternatively, to install ansible-lint on any other system, we use pip:

    sh$ python3 -m pip install ansible-lint

Now, lets write a playbook and see what happens when we add parameters to a play

## Ansible Navigator

Ansible Navigator is a command-line and text-based user interface for interacting with our automation.  It allows us to browse collections we have installed, see the execution environments we have available and their contents, view documentation, and run playbooks.  The focus of Ansible Navigator is providing a single interface for content creators to interact with their environment without the need for multiple windows, browser tabs, or terminals.

### Installation

To get started, we need to install ansible-navigator.

To install ansible-navigator on Red Hat Enterprise Linux with the Ansible Automation Platform subscription

    sh$ sudo dnf -y install ansible-navigator

Alternatively, to install ansible-navigator on any other system, we use pip

    sh$ python3 -m pip install ansible-navigator

### Configuration

Ansible Navigator has its own configuration file where we can set the configuration for our environment.  It looks for a configuration file in several places.

* In our home directory

        ~/ansible-navigator.yml

* In our project directory

        ./ansible-navigator.yml

* As an environment variable

        ANSIBLE_NAVIGATOR_CONFIG=<file path>

The configuration can use either `YAML` or `JSON` format and must use the appropriate extension.  For `YAML` format, this must be either `.yaml` or `.yml` and for `JSON` it must be `.json`

### TUI Navigation

Let's take a look at the ansible-navigator interface now.

When we launch ansible-navigator, by default we get a Welcome screen.  This is the primary menu we can use to navigate our automation environment.

#### Collections

Let's start by taking a look at the collections we have available.  The text-based interface uses a vim-like command structure, so to look at the collections we have available we type a colon followed by the item we want to view like:

    :collections

Here we can see the collections which are available in the execution environment we have configured.  In this case, we're using the EE which comes with ansible-navigator, called `creator-ee`.  I'll show you a bit later how we can configure the EE we use by default.

This execution environment includes several common collections which are used by automation creators.  We can view the contents of a collection by typing the number which appears next to the collection name.  Let's take a look at the ansible.posix collection, so that means we type the number <X>.  Here we can see all of the contents of the ansible.posix collection and what kind they are.  Let's take a deeper look into one of the modules.  `sysctl` is a good choice.  A quick note:  If the number is double-digits, we would use the `:<number>` command format to select it, in this case it would be `:<X>`

As you can see, when we select a component of a collection, we get all of the critical details about that component.  This includes the documentation for the module.  You can cycle through each of the components of the collection from here by pressing the `+` or `-` keys and it will go to either the previous or next component for the collection.

#### Docs

For documentation, there is another way if you know the module or plugin you want to get documentation for, so you don't have to traverse the `:collections` menu.  In order to see this, we can either press `Esc` to go back to our Welcome menu, or we can enter the command directly from anywhere in Ansible Navigator.  Note that each time you press `Esc` it takes you back to the previous menu, so you may need to press `Esc` multiple times.

From the Welcome menu, you can see we have the `:doc` which we can use to view a specific module or plugin by name.  Let's choose one from the `ansible.builtin` collection like the `yum` module.  To open the documentation for that module directly, we would type a `:doc` followed by the module name.  It's best practice to use the fully qualified name, so we're sure that it's the specific module we want.  It would look like this:

    :doc ansible.builtin.yum

This takes us directly to the documentation for the yum module.

#### Images

The `:images` menu is how we can interact with the execution environments we have available in our environment, either the default ones or EE's we have created.  Let's start with:

    :images

Because an execution enviroment is a container image, here you will see all of the container images in your environment, similar to what you would see using `podman images` or `docker images` and their associated tags, creation timeframe, size, and most importantly an indication whether the image is an execution environment.

If you notice, there is some highlighting of the lines.  Here is what they mean:

<span style="color:magenta">
Magenta signifies that this is the execution environment currently configured and in use
</span>

<span style="color:green">
Green signifies that this is an available execution environment
</span>

The other items in the list, which you'll see are grayed out are container images which are not an execution environment.

We can see the contents of any of our execution environments from the `:images` menu as well, including the collections installed and documentation for those collections.

## Ansible Builder

Let's step away from ansible-navigator for a moment and talk about execution environments.  If you recall from the presentation, I mentioned that Execution Environments are container images which include all of the things we need to run automations using ansible.  They include ansible-core, or ansible-engine 2.9, any collections we use for our automations, and the python and binary dependencies we need for those collections.  All of those things get built into a container image which we can use as an automation creator to develop and test our automations.  Just as important, because an excecution environment is a container image, it is portable, so we can hand it over to the automation managers or operators so they are able to scale our automation for the enterprise using Ansible Automation Platform.

We don't, and can't expect automation creators or developers to 

### Explain installation of ansible-builder

To install ansible-builder on Red Hat Enterprise Linux with the Ansible Automation Platform subscription

    sh$ sudo dnf -y install ansible-builder

Alternatively, to install ansible-builder on any other system, we use pip

    sh$ python3 -m pip install ansible-builder

### Show ee configuration file

### Show requirements files

### Show ansible.cfg

### Demonstrate ansible-builder build

We can build our execution environment using ansible-builder build

    sh$ ansible-builder build -t <ee_tag> 
