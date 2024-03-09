<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/dispatcher-handler.html#webflux-viewresolution">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>Spring MVC는 특정 뷰 기술에 얽매이지 않고 브라우저에서 모델을 렌더링할 수 있게 해주는 <code>ViewResolver</code> 및 <code>View</code> 인터페이스를 정의합니다. <code>ViewResolver</code>는 뷰 이름과 실제 뷰 간의 매핑을 제공합니다. <code>View</code>는 특정 뷰 기술로 넘겨지기 전에 데이터를 준비하는 작업을 다룹니다.</p>
<p>다음 표에서는 <code>ViewResolver</code> 계층 구조에 대한 자세한 내용을 제공합니다.</p>
<table id="mvc-view-resolvers-tbl" class="tableblock frame-all grid-all stripes-odd stretch">
<caption class="title">표 1. ViewResolver 구현체</caption>




<thead>
<tr>
<th class="tableblock halign-left valign-top">ViewResolver</th>
<th class="tableblock halign-left valign-top">설명</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>AbstractCachingViewResolver</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>AbstractCachingViewResolver</code>의 하위 클래스는 해결한 뷰 인스턴스를 캐시합니다. 캐시는 특정 뷰 기술의 성능을 향상시킵니다. <code>cache</code> 속성을 <code>false</code>로 설정하여 캐시를 끌 수 있습니다. 또한 특정 뷰를 런타임에 새로 고칠 필요가 있을 때(예: FreeMarker 템플릿이 수정된 경우) <code>removeFromCache(String viewName, Locale loc)</code> 메서드를 사용할 수 있습니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>UrlBasedViewResolver</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ViewResolver</code> 인터페이스의 간단한 구현으로, 명시적인 매핑 정의 없이 논리적 뷰 이름을 URL로 직접 resolve합니다. 논리적 이름이 임의의 매핑 없이 뷰 리소스의 이름과 일치하는 경우에 적합합니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>InternalResourceViewResolver</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>UrlBasedViewResolver</code>의 편리한 하위 클래스로, <code>InternalResourceView</code>(실제로 서블릿 및 JSP) 및 <code>JstlView</code>와 같은 하위 클래스를 지원합니다. 이 리졸버에 의해 생성된 모든 뷰에 대한 뷰 클래스를 <code>setViewClass(..)</code>를 사용하여 지정할 수 있습니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/docs/6.1.5/javadoc-api/org/springframework/web/reactive/result/view/UrlBasedViewResolver.html"><code>UrlBasedViewResolver</code></a> javadoc을 참조하십시오.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>FreeMarkerViewResolver</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>UrlBasedViewResolver</code>의 편리한 하위 클래스로, <code>FreeMarkerView</code>와 해당 하위 클래스를 지원합니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ContentNegotiatingViewResolver</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">요청 파일 이름 또는 <code>Accept</code> 헤더를 기반으로 뷰를 resolve하는 <code>ViewResolver</code> 인터페이스의 구현체입니다. <a href="#mvc-multiple-representations">콘텐츠 협상</a>을 참조하십시오.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>BeanNameViewResolver</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">뷰 이름을 현재 애플리케이션 컨텍스트의 빈 이름으로 해석하는 <code>ViewResolver</code> 인터페이스의 구현체입니다. 이는 매우 유연한 변형으로, 고유한 뷰 이름을 기반으로 다른 유형의 뷰를 혼합하고 매치할 수 있습니다. 각 <code>View</code>는 XML 또는 구성 클래스에서 빈으로 정의될 수 있습니다.</p></td>
</tr>
</tbody>
</table>
<h1 id="handling">Handling</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/dispatcher-handler.html#webflux-viewresolution-handling">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>하나 이상의 리졸버 빈을 선언하고, 필요한 경우 순서를 지정하는 <code>order</code> 속성을 설정하여 뷰 리졸버를 연결할 수 있습니다. order 속성이 높을수록 뷰 resolver가 체인에 더 늦게 배치된다는 점을 기억하세요.</p>
<p><code>ViewResolver</code>의 계약은 뷰를 찾을 수 없음을 나타내기 위해 null을 반환할 수 있음을 지정합니다. 그러나 JSP 및 <code>InternalResourceViewResolver</code>의 경우 JSP가 존재하는지 알아내는 유일한 방법은 <code>RequestDispatcher</code>를 통해 디스패치를 수행하는 것입니다. 따라서 항상 <code>InternalResourceViewResolver</code>가 뷰 resolver의 전체 순서에서 마지막이 되도록 구성해야 합니다.</p>
<p>뷰 해상도 구성은 Spring 구성에 <code>ViewResolver</code> 빈을 추가하는 것만큼 간단합니다. <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-config.html">MVC 구성</a>은 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-config/view-resolvers.html">뷰 resolver</a> 및 컨트롤러 로직 없이 HTML 템플릿 렌더링에 유용한 로직 없는 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-config/view-controller.html">뷰 컨트롤러</a>를 추가하기 위한 전용 구성 API를 제공합니다.</p>
<h1 id="redirecting">Redirecting</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/dispatcher-handler.html#webflux-redirecting-redirect-prefix">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>view 이름의 특수 <code>redirect:</code> 접두어를 사용하면 리디렉션을 수행할 수 있습니다. <code>UrlBasedViewResolver</code>(및 해당 하위 클래스)는 이를 리디렉션이 필요하다는 명령으로 인식합니다. view 이름의 나머지 부분은 리디렉션 URL입니다.</p>
<p>최종 효과는 컨트롤러가 <code>RedirectView</code>를 반환한 것과 동일하지만 이제 컨트롤러 자체가 논리적 뷰 이름으로 작동할 수 있습니다. 논리적 view 이름(예: <code>redirect:/myapp/some/resource</code>)은 현재 서블릿 컨텍스트를 기준으로 리디렉션되는 반면, <code>redirect:https://myhost.com/some/arbitrary/path</code>와 같은 이름은 절대 URL로 리디렉션됩니다.</p>
<h1 id="forwarding">Forwarding</h1>
<p><code>UrlBasedViewResolver</code> 및 하위 클래스에 의해 최종적으로 resolve되는 뷰 이름에 특별한 <code>forward:</code> 접두사를 사용할 수도 있습니다. 이는 <code>RequestDispatcher.forward()</code>를 수행하는 <code>InternalResourceView</code>를 생성합니다. 따라서 이 접두사는 <code>InternalResourceViewResolver</code> 및 <code>InternalResourceView</code>(JSP용)에는 유용하지 않지만, 다른 view technology을 사용하지만 여전히 Servlet/JSP 엔진에서 처리할 리소스를 강제로 전달하려는 경우에는 도움이 될 수 있습니다. 대신 여러 개의 뷰 리졸버를 연결할 수도 있습니다.</p>
<h1 id="content-negotiation">Content Negotiation</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/dispatcher-handler.html#webflux-multiple-representations">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p><code>ContentNegotiatingViewResolver</code>는 뷰 자체를 resolve하지 않고 오히려 다른 뷰 resolver에게 위임하고 클라이언트가 요청한 표현(representation)과 유사한 뷰를 선택합니다. 표현은 <code>Accept</code> 헤더 또는 쿼리 매개변수(예: "<code>/path?format=pdf</code>")에서 결정될 수 있습니다.</p>
<p><code>ContentNegotiatingViewResolver</code>는 요청 미디어 type을 각 <code>ViewResolver</code>와 연관된 <code>View</code>가 지원하는 미디어 type(<code>Content-Type</code>이라고도 함)과 비교하여 요청을 처리할 적절한 <code>View</code>를 선택합니다. 호환되는 <code>Content-Type</code>이 있는 목록의 첫 번째 <code>View</code>는 표현을 클라이언트에 반환합니다. <code>ViewResolver</code> 체인에서 호환 가능한 뷰를 제공할 수 없는 경우 <code>DefaultViews</code> 속성을 통해 지정된 view 목록이 참조됩니다. 후자의 옵션은 논리적 view 이름에 관계없이 현재 리소스의 적절한 표현을 렌더링할 수 있는 싱글톤 <code>Views</code>에 적합합니다. <code>Accept</code> 헤더에는 와일드카드(예: <code>text/*</code>)가 포함될 수 있으며, 이 경우 <code>Content-Type</code>이 <code>text/xml</code>인 <code>View</code>가 호환 가능합니다.</p>
<p>구성 세부 사항은 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-config.html">MVC 구성 아래</a>의 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-config/view-resolvers.html">View Resolvers</a>를 참조하세요.</p>