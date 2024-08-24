<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/reactive-spring.html#webflux-filters">반응형 스택에서 이에 상응하는 내용 보기</a> </p>
<p><code>spring-web</code> 모듈은 몇 가지 유용한 필터를 제공합니다:</p>
<ul>
<li>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webmvc/filters.html#filters-http-put">Form Data</a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webmvc/filters.html#filters-forwarded-headers">Forwarded Headers</a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webmvc/filters.html#filters-shallow-etag">Shallow ETag</a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webmvc/filters.html#filters-cors">CORS</a></p>
</li>
</ul>
<h1 id="form-data">Form Data</h1>
<p>브라우저는 HTTP GET 또는 HTTP POST를 통해서만 양식 데이터를 제출할 수 있지만 브라우저가 아닌 클라이언트는 HTTP PUT, PATCH 및 DELETE를 사용할 수도 있습니다. Servlet API에는 HTTP POST에 대해서만 양식 필드 액세스를 지원하기 위한 <code>ServletRequest.getParameter*()</code> 메소드가 필요합니다.</p>
<p><code>spring-web</code> 모듈은 <code>FormContentFilter</code>를 제공하여 <code>application/x-www-form-urlencoded</code> 콘텐츠 유형으로 HTTP PUT, PATCH 및 DELETE 요청을 가로채고, 요청 본문에서 양식 데이터를 읽고, <code>ServletRequest</code>를 래핑하여 <code>ServletRequest.getParameter*()</code> 메소드 계열을 통해 사용할 수 있는 양식 데이터입니다.</p>
<h1 id="forwarded-headers">Forwarded Headers</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/reactive-spring.html#webflux-forwarded-headers">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>요청이 로드 밸런서와 같은 프록시를 통과함에 따라 호스트, 포트 및 체계가 변경될 수 있으며 이로 인해 클라이언트 관점에서 올바른 호스트, 포트 및 체계를 가리키는 링크를 생성하는 것이 어려워집니다.</p>
<p><a href="https://datatracker.ietf.org/doc/html/rfc7239">RFC 7239</a>는 프록시가 원래 요청에 대한 정보를 제공하는 데 사용할 수 있는 <code>Forwarded</code> HTTP 헤더를 정의합니다.</p>
<h2 id="non-standard-headers">Non-standard Headers</h2>
<p><code>X-Forwarded-Host</code>, <code>X-Forwarded-Port</code>, <code>X-Forwarded-Proto</code>, <code>X-Forwarded-Ssl</code> 및 <code>X-Forwarded-Prefix</code>를 포함한 다른 비표준 헤더도 있습니다.</p>
<h3 id="x-forwarded-host">X-Forwarded-Host</h3>
<p>표준은 아니지만 <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Forwarded-Host"><code>X-Forwarded-Host: &lt;host&gt;</code></a>는 원래 호스트를 다운스트림 서버와 통신하는 데 사용되는 사실상의 표준 헤더입니다. 예를 들어, <a href="https://example.com/resource"><code>example.com/resource</code></a> 요청이 요청을 <a href="http://localhost:8080/resource"><code>localhost:8080/resource</code></a>로 전달하는 프록시로 전송되면 <code>X-Forwarded-Host: example.com</code> 헤더가 서버에 전송될 수 있습니다. 원래 호스트는 <code>example.com</code>이었습니다.</p>
<h3 id="x-forwarded-port">X-Forwarded-Port</h3>
<p>표준은 아니지만 <code>X-Forwarded-Port: &lt;port&gt;</code>는 원래 포트를 다운스트림 서버에 전달하는 데 사용되는 사실상의 표준 헤더입니다. 예를 들어, <a href="https://example.com/resource"><code>example.com/resource</code></a> 요청이 요청을 <a href="http://localhost:8080/resource"><code>localhost:8080/resource</code></a>로 전달하는 프록시로 전송되면 <code>X-Forwarded-Port: 443</code> 헤더가 전송되어 원래 <code>443</code> 포트가 서버에 있음을 알릴 수 있습니다.</p>
<h3 id="x-forwarded-proto">X-Forwarded-Proto</h3>
<p>표준은 아니지만 <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Forwarded-Proto"><code>X-Forwarded-Proto: (https|http)</code></a>는 원래 프로토콜(예: https / https)을 다운스트림 서버에 전달하는 데 사용되는 사실상의 표준 헤더입니다. 예를 들어, <a href="https://example.com/resource"><code>example.com/resource</code></a>의 요청이 요청을 <a href="http://localhost:8080/resource"><code>localhost:8080/resource</code></a>로 전달하는 프록시로 전송되면 <code>X-Forwarded-Proto: https</code> 헤더가 전송되어 원래 <code>https</code> 프로토콜이 서버에 있음을 알릴 수 있습니다. </p>
<h3 id="x-forwarded-ssl">X-Forwarded-Ssl</h3>
<p>표준은 아니지만 <code>X-Forwarded-Ssl: (on|off)</code>는 원래 프로토콜(예: https / https)을 다운스트림 서버에 전달하는 데 사용되는 사실상의 표준 헤더입니다. 예를 들어, <a href="https://example.com/resource"><code>example.com/resource</code></a> 요청이 요청을 <a href="http://localhost:8080/resource"><code>localhost:8080/resource</code></a>로 전달하는 프록시로 전송되면 <code>X-Forwarded-Ssl: on</code> 헤더가 원래 프로토콜이 <code>https</code>였음을 서버에 알립니다.</p>
<h3 id="x-forwarded-prefix">X-Forwarded-Prefix</h3>
<p>표준은 아니지만 <a href="https://microsoft.github.io/reverse-proxy/articles/transforms.html#defaults"><code>X-Forwarded-Prefix: &lt;prefix&gt;</code></a>는 원래 URL 경로 접두어를 다운스트림 서버에 전달하는 데 사용되는 사실상의 표준 헤더입니다.</p>
<p><code>X-Forwarded-Prefix</code>의 사용은 배포 시나리오에 따라 다를 수 있으며 대상 서버의 경로 접두사를 대체, 제거 또는 앞에 추가할 수 있도록 유연해야 합니다.</p>
<p><em>Scenario 1: Override path prefix</em></p>
<pre><code class="language-null">https://example.com/api/{path} -&gt; http://localhost:8080/app1/{path}</code></pre>
<p>접두사는 캡처 그룹 <code>{path}</code> 앞의 경로 시작입니다. 프록시의 경우 접두사는 <code>/api</code>이고 서버의 경우 접두사는 <code>/app1</code>입니다. 이 경우 프록시는 <code>X-Forwarded-Prefix: /api</code>를 보내 원래 접두사 <code>/api</code>가 서버 접두사 <code>/app1</code>을 재정의하도록 할 수 있습니다.</p>
<p><em>Scenario 2: Remove path prefix</em><br>
때때로 응용 프로그램에서 접두사를 제거하려고 할 수도 있습니다. 예를 들어 다음 프록시-서버 매핑을 고려해보세요.</p>
<pre><code class="language-null">https://app1.example.com/{path} -&gt; http://localhost:8080/app1/{path}
https://app2.example.com/{path} -&gt; http://localhost:8080/app2/{path}</code></pre>
<p>프록시에는 접두사가 없지만 <code>app1</code> 및 <code>app2</code> 애플리케이션에는 각각 경로 접두사 <code>/app1</code> 및 <code>/app2</code>가 있습니다. 프록시는 <code>X-Forwarded-Prefix:</code>를 전송하여 빈 접두사가 서버 접두사 <code>/app1</code> 및 <code>/app2</code>를 재정의하도록 할 수 있습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
이 배포 시나리오의 일반적인 경우는 프로덕션 응용 프로그램 서버별로 라이선스를 지불하는 것이며, 비용을 줄이기 위해 서버당 여러 응용 프로그램을 배포하는 것이 좋습니다. 또 다른 이유는 서버 실행에 필요한 리소스를 공유하기 위해 동일한 서버에서 더 많은 응용 프로그램을 실행하는 것입니다.</p>
<p>이러한 시나리오에서는 동일한 서버에 여러 애플리케이션이 있으므로 애플리케이션에는 비어 있지 않은 컨텍스트 루트가 필요합니다. 그러나 이는 애플리케이션이 다음과 같은 이점을 제공하는 다른 하위 도메인을 사용할 수 있는 공개 API의 URL 경로에 표시되어서는 안 됩니다.</p>
<ul>
<li>
<p>보안이 추가되었습니다. 동일 원산지 정책</p>
</li>
<li>
<p>애플리케이션의 독립적인 확장(다른 도메인은 다른 IP 주소를 가리킴)</p>
</li>
</ul>
</blockquote>
<p><em>Scenario 3: Insert path prefix</em><br>
다른 경우에는 접두사를 추가해야 할 수도 있습니다. 예를 들어 다음 프록시-서버 매핑을 고려해보세요.</p>
<pre><code class="language-null">https://example.com/api/app1/{path} -&gt; http://localhost:8080/app1/{path}</code></pre>
<p>이 경우 프록시의 접두사는 <code>/api/app1</code>이고 서버의 접두사는 <code>/app1</code>입니다. 프록시는 <code>X-Forwarded-Prefix: /api/app1</code>을 보내 원래 접두사 <code>/api/app1</code>이 서버 접두사 <code>/app1</code>을 재정의하도록 할 수 있습니다.</p>
<h2 id="forwardedheaderfilter">ForwardedHeaderFilter</h2>
<p><code>ForwardedHeaderFilter</code>는 a) <code>Forwarded</code> 헤더를 기반으로 호스트, 포트 및 구성표를 변경하고 b) 추가 영향을 제거하기 위해 해당 헤더를 제거하기 위해 요청을 수정하는 서블릿 필터입니다. 필터는 요청 래핑에 의존하므로 원래 요청이 아닌 수정된 요청에 작동해야 하는 <code>RequestContextFilter</code>와 같은 다른 필터보다 먼저 주문되어야 합니다.</p>
<h2 id="security-considerations">Security Considerations</h2>
<p>헤더가 의도한 대로 프록시에 의해 추가되었는지 아니면 악의적인 클라이언트에 의해 추가되었는지 애플리케이션이 알 수 없으므로 전달된 헤더에 대한 보안 고려 사항이 있습니다. 이것이 바로 외부에서 들어오는 신뢰할 수 없는 <code>Forwarded</code> 헤더를 제거하도록 신뢰 경계의 프록시를 구성해야 하는 이유입니다. 또한 <code>removeOnly=true</code>로 <code>ForwardedHeaderFilter</code>를 구성할 수도 있습니다. 이 경우 헤더는 제거되지만 사용되지 않습니다.</p>
<h2 id="dispatcher-types">Dispatcher Types</h2>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-async.html">비동기 요청</a> 및 오류 디스패치를 지원하려면 이 필터를 <code>DispatcherType.ASYNC</code> 및 <code>DispatcherType.ERROR</code>와 매핑해야 합니다. Spring Framework의 <code>AbstractAnnotationConfigDispatcherServletInitializer</code>(<a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/container-config.html">서블릿 구성</a> 참조)를 사용하는 경우 모든 필터는 모든 디스패치 유형에 대해 자동으로 등록됩니다. 그러나 <code>web.xml</code>을 통해 또는 Spring Boot에서 <code>FilterRegistrationBean</code>을 통해 필터를 등록하는 경우 <code>DispatcherType.REQUEST</code> 외에 <code>DispatcherType.ASYNC</code> 및 <code>DispatcherType.ERROR</code>를 포함해야 합니다.</p>
<h1 id="shallow-etag">Shallow ETag</h1>
<p><code>ShallowEtagHeaderFilter</code> 필터는 응답에 기록된 콘텐츠를 캐시하고 여기에서 MD5 해시를 계산하여 "얕은" ETag를 생성합니다. 다음에 클라이언트가 보낼 때 동일한 작업을 수행하지만 계산된 값을 <code>If-None-Match</code> 요청 헤더와 비교하여 두 값이 동일하면 304(NOT_MODIFIED)를 반환합니다.</p>
<p>이 전략은 각 요청에 대해 전체 응답을 계산해야 하므로 네트워크 대역폭은 절약하지만 CPU는 절약하지 않습니다. 상태 변경 HTTP 메서드와 <code>If-Match</code> 및 <code>If-Unmodified-Since</code>와 같은 기타 HTTP 조건부 요청 헤더는 이 필터의 범위를 벗어납니다. 컨트롤러 수준의 다른 전략은 계산을 방지하고 HTTP 조건부 요청을 더 광범위하게 지원할 수 있습니다. <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-caching.html">HTTP 캐싱</a>을 참조하세요.</p>
<p>이 필터에는 <code>W/"02a2d595e6ed9a0b24f027f2b63b134d6"</code>(<a href="https://datatracker.ietf.org/doc/html/rfc7232#section-2.3">RFC 7232 섹션 2.3</a>에 정의됨)과 유사한 약한 ETag를 쓰도록 필터를 구성하는 <code>writeWeakETag</code> 매개변수가 있습니다.</p>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-async.html">비동기 요청</a>을 지원하려면 이 필터를 <code>DispatcherType.ASYNC</code>와 매핑하여 필터가 마지막 비동기 디스패치 끝까지 ETag를 지연하고 성공적으로 생성할 수 있도록 해야 합니다. Spring Framework의 <code>AbstractAnnotationConfigDispatcherServletInitializer</code>(<a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/container-config.html">서블릿 구성</a> 참조)를 사용하는 경우 모든 필터는 모든 디스패치 유형에 대해 자동으로 등록됩니다. 그러나 <code>web.xml</code>을 통해 또는 <code>FilterRegistrationBean</code>을 통해 Spring Boot에 필터를 등록하는 경우 <code>DispatcherType.ASYNC</code>를 포함해야 합니다.</p>
<h1 id="cors">CORS</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/reactive-spring.html#webflux-filters-cors">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>Spring MVC는 컨트롤러의 주석을 통해 CORS 구성에 대한 세부적인 지원을 제공합니다. 그러나 Spring Security와 함께 사용할 경우 Spring Security의 필터 체인보다 먼저 주문해야 하는 내장 <code>CorsFilter</code>를 사용하는 것이 좋습니다.</p>
<p>자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc-cors.html">CORS</a> 및 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc-cors.html#mvc-cors-filter">CORS 필터</a> 섹션을 참조하세요.</p>