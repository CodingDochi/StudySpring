<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/reactive-spring.html#webflux-multipart">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p><code>org.springframework.web.multipart</code> 패키지의 <code>MultipartResolver</code>는 파일 업로드를 포함한 멀티파트 요청을 구문 분석하기 위한 전략입니다. Servlet 멀티파트 요청 구문 분석을 위한 컨테이너 기반 <code>StandardServletMultipartResolver</code> 구현이 있습니다. Apache Commons FileUpload를 기반으로 하는 오래된 <code>CommonsMultipartResolver</code>는 새로운 Servlet 5.0+ 기준이 포함된 Spring Framework 6.0부터 더 이상 사용할 수 없습니다.</p>
<p>멀티파트 처리를 활성화하려면 <code>DispatcherServlet</code> Spring 구성에서 <code>multipartResolver</code>라는 이름으로 <code>MultipartResolver</code> 빈을 선언해야 합니다. <code>DispatcherServlet</code>은 이를 감지하여 들어오는 요청에 적용합니다. <code>multipart/form-data</code> 콘텐츠 유형의 POST가 수신되면 resolver는 현재 <code>HttpServletRequest</code>를 <code>MultipartHttpServletRequest</code>로 래핑하여 콘텐츠를 구문 분석하여 부분을 요청 매개변수로 노출하는 것 외에도 확인된 파일에 대한 액세스를 제공합니다.</p>
<h1 id="servlet-multipart-parsing">Servlet Multipart Parsing</h1>
<p>서블릿 멀티파트 구문 분석은 서블릿 컨테이너 구성을 통해 활성화되어야 합니다. 그렇게 하려면:</p>
<ul>
<li>
<p>Java에서는 서블릿 등록에 <code>MultipartConfigElement</code>를 설정합니다.</p>
</li>
<li>
<p><code>web.xml</code>에서 서블릿 선언에 <code>"&lt;multipart-config&gt;"</code> 섹션을 추가합니다.</p>
</li>
</ul>
<p>다음 예에서는 서블릿 등록에 <code>MultipartConfigElement</code>를 설정하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppInitializer</span> <span class="token keyword">extends</span> <span class="token class-name">AbstractAnnotationConfigDispatcherServletInitializer</span> <span class="token punctuation">{</span>

	<span class="token comment">// ...</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">protected</span> <span class="token keyword">void</span> <span class="token function">customizeRegistration</span><span class="token punctuation">(</span><span class="token class-name">ServletRegistration</span><span class="token punctuation">.</span><span class="token class-name">Dynamic</span> registration<span class="token punctuation">)</span> <span class="token punctuation">{</span>

		<span class="token comment">// Optionally also set maxFileSize, maxRequestSize, fileSizeThreshold</span>
		registration<span class="token punctuation">.</span><span class="token function">setMultipartConfig</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">MultipartConfigElement</span><span class="token punctuation">(</span><span class="token string">"/tmp"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p>Servlet 멀티파트 구성이 완료되면 <code>multipartResolver</code>라는 이름을 가진 <code>StandardServletMultipartResolver</code> 유형의 Bean을 추가할 수 있습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
이 resolver 변형은 서블릿 컨테이너의 멀티파트 파서를 있는 그대로 사용하여 잠재적으로 애플리케이션을 컨테이너 구현 차이에 노출시킵니다. 기본적으로 모든 HTTP 메서드를 사용하여 모든 <code>multipart/</code> 콘텐츠 유형을 구문 분석하려고 시도하지만 이는 모든 서블릿 컨테이너에서 지원되지 않을 수 있습니다. 자세한 내용과 구성 옵션은 <a href="https://docs.spring.io/spring-framework/docs/6.1.6/javadoc-api/org/springframework/web/multipart/support/StandardServletMultipartResolver.html"><code>StandardServletMultipartResolver</code></a> javadoc를 참조하세요.</p>
</blockquote>