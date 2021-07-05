# SpringBoot

## Spring Boot

## 相关知识

* JAVA的三层接口

    Service层 （业务逻辑层）

        数据库数据重组与构建层        
    DAO层 （数据逻辑层）

        数据逻辑处理层（编写SQL与读取）

    DOMAIN层 （数据模型层）

        数据模型（MODEL层）

* 注解

    预处理机制的标识

    @RestController :控制器标识

        @PostMapper

        @GetMapper

        @RequestParams

        @RequestBody

        ```java
        @GetMapper("/getTest")
        public void getTest(@RequestParams("name") String name){


        }

        @PostMapper("/postTest")
        public void postTest(@RequestBody UserInfo userInfo){

        }

        ```

    @Service

        @Autowired 注入bean使用

        ```java
        @Service("TestService") //或变成Bean
        Public Class TestService{
            @Autowired
            private Dao myDao

            public void test(){
                myDao.print();
            }

        }
        ```

    @Configuration //配置类使用

        @Bean 

        ```java
        @Configuration  
        Public Class TestConfig{

            @Bean("mydao")
            public Dao test(){
              return  new Dao()
            }

        }
        ```

    @Component //通常使用在util

* Bean
  
    Bean是框架预先注入的单例实例；（个人理解）

    预处理，以及有相关的生命周期管理；

## 问题

1. log4j多类包冲突

    原因：springBoot使用的logBack底层也是log4j，与其他空间log4j的版本号冲突
    使用maven处理,在poem文件下相应的依赖上增加exclude标签排除处理

    JAVA中常用的日志库

    Log4j
    Log4J2
    Logging:Jdk的默认log
    LogBack:springBoot默认


   ```shell

   ```
2. 关于jdkLogging不能输出到文件问题;