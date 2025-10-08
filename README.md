# Word-Press-Site-On-AWS 

# Part 1 — Create the VPC

1. Log in to your AWS account.

2. In the search bar at the top, type VPC and click it.

3. Click Create VPC.
![create-vpc](./New-Pic-24/1.create-vpc.png)
    Choose:
VPC only (simpler) OR VPC and more (AWS will create subnets, route tables, and an Internet Gateway for you automatically — good for beginners).
4. Enter:
 Name tag: WordPress-VPC
 IPv4 CIDR block: 10.0.0.0/16

5. Leave IPv6 off (optional for now).

 6. Click Create VPC.
 #[name-vpc](./New-Pic-24/2.name-vpc.png).

# Part 2 — Create Subnets
We need public and private areas.

1. In the left menu, click Subnets → Create subnet.
![subnet](./New-Pic-24/3.subnet.png).

2. Select your WordPress-VPC.
![select-vpc](./New-Pic-24/4.name-vpc.png)'

3. Create:
- Public Subnet A:
CIDR block: 10.0.1.0/24
Availability Zone: pick your preferred region/AZ
![set-subnet](/New-Pic-24/5.set-vpc.png).

- Private Subnet A:
CIDR block: 10.0.2.0/24
Availability Zone: same region but can be same or different AZ.
![private-subnet](./New-Pic-24/5.private-subnet.png).

# Part 3 — Create an Internet Gateway
This is the gate for your public subnet.

1. Left menu → Internet Gateways → Create internet gateway.
![IGW](./New-Pic-24/6.IGW.png).

2. Name it: WordPress-IGW.
![Name-IGW](./New-Pic-24/7.NAME-IGW.png).

3. Click Attach to VPC and choose WordPress-VPC.
![Attach-vpc](./New-Pic-24/8.Attach-vpc.png).

![Attached](./New-Pic-24/9.Attached.png).


# Part 4 — Create Route Tables
We make two:
# Public Route Table:
1. Left menu → Route Tables → Create route table.
![raue-table](./New-Pic-24/10.route-table.png).

2. Name: WordPress-Public-RT.
![Name-RT](./New-Pic-24/11.Name-RT.png).

3. Attach to WordPress-VPC.
4. In the Routes tab → Edit routes → Add route:
![Edit-route-RT](./New-Pic-24/12.Edit-RT.png).
- Destination: 0.0.0.0/0

- Target: Your Internet Gateway.
![SET-edition](./New-Pic-24/13.Set-edition.png).

5. In the Subnet associations tab → Associate with Public Subnet A.
![Associate-subnet](./New-Pic-24/14.Associate-subnet.png).
![Associate-public-ub](./New-Pic-24/15.Associte-public-subnet.png).

# Private Route Table:
1. Same steps as above, name it WordPress-Private-RT.
![private-RT](./New-Pic-24/16.Private-RT.png)

2. No internet gateway route.
![SET-IP](./New-Pic-24/18.set-ip-destination.png)

3. Associate with Private Subnet A.
![Associate-sub](./New-Pic-24/20.associte-subnet.png)

![save-association](./New-Pic-24/21.save-association.png).

# NAT Gateway for Private Internet Access
Purpose: Allows private resources to initiate outbound internet connections (updates, package installs) without being exposed to inbound traffic.

Steps:

1. VPC → NAT Gateways → Create NAT Gateway.
![Nat-gte](./New-Pic-24/22.Nat-gtw.png)

2. Subnet: Choose your Public Subnet (must be public so NAT can reach internet).
3. Elastic IP: Allocate a new Elastic IP.
![name-ngw](./New-Pic-24/23.Name-NGW.png)
Create.

# Update Private Route Table
Steps:
1. Go to Route Tables → Private Route Table.

2. Edit routes:
![update-p-subnet](./New-Pic-24/24.udate-p-subnet.png).


# Create the Amazon RDS MySQL Instance
Purpose: A managed MySQL database so you don’t have to worry about manual installation, backups, or patching.

1. In AWS Console → RDS → Create database.
![create-RDS](./New-Pic-24/25.Create-RDS.png).

2. Engine type → Choose MySQL.
![ENGINE-TYPE](./New-Pic-24/26.engine-type.png).

3. Templates:

- For learning/testing → Free tier (if available).

- For production → Production (enables Multi-AZ, backups, etc.).
![FREE-TIER](./New-Pic-24/27.free-tier.png).

4. DB instance identifier → wordpress-db.
![Name-DB](./New-Pic-24/28.Name-DB.png).

5. Master username → e.g., admin.

6. Master password → Create a strong password and save it (you’ll need it for WordPress).
![PASSWD](./New-Pic-24/29.Paswd.png).

7. Instance class → e.g., db.t3.micro for testing, bigger for production.
![Instance-type](./New-Pic-24/30.instance-type.png).

8. Storage:

- Start small (20 GB GP2/GP3) and enable autoscaling if needed.

9. Connectivity:

- VPC → Choose your WordPress-VPC.

- Subnet group → Select your private subnets (RDS should be private).

- Public access → No.
![connectivity](./New-Pic-24/31.connectivity.png).

- VPC security group → Create or choose one that only allows MySQL (port 3306) from your WordPress EC2 security group.

10. Database authentication → Password authentication.
![database-authentication](./New-Pic-24/33.Database-authentication.png).

11. Create database.

# Configure Security Groups for RDS
1. Go to EC2 → Security Groups.

2. Find the security group attached to your RDS instance.

3. Edit inbound rules:

- Type → MySQL/Aurora

- Port → 3306

- Source → Select the security group of your WordPress EC2 instance (not “Anywhere”).

4. Save changes.