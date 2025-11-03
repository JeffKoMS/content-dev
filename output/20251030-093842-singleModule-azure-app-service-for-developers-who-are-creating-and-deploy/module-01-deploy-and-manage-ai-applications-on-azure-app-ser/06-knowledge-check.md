# Knowledge check

_Unit type: Knowledge Check | Estimated duration: 3 minutes_

Test your understanding of deploying and managing AI applications on Azure App Service by applying the concepts you learned to realistic scenarios. Each question presents a situation you might encounter when configuring hosting plans, securing AI service integration, or troubleshooting production deployments.

## Knowledge check

1. Your company deploys a customer support chatbot that uses Azure OpenAI Service to generate responses. During business hours (8 AM - 6 PM), the application receives an average of 25,000 requests per day with spikes to 40,000 requests during product launches. Your target response time is under 2 seconds, and your compliance team requires all traffic between the chatbot and Azure OpenAI to remain within your corporate network. Which Azure App Service hosting plan best meets these requirements?

   - A. Standard S2 tier with auto-scaling enabled up to 10 instances based on CPU threshold of 70%
   - B. Premium P1v2 tier with VNet integration and auto-scaling enabled up to 20 instances based on HTTP queue length
   - C. Basic B2 tier with manual scaling to 3 instances and scheduled scale-up during business hours

**Answer:** B. Premium P1v2 tier with VNet integration and auto-scaling enabled up to 20 instances based on HTTP queue length
**Rationale:** Premium P1v2 tier is correct because it provides VNet integration required to keep traffic on the corporate network (satisfying the compliance requirement), supports auto-scaling up to 30 instances (sufficient for the 40,000 request spike), and offers the performance needed for sub-2-second response times. Standard tier (option 1) lacks VNet integration for private network connectivity, failing the compliance requirement. Basic tier (option 3) doesn't support auto-scaling and would require manual intervention during traffic spikes, making it unsuitable for production workloads with variable demand.

2. You're deploying a product recommendation engine to Azure App Service that integrates with Azure OpenAI Service. Your security team prohibits storing API keys in application configuration and requires detailed audit logs of all credential access attempts. Your operations team wants to minimize credential rotation overhead. Which authentication approach best addresses these requirements?

   - A. Store the Azure OpenAI API key in Azure Key Vault and use a Key Vault reference in App Service application settings
   - B. Enable system-assigned managed identity for the App Service and assign the Cognitive Services OpenAI User role to the identity
   - C. Store the Azure OpenAI API key directly in App Service application settings encrypted at rest

**Answer:** B. Enable system-assigned managed identity for the App Service and assign the Cognitive Services OpenAI User role to the identity
**Rationale:** System-assigned managed identity (option 2) is correct because it completely eliminates API keys (satisfying the security team's prohibition), provides audit logging through Azure AD sign-in logs (meeting the audit requirement), and requires no credential rotation (minimizing operations overhead). Key Vault reference (option 1) improves security over plaintext keys but still requires managing and rotating API keys, creating ongoing operations work. Storing encrypted keys in application settings (option 3) violates the security team's policy against storing keys in configuration and provides no audit logging of access attempts.

3. Your AI application deployed to Azure App Service successfully integrates with Azure OpenAI Service in your development environment, but after deploying to production, all recommendation requests return HTTP 500 errors. Application Insights logs show 'Failed to acquire token' errors when calling Azure OpenAI. The production App Service uses a system-assigned managed identity, and you verified the OPENAI_ENDPOINT application setting is configured correctly. What is the most likely cause and solution?

   - A. The managed identity lacks the required RBAC role assignment; assign the Cognitive Services OpenAI User role to the App Service's managed identity at the Azure OpenAI resource scope
   - B. The Azure OpenAI endpoint URL is incorrect; update the OPENAI_ENDPOINT application setting to use the private endpoint address instead of the public FQDN
   - C. Application Insights is blocking the OpenAI API calls; disable Application Insights monitoring temporarily to isolate the authentication issue

**Answer:** A. The managed identity lacks the required RBAC role assignment; assign the Cognitive Services OpenAI User role to the App Service's managed identity at the Azure OpenAI resource scope
**Rationale:** Missing RBAC role assignment (option 1) is correct because 'Failed to acquire token' specifically indicates the managed identity exists but lacks permissions to access Azure OpenAI Service—assigning Cognitive Services OpenAI User role resolves the authentication failure. Incorrect endpoint URL (option 2) would produce connection errors or DNS resolution failures, not token acquisition errors, and the question states the endpoint setting was verified. Disabling Application Insights (option 3) would eliminate visibility into the problem without addressing the root cause, and Application Insights doesn't block API calls—it only observes them.
