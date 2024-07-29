---
tags:
  - elasticache
---
## Elasticache Features
-
- Caching strategies
	- `Cache-Aside` (Lazy loading)
		- initial overhead due to `cache miss` for requested data.
		- Most common strategy: application first checks cache, 
			- `cache hit`: for a given parameters, then return it.
			- `cache miss`: query #database , populate the cache and return it.
	- `Write-Through`
		- The #cache is proactively updated immediately following the primary database update.
		- When the primary database is updated, cache will also be updated.
		- Better performance due to large cache.
		- Infrequently accessed data also written to the cachesha
	- 🍃 A proper caching strategy includes effective use of both write-through and lazy loading of your data and setting an appropriate expiration for the data to keep it relevant and lean.