# AWS Command Line Interface

Partially based on <https://www.youtube.com/watch?v=8rjRfW4JM2I>.

1. Install aws-cli through pip or through your linux distribution's package manager.
2. Create new user through the [IAM console](https://console.aws.amazon.com/iam/home?#home):
    - Check the boxes for "programmatic access" and "AWS management console access".
    - After the user is created, save the user's access key ID and secret access key somewhere.
    - Then click on the user and attach the "AdministratorAccess" policy to this user.
3. Run `aws configure`:
    - Put in the newly created users AWS credentials.
    - For default region choose the region where you have the required resource limits (for me `us-west-2`).
    - For default output format choose `text`.

# AWS Deep Learning AMI

* Basic info: <https://aws.amazon.com/marketplace/pp/B077GF11NF>
* Walk-through with screenshots: <https://aws.amazon.com/blogs/machine-learning/get-started-with-deep-learning-using-the-aws-deep-learning-ami/>
* How to use: <https://docs.aws.amazon.com/dlami/latest/devguide/tutorial-conda.html>
* p2.xlarge seems like a good choice (0.9$ / hour, 1x NVidia K80)

## Create and launch an instance

0. From the EC2 console create a *security group* (on Inbound open port 8888 for all sources in order to access Jupyter notebooks; and open port 22 to be accessed by 0.0.0.0/0 for SSH) and a *key pair* to be used with the instance / instances.
1. Go to the EC2 console.
2. Change your region (top right) if it's not the correct region.
3. Choose "Launch instance", or "Spot Requests" for spot instance.
4. Choose "AWS Marketplace" for regular instances, or "Search AMI" for spot instances.
5. Search for "Deep Learning AMI (Ubuntu)".
6. Configure the instance:
    * For regular instance: Select (check prices) -> Continue (pick `p2.xlarge`), Configure Instance Details, Add Storage, Configure Security Group -> Review and Launch -> Launch.
    * For spot instance: select security group and keys.

### Keep your data with spot instances

* Choose to terminate the EBS volume associated with the instance when the instance is terminated.
* To keep your data, regularly save a snapshot image of the instance while it's running (see below on how to).
* Create new spot instances based on the last saved snapshot.
* For code backups, push to GitHub; no need to save new snapshot images of the instance.

## Connect to the instance

Based on <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html> and <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html>

1. After the instance has launched click on "View Instances", and get the ID and DNS name of the instance for the page that appears.
2. From the directory where the private key is located do:

```
chmod 400 my-key-pair.pem
ssh -i my-key-pair.pem ubuntu@<Your instance DNS>
```

* Install the necessary tools with `sudo apt ...`; if `apt` does not work, try <https://stackoverflow.com/questions/44782484/aws-apt-install-error-could-not-get-lock-var-lib-dpkg-lock-open-11-resour>.
* Preferably run everything within tmux, so you can detach it if ssh needs to be disconnected for some time.

### Jupyter

* Once connected, you can start a jupyter notebook with:

```
source activate python3
# or something other than python3, see README for other options
jupyter notebook
```

* Open a tunnel between your client and the remote EC2 instance:

```
ssh -i ~/mykeypair.pem -L 8157:127.0.0.1:8888 ubuntu@ec2-###-##-##-###.compute-1.amazonaws.com
```

* Now you can access the Jupyter notebook server at `https://127.0.0.1:8157` (or `http`?).

## Keep an image to start another instance in the future

1. In the EC2 interface right-click on the instance and "create image"; images appear under "Images" -> "AMI".
2. Terminate instance.
3. Start a new instance from the saved image.

### To delete an AMI image and storage volume snapshot

<https://serverfault.com/questions/436648/how-to-delete-ec2-ami>

1. Open the Amazon EC2 console at https://console.aws.amazon.com/ec2/.
2. In the navigation bar, verify your region.
3. In the navigation panel, click AMIs.
4. Select the AMI, click Actions, and then click Deregister. When prompted for confirmation, click Continue.
5. In the navigation pane, click Snapshots.
6. Select the snapshot, click Actions, and then click Delete. When prompted for confirmation, click Yes, Delete.

## Create a large volume for data

One can create a separate EBS volume and attach it to individual instances. However, the volume needs to be in the same region and subregion as the instance.

* Create a volume under EBS.
* Attach it to instances when new instances are created (right click on the volume to attach, then mount in the instance; see <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-using-volumes.html>).
