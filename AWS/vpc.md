### Q1. What is a VPC, and why would you use one instead of default networking?
- A VPC, or Virtual Private Cloud, is a logically isolated virtual network in AWS where we control IP addressing, subnets, routing, and network security.
- AWS provides a default VPC for quick deployments, but for production environments we usually design a custom VPC so we can properly separate public and private resources, control traffic using route tables and security controls, plan IP addresses, and support connectivity with other networks or on-premises environments.

### Q2. What's the difference between a public subnet and a private subnet?
- The main difference is the route table. A public subnet has a default route, such as 0.0.0.0/0, pointing to an Internet Gateway, which allows resources with appropriate public addressing and security rules to communicate directly with the internet.
- A private subnet doesn't have a direct route to an Internet Gateway. If private resources need outbound internet access, they can use a NAT Gateway.

### Q3. What is a CIDR block, and how do you decide the size for a VPC?
- CIDR stands for Classless Inter-Domain Routing. It is a notation used to define an IP address range and the size of a network.
- I first check the existing corporate and AWS network ranges to make sure there is no CIDR overlap, especially if we may connect to on-premises networks or other VPCs.
- Then I estimate the number of Availability Zones, subnets, and resources we need, including future growth.
- Based on those requirements, I choose a CIDR large enough to accommodate the workload without unnecessarily wasting address space.
- For example, a common starting point is 10.0.0.0/16, which provides 65,536 IPv4 addresses and gives enough room to create multiple /24 subnets.

### Q4. What's the purpose of an Internet Gateway (IGW)?
- An Internet Gateway is a horizontally scaled, redundant VPC component that provides a path between a VPC and the internet. It is attached to the VPC, and a subnet becomes public when its route table has a route, typically 0.0.0.0/0, pointing to the Internet Gateway. Resources also need appropriate public IP addressing and security rules to actually communicate with the internet.

### Q5. What is a route table, and how does it control traffic?
- A route table is a set of routing rules that determines where network traffic from a subnet should be sent.
- Each route contains a destination CIDR and a target, such as local, an Internet Gateway, NAT Gateway, or Transit Gateway.
- The route table is associated with a subnet, and when traffic leaves a resource, AWS selects the most specific matching route to determine the next hop.
- For example, 10.0.0.0/16 → local keeps VPC traffic internal, while 0.0.0.0/0 → IGW provides a path to the internet.

### Q6. What's the default behavior of the default VPC that comes with an AWS account?
- AWS creates a Default VPC in each Region to provide a ready-to-use network environment.
- It typically has a 172.31.0.0/16 CIDR, subnets across Availability Zones, an Internet Gateway, route tables, and default security controls.
- The default subnets have a route to the Internet Gateway and normally auto-assign public IPv4 addresses to resources such as EC2, making them public subnets.
- It's convenient for testing and quick deployments, but for production we generally create a custom VPC so we have full control over CIDRs, public/private subnet design, routing, and security.
  
### Q7. Explain how a NAT Gateway works and why a private subnet needs one.
- A NAT Gateway provides outbound internet connectivity for resources in a private subnet without giving those resources public IP addresses.
- The private subnet's route table sends internet-bound traffic, usually 0.0.0.0/0, to the NAT Gateway.
- The NAT Gateway translates the private source IP to its public address and sends the traffic through the Internet Gateway.
- The response comes back through the NAT Gateway and is translated back to the private resource.
- This allows private resources to initiate outbound connections while preventing unsolicited inbound connections from the internet.
  
### Q8. What's the difference between a NAT Gateway and a NAT Instance? When would you pick one over the other?
### Q9. Walk through what happens step-by-step when an EC2 instance in a public subnet talks to the internet.
### Q10. What are Security Groups vs. Network ACLs, and how do they differ in statefulness?
### Q11. Can a subnet span multiple Availability Zones? Why or why not?
### Q12. What is an Elastic IP, and when is it actually necessary?
### Q13. How does DNS resolution work inside a VPC (VPC DNS resolver, enableDnsSupport, enableDnsHostnames)?
### Q14. How would you design a VPC for a 3-tier application (web, app, database)?
### Q15. What is VPC Peering, and what are its key limitations (e.g., no transitive routing)?
### Q16. What's the difference between VPC Peering and a Transit Gateway? When would you use which?
### Q17. How do you connect an on-prem data center to a VPC (VPN vs Direct Connect)?
### Q18. What are VPC Endpoints, and what's the difference between a Gateway Endpoint and an Interface Endpoint?
### Q19. Why would you use a VPC Endpoint instead of routing traffic through a NAT Gateway to reach S3?
### Q20. How do you troubleshoot when an EC2 instance in a private subnet can't reach the internet?
### Q21. What is VPC Flow Logs, and how would you use it to debug a connectivity issue?
### Q22. How does an Auto Scaling Group interact with subnets across multiple AZs?
### Q23. How would you design a multi-account, multi-VPC network for an organization (hub-and-spoke with Transit Gateway)?
### Q24. What's the difference between Transit Gateway and VPC Peering at scale — routing complexity, cost, and blast radius considerations?
### Q25. How do you handle overlapping CIDR blocks between VPCs that need to be connected?
### Q26. How would you set up a VPC for an EKS cluster — considerations for subnet sizing, IP exhaustion (since pods consume IPs), and public/private endpoint access?
### Q27. What's the role of a Shared VPC / Resource Access Manager (RAM) in a multi-account setup?
### Q28. How do Security Groups behave when referenced across peered VPCs or accounts?
### Q29. How would you design for high availability and fault isolation using multiple AZs and NAT Gateways (cost vs. resilience trade-off)?
### Q30. How do you enforce network segmentation and zero-trust principles within a VPC (e.g., using Security Groups as the primary boundary vs. NACLs)?
### Q31. What happens to routing and connectivity during a NAT Gateway failure, and how do you design around it?
### Q32. How would you audit and secure a VPC against unintended public exposure (e.g., an S3 bucket or RDS instance accidentally reachable)?

