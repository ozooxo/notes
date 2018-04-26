# Chef Notes

A set of automation tools for automating the following aspects:

+ `Chef`: Infrastructure automation
    + Define infrastructure policy as code
+ `InSpec`: Applications automation
+ `Habitat`: Compliance automation 

Approaches:

+ Test
+ Repair

Setup involve three parts:

+ Workstation: manage server remotely using `Chef`
    + The OS doesn't need to be the same as the Chef server.
+ Chef server:
    + Central repository of the Chef code
        + Development under `Chef DK`
    + Save information about every node 
    + May use `Chef automate` to (1) host the Chef server, (2) provide automated build pipelines.
+ Node
    + Run `Chef client` which downloads/runs the code from the Chef server to bing itself up-to-date configurations.