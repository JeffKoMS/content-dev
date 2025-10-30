# Summary

_Unit type: Summary | Estimated duration: 2 minutes_

You successfully deployed Contoso Retail's product recommendation engine to Azure App Service, eliminating the manual scaling and security challenges that plagued the initial launch. By selecting Standard tier hosting with auto-scaling triggered by HTTP queue length, you ensured the application automatically adapts to traffic spikes without manual intervention from your operations team. Implementing managed identity authentication removed API key management overhead, satisfying your security team's zero-trust requirements while eliminating credential rotation tasks. Application Insights now provides visibility into Azure OpenAI Service call patterns, token consumption, and response times, giving your team the data needed to optimize both performance and costs.

## Key takeaways

Throughout this module, you learned job-critical skills for deploying production AI applications:

- **Hosting plan selection**: Evaluate expected request volume, response time requirements, and compliance constraints to choose between Basic (development/testing), Standard (production with moderate traffic), Premium (high-volume with private networking), or Isolated (enterprise compliance) tiers, balancing performance against monthly costs that range from $55 to $680+.
- **Secure AI service integration**: Configure Azure App Service to integrate Azure OpenAI Service and Azure AI Services using Key Vault references for secret protection or managed identity for zero-credential authentication, selecting the approach that aligns with your security policies and operational capacity.
- **Managed identity authentication**: Enable system-assigned or user-assigned managed identities and assign Cognitive Services OpenAI User or Cognitive Services User RBAC roles to grant least-privilege access to AI services, eliminating API key storage and rotation requirements.
- **Production monitoring**: Use Application Insights to track AI service dependencies, response times, success rates, and token consumption, providing your operations team with telemetry to diagnose performance issues and your finance team with data to optimize AI service costs.

These skills enable you to deploy AI applications that scale automatically, authenticate securely, and provide operational visibility—core requirements for maintaining SLA commitments while controlling costs in production environments.

## Next steps

Build on your Azure App Service and AI integration knowledge with these recommended learning paths:

- **Scale applications in Azure App Service**: Learn advanced auto-scaling strategies including custom metrics, scheduled scaling, and performance optimization techniques for high-traffic AI workloads.
- **Implement Azure Container Apps for AI workloads**: Explore containerized deployment options for AI applications requiring microservices architecture or event-driven scaling patterns.
- **Build CI/CD pipelines for AI applications with Azure DevOps**: Automate deployment workflows including model versioning, staged rollouts, and automated testing for AI-powered applications.
- **Optimize costs for Azure AI services**: Discover cost management techniques including reserved capacity, commitment tiers, and usage analytics to reduce Azure OpenAI and Azure AI Services spending.

## Additional resources

- [Azure App Service documentation](https://learn.microsoft.com/azure/app-service/) - Comprehensive reference for App Service features, configuration, and best practices
- [Azure OpenAI Service documentation](https://learn.microsoft.com/azure/ai-services/openai/) - Official documentation for deploying and using Azure OpenAI Service
- [Application Insights for App Service](https://learn.microsoft.com/azure/azure-monitor/app/azure-web-apps) - Guide to configuring and using Application Insights monitoring for App Service applications
