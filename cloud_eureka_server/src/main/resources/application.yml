server:
  #项目端口
  port: 8001
  #＃应用程序的上下文路径
  #context-path: /myeurekaserver

spring:
  application:
    #未配置spring.application.name 或者 eureka.instance.appname 属性。如果这两个属性均不配置，
    #就会导致eureka server上的应用名称 UNKNOWN 的问题
    #应用程序名称
    name: eurekaserver

eureka:
  server:
#eureka server的自我保护机制（默认是打开的）
#在开发环境是关闭  因为你可能需要不断的开启与关闭实例，如果并未关闭自我保护模式，那么很容易就会触发自我保护模式，此时对调试会相对比较麻烦
#*************************************
#在生产环境中，需要打开自我保护机制
#当 Eureka Server 节点在短时间内丢失了过多实例的连接时（比如网络故障或频繁的启动关闭客户端），
#那么这个节点就会进入自我保护模式，一旦进入到该模式，Eureka server 就会保护服务注册表中的信息，不再删除服务注册表中的数据（即不会注销任何微服务）
#当网络故障恢复后，该 Ereaka Server 节点就会自动退出自我保护模式
#通过重启 Eureka Server来退出自我保护模式或者剔除确实已经不用了的实例
#通常而言，PRD 环境建议对 Eureka Server 做负载均衡，这样在依次关闭并开启 Eureka Server 后，无效的实例会被清除，并且不会对正常的使用照成影响
#-------------------------------------
    enable-self-preservation: false
    #eureka server清理无效节点的时间间隔,默认60000毫秒,即60秒（eviction 驱逐的意思）
    eviction-interval-timer-in-ms: 60000

  instance:
    #设置当前实例的主机名称 使用  主机IP
    #***********************************
    #许多高可用的Eureka配置里都要设置hosts文件，它是必须的吗？
      #不是必须的，配置hosts文件的目的是使各个Eureka的eureka.instance.hostname不同
      #Eureka互相注册要求各个Eureka实例的eureka.instance.hostname不同，如果相同，则会被Eureka标记为unavailable-replicas
    #这里我们把eureka.instance.hostname取了${spring.cloud.client.ipAddress}的值，
    #这个值取的是当前启动Eureka的机器的IP，这样虽然配置一样但却自动区分了每台机器的Eureka，
    #这时我们再取prefer-ip-address: true，就可以确保机器优先使用IP而不是到hosts解析域名
    #如果要在同一台机器上启动多个Eureka，那么要注意保证各个Eureka的hostname不同
    #一种办法是常用的配置hosts文件，还有一种，是使用当前机器IP、127.0.0.1和localhost分别作为三个Eureka的hostname；
    #如果你使用127.0.0.1或localhost，注意一定要设置prefer-ip-address: false
    
    #hostname和prefer-ip-address是做什么的？
    #如果点击下面注册中心的可用实例列表中的地址，访问结果会分以下几个情况：
    #1)hostname和prefer-ip-address都没有配置，则访问 主机名:服务名:端口号
        #如http://desktop-1fkok7b:8761/info
    #2)配置了hostname而没有配置prefer-ip-address，则访问 hostname:服务名:端口号
        #如http://myhostname:8761/info
    #3)一旦配置了prefer-ip-address，则访问 ipAddress:服务名:端口号
        #如http://172.20.10.3:8761/info
    #-------------------------------------
    #这里我们已经把hostname设置为当前机器IP（${spring.cloud.client.ipAddress}），可以想见prefer-ip-address并不是必须的，但是合理使用它可以避免因为访问主机名而请求不到服务的情况，当然或许还节约了地址解析的损耗（虽然比较小）
    #在PRD 环境 需要服务中心高可用 一般Eureka放在不同的机器上，所以用当前启动Eureka的机器的IP是ok的
    hostname: ${spring.cloud.client.ipAddress}
    prefer-ip-address: true
    #运行阶段执行健康检查的目的是为了从Eureka服务器注册表中识别(如果没开自我保护机制会删除。开了会保护)不可访问的微服务并更新服务实例列表 ；
    #Eureka 服务器并不是向客户端发送心跳请求，而是反过来，Eureka 客户端将心跳发送到Eureka服务器，让服务器了解其状态。
    #默认情况下Eureka客户会每隔30秒发送一次心跳来续约。 通过续约来告知Eureka Server该Eureka客户仍然存在，没有出现问题。 
    #默认情况下，如果Eureka Server在90秒没有收到Eureka客户的续约，它会将实例从其注册表中删除（如果没有开启保护机制的话）
    # renewal(续约。重申 的意思)
    #用于定义服务续约任务的调用间隔时间，默认30秒
    lease-renewal-interval-in-seconds: 30
    #用于定义服务失效的时间（续约到期时间），默认为90秒   expiration(截止)
    lease-expiration-duration-in-seconds: 90
    
  client:
    #eureka 会默认把自己也当作服务注册，这在以后做高可用的时候会用到
    #可以通过下面两行来禁用掉；但是 PRD 环境 需要服务中心高可用，如果只有一个服务中心，服务中心那么服务就不课用了，所以要把自己也注册到service-url.defaultZone
    #register-with-eureka: false
    #fetch-registry: false
    #若是未禁用eureka服务注册中心的客户端注册行为，需提供service-url注册中心地址
    service-url:
      #高可用需要有多个服务中心，那么就需要写多个，把自己注册上去
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/,http://127.0.0.1:8002/eureka/
    healthcheck:
      #开启健康检查  eureka.client.healthcheck.enabled=true
      #必须设置在application.yml中，而不能设置在bootstrap.yml 中，否则一些场景下会导致应用状态 UNKNOWN 的问题
      enabled: true 
    
    

