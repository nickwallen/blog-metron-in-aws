Apache Metron - First Steps
===================================

**TODO** - What is Metron?  Background?

These instruction will take you step-by-step from nothing to a fully-functioning, multi-node Apache Metron cluster running in [Amazon's EC2](https://aws.amazon.com/ec2) environment.  The platform will capture live network packets from the wire, leverage best-in-class open source tooling as additional sources of telemetry, and perform real-time enrichment of that data.  All of this data is then persisted for advanced analytical modeling and presented within a fully customizable, searchable single pane of glass.

**TODO**: Table of Contents

Getting Started
---------------

Apache Metron depends on many excellent third-party, open-source components.  To see Apache Metron in action all of these components need to be deployed, configured and connected.  To that end, the Apache Metron team has tried to automate as much of the deployment process as possible.  

### Amazon

If you already have an Amazon Web Services account that you have used to deploy EC2 hosts within a VPC, then you are ready to go and skip over the next few steps.

1. Head over to [Amazon Web Services](http://aws.amazon.com/) and create an account, if you have not already done so.  As part of the account creation process you will need to provide a credit card to cover any charges that may apply.  

2. Create a set of user credentials through [Amazon's Identity and Access Management (IAM) ](https://console.aws.amazon.com/iam/) dashboard.  On the IAM dashboard menu click "Users" and then "Create New User". Provide a name and ensure that "Generate an access key for each user" remains checked.  Download the credentials and keep them for use later in these instructions.

3.  While still in [Amazon's Identity and Access Management (IAM) ](https://console.aws.amazon.com/iam/) dashboard, click on the user that was previously created.  Click the "Permissions" tab and then the "Attach Policy" button.  Grant the following two policies to this user.

  ```
  AmazonEC2FullAccess
  AmazonVPCFullAccess
  ```

4. Apache Metron uses the [official, open source CentOS 6](https://aws.amazon.com/marketplace/pp/B00NQAYLWO) Amazon Machine Image (AMI).  If you have never used this AMI before then you will need to accept Amazon's terms and conditions.  Navigate to the [AMI page](https://aws.amazon.com/marketplace/pp/B00NQAYLWO), click the Continue button.  Choose the "Manual Launch" tab then click the "Accept Software Terms" button.

Software Prerequisites
----------------------

Hopefully you will find that the most difficult tasks are complete.  The computer used to deploy Apache Metron will need to have [Ansible](https://github.com/ansible/ansible), Python, Maven and Git installed.

### Mac OS X

The easiest way to install these tools on a Mac is to use the excellent [Homebrew](http://brew.sh/) project.

1. Install Homebrew by running the following command in a terminal.  Navigate to [Homebrew](http://brew.sh/) for the latest installation instructions.

  ```
  /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
  ```

2. With Homebrew installed, run the following command in a terminal to install all of the required tools.

  ```  
  brew install ansible brew-pip maven git
  ```

3. Ensure that a public SSH key is located at `~/.ssh/id_rsa.pub`.  If this file does not exist, run the following command at a terminal and accept all defaults.  Only the public key, not the private key, will be uploaded to Amazon and configured on each host to enable SSH connectivity.  While it is possible to create and use an alternative key those details will not be outlined in these instructions.  

  ```
  ssh-keygen -t rsa
  ```

### Windows
1. **TODO**
2. **TODO**
3. **TODO**

### Linux
1. **TODO**
2. **TODO**
3. **TODO**

Deploy Apache Metron
--------------------

1. Build the latest Apache Metron code using the following commands in a terminal.

  ```
  git clone http://github.com/apache/incubator-metron.git
  cd incubator-metron/metron-streaming
  mvn package -DskipTests
  ```

2. Export the previously generated user credentials in an environment variable.

  ```
  export AWS_ACCESS_KEY_ID="AKIAI6NRFEO27E5FFELQ"
  export AWS_SECRET_ACCESS_KEY="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"
  ```

3. Start the Apache Metron deployment process.  The process is likely to take between 70-90 minutes.  Fortunately, everything is fully automated and you should feel free to grab a coffee, read a book, or practice yoga.  

  ```
  cd ../deployment/amazon-ec2
  export EC2_INI_PATH=conf/ec2.ini
  ansible-playbook -i ec2.py playbook.yml
  ```

Explore Apache Metron
---------------------

Once the deployment process is complete the terminal will display a set of useful links for exploring Apache Metron.  First, navigate to the Metron Dashboard which is at http://ec2-52-37-255-142.us-west-2.compute.amazonaws.com:5000 in the example below.

  ```
  TASK [debug] *******************************************************************
  ok: [localhost] => {
      "Success": [
          "Apache Metron deployed successfully",
          "   Metron  @  http://ec2-52-37-255-142.us-west-2.compute.amazonaws.com:5000",
          "   Ambari  @  http://ec2-52-37-225-202.us-west-2.compute.amazonaws.com:8080",
          "   Sensors @  ec2-52-37-225-202.us-west-2.compute.amazonaws.com on tap0",
          "For additional information, see https://metron.incubator.apache.org/'"
      ]
  }
  ```

Each of the provisioned hosts will be accessible from the internet. Connecting to one over SSH as the user `centos` will not require a password as it will authenticate with your SSH key.

  ```
  ssh centos@ec2-52-91-215-174.compute-1.amazonaws.com
  ```

**TODO** - Describe current Kibana panels and what they mean

Customize Apache Metron
-----------------------

While the defaults provided by Metron are useful, the true power of the dashboard comes from its customizability.  Let's uncover where in the world the talkers on our network are located.  

1. Add a new panel.  On the far left side of the dashboard hover over the green tab and click the plus (+) icon once it becomes visible.

2. For the panel type choose "Map" and give your map a title.  For "Field" enter `enrichments.geo.ip_src_addr.country`.

**TODO** Add another intersting panel

Next Steps
----------

**TODO** - Call to action
