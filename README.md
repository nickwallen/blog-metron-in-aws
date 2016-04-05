Apache Metron - First Steps in the Cloud
========================================

Apache Metron integrates a variety of open source technologies to offer a centralized platform for cyber security monitoring, analysis, and threat detection.  The platform provides full packet capture indexing, telemetry aggregation and enrichment, advanced behavioral analytics, and the ability to tailor the platform to the specific threats facing your organization.

These instruction will take you step-by-step from nothing to a fully-functioning, multi-node Apache Metron cluster running in [Amazon's EC2](https://aws.amazon.com/ec2) cloud environment.  The platform will capture live network packets from the wire, leverage best-in-class open source tooling as additional sources of telemetry, and perform real-time enrichment of that data.  The data is persisted for advanced analytical modeling and presented within a fully customizable, searchable single pane of glass.

Getting Started
---------------

Apache Metron depends on many excellent third-party, open-source components.  To see Apache Metron in action, all of these components need to be deployed, configured and connected.  To that end, the Apache Metron team has automated as much of the deployment process as possible.  

### Amazon Web Services

If you already have an Amazon Web Services account that you have used to deploy EC2 hosts then you should be able to skip the next few steps.

1. Head over to [Amazon Web Services](http://aws.amazon.com/) and create an account.  As part of the account creation process you will need to provide a credit card to cover any charges that may apply.

2. Create a set of user credentials through [Amazon's Identity and Access Management (IAM) ](https://console.aws.amazon.com/iam/) dashboard.  On the IAM dashboard menu click "Users" and then "Create New User". Provide a name and ensure that "Generate an access key for each user" remains checked.  Download the credentials and keep them for later use.

  ![Download credentials](images/aws-create-user-credentials.png)

3.  While still in [Amazon's Identity and Access Management (IAM) ](https://console.aws.amazon.com/iam/) dashboard, click on the user that was previously created.  Click the "Permissions" tab and then the "Attach Policy" button.  Attach the following policies to the user.

  - AmazonEC2FullAccess
  - AmazonVPCFullAccess

  ![Download user credentials](images/aws-attach-policy.png)

4. Apache Metron uses the [official, open source CentOS 6](https://aws.amazon.com/marketplace/pp/B00NQAYLWO) Amazon Machine Image (AMI).  If you have never used this AMI before then you will need to accept Amazon's terms and conditions.  Navigate to the [web page for this AMI](https://aws.amazon.com/marketplace/pp/B00NQAYLWO) and click the "Continue" button.  Choose the "Manual Launch" tab then click the "Accept Software Terms" button.

  ![Accept AMI terms](images/aws-accept-ami-terms.png)

Having successfully created your Amazon Web Services account, hopefully you will find that the most difficult tasks are behind us.  

### Software Prerequisites

The computer used to deploy Apache Metron will need to have [Ansible](https://github.com/ansible/ansible), Python, Maven, SSH, and Git installed.  Any platform that supports these tools is suitable, but the following instructions cover only Mac OS X.  The easiest means of installing these tools on a Mac is to use the excellent [Homebrew](http://brew.sh/) project.

1. Install Homebrew by running the following command in a terminal.  Refer to the  [Homebrew](http://brew.sh/) home page for the latest installation instructions.

  ```
  /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
  ```

2. With Homebrew installed, run the following command in a terminal to install all of the required tools.

  ```  
  brew install ansible brew-pip maven git
  ```

3. Ensure that a public SSH key is located at `~/.ssh/id_rsa.pub`.  

  ```
  $ cat ~/.ssh/id_rsa.pub
  ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQChv5GJxPjR39UJV7VY17ivbLVlxFrH7UHwh1Jsjem4d1eYiAtde5N2y65/HRNxWbhYli9ED8k0/MRP92ejewucEbrPNq5mytPqdC4IvZ98Ln2GbqTDwvlP3T7xa/wYFOpFsOmXXql8216wSrnrS4f3XK7ze34S6/VmY+lsBYnr3dzyj8sG/mexpJgFS/w83mWJV0e/ryf4Hd7P6DZ5fO+nmTXfKNK22ga4ctcnbZ+toYcPL+ODCh8598XCKVo97XjwF5OxN3vl1p1HHguo3cHB4H1OIaqX5mUt59gFIZcAXUME89PO6NUiZDd3RTstpf125nQVkQAHu2fvW96/f037 nick@localhost
  ```

  If this file does not exist, run the following command at a terminal and accept all defaults.  Only the public key, not the private key, will be uploaded to Amazon and configured on each host to enable SSH connectivity.  While it is possible to create and use an alternative key those details will not be covered.  

  ```
  ssh-keygen -t rsa
  ```

### Deploy Apache Metron

1. Build the latest Apache Metron code using the following commands in a terminal.

  ```
  git clone http://github.com/apache/incubator-metron.git
  cd incubator-metron/metron-streaming
  mvn package -DskipTests
  ```

2. Export the previously generated user credentials in an environment variable.  Update the commands below to reflect the access credentials that were previously created for the Apache Metron deployment.

  ```
  export AWS_ACCESS_KEY_ID="AKIAI6NRFEO27E5FFELQ"
  export AWS_SECRET_ACCESS_KEY="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"
  ```

3. Start the Apache Metron deployment process.  The process is likely to take between 70-90 minutes.  Fortunately, everything is fully automated and you should feel free to grab a coffee.

  ```
  cd ../deployment/amazon-ec2
  export EC2_INI_PATH=conf/ec2.ini
  ansible-playbook -i ec2.py playbook.yml
  ```

Most transient issues can be resolved by simply re-running the playbook.  The following errors may occur due to misconfiguration of your Amazon Web Services account.  Each of these problems and more are covered in the [README](https://github.com/apache/incubator-metron/blob/master/deployment/amazon-ec2/README.md#common-errors) document.  

  - ['No handler was ready to authenticate...Check your credentials'](https://github.com/apache/incubator-metron/blob/master/deployment/amazon-ec2/README.md#error-no-handler-was-ready-to-authenticatecheck-your-credentials)
  - ['PendingVerification: Your account is currently being verified'](https://github.com/apache/incubator-metron/blob/master/deployment/amazon-ec2/README.md#error-pendingverification-your-account-is-currently-being-verified)
  - ['OptInRequired: ... you need to accept terms and subscribe'](https://github.com/apache/incubator-metron/blob/master/deployment/amazon-ec2/README.md#error-optinrequired--you-need-to-accept-terms-and-subscribe)
  - ['Instance creation failed => InstanceLimitExceeded'](https://github.com/apache/incubator-metron/blob/master/deployment/amazon-ec2/README.md#error-instance-creation-failed--instancelimitexceeded)

Explore Apache Metron
---------------------

Once the deployment process is complete the terminal will display a set of useful links for exploring Apache Metron.  

  ![Deployment complete](images/deploy-complete.png)

Multiple hosts have been instantiated to run Apache Metron.  To view or manage these hosts login to the Amazon EC2 Dashboard.  Each of the hosts has a tag named `env` which is defined as `metron-test` by default.  This tag can be used to filter the visible hosts within the Amazon EC2 Dashboard.

  ![EC2 Dashboard](images/ec2-dashboard.png)

Each of the provisioned hosts are fully accessible from the internet. Connecting to one over SSH as the user `centos` will not require a password and will authenticate with your SSH key.

  ```
  ssh centos@ec2-52-91-215-174.compute-1.amazonaws.com
  ```

Explore the Apache Metron Dashboard.  In this example, the Metron dashboard was installed at http://ec2-52-37-255-142.us-west-2.compute.amazonaws.com:5000, but the specific URL will differ.  A direct link for your environment can be found in the terminal output.

The default Metron deployment is configured to produce enough telemetry data to validate basic functioning of the platform.  It is not intended to serve as a production environment.  When deployed in this manner, Metron transmits dummy network packet data over a virtual network interface to mimic what might occur in a live production environment.  Each of the default sensors, including [Bro](https://www.bro.org), [Yaf](https://tools.netsa.cert.org/yaf), and [Snort](https://www.snort.org), consume this raw packet data, perform their own unique analyses, and forward on the results.  All of this telemetry is then aggregated, enriched, and indexed by Apache Metron.  The indexed data is presented within a fully customizable, searchable single pane of glass.

  ![Metron dashboard](images/metron-dashboard.png)

Next Steps
----------

For more information and to get involved join us at https://metron.incubator.apache.org.
