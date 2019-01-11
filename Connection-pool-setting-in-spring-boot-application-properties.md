					# Only the following three are required (if you use the embedded database, these three can also be used without filling, the default configuration will be used), other configurations are not required.
					spring.datasource.url=jdbc:h2:file:./demo-db
					spring.datasource.username=sa
					spring.datasource.password=sa
					# driver-class-name Not required, can be inferred from the url
					spring.datasource.driver-class-name=org.h2.Driver

					# Druid Data source configuration, inheriting spring.datasource.* configuration, the same is overwritten
					spring.datasource.druid.initial-size=2
					spring.datasource.druid.max-active=30
					spring.datasource.druid.min-idle=2
					spring.datasource.druid.max-wait=1234
					spring.datasource.druid.pool-prepared-statements=true
					spring.datasource.druid.max-pool-prepared-statement-per-connection-size=5
					# spring.datasource.druid.max-open-prepared-statements= #equivalent to max-pool-prepared-statement-per-connection-size above
					spring.datasource.druid.validation-query=select 1
					spring.datasource.druid.validation-query-timeout=1
					spring.datasource.druid.test-on-borrow=true
					spring.datasource.druid.test-on-return=true
					spring.datasource.druid.test-while-idle=true
					spring.datasource.druid.time-between-eviction-runs-millis=10000
					spring.datasource.druid.min-evictable-idle-time-millis=30001
					spring.datasource.druid.async-close-connection-enable=true


					spring.datasource.druid.aop-patterns=com.alibaba.druid.spring.boot.demo.service.*

					#定制StatFilter Configure other Filters no longer demonstrate
					spring.datasource.druid.filter.stat.db-type=h2
					spring.datasource.druid.filter.stat.log-slow-sql=true
					spring.datasource.druid.filter.stat.slow-sql-millis=2000

					# JPA
					spring.jpa.show-sql= true
					spring.jpa.hibernate.ddl-auto=create-drop

					# More configuration properties see within DruidDataSource member variables (as long as they support the set method), or ideas based IDE, or check out the official documents
