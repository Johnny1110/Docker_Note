# Redis Web API Service

<br>

---

<br>

使用 Spring Boot 搭建簡單的 Web API service，串接 redis 服務。

<br>
<br>


## pom.xml

<br>

這裡並沒有什麼需要過多解釋的，開發過 Spring Boot 的工程師應該都看得懂。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.frizo.lib</groupId>
    <artifactId>lib-demo</artifactId>
    <version>1.0-SNAPSHOT</version>
    <name>lib demo</name>

    <properties>
        <spring.boot.version>2.2.1.RELEASE</spring.boot.version>
        <start-class>com.frizo.lib.redis.demo.RedisWebApplication</start-class>
        <maven.compiler.version>3.8.1</maven.compiler.version>
        <maven.resource.version>3.1.0</maven.resource.version>
    </properties>

    <dependencies>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>${spring.boot.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
            <version>${spring.boot.version}</version>
        </dependency>

    </dependencies>


    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>${maven.compiler.version}</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>

            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <version>${maven.resource.version}</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>

            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <fork>true</fork>
                    <mainClass>${start-class}</mainClass>
                </configuration>
                <executions>
                    <execution>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>

        </plugins>
    </build>

</project>
```

<br>
<br>
<br>
<br>

## application.properties

<br>

這裡是關於連接 redis 與 tomcat 的設定，這邊注意我們 `redis.host`，在以往開發中，我們經常會在本機架設一個測試用的 redis 服務，所以基本上這個 host 都是設定成 `localhost`。但是這次我們要用 docker 來做，所以我們直接把 `redis.host` 設定成將來 redis 的容器名稱。如果不理解為什麼要這樣做的話，請回到 [Docker 容器間溝通](../../../c2c) 重新看一遍。

```properties
server.port= 4000

spring.redis.host=redis
spring.redis.port=6379
spring.redis.database=0
spring.redis.password=
spring.redis.jedis.pool.max-active=8
spring.redis.jedis.pool.max-wait=-1
spring.redis.jedis.pool.max-idle=8
spring.redis.jedis.pool.min-idle=0
```

<br>
<br>
<br>
<br>

## RedisConfig.java

<br>

RedisConfig.java 是用來設定做 RedisTemplate 調用設定的文件，沒有什麼好講解的，都是從官方文件抓下來的程式：

```java
@Configuration
@EnableAutoConfiguration
public class RedisConfig {

    @Bean
    @ConditionalOnMissingBean(name = "redisTemplate")
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {

        Jackson2JsonRedisSerializer<Object> jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer<Object>(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);

        RedisTemplate<String, Object> template = new RedisTemplate<String, Object>();
        template.setConnectionFactory(redisConnectionFactory);
        template.setKeySerializer(jackson2JsonRedisSerializer);
        template.setValueSerializer(jackson2JsonRedisSerializer);
        template.setHashKeySerializer(jackson2JsonRedisSerializer);
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        template.afterPropertiesSet();
        return template;
    }

    @Bean
    @ConditionalOnMissingBean(StringRedisTemplate.class)
    public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory) {
        StringRedisTemplate template = new StringRedisTemplate();
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }

}
```

寫好這個 Bean 設定，我們就可以在任何其他 Bean 內自動注入 `StringRedisTemplate` 了。

<br>
<br>
<br>
<br>

## RedisService

<br>

說實話，這一個 Service 根本沒必要寫，直接在 Controller 裡面 AutoWired StringRedisTemplete 就好了。但是... 習慣是改不掉的。

<br>

### RedisService.java

```java
public interface RedisService {

    void set(String key, String value);

    String get(String key);

}
```

<br>

### RedisServiceImpl.java

```java
@Service
public class RedisServiceImpl implements RedisService{

    @Autowired
    private StringRedisTemplate stringRedisTemplate;

    @Override
    public void set(String key, String value) {
        stringRedisTemplate.opsForValue().set(key, value);
    }

    @Override
    public String get(String key) {
        String data = stringRedisTemplate.opsForValue().get(key);
        return data;
    }
}
```

<br>
<br>
<br>
<br>

## Controller

<br>

最後就是 Controller 的部份，先來寫一個歡迎頁：

<br>

### BaseController.java

<br>

```java
@RestController
public class BaseController {

    @GetMapping("/")
    public String home(){
        return "welcome to redis web app";
    }
}
```

<br>
<br>

然後就是 Redis 服務相關的 API：

<br>

### RedisController.java

<br>

```java
@RestController
@RequestMapping("/redis")
public class RedisController {

    @Autowired
    private RedisService redisService;

    @GetMapping("/set")
    public String setToRedis(@RequestParam("key") String key, @RequestParam("value") String data){
        redisService.set(key, data);
        return "success";
    }

    @GetMapping("/get")
    public String getValueFromRedis(@RequestParam("key") String key){
        String data = redisService.get(key);
        return data;
    }
}
```

<br>
<br>
<br>
<br>


## 建構 jar 檔

<br>

cd 到與 pom.xml 同級的目錄，執行以下指令（環境要有 jdk 與 maven）:

```bash
mvn clean package
```

<br>

執行完成之後，在 target 資料夾中會找到一個 `lib-demo-1.0-SNAPSHOT.jar`，這個就是之後要放入容器中的 jar 檔。

<br>

---

<br>

以上就是這個小 web 應用導讀。


