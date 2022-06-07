# Pipeline Overview
It is about a CI/CD pipeline built using Jenkins, Docker, AWS, Terraform.
The Jenkins application was hosted on Google Cloud Platform(GCP) Virtual Machine.

Many Plugins were installed into Jenkins like NodeJS Executor, Docker, Terraform, AWS, BlueOcean, any many other plugins.

For Terraform code: <a href="https://github.com/AhmedGrati/sahti-iac">click on this link</a>
## Continuous Integration
1- Checkout for the specific project

2- Build the NPM project

3- Execute Unit & E2E Tests

## Continuous Deployment
1- Build Docker Image and push it to dockerhub

2- Checkout to Terraform Code

3- Apply our ECS cluster using Terraform

<img src="./readme_assets/jenkins-pipeline.png" style="display: block; margin-left: auto; margin-right: auto">

<img src="./readme_assets/pipeline.png" style="display: block; margin-left: auto; margin-right: auto">

# Execution
## ECS Cluster
<img src="./readme_assets/cluster.png" style="display: block; margin-left: auto; margin-right: auto">



## Application
Access Application through Application Load Balancer of AWS
<img src="./readme_assets/execution.png" style="display: block; margin-left: auto; margin-right: auto">

# Things to Improve
1- Add Image versioning instead of pushing as latest tag, we could use the hash of the commit as tag.

2- Integrate security tools in our pipeline and transform it to DevSecOps, to increase the security of our infrastructure. e.g: Snyk to scan docker images and find vulnerabilities.
