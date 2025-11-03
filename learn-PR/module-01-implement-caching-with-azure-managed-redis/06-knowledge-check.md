# Knowledge check

_Unit type: Knowledge Check | Estimated duration: 3 minutes_

Test your understanding of Azure Managed Redis tier selection, security configuration, and caching pattern implementation. These scenario-based questions assess your ability to apply concepts to real-world decision points you'll encounter when architecting cache solutions for production workloads.

## Knowledge check

1. Your financial services application requires 99.99% uptime SLA and needs to cache 800GB of customer transaction data with geographic redundancy. Database queries currently average 200ms and must reduce to under 15ms. Which Azure Managed Redis tier meets these requirements?

   - A. Standard tier with 53GB cache size because it provides 99.9% SLA and supports automatic replication for high availability
   - B. Premium tier with clustering enabled across 10 shards because it supports 1.2TB cache size but only provides 99.95% SLA
   - C. Enterprise tier with active-active geo-replication because it provides 99.99% SLA, supports 4.5TB cache, and enables multi-region deployment

**Answer:** C. Enterprise tier with active-active geo-replication because it provides 99.99% SLA, supports 4.5TB cache, and enables multi-region deployment
**Rationale:** Enterprise tier is correct because it's the only tier offering 99.99% SLA while supporting 800GB+ cache size and geo-replication for geographic redundancy. Standard tier caps at 53GB, failing the capacity requirement. Premium tier provides 99.95% SLA, missing the 99.99% requirement despite supporting adequate capacity through clustering. Enterprise tier uses Redis Enterprise software with active-active geo-replication across regions.

2. Your healthcare application must comply with HIPAA requirements mandating network isolation and identity-based authentication with full audit trails for PHI data access. Which security configuration satisfies these compliance requirements for Azure Managed Redis?

   - A. Public endpoint with access keys and firewall rules restricting IP addresses to corporate network ranges only
   - B. Private endpoint in application VNet with Azure AD authentication using managed identities and RBAC role assignments
   - C. VNet injection in Premium tier with access keys rotated monthly and TLS 1.2 encryption enabled by default

**Answer:** B. Private endpoint in application VNet with Azure AD authentication using managed identities and RBAC role assignments
**Rationale:** Private endpoint with Azure AD authentication is correct because it eliminates public internet exposure (network isolation), uses identity-based access control (managed identities), and provides complete audit trails through Azure Monitor logs capturing every cache operation with identity context. Public endpoints fail network isolation requirements regardless of firewall rules. Access keys provide limited audit capabilities and manual rotation overhead, failing HIPAA's identity traceability requirements even with VNet injection.

3. Your e-commerce application caches product inventory counts that update every 30 seconds via external warehouse management system. Cache misses trigger expensive database queries taking 250ms. Stale inventory data causes overselling when counts lag by more than 60 seconds. Which caching pattern and TTL configuration best balances performance with data freshness?

   - A. Cache-aside pattern with 300-second TTL because it maximizes cache hit rate and reduces database load even though data may lag by 5 minutes
   - B. Write-through pattern with 45-second TTL because it guarantees cache and database consistency on every inventory update despite adding write latency
   - C. Cache-aside pattern with 60-second TTL because it limits staleness to acceptable threshold while achieving 95%+ hit rate for read-heavy inventory queries

**Answer:** C. Cache-aside pattern with 60-second TTL because it limits staleness to acceptable threshold while achieving 95%+ hit rate for read-heavy inventory queries
**Rationale:** Cache-aside with 60-second TTL is correct because it prevents staleness beyond the 60-second tolerance while maintaining high hit rates for read-heavy workloads (inventory queries outnumber updates). Write-through isn't required since eventual consistency within 60 seconds is acceptable—inventory updates flow from external WMS, not through the application's write path. 300-second TTL would allow 5-minute staleness, potentially causing overselling. The 60-second TTL ensures cache entries expire within the acceptable lag window while letting the 30-second external update cycle refresh data naturally.

## Additional resources

- [Azure Cache for Redis best practices](https://learn.microsoft.com/azure/azure-cache-for-redis/cache-best-practices) - Comprehensive guide covering tier selection, security patterns, caching strategies, and operational excellence recommendations
- [Azure Cache for Redis overview](https://learn.microsoft.com/azure/azure-cache-for-redis/cache-overview) - Service tier comparison, SLA guarantees, feature matrices, and architecture guidance for production deployments

## Enhancement suggestions

- **Interactive:** Scenario-based assessment — Three multiple-choice questions testing tier selection for specific workload requirements, authentication method selection for compliance scenarios, and caching pattern selection for data consistency needs
