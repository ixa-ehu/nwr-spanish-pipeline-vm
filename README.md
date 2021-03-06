

# Introduction #

This is a virtual machine (VM) with all the required modules and NLP processors to run the Spanish pipeline developed within the [Newsreader](http://www.newsreader-project.eu) project for event extraction. The pipeline expects a [NAF](https://github.com/newsreader/NAF) document as input (see for example `docs/input/example.naf`) and produces NAF documents with the annotations of the different linguistic processors.

# Pre-requisites #

The are some pre-requisites the host machine has to fulfil for running the VM inside it:

* Linux operating system (any recent flavor would do it)

* In-kernel KVM virtualization capabilities. You can also determine if your system processor supports KVM by running the following command:

        % grep -E 'vmx|svm' /proc/cpuinfo

  if this command returns output, then your system supports KVM. You also have to verify that the KVM-related feature is enabled in the machine's BIOS.

* 64 bit CPU (x86_64)

# Download the virtual machine #

You need two files for running the VM.

* [Image file](http://ixa2.si.ehu.es/newsreader_resources/newsreaderpublicvm_es.img)
* [XML file](http://ixa2.si.ehu.es/newsreader_resources/newsreaderpublicvm_es.xml)

The first is the VM image with all the required modules installed. The second is an short XML document which is needed for running the VM.

# Running the VM #

## Preliminary steps ##

For running the VM, you need first to do some preliminary steps:

* Install the necessary software into the host machine. On Deban/Ubuntu machines this includes the following packages:

    - qemu-kvm
    - libvirt-bin
    - virt-manager

* Tweak the the XML file:
    - Put the absolute path to the IMG file above in the `file` attribute of `<source>` element (under `<disk>`, around line 22)
    - Currently the VM is configured to use 8Gb RAM. You can change this value by editing around line 4 (`<memory>` and `<currentMemory>` elements).
    - If you experience problems running the VM, maybe you need to change the `<emulator>` element (around line 19) and put the correct path of the kvm emulator executable in your system.

## Running the VM ##

From the host machine, cd to where the IMG and XML documents are and run the following:

    % virsh create newsreaderpublicvm_es.xml
    Domain newsreaderpublicvm_es created from newsreaderpublicvm_es.xml

The machine should be running now. You can test this using the `virsh list` command:

    % virsh list
     Id    Name                           State
    ----------------------------------------------------
     2     newsreaderpublicvm_es          running

Note: The VM needs some time to completely load all the modules and services, so please be patient until the login screen appears.

The IP address of the machine is fixed: **192.168.122.92**

Now you can connect to the VM.

## Connecting via ssh ##

As said before, the IP address of the VM is 192.168.122.92. Thus, being on the host computer, just ssh to the VM using this address and the `newsreader` user:

    % ssh newsreader@192.168.122.92

The password is **readNEWS89**

# Running the pipeline #

Inside the VM, you can run the NewsReader pipeline by executing the following command:

    $ pipeline/run_pipeline.sh docs/input/example.naf > docs/output/example.naf


# Shut down the VM #

Logout from VM and then, from the host computer:

    % virsh destroy newsreaderpublicvm_es

Alternatively, and being on the VM, run the following command:

   $ sudo shutdown -h now

# List of files and directories in th VM #

    + README.md      this file
    + opt/           optional packages
    + components/    the different NLP processors
    + pipeline/      scripts for running the pipeline
    + docs/          directory with documents

# Pipeline components #

The current version of the Spanish pipeline executes the following NLP processors following this specific order:

    components/EHU-tok.v40
    components/EHU-pos.v40
    components/EHU-parse.v40
    components/EHU-time.v30
    components/EHU-nerc.v40
    components/EHU-ukb-es
    components/EHU-ned
    components/EHU-corefgraph.v21
    components/EHU-srl.v30
    components/VUA-eventcoref.v30
    components/EHU-wikify.v30
    components/EHU-topic.v30
