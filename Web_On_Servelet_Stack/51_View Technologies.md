<p>Spring MVC의 뷰 기술 사용은 플러그 가능합니다. Thymeleaf, Groovy 마크업 템플릿, JSP 또는 기타 기술을 사용하기로 결정했는지 여부는 주로 구성 변경의 문제입니다. 이 장에서는 Spring MVC와 통합된 뷰 기술을 다룹니다. 우리는 당신이 이미 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/viewresolver.html">뷰 Resolution</a>에 익숙하다고 가정합니다.</p>
<blockquote>
<p><strong>[Warning]</strong><br>
Spring MVC 애플리케이션의 뷰는 해당 애플리케이션의 내부 신뢰 경계 내에 있습니다. 뷰는 애플리케이션 컨텍스트의 모든 Bean에 액세스할 수 있습니다. 따라서 보안에 영향을 미칠 수 있으므로 외부 소스에서 템플릿을 편집할 수 있는 애플리케이션에서는 Spring MVC의 템플릿 지원을 사용하지 않는 것이 좋습니다.</p>
</blockquote>
<h1 id="section-summary">Section Summary</h1>
<ul>
<li><a href="https://docs.spring.io/spring-framework/reference/web/webmvc-view/mvc-thymeleaf.html">Thymeleaf</a></li>
<li><a href="https://docs.spring.io/spring-framework/reference/web/webmvc-view/mvc-freemarker.html">FreeMarker</a></li>
<li><a href="https://docs.spring.io/spring-framework/reference/web/webmvc-view/mvc-groovymarkup.html">Groovy Markup</a></li>
<li><a href="https://docs.spring.io/spring-framework/reference/web/webmvc-view/mvc-script.html">Script Views</a></li>
<li><a href="https://docs.spring.io/spring-framework/reference/web/webmvc-view/mvc-jsp.html">JSP and JSTL</a></li>
<li><a href="https://docs.spring.io/spring-framework/reference/web/webmvc-view/mvc-feeds.html">RSS and Atom</a></li>
<li><a href="https://docs.spring.io/spring-framework/reference/web/webmvc-view/mvc-document.html">PDF and Excel</a></li>
<li><a href="https://docs.spring.io/spring-framework/reference/web/webmvc-view/mvc-jackson.html">Jackson</a></li>
<li><a href="https://docs.spring.io/spring-framework/reference/web/webmvc-view/mvc-xml-marshalling.html">XML Marshalling</a></li>
<li><a href="https://docs.spring.io/spring-framework/reference/web/webmvc-view/mvc-xslt.html">XSLT Views</a></li>
</ul>