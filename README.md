# **Cost And Usage Governance**

This lab is provided as part of **[AWS Innovate For Every Application Edition](https://aws.amazon.com/events/aws-innovate/apj/for-every-app/)**

ℹ️ You will run this lab in your own AWS account. Please follow directions at the end of the lab to remove resources to avoid future costs.

## Introduction
 This hands-on lab will guide you through the steps to implement cost and usage governance. The skills you learn will help you control your cost and usage in alignment with your business requirements.

## Goals
- Implement IAM Policies to control usage

## Costs
- A small number of instances will be started & then immediately terminated
- Costs will be less than $5 if all steps including the teardown are performed

## Time to complete
- The lab should take approximately 30 minutes to complete

## Create a group of users for testing
This lab requires you to develop a restrictive IAM policy, then apply the policies to a group of users, then login as a user in that group and verify the policy. We will create this test group.

1. Go to the **IAM** service page:
![Images/AWSPolicy1.png](/Images/AWSPolicy1.png)

2. Click on **Groups**, click **Create New Group**:
![Images/AWSCreateTest2.png](/Images/AWSCreateTest2.png)

3. Set the group name to **CostTest** and click **Create Group**:
![Images/AWSCreateTest3.png](/Images/AWSCreateTest3.png)

4. Click **Users** and **Add users**:
![Images/AWSCreateTest6.png](/Images/AWSCreateTest6.png)

8. Configure the user as follows:
    - **Username**: TestUser1
    - Tick **Provide useraccess to the AWS management Console - optional**
    - **Console password**: Autogenerated password
    - Un-select **Users must create a new password at next sign-in**
    - Click **Next**
    ![Images/AWSCreateTest8.png](/Images/AWSCreateTest8.png)

9. Select the **CostTest** group, and click **Next: Tags**:
![Images/AWSCreateTest9.png](/Images/AWSCreateTest9.png)

10. Click **Next: Review**:
![Images/AWSCreateTest10.png](/Images/AWSCreateTest10.png)

11. Review the details and click **Create user**:
![Images/AWSCreateTest11.png](/Images/AWSCreateTest11.png)

12. Record the **logon link**, the **User** and the **Password** for later use, click **Close**:
![Images/AWSCreateTest12.png](/Images/AWSCreateTest12.png)


## Create an IAM Policy to restrict service usage by region
To manage costs you need to manage and control your usage. AWS offers multiple regions, so depending on your business requirements you can limit access to AWS services depending on the region. This can be used to ensure usage is only allowed in specific regions which are more cost effective, and minimize associated usage and cost, such as data transfer.

We will create a policy that allows all EC2, RDS and S3 access in a single region only. 
NOTE: it is best practice to provide only the minimum access required, the policy used here is for brevity and simplicity, and should only be implemented as a demonstration before being removed.

### Create the Region restrictive IAM Policy
1. Go to the **IAM** service page:
![Images/AWSPolicy1.png](/Images/AWSPolicy1.png)

2. Select **Policies** from the left menu and Click **Create Policy**:
![Images/AWSPolicy2.png](/Images/AWSPolicy2.png)

4. Click the **JSON** tab:
![Images/AWSPolicy4.png](/Images/AWSPolicy4.png)

5. Copy and paste the policy into the console:
```
{
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                    "ec2:*",
                    "rds:*",
                    "s3:*"
                ],
                "Resource": "*",
        "Condition": {"StringEquals": {"aws:RequestedRegion": "us-east-1"}}
            }
        ]
}
```

![Images/AWSPolicy5.png](/Images/AWSPolicy5.png)

6. Click **Next**

7. Create the policy with the following details:
    - **Name**: RegionRestrict
    - **Description**: EC2, RDS, S3 access in us-east-1 only
    - Click **Create policy**:
![Images/AWSPolicy6.png](/Images/AWSPolicy6.png)

You have successfully created an IAM policy to restrict usage by region.


### Apply the policy to your test group
1. Select **User groups** from the left menu:
![Images/AWSPolicy7.png](/Images/AWSPolicy7.png)

2. Click on the **CostTest** group (created previously):
![Images/AWSPolicy8.png](/Images/AWSPolicy8.png)

3. Select the **Permissions** tab:
![Images/AWSPolicy9.png](/Images/AWSPolicy9.png)

4. Click on **Add permissions** dropdown list and choose **Attach Policies**:
![Images/AWSPolicy10.png](/Images/AWSPolicy10.png)

5. Click **Policy Type** and select **Customer Managed**:
![Images/AWSPolicy12.png](/Images/AWSPolicy12.png)

6. Select the checkbox next to **RegionRestrict** (created above) and click **Add permissions**:
![Images/AWSPolicy13.png](/Images/AWSPolicy13.png)

You have successfully attached the policy to the CostTest group.
Log out from the console

### Verify the policy is in effect

1. Logon to the console as the **TestUser1** user, go to the **EC2** Service dashboard:
![Images/AWSPolicy14.png](/Images/AWSPolicy14.png)

2. Click the current region in the top right, and select **US West (N.California)**:
![Images/AWSPolicy15.png](/Images/AWSPolicy15.png)

3. Try to launch an instance by clicking **Launch Instance**, select **Launch Instance**:
![Images/AWSPolicy17.png](/Images/AWSPolicy17.png)

4. Click on **Select** next to the **Amazon Linux 2 AMI**, You will receive an error when you select an AMI as you do not have permissions:
![Images/AWSPolicy18.png](/Images/AWSPolicy18.png)

You have successfully verified that you cannot launch any instances outside of the N.Virginia region. We will now verify we have access in us-east-1 (N.Virginia):

5. Change the region by clicking the current region, and selecting **US East (N.Virginia)**:
![Images/AWSPolicy19.png](/Images/AWSPolicy19.png)

6. Now attempt to launch an instance, choose the **Amazon Linux 2 AMI**, leave **64-bit (x86)** selected, click **Select**:
![Images/AWSPolicy20.png](/Images/AWSPolicy20.png)

7. Scroll down and select a **c5.large**, and click **Review and Launch**:
![Images/AWSPolicy21.png](/Images/AWSPolicy21.png)

8. Take note of the security group created (as you need to delete it), Click **Launch**:
![Images/AWSPolicy23.png](/Images/AWSPolicy23.png)

9. Select **Proceed without a key pair**, and click **I acknowledge..** checkbox, and click **Launch Instances**:
![Images/AWSPolicy24.png](/Images/AWSPolicy24.png)

10. You will get a success message, click on the instance id:
![Images/AWSPolicy25.png](/Images/AWSPolicy25.png)

11. Ensure the correct instance is selected, click **Actions**, then **Instance State**, then **Terminate**:
![Images/AWSPolicy26.png](/Images/AWSPolicy26.png)

12. Confirm the instance ID is correct, click **Yes, Terminate**:
![Images/AWSPolicy27.png](/Images/AWSPolicy27.png)

13. Log out of the console as TestUser1.

You have successfully implemented an IAM policy that restricts all EC2, RDS and S3 operations to a single region. You have also successfully launched a **c5 Instance Type**.

## Create an IAM Policy to restrict EC2 usage by family
AWS offers different instance families within EC2. Depending on your workload requirements - different types will be most cost effective. For non-specific environments such as testing or development, you can restrict the instance families in those accounts to the most cost effective generic types. It is also an effective way to increase Savings Plan or Reserved Instance utilization, by ensuring these accounts will consume any available commitment discounts.

We will create a policy that allows operations on specific instance families only. This will not only restrict launching an instance, but all other activities. 
NOTE: it is best practice to provide only the minimum access required, the policy used here is for brevity and simplicity, and should only be implemented as a demonstration before being removed.

### Create the Instance family restrictive IAM Policy

1. Log on to the console as your regular user with the required permissions, Go to the **IAM** service page:
![Images/AWSFamilyRestrict0.png](/Images/AWSFamilyRestrict0.png)

2. Select **Policies** from the left menu:
![Images/AWSFamilyRestrict1.png](/Images/AWSFamilyRestrict1.png)

3. Click **Create Policy**:
![Images/AWSFamilyRestrict2.png](/Images/AWSFamilyRestrict2.png)

4. Click on the **JSON** tab:
![Images/AWSFamilyRestrict3.png](/Images/AWSFamilyRestrict3.png)

5. Copy and paste the policy into the console:
```
 {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": "ec2:*",
                "Resource": "*",
                "Condition": {
                    "ForAllValues:StringLike": {
                        "ec2:InstanceType": [
                            "t3.*",
                            "a1.*",
                            "m5.*"
                        ]
                    }
                }
            }
        ]
}
```

6. Click **Next**:
![Images/AWSFamilyRestrict4.png](/Images/AWSFamilyRestrict4.png)

7. Enter the details:
    - **Name**: EC2_FamilyRestrict
    - **Description**: Restrict to t3, a1 and m5 families
    -  Click on **Create Policy**:
![Images/AWSFamilyRestrict5.png](/Images/AWSFamilyRestrict5.png)

You have successfully created an IAM policy to restrict usage by **Instance Family**.

### Apply the policy to your test group

1. Click on **Groups** from the left menu:
![Images/AWSFamilyRestrict6.png](/Images/AWSFamilyRestrict6.png)

2. Click on the **CostTest** group (created previously):
![Images/AWSFamilyRestrict7.png](/Images/AWSFamilyRestrict7.png)

3. We need to remove the **RegionRestrict** policy, as it permitted all EC2 actions. Click on **Detach Policy** for **RegionRestrict**:
![Images/AWSFamilyRestrict8.png](/Images/AWSFamilyRestrict8.png)

4. Click on **Detach**:

![Images/AWSFamilyRestrict9.png](/Images/AWSFamilyRestrict9.png)

5. Click on **Attach Policy**:
![Images/AWSFamilyRestrict10.png](/Images/AWSFamilyRestrict10.png)

6. Click on **Policy Type**, then click **Customer Managed**:
![Images/AWSFamilyRestrict11.png](/Images/AWSFamilyRestrict11.png)

7. Select the checkbox next to **Ec2_FamilyRestrict**, and click **Attach Policy**:
![Images/AWSFamilyRestrict12.png](/Images/AWSFamilyRestrict12.png)

You have successfully attached the policy to the CostTest group.
Log out from the console

### Verify the policy is in effect

1. Logon to the console as the **TestUser1** user, go to the EC2 Service dashboard:
![Images/AWSFamilyRestrict13.png](/Images/AWSFamilyRestrict13.png)

3. Try to launch an instance by clicking **Launch Instance**, select **Launch Instance**:
![Images/AWSPolicy17.png](/Images/AWSPolicy17.png)

3. Click on **Select** next to the **Amazon Linux 2** AMI:
![Images/AWSFamilyRestrict15.png](/Images/AWSFamilyRestrict15.png)

4. We will select an instance we are not able to launch first, so select a **c5.large** instance, click **Review and Launch**:
![Images/AWSFamilyRestrict16.png](/Images/AWSFamilyRestrict16.png)

5. Make note of the security group created, click **Launch**:
![Images/AWSFamilyRestrict17.png](/Images/AWSFamilyRestrict17.png)

6. Select **Proceed without a key pair**, and click **I acknowledge that I will not be able to...**, then click **Launch Instances**:
![Images/AWSFamilyRestrict18.png](/Images/AWSFamilyRestrict18.png)

7. You will receive an error, notice the failed step was **Initiating launches**.  Click **Back to Review Screen**:
![Images/AWSFamilyRestrict19.png](/Images/AWSFamilyRestrict19.png)

8. Click **Edit instance type**:
![Images/AWSFamilyRestrict20.png](/Images/AWSFamilyRestrict20.png)

9. We will select an instance type we can launch (t3, a1 or m5) select **t3.micro**, and click **Review and Launch**:
![Images/AWSFamilyRestrict21.png](/Images/AWSFamilyRestrict21.png)

10. Select **Yes, I want to continue with this instance type (t3.micro)**, click **Next**:
![Images/AWSFamilyRestrict22.png](/Images/AWSFamilyRestrict22.png)

11. Click **Launch**:
![Images/AWSFamilyRestrict23.png](/Images/AWSFamilyRestrict23.png)

12. Select **Proceed without a key pair**, and click **I acknowledge that i will not be able to...**, then click **Launch Instances**:
![Images/AWSFamilyRestrict24.png](/Images/AWSFamilyRestrict24.png)

13. You will receive a success message.  Click on the **Instance ID** and **terminate the instance** as above:
![Images/AWSFamilyRestrict25.png](/Images/AWSFamilyRestrict25.png)

14. Log out of the console as TestUser1.

You have successfully implemented an IAM policy that restricts all EC2 actions to T3, A1 and M5 instance types.
 
---

## Extend an IAM Policy to restrict EC2 usage by instance size
We can also restrict the size of instance that can be launched. This can be used to ensure only low cost instances can be created within an account. This is ideal for testing and development, where high capacity instances may not be required. We will extend the EC2 family policy above, and add restrictions by adding the sizes of instances allowed.


### Extend the EC2Family_Restrict IAM Policy

1. Log on to the console as your regular user with the required permissions, go to the **IAM** service page:
![Images/AWSFamilyUpdate0.png](/Images/AWSFamilyUpdate0.png)

2. Click on **Policies** on the left menu:
![Images/AWSFamilyUpdate1.png](/Images/AWSFamilyUpdate1.png)

3. Click on **Filter policies**, then select **Customer managed**:
![Images/AWSFamilyUpdate2.png](/Images/AWSFamilyUpdate2.png)

4. Click on **EC2_FamilyRestrict** to modify it:
![Images/AWSFamilyUpdate3.png](/Images/AWSFamilyUpdate3.png)

5. Click on **Edit policy**:
![Images/AWSFamilyUpdate4.png](/Images/AWSFamilyUpdate4.png)

6. Click on the **JSON** tab:
![Images/AWSFamilyUpdate5.png](/Images/AWSFamilyUpdate5.png)

7. Modify the policy by adding in the sizes, add in **nano**, **medium**, **large**, be careful not to change the syntax and not remove the quote characters. Click on **Review policy**:
![Images/AWSFamilyUpdate6.png](/Images/AWSFamilyUpdate6.png)

8. Click on **Save changes**:
![Images/AWSFamilyUpdate7.png](/Images/AWSFamilyUpdate7.png)

9. Log out from the console

You have successfully modified the policy to restrict usage by instance size.

### Verify the policy is in effect

1. Logon to the console as the **TestUser1** user, click on **Services** and go to the **EC2** dashboard:
![Images/AWSFamilyUpdate8.png](/Images/AWSFamilyUpdate8.png)

3. Try to launch an instance by clicking **Launch Instance**, select **Launch Instance**:
![Images/AWSPolicy17.png](/Images/AWSPolicy17.png)

3. Click on **Select** next to the  **Amazon Linux 2 AMI**:
![Images/AWSFamilyUpdate10.png](/Images/AWSFamilyUpdate10.png)

4. We will attempt to launch a **t3.micro** which was successful before. Click on **Review and Launch**:
![Images/AWSFamilyUpdate11.png](/Images/AWSFamilyUpdate11.png)

5. Review the configuration and take note of the security group created, click **Launch**:
![Images/AWSFamilyUpdate12.png](/Images/AWSFamilyUpdate12.png)

6. Select **Proceed without a key pair**, and click **I acknowledge that i will not be able to...**, then click **Launch Instances**:
![Images/AWSFamilyUpdate13.png](/Images/AWSFamilyUpdate13.png)

7. You will get a failure, as it wasn't a size we allowed in the policy. Click **Back to Review Screen**:
![Images/AWSFamilyUpdate14.png](/Images/AWSFamilyUpdate14.png)

8. Click **Edit instance type**:
![Images/AWSFamilyUpdate15.png](/Images/AWSFamilyUpdate15.png)

9. We will now select a **t3.nano** which will succeed. Click **Review and Launch**:
![Images/AWSFamilyUpdate16.png](/Images/AWSFamilyUpdate16.png)

10. Select **Yes, I want to continue with this instance type (t3.nano)**, and click **Next**:
![Images/AWSFamilyUpdate17.png](/Images/AWSFamilyUpdate17.png)

11. Review the configuration and click **Launch**:
![Images/AWSFamilyUpdate18.png](/Images/AWSFamilyUpdate18.png)

12. Select **Proceed without a key pair**, and click **I acknowledge that i will not be able to...**, then click **Launch Instances**:
![Images/AWSFamilyUpdate19.png](/Images/AWSFamilyUpdate19.png)

13. It will succeed. Click on the **Instance ID** and **terminate** the instance as above:
![Images/AWSFamilyUpdate20.png](/Images/AWSFamilyUpdate20.png)

13. Log out of the console as TestUser1.

You have successfully implemented an IAM policy that restricts all EC2 instance operations by family and size.

---

## Create an IAM policy to restrict EBS Volume creation by volume type
Extending cost optimization governance beyond compute instances will ensure overall higher levels of cost optimization. Similar to EC2 instances, there are different storage types. Governing the type of storage that can be created in an account can be effective to minimize cost.

We will create an IAM policy that denies operations that contain provisioned IOPS (io1) EBS volume types. This will not only restrict creating a volume, but all other actions that attempt to use this volume type. 

NOTE: it is best practice to provide only the minimum access required, the policy used here is for brevity and simplicity, and should only be implemented as a demonstration before being removed.

### Create the EBS type restrictive IAM Policy

1. Log on to the console as your regular user with the required permissions, go to the **IAM** service page:
![Images/AWSEBSPolicy0.png](/Images/AWSEBSPolicy0.png)

2. Click on **Policies** on the left menu:
![Images/AWSEBSPolicy1.png](/Images/AWSEBSPolicy1.png)

3. Click **Create policy**:
![Images/AWSEBSPolicy2.png](/Images/AWSEBSPolicy2.png)

4. Click on the **JSON** tab:
![Images/AWSEBSPolicy3.png](/Images/AWSEBSPolicy3.png)

5. Copy and paste the policy into the console:
```
{
        "Version": "2012-10-17",
        "Statement": [
            {
                "Sid": "VisualEditor0",
                "Effect": "Deny",
                "Action": "ec2:*",
                "Resource": "*",
                "Condition": {
                    "StringEquals": {
                        "ec2:VolumeType": "io1"
                    }
                }
            }
        ]
}
```

6. Click on **Next**:
![Images/AWSEBSPolicy4.png](/Images/AWSEBSPolicy4.png)

7. Configure the following details:
    - **Name**: EC2EBS_Restrict
    - **Description**: Dont allow EBS io1 volumes
    - Click **Create policy**:
![Images/AWSEBSPolicy5.png](/Images/AWSEBSPolicy5.png)

You have successfully created an IAM policy to restrict EBS actions by volume type.

### Apply the policy to your test group

1. Click on **Groups** from the left menu:
![Images/AWSEBSPolicy6.png](/Images/AWSEBSPolicy6.png)

2. Click on the **CostTest** group:
![Images/AWSEBSPolicy7.png](/Images/AWSEBSPolicy7.png)

3. Click on **Attach Policy**:
![Images/AWSEBSPolicy8.png](/Images/AWSEBSPolicy8.png)

4. Click on **Policy Type**, then click **Customer Managed**:
![Images/AWSEBSPolicy9.png](/Images/AWSEBSPolicy9.png)

5. Select the checkbox next to **EC2EBS_Restrict**, and click **Attach Policy**:
![Images/AWSEBSPolicy10.png](/Images/AWSEBSPolicy10.png)

You have successfully attached the policy to the CostTest group.
Log out from the console

### Verify the policy is in effect

1. Logon to the console as the **TestUser1** user, click on **Services** then click **EC2**:
![Images/AWSEBSPolicy11.png](/Images/AWSEBSPolicy11.png)

2. Try to launch an instance by clicking **Launch Instance**, select **Launch Instance**:
![Images/AWSPolicy17.png](/Images/AWSPolicy17.png)

3. Click **Select** next to **Amazon Linux 2...**:
![Images/AWSEBSPolicy13.png](/Images/AWSEBSPolicy13.png)

4. Select **t3.nano** (which is allowed as per our already applied policy, which we tested in the last exercise), click **Next: Configure Instance Details**:
![Images/AWSEBSPolicy14.png](/Images/AWSEBSPolicy14.png)

5. Click **Next Add Storage**:
![Images/AWSEBSPolicy15.png](/Images/AWSEBSPolicy15.png)

6. Click on **Add New Volume**, click on the **dropdown**, then select **Provisioned IOPS SSD (io1)**:
![Images/AWSEBSPolicy16.png](/Images/AWSEBSPolicy16.png)

7. Click **Review and Launch**:
![Images/AWSEBSPolicy17.png](/Images/AWSEBSPolicy17.png)

8. Take note of the security group created, and click **Launch**:
![Images/AWSEBSPolicy18.png](/Images/AWSEBSPolicy18.png)

9. Select **Proceed without a key pair**, and click **I acknowledge that i will not be able to...**, then click **Launch Instances**:
![Images/AWSEBSPolicy19.png](/Images/AWSEBSPolicy19.png)

10. The launch will fail, as it contained an io1 volume.  Click **Back to Review Screen**:
![Images/AWSEBSPolicy20.png](/Images/AWSEBSPolicy20.png)

11. Scroll down and click **Edit storage**:
![Images/AWSEBSPolicy21.png](/Images/AWSEBSPolicy21.png)

12. Click the **dropdown** and change it to **General Purpose SSD(gp2)**, click **Review and Launch**:
![Images/AWSEBSPolicy22.png](/Images/AWSEBSPolicy22.png)

13. Click **Launch**:
![Images/AWSEBSPolicy18.png](/Images/AWSEBSPolicy18.png)

14. Select **Proceed without a key pair**, and click **I acknowledge that i will not be able to...**, then click **Launch Instances**:
![Images/AWSEBSPolicy23.png](/Images/AWSEBSPolicy23.png)

15. It will now succeed, as it doesn't contain an io1 volume type.  Click on the **instance ID** and **terminate** the instance as above:
![Images/AWSEBSPolicy24.png](/Images/AWSEBSPolicy24.png)

16. Log out of the console as TestUser1.

You have successfully implemented an IAM policy that denies operations if there is an EBS volume of type io1.



---

## Teardown

Log onto the console as your regular user with the required permissions.

### Delete the IAM policies
We will delete the IAM policies created, as they are no longer applied to any groups.

1. Log on to the console as your regular user with the required permissions, go to the **IAM** service page:
![Images/AWSPolicy1.png](/Images/AWSPolicy1.png)

2. Click on **Policies** on the left:
![Images/AWSPolicy2.png](/Images/AWSPolicy2.png)

3.Click on **Filter Policies** and select **Customer managed**:
![Images/AWSTeardown11.png](/Images/AWSTeardown11.png)

4. Select the policy you want to delete **Region_Restrict**:
![Images/AWSTeardown12.png](/Images/AWSTeardown12.png)

5. Click on **Policy actions**, and select **Delete**:
![Images/AWSTeardown13.png](/Images/AWSTeardown13.png)

6. Click on **Delete**:
![Images/AWSTeardown14.png](/Images/AWSTeardown14.png)

7. Perform the same steps above to delete the **Ec2_FamilyRestrict** and **EC2EBS_Restrict** policies.

8. Click on **Groups**:
![Images/AWSTeardown15.png](/Images/AWSTeardown15.png)

9. Select the **CostTest** group, click **Group Actions**, click **Delete Group**:
![Images/AWSTeardown16.png](/Images/AWSTeardown16.png)

10. Click **Yes, Delete**:
![Images/AWSTeardown17.png](/Images/AWSTeardown17.png)

11. Click **Users**:
![Images/AWSTeardown18.png](/Images/AWSTeardown18.png)

12. Select **TestUser1**, and click **Delete user**:
![Images/AWSTeardown19.png](/Images/AWSTeardown19.png)

13. Click **Yes, delete**:
![Images/AWSTeardown20.png](/Images/AWSTeardown20.png)

14. Go to the **EC2** dashboard:
![Images/AWSTeardown21.png](/Images/AWSTeardown21.png)

15. Click **Security Groups** on the left:
![Images/AWSTeardown22.png](/Images/AWSTeardown22.png)

16. Select the security groups **you took note of**, ensure you have the correct groups that were created. Click **Actions**, select **Delete Security Groups**:
![Images/AWSTeardown23.png](/Images/AWSTeardown23.png)

17. **Triple check** they are the groups you wrote down, and click **Yes, Delete**:
![Images/AWSTeardown24.png](/Images/AWSTeardown24.png)

18. Confirm there are no io1 unattached EBS volumes, go to the **EC2 dashboard**, click on **Elastic Block Store**, click **Volumes**. You can sort by the **Created** column to help identify volumes that were not terminated as part of this lab.

## Conclusion
You've completed the lab and learnt how to implement IAM Policies to control usage.

This lab is part of the **AWS Well-Architected Labs** which contains documentation and code in the format of hands-on labs to help you learn, measure, and build using architectural best practices. 

You can can learn more about the **AWS Well-Architected Labs** [here](https://wellarchitectedlabs.com/) 


## Survey
Let us know what you thought of this session and how we can improve the presentation experience for you in the future by completing this [event session poll](https://amazonmr.au1.qualtrics.com/jfe/form/SV_1U4cxprfqLngWGy?Session=HOL09). Participants who complete the surveys from AWS Innovate Online Conference will receive a gift code for USD25 in AWS credits1, 2 & 3. AWS credits will be sent via email by September 29, 2023.
Note: Only registrants of AWS Innovate Online Conference who complete the surveys will receive a gift code for USD25 in AWS credits via email.

<sup>1</sup>AWS Promotional Credits Terms and conditions apply: https://aws.amazon.com/awscredits/ 

<sup>2</sup>Limited to 1 x USD25 AWS credits per participant.

<sup>3</sup>Participants will be required to provide their business email addresses to receive the gift code for AWS credits.
