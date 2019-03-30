-   [Database](#database)
-   [Framework](#framework)
    -   [Spring](#spring)

Database
--------

redirected to [db-and-sys](../db-and-sys)

Framework
---------

### Spring

-   AOP: Aspect Oriented Programming
    -   decouple peripheral service from controller
    -   implementation
        -   proxy: `ControllerProxy::{ service(); trueController(); }`
            -   dynamic proxy (Spring)
                -   handler interface
                    -   `Enhancer: Interface`
                    -   `Handler::invoke(Method)::{ this.method(); }`
                -   call parent impl by reflection
                    -   `Enhancer: Impl`
                    -   `Enhancer::intercept(Object, Method)::{ service(); reflect.superMethod(); }`
-   IOC: Inverse of Control
    -   inverse control principle
        -   before: request flow from high level to low level
        -   now: low-level is built directly from request
    -   dependency injection
        -   method to construct high-level from low-level object
        -   method
            -   `Higher(Lower)`: ctor injection
            -   `setLower(Lower)`: setter injection
            -   `setLower(Lower)` and `ConcreteLower: Lower`: interface
                injection
    -   IoC Container: factory from dependency
    -   Other Feature
        -   loosely coupled
        -   recursive dependency
    -   Initialization: XML -\> Resource -\> BeanDefinition -\>
        BeanFactory
    -   Bean
        -   type `@Scope("type")`
            -   singleton (default)
                -   lazy-init
            -   prototype (`new Bean()`)
                -   Spring not responsible for its destruction
            -   request (new at HTTP request)
            -   session (share in HTTP Session)
            -   globalSession
        -   lifetime
            -   create Bean
            -   property setting
                -   XXAware interface
            -   (a) BeanPostProcessor::postProcessBeforeInitialization

            -   @PostConstruct / InitializingBean::afterPropertySet /
                XML::init-method
            -   (a) BeanPostProcessor::postProcessAfterInitialization

            -   @PreDestroy / DisposableBean::destroy /
                XML::destroy-method
-   SpringMVC
    -   Procedure
        -   Controller: request -\> DispatcherServlet use HandlerMapping
            -\> Handler (controller)
        -   Execution: handler -\> HandlerAdapter -\> execute -\>
            produce ModelAndView (data and logical view)
        -   View: view -\> ViewResolver -\> lookup View
        -   Model: model -\> DispatcherServlet -\> View -\> client
    -   Annotation
    -   Interceptor (拦截器)
        -   `HandlerInterceptor`
            -   preHandle -\> boolean
            -   postHandle: after Handler execution
            -   afterCompletion: after all
