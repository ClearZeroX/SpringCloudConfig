第六篇: 分布式配置中心(Spring Cloud Config) (Finchley版本)
    1、服务端config-server
        1、依赖: spring-boot-starter-web 起步依赖
                spring-cloud-config-server 起步依赖
        2、@EnableConfigServer 注解开启配置服务
        3、配置文件:
            spring.application.name=config-server
            server.port=8888

            spring.cloud.config.server.git.uri=https://github.com/ZhaLC/SpringCloudConfig.git
            spring.cloud.config.server.git.searchPaths=/SpringCloudConfig
            spring.cloud.config.label=develop
            spring.cloud.config.server.git.username=
            spring.cloud.config.server.git.password=

            spring.cloud.config.server.git.uri：配置git仓库地址
            spring.cloud.config.server.git.searchPaths：配置仓库路径
            spring.cloud.config.label：配置仓库的分支
            spring.cloud.config.server.git.username：访问git仓库的用户名
            spring.cloud.config.server.git.password：访问git仓库的用户密码

            远程仓库hhttps://github.com/ZhaLC/SpringCloudConfig/SpringcloudConfig/
            中有个文件config-client-dev.properties文件中有一个属性：foo = foo version 3

            http请求地址和资源文件映射如下:(可以通过这几种方式获取远程仓库的配置文件)
            /{application}/{profile}[/{label}]
            /{application}-{profile}.yml
            /{label}/{application}-{profile}.yml
            /{application}-{profile}.properties
            /{label}/{application}-{profile}.properties

    2、客户端 config-client
        1、依赖: spring-boot-starter-web起步依赖
                spring-cloud-starter-config起步依赖
        2、配置文件:bootstrap.properties(只有bootstrap.properties配置文件下配置才能读配置中心(config-server)配置)
            spring.application.name=config-client
            spring.cloud.config.label=develop
            spring.cloud.config.profile=dev
            #指明配置服务中心的网址
            spring.cloud.config.uri=http://localhost:8982/
            server.port=8981

    3、config-client从config-server获取了foo属性, 而config-server是从git仓库获取的.


第七篇: 高可用的分布式配置中心(Spring Cloud Config) (Finchley版本)
    1、将config-server和config-client都注册到服务中
    2、config-client从config-server获取配置文件不再是配置网址, 而是服务名如下:
        原:
            spring.application.name=config-client
            spring.cloud.config.label=develop
            spring.cloud.config.profile=dev
            #指明配置服务中心的网址
            spring.cloud.config.uri=http://localhost:8982/
            server.port=8981
        新:
            spring.application.name=config-client
            spring.cloud.config.label=develop
            spring.cloud.config.profile=dev
            #指明配置服务中心的网址
            #spring.cloud.config.uri=http://localhost:8982/   ###不再使用url访问config-server
            server.port=8981

            #下面使用服务来访问config-server
            eureka.client.serviceUrl.defaultZone=http://localhost:8980/eureka/
            #是从配置中心读取文件
            spring.cloud.config.discovery.enabled=true
            #配置中心的servieId，即服务名
            spring.cloud.config.discovery.serviceId=config-server