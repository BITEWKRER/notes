## SSM接口返回json格式数据
添加依赖
```
    <!--json jar包-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.62</version>
        </dependency>
```
修改springmvc.xml的annotation 标签数据,改为如下格式
```
   @RequestMapping(value = "/getAllUsers", produces = "text/html;charset=utf-8")
    @ResponseBody
    public String getAllUsers() {
        List<User> users = service.getAllUsers();
        return "{ \"code\":200,\"message\":\"成功\",\"data\":" + JSONObject.toJSONString(users) + ",\"count\" : \"10\"}";
    }
```
前端layui页面设置response
```
<script>
    layui.use('table', function () {
        var table = layui.table;

        table.render({
            elem: '#test'
            , url: 'http://localhost:8080/ssmWork_war_exploded/getAllUsers'
            , cellMinWidth: 80
            , response: {
                statusName: 'code',
                statusCode: 200,
                msgName: 'msg',
                countName: 'count',
                dataName: 'data'
            }
            , cols: [[
                {field: 'id', width: 80, title: 'ID', sort: true}
                , {field: 'name', width: 80, title: '用户名'}
                , {field: 'sexual', width: 80, title: '性别'}
                , {field: 'password', width: 300, title: '密码'}
                , {field: 'integral', width: 80, title: '积分', sort: true}
                , {field: 'total', width: 120, title: '文章总数', sort: true}
                , {field: 'signature', title: '签名', width: '30%', minWidth: 100}
            ]]
        });
    });
</script>
```
http://localhost:8080/ssmWork_war_exploded/getAllUsers

## SSM 分页实现
dao
```
	// bean对象自动解析，单独的参数需要使用param1，param2，同样也可以使用注解标识
    @Select("select * from users limit #{param1},#{param2}")
    public List<User> PaginationUser(Integer start, Integer num);
```
service
```
  public List<User> PaginationUser(Integer start,Integer num);
```
serviceImpl
```
    @Override
    public List<User> PaginationUser(Integer start, Integer num) {
        return dao.PaginationUser(start, num);
    }
```
controller
如果返回文本："text/html;charset=utf-8"
如果返回json："application/json;charset=utf-8"
```
    @RequestMapping(value = "/PaginationUser", produces = "application/json;charset=utf-8" )
    @ResponseBody
    public String PaginationUser(@RequestParam(name = "page") Integer page) {
        int start = 1; // 默认第一条数据为1
        int num = 5; //规定返回数据量
        List<User> users;
        if (page == 1) {
            users = service.PaginationUser(0, num); //数据库从0开始
        } else {
            users = service.PaginationUser((page * num) - 1, num);
        }

        int count = service.countAll();

        // 数据封装
        if (users.size() != 0) {
            return "{ \"code\":200,\"message\":\"成功\",\"data\":" + JSONObject.toJSONString(users) + ",\"count\" : " +
                    (count % 5 == 0 ? count / 5 : (int) (count / 5) + 1)
                    + "}";
            //计算页数
        } else {
            return "{ \"code\":100,\"message\":\"失败\",\"data\": [],\"count\" : 0}";
        }
    }
```
最终效果(注意观察页数也返回的数据)
请求页数不存在（一般不会存在这种情况，这需要前端渲染）
![a610c644.png](../images/cf739aff-ee7d-422a-852d-7729744105c2/a610c644.png)
请求正常
![a032d00a.png](../images/cf739aff-ee7d-422a-852d-7729744105c2/a032d00a.png)
第二页
![21aed882.png](../images/cf739aff-ee7d-422a-852d-7729744105c2/21aed882.png)
## SSM处理 读取 resources文件下的dao.xml文件
在pom.xml文件下build标签下加入如下语句即可实现xml语句书写
```
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                    <include>**/mapper/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>
        </resources>
```
## SSM 文件上传 
pom.xml加入依赖
```
        <!--文件上传-->
        <dependency>
            <groupId>commons-fileupload</groupId>
            <artifactId>commons-fileupload</artifactId>
            <version>1.3.1</version>
        </dependency>
```
applicationContext.xml加入配置
```
    <!--文件上传-->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver"/>
```
