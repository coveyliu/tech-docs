

# 对象



## 如何写 chain 

```
- HandlerExecutionChain 
	- chain 中应该包含什么组件？
	- 如何往 chain 上添加组件
	- 如何调用 chain 中的组件？
	- chain 上的组件是如何执行的？
		- 方式1：所有组件执行一遍
		- 方式2：沿着 chain 顺序执行，如果某个组件不能处理，立即终止
- 
```



- `HandlerExecutionChain`

```java
public class HandlerExecutionChain {

	private final Object handler;

	@Nullable
	private HandlerInterceptor[] interceptors;

	@Nullable
	private List<HandlerInterceptor> interceptorList;

	private int interceptorIndex = -1;


	// ============ 添加组件
    public HandlerExecutionChain(Object handler) {
		this(handler, (HandlerInterceptor[]) null);
	}
    
	public void addInterceptor(HandlerInterceptor interceptor) {
		initInterceptorList().add(interceptor);
	}
    
    public void addInterceptors(HandlerInterceptor... interceptors) {
		if (!ObjectUtils.isEmpty(interceptors)) {
			CollectionUtils.mergeArrayIntoCollection(interceptors, initInterceptorList());
		}
	}
    
    // ============ 应用组件
    boolean applyPreHandle(HttpServletRequest request, HttpServletResponse response) throws Exception {
		HandlerInterceptor[] interceptors = getInterceptors();
		if (!ObjectUtils.isEmpty(interceptors)) {
			for (int i = 0; i < interceptors.length; i++) {
				HandlerInterceptor interceptor = interceptors[i];

				// 如果某一个 HandlerInterceptor 不能应用了，直接 return
				if (!interceptor.preHandle(request, response, this.handler)) {
					triggerAfterCompletion(request, response, null);
					return false;
				}
				this.interceptorIndex = i;
			}
		}
		return true;
	}
    
    void applyPostHandle(HttpServletRequest request, HttpServletResponse response, @Nullable ModelAndView mv)
			throws Exception {

		HandlerInterceptor[] interceptors = getInterceptors();
		if (!ObjectUtils.isEmpty(interceptors)) {
			for (int i = interceptors.length - 1; i >= 0; i--) {
				HandlerInterceptor interceptor = interceptors[i];
                // 每一个 HandlerInterceptor d
				interceptor.postHandle(request, response, this.handler, mv);
			}
		}
	}
```





