# Documentation
Creating a scalable horizontal business environment for Metabase, enabling the infrastructure to expand seamlessly as demand increases.


### The Metabase Service
<img src="https://assets.stickpng.com/images/62a9cfba8ff6441a2952dae3.png" style="width:20%;height:auto;">

The service is accessible at https://metabase.freedynamicdns.net, utilizing a dynamic DNS service for a user-friendly address.
<BR>

### The Baseline Infrastructure

***AWS***
- VPC with multi-AZ subnets (public, private, isolated).
- Security firewalls control traffic, especially between private and isolated. subnets
- Spot Instance types t3.medium and c5.large.
- EC2 Scalability configured to automatically scale based on CPU utilization, reaching 90%.
- Nodegroup capacity desired 2, minimum: 2, maximum 3.
  
***EKS CLUSTER***
- Created the cluster using eksctl, a command-line tool for simplified EKS cluster management on AWS.
- HPA: 1 pod min and 5 max, target 150% CPU and 90% MEM utilization.
- Deployment: port 3000, requests 500 CPU/MEM, limmit 2000 CPU/MEM.
- Service: forward traffic from port 80 to the deployment on port 3000.
- Ingress: traffic from metabase.freedynamicdns.net and forward it to port 80 of svc when the path is '/'. This enables external access to the service.
- Secrets: securely hide the database connection password, ensuring a more robust and secure configuration.
<img src="https://i.ibb.co/HDSMPzc/kubectl.gif" style="width:80%;height:auto;">
Figure1 - List resources

***RDS***
- The Metabase service is configured to persist data in a PostgreSQL database.
- The RDS configuration ensures secure, isolated access, high availability through Multi-AZ, and AWS-managed backups with a retention period of 7 days for data protection.
  
***DNS***
- Utilized a free DNS service from No-IP.com to map a user-friendly domain to the load balancer in the cluster.
  
***SSL***
- Utilized Cert-manager for secure communication by managing SSL/TLS certificates within the Kubernetes cluster.

<img src="https://i.ibb.co/R2WysZ6/infra.png" style="width:100%;height:auto;">

Figure2 - High-level EKS/RDS SaaS Architecture Concepts
<BR>
### The Pipeline

***DOCKER HUB***
- Metabase is available on Docker Hub, and you can pull the official Metabase Docker image from there. The official Metabase Docker image is maintained by the Metabase team. 
```docker pull metabase/metabase:latest```

***GITHUB ACTIONS***
- GitHub was utilized for version control, and GitHub Actions was employed for pipeline creation. 
- The pipeline, triggered on main branch pushes and pull requests, consists of five steps: code checkout, AWS credentials setup, kubeconfig manifest creation, context selection and deployment to the cluster.

<img src="https://i.ibb.co/WV5HvB1/pipeline.png" style="width:100%;height:auto">
Figure3 - The workflow