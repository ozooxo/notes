# Chef Notes

+ A configuration management technology
+ A set of automation tools

Automating the following aspects:

+ `Chef`: Infrastructure automation
    + Define infrastructure policy as code
+ `InSpec`: Compliance automation
    + For compliance/security process
    + Translating compliance policies to code
+ `Habitat`: Applications automation

Approaches:

+ Test
+ Repair

Features:

+ Build infrastructure as code
    + Pros: more maintainable, versionable, testable, and collaborative.
+ Cloud integration (for distributing infrastructure on multi-cloud environment):
    + Through `knife` utility.

3-tier client server model:

+ Workstation:
    + Configuration and development. Manage server remotely using `Chef`
    + The OS doesn't need to be the same as the Chef server.
+ Chef server:
    + Central repository of the Chef code
        + Development under `Chef DK`
    + Save information about every node
    + May use `Chef automate` (a web-based UI) to (1) host the Chef server, (2) provide automated build pipelines.
+ Node:
    + Actual machines managed by Chef server.
    + May have different configurations.
        + Different nodes may run different versions of Chef cookbook.
    + Run `chef-client` which downloads/runs the code from the Chef server to bring itself up-to-date configurations.
        + Modes:
            + Chef clients shall be setup in "daemon mode", to run per-... period of time.
            + For simplification/testing reasons, `chef-client` may be run in the so-called `--local-mode` to apply chef code on the Chef server.
        + `chef-client` does test and repair, i.e., if the content didn't change, the configuration will not be modified again. Also, Chef restored the original configuration if it is being changed from the outside.

Other associated modules/tools:

+ `knife`: Chef’s command-line tool to interact with the Chef server from the workstation.
+ `solo`: allow guest machines to use Chef cookbooks without any Che client/server configuration.
+ Chef-shell: local interactive session for simple testing (before the remote testing/debugging with long feedback cycle).
+ Foodcritic: Flagging in Chef cookbook.
+ `ChefSpec`: Simulate a Chef run. Mostly used for TDD/unit test.
+ `Test Kitchen`: Chef's integrated testing framework. Provide a temporary environment that helps to run and test cookbooks.

Components:

+ Cookbook: created by (1) chef command, (2) knife utility
+ Role: a logically way to group nodes.
    + Example roles: web server/database server/load balancer...
+ Environment:
    + Example environments: development/testing/production/...

Component relationship:

+ A **cookbook** contains multiple **recipes**.
+ A **recipe** contains multiple **resources**.
+ A **cookbook** contain multiple **attributes** for basic settings.
+ A **resource** may be a static **files**, a non-static **templates** (in `.ebr` format), or a package which will be later on placed on the nodes.
+ Metadata of the developed package (names, version, ...) are saved in **metadata.rb**.
    + Version is manually written, but should be update for every cookbook changes.

## Commands

### From Workstation

`knife cookbook upload [cookbook-name]`

`berks install`: using setups provided by Chef Supermarket.

`berks upload --no-ssl-verify`

Templates.

May have roles (under the `roles` directory) using `json` configs. Then `knife role from file roles/customized_role_name.json`. Each role may have its independent run list of recipes.

`knife role list`

### From Chef server

`knife node list`/`knife node show customized-node-name`

Chef Automate server is built on top of it.

May clean up the environments by manually deleting nodes (still need to tear down the instance), cookbooks (all versions/particular version), and/or roles.

### From node

`knife bootstrap`

Can be authorized using:

+ Key-based: refer to the private key file `*.pem`
+ Password
+ Forwarded port (for local VM)

`knife ssh`: update configuration

May use the `chef-client` cookbook to automatically update notes. Trigger the `chef-client` by `knife ssh 'role:customized_role_name' 'sudo chef-client' --ssh-user ubuntu --identity-file ~/.ssh/private_key --attribute ipaddress`

## Overlap with/competitors

+ Puppet: deliver and operating software.
+ Ansible: automation/deployment.
+ SaltSatck: data-driven configuration/infrastructure management.
+ Fabric

## Postscripts

Chef itself is really like a web service. Chef server is the web server which generated dynamic config files from templates (like generate dynamic HTML from PHP/JSP/...). Chef notes are the web clients which query data from the Chef server, and get the static config files (like static HTML web clients get).
