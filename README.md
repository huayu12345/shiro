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
    
