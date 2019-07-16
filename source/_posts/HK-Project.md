---
title: 新项目三个月小结
date: 2018-07-23 21:12:02
categories: 
    - 项目总结
tags:
    - Java 8
    - Spring
    - Mock
    - Guava
    - Lombok
    - FastJson
    - Linux
---

明天要转正了，虽然这三个月一直在苦逼的加班，但是学到了不少新知识，趁最近没那么忙了总结一下。

<!-- more -->

# <font color = "#159957">Java 8</font>
新项目用的JDK 1.8，以前有粗略了解过Java 8，正真上手后不由得感慨：函数式编程真的好爽。

### <font color = "#159957">Stream</font>
通过stream()方法获取对象的数据集，通过一系列中间（Intermediate）方法，对数据集进行过滤、检索等再处理，最后通过最终（terminal）方法完成对数据集中元素的处理。

```Java
private static final String SEPERATOR = ",";
//将多个字符串以逗号分隔拼接
public String buildMessage(String... args) {
    return Arrays.stream(args).collect(Collectors.joining(SEPERATOR));
}
```

### <font color = "#159957">Lamda</font>

```Java
    Student xiaoMing = Student.builder()
            .name("小明")
            .no(1)
            .age(18)
            .sex("男")
            .build();
    Student xiaoHong = Student.builder()
            .name("小红")
            .no(2)
            .age(19)
            .sex("女")
            .build();
    List<Student> studentList = Lists.newArrayList(xiaoMing, xiaoHong);
    //选取
    List<String> nameList = studentList.stream().map(Student::getName).collect(Collectors.toList());
    //过滤
    List<Student> boyStudents = studentList.stream()
            .filter(student -> student.getSex().equals("男"))
            .collect(Collectors.toList());
    //排序
    studentList.stream().sorted((x,y) -> {
        if (x.getAge() == y.getAge()) {
            return x.getName().compareTo(y.getName());
        }else {
            return x.getAge() > y.getAge() ? 1 : -1;
        }
    }); 
```

### <font color = "#159957">CompletableFuture</font>

使用CompletableFuture进行异步操作。

```Java
@Service
public class StudentService {

    @Resource
    private AsyncStudentService asyncStudentService;

    public void doHomeWork() throws ExecutionException, InterruptedException {
        //skip build Student
        Future<HomeWork> xiaoMingWorkFuture = asyncStudentService.doHomeWork(xiaoMing);
        Future<HomeWork> xiaoHongWorkFuture = asyncStudentService.doHomeWork(xiaoHong);
        //get the result
        HomeWork xiaoMingWork = xiaoMingWorkFuture.get();
        HomeWork xiaoHongWork = xiaoHongWorkFuture.get();
    }
}
```

```Java
@Service
public class AsyncStudentService {

    @Async
    public Future<HomeWork> doHomeWork(Student student) {
        HomeWork work = new HomeWork();
        //student do homework
        //...
        return CompletableFuture.completedFuture(work);
    }
}
```

当要对一组List异步操作时，可以将List分组再操作:

```Java
public static <T> List<List<T>> splitList(List<T> list, int len) {
    if (CollectionUtils.isEmpty(list)) {
        return Collections.emptyList();
    }
    List<List<T>> result = Lists.newArrayList();
    int size = list.size();
    int count = (size + len - 1) / len;
    for (int i = 0; i < count; i++) {
        List<T> subList = list.subList(i * len, ((i + 1) * len > size ? size : len * (i + 1)));
        result.add(subList);
    }
    return result;
}

public List<HomeWork> doHomeWork(List<HomeWork> homeWorkList) throws ExecutionException, InterruptedException {

    List<List<HomeWork>> workListList = splitList(homeWorkList, 10);
    List<Future<HomeWork>> resultFuture = Lists.newArrayList();
    workListList.forEach(workList ->{
        resultFuture.add(asyncStudentService.doHomeWork(workList));
    });
    List<HomeWork> resultList = Lists.newArrayList();
    for (Future<HomeWork> homeWorkFuture : resultFuture) {
        resultList.add(homeWorkFuture.get());
    }
    return resultList;
}
```

### <font color = "#159957">Optional</font>
Java 8提供的Optional类主要用来优雅的解决NPE问题(NullPointerException)。

```Java
Student student = new Student();
//student有可能为空
Optional<Student> optional = Optional.ofNullable(student);
optional.map(s -> s.getName())
        .map(name -> name.toUpperCase())
        .orElse(null);
        //.orElseThrow(() -> new Exception());
```

如果不使用Optional，代码将会很冗余：
```Java
if(student != null) {
  String name = user.getName();
  if(name != null) {
    return name.toUpperCase();
  } else {
    return null;
  }
} else {
  return null;
}
```

# <font color = "#159957">Spring 定时任务</font>

1. 定义定时任务表及对应实体类，定义定时任务父类，定义子类重写process方法:

```Java
@Data
public class JobEntity {
    private Integer id;
    private String jobServiceName;
    private Data jobTime;
    private Integer status;
    private Integer version;
}

public class TaskJobService {
    public void process() {

    }
}
```

2. 通过Spring自带的``@EnableScheduling``和``@Scheduled``定义定时任务

```Java
@Component
@EnableScheduling
public class ScheduleTaskJob {
    @Scheduled(cron = "0 0/5 * * * *")
    public void queryJobList() {
        //read from task_job
        List<TaskJobService> taskJobList = Lists.newArrayList();
        for (TaskJobService job : taskJobList) {
            try {
                //从上下文中拿到jobName对应的Service并执行process方法
                doProcess();
            }finally {
                //每次执行完job version加1，更新job状态为已完成
                updateJob();
            }
        }
    }
}
```

3. 从上下文中拿到jobName对应的Service并执行process方法

```Java
@Service
public class JobService extends ApplicationObjectSupport {
    public void doProcess(String jobServiceName) {
        ApplicationContext applicationContext = getApplicationContext();
        TaskJobService job = (TaskJobService)applicationContext.getBean(jobServiceName);
        job.process();
    }
}
```

# <font color = "#159957">Mock单元测试</font>

mock是模拟对象，用于模拟真实对象的行为。Powermock主要用于打桩。比如：方法A的参数需要传入实例B，方法A需要调用B的某个方法B.C()。方法C因为耗时长或者根本没有实现或者其他不方便在单元测试中实现等原因，需要伪造返回，此时Powermock即可派上用场。

pom文件中引入:

```Java
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
</dependency>
<dependency>
    <groupId>org.powermock</groupId>
    <artifactId>powermock-module-junit4</artifactId>
</dependency>
<dependency>
    <groupId>org.powermock</groupId>
    <artifactId>powermock-api-mockito2</artifactId>
</dependency>
```

编写Controller测试类：

```Java
@RunWith(SpringRunner.class)
@SpringBootTest
@AutoConfigureMockMvc
public class DemoControllerTest {

    private MockMvc mockMvc;

    @MockBean
    private DemoService demoService;

    @InjectMocks
    private DemoController demoController;

    //初始化MockMvc
    @Before
    public void setUp() throws Exception {
        MockitoAnnotations.initMocks(this);
        this.mockMvc = MockMvcBuilders.standaloneSetup(demoController).build();
    }

    @Test
    public void demoMethod() throws Exception {
        //1. 组装入参
        String requestBody = null;
        //2. 打桩Service
        String result = null;
        when(demoService.demoMethod(any(String.class))).thenReturn(result);
        //3. 测试接口,断言
        this.mockMvc
                .perform(MockMvcRequestBuilders.post("/test").contentType(MediaType.APPLICATION_JSON).content(requestBody))
                .andDo(MockMvcResultHandlers.print())
                .andExpect(MockMvcResultMatchers.status().isOk())
                .andExpect(jsonPath("$.status").value("200"));
    }
}
```

# <font color = "#159957">好用的Java类库</font>

### <font color = "#159957">Guava</font>

```Java
import com.google.common.collect.Lists;
import com.google.common.collect.Maps;

//使用Guava创建集合
ArrayList<Student> students = Lists.newArrayList();
ArrayList<String> strings = Lists.newArrayList("a", "b");
HashMap<Object, Object> hashMap = Maps.newHashMap();
```

### <font color = "#159957">Lombok</font>

使用@Data代替get(),set()方法，@ToString生成类的toString()方法，@Builder可以快速使用构建模式，等等。

```Java
@Data
@ToString
@Builder
public class Student {

    private String name;
    private Integer no;
    private Integer age;
    private String sex;

    public Student() {
    }
}
```

### <font color = "#159957">FastJson</font>

```Java
//List和String互转
String string = JSON.toJSONString(Lists.newArrayList("a", "b"));
List<String> list = JSON.parseArray(string, String.class);
```

### <font color = "#159957">其他</font>

使用@RestController代替@ResponseBody + @Controller；使用@PostMapping代替@RequestMapping(method = RequestMethod.POST),@GetMapping代替@RequestMapping(method = RequestMethod.GET)等

```Java
//返回空List
List<Object> objects = java.util.Collections.emptyList();
//判断List是否为空
org.springframework.util.CollectionUtils.isEmpty(Lists.newArrayList());
```

# <font color = "#159957">Linux</font>

```bash
#动态查看50行日志
tail -fn 50 test.log
# 查看时间段日志
sed -n '/2018-07-28 23:30:00/,/2018-07-28 23:30:00/p' test.log
# grep同时满足多个条件
grep "word1" test.log | grep "word2"
# 当用grep搜线程号时，除了显示包含线程号的那一列之外，并显示该列之前后的内容。
grep -C 5 "threadNo" test.log
# 日志文件不大时，用vim命令查看，输入以/为开头的命令，n查找下一个，N查找上一个
# 远程调用json格式的post请求
curl -l -H "Content-type: application/json" -X POST -d '{"name":"test","password":"test"}' http://127.0.0.1:8080/test
# 下载日志文件到本地(send),上传是rz(receive)
yum install -y lrzsz #先安装
sz test.log
# less查看命令
# G移到最后一行，使用？匹配前一个文本，n向后查询，N向前查询，用/匹配时相反
# ctrl+F 向前移动一屏，ctrl+B向后移动一屏，j向下移动一行，k向上移动一行，space向下翻一页，b向上翻一页
less test.log
```

# <font color = "#159957">其他</font>

1. IDEA Live Templates
-  进入Live Templates菜单，新建模板：
![](/images/article_pictures/idea_live_template.jpeg)

- 在代码中输入my_logger等快捷输出代码：
```Java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
public class Test{
    //my_logger
    private static final Logger LOGGER = LoggerFactory.getLogger(Test.class);
    public void test(){
        //my_logger_info_json
        String str = null;
        LOGGER.info("Test_test_str:{}",JSON.toJSONString(str));
    }
}
```

