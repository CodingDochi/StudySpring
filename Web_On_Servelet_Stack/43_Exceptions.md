<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-exceptions.html">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p><code>@Controller</code> 및 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-advice.html">@ControllerAdvice</a> 클래스는 다음 예제와 같이 컨트롤러 메서드의 예외를 처리하기 위해 <code>@ExceptionHandler</code> 메서드를 가질 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Controller</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SimpleController</span> <span class="token punctuation">{</span>

	<span class="token comment">// ...</span>

	<span class="token annotation punctuation">@ExceptionHandler</span>
	<span class="token keyword">public</span> <span class="token class-name">ResponseEntity</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> <span class="token function">handle</span><span class="token punctuation">(</span><span class="token class-name">IOException</span> ex<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>예외는 전파되는 최상위 예외(예: 직접 <code>IOException</code> 발생) 또는 래퍼 예외 내의 중첩 원인(예: <code>IllegalStateException</code> 내에 래핑된 <code>IOException</code>)과 일치할 수 있습니다. 5.3부터는 임의의 원인 수준에서 일치할 수 있지만 이전에는 즉각적인 원인만 고려되었습니다.</p>
<p>일치하는 예외 유형의 경우 앞의 예제와 같이 대상 예외를 메서드 인수로 선언하는 것이 좋습니다. 여러 예외 메서드가 일치하는 경우 일반적으로 원인 예외 일치보다 루트 예외 일치가 선호됩니다. 보다 구체적으로 <code>ExceptionDepthComparator</code>는 발생한 예외 유형의 깊이를 기준으로 예외를 정렬하는 데 사용됩니다.</p>
<p>또는 다음 예제와 같이 annotation 선언을 통해 일치하도록 예외 유형을 좁힐 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@ExceptionHandler</span><span class="token punctuation">(</span><span class="token punctuation">{</span><span class="token class-name">FileSystemException</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> <span class="token class-name">RemoteException</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">}</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token class-name">ResponseEntity</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> <span class="token function">handle</span><span class="token punctuation">(</span><span class="token class-name">IOException</span> ex<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 예제와 같이 매우 일반적인 인수 서명과 함께 특정 예외 유형 목록을 사용할 수도 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@ExceptionHandler</span><span class="token punctuation">(</span><span class="token punctuation">{</span><span class="token class-name">FileSystemException</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> <span class="token class-name">RemoteException</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">}</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token class-name">ResponseEntity</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> <span class="token function">handle</span><span class="token punctuation">(</span><span class="token class-name">Exception</span> ex<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
근본 예외 일치와 원인 예외 일치 사이의 차이는 놀라울 수 있습니다.</p>
<p>앞서 표시된 <code>IOException</code> 변형에서 메서드는 일반적으로 실제 <code>FileSystemException</code> 또는 <code>RemoteException</code> 인스턴스를 인수로 사용하여 호출됩니다. 둘 다 <code>IOException</code>에서 확장되기 때문입니다. 그러나 일치하는 예외가 그 자체가 <code>IOException</code>인 래퍼 예외 내에서 전파되는 경우 전달된 예외 인스턴스는 해당 래퍼 예외입니다.</p>
<p><code>handle(Exception)</code> 변형에서는 동작이 훨씬 더 간단합니다. 이는 항상 래핑 시나리오에서 래퍼 예외와 함께 호출되며, 이 경우 실제로 일치하는 예외는 <code>ex.getCause()</code>를 통해 찾을 수 있습니다. 전달된 예외는 최상위 예외로 발생하는 경우에만 실제 <code>FileSystemException</code> 또는 <code>RemoteException</code> 인스턴스입니다.</p>
</blockquote>
<p>일반적으로 인수 서명을 최대한 구체적으로 지정하여 루트 예외 유형과 원인 예외 유형 간의 불일치 가능성을 줄이는 것이 좋습니다. 다중 일치 메서드를 개별 <code>@ExceptionHandler</code> 메서드로 분리하는 것을 고려하세요. 각 메서드는 서명을 통해 단일 특정 예외 유형과 일치합니다.</p>
<p>다중 <code>@ControllerAdvice</code> 배열에서는 해당 순서에 따라 우선순위가 지정된 <code>@ControllerAdvice</code>에 기본 루트 예외 매핑을 선언하는 것이 좋습니다. 원인보다 루트 예외 일치가 선호되는 반면 이는 지정된 컨트롤러 또는 <code>@ControllerAdvice</code> 클래스의 메서드 간에 정의됩니다. 이는 우선순위가 높은 <code>@ControllerAdvice</code> 빈의 원인 일치가 우선순위가 낮은 <code>@ControllerAdvice</code> 빈의 모든 일치(예: 루트)보다 선호됨을 의미합니다.</p>
<p>마지막으로 <code>@ExceptionHandler</code> 메소드 구현은 주어진 예외 인스턴스를 원래 형식으로 다시 발생시켜 처리를 취소하도록 선택할 수 있습니다. 이는 루트 수준 일치에만 관심이 있거나 정적으로 확인할 수 없는 특정 컨텍스트 내의 일치에만 관심이 있는 시나리오에서 유용합니다. 다시 던져진 예외는 주어진 <code>@ExceptionHandler</code> 메소드가 처음부터 일치하지 않은 것처럼 나머지 해결 체인을 통해 전파됩니다.</p>
<p>Spring MVC의 <code>@ExceptionHandler</code> 메소드에 대한 지원은 <code>DispatcherServlet</code> 레벨, <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/exceptionhandlers.html">HandlerExceptionResolver</a> 메커니즘을 기반으로 구축되었습니다.</p>
<h1 id="method-arguments">Method Arguments</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-exceptions.html#webflux-ann-exceptionhandler-args">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p><code>@ExceptionHandler</code> 메소드는 다음 인수를 지원합니다:</p>
<table class="tableblock frame-all grid-all stripes-odd stretch">




<thead>
<tr>
<th class="tableblock halign-left valign-top">매개변수</th>
<th class="tableblock halign-left valign-top">설명</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">예외 유형</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">발생한 예외에 대한 접근</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>HandlerMethod</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">예외를 발생시킨 컨트롤러 메서드에 대한 접근</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>WebRequest</code>, <code>NativeWebRequest</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">Servlet API를 직접 사용하지 않고 요청 매개변수 및 요청 및 세션 속성에 대한 일반적인 접근</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>jakarta.servlet.ServletRequest</code>, <code>jakarta.servlet.ServletResponse</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">특정 요청 또는 응답 유형 선택(예: <code>ServletRequest</code> 또는 <code>HttpServletRequest</code> 또는 Spring의 <code>MultipartRequest</code> 또는 <code>MultipartHttpServletRequest</code>)</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>jakarta.servlet.http.HttpSession</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">세션의 존재를 강제합니다. 따라서 이러한 매개변수는 절대 <code>null</code>이 아닙니다.<br>세션 액세스는 스레드 안전하지 않습니다. 여러 요청이 동시에 세션에 액세스할 수 있는 경우 <code>RequestMappingHandlerAdapter</code> 인스턴스의 <code>synchronizeOnSession</code> 플래그를 <code>true</code>로 설정하는 것이 좋습니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>java.security.Principal</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">현재 인증된 사용자 - 알려진 특정 <code>Principal</code> 구현 클래스일 수 있습니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>HttpMethod</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">요청의 HTTP 메서드</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>java.util.Locale</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">가장 구체적인 사용 가능한 <code>LocaleResolver</code>에 의해 결정된 현재 요청 로케일 - 즉, 구성된 <code>LocaleResolver</code> 또는 <code>LocaleContextResolver</code>의 효과.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>java.util.TimeZone</code>, <code>java.time.ZoneId</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">현재 요청과 관련된 시간대, <code>LocaleContextResolver</code>에 의해 결정됩니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>java.io.OutputStream</code>, <code>java.io.Writer</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">Servlet API에 의해 노출된 원시 응답 본문에 대한 액세스</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>java.util.Map</code>, <code>org.springframework.ui.Model</code>, <code>org.springframework.ui.ModelMap</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">오류 응답의 모델에 액세스하기 위해 사용됩니다. 항상 비어 있습니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>RedirectAttributes</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">리다이렉트 경우 사용할 속성 지정 - (쿼리 문자열에 추가될) 및 리다이렉트 후 요청 전까지 일시적으로 저장될 플래시 속성. <a href="ann-methods/redirecting-passing-data.html" class="xref page">Redirect Attributes</a> 및 <a href="ann-methods/flash-attributes.html" class="xref page">Flash Attributes</a> 참조</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>@SessionAttribute</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">클래스 수준의 <code>@SessionAttributes</code> 선언 결과로 세션에 저장된 모델 속성과 달리 모든 세션 속성에 액세스합니다. 자세한 내용은 <a href="ann-methods/sessionattribute.html" class="xref page"><code>@SessionAttribute</code></a>를 참조하세요.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>@RequestAttribute</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">요청 속성에 액세스합니다. 자세한 내용은 <a href="ann-methods/requestattrib.html" class="xref page"><code>@RequestAttribute</code></a>를 참조하세요.</p></td>
</tr>
</tbody>
</table>
<h1 id="return-values">Return Values</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-exceptions.html#webflux-ann-exceptionhandler-return-values">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p><code>@ExceptionHandler</code> 메서드는 다음 반환 값을 지원합니다.</p>
<table class="tableblock frame-all grid-all stripes-odd stretch">
  
    
    
  
  <thead>
    <tr>
      <th class="tableblock halign-left valign-top">리턴 값</th>
      <th class="tableblock halign-left valign-top">설명</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="tableblock halign-left valign-top"><p class="tableblock"><code>@ResponseBody</code></p></td>
      <td class="tableblock halign-left valign-top"><p class="tableblock">리턴 값은 <code>HttpMessageConverter</code> 인스턴스를 통해 변환되어 응답으로 작성됩니다. 자세한 내용은 <a href="ann-methods/responsebody.html" class="xref page"><code>@ResponseBody</code></a>를 참조하십시오.</p></td>
    </tr>
    <tr>
      <td class="tableblock halign-left valign-top"><p class="tableblock"><code>HttpEntity&lt;B&gt;</code>, <code>ResponseEntity&lt;B&gt;</code></p></td>
      <td class="tableblock halign-left valign-top"><p class="tableblock">리턴 값은 전체 응답(포함하여 HTTP 헤더 및 본문)이 <code>HttpMessageConverter</code> 인스턴스를 통해 변환되어 응답으로 작성되도록 지정됩니다. 자세한 내용은 <a href="ann-methods/responseentity.html" class="xref page">ResponseEntity</a>를 참조하십시오.</p></td>
    </tr>
    <tr>
      <td class="tableblock halign-left valign-top"><p class="tableblock"><code>ErrorResponse</code></p></td>
      <td class="tableblock halign-left valign-top"><p class="tableblock">바디에 세부 정보를 포함한 RFC 7807 오류 응답을 렌더링합니다. 자세한 내용은 <a href="../mvc-ann-rest-exceptions.html" class="xref page">오류 응답</a>을 참조하십시오.</p></td>
    </tr>
    <tr>
      <td class="tableblock halign-left valign-top"><p class="tableblock"><code>ProblemDetail</code></p></td>
      <td class="tableblock halign-left valign-top"><p class="tableblock">바디에 세부 정보를 포함한 RFC 7807 오류 응답을 렌더링합니다. 자세한 내용은 <a href="../mvc-ann-rest-exceptions.html" class="xref page">오류 응답</a>을 참조하십시오.</p></td>
    </tr>
    <tr>
      <td class="tableblock halign-left valign-top"><p class="tableblock"><code>String</code></p></td>
      <td class="tableblock halign-left valign-top"><p class="tableblock"><code>ViewResolver</code> 구현체를 사용하여 해결될 뷰 이름과 함께 암시적 모델과 함께 사용될 뷰 이름입니다 — 명령 객체 및 <code>@ModelAttribute</code> 메소드를 통해 결정됩니다. 핸들러 메소드는 <code>Model</code> 인자를 선언하여 모델을 프로그래밍 방식으로 보강할 수도 있습니다(이전에 설명된대로).</p></td>
    </tr>
    <tr>
      <td class="tableblock halign-left valign-top"><p class="tableblock"><code>View</code></p></td>
      <td class="tableblock halign-left valign-top"><p class="tableblock">암시적 모델과 함께 렌더링에 사용할 <code>View</code> 인스턴스입니다 — 명령 객체 및 <code>@ModelAttribute</code> 메소드를 통해 결정됩니다. 핸들러 메소드는 <code>Model</code> 인자를 선언하여 모델을 프로그래밍 방식으로 보강할 수도 있습니다(이전에 설명된대로).</p></td>
    </tr>
    <tr>
      <td class="tableblock halign-left valign-top"><p class="tableblock"><code>java.util.Map</code>, <code>org.springframework.ui.Model</code></p></td>
      <td class="tableblock halign-left valign-top"><p class="tableblock">암시적 모델에 추가할 속성으로, 뷰 이름은 <code>RequestToViewNameTranslator</code>를 통해 암시적으로 결정됩니다.</p></td>
    </tr>
    <tr>
      <td class="tableblock halign-left valign-top"><p class="tableblock"><code>@ModelAttribute</code></p></td>
      <td class="tableblock halign-left valign-top"><p class="tableblock">암시적으로 <code>RequestToViewNameTranslator</code>를 통해 결정된 뷰 이름과 함께 모델에 추가할 속성입니다.</p>
<p class="tableblock">  <code>@ModelAttribute</code>은 선택 사항입니다. 이 테이블의 끝 부분의 “다른 반환 값”을 참조하십시오.</p></td>
    </tr>
    <tr>
      <td class="tableblock halign-left valign-top"><p class="tableblock"><code>ModelAndView</code> 객체</p></td>
      <td class="tableblock halign-left valign-top"><p class="tableblock">사용할 뷰 및 모델 속성 및 선택적으로 응답 상태입니다.</p></td>
    </tr>
    <tr>
      <td class="tableblock halign-left valign-top"><p class="tableblock"><code>void</code></p></td>
      <td class="tableblock halign-left valign-top"><p class="tableblock"><code>void</code> 반환 유형(또는 <code>null</code> 반환 값)을 갖는 메소드는 <code>ServletResponse</code> 또는 <code>OutputStream</code> 인자 또
</p><p>는 <code>@ResponseStatus</code> 어노테이션이 있으면 완전히 응답을 처리한 것으로 간주됩니다. 컨트롤러가 긍정적인 <code>ETag</code> 또는 <code>lastModified</code> 타임스탬프 확인을 수행했으면 동일합니다(세부 사항은 <a href="../mvc-caching.html#mvc-caching-etag-lastmodified" class="xref page">컨트롤러</a>를 참조하십시오).</p><p></p>
<p class="tableblock">  위의 조건이 모두 참이 아니면 <code>void</code> 반환 유형은 REST 컨트롤러의 경우 “응답 본문 없음” 또는 HTML 컨트롤러의 경우 기본 뷰 이름 선택을 나타낼 수도 있습니다.</p></td>
    </tr>
    <tr>
      <td class="tableblock halign-left valign-top"><p class="tableblock">다른 반환 값</p></td>
      <td class="tableblock halign-left valign-top"><p class="tableblock">반환 값이 위의 어느 것과 일치하지 않고 단순한 유형이 아닌 경우(<a href="https://docs.spring.io/spring-framework/docs/6.1.5/javadoc-api/org/springframework/beans/BeanUtils.html#isSimpleProperty-java.lang.Class-">BeanUtils#isSimpleProperty</a>에 의해 결정됨), 기본적으로 모델 속성으로 처리됩니다. 단순한 유형인 경우 해결되지 않습니다.</p></td>
    </tr>
  </tbody>
</table>