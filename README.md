# Automate Centralized Deployment of Amazon SageMaker Studio with AWS Service Catalog


This repository includes reference architecture and cloud formation templates for provisioning SageMaker Studio as covered in the blog post here *(link to the post once published)*. We use AWS Service Catalog to set up SageMaker Studio and provision Studio users. The diagram below showcases two flows that are supported in this architecture, Cloud Admin and Data Scientist.

![Alt Flow Diagram](images/flow_diagram.png?raw=true "Flow Diagram")

The above architecture diagram shows the necessary permissions and flow for the two user personas - Cloud Admin and Data Scientist.
* Cloud Admin logs in to AWS with the `Cloud Admin Role`. This role enables them to access Service Catalog to launch products. Products in this example are a Studio Domain and a Studio User Profile.
* The `Provisioning Role` is assumed by SageMaker and has the permissions to
    - Create a Studio Domain
    - Create a Studio User Profile
    - Attach a Sagemaker Studio custom image to the Studio Domain
* The data scientist logs in to AWS with the `Data Scientist` Role. The role has permissions to create a pre-signed URL to enable the data scientist to login to SageMaker Studio.
* SageMaker Studio will then assume a `Studio Execution Role` as defined in the data scientists's user profile which will have the necessary permissions to create or launch a `JupyterServer App` to complete the user login process.

If you would like to learn more about how we go about provisioning, please review the sections below. If you would like get started with the setup, please click the button below to create a Service Catalog Product and Product Portfolio which can provision Studio and its associated roles: 

[![Launch in us-west-2](https://raw.githubusercontent.com/awslabs/aws-media-insights-engine/development/docs/assets/images/launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=sagemaker-studio&templateURL=https://aws-ml-blog.s3.us-east-1.amazonaws.com/artifacts/best-practices-provisioning-sagemaker-studio/launch.template)

This setup covers two personas:

* *Cloud Admin* – This team is also responsible for building and maintaining the infrastructure for supporting ML services, such as provisioning notebooks for data scientists to use, creating secure buckets for storing data, managing costs for ML from various lines of business (LOBs), and more.

* *Data science:* Data scientists within an AI Center of Excellence (COE) or embedded within the LOBs are responsible for building, training, and deploying models. In regulated industries, data scientists need to adhere to the organization’s security boundaries, such as using encrypted buckets for data access, use of private networking for accessing APIs, committing code to source control, ensuring all their experiments and trials are properly logged, enforcing encryption of data in transit, and monitoring deployed models.


Below are the resources the template provisions:

1. Cloud Admin IAM Role
2. Provisioning IAM Role
3. Data Scientist IAM Role
4. Studio Execution IAM Role
5. Three Service Catalog products 
* Create Domain
* Create User



## Below are the steps you will go through:

### Populate Sevice Catalog Products

0. You will need an IAM user designated to use for launch product provisioning from Service Catalog. Please attach the **ServiceCartalogEndUserFullAccess** managed policy to the IAM user. Note that, the IAM user used for product provisoning from Service Catalog does not need access to SageMaker. Service Catalog will use a separate role for spinning up SageMaker Studio resources which will be also created along with the Service Catalog products.

Note that, you also can create just an IAM role instead of an IAM user which can be assumed before product provisioning.

1. Click on the Launch button above. Note - by default the template will launch in US-WEST-2 and you can switch to other regions before starting the CNF template.

2. Click next

3. Update the Launch Principal. This can be an IAM user, group or role which grants provisioning access to this product. Use the user you just created during the first step. Click the **Next** button.
 
![Alt Launch Principal](assets/launch_principal.jpg?raw=true "Launch Principal")
  
4. Click the **Next** button. Acknoledge that AWS CloudFormation template will create IAM resources by clicking the checkbox at the bottom of the page. Hit the **Create stack** button.

![Alt IAM capabilites](assets/iam_capabilities.jpg?raw=true "IAM Capabilities")

5. Go to the **Service Catalog** console, click on **Administration / Products** on the left navigation plane. Check if all SageMaker Studio products have been provisioned.

![Alt Service Catalog Products](assets/service_catalog_products.jpg?raw=true "Service Catalog Products")


### Provision SageMaker Studio Domain

1. Go to **AWS Console / Service Catalog / Products**. Launch the **SageMaker Studio** product.

![Alt Step 1](assets/step1.jpg?raw=true "Step 1")

2. Provide a name for the product, your VPC and subenet IDs used for SageMaker communication and hit the **Launch Product** button

![Alt Step 2](assets/step2.jpg?raw=true "Step 2")

### Provision SageMaker User Profiles

1. Go to the SageMaker Console and navigate to **SageMaker Studio** on the left pane. Get the domain-id of your newly created SageMaker Domain. 

![Alt Step 3](assets/step3.jpg?raw=true "Step 3")

2. To provision a user profile, go to service catalog and launch the **SageMaker User Profile** product.

![Alt Step 4](assets/step4.jpg?raw=true "Step 4")

3. Provide the Studio User Profile name along with the SageMaker domain ID in the parameters section. Then hit the **Launch Product** button.

![Alt Step 5](assets/step5.jpg?raw=true "Step 5")

4. Navigate to the SageMaker Studio console and check if the new User Profile has been created. Click on the **Open Studio** button to launch Studio.

![Alt User Profile](assets/user_profile.jpg?raw=true "User Profile")
