---
title: Spring IOC 和 DI
date: 2019-12-25 11:49:51
tags: 
- Java
---

## IOC - 一种编程思想

### 定义 （Inversion of Control)
  对象把对其他对象的控制权交给第三方容器（比如Spring），由第三方容器来统一管理对象的生命周期，提供所需的依赖。
  控制权：创建、销毁

### IOC VS DI
  * 思想 VS 实现方式
### 处理依赖的第三种方法
假定A依赖B，如何处理依赖关系：
方法1. 原始做法new B()
```java
class B {
  public void doSomeThing() {}
}

class A {
  public doSomeThing() {
    B b = new B();
    b.doSomeThing();
  }
}
```
方法2. 简单工厂等创造器模式
```java
interface IB {
  void doSomeThing();
}

class B1 implements IB {}
class B2 implements IB {}
class B3 implements IB {}

class BFactory {
  public static IB newB(int type) {
    if (type == 1) {
      return new B1();
    } else if (type == 2) {
      return new B2();
    } else {
      return new B3();
    }
  }
}

class A {
  public doSomeThing() {
    IB b = BFactory.newB(1);
    b.doSomeThing();
  }
}
```
方法3. 依赖注入
```java
class B {
  public void doSomeThing() {}
}

class A {
  @Autowired
  private B b;
  
  public doSomeThing() {
    b.doSomeThing();
  }
}
```

### Spring 两大核心思想之一
  * IOC
  * AOP

## DI - IOC的实现方式
Spring的DI机制降低了业务对象替换的复杂性，提高了组件之间的解耦。
### Spring 实现依赖注入的两种方式
* 属性 setter 方法注入
* 有参构造函数注入

## IOC 容器 - 对象的超级工厂
### IOC 容器主要作用
  * 对象的实例化、组装和管理 （instantiate、assembly、manage）
  * 为对象注入依赖 （Dependency Injection）
### Spring Core 就是一个IOC容器
在Spring体系结构中，Spring Core牢牢占据C位。
![Spring核心容器](/Users/shangdan/Desktop/images/20181018212051213.png)

Spring容器抽象视图
![Spring容器抽象视图](/Users/shangdan/Desktop/images/v2-e640041644f791b0ae800e6a1addc4ec_r.jpg)

## Spring如何实现IOC容器
### 对外接口
 * 定义和注册bean
 * 获取bean

### 核心概念
 * Bean  - Spring容器管理的Java对象。
 * BeanFactory   - 专门用来生成和获取Bean的接口。
 * ApplicationContext - Spring IOC容器的对外代表（接口）。
 * BeanDefination   - 存储Bean定义的接口。
 * BeanRegistry   - Bean的注册中心。

这里的BeanDefination接口就是xml配置文件中的<bean>标签在Spring中的表示形式。

### Spring提供的两种IOC容器
* BeanFactory  - org.springframework.beans.factory.BeanFactory
  管理Bean的超级工厂。
* ApplicationContext  - org.springframework.context.ApplicationContext
  通过继承和组合的方式对BeanFactory做的一层封装。除了具备BeanFactory的能力外，还要负责环境配置管理、生命周期管理、复杂的初始化操作。

#### BeanFactory和ApplicationContext的关系

![BeanFactory和ApplicationContext的关系](/Users/shangdan/Desktop/images/2331003-7940e4534a443c8d.png)
还又一个StaticListableBeanFactory。

[Spring官方文档](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#spring-core)的对两者关系的简短表述：

```
In short, the BeanFactory provides the configuration framework and basic functionality, and the ApplicationContext adds more enterprise-specific functionality. The ApplicationContext is a complete superset of the BeanFactory and is used exclusively in this chapter in descriptions of Spring’s IoC container
```

#### ApplicationContext的继承结构
![ApplicationContext继承结构](/Users/shangdan/Desktop/images/2331003-e7037d3486304ddd.png)

### Bean的定义
bean的属性列表
![Bean标签的属性列表](/Users/shangdan/Desktop/images/2331003-efe276be76daf9ee.png)
bean的三种定义方式
 * xml文件中的bean标签
 * 注解 + 扫描 ComponentScan
 * 定义Configuration类，在类中提供 @Bean 方法

#### xml形式的定义
```xml
<bean id="exampleBean" name="name1, name2, name3"  class="com.javadoop.ExampleBean"
      scope="singleton" lazy-init="true" init-method="init" destroy-method="cleanup">
    <!-- 可以用下面三种形式指定构造参数 -->
    <constructor-arg type="int" value="7500000"/>
    <constructor-arg name="years" value="7500000"/>
    <constructor-arg index="0" value="7500000"/>
    <!-- property 的几种情况 -->
    <property name="beanOne">
        <ref bean="anotherExampleBean"/>
    </property>
    <property name="beanTwo" ref="yetAnotherBean"/>
    <property name="integerProperty" value="1"/>
</bean>
```
#### 注解形式的定义 + 注解扫描
```java
@Controller
public class EmployeeController {
  @Autowired
  private EmployeeDao employeeDao;
  
  public String index() {
    return "Hello from Spring!";
  }
}

@SpringBootApplication
@ComponentScan(basePackages = "com.company.app")
public class MySpringBootApp {
  public static void main(String[] args) {
    ApplicationContext context = SpringApplication.run(MySpringBootApp.class, args);
  }
}
```
### bean定义、注册、获取的示例代码
```java
// 新建一个工厂
DefaultListableBeanFactory factory = new DefaultListableBeanFactory();

// 新建一个 bean definition
GenericBeanDefinition beanDefinition = (GenericBeanDefinition) BeanDefinitionBuilder
  .genericBeanDefinition(SomeService.class)
  .setAutowireMode(GenericBeanDefinition.AUTOWIRE_BY_TYPE)
  .getBeanDefinition();

// 注册到工厂
factory.registerBeanDefinition("someService", beanDefinition);

// 自己定义一个 bean post processor. 作用是在 bean 初始化之后, 判断这个 bean 如果实现了 ApplicationContextAware 接口, 就把 context 注册进去..(先不要管 context 哪来的...例子嘛)
factory.addBeanPostProcessor(new BeanPostProcessor() {
  @Override
  public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
    return bean;
  }

  @Override
  public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
    if (bean instanceof ApplicationContextAware) {
      GenericApplicationContext context = new GenericApplicationContext(factory);
      ((ApplicationContextAware) bean).setApplicationContext(context);
    }
    return bean;
  }
});

// 再注册一个 bean post processor: AutowiredAnnotationBeanPostProcessor. 作用是搜索这个 bean 中的 @Autowired 注解, 生成注入依赖的信息.
AutowiredAnnotationBeanPostProcessor autowiredAnnotationBeanPostProcessor = new AutowiredAnnotationBeanPostProcessor();
autowiredAnnotationBeanPostProcessor.setBeanFactory(factory);
factory.addBeanPostProcessor(autowiredAnnotationBeanPostProcessor);

// getBean() 时, 初始化
SomeService SomeService = factory.getBean("someService",SomeService.class);
SomeService.doSomething();

```

### Spring Core中模块之间的依赖关系图
![Spring核心容器模块之间的依赖关系](/Users/shangdan/Desktop/images/1540290875453691.png)


### 核心实现类
最简单的BeanFactory：`StaticListableBeanFactory`
```java
public class StaticListableBeanFactory implements ListableBeanFactory {
  private final Map<String, Object> beans;

  public StaticListableBeanFactory() {
    this.beans = new LinkedHashMap();
  }

  public StaticListableBeanFactory(Map<String, Object> beans) {
    Assert.notNull(beans, "Beans Map must not be null");
    this.beans = beans;
  }

  public void addBean(String name, Object bean) {
    this.beans.put(name, bean);
  }

  public Object getBean(String name) throws BeansException {
    String beanName = BeanFactoryUtils.transformedBeanName(name);
    Object bean = this.beans.get(beanName);
    if (bean == null) {
      throw new NoSuchBeanDefinitionException(beanName, "Defined beans are [" + StringUtils.collectionToCommaDelimitedString(this.beans.keySet()) + "]");
    } else if (BeanFactoryUtils.isFactoryDereference(name) && !(bean instanceof FactoryBean)) {
      throw new BeanIsNotAFactoryException(beanName, bean.getClass());
    } else if (bean instanceof FactoryBean && !BeanFactoryUtils.isFactoryDereference(name)) {
      try {
        Object exposedObject = ((FactoryBean)bean).getObject();
        if (exposedObject == null) {
          throw new BeanCreationException(beanName, "FactoryBean exposed null object");
        } else {
          return exposedObject;
        }
      } catch (Exception var5) {
        throw new BeanCreationException(beanName, "FactoryBean threw exception on object creation", var5);
      }
    } else {
      return bean;
    }
  }
  
  ...
}
```
单例Bean对象的注册表类：
```java
public class DefaultSingletonBeanRegistry extends SimpleAliasRegistry implements SingletonBeanRegistry {

	/** Cache of singleton objects: bean name to bean instance. */
	private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);

	/** Cache of singleton factories: bean name to ObjectFactory. */
	private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<>(16);

	/** Cache of early singleton objects: bean name to bean instance. */
	private final Map<String, Object> earlySingletonObjects = new HashMap<>(16);

	/** Set of registered singletons, containing the bean names in registration order. */
	private final Set<String> registeredSingletons = new LinkedHashSet<>(256);

	/** Names of beans that are currently in creation. */
	private final Set<String> singletonsCurrentlyInCreation =
			Collections.newSetFromMap(new ConcurrentHashMap<>(16));

	/** Names of beans currently excluded from in creation checks. */
	private final Set<String> inCreationCheckExclusions =
			Collections.newSetFromMap(new ConcurrentHashMap<>(16));

	/** List of suppressed Exceptions, available for associating related causes. */
	@Nullable
	private Set<Exception> suppressedExceptions;

	/** Flag that indicates whether we're currently within destroySingletons. */
	private boolean singletonsCurrentlyInDestruction = false;

	/** Disposable bean instances: bean name to disposable instance. */
	private final Map<String, Object> disposableBeans = new LinkedHashMap<>();

	/** Map between containing bean names: bean name to Set of bean names that the bean contains. */
	private final Map<String, Set<String>> containedBeanMap = new ConcurrentHashMap<>(16);

	/** Map between dependent bean names: bean name to Set of dependent bean names. */
	private final Map<String, Set<String>> dependentBeanMap = new ConcurrentHashMap<>(64);

	/** Map between depending bean names: bean name to Set of bean names for the bean's dependencies. */
	private final Map<String, Set<String>> dependenciesForBeanMap = new ConcurrentHashMap<>(64);
  
  ...
}
```

### Spring Bean实例的创建
```java
// org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory 
protected Object doCreateBean(final String beanName, final RootBeanDefinition mbd, final @Nullable Object[] args)
			throws BeanCreationException {

		// Instantiate the bean.
		...
		instanceWrapper = createBeanInstance(beanName, mbd, args); // line 555
		...
		// inject dependencies for bean properties 
		populateBean(beanName, mbd, instanceWrapper); // line 592
		exposedObject = initializeBean(beanName, exposedObject, mbd);
		...
		
		return exposedObject;
}
```
createBeanInstance方法如下：

```java
// org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory line 759
protected BeanWrapper createBeanInstance(String beanName, RootBeanDefinition mbd, @Nullable Object[] args) {
    Class<?> beanClass = this.resolveBeanClass(mbd, beanName, new Class[0]);
    if (beanClass != null && !Modifier.isPublic(beanClass.getModifiers()) && !mbd.isNonPublicAccessAllowed()) {
      throw new BeanCreationException(mbd.getResourceDescription(), beanName, "Bean class isn't public, and non-public access not allowed: " + beanClass.getName());
    } else {
      Supplier<?> instanceSupplier = mbd.getInstanceSupplier();
      if (instanceSupplier != null) {
        return this.obtainFromSupplier(instanceSupplier, beanName);
      } else if (mbd.getFactoryMethodName() != null) {
        return this.instantiateUsingFactoryMethod(beanName, mbd, args);
      } else {
        boolean resolved = false;
        boolean autowireNecessary = false;
        if (args == null) {
          synchronized(mbd.constructorArgumentLock) {
            if (mbd.resolvedConstructorOrFactoryMethod != null) {
              resolved = true;
              autowireNecessary = mbd.constructorArgumentsResolved;
            }
          }
        }

        if (resolved) {
          return autowireNecessary ? this.autowireConstructor(beanName, mbd, (Constructor[])null, (Object[])null) : this.instantiateBean(beanName, mbd);
        } else {
          Constructor<?>[] ctors = this.determineConstructorsFromBeanPostProcessors(beanClass, beanName);
          if (ctors == null && mbd.getResolvedAutowireMode() != 3 && !mbd.hasConstructorArgumentValues() && ObjectUtils.isEmpty(args)) {
            ctors = mbd.getPreferredConstructors();
            return ctors != null ? this.autowireConstructor(beanName, mbd, ctors, (Object[])null) : this.instantiateBean(beanName, mbd);
          } else {
            return this.autowireConstructor(beanName, mbd, ctors, args);
          }
        }
      }
    }
  }
```
经过多次弯弯绕之后，终于来到了最终的实例化方法：

```java
// org.springframework.beans.BeanUtils  line 49
public static <T> T instantiateClass(Constructor<T> ctor, Object... args) throws BeanInstantiationException {
    Assert.notNull(ctor, "Constructor must not be null");

    try {
      ReflectionUtils.makeAccessible(ctor);
      return KotlinDetector.isKotlinReflectPresent() && KotlinDetector.isKotlinType(ctor.getDeclaringClass()) ? BeanUtils.KotlinDelegate.instantiateClass(ctor, args) : ctor.newInstance(args);
    } catch (InstantiationException var3) {
      throw new BeanInstantiationException(ctor, "Is it an abstract class?", var3);
    } catch (IllegalAccessException var4) {
      throw new BeanInstantiationException(ctor, "Is the constructor accessible?", var4);
    } catch (IllegalArgumentException var5) {
      throw new BeanInstantiationException(ctor, "Illegal arguments for constructor", var5);
    } catch (InvocationTargetException var6) {
      throw new BeanInstantiationException(ctor, "Constructor threw exception", var6.getTargetException());
    }
  }
```
可见，本质上还是使用反射来获取构造函数，实现对象的实例化的。

### Spring Bean的销毁
```java
/**
	 * Actually performs context closing: publishes a ContextClosedEvent and
	 * destroys the singletons in the bean factory of this application context.
	 * <p>Called by both {@code close()} and a JVM shutdown hook, if any.
	 * @see org.springframework.context.event.ContextClosedEvent
	 * @see #destroyBeans()
	 * @see #close()
	 * @see #registerShutdownHook()
	 */
	protected void doClose() {
			// Publish shutdown event.
			publishEvent(new ContextClosedEvent(this));
			
			// Stop all Lifecycle beans, to avoid delays during individual destruction.
			this.lifecycleProcessor.onClose();
				
			// Destroy all cached singletons in the context's BeanFactory.
			destroyBeans();

			// Close the state of this context itself.
			closeBeanFactory();

			// Let subclasses do some final clean-up if they wish...
			onClose();
		}
	}
```
