# Exercise: Deploy and configure an AI-powered web application

_Unit type: Exercise | Estimated duration: 15 minutes_

Your retail company needs to deploy a product recommendation engine to production. As the cloud engineer, you'll deploy an Azure App Service instance, configure it to integrate with Azure OpenAI Service using managed identity authentication, and validate that the application generates AI-powered recommendations successfully. This exercise demonstrates the end-to-end deployment workflow you'll use when moving AI applications from development to production environments.

## Exercise

**Scenario:** You work as a cloud engineer for Contoso Retail. The company has developed a product recommendation engine that uses Azure OpenAI Service to generate personalized suggestions based on customer browsing history. Your task is to deploy this application to Azure App Service with Standard tier hosting, configure managed identity authentication to eliminate API key management, and validate that the production deployment successfully integrates with Azure OpenAI Service. The application code is available in a GitHub repository, and you need to ensure the deployment follows security best practices by avoiding hardcoded credentials.

**Prerequisites**

- Active Azure subscription with Contributor access to create resources
- Azure OpenAI Service resource already deployed with a GPT-4 model deployment named 'gpt-4'
- Basic familiarity with the Azure portal navigation
- Completion of previous concept units in this module

### Steps

1. Sign in to the Azure portal at https://portal.azure.com and create a new resource group named rg-contoso-recommendations in the East US region to contain all exercise resources
   - Expected result: Resource group rg-contoso-recommendations appears in the resource groups list with status Active and location East US
   - Screenshot: Azure portal showing the resource groups blade with rg-contoso-recommendations highlighted in the list
2. Navigate to App Services and create a new web app named contoso-recommendations-[yourname] using the .NET 8 (LTS) runtime stack, Standard S1 pricing tier, and placing it in the rg-contoso-recommendations resource group
   - Expected result: App Service deployment completes successfully with a unique URL displayed (for example, https://contoso-recommendations-yourname.azurewebsites.net) and status showing as Running in the overview page
3. Navigate to the Identity blade of your App Service, select the System assigned tab, and toggle Status to On to enable system-assigned managed identity, then save the configuration and copy the Object (principal) ID for later use
   - Expected result: System assigned managed identity Status shows On with a green indicator, and an Object (principal) ID is displayed (for example, a 32-character GUID like 12345678-1234-1234-1234-123456789abc)
   - Screenshot: App Service Identity blade showing system-assigned identity enabled with Object ID visible
4. Navigate to your existing Azure OpenAI Service resource, open the Access Control (IAM) blade, select Add role assignment, choose the Cognitive Services OpenAI User role, and assign it to the managed identity you just created by searching for your App Service name in the Members section
   - Expected result: Role assignment completes successfully with a notification confirming that Cognitive Services OpenAI User role was assigned to your App Service's managed identity, visible in the Role assignments tab filtered by your managed identity
   - Screenshot: Azure OpenAI Service IAM blade showing the new role assignment with your App Service's managed identity listed as a member of Cognitive Services OpenAI User role
5. Return to your App Service, navigate to Configuration under Settings, add a new application setting named OPENAI_ENDPOINT with value https://[your-openai-resource-name].openai.azure.com (replace with your actual Azure OpenAI resource name), then save the configuration
   - Expected result: Application setting OPENAI_ENDPOINT appears in the configuration list with the correct endpoint URL, and the App Service automatically restarts to apply the new setting
6. Navigate to the Deployment Center blade, select GitHub as the source, authenticate with your GitHub account if prompted, choose the repository contoso-samples/product-recommendation-engine (fictional example), select the main branch, and save to enable continuous deployment
   - Expected result: Deployment workflow runs automatically and completes with success status, showing the latest commit deployed and application logs confirming successful startup
   - Screenshot: Deployment Center showing GitHub source connected with successful deployment status and commit details
7. Navigate to your App Service URL (https://contoso-recommendations-yourname.azurewebsites.net), enter 'outdoor gear' in the product category input field, and select Submit to test the recommendation engine
   - Expected result: The page displays AI-generated product recommendations including items like 'Hiking Backpack', 'Trail Running Shoes', and 'Portable Water Filter' with descriptions and confidence scores, confirming successful integration with Azure OpenAI Service using managed identity authentication
   - Screenshot: Web application interface showing the recommendation results with multiple product suggestions displayed
8. Navigate to Application Insights linked to your App Service, open the Performance blade, view the transaction timeline, and verify that calls to Azure OpenAI Service appear as dependencies with response times and success rates
   - Expected result: Application Insights Performance view shows Azure OpenAI Service listed in the dependencies section with average response time under 2 seconds, 100% success rate, and token usage metrics visible in the details pane
   - Screenshot: Application Insights Performance blade showing OpenAI Service dependency metrics with response times and success indicators

### Success criteria

- App Service deployed with Standard S1 tier and status Running
- System-assigned managed identity enabled with Object ID visible
- Cognitive Services OpenAI User role assigned to the managed identity
- Application setting OPENAI_ENDPOINT configured correctly
- Web application returns AI-generated recommendations without authentication errors
- Application Insights tracks Azure OpenAI Service calls as dependencies

### Cleanup

- Navigate to the resource groups blade in the Azure portal and select rg-contoso-recommendations
- Select Delete resource group, type the resource group name to confirm, and select Delete to remove all exercise resources including App Service, Application Insights, and role assignments
- Verify in the Notifications pane that resource group deletion completed successfully to avoid ongoing charges

### Common issues

- **App Service name already exists error during creation** - Append a unique suffix like your initials and today's date to the app name (for example, contoso-recommendations-jd0115) since App Service names must be globally unique across Azure
- **Recommendation request returns 403 Forbidden error** - Verify that the RBAC role assignment completed successfully by checking the Azure OpenAI Service IAM blade—role propagation can take 2-5 minutes, so wait briefly then retry the request
- **Application Insights doesn't show OpenAI dependencies** - Ensure the Application Insights connection string is configured in App Service application settings and that you've made at least one recommendation request after deployment—telemetry can take 2-3 minutes to appear in queries

## Additional resources

- [Deploy code to App Service](https://learn.microsoft.com/azure/app-service/deploy-github-actions) - Detailed guide on configuring GitHub Actions deployment for App Service

## Enhancement suggestions

- Screenshot of the running product recommendation web application showing an input form where users can enter a product category, a Submit button, and a response section displaying AI-generated product recommendations from Azure OpenAI Service with product names, descriptions, and confidence scores
- Screenshot of the Application Insights Performance blade showing a transaction timeline with dependencies highlighted, specifically showing calls to Azure OpenAI Service with response times, success rates, and token usage metrics visible in the details pane
- Screenshot of the Azure OpenAI Service Access Control (IAM) blade with the Role assignments tab selected, filtering by the App Service's managed identity name, showing the Cognitive Services OpenAI User role assigned with a green checkmark indicating active permission
- Interactive step-by-step checklist that learners complete as they progress through the exercise, including checkboxes for: App Service created with Standard tier, system-assigned managed identity enabled and Object ID copied, RBAC role assigned to Azure OpenAI, application settings configured with OpenAI endpoint, code deployed successfully, test request returns AI recommendations, Application Insights shows dependency calls, and all resources deleted during cleanup

## Accessibility notes

Provide descriptive alt text for all screenshots showing the specific UI elements learners need to locate and interact with, including button labels, field names, and status indicators.
