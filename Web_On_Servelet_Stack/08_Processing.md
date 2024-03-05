<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/dispatcher-handler.html#webflux-dispatcher-handler-sequence">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p><code>DispatcherServlet</code>은 다음과 같이 요청을 처리합니다.</p>
<ul>
<li>
<p><code>WebApplicationContext</code>는 프로세스의 컨트롤러 및 기타 요소가 사용할 수 있는 속성으로 요청에서 검색되고 바인딩됩니다. 기본적으로 <code>DispatcherServlet.WEB_APPLICATION_CONTEXT_ATTRIBUTE</code> 키 아래에 바인딩됩니다.</p>
</li>
<li>
<p>로케일 resolver는 프로세스의 요소가 request을 처리할 때(뷰 렌더링, 데이터 준비 등) 사용할 로케일을 resolve할 수 있도록 요청에 바인딩됩니다. 로캘 resolving이 필요하지 않으면 로캘 resolver도 필요하지 않습니다.</p>
</li>
<li>
<p>테마 resolver는 뷰와 같은 요소가 사용할 테마를 결정할 수 있도록 요청에 바인딩됩니다. 테마를 사용하지 않는다면 무시해도 됩니다.</p>
</li>
<li>
<p>멀티파트 파일 resolver를 지정하면 요청에서 멀티파트가 검사됩니다. 멀티파트가 발견되면 프로세스의 다른 요소에 의한 추가 처리를 위해 요청이 <code>MultipartHttpServletRequest</code>로 래핑됩니다. 멀티파트 처리에 대한 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/multipart.html">Multipart Resolver</a>를 참조하세요.</p>
</li>
<li>
<p>적절한 핸들러가 검색됩니다. 핸들러가 발견되면 핸들러(전처리기, 후처리기 및 컨트롤러)와 연관된 실행 체인이 실행되어 렌더링할 모델을 준비합니다. 또는 어노테이션이 달린 컨트롤러의 경우 뷰를 반환하는 대신 response을 (<code>HandlerAdapter</code> 내에서) 렌더링할 수 있습니다.</p>
</li>
<li>
<p>모델이 반환되면 뷰가 렌더링됩니다. 모델이 반환되지 않으면(보안상의 이유로 전처리기나 후처리기가 요청을 가로채기 때문일 수 있음) request가 이미 이행되었을 수 있으므로 뷰가 렌더링되지 않습니다.</p>
</li>
</ul>
<p><code>WebApplicationContext</code>에 선언된 <code>HandlerExceptionResolver</code> 빈은 request 처리 중에 발생한 예외를 resolve하는 데 사용됩니다. 이러한 예외 resolver 프로그램을 사용하면 예외를 해결하기 위한 논리를 사용자 정의할 수 있습니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/exceptionhandlers.html">예외</a>를 참조하세요.</p>
<p>HTTP 캐싱 지원을 위해 핸들러는, <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-caching.html#mvc-caching-etag-lastmodified">컨트롤러에 대한 HTTP 캐싱</a>에 설명된 대로 어노테이션이 달린 컨트롤러에 대한 추가 옵션과 함께 <code>WebRequest</code>의 <code>checkNotModified</code> 메서드를 사용할 수 있습니다.</p>
<p><code>web.xml</code> 파일의 Servlet 선언에 Servlet 초기화 매개변수(<code>init-param</code> 요소)를 추가하여 개별 <code>DispatcherServlet</code> 인스턴스를 사용자 정의할 수 있습니다. 다음 표에는 지원되는 매개변수가 나열되어 있습니다.</p>
<table id="mvc-disp-servlet-init-params-tbl" class="tableblock frame-all grid-all stripes-odd stretch">
<caption class="title">표 1. DispatcherServlet 초기화 매개변수</caption>




<thead>
<tr>
<th class="tableblock halign-left valign-top">매개변수</th>
<th class="tableblock halign-left valign-top">설명</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>contextClass</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ConfigurableWebApplicationContext</code>를 구현한 클래스로, 이 서블릿에서 인스턴스화되고 로컬로 구성됩니다. 기본적으로 <code>XmlWebApplicationContext</code>가 사용됩니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>contextConfigLocation</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">컨텍스트 인스턴스에 전달되는 문자열( <code>contextClass</code>로 지정됨)로, 컨텍스트를 찾을 수 있는 위치를 나타냅니다. 문자열은 여러 개의 문자열로 구성될 수 있으며(쉼표를 구분자로 사용), 여러 컨텍스트를 지원합니다. 두 번 정의된 빈이 있는 여러 컨텍스트 위치의 경우, 최신 위치가 우선합니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>namespace</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>WebApplicationContext</code>의 네임스페이스입니다. 기본값은 <code>[servlet-name]-servlet</code>입니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>throwExceptionIfNoHandlerFound</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">요청에 대한 핸들러를 찾을 수 없을 때 <code>NoHandlerFoundException</code>을 throw할지 여부입니다. 그런 다음 예외를 <code>HandlerExceptionResolver</code>(예: <code>@ExceptionHandler</code> 컨트롤러 메서드 사용)를 사용하여 catch하고 다른 예외처럼 처리할 수 있습니다.</p>
<p class="tableblock">6.1 버전부터 이 속성은 <code>true</code>로 설정되어 있으며 사용이 중단되었습니다.</p>
<p class="tableblock">참고로, <a href="../mvc-config/default-servlet-handler.html" class="xref page">기본 서블릿 처리</a>가 구성되어 있는 경우에도 미해결 요청은 항상 기본 서블릿으로 전달되고 404가 발생하지 않습니다.</p></td>
</tr>
</tbody>
</table>