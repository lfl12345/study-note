# Spring Boot拦截器（Interceptor）

## Interceptor介绍

拦截器（Interceptor）和Servlet的Filter过滤器一样，他俩都是面向切面编程——AOP的具体实现

可以使用Interceptor来执行某些任务，例如在Controller处理请求之前编写日志，添加或更新配置。

在spring中，当请求发送到Controller时，在被Controller处理之前，他必须经过一个或者多个Interceptors。

## Interceptor作用

拦截器本质上是面向切面编程（AOP），符合横切关注点的功能都可以放在拦截器中来实现，主要的应用场景包括：

1. 日志记录：记录请求信息的日志，以便于进行信息监控、信息统计、计算PV（Page View）等；
2. 权限检查：如登录检测，进入处理器检测是否登录；
3. 性能监控：通过拦截器在进入处理器之前记录时间，在处理完后记录结束时间从而得到该请求的处理时间。（反向代理，如Apache也可以自动记录）
4. 通用行为：读取Cookie得到用户信息并将用户对象放入请求，从而方便后续流程使用，还有如提取Locale、Theme信息等，只要是多个处理器都需要的即可使用拦截器实现。

## Interceptor和Filter的区别

1. Filter只能在容器初始化时被调用一次，在action的生命周期中，而拦截器可以多次被调用。
2. 过滤器可以对几乎所有的请求起作用，拦截器只能对action请求起作用。
3. 过滤器不能访问action上下文、堆栈里面的对象，而拦截器可以访问
4. 过滤器依赖于servlet容器，而拦截器不依赖于servlet容器
5. 过滤器是基于函数回调，而拦截器是基于Java反射机制的。
6. 过滤器不能获取IOC容器中的各个bean，而拦截器可以，这点很重要，在拦截器里面注入一个service，可以调用业务逻辑。

拦截器是在DispatcherServlet这个Servlet中执行的，因此所有的请求最先进入Filter，最后离开Filter，其执行顺序如下：

Filter->Interceptor.preHandler->Handler->Interceptor.posHandler->Interceptor.afterCompletion->Filter

## 自定义Interceptor

如果需要自定义Interceptor的话必须实现：

1. org.springframework.web.servlet.HandlerInterceptor接口或者继承
2. org.springframework.web.servlet.HandlerInterceptorAdapter类，但是这个类已经被废弃了，因为HandlerInterceptor接口把它里面的方法都变成了默认的方法，所以不用适配器去实现里面的方法。

继承或实现上面的类之后，需要重写下面三个方法：

1. p reHandler(HttpServletRequest request, HttpServletResponse response, Object handler)方法在请求处理之前被调用。该方法在Interceptor类中最先执行，用来进行一些前置初始化操作或者是对当前请求做预处理，可以以进行一些判断来请求是否要继续进行下去。该方法的返回值是Boolean类型，当它返回false时，表示请求结束，后续的Interceptor和Controller都不会再执行；当它返回true时会继续调用下一个Interceptor的perHandle方法，如果已经是最后一个Interceptor的时候就会调用当情请求的Controller方法。
2. postHandler(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView)方法在当前请求处理完成之后，也就是Controller方法调用之后执行，但是它会在DispatcherServlet进行视图返回渲染之前被调用，所以我们可以在这个方法中对controller处理之后的ModelAnd View对象进行操作。
3. afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handle, Exception ex)方法需要在当前对应的Interceptor类的preHandler方法返回值为true时才会执行。顾名思义，该方法将在整个请求结束之后，也就是在Dispatcher Servlet渲染了对应的视图之后执行。此方法主要用来进行资源清理。

## 多个拦截器的顺序

* 多个拦截器的执行顺序和注册的顺序有关，先注册的先执行
* 可以通过Order方法来设置执行顺序，值越小越先执行。