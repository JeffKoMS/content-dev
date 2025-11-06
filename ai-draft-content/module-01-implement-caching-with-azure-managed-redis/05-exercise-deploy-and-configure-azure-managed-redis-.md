# Exercise - Deploy and configure Azure Managed Redis

_Unit type: Exercise | Estimated duration: 15 minutes_

You work for a retail company experiencing slow product catalog queries during peak shopping hours. Database response times average 180ms, causing customer frustration and abandoned carts. Your task is to deploy Azure Managed Redis Standard tier, implement cache-aside pattern for product lookups, and validate performance improvements using Azure Monitor metrics.

This exercise guides you through creating a Redis cache instance with secure authentication, connecting a .NET application using managed identity, and monitoring cache hit rates. You'll configure appropriate TTL values for product data and verify that cached queries respond in under 10ms.

## Exercise

**Scenario:** Deploy Azure Managed Redis to accelerate product catalog queries for a retail application experiencing high database load during peak traffic periods.

**Prerequisites**

- Active Azure subscription with Contributor access
- Azure CLI installed (version 2.50 or later)
- .NET 8.0 SDK installed locally
- Basic familiarity with Azure resource groups and networking concepts

### Steps

1. Create a resource group named 'rg-redis-lab' in East US region using Azure CLI
   - Expected result: Command succeeds with JSON output showing resource group properties including location 'eastus' and provisioning state 'Succeeded'
2. Deploy Azure Cache for Redis Standard C1 tier with name 'redis-productcache-[yourname]' in the resource group
   - Expected result: Deployment completes in 8-12 minutes; resource shows status 'Running' with hostname 'redis-productcache-[yourname].redis.cache.windows.net'
3. Enable system-assigned managed identity on the Redis instance using Azure portal or CLI
   - Expected result: Managed identity object ID displays in output; identity status shows 'Enabled' in portal Identity blade
4. Assign 'Redis Cache Contributor' role to your Azure AD user account for the Redis resource
   - Expected result: Role assignment completes with confirmation message; your account appears in Access Control (IAM) with Redis Cache Contributor role
5. Retrieve the Redis hostname and note it for application configuration
   - Expected result: Hostname displays in format '[cachename].redis.cache.windows.net' on port 6380 with SSL enabled
6. Clone the sample .NET application from Azure samples repository and navigate to the Redis-CacheAside folder
   - Expected result: Repository clones successfully; folder contains Program.cs, appsettings.json, and .csproj file
7. Update appsettings.json with your Redis hostname and configure DefaultAzureCredential for Azure AD authentication
   - Expected result: Configuration file contains correct hostname and authentication mode set to 'AzureAD'
8. Run the application locally using 'dotnet run' and observe initial cache miss followed by database query
   - Expected result: Console output shows 'Cache miss for product:101' followed by 'Database query: 175ms' then 'Cache SET product:101 TTL=3600'
9. Execute the same product query again and verify cache hit with reduced latency
   - Expected result: Console output shows 'Cache hit for product:101' with response time under 8ms
10. Navigate to Azure Monitor metrics for your Redis cache and add a chart showing cache hits and cache misses over the last 30 minutes
   - Expected result: Metrics chart displays with cache hits increasing after initial queries; hit rate metric shows percentage above 80% after warmup period
11. Review server load and connected clients metrics to confirm cache operates within capacity
   - Expected result: Server load metric shows CPU utilization below 30%; connected clients metric displays your application connection
12. Check your work: Query a product ID that wasn't previously cached and verify the cache-aside pattern executes correctly (cache miss → database query → cache SET → subsequent hit)
   - Expected result: Application logs show complete cache-aside cycle with first request taking 150ms+ and second request under 10ms

### Success criteria

- Redis Standard tier instance deploys successfully with managed identity enabled and Azure AD authentication configured
- .NET application authenticates to Redis using DefaultAzureCredential and executes GET/SET operations without access key
- Azure Monitor metrics show cache hit rate above 75% after multiple queries with response times under 10ms for cached data

### Cleanup

- Stop the .NET application process
- Delete the resource group 'rg-redis-lab' using Azure CLI command 'az group delete --name rg-redis-lab --yes'
- Verify resource group deletion completes successfully in Azure portal

### Common issues

- **Application throws authentication error 'NOAUTH Authentication required'** - Verify Redis Cache Contributor role assignment and ensure Azure CLI is authenticated with 'az login' before running application
- **Metrics show 100% cache misses despite multiple identical queries** - Check application logs for SET commands including EX parameter; verify maxmemory-policy in Redis configuration allows key persistence
- **Redis deployment fails with 'Cache name already exists' error** - Append unique identifier like timestamp or initials to cache name; verify availability using 'az redis check-name --name [cachename]'

## Additional resources

- [Azure Cache for Redis quickstart - Azure CLI](https://learn.microsoft.com/azure/azure-cache-for-redis/cache-overview) - Step-by-step guide for deploying Redis instances using Azure CLI with authentication and configuration examples
- [Azure Cache for Redis code samples repository](https://github.com/Azure-Samples/azure-cache-redis-samples) - Sample applications demonstrating cache-aside pattern implementation in .NET, Java, Python, and Node.js

## Enhancement suggestions

- **Image:** Azure portal Redis creation networking tab — Screenshot showing networking configuration options including public endpoint, private endpoint, and VNet injection choices (Alt Text: Azure portal screenshot of Redis cache creation wizard on networking tab. Radio buttons show public endpoint (selected for this exercise), private endpoint, and VNet injection options. Private endpoint section shows add button and DNS configuration settings.)
- **Image:** Azure Monitor cache metrics dashboard — Screenshot showing key Redis performance metrics including cache hits, cache misses, server load, and connected clients (Alt Text: Azure Monitor metrics dashboard displaying four charts: cache hit rate at 87%, cache miss count trending down, server load at 42% CPU, and connected clients showing 12 active connections. Time range set to last 30 minutes.)
- **Interactive:** Azure sandbox environment — Integrated sandbox providing temporary Azure subscription with step-by-step guidance for deploying Redis Standard tier, configuring managed identity, and testing cache operations with provided sample application
