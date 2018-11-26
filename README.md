# Hackney API Platform

## Adding your application to the platform

> Coming soon.

## Setting up new application servers

### Step here

Description here.

## Making Swagger documentation public

1. Go to the API Gateway in ECS, and open at the Hackney Platform API.
2. Configure the API you're working on to have a `/swagger` resource, with it's own `/{proxy+}` resource within. This will take precedence over top level `/{proxy+}` requests. This proxy should be configured to not require an API token, and should only permit requests to secure, public routes in the application.
3. "Deploy" the API in the API Gateway to staging and test.
4. "Deploy" the API in the API Gateway to production and test.
5. Configure your Swagger documentation to accept an authentication token. [See a .NET Core example of this.][dotnet-swagger-auth-header]

[dotnet-swagger-auth-header]: https://github.com/LBHackney-IT/LBHTenancyAPI/blob/master/LBHTenancyAPI/Startup.cs#L73-L84
