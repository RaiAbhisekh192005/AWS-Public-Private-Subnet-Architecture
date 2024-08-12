# VPC with Public and Private Subnets Across Two Availability Zones 🌐

## Project Overview
This project sets up a Virtual Private Cloud (VPC) with the following components:
- Public and private subnets across two Availability Zones.
- NAT Gateways in each public subnet.
- Load Balancer nodes in each public subnet.
- Servers in private subnets, managed by an Auto Scaling Group.
- Secure Internet access for servers through NAT Gateways.
- Bastion Host for connecting to servers in private subnets.

## Architecture Diagram
![Architecture Diagram](https://docs.aws.amazon.com/images/vpc/latest/userguide/images/vpc-example-private-subnets.png)

## Steps to Perform the Project

### 1. Create a VPC
1. Sign in to the AWS Management Console.
2. Navigate to the VPC Dashboard.
3. Click on **Create VPC**.
4. Enter the following details:
   - **Name Tag**: `my-vpc`
   - **IPv4 CIDR Block**: `10.0.0.0/16`
   - **Tenancy**: `default`
5. Click **Create VPC**.

### 2. Create Public and Private Subnets
1. In the VPC Dashboard, select **Subnets** and click **Create Subnet**.
2. Create two public subnets:
   - **Subnet Name**: `publicsubnet1`
   - **VPC**: `my-vpc`
   - **Availability Zone**: `us-east-1a`
   - **IPv4 CIDR Block**: `10.0.1.0/24`
   - Repeat for `publicsubnet2` in `us-east-1b` with `10.0.2.0/24`.
3. Create two private subnets:
   - **Subnet Name**: `privatesubnet1`
   - **VPC**: `my-vpc`
   - **Availability Zone**: `us-east-1a`
   - **IPv4 CIDR Block**: `10.0.3.0/24`
   - Repeat for `privatesubnet2` in `us-east-1b` with `10.0.4.0/24`.

### 3. Create an Internet Gateway
1. In the VPC Dashboard, select **Internet Gateways** and click **Create Internet Gateway**.
2. Enter the **Name Tag**: `my-igw`.
3. Click **Create Internet Gateway**.
4. Attach the Internet Gateway to `my-vpc`.

### 4. Create NAT Gateways
1. In the VPC Dashboard, select **NAT Gateways** and click **Create NAT Gateway**.
2. Select `publicsubnet1` and allocate an Elastic IP.
3. Repeat for `publicsubnet2`.

### 5. Configure Route Tables
1. In the VPC Dashboard, select **Route Tables** and create a route table for public subnets.
   - **Name Tag**: `publicroutetable`
   - **VPC**: `my-vpc`
2. Add a route to the Internet Gateway:
   - **Destination**: `0.0.0.0/0`
   - **Target**: `my-igw`
3. Associate `publicroutetable` with `publicsubnet1` and `publicsubnet2`.
4. Create a route table for private subnets:
   - **Name Tag**: `privateroutetable`
   - **VPC**: `my-vpc`
5. Add a route to the NAT Gateways:
   - **Destination**: `0.0.0.0/0`
   - **Target**: `nat gateway id` (for each private subnet)
6. Associate `privateroutetable` with `privatesubnet1` and `privatesubnet2`.

### 6. Create a Load Balancer
1. Navigate to the EC2 Dashboard and select **Load Balancers**.
2. Click **Create Load Balancer** and choose **Application Load Balancer**.
3. Configure the load balancer:
   - **Name**: `my-alb`
   - **Scheme**: `internet-facing`
   - **Listeners**: `http`
   - **VPC**: `my-vpc`
   - **Subnets**: `publicsubnet1`, `publicsubnet2`
4. Configure security settings and target groups as needed.

### 7. Launch EC2 Instances in Private Subnets
1. Navigate to the EC2 Dashboard and click **Launch Instance**.
2. Select an AMI and instance type.
3. Configure instance details:
   - **Network**: `my-vpc`
   - **Subnet**: `privatesubnet1` or `privatesubnet2`
   - **Auto-assign Public IP**: `disable`
4. Configure storage, tags, and security groups.
5. Review and launch the instance.

### 8. Set Up Auto Scaling Group
1. Navigate to the EC2 Dashboard and select **Auto Scaling Groups**.
2. Click **Create Auto Scaling Group**.
3. Configure the group:
   - **Name**: `my-asg`
   - **Launch Template**: select or create a launch template
   - **VPC**: `my-vpc`
   - **Subnets**: `privatesubnet1`, `privatesubnet2`
4. Configure scaling policies and review the settings.

### 9. Set Up a Bastion Host
1. Launch an EC2 instance in one of the public subnets.
2. Configure security groups to allow SSH access from your IP.
3. Use the Bastion Host to connect to instances in private subnets.

## Conclusion
This setup ensures a secure and scalable architecture with high availability across multiple Availability Zones. The NAT Gateways provide secure Internet access for the servers in private subnets, while the Load Balancer distributes traffic efficiently.

Feel free to customize the steps and configurations based on your specific requirements.

---

Happy coding! 🚀
