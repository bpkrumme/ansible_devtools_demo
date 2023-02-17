# ansible_devtools_demo

This is a demo which provides an overview of the developer tools included with Ansible Automation platform 2.3+

The following sections are a suggested runbook for performing the demonstration and follow the PDF slide deck included in the repository

## Set up the Demo environment

There is an expectation that if you present this demo you have an understanding of Ansible and either have a demo environment or can create one.  There are no playbooks or automation included to configure a lab environment.

1. You will need one or more hosts to automate against.  You can do this in your own lab environment, or using a hyperscaler, your choice
2. Update the `demo_inventory/hosts` inventory file and files in the `group_vars` and `host_vars` directories to reflect your demo environment
3. Update the `ansible.cfg` to meet your needs, especially the `remote_user` parameter and your token for pulling collections from Automation Hub
4. Test ad-hoc commands against your demo environment to make sure it works before performing the demo
5. You should run ansible-lint prior to doing the demo to cache collections for the linter to work quickly
6. Podman must be installed and configured on your demo control node
7. Log into the `registry.redhat.io` container registry for pulling EE builder images

        $ podman login registry.redhat.io

## Demonstrate use of the Ansible VS Code extension

Demo steps:

1. Create `apache_install.yml` playbook in the root directory
2. Build out the base structure of a playbook
    1. Intentionally use a lowercase name for the play, signaling ansible-lint
    2. explain that ansible-lint is integrated with VS Code Extension when you have both installed
3. Add tasks, making note to attendees of syntax suggestions and highlighting
    1. Install httpd package using yum or dnf module (USE FQCN)
        1. Place mouse pointer over FQCN
        2. Show live documentation
    2. Enable httpd service using service module (not FQCN)
    3. copy index.html using copy module (not FQCN)
4. Point out the red squiggly syntax identifiers and explain that this is because we have ansible-lint installed as well, so we're getting live syntax-checking.  This is a good time to move on to ansible-lint
5. Do not run the playbook, it will be done later in the ansible-navigator section

## Demonstrate direct usage of Ansible Lint

Demo steps:

1. Open a terminal in VS Code
2. run `ansible-lint apache_install.yml` in the terminal window.
3. Show list of the rule violations
    1. Highlight link to documentation for each rule (red rule name)
    2. Each violation has a summary of the detected rule violation
    3. If in VS Code, highlight link to open file in editor (blue file name)
4. Show documentation link for instructions to ignore rules
5. Show rule violation summmary which includes
    1. count of the times a particular rule is violated
    2. tag of the rule
    3. which rule profile
    4. any associated rule tags

## Demonstrate Ansible Navigator

1. Show ansible-navigator configuration options
    * In our home directory

            ~/ansible-navigator.yml

    * In our project directory

            ./ansible-navigator.yml

    * As an environment variable

            ANSIBLE_NAVIGATOR_CONFIG=<file path>

2. Explain that the configuration can use either `YAML` or `JSON` format and must use the appropriate extension.  For `YAML` format, this must be either `.yaml` or `.yml` and for `JSON` it must be `.json`
3. Show our current configuration file in the project directory
4. Launch ansible-navigator in VS Code terminal
    1. Interactive Overview - Explain: The text-based interface uses a vim-like command structure, so to we type a colon followed by the menu we want to view
        1. Collections

                :collections

            1. This shows us the collections available in the current execution environment - more on EEs later
            2. View the ansible.posix collection by typing the number next to the collection
                1. If number is double-digit, we use the same command structure as previous menus such as `:<X>
                2. Show list of collection components
            3. Deeper look into collection component `ansible.posix.sysctl` by typing `:<X>` next to the module
                1. Explain that this is how we can see documentation without a web browser
                2. We can navigate to previous or next component in collection with `-` or `+`
            4. `Esc` key takes us back to the previous page, it takes multiple presses to get back to the welcome screen
        2. Docs - There's another way to view documention if we know the fqcn
            1. Navigate directly to `ansible.builtin.dnf` with

                    :doc ansible.builtin.dnf

            2. We can do this from anywhere in the ansible-navigator TUI
                1. `Esc` takes us back to the previous page...not to the parent menu
        3. Inventory
            1. Open our inventory with

                    :inventory -i demo_inventory/hosts

            2. Show browsing groups
            3. Show browsing hosts
                1. Show host details
        4. Images
            1. `Esc` back to Welcome screen and open Images menu with

                :images

            2. Explain that this is how we can interact with our EE's, but the menu shows all of the container images on our system whether they are an EE or not.
            3. Point out image name, tag, wheither it's an EE or not
            4. More on EE's in a few moments
        5. Run - We can also run playbooks from within ansible-navigator TUI
            1. run `apache_install.yml` playbook with

                    :run playbooks/apache_install.yml

            2. Show play list, point out summary and progress section
            3. Descend into play showing task list, point out result for each task, host it was run on, task name, and task action
            4. Show task details for a task other than gather_facts, point out module arguments, and msg field
5. CLI Overview
    1. Explain that commands can be run from the CLI directly
    1. Explain that there are two modes
        1. Interactive mode
            1. This is the default
            2. Takes us into the TUI even if we run a specific command
        2. CLI Mode
            1. Most common to run playbooks and syntax is slightly different from `ansible-playbook`
            2. We must add the mode switch `-m stdout` to the command

                    $ ansible-navigator run <playbook> -m stdout

            3. This gives us the same output we would expect from `ansible-playbook`
            4. We can use stdout for other commands as well, when used for Docs it gives us the same output we would expect from `anisble-doc`

## Demonstrate Ansible Builder

1. Show our `configure_base_system.yml` playbook which includes roles from the `redhat.rhel_system_roles` collection
    1. We need an Execution Environment with this collection in order to run the playbook, and so we can provide our runtime to AAP Platform Operators
2. Explain that this collection is a certified and supported collection that we can get from Automation Hub
3. Show the `ansible.cfg.example` ansible configuration file with the token configuration for Automation Hub
4. Show the `demo_ee/execution-environment.yml
    1. base image and builder image which are pulled from the `registry.redhat.io` container registry
    2. ansible.cfg which sets our ansible configuration inside the Execution Environment
    3. requirements.yml which defines the collections we want available in the EE
        1. This includes requirements for the playbook I showed, but also some requirements for automations we are going to do in the future, so I've included those as well
    4. requirements.txt which defines the python libraries we need to support our collections
        1. These are some of the requirements for the amazon.aws collection
    5. bindep.txt which defines the binary packages we need installed in the EE to support additional functionality
5. Demonstrate building the Execution environment using anisble-builder
    1. Change directory to `demo_ee` directory

            $ cd demo_ee

    2. Run the `ansible-builder` command to build the Execution Environment

            $ ansible-builder build -t demo_ee -v 3 --prune-images

6. Now we can configure ansible-navigator to use our Execution Environment
    1. Modify `ansible-navigator.yml` removing the coment on the `image` line
    2. Explain that this sets our default execution environment, but we could also set this on the CLI when we launch Ansible Navigator

            $ ansible-navigator --execution-environment-image demo_ee

7. Let's run this playbook for our base configuration using `ansible-navigator` and see what happens.
    1. Show the inventory variables in `group_vars` to show that we have a default configuration we want
    2. Show the inventory variables in `host_vars` to show that we have one host that we want to be different
    3. Run the playbook

            $ ansible-navigator run configure_base_system.yml

        OR

            $ ansible-navigator run --execution-environment-image demo_ee configure_base_system.yml