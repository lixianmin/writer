



---

#### 0x01 常见注解



| 注解                        | 描述 |
| --------------------------- | ---- |
| @ApiOperation | 用于Swagger构建API文档 |
| @ApiParam | 用于Swagger构建API文档 |
| @Aspect | 把当前类标记为一个切面供容器读取 |
| @EnableTransactionManagement | 开启注解事务管理，为@Transactional做准备 |
| @GetMapping | 组合注解，等价于@RequestMapping(method = RequestMethod.GET)，将http get请求映射到特定的处理方法上 |
| @Interface                        | 用于声明一个注解                                             |
| @Override                    | 表明这是一个override方法                                    |
| @PostMapping | 组合注解，将http post请求映射到特定的处理方法上 |
| @RequestHeader | 表明参数来源于http请求中的header部分 |
| @RequestParam | 表明参数来源于http请求中的param部分 |
| @ResponseBody | 将controller中方法返回的对象通过系统配置的HttpMessageConverter转化为指定格式之后，写入到response对象的body区，大抵相当于： response.getWriter().write(JSON.toJSON(user).toString()); |
| @Throws(IOException::class)  | 告诉编译器该方法抛出了异常。因为kotlin中没有checked exception，此注解用于java中调用此方法因编译出错 |
|                              |                                                              |



----

#### 0x02 Autowired系列



| 注解              | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| @Autowired        | 按照type注入                                                 |
| @Bean             | 方法级注解，被表注方法的返回值是一个Bean。该方法也可以有其它**依赖的Bean参数** |
| @Component        | 泛指组件，当组件不好归类的时候使用这个注解                   |
| @Controller       | 标注控制层组件                                               |
| @ControllerAdvice | 配合@ExceptionHandler统一处理controller层的异常              |
| @Qualifier        | @Autowired按照type注入，当相同type的bean不止一个时，用@Qualifier传入name确定 |
| @Repository       | 标注DB访问组件                                               |
| @Resource         | 默认根据name注入，其次按照type搜索                           |
| @Service          | 标注业务层组件                                               |
|                   |                                                              |



---

#### 0x03 数据库操作

| 注解    | 描述                           |
| ------- | ------------------------------ |
| @Select | ibatis中把方法影射为数据库操作 |
|         |                                |
|         |                                |



---

#### 0x04 元注解



| 注解                                | 描述                                                      |
| ----------------------------------- | --------------------------------------------------------- |
| @Document                           | 说明该注解将被包含在javadoc中                             |
| @Inherited                          | 说明子类可以继承父类中的该注解                            |
| @Retention(RetentionPolicy.RUNTIME) | 注解会在class字节码文件中存在，在运行时可以通过反射获取到 |
| @Target(ElementType.METHOD)         | 注解的目标是方法                                          |



----

#### 0x09 References

1. [Spring注解@Component、@Repository、@Service、@Controller区别](https://blog.csdn.net/zhang854429783/article/details/6785574)
2. 







