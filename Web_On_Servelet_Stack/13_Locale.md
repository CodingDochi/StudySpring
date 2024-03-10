<p>Spring 아키텍처의 대부분은 Spring 웹 MVC 프레임워크처럼 국제화(internalization)를 지원합니다. <code>DispatcherServlet</code>을 사용하면 클라이언트의 로케일을 사용하여 메시지를 자동으로 resolve할 수 있습니다. 이는 <code>LocaleResolver</code> 개체를 사용하여 수행됩니다.</p>
<p>요청이 들어오면 <code>DispatcherServlet</code>은 로케일 resolver를 찾고, 찾으면 이를 사용하여 로케일을 설정하려고 시도합니다. <code>RequestContext.getLocale()</code> 메소드를 사용하면 로케일 resolver에 의해 resolve된 로케일을 항상 검색할 수 있습니다.</p>
<p>자동 로케일 확인 외에도 인터셉터를 핸들러 매핑(핸들러 매핑 인터셉터에 대한 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/handlermapping-interceptor.html">Interception</a> 참조)에 연결하여 특정 상황(예: 요청의 매개변수 기반)에서 로케일을 변경할 수도 있습니다.</p>
<p>로케일 resolver와 인터셉터는 <code>org.springframework.web.servlet.i18n</code> 패키지에 정의되어 있으며 일반적인 방식으로 애플리케이션 컨텍스트에서 구성됩니다. 다음과 같은 로케일 resolver가 Spring에 포함되어 있습니다.</p>
<ul>
<li>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/localeresolver.html#mvc-timezone">Time Zone</a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/localeresolver.html#mvc-localeresolver-acceptheader">Header Resolver</a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/localeresolver.html#mvc-localeresolver-cookie">Cookie Resolver</a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/localeresolver.html#mvc-localeresolver-session">Session Resolver</a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/localeresolver.html#mvc-localeresolver-interceptor">Locale Interceptor</a></p>
</li>
</ul>
<h1 id="time-zone">Time Zone</h1>
<p>클라이언트의 로케일을 얻는 것 외에도 해당 시간대(time zone)를 아는 것이 유용한 경우가 많습니다. <code>LocaleContextResolver</code> 인터페이스는 resolver가 시간대 정보를 포함할 수 있는 보다 풍부한 <code>LocaleContext</code>를 제공할 수 있도록 하는 <code>LocaleResolver</code>에 대한 확장을 제공합니다.</p>
<p>가능한 경우 <code>RequestContext.getTimeZone()</code> 메서드를 사용하여 사용자의 <code>TimeZone</code>을 얻을 수 있습니다. 시간대 정보는 Spring의 <code>ConversionService</code>에 등록된 모든 날짜/시간 <code>Converter</code> 및 <code>Formatter</code> 객체에 의해 자동으로 사용됩니다.</p>
<h1 id="header-resolver">Header Resolver</h1>
<p>이 로케일 확인자는 클라이언트(예: 웹 브라우저)가 보낸 요청에서 <code>accept-language</code> 헤더를 검사합니다. 일반적으로 이 헤더 필드에는 클라이언트 운영 체제의 로캘이 포함됩니다. 이 resolver는 시간대 정보를 지원하지 않습니다.</p>
<h1 id="cookie-resolver">Cookie Resolver</h1>
<p>이 로케일 확인자는 클라이언트에 존재할 수 있는 <code>Cookie</code>를 검사하여 <code>Locale</code> 또는 <code>TimeZone</code>이 지정되었는지 확인합니다. 그렇다면 지정된 세부 정보를 사용합니다. 이 로캘 resolver의 속성을 사용하면 쿠키 이름과 최대 수명을 지정할 수 있습니다. 다음 예제에서는 <code>CookieLocaleResolver</code>를 정의합니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>localeResolver<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.web.servlet.i18n.CookieLocaleResolver<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>cookieName<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>clientlanguage<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

	<span class="token comment">&lt;!-- in seconds. If set to -1, the cookie is not persisted (deleted when browser shuts down) --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>cookieMaxAge<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>100000<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>다음 표에는 <code>CookieLocaleResolver</code> 속성(property)이 설명되어 있습니다.</p>
<table id="mvc-cookie-locale-resolver-props-tbl" class="tableblock frame-all grid-all stripes-odd stretch">
<caption class="title">표 1. CookieLocaleResolver 속성</caption>





<thead>
<tr>
<th class="tableblock halign-left valign-top">속성</th>
<th class="tableblock halign-left valign-top">기본값</th>
<th class="tableblock halign-left valign-top">설명</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>cookieName</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">클래스 이름 + LOCALE</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">쿠키의 이름</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>cookieMaxAge</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">서블릿 컨테이너 기본값</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">쿠키가 클라이언트에 유지되는 최대 시간. <code>-1</code>이 지정된 경우, 쿠키가 유지되지 않습니다. 클라이언트가 브라우저를 종료할 때까지만 사용 가능합니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>cookiePath</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">/</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">쿠키의 가시성(visibility)을 사이트의 특정 부분으로 제한합니다. <code>cookiePath</code>가 지정된 경우 해당 경로와 그 아래 경로에서만 쿠키가 가시적입니다.</p></td>
</tr>
</tbody>
</table>
<h1 id="session-resolver">Session Resolver</h1>
<p><code>SessionLocaleResolver</code>를 사용하면 사용자 요청과 연결될 수 있는 세션에서 <code>Locale</code> 및 <code>TimeZone</code>을 검색할 수 있습니다. <code>CookieLocaleResolver</code>와 달리 이 전략은 로컬에서 선택한 로케일 설정을 서블릿 컨테이너의 <code>HttpSession</code>에 저장합니다. 결과적으로 이러한 설정은 각 세션마다 일시적이므로 각 세션이 종료되면 손실됩니다.</p>
<p>Spring Session 프로젝트와 같은 외부 세션 관리 메커니즘과 직접적인 관계가 없다는 점에 유의하세요. 이 <code>SessionLocaleResolver</code>는 현재 <code>HttpServletRequest</code>에 대해 해당 <code>HttpSession</code> 속성을 평가하고 수정합니다.</p>
<h1 id="locale-interceptor">Locale Interceptor</h1>
<p><code>HandlerMapping</code> 정의 중 하나에 <code>LocaleChangeInterceptor</code>를 추가하여 로케일 변경을 활성화할 수 있습니다. 요청에서 매개변수를 감지하고 이에 따라 로케일을 변경하여 디스패처의 애플리케이션 컨텍스트에서 <code>LocaleResolver</code>의 <code>setLocale</code> 메소드를 호출합니다. 다음 예에서는 <code>siteLanguage</code>라는 매개변수가 포함된 모든 <code>*.view</code> 리소스에 대한 호출이 이제 로케일을 변경함을 보여줍니다. 예를 들어, <a href="https://www.sf.net/home.view?siteLanguage=nl"><code>www.sf.net/home.view?siteLanguage=nl</code></a>이라는 URL을 요청하면 사이트 언어가 네덜란드어로 변경됩니다. 다음 예에서는 로케일을 가로채는 방법을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>localeChangeInterceptor<span class="token punctuation">"</span></span>
		<span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.web.servlet.i18n.LocaleChangeInterceptor<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>paramName<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>siteLanguage<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>localeResolver<span class="token punctuation">"</span></span>
		<span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.web.servlet.i18n.CookieLocaleResolver<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>urlMapping<span class="token punctuation">"</span></span>
		<span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.web.servlet.handler.SimpleUrlHandlerMapping<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>interceptors<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>list</span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>ref</span> <span class="token attr-name">bean</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>localeChangeInterceptor<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>list</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>mappings<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>/**/*.view=someController<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span></code></pre>