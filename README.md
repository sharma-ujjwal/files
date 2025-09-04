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
