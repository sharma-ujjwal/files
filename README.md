```
package com.sunlife.inc.utility.config;

import ch.qos.logback.classic.Level;
import ch.qos.logback.classic.LoggerContext;
import ch.qos.logback.classic.turbo.TurboFilter;
import ch.qos.logback.core.spi.FilterReply;
import jakarta.annotation.PostConstruct;
import org.slf4j.LoggerFactory;
import org.slf4j.Marker;
import org.springframework.stereotype.Component;

@Component
public class ExactLevelFilterConfig {

    private final LoggerContext ctx = (LoggerContext) LoggerFactory.getILoggerFactory();

    private volatile Level exactLevel = null; // null => allow all

    private final TurboFilter exactLevelFilter = new TurboFilter() {
        @Override
        public FilterReply decide(Marker marker,
                                  ch.qos.logback.classic.Logger logger,
                                  Level level,
                                  String format,
                                  Object[] params,
                                  Throwable t) {
            Level target = exactLevel;
            if (target == null) {
                return FilterReply.NEUTRAL; // no restriction
            }
            return level == target ? FilterReply.NEUTRAL : FilterReply.DENY;
        }
    };

    @PostConstruct
    void install() {
        exactLevelFilter.start();
        ctx.getTurboFilterList().add(exactLevelFilter);
    }

    public void setExactLevel(Level level) {
        this.exactLevel = level;
    }

    public void clearExactLevel() {
        this.exactLevel = null;
    }
}
```
