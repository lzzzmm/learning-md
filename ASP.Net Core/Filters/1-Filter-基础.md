# 1-asp.net core-Filter-基础
## 1、原理
筛选器是可以在请求管道中的特定阶段之前或之后运行代码。
![2024-10-14-03-15-53.png](./images/2024-10-14-03-15-53.png)

只有当路由选择了MVC Action之后，过滤器管道才有机会执行。

过滤器有多种：
![2024-10-14-03-17-32.png](./images/2024-10-14-03-17-32.png)

- Authorization Filters（授权过滤器）
- Resource Filters（资源过滤器）
- Action Filters（操作过滤器）
- Exception Filters（异常过滤器）
- Result Filters（结果过滤器）

## 2、过滤器作用域
过滤器的作用域范围，可分为三种，从小到大是：
- 某个Controller中的某个Action上（不支持Razor Page中的处理方法）
- 某个Controller或Razor Page上
- 全局，应用到所有Controller、Action和Razor Page上

对于同一个过滤器，执行顺序（以IActionFilter为例）：
- 1、全局过滤器OnActionExecuting
- 2、Controller和Razor Page过滤器的OnActionExecuting
- 3、Action过滤器的OnActionExecuting
- 4、Action过滤器的OnActionExecuted
- 5、Controller和Razor Page过滤器的 OnActionExecuted
- 6、全局过滤器的 OnActionExecuted

## 3、过滤器注册方式
### 3.1 全局
```cs
services.AddControllers(options =>
{
    options.Filters.Add(typeof(ShoppingActionFilter)); // 全局过滤器
});
```

### 3.2 Controller、Razor Page 或 Action
作用域为 Controller、Razor Page 或 Action 在注册方式上来说，都是以特性的方式进行标注。
```cs
[ServiceFilter(typeof(ShoppingActionFilter))] //控制器级别
public class EasyTestController : ControllerBase{}
```

## 4、过滤器上下文
过滤器方法参数会继承FilterContext，而FilterContext继承ActionContext。

ActionContext：
![2024-10-14-08-08-07.png](./images/2024-10-14-08-08-07.png)

下面对各个字段进行解释，不全，可以直接到编辑器看源代码。

### 4.1 ActionDescriptor
- Id : 标识该Action的唯一标识
- RouteValues : 路由字典，包含了controller、action的名字等
- AttributeRouteInfo : 特性路由的相关信息
- ActionConstraints : Action的约束列表
- EndpointMetadata : 终结点元数据
- Parameters : 路由中的参数列表，包含参数名、参数类型、绑定信息等
- FilterDescriptors : 过滤器管道中与当前Action有关的过滤器列表
- DisplayName : Action的个性化名称
- Properties : 共享元数据

### 4.2 HttpContext
- Request：表示当前 HTTP 请求的详细信息，包括请求方法、请求路径、请求头、查询字符串、请求体等
- Response：当前 HTTP 响应的信息，包括状态码、响应头、响应体等
- User：当前用户的身份信息
- Items：一个字典，用于存储请求级别的临时数据。这些数据在请求的生命周期内是有效的
- Connection：提供有关客户端连接的信息，如 IP 地址、端口号等

### 4.3 ModelState
- IsValid：指示模型状态是否有效。如果所有的验证都通过，则为 true；否则为 false
- ValidationState
    - Valid：模型有效
    - Invalid：模型无效
    - Skipped：模型未进行验证

### 4.4 RouteData
- DataTokens：存储与路由相关的额外数据，通常用于传递元数据。这些数据在路由匹配时可用，但不会参与路由的匹配逻辑
- Routers ：Microsoft.AspNetCore.Routing.IRouter 的实例列表，与当前请求相关联的路由信息，通常是与匹配的路由模板相关的信息
- Values：存储路由匹配的值，包括从 URL 路径、查询字符串和路由参数提取的值。键是参数名称，值是参数的实际值

```cs
public class ShoppingResourceFilter : IResourceFilter
{
    public void OnResourceExecuting(ResourceExecutingContext context)
    {
        var request = context.HttpContext.Request; // 获取当前HTTP请求
        var path = request.Path; //路径
        var query = request.QueryString; // 查询字符串
        var headers = request.Headers; // 请求头
        
        var user = context.HttpContext.User; // 访问用户信息

        var dbContext = context.HttpContext.RequestServices.GetService<ShoppingDbContext>(); // 访问依赖注入的服务
        
        // 访问和设置用户会话数据
        var session = context.HttpContext.Session;
        session.SetString("Key", "Value");
        var value = session.GetString("Key");

        context.Result = new BadRequestResult(); // 短路用的
    }

    public void OnResourceExecuted(ResourceExecutedContext context)
    {
        var response = context.HttpContext.Response;
        response.Headers.Add("X-Custom-Header", "value");
        response.StatusCode = 200; // 设置状态码
    }
}
```


## 5、过滤器类型
### 5.1 Authorization Filters
具有在它之前的执行的方法，但没有之后执行的方法。

自定义授权筛选器需要自定义授权框架。 建议配置授权策略或编写自定义授权策略，而不是编写自定义筛选器。

（不建议用的话就不写demo啦~老老实实按Authentication来）

### 5.2 Resource Filters
实现 IResourceFilter 或 IAsyncResourceFilter 接口（其他过滤器都有这两种类型的接口，如命名，一个同步一个异步）

#### 5.2.1 IResourceFilter
```cs


```
### 5.3 Action Filters

### 5.4 Exception Filters

### 5.5 Result Filters

