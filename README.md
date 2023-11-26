# Terraform Azure Infrastructure

This repository shows how to use Terraform to provision infrastructure on Azure.

## Contents

The Terraform configuration files are located in the root directory:

- `main.tf`: Main Terraform configuration 
- `vm.tf`: Virtual Machine definition
- `provider.tf`: Terraform providers
- `ssh.tf`: SSH key generation
- `variables.tf`: Input variables
- `outputs.tf`: Output values  

There is an `az` folder that includes a compilation of minimal and all Azure images as of November 2023, primarily focused on the East US region for reference. Note that there might be slight variations in other regions.

The `.github/workflows` directory hosts GitHub Actions workflow files for automated infrastructure provisioning:

- `create-vm.yml`: Orchestrates the creation/update of infrastructure.
- `destroy-vm.yml`: Destroys the infrastructure based on the existing plan.

These files accommodate various VM configuration parameters as inputs, utilizing them to generate and execute plans and destroy plans.

## Workflow Overview

The workflow does the following:

1. Validates and generates Terraform plan 
2. Allows specifying custom VM parameters
3. Applies the changes to create/update infrastructure  
4. The `destroy-vm.yml` workflow is designed to gracefully destroy the infrastructure based on the existing Terraform plan. It simplifies the process of tearing down the provisioned resources, ensuring a smooth and controlled deprovisioning.


## Getting Started:
here be step-by-step guide to run your repository and create a VM in Azure using the provided workflow dispatch:

### Prerequisites:
1. Ensure you have an Azure account with permissions to create resources (VM).
2. Specify the values of required input variables such as `AZ_CLIENT_ID`, `AZ_SUBSCRIPTION_ID`, `AZ_TENANT_ID`, and `AZ_CLIENT_SECRET` as secrets in your GitHub repository settings.

To obtain the Azure AD Tenant ID, Client ID (Application ID), and Subscription ID for GitHub Actions with Azure OIDC login, you can follow the steps outlined in the provided documentation.

### Obtain Azure AD Tenant ID, Client ID, and Subscription ID:

Follow these steps in 
[GitHub Actions Documentation](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-azure#creating-an-azure-ad-application)  
[Medium article](https://medium.com/@rojal.bati/performing-azure-oidc-login-using-github-actions-af46ff8a73f7)

### Set Up GitHub Secrets:

After obtaining the required information, set up the following secrets in your GitHub repository:

- `AZ_TENANT_ID`: Azure AD Tenant ID.
- `AZ_CLIENT_ID`: Azure AD Client ID (Application ID).
- `AZ_CLIENT_SECRET`: Azure AD Client Secret.
- `AZ_SUBSCRIPTION_ID`: Azure Subscription ID.

By following these steps, you should be able to configure your GitHub Actions workflow for Azure OIDC login successfully.

### Usage:

1. **Fork and Clone the Repository:**
   - Fork the repository containing Terraform configuration and GitHub Actions files.
   - Clone the repository to your local machine.

2. **Set Up Secrets on GitHub:**
   - Open the repository on GitHub.
   - Go to "Settings" -> "Secrets" -> "New repository secret".
   - Add the required secrets: `AZ_CLIENT_ID`, `AZ_CLIENT_SECRET`, `AZ_SUBSCRIPTION_ID`, and `AZ_TENANT_ID`.

3. **Modify VM Configuration (Optional):**
   - Open the `vm.tf` file in the repository.
   - Adjust the VM configuration as needed.

4. **Start Dispatching the Workflow:**
   - Go to the "Actions" tab in your repository on GitHub.
   - Select "Create VM" from the list of workflows and click "Run workflow".
   - Fill in the prompted input values (optional).

5. **Wait for the Process to Complete:**
   - Monitor the process in the "Actions" tab.
   - Ensure the workflow successfully completes its steps.

6. **Verify VM in Azure:**
   - After the workflow succeeds, log in to the Azure portal to verify that the VM has been created according to your specified configuration.
7. **Destroy VM**
   - To destroy, go to the "Actions" tab in your repository on GitHub.
   - Select "Destroy VM" from the list of workflows and click "Run workflow".
  
### Important Notes:
- Ensure that the VM configuration in `vm.tf` aligns with your requirements.
- Pay attention to output messages and logs in the "Actions" tab if there are any errors.
- Ensure that the required Azure secrets are correctly set in the secrets settings.
- The "Destroy VM" workflow fetches the `terraform.tfstate` file from the last successful "Create VM" run to destroy the existing VM. If there is no successful prior "Create VM" run, the destroy will fail.


## Common issue
```js
│ Error: checking for presence of existing resource group: resources.GroupsClient#Get: 
Failure responding to request: StatusCode=403 -- 
Original Error: autorest/azure: Service returned an error. Status=403 
Code="AuthorizationFailed" 
Message="The client does not have authorization to perform action 'Microsoft.Resources/subscriptions/resourcegroups/read' over scope '/subscriptions/***/resourcegroups/***' or the scope is invalid. 
If access was recently granted, please refresh your credentials."
```
If you encounter the above error, it may be due to authorization issues when checking for the presence of an existing resource group. Please refer [to this article](https://learn.microsoft.com/en-us/entra/identity-platform/howto-create-service-principal-portal) for additional insights on creating a service principal in the Azure portal and resolving authorization-related problems.
