# spring boot 技巧

## 整合swagger，快速生成api文档

引入依赖

```xml
        <!--api快速生成-->
        <!--Swagger-->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.9.2</version>
        </dependency>

        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.2.2</version>
        </dependency>
```

添加配置

```java
@Configuration
@EnableSwagger2 // api快速生成
public class swagger2Config {
    @Bean
    public Docket createRestApi(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage("club.zw.hotel.controller"))  //需要生成api的包
                .paths(PathSelectors.any())
                .build();
    }


    private ApiInfo apiInfo(){
        return new ApiInfoBuilder()
                .title("酒店管理系统 APIS")   // api文档名称
                .description("无更多信息")       // 描述
                .termsOfServiceUrl("www.baidu.com") //服务条款网址
                .version("1.0.1")   
                .build();
    }
}
```

[访问链接：http://localhost:8080/swagger-ui.html 即可]: http://localhost:8080/swagger-ui.html

## lombok 自动生成

1. 引入依赖

   ```xml
           <!--lombok-->
           <dependency>
               <groupId>org.projectlombok</groupId>
               <artifactId>lombok</artifactId>
               <version>1.18.10</version>
               <scope>provided</scope>
           </dependency>
   ```

2. 在idea中安装lombok插件

3. 使用注解即可

   - @Data
   - @Getter
   - @Setter

- @Sl4j

  注意：如果使用setter注解，则命名不能为下划线。



## 热部署

```xml
        <!--热部署-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <optional>true</optional>
        </dependency>
```

在build标签中添加

```xml
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <fork>true</fork>
                </configuration>
            </plugin>
```

即可实现热部署，无需重启项目。

## Json 再封装

创建符合格式的返回数据类

```java
public class JsonMap {
    private int code;
    private String msg;
    private String data = null;

    public JsonMap(int code, String msg, String data) {
        this.code = code;
        this.msg = msg;
        this.data = data;
    }

    public int getCode() {
        return code;
    }

    public void setCode(int code) {
        this.code = code;
    }

    public String getMsg() {
        return msg;
    }

    public void setMsg(String msg) {
        this.msg = msg;
    }

    public String getData() {
        return data;
    }

    public void setData(String data) {
        this.data = data;
    }

    @Override
    public String toString() {
        return "{ \"code\":" + code
                + ",\"msg\":\"" + msg
                + "\",\"data\":" + data
                + "}";
    }
}
```

书写静态方法方便调用

```java
public  class toJsonString {

    public static String toJsonString(int code, String msg, String data){
        return new JsonMap(code,msg,data).toString();
    }
}
```

实际调用

```java
    @PutMapping("/updateNotPayOrdersStatus/{id_number}")
    public String updateNotPayOrdersStatus(@PathVariable("id_number") String id_number) {
        if (orderService.updateNotPayOrdersStatus(id_number)) {
            return toJsonString(200, "更新未支付账单状态成功", null);
        } else {
            return toJsonString(100, "更新未支付账单状态失败", null);
        }
    }
```

## string 字符串转 指定类型list

书写自定义类型的组件

```java
package AI.hotel.bean.customResult;

import java.io.Serializable;

public class OrderIdAndImgName implements Serializable {

    private Integer id;
    private String recent;

    public OrderIdAndImgName(Integer id, String recent) {
        this.id = id;
        this.recent = recent;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getImgName() {
        return recent;
    }

    public void setImgName(String imgName) {
        this.recent = imgName;
    }

}
```

书写string转list的方法

```java
    public static List<OrderIdAndImgName> stringToArray(String string) {
        List<OrderIdAndImgName> list = new ArrayList<>();

        List inventoryResultList = JSONObject.parseArray(string);

        for (Integer i = 0; i < inventoryResultList.size(); i++) {
            JSONObject jsonObject = (JSONObject) inventoryResultList.get(i);
            String imgName = jsonObject.getString("imgName");
            int id = Integer.parseInt(jsonObject.getString("id"));
            list.add(new OrderIdAndImgName(id,imgName));
        }
        return list;
    }
```

外部调用

```java
        //字符串解析返回list
        List<OrderIdAndImgName> data = stringToArray(list);
```
