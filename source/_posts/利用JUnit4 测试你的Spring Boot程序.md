---
title: 利用JUnit4 测试你的Spring Boot程序
tags:
  - springboot
  - 单元测试
categories:
  - WEB开发
date: 2018-06-14 10:48:01
id: test-your-springboot-application-with-JUnit4
---


使用spring boot写接口时，开发人员习惯使用postman等接口调试工具来进行调试。
这当然没有什么问题！可以！

但是怎么解决下面的问题：
1. 每次对程序的某个部分进行修改程序的时候是否对其他部分产生影响就不知道了，难道要每一次都使用postman来进行操作？
2. 怎么自证？毕竟你的postman只运行在你的机器上。测试人员告诉你在某个条件下你的接口出问题了，怎么使用代码来复现这个过程？


使用JUnit可以解决上述问题，当然不仅仅是上述问题。
<!-- more -->

一般我们使用JUnit来编写单元测试代码，而今天分享的是编写Spring boot的类似集成代码。
目前已经开始要求项目组接口开发人员在发布接口之前必需要编写类似自证代码，通过之后才可发布。

测试的Demo代码如下：

``` java
@SpringBootTest
@RunWith(SpringJUnit4ClassRunner.class)
@ActiveProfiles("test") //激活test profiles。在该profiles下使用测试数据库
public class BootApplicationTest {

    private MockMvc mockMvc;

    @Autowired
    private WebApplicationContext wac;

    String url_prefix = "/test-authority";


    @Before
    public void setShiro() {
        SecurityUtils.setSecurityManager(wac.getBean(SecurityManager.class));//因为项目使用到了apache shiro框架，在这里设置下SecurityManager
    }



    @Before
//    @Sql(scripts = "classpath:schema.sql", executionPhase = Sql.ExecutionPhase.BEFORE_TEST_METHOD) 可以在测试代码跑之前执行某sql文件来初始化测试数据。当然使用在resources目录下附加上schema.sql 和data.sql语句也可同样初始化测试数据库。
    public void setUp() throws Exception {
        MockitoAnnotations.initMocks(this);//使用mock初始化webapplicationcontext
        mockMvc = MockMvcBuilders
                .webAppContextSetup(wac)
                .build();
    }

    @Test
    public void pingTest() throws Exception {
        MvcResult result = mockMvc.perform(MockMvcRequestBuilders.get("/ping"))
                .andExpect(status().isOk())
                .andExpect(MockMvcResultMatchers.status().isOk())
                .andExpect(content().contentType("text/plain;charset=UTF-8"))
                .andExpect(content().string("pong"))
                .andDo(MockMvcResultHandlers.print())
                .andReturn();
    }


    @Test
    public void userGetinfoWithoutAuthority() throws Exception {
        mockMvc.perform(MockMvcRequestBuilders.post(url_prefix +"/user/getInfo")
        )
                .andExpect(status().isOk())
                .andExpect(MockMvcResultMatchers.status().isOk())
                .andExpect(content().contentType("application/json;charset=UTF-8"))
                .andExpect(content().json("{'returnCode':'20011','returnMsg':'登陆已过期,请重新登陆','returnData':{}}"))
                .andExpect(jsonPath("$.returnCode").value(20011))
                .andExpect(jsonPath("$.returnMsg").value("登陆已过期,请重新登陆"))
                .andDo(MockMvcResultHandlers.print())
                .andReturn();
    }


    @Test
    public void loginAuthPasswordError() throws Exception {
        JSONObject requestBody = new JSONObject();
        requestBody.put("username", "admin");
        requestBody.put("password", "123123");

        mockMvc.perform(MockMvcRequestBuilders.post(url_prefix +"/login/auth").
                contentType(MediaType.APPLICATION_JSON).content(requestBody.toJSONString()))
                .andExpect(status().isOk())
                .andExpect(MockMvcResultMatchers.status().isOk())
                .andExpect(content().contentType("application/json;charset=UTF-8"))
                .andExpect(content().json("{'returnCode':'10003','returnMsg':'账户名或密码错误','returnData':{}}"))
                .andExpect(jsonPath("$.returnCode").value(10003))
                .andExpect(jsonPath("$.returnMsg").value("账户名或密码错误"))
                .andDo(MockMvcResultHandlers.print())
                .andReturn();
    }

    @Test
    public void loginAuthPasswordCorrect() throws Exception {
        JSONObject requestBody = new JSONObject();
        requestBody.put("username", "admin");
        requestBody.put("password", "e10adc3949ba59abbe56e057f20f883e");

        mockMvc.perform(MockMvcRequestBuilders.post(url_prefix +"/login/auth").
                contentType(MediaType.APPLICATION_JSON).content(requestBody.toJSONString()))
                .andExpect(status().isOk())
                .andExpect(MockMvcResultMatchers.status().isOk())
                .andExpect(content().contentType("application/json;charset=UTF-8"))
                .andExpect(content().json("{}"))
                .andExpect(jsonPath("$.returnCode").value(100))
                .andExpect(jsonPath("$.returnMsg").value("请求成功"))
//                .andExpect(jsonPath("$.returnData").value(100))

                //{"returnCode":"100","returnMsg":"请求成功","returnData":{"result":"success","token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE1Mjg2OTgzNDEsInVzZXJuYW1lIjoiYWRtaW4ifQ.qIAwNtouYmPmWpST0di9x4LvcA2yHQ_HCSBxsYA1Abk"}}
                .andDo(MockMvcResultHandlers.print())
                .andReturn();
    }


}
```

作为demo，上述测试用例代码模拟了用户登录操作过程中的几个过程。包括：
1. 暂无意义的ping接口。
2. 在没有授权信息时调用获取用户信息的用例：userGetinfoWithoutAuthority
3. 模拟密码错误的登录场景：loginAuthPasswordError
4. 模拟密码正确的使用场景：loginAuthPasswordCorrect

真正的代码要复杂的多，但意思总归是这么个意思。只要熟悉mockio的API，写出自己的测试代码并不难。


跑一下代码，全绿！！真是太好拉！

![](https://ws3.sinaimg.cn/large/006tKfTcgy1fsaidjmixej31fq0kyzp8.jpg)