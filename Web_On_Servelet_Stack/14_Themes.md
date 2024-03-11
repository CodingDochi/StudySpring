<p>Spring Web MVC 프레임워크 테마를 적용하여 애플리케이션의 전반적인 모양과 느낌을 설정함으로써 사용자 경험을 향상시킬 수 있습니다. 테마는 애플리케이션의 시각적 스타일에 영향을 미치는 정적 리소스(일반적으로 스타일 시트 및 이미지)의 모음입니다.</p>
<blockquote>
<p><strong>[Warning]</strong><br>
6.0부터 테마 지원은 CSS 사용을 위해 더 이상 사용되지 않으며 서버 측에서는 특별한 지원이 없습니다.</p>
</blockquote>
<h1 id="defining-a-theme">Defining a theme</h1>
<p>웹 애플리케이션에서 테마를 사용하려면 <code>org.springframework.ui.context.ThemeSource</code> 인터페이스의 구현을 설정해야 합니다. <code>WebApplicationContext</code> 인터페이스는 <code>ThemeSource</code>를 확장하지만 그 책임을 전용 구현에 위임합니다. 기본적으로 위임은 클래스 경로의 루트에서 속성 파일을 로드하는 <code>org.springframework.ui.context.support.ResourceBundleThemeSource</code> 구현입니다. 사용자 정의 <code>ThemeSource</code> 구현을 사용하거나 <code>ResourceBundleThemeSource</code>의 기본 이름 접두사를 구성하려면 예약된 이름 <code>themeSource</code>를 사용하여 애플리케이션 컨텍스트에 Bean을 등록할 수 있습니다. 웹 애플리케이션 컨텍스트는 해당 이름의 Bean을 자동으로 감지하여 사용합니다.</p>
<p><code>ResourceBundleThemeSource</code>를 사용하면 테마가 간단한 속성 파일에 정의됩니다. 속성 파일에는 다음 예제와 같이 테마를 구성하는 리소스가 나열됩니다.</p>
<pre><code class="language-null">styleSheet=/themes/cool/style.css
background=/themes/cool/img/coolBg.jpg</code></pre>
<p>속성의 키는 뷰 코드에서 테마 요소를 참조하는 이름입니다. JSP의 경우 일반적으로 <code>spring:message</code> 태그와 매우 유사한 <code>spring:theme</code> 사용자 정의 태그를 사용하여 이 작업을 수행합니다. 다음 JSP 조각은 이전 예제에서 정의된 테마를 사용하여 모양과 느낌을 사용자 정의합니다.</p>
<pre><code class="language-xml">&lt;%@ taglib prefix="spring" uri="http://www.springframework.org/tags"%&gt;
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>html</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>head</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>link</span> <span class="token attr-name">rel</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>stylesheet<span class="token punctuation">"</span></span> <span class="token attr-name">href</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>&lt;spring:theme code='styleSheet'/&gt;<span class="token punctuation">"</span></span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>text/css<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>head</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>body</span> <span class="token attr-name">style</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>background=&lt;spring:theme code='background'/&gt;<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		...
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>body</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>html</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>기본적으로 <code>ResourceBundleThemeSource</code>는 빈 기본 이름 접두사를 사용합니다. 결과적으로 속성 파일은 클래스 경로의 루트에서 로드됩니다. 따라서 <code>cool.properties</code> 테마 정의를 클래스 경로 루트의 디렉토리(예: <code>/WEB-INF/classes</code>)에 배치합니다. <code>ResourceBundleThemeSource</code>는 표준 Java 리소스 번들 로딩 메커니즘을 사용하여 테마의 완전한 국제화를 허용합니다. 예를 들어, 네덜란드어 텍스트가 있는 특수 배경 이미지를 참조하는 <code>/WEB-INF/classes/cool_nl.properties</code>가 있을 수 있습니다.</p>
<h1 id="resolving-themes">Resolving Themes</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/themeresolver.html#mvc-themeresolver-defining">이전 섹션</a>에 설명된 대로 테마를 정의한 후 사용할 테마를 결정합니다. <code>DispatcherServlet</code>은 사용할 <code>ThemeResolver</code> 구현을 찾기 위해 <code>themeResolver</code>라는 빈을 찾습니다. 테마 resolver는 <code>LocaleResolver</code>와 거의 동일한 방식으로 작동합니다. 특정 요청에 사용할 테마를 감지하고 요청 테마를 변경할 수도 있습니다. 다음 표에서는 Spring에서 제공하는 테마 해결 프로그램에 대해 설명합니다.</p>
<table id="mvc-theme-resolver-impls-tbl" class="tableblock frame-all grid-all stripes-odd stretch">
<caption class="title">표 1. ThemeResolver 구현체</caption>




<thead>
<tr>
<th class="tableblock halign-left valign-top">클래스</th>
<th class="tableblock halign-left valign-top">설명</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>FixedThemeResolver</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">사용자가 설정한 고정 테마를 선택합니다. <code>defaultThemeName</code> 속성을 사용하여 설정합니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>SessionThemeResolver</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">테마가 사용자의 HTTP 세션에 유지됩니다. 각 세션마다 한 번만 설정하면 되지만 세션 간에는 지속되지 않습니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>CookieThemeResolver</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">선택한 테마가 클라이언트의 쿠키에 저장됩니다.</p></td>
</tr>
</tbody>
</table>
<p>Spring은 또한 간단한 요청 매개변수를 사용하여 모든 요청에 ​​대해 테마를 변경할 수 있는 <code>ThemeChangeInterceptor</code>를 제공합니다.</p>