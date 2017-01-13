# aws-sipp
## Use Ansible to build an AWS-based SIP test network

### Set up AWS instances

Configure your AWS access key and secret in environment varibles prior
to running Ansible.  For example, you may add the following stanza to
your .bash_profile:
```Shell
export AWS_ACCESS_KEY_ID=accesskey
export AWS_SECRET_ACCESS_KEY=secret
```
cd to one of the project directories, either `ansible-bare-AWS` or 
`ansible-docker-AWS`

To run the playbook to provision the VPC and 2 EC2 instances, run
`ansible-playbook -i inventory provision.yml`
Note that your SSH key from ~/.ssh/id_rsa.pub will automatically be
loaded for ec2-user.

Once the instances are provisioned, to install sipp, or docker,
respectively, run
`ansible-playbook -i ec2.py deploy.yml`

To terminate all instances, run `ansible-playbook -v -i inventory
terminate.yml`

### Testing bare AWS instances

Run the ansible deploy above, and note the external hostnames
displayed.  Make an note of one instance as `client` and one instance as
`server`.  Software on both is identical, so it doesn't matter which
is which.  A future enhancement may be able to designate and list the
servers for you.

1. Open at least 3 terminal windows; in one of them, `ssh
ec2-user@`_client_, and in two others, `ssh ec2-user@`_server_

2. In a terminal window on the server, run `sudo tcpdump -s 0 -w udp.pcap
udp` to capture all UDP traffic. 

3. In the second terminal window on the server, run `ifconfig` and note
the network 10 address displayed, for example
```
eth0      Link encap:Ethernet  HWaddr 0E:BE:9C:62:17:56  
          inet addr:10.2.2.16  Bcast:10.2.2.63  Mask:255.255.255.192
```

4. On that same server, `cd sipp` and run `./sipp-server.sh`.  This will run the server until stopped with a test scenario using a media file.

5. In a terminal window on the client, `cd sipp`.  There are at least two scripted test scenarios:

`./sipp-client-one-call.sh  10.2.2.57` using the IP address of the server you determined in step 3

