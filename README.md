# Hackney API Platform

- [Adding your application to the Platform](adding-your-application-to-the-platform)
- [Setting up new application servers](setting-up-new-application-servers)

## Adding your application to the Platform

> ⚠️ **Please inform the maintainers before migrating your application to AWS.** ⚠️
>
> We're keen to get more applications running on the API Platform, but need to know in advance to confirm requirements and ensure we're currently capable of supporting more.
>
> We can also provide support as you work to migrate your application across.
>
> You should contact:
> - **Rashmi Shetty** - Development Manager at London Borough of Hackney (rashmi.shetty@hackney.gov.uk)
> - **Richard Foster** - Lead Engineer at Made Tech (richard@madetech.com)
>
> Once we've given the go-ahead, continue.

### Preparing the application

- dockerise the app
  - see income api + tenancy api for examples
  - prefer runtime configuration over compile time configuration. i.e. pass environment variables into `docker run` rather than args into `docker build`. this makes the compiled image more portable as it can be configured by it's environment, rather than by a step in a build process. also means fewer images need to be compiled, as the same image can be used in many places
- build the image
  - using `docker build`
- tag the image
  - using `docker tag`
  - using the latest commit SHA
  - using the term 'latest'
  - using the environment name ('staging', 'production', etc)

### Running the application on AWS

- create an ECR repository
  - make the naming consistent with other repositories - e.g. hackney/apps/my-api
- push to ECR
  - use the AWS cli to log in first
  - use `docker push` to push
- create a task definition using that image
  - use the naming convention task-{api-name}-{environment}, e.g. task-repairs-api-staging
  - you need to create a new task definition every time you push a new image
- find an available port to run your application on
  - you can find a list of taken ports by looking at the the load balancer
- create a target group using that port
  - use the naming convention tg-{api-name}-{environment}, e.g. tg-repairs-api-staging
- forward traffic to that port on the load balancer to your target group
- create a service using EC2 mode + that task definition in the cluster
  - use the naming convention {api-name}-{environment}, e.g. repairs-api-staging
  - the service needs to be updated to use the new task definition revision every time you push a new image
  - the service should use the target group you created yourself

### Attaching it to the API Gateway

- create a application resource in the api gateway
  - for example `/my-api`
- create a proxy resource in the application resource
  - this should be called `/{proxy+}`
- ensure api key is enabled
  - checkbox in the request step of the proxy resource
- connect the proxy via a VPC Link to the target group
  - send requests to the load balancer on the applications port
  - you should use your stage variable to do this, as it will be different between environments
- add each environment's port to the ECS security group
  - this allows traffic to reach the

### Bonus ⭐

- API Standards: https://github.com/LBHackney-IT/API-Playbook
- Deployment Pipeline: https://github.com/LBHackney-IT/API-Playbook#deployment-pipeline

### Common Problems 😭

#### "Internal server error" JSON response

This can occur for many reasons, but may be because your container can't be reached from the API Gateway.

- Check that your security group has inbound traffic permitted for that port.
- Check that your target group has that port specified.
- Check that your task definition maps your chosen port on the host to it's application port within the container.
- SSH into the machine and check the container is running, and is responding to requests. <small>You will need to get access from the API Platform maintainers.</small>

## Setting up new application servers

### Step here

Description here.

## changes we'd like

- infrastructure as code
  - Terraform/Ansible/etc allow us to provision infrastructure automatically using scripts, rather than having to do so manually
- application load balancer over network load balancer
