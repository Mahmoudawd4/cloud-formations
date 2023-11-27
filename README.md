
# Infrastructure as Code: 

Deploying an EC2 Instance and VPC with AWS CloudFormation


##  1.Create Template 

 cretae file.yaml
## 2. Template File:

## file.ymal

#### This section defines an Amazon VPC (Virtual Private Cloud) named "MyVPC" with a specified CIDR block (10.10.0.0/16). It enables DNS support and DNS hostnames. The VPC is also tagged with a name.

```bash

Resources:
  MyVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.10.0.0/16
      EnableDnsSupport: true
      EnableDnsHostnames: true
      Tags:
        - Key: Name
          Value: MyVPC


```

#### This section creates a public subnet named "PublicSubnet" within the specified VPC. It has a CIDR block of 10.10.1.0/24, is associated with the us-east-1a availability zone, and automatically maps public IP addresses to instances launched in the subnet.

```bash

  PublicSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: 10.10.1.0/24
      AvailabilityZone: us-east-1a
      MapPublicIpOnLaunch: true
      Tags:
        - Key: Name
          Value: PublicSubnet




```


#### Here, an Internet Gateway named "InternetGateway" is created and tagged.




```bash


   InternetGateway:
    Type: AWS::EC2::InternetGateway
    Properties:
      Tags:
        - Key: Name
          Value: InternetGateway



```



#### This section attaches the Internet Gateway to the VPC.




```bash
  AttachGateway:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref MyVPC
      InternetGatewayId: !Ref InternetGateway



```


 #### A public route table named "PublicRouteTable" is created and associated with the VPC.





```bash

  PublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref MyVPC


```

#### This section creates a default route in the public route table, allowing all traffic (0.0.0.0/0) to go through the Internet Gateway.

```bash

  PublicRoute:
    Type: AWS::EC2::Route
    DependsOn: AttachGateway
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref InternetGateway


```



#### Associates the public subnet with the public route table.


```bash

  SubnetRouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref PublicSubnet
      RouteTableId: !Ref PublicRouteTable


```

#### This section creates a security group named "WebServerSecurityGroup" allowing SSH (port 22) and HTTP (port 80) access from anywhere.



```bash

  WebServerSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Enable SSH and HTTP access
      VpcId: !Ref MyVPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0



```


#### EC2 instance named "WebServer" is launched in the public subnet. It uses the specified Amazon Machine Image (AMI), instance type, key pair, security group, subnet, and a user data script to install and start an Apache web server with a simple HTML page.

```bash

  WebServer:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-0230bd60aa48260c6
      InstanceType: t2.micro
      KeyName: hardkey 
      SecurityGroupIds:
        - !Ref WebServerSecurityGroup
      SubnetId: !Ref PublicSubnet
      UserData:
        Fn::Base64: !Sub |
          #!/bin/bash
          yum update -y
          yum install -y httpd
          service httpd start
          chkconfig httpd on
          echo "Hello, mahmoud!" > /var/www/html/index.html
      Tags:
        - Key: Name
          Value: WebServer



```




## steps

go to stacke and create  stack

![Screenshot from 2023-11-27 01-55-29](https://github.com/Mahmoudawd4/cloud-formations/assets/38701340/2c0c5eeb-545d-4256-a554-fdf8d42a29ee)

upload file.ymal
![Screenshot from 2023-11-27 01-56-18](https://github.com/Mahmoudawd4/cloud-formations/assets/38701340/74125fd4-a87c-47ba-ac7d-725f93d01383)

create stack name
![Screenshot from 2023-11-27 01-56-41](https://github.com/Mahmoudawd4/cloud-formations/assets/38701340/50f80c9a-06ad-41a8-94af-17deb7b5a9f5)

next
![Screenshot from 2023-11-27 01-56-55](https://github.com/Mahmoudawd4/cloud-formations/assets/38701340/dfb845f2-54db-4d2c-9561-31ee9d894308)

next
stckes in progress
![Screenshot from 2023-11-27 01-57-13](https://github.com/Mahmoudawd4/cloud-formations/assets/38701340/c9e0cc88-0e3a-4077-97fe-0047bcf0fd94)

![Screenshot from 2023-11-27 01-57-28](https://github.com/Mahmoudawd4/cloud-formations/assets/38701340/92188a00-0268-46d7-a513-65d9bfd5b9bf)

 done 
![Screenshot from 2023-11-27 01-58-22](https://github.com/Mahmoudawd4/cloud-formations/assets/38701340/32cf6b64-b2d8-439f-942b-3f67408d469d)


go to copy dns webserver
![Screenshot from 2023-11-27 01-59-52](https://github.com/Mahmoudawd4/cloud-formations/assets/38701340/4fb50eb0-1566-481c-bfd1-858bc840adfe)


delete
stack

![Screenshot from 2023-11-27 02-00-16](https://github.com/Mahmoudawd4/cloud-formations/assets/38701340/d372e132-44d9-47d5-af0d-55b030037357)

![Screenshot from 2023-11-27 02-00-22](https://github.com/Mahmoudawd4/cloud-formations/assets/38701340/f7755258-ebd1-4baf-8342-e135afbc5852)



## Authors

- [@mahmoudawd](https://github.com/Mahmoudawd4)
