```
package com.assurant.inc.amp.config;

import com.assurant.inc.tpm.ws.FileActionTxnActivityField;
import net.sf.ehcache.Cache;
import net.sf.ehcache.Element;
import net.sf.ehcache.config.CacheConfiguration;
import org.springframework.beans.factory.annotation.Required;
import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.cache.ehcache.EhCacheCacheManager;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.DependsOn;
import org.springframework.context.annotation.Scope;

import javax.annotation.PreDestroy;
import java.util.List;

@Configuration
public class CacheConfig extends AdminMemberProcessorAppConfig {

    @Bean(name = "cacheManager")
    @Scope
    @Required
    public EhCacheCacheManager cacheManager() {
        EhCacheCacheManager manager = new EhCacheCacheManager();
        System.setProperty(net.sf.ehcache.CacheManager.ENABLE_SHUTDOWN_HOOK_PROPERTY, "true");

        CacheConfiguration cacheConfiguration = new CacheConfiguration();
        cacheConfiguration.setName("ampdatacache");
        cacheConfiguration.setMemoryStoreEvictionPolicy("LRU");
        cacheConfiguration.setMaxEntriesLocalHeap(10000);
        cacheConfiguration.setMaxEntriesLocalDisk(10000);
        cacheConfiguration.timeToLiveSeconds(300);

        net.sf.ehcache.config.Configuration config = new net.sf.ehcache.config.Configuration();
        config.addCache(cacheConfiguration);

        return manager;
    }

    @PreDestroy
    public void shutdownCache() {
        cacheManager().getCacheManager().clearAll();
        cacheManager().getCacheManager().shutdown();
    }

    @Bean(name= "fileActionTxnActivityFieldsCache")
    @Scope
    @DependsOn("tpmRestClient")
    public Cache fileActionTxnActivityFieldsCache(){
        final String cacheName = "fileActionTxnActivityFieldsCache";
        Cache fileActionTxnActivityFieldsCache = new Cache(new CacheConfiguration(cacheName, 0).eternal(true));
        cacheManager().getCacheManager().addCache(fileActionTxnActivityFieldsCache);
        List<FileActionTxnActivityField> fileActionTxnActivityFieldList = tpmRestClient().findAllFileActionTxnActivityField();
        cacheManager().getCacheManager().getCache(cacheName).put(new Element(cacheName,fileActionTxnActivityFieldList));
        return fileActionTxnActivityFieldsCache;
    }

    @Bean(name = "policyRequestCache")
    @Scope(value = BeanDefinition.SCOPE_SINGLETON)
    public Cache policyRequestCache(){
        Cache policyRequestCache = new Cache(new CacheConfiguration("policyRequestCache", 0).eternal(true));
        cacheManager().getCacheManager().addCache(policyRequestCache);
        return policyRequestCache;
    }

    @Bean(name = "qpsClientRequestCache")
    @Scope(value = BeanDefinition.SCOPE_SINGLETON)
    public Cache qpsClientRequestCache(){
        Cache qpsClientRequestCache = new Cache(new CacheConfiguration("qpsClientRequestCache", 0).eternal(true));
        cacheManager().getCacheManager().addCache(qpsClientRequestCache);
        return qpsClientRequestCache;
    }
}




import com.assurant.inc.tpm.ws.FileActionTxnActivityField;
import org.ehcache.Cache;
import org.ehcache.CacheManager;
import org.ehcache.config.CacheConfiguration;
import org.ehcache.config.builders.CacheConfigurationBuilder;
import org.ehcache.config.builders.CacheManagerBuilder;
import org.ehcache.config.builders.ResourcePoolsBuilder;
import org.ehcache.config.units.EntryUnit;
import org.ehcache.expiry.ExpiryPolicy;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.DependsOn;
import org.springframework.context.annotation.Scope;

import javax.annotation.PreDestroy;
import java.time.Duration;
import java.util.List;

@Configuration
@EnableCaching
public class CacheConfig extends AdminMemberProcessorAppConfig {

    private CacheManager ehcacheManager;

    @Bean
    public CacheManager ehcacheManager() {
        // Create cache configuration for ampdatacache
        CacheConfiguration<String, Object> ampDataCacheConfig = CacheConfigurationBuilder
            .newCacheConfigurationBuilder(String.class, Object.class,
                ResourcePoolsBuilder.newResourcePoolsBuilder()
                    .heap(10000, EntryUnit.ENTRIES)
                    .disk(10000, EntryUnit.ENTRIES, true))
            .withExpiry(ExpiryPolicy.builder().timeToLiveExpiration(Duration.ofSeconds(300)))
            .build();

        // Build cache manager
        ehcacheManager = CacheManagerBuilder.newCacheManagerBuilder()
            .with(CacheManagerBuilder.persistence("./cache-data")) // Disk persistence directory
            .withCache("ampdatacache", ampDataCacheConfig)
            .build(true); // true = initialize immediately

        return ehcacheManager;
    }

    @Bean
    public org.springframework.cache.CacheManager cacheManager() {
        return new org.springframework.cache.ehcache.EhCacheCacheManager(ehcacheManager());
    }

    @PreDestroy
    public void shutdownCache() {
        if (ehcacheManager != null) {
            ehcacheManager.close();
        }
    }

    @Bean(name = "fileActionTxnActivityFieldsCache")
    @Scope(BeanDefinition.SCOPE_SINGLETON)
    @DependsOn("tpmRestClient")
    public Cache<String, List<FileActionTxnActivityField>> fileActionTxnActivityFieldsCache() {
        final String cacheName = "fileActionTxnActivityFieldsCache";
        
        // Create eternal cache configuration
        CacheConfiguration<String, List<FileActionTxnActivityField>> cacheConfig = 
            CacheConfigurationBuilder.newCacheConfigurationBuilder(
                String.class, 
                (Class<List<FileActionTxnActivityField>>) (Class<?>) List.class,
                ResourcePoolsBuilder.heap(0)) // 0 = unlimited
            .withExpiry(ExpiryPolicy.NO_EXPIRY)
            .build();

        // Create and initialize cache
        Cache<String, List<FileActionTxnActivityField>> cache = ehcacheManager.createCache(
            cacheName, cacheConfig);
        
        // Pre-populate cache
        List<FileActionTxnActivityField> fileActionTxnActivityFieldList = tpmRestClient().findAllFileActionTxnActivityField();
        cache.put(cacheName, fileActionTxnActivityFieldList);
        
        return cache;
    }

    @Bean(name = "policyRequestCache")
    @Scope(BeanDefinition.SCOPE_SINGLETON)
    public Cache<String, Object> policyRequestCache() {
        CacheConfiguration<String, Object> cacheConfig = CacheConfigurationBuilder
            .newCacheConfigurationBuilder(String.class, Object.class,
                ResourcePoolsBuilder.heap(0))
            .withExpiry(ExpiryPolicy.NO_EXPIRY)
            .build();

        return ehcacheManager.createCache("policyRequestCache", cacheConfig);
    }

    @Bean(name = "qpsClientRequestCache")
    @Scope(BeanDefinition.SCOPE_SINGLETON)
    public Cache<String, Object> qpsClientRequestCache() {
        CacheConfiguration<String, Object> cacheConfig = CacheConfigurationBuilder
            .newCacheConfigurationBuilder(String.class, Object.class,
                ResourcePoolsBuilder.heap(0))
            .withExpiry(ExpiryPolicy.NO_EXPIRY)
            .build();

        return ehcacheManager.createCache("qpsClientRequestCache", cacheConfig);
    }
}


package com.example.config;

import com.assurant.inc.tpm.ws.FileActionTxnActivityField;
import org.ehcache.config.builders.*;
import org.ehcache.jsr107.Eh107Configuration;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.cache.jcache.JCacheCacheManager;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.cache.Cache;
import javax.cache.CacheManager;
import javax.cache.Caching;
import javax.cache.spi.CachingProvider;
import java.time.Duration;
import java.util.List;

@Configuration
@EnableCaching
public class CacheConfig extends AdminMemberProcessorAppConfig {

    /**
     * Create the JSR-107 (JCache) manager backed by Ehcache 3.x
     */
    @Bean(destroyMethod = "close")
    public CacheManager eh107CacheManager() {
        CachingProvider provider = Caching.getCachingProvider();
        CacheManager cacheManager = provider.getCacheManager();

        // ampdatacache - with TTL 300 seconds
        org.ehcache.config.CacheConfiguration<String, Object> ampDataCacheConfig =
                CacheConfigurationBuilder
                        .newCacheConfigurationBuilder(
                                String.class, Object.class,
                                ResourcePoolsBuilder.heap(10_000))
                        .withExpiry(ExpiryPolicyBuilder.timeToLiveExpiration(Duration.ofSeconds(300)))
                        .build();

        cacheManager.createCache("ampdatacache",
                Eh107Configuration.fromEhcacheCacheConfiguration(ampDataCacheConfig));

        // fileActionTxnActivityFieldsCache - eternal
        org.ehcache.config.CacheConfiguration<String, List<FileActionTxnActivityField>> fileActionCacheConfig =
                CacheConfigurationBuilder
                        .newCacheConfigurationBuilder(
                                String.class, (Class<List<FileActionTxnActivityField>>) (Class<?>) List.class,
                                ResourcePoolsBuilder.heap(1000))
                        .withExpiry(ExpiryPolicyBuilder.noExpiration())
                        .build();

        cacheManager.createCache("fileActionTxnActivityFieldsCache",
                Eh107Configuration.fromEhcacheCacheConfiguration(fileActionCacheConfig));

        // policyRequestCache - eternal
        org.ehcache.config.CacheConfiguration<String, Object> policyRequestConfig =
                CacheConfigurationBuilder
                        .newCacheConfigurationBuilder(
                                String.class, Object.class,
                                ResourcePoolsBuilder.heap(1000))
                        .withExpiry(ExpiryPolicyBuilder.noExpiration())
                        .build();

        cacheManager.createCache("policyRequestCache",
                Eh107Configuration.fromEhcacheCacheConfiguration(policyRequestConfig));

        // qpsClientRequestCache - eternal
        org.ehcache.config.CacheConfiguration<String, Object> qpsClientRequestConfig =
                CacheConfigurationBuilder
                        .newCacheConfigurationBuilder(
                                String.class, Object.class,
                                ResourcePoolsBuilder.heap(1000))
                        .withExpiry(ExpiryPolicyBuilder.noExpiration())
                        .build();

        cacheManager.createCache("qpsClientRequestCache",
                Eh107Configuration.fromEhcacheCacheConfiguration(qpsClientRequestConfig));

        return cacheManager;
    }

    /**
     * Bridge JCache (Ehcache 3) into Spring Cache abstraction
     */
    @Bean
    public org.springframework.cache.CacheManager springCacheManager(CacheManager eh107CacheManager) {
        return new JCacheCacheManager(eh107CacheManager);
    }

    /**
     * Populate fileActionTxnActivityFieldsCache after startup
     */
    @Bean
    public Cache<String, List<FileActionTxnActivityField>> fileActionTxnActivityFieldsCache(CacheManager eh107CacheManager) {
        Cache<String, List<FileActionTxnActivityField>> cache =
                eh107CacheManager.getCache("fileActionTxnActivityFieldsCache",
                        String.class, (Class<List<FileActionTxnActivityField>>) (Class<?>) List.class);

        List<FileActionTxnActivityField> fileActionTxnActivityFieldList =
                tpmRestClient().findAllFileActionTxnActivityField();

        cache.put("fileActionTxnActivityFieldsCache", fileActionTxnActivityFieldList);

        return cache;
    }
}


