<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/dispatcher-handler.html#webflux-special-bean-types">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p><code>DispatcherServlet</code>은 요청을 처리하고 적절한 응답을 렌더링하기 위해 특수 Bean에 위임합니다. "특수 빈"이란 프레임워크 계약을 구현하는 Spring 관리 <code>Object</code> 인스턴스를 의미합니다. 일반적으로 내장된 계약과 함께 제공되지만 해당 속성을 사용자 정의하고 확장하거나 교체할 수 있습니다.</p>
<p>다음 표에는 <code>DispatcherServlet</code>이 감지한 특수 Bean이 나열되어 있습니다.</p>
<table id="mvc-webappctx-special-beans-tbl" class="tableblock frame-all grid-all stripes-odd stretch">




<thead>
<tr>
<th class="tableblock halign-left valign-top">빈 유형</th>
<th class="tableblock halign-left valign-top">설명</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>HandlerMapping</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">요청을 핸들러에 매핑하고 사전 및 사후 처리를 위한 <a href="handlermapping-interceptor.html" class="xref page">인터셉터</a> 목록과 함께 매핑합니다. 매핑은 일부 기준을 기반으로 하며 <code>HandlerMapping</code> 구현에 따라 세부 사항이 달라집니다.</p>
<p class="tableblock">두 가지 주요 <code>HandlerMapping</code> 구현은 <code>@RequestMapping</code> 주석이 지정된 메서드를 지원하는 <code>RequestMappingHandlerMapping</code>과 (URI 경로 패턴을 핸들러로 명시적으로 등록하는) <code>SimpleUrlHandlerMapping</code>이 있습니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>HandlerAdapter</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>DispatcherServlet</code>이 핸들러를 요청에 매핑된 방식과 상관없이 호출할 수 있도록 돕습니다. 예를 들어, 주석 해결을 위해 주석을 해결하는 등의 작업이 필요합니다. <code>HandlerAdapter</code>의 주요 목적은 <code>DispatcherServlet</code>을 이러한 세부 사항으로부터 보호하는 것입니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="exceptionhandlers.html" class="xref page"><code>HandlerExceptionResolver</code></a></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">예외를 해결하기 위한 전략으로, 예외를 핸들러로 매핑하거나 HTML 오류 뷰 또는 다른 대상으로 매핑할 수 있습니다. <a href="exceptionhandlers.html" class="xref page">예외</a>를 참조하십시오.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="viewresolver.html" class="xref page"><code>ViewResolver</code></a></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">핸들러에서 반환된 논리적 <code>String</code> 기반 뷰 이름을 실제 <code>View</code>로 해결하여 응답에 렌더링하는 데 사용됩니다. <a href="viewresolver.html" class="xref page">뷰 해결</a> 및 <a href="../../webmvc-view.html" class="xref page">뷰 기술</a>을 참조하십시오.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="localeresolver.html" class="xref page"><code>LocaleResolver</code></a>, <a href="localeresolver.html#mvc-timezone" class="xref page">LocaleContextResolver</a></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">클라이언트가 사용하는 <code>Locale</code> 및 필요한 경우 시간대를 해결하여 국제화된 뷰를 제공할 수 있습니다. <a href="localeresolver.html" class="xref page">Locale</a>를 참조하십시오.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="themeresolver.html" class="xref page"><code>ThemeResolver</code></a></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">웹 애플리케이션이 사용할 수 있는 테마를 해결합니다. 예를 들어 개인화된 레이아웃을 제공할 수 있습니다. <a href="themeresolver.html" class="xref page">테마</a>를 참조하십시오.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="multipart.html" class="xref page"><code>MultipartResolver</code></a></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">브라우저 양식 파일 업로드와 같은 멀티파트 요청을 파싱하기 위한 추상화로, 일부 멀티파트 파싱 라이브러리의 도움을 받습니다. <a href="multipart.html" class="xref page">멀티파트 해결사</a>를 참조하십시오.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="../mvc-controller/ann-methods/flash-attributes.html" class="xref page"><code>FlashMapManager</code></a></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">한 요청에서 다른 요청으로 속성을 전달하는 데 사용할 수 있는 "입력" 및 "출력" <code>FlashMap</code>을 저장하고 검색합니다. 보통 리디렉션을 통해 전달됩니다. <a href="../mvc-controller/ann-methods/flash-attributes.html" class="xrefpage">Flash 속성</a>을 참조하십시오.</p></td>
</tr>
</tbody>
</table>