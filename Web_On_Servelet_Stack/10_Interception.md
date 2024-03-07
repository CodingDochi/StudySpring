<p>모든 <code>HandlerMapping</code> 구현은 특정 요청에 특정 기능을 적용하려는 경우(예: 주체 확인) 유용한 핸들러 인터셉터를 지원합니다. 인터셉터는 모든 종류의 사전 처리 및 사후 처리를 수행할 수 있는 충분한 유연성을 제공해야 하는 세 가지 메서드를 사용하여 <code>org.springframework.web.servlet</code> 패키지에서 <code>HandlerInterceptor</code>를 구현해야 합니다.</p>
<ul>
<li>
<p><code>preHandle(..)</code>: 실제 핸들러가 실행되기 전</p>
</li>
<li>
<p><code>postHandle(..)</code>: 핸들러가 실행된 후</p>
</li>
<li>
<p><code>afterCompletion(..)</code>: 전체 요청이 완료된 후</p>
</li>
</ul>
<p><code>preHandle(..)</code> 메서드는 부울 값을 반환합니다. 이 방법을 사용하여 실행 체인 처리를 중단하거나 계속할 수 있습니다. 이 메서드가 <code>true</code>를 반환하면 핸들러 실행 체인이 계속됩니다. false를 반환하면 <code>DispatcherServlet</code>은 인터셉터 자체가 요청을 처리했다고 가정하고(예를 들어 적절한 뷰를 렌더링함) 실행 체인에서 다른 인터셉터와 실제 핸들러를 계속 실행하지 않습니다.</p>
<p>인터셉터를 구성하는 방법에 대한 예는 MVC 구성 섹션의 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-config/interceptors.html">인터셉터</a>를 참조하세요. 개별 <code>HandlerMapping</code> 구현에서 setter를 사용하여 직접 등록할 수도 있습니다.</p>
<p><code>postHandle</code> 메서드는 응답이 <code>HandlerAdapter</code> 내에서 <code>postHandle</code> 이전에 작성되고 커밋되는 <code>@ResponseBody</code> 및 <code>ResponseEntity</code> 메서드에서는 덜 유용합니다. 즉, 헤더를 추가하는 등 응답을 변경하기에는 너무 늦었습니다. 이러한 시나리오의 경우 <code>ResponseBodyAdvice</code>를 구현하고 이를 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-advice.html">Controller Advice</a> Bean으로 선언하거나 <code>RequestMappingHandlerAdapter</code>에서 직접 구성할 수 있습니다.</p>