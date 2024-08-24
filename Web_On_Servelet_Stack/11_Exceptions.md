<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/dispatcher-handler.html#webflux-dispatcher-exceptions">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>요청 매핑 중에 예외가 발생하거나 요청 핸들러(예: <code>@Controller</code>)에서 발생하는 경우 <code>DispatcherServlet</code>은 예외를 resolve하고 대체 처리(일반적으로 오류 응답)를 제공하기 위해 <code>HandlerExceptionResolver</code> 빈 체인에 위임합니다.</p>
<p>다음 표에는 사용 가능한 <code>HandlerExceptionResolver</code> 구현이 나열되어 있습니다.</p>
<table class="tableblock frame-all grid-all stripes-odd stretch">
<caption class="title">표 1. HandlerExceptionResolver 구현체</caption>




<thead>
<tr>
<th class="tableblock halign-left valign-top"><code>HandlerExceptionResolver</code></th>
<th class="tableblock halign-left valign-top">설명</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>SimpleMappingExceptionResolver</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">예외 클래스 이름과 오류 뷰 이름 간의 매핑. 브라우저 애플리케이션에서 오류 페이지를 렌더링하는 데 유용합니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="https://docs.spring.io/spring-framework/docs/6.1.5/javadoc-api/org/springframework/web/servlet/mvc/support/DefaultHandlerExceptionResolver.html"><code>DefaultHandlerExceptionResolver</code></a></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">Spring MVC에서 발생한 예외를 resolve하고 HTTP 상태 코드에 매핑합니다. 대안으로 <code>ResponseEntityExceptionHandler</code> 및 <a href="../mvc-ann-rest-exceptions.html" class="xref page">에러 응답</a>도 참고하십시오.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ResponseStatusExceptionResolver</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>@ResponseStatus</code> 주석을 사용하여 발생한 예외를 resolve하고 주석의 값에 따라 HTTP 상태 코드에 매핑합니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ExceptionHandlerExceptionResolver</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>@Controller</code> 또는 <code>@ControllerAdvice</code> 클래스의 <code>@ExceptionHandler</code> 메서드를 호출하여 예외를 resolve합니다. <a href="../mvc-controller/ann-exceptionhandler.html" class="xref page">@ExceptionHandler 메서드</a>를 참조하십시오.</p></td>
</tr>
</tbody>
</table>
<h1 id="chain-of-resolvers">Chain of Resolvers</h1>
<p>Spring 구성에서 여러 <code>HandlerExceptionResolver</code> 빈을 선언하고 필요에 따라 <code>order</code> 속성을 설정하여 예외 해결 프로그램 체인을 형성할 수 있습니다. order 속성이 높을수록 예외 resolver 프로그램이 더 늦게 배치됩니다.</p>
<p><code>HandlerExceptionResolver</code>의 계약은 다음을 반환할 수 있다고 지정합니다.</p>
<ul>
<li>
<p>error view를 가리키는 <code>ModelAndView</code></p>
</li>
<li>
<p>resolver 내에서 예외가 처리된 경우 빈(empty) <code>ModelAndView</code>입니다.</p>
</li>
<li>
<p>예외가 resolve되지 않은 상태로 남아 있으면 <code>null</code>이며, 후속 rewolver가 시도하고, 예외가 끝에 남아 있으면 서블릿 컨테이너까지 bubble up될 수 있습니다.</p>
</li>
</ul>
<hr>
<p><strong>bubble up</strong><br>
"bubble up"은 일반적으로 어떤 것이 계층 구조나 다른 형태의 구조 안에서 상위로 올라가거나 전파되는 것을 의미합니다. 이 맥락에서는 예외가 하위 수준의 처리기에서 해결되지 않으면, 그 예외가 상위 수준으로 전파되어 더 높은 수준의 처리기나 최종적으로는 Servlet 컨테이너로 전달되는 것을 의미합니다.</p>
<hr>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-config.html">MVC 구성</a>은 기본 Spring MVC 예외, <code>@ResponseStatus</code> 어노테이션 예외 및 <code>@ExceptionHandler</code> 메서드 지원을 위한 내장 resolver를 자동으로 선언합니다. 해당 목록을 사용자 정의하거나 바꿀 수 있습니다.</p>
<h1 id="container-error-page">Container Error Page</h1>
<p>예외가 <code>HandlerExceptionResolver</code>에 의해 resolve되지 않은 상태로 남아 전파되도록 남겨지거나 응답 상태가 오류 상태(즉, 4xx, 5xx)로 설정된 경우 서블릿 컨테이너는 HTML에서 기본 오류 페이지를 렌더링할 수 있습니다. 컨테이너의 기본 오류 페이지를 사용자 정의하려면 <code>web.xml</code>에서 오류 페이지 매핑을 선언하면 됩니다. 다음 예에서는 그 방법을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>error-page</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>location</span><span class="token punctuation">&gt;</span></span>/error<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>location</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>error-page</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>앞의 예에서 예외가 발생하거나 응답에 오류 상태가 있으면 서블릿 컨테이너는 컨테이너 내에서 구성된 URL(예: <code>/error</code>)에 ERROR 디스패치를 만듭니다. 그런 다음 이는 <code>DispatcherServlet</code>에 의해 처리되어 <code>@Controller</code>에 매핑될 수 있습니다. 이는 다음 예제와 같이 모델과 함께 오류 뷰 이름을 반환하거나 JSON 응답을 렌더링하도록 구현될 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@RestController</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ErrorController</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span>path <span class="token operator">=</span> <span class="token string">"/error"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">Map</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">Object</span><span class="token punctuation">&gt;</span></span> <span class="token function">handle</span><span class="token punctuation">(</span><span class="token class-name">HttpServletRequest</span> request<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">Map</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">Object</span><span class="token punctuation">&gt;</span></span> map <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">HashMap</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		map<span class="token punctuation">.</span><span class="token function">put</span><span class="token punctuation">(</span><span class="token string">"status"</span><span class="token punctuation">,</span> request<span class="token punctuation">.</span><span class="token function">getAttribute</span><span class="token punctuation">(</span><span class="token string">"jakarta.servlet.error.status_code"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		map<span class="token punctuation">.</span><span class="token function">put</span><span class="token punctuation">(</span><span class="token string">"reason"</span><span class="token punctuation">,</span> request<span class="token punctuation">.</span><span class="token function">getAttribute</span><span class="token punctuation">(</span><span class="token string">"jakarta.servlet.error.message"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">return</span> map<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>Tip</strong><br>
Servlet API는 Java에서 오류 페이지 매핑을 생성하는 방법을 제공하지 않습니다. 그러나 <code>WebApplicationInitializer</code>와 최소 <code>web.xml</code>을 모두 사용할 수 있습니다.</p>
</blockquote>