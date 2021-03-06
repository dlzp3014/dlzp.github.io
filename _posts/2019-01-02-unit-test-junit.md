---
layout: post
title:  "Junit使用"
date:   2019-01-02 00:57:00
categories: Unit-Test
tags: Junit
---


* content
{:toc}

Junit是一个由Java编写的开源的回归测试(指重复以前全部或者部分的相同测试)框架，用于编写和运行可重复的测试，它是用于单元测试框架提提xUnit的一个实例。所谓单元测试也就是一个白盒测试，该框架得到了绝大多数JavaIDE和其他工具(Maven)的集成支持，同时还有很多的第三方扩展和增强包可使用。

Junit在执行每个@Test方法之前，会为测试类创建一个新的是实例，这有助于提供测试方法之间的独立性，并且避免在测试代码中产生意外的副作用。因为每个测试方法都运行于一个新的测试类实例上，所以不能再测试方法之间重用各个实例的变量值

Junit没有main方法作为入口其运行的原理为：在org.junit.runner.JUnitCore.class(JUnitCore is a facade for running tests)类中，就一个标准的main方法，这就是JUnit的入口函数:

```java
public static void main(String... args) {
    Result result = new JUnitCore().runMain(new RealSystem(), args);
    System.exit(result.wasSuccessful() ? 0 : 1);
}
```

为了进行测试验证， JUnit提供了Assert类，里面包含了大量的assert方法，主要包括Equals,Same,True,False,NotNull,ArrayEquals

Junit中可以使用Suite一次运行多个测试用例，Suite是一个容器，用来把几个测试类归在一起，并把它们作为一个集合来运行，测试运行器会启动Suite，而运行哪些测试类由Suite决定

```java
@RunWith(Suite.class)
@Suite.SuiteClasses(Clazz.class)
```
[官网地址]：
[ JUnit 4 ](https://junit.org/junit4/) -- [javadoc](https://junit.org/junit4/javadoc/latest/index.html) 

[ JUnit 5 ](https://junit.org/junit5/) -- [JUnit 5 User Guide](https://junit.org/junit5/docs/current/user-guide/)







## Junit相关概念
- 测试: 一个以@Test注释的方法定义，为了运行这个方法，Junit会创建一个包含类的新实例，然后再调用这个被注释的方法
- 测试类: 一个测试类是@Test方法的容器
- Assert: 定义想测试的条件，当条件成立时，assert方法保持沉默，条件不成立时则抛出异常
- Suite: Suite允许将测试类归成一组
- Runner: Runner类用来运行测试，Junit4是向后兼容的，可以运行Junit3的测试

## 使用Junit时注意事项

- 测试方法上面必须使用@Test注解进行修饰。
 
- 测试方法必须使用public void 进行修饰，不能带有任何参数。
 
- 新建一个源代码目录用来存放测试代码。
 
- 测试类的包应该与被测试类的包保持一致。

- 测试单元中的每一个方法必须独立测试，每个测试方法之间不能有依赖。
 
- 测试类使用Test做为类名的后缀（非必要）。

- 测试方法使用test作为方法名的前缀（非必要）。

## 错误解析

- Failure:一般是单元测试使用的断言方法判断失败引起，说明预期结果和程序运行结果不一致。

- error:是有代码异常引起的，产生于测试代码本身中的Bug。

- 测试用例是不是用来证明你是对的，而是用来证明你没有错。


## 测试套件

@RunWith()：
- Suite.class:测试运行器Suite.class，@Suite.SuiteClasses：将需要运行的测试类放入Suite.SuiteClasses({})的数组中,以执行多个测试类
- Parameterized.class:测试数据,@Parameters

## Junit常用注解

### @BeforeClass
所修饰的方法在所有方法加载前执行，而且是静态的在类加载后就会执行该方法 在内存中只有一份实例，适合用来加载配置文件。

### @AfterClass
所修饰的方法在所有方法执行完毕之后执行，通常用来进行资源清理，例如关闭数据库连接。

### @Before和@After在每个测试方法执行前都会执行一次。

### @Test
- (excepted=XX.class) 异常测试。

```java
@Test(expected = RuntimeException.class,)
public void testException(){
    throw new RuntimeException("error msg");
}
```
当前测试仅能测试出方法按照预期抛出异常，如果代码里面不只一个地方抛出RuntimeException（只是包含的信息不一样），还是无法分辨具体的异常信息。这种情况可以使用JUnit的ExpectedException Rule来解决。
```java
 @Test
public void testExpectedExceptionRule(){
    //期待抛出RuntimeException
    expectedException.expect(RuntimeException.class);
    //期待抛出的异常信息中包含"Access Denied"字符串
    expectedException.expectMessage(CoreMatchers.containsString("Access Denied"));
    //当然也可以直接传入字符串，表示期待的异常信息（完全匹配）
    //expectedException.expectMessage("Access Denied!");
}
```
- (timeout=毫秒) 允许程序运行的时间。

### @Ignore 

所修饰的方法被测试器忽略。

### @RunWith 

可以修改测试运行器 org.junit.runner.Runner

### 执行顺序
- 一个测试类单元测试的执行顺序为:
@BeforeClass -> @Before -> @Test -> @After -> @AfterClass
- 每一个测试方法的调用顺序为
@Before –> @Test –> @After

## JUnit常用断言
    JUnit提供了一些辅助函数，用来确定被测试的方法是否按照预期的效果正常工作，通常，把这些辅助函数称为断言

- assertArrayEquals(expecteds,actuals):检查数组是否相等
- assertEquals(expecteds,actuals):检查对象是否相等
- assertNotEquals(expecteds,actuals):检查对象不相等
- assertNotNull(Object object):对象不为为null检查
- assertNull(Object object):对象为null检查
- assertTrue(boolean condition):true
- assertFalse(boolean condition):false 
- assertThat(T actual, Matcher<? super T> matcher):检查结果是否满足指定的条件


## Junit Demo

```java
public class JunitTest {

    /**
     * @BeforeClass修饰的方法会在所有方法被调用前被执行，该方法是静态的，
     * 所以当测试类被加载后接着就会运行它,而且在内存中它只会存在一份实例，它比较适合加载配置文件
     */
    @BeforeClass
    public static void initResource(){
        System.out.println("init resource...");
    }

    /**
     *  @AfterClass所修饰的方法通常用来对资源的清理，如关闭数据库的连接
     */
    @AfterClass
    public static void releaseResource(){
        System.out.println("release resource...");

    }

    @Before
    public void setUp(){
        System.out.println("Called before each test method");
    }

    /**
     * @Test注解方法中抛出了异常时，所有的@After注解方法依然会被执行
     */
    @After
    public void setDown(){
        System.out.println("Called after each test method");
    }

    @Test
    public void testMethod(){
        System.out.println("execute testMethod");
        List<String> list = Arrays.asList("a");
        Assert.assertTrue(list.get(0).equals("a"));
    }

    @Test
    public void testMethod02(){
        System.out.println("execute testMethod02");
        List<String> list = Arrays.asList("a");
        Assert.assertTrue(list.get(0).equals("a"));
    }
}

console:

init resource...
Called before each test method
execute testMethod02
Called after each test method
Called before each test method
execute testMethod
Called after each test method
release resource...

```