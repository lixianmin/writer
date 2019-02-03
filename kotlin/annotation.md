



---

#### 0x01 常见注解



| 注解                        | 描述 |
| --------------------------- | ---- |
| @ApiOperation | 用于Swagger构建API文档 |
| @ApiParam | 用于Swagger构建API文档 |
| @GetMapping | 组合注解，等价于@RequestMapping(method = RequestMethod.GET)，将http get请求映射到特定的处理方法上 |
| @Interface                        | 用于声明一个注解                                             |
| @Override                    | 表明这是一个override方法                                    |
| @PostMapping | 组合注解，将http post请求映射到特定的处理方法上 |
| @RequestHeader | 表明参数来源于http请求中的header部分 |
| @RequestParam | 表明参数来源于http请求中的param部分 |
| @Throws(IOException::class)  | 告诉编译器该方法抛出了异常。因为kotlin中没有checked exception，此注解用于java中调用此方法因编译出错 |
|                             ||



----

#### 0x02 Autowired系列



| 注解        | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| @Autowired  | 标注字段可以被容器注入                                       |
| @Bean       | 方法级注解，被表注方法的返回值是一个Bean。该方法也可以有其它**依赖的Bean参数** |
| @Component  | 泛指组件，当组件不好归类的时候使用这个注解                   |
| @Controller | 标注控制层组件                                               |
| @Repository | 标注DB访问组件                                               |
| @Service    | 标注业务层组件                                               |
|             |                                                              |



----

#### 0x09 References

1. [Spring注解@Component、@Repository、@Service、@Controller区别](https://blog.csdn.net/zhang854429783/article/details/6785574)
2. 







