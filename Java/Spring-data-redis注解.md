---
title: Spring-data-redis注解使用及注意
tags: [spring,redis] 
categories:
	- redis
    - spring
type : "tags"

---

#####  @CachePut

&#8195;&#8195;设置缓存，先执行方法，并将执行结果缓存起来，在方法上使用。

- value属性：指定Cache缓存的名称，value表示当前Key被缓存在那个Cahce上，可以配置一个一个Cache，也可以配置多个Cache，如：value = {Cache1, Cache2}

- Key属性：指定Redis的Key属性值，该值支持SpringEL表达式，当没有指定该属性时，Spring使用默认策略生成Key

- condition属性：指定缓存条件，可以通过condition属性过滤不希望加入redis缓存的数据，默认为空，表示缓存所有数据

  ```java 
  @CachePut(key="T(String).valueof(#user.uid)", condition = "#user.uid>1000")
  public User SaveUser(final User user){
    ....
  }
  ```

##### @CacheEvict

&#8195;&#8195;删除缓存，在执行方法前删除缓存，在方法上使用。

&#8195;&#8195;@CacheEvict属性与@CachePut属性一致。额外多两个属性

- allEntries属性：表示是否将缓存全部清空，boolean类型，默认false，表示不需要全部清楚，当为true时，表示清空value属性所指向的Cache中的所有缓存，这时Key属性已无意义
- beforeInvocation属性：表示是否在执行方法前执行缓存。

##### @Cacheable

&#8195;&#8195;查询缓存，首先检查注解中的Key是否存在缓存，如果存在，则返回value，不再执行方法，否则，执行方法并将方法结果缓存起来，所以`@Cacheable` 具备@CachePut的能力。

&#8195;&#8195;@Cacheable属性与@CachePut属性一致。



&#8195;&#8195;在使用Spring缓存注解前，需要在配置文件中启用Spring对基于注解的Cache的支持，在spring-redis.xml文件中，加上`<cache:annotation-driven/>`配置。`<cache:annotation-driven/>`有一个`cache-mamage`属性，用来指定所需要用到的缓存管理起(CacheManager)的Spring Bean的名称，如果不进行设置，默认是`CacheManager`,如果使用该属性，还需要配置一个类型为`CacheManager`的Spring Bean。使用Redis需要使用到`spring-data-redis`包中的`RedisCacheManager` 实现。

&#8195;&#8195;`<cache:annotation-driven/>`还可以指定一个`mode`属性，可选值有`proxy`和`aspectj`，默认使用proxy，当mode 为proxy时，只有当缓存注解的方法被对象外部的方法调用时（只有将@Cacheable等注解加在`public`类型方法上，SpringCache才会生效），SpringCache才会发生作用，反过来说，如果一个缓存方法，被其所在对象的内部方法调用时，SpringCache是不会发生作用的。当mode为aspectj时，只有注解被内部调用，缓存才会生效，非`public`类型的方法也可以使用。



&#8195;&#8195;`<cache:annotation-driven/>`还可以指定一个`proxy-target-class`属性，设置代理类的创建机制，为true时表示使用`CGLib`创建代理类，为false时表示使用JDK的动态代理机制创建代理类，默认false，*当属性值为true时，`@Cacheable`和`@Cacheinvalidate`等注解，必须标记在具体类上，不能标记在接口上，否则不会发生作用，当属性值为false时，`@Cacheable`和`@Cacheinvalidate`等注解可以标记在接口上，依然可以生效*。



在配置RedisCacheManager缓存管理器Bean时，需要配置两个构造参数：

- redisTemplate模版bean
- cacheNames缓存名称

