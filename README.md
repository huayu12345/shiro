shiro:权限管理包括用户身份认证和授权两部分
    java领域中spring security(原名Acegi)也是一个开源的权限管理框架，但是spring security依赖spring运行，而shiro就相对独立，最主要是因为shiro使用简单、灵活，所以现在越来越多的用户选择shiro。
    
    *Subject：可以理解为与shiro打交道的对象，该对象封装了一些对方的信息，shiro可以通过subject拿到这些信息
    *SecurityManager：Shiro的总经理，通过指使Authorizer和Authenticator等对subject进行授权和身份验证等工作
    *Realm：管理着一些如用户、角色、权限等重要信息，Shiro中所需的这些重要信息都是从Realm这里获取的，Realm本质上就是一个重要信息的数据源
    Authenticator：认证器，负责Subject的认证操作，认证过程就是根据Subject提供的信息通过Realm查询到相关信息，然后做对比，支持扩展
    Authorizer：授权器，控制着Subject对服务资源的访问权限
    SessionManager：用于管理Session，这个Session可以是web的也可以不是web的。
    SessionDao：把Session的 CRUD和存储介质联系起来的工具，存储介质可以是数据库，也可以是缓存，比如把session放到redis里面
    CacheManager：缓存控制器，Realm管理的数据（用户、角色、权限）可以放到缓存里由CacheManager管理，提高认证授权等的速度
    Cryptography：加密组件，Shiro提供了很多加解密算法的组件
    我们需要操作哪些组件呢？
    作为一个成熟的跟Apache混的组件，用起来肯定越简单越符合用户习惯。我们使用的时候只需要把用户、角色、权限信息存储好（一般放到数据库里）并提供接口可以把这些信息注入到Shiro中就可以了。然后再对Shiro进行一些简单的配置即可！
    

RBAC是基于角色的访问控制（Role-Based Access Control ）在 RBAC 中，权限与角色相关联，用户通过成为适当角色的成员而得到这些角色的权限。这就极大地简化了权限的管理。这样管理都是层级相互依赖的，权限赋予给角色，而把角色又赋予用户，这样的权限设计很清楚，管理起来很方便。
RBAC认为授权实际上是Who 、What 、How 三元组之间的关系，也就是Who 对What 进行How 的操作，也就是“主体”对“客体”的操作。

Who：是权限的拥有者或主体（如：User，Role）。
What：是操作或对象（operation，object）。
How：具体的权限（Privilege,正向授权与负向授权）。

Shiro 是如何工作的
简单来说，在Spring项目中

Shiro会将他的所有组件注册到SecurityManager中。
再通过SecurityManager注册到ShiroFilterFactoryBean（这个类实现了Spring的BeanPostProcessor会预先加载）中。
最后以filter的形式注册到Spring容器（实现了Spring的FactoryBean，构造一个filter注册到Spring容器中），实现用户权限的管理。

springboot如何整合shiro:
1、所需依赖：
	<!-- SpringBoot中使用 Shiro 做用户、角色、权限管理 -->
    <dependency>
        <groupId>org.apache.shiro</groupId>
        <artifactId>shiro-core</artifactId>
        <version>1.4.0</version>
    </dependency>
    <dependency>
        <groupId>org.apache.shiro</groupId>
        <artifactId>shiro-spring</artifactId>
        <version>1.4.0</version>
    </dependency>
2、shiroConfig（Spring项目集成Shiro的通用配置）
（1）ShiroFilterFactoryBean：用于定义请求的拦截规则，Shiro为我们默认提供了一些项，常用如下
    anon : 请求不拦截
    authc：要求用户必须认证通过
    user：要求用户为记住我状态
    roles[xxx]：要求用户必须满足xxx角色
    perms[xxx]：要求用户必须满足xxx权限
（2）Realm（用于连接Shiro和客户系统的用户数据的桥梁）
    通过实现AuthorizingRealm来提供用户认证和授权两个API
    doGetAuthenticationInfo：认证方法，在执行subject.login(token)；后，Shiro认证器会读取Realm中的该方法获取AuthenticationInfo对象（认证信息），包含principal（我们存储在shiro subject中的对象），credentials（密码）。
    doGetAuthorizationInfo：授权方法，在需要校验用户访问权限的时候，Shiro授权器会读取Realm中的该方法获取AuthorizationInfo对象（授权信息）读取DB后，可以通过addRoles（roleCollection）和addStringPermissions（permCollection）设置当前用户的角色和权限。Shiro在拿到这个权限信息后，会去找缓存管理器。以当前subject的principal作为key缓存起来。
（3）CredentialsMatcher：密码匹配器，用于匹配doGetAuthenticationInfo方法返回的credentials和subject.login（token），时的token中的password是否一致。常用的实现有SimpleCredentialsMatcher（默认是该实现）、HashedCredentialsMatcher（该实现可以进行加密匹配）
（4） DefaultWebSecurityManger：如上述，用于协调Shiro内部各种安全组件，我们需要将我们扩展的bean注册到SecurityManager中
（5）RememberMeManager：开启该组件后使用记住服务，token中rememberMe为true时，登录成功之后会创建RememberMe cookie。
    
