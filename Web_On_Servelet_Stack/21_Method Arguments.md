<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-methods/arguments.html">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>다음 표에서는 지원되는 컨트롤러 메서드 인수에 대해 설명합니다. 어떤 인수에도 반응형 유형이 지원되지 않습니다.</p>
<p>JDK 8의 <code>java.util.Optional</code>은 <code>required</code> 속성(예: <code>@RequestParam</code>, <code>@RequestHeader</code> 등)이 있는 주석과 함께 메소드 인수로 지원되며 <code>required=false</code>와 동일합니다.</p>
<table class="tableblock frame-all grid-all stripes-odd stretch">




<thead>
<tr>
<th class="tableblock halign-left valign-top">컨트롤러 메소드 인자</th>
<th class="tableblock halign-left valign-top">설명</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>WebRequest</code>, <code>NativeWebRequest</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">서블릿 API를 직접 사용하지 않고 요청 매개변수와 요청 및 세션 속성에 대한 일반적인 접근.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>jakarta.servlet.ServletRequest</code>, <code>jakarta.servlet.ServletResponse</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">특정 요청 또는 응답 유형 선택 — 예를 들어, <code>ServletRequest</code>, <code>HttpServletRequest</code>,
  또는 스프링의 <code>MultipartRequest</code>, <code>MultipartHttpServletRequest</code> 등.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>jakarta.servlet.http.HttpSession</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">세션의 존재를 강제합니다. 따라서 이러한 인수는 결코 <code>null</code>이 아닙니다.
  세션 액세스는 스레드 안전하지 않습니다. 여러 요청이 세션에 동시에 액세스하도록 허용되는 경우 <code>RequestMappingHandlerAdapter</code> 인스턴스의 <code>synchronizeOnSession</code> 플래그를 <code>true</code>로 설정하는 것이 좋습니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>jakarta.servlet.http.PushBuilder</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">프로그래밍 방식의 HTTP/2 리소스 푸시를 위한 Servlet 4.0 푸시 빌더 API.
  Servlet 사양에 따르면 클라이언트가 해당 HTTP/2 기능을 지원하지 않으면 주입된 <code>PushBuilder</code> 인스턴스가 <code>null</code>일 수 있습니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>java.security.Principal</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">현재 인증된 사용자 — 알려진 경우 특정 <code>Principal</code> 구현 클래스.</p>
<p class="tableblock">  이 인수는 게으르게 해결되지 않습니다. 즉, 사용자가 <code>HttpServletRequest#getUserPrincipal</code>를 통해 기본 해결로 전환되기 전에 사용자 정의 리졸버를 통해 이를 해결할 수 있도록 주석이 달려 있습니다.
  예를 들어 Spring Security <code>Authentication</code>은 <code>Principal</code>을 구현하고 이를 통해 <code>HttpServletRequest#getUserPrincipal</code>에 주입되며, <code>@AuthenticationPrincipal</code>로 주석이 달려 있지 않은 경우 Spring Security 리졸버를 통해 <code>Authentication#getPrincipal</code>로 해결됩니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>HttpMethod</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">요청의 HTTP 메소드.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>java.util.Locale</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">가장 구체적인 <code>LocaleResolver</code>에 의해 결정된 현재 요청 로캘(설정된 <code>LocaleResolver</code> 또는 <code>LocaleContextResolver</code>).</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>java.util.TimeZone</code> + <code>java.time.ZoneId</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">현재 요청과 관련된 시간대, <code>LocaleContextResolver</code>에 의해 결정됨.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>java.io.InputStream</code>, <code>java.io.Reader</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">Servlet API에 의해 노출된 원시 요청 본문에 대한 액세스.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>java.io.OutputStream</code>, <code>java.io.Writer</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">Servlet API에 의해 노출된 원시 응답 본문에 대한 액세스.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>@PathVariable</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">URI 템플릿 변수에 액세스하기 위해. <a href="../ann-requestmapping.html#mvc-ann-requestmapping-uri-templates" class="xref page">URI 패턴</a> 참조.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>@MatrixVariable</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">URI 경로 세그먼트의 이름-값 쌍에 액세스하기 위해. <a href="matrix-variables.html" class="xref page">매트릭스 변수</a> 참조.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>@RequestParam</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">멀티파트 파일을 포함한 서블릿 요청 매개변수에 액세스하기 위해. 매개변수 값은 선언된 메소드 인수 유형으로 변환됨. <a href="requestparam.html" class="xref page"><code>@RequestParam</code></a> 및 <a href="multipart-forms.html" class="xref page">멀티파트</a> 참조.</p>
<p class="tableblock">  간단한 매개변수 값의 경우 <code>@RequestParam</code>의 사용은 선택 사항입니다.
  이 표의 마지막 부분인 “다른 모든 인수”를 참조하십시오.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>@RequestHeader</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">요청 헤더에 액세스하기 위해. 헤더 값은 선언된 메소드 인수 유형으로 변환됨. <a href="requestheader.html" class="xref page"><code>@RequestHeader</code></a> 참조.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>@CookieValue</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">쿠키에 액세스하기 위해. 쿠키 값은 선언된 메소드 인수 유형으로 변환됨. <a href="cookievalue.html" class="xref page"><code>@CookieValue</code></a> 참조.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>@RequestBody</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">HTTP 요청 본문에 액세스하기 위해. 본문 내용은 <code>HttpMessageConverter</code> 구현을 사용하여 선언된 메소드 인수 유형으로 변환됨. <a href="requestbody.html" class="xref page"><code>@RequestBody</code></a> 참조.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>HttpEntity&lt;B&gt;</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">요청 헤더 및 본문에 액세스하기 위해. 본문은 <code>HttpMessageConverter</code>를 사용하여 변환됨. <a href="httpentity.html" class="xref page">HttpEntity</a> 참조.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>@RequestPart</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>multipart/form-data</code> 요청의 파트에 액세스하기 위해. 파트의 본문은 <code>HttpMessageConverter</code>를 사용하여 변환됨. <a href="multipart-forms.html" class="xref page">멀티파트</a> 참조.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>java.util.Map</code>, <code>org.springframework.ui.Model</code>, <code>org.springframework.ui.ModelMap</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">HTML 컨트롤러에서 사용되는 및 뷰 렌더링 시 템플릿에 노출되는 모델에 액세스하기 위해.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>RedirectAttributes</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">리다이렉트 시 사용할 속성 지정 (즉, 쿼리 문자열에 추가됨) 및 리다이렉트 후 요청까지 일시적으로 저장할 플래시 속성 지정.
  <a href="redirecting-passing-data.html" class="xref page">리다이렉트 속성</a> 및 <a href="flash-attributes.html" class="xref page">플래시 속성</a> 참조.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>@ModelAttribute</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">모델에서 기존 속성에 액세스하기 위해 (없으면 인스턴스화됨) 데이터 바인딩 및 유효성 검사가 적용됨. <a href="modelattrib-method-args.html" class="xref page"><code>@ModelAttribute</code></a>, <a href="../ann-modelattrib-methods.html" class="xref page">모델</a>, <a href="../ann-initbinder.html" class="xref page"><code>DataBinder</code></a> 참조.</p>
<p class="tableblock">  <code>@ModelAttribute</code>의 사용은 선택 사항입니다(예: 속성 설정). 이 표의 “다른 모든 인수”를 참조하십시오.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>Errors</code>, <code>BindingResult</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">명령 객체(즉, <code>@ModelAttribute</code> 인수) 또는 <code>@RequestBody</code> 또는 <code>@RequestPart</code> 인수의 유효성 검증 및 데이터 바인딩에서의 오류에 액세스하기 위해.
  유효성 검증 후 바로 오류 또는 <code>BindingResult</code> 인수를 선언해야 함.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>SessionStatus</code> + 클래스 수준의 <code>@SessionAttributes</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">폼 처리 완료 표시를 위해, 클래스 수준의 <code>@SessionAttributes</code> 주석을 통해 선언된 세션 속성의 정리를 트리거합니다. 자세한 내용은 <a href="sessionattributes.html" class="xref page"><code>@SessionAttributes</code></a> 참조.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>UriComponentsBuilder</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">현재 요청의 호스트, 포트, 스키마, 컨텍스트 경로 및 서블릿 매핑의 리터럴 부분에 상대적인 URL을 준비하기 위해. <a href="../../mvc-uri-building.html" class="xref page">URI 링크</a> 참조.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>@SessionAttribute</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">세션에 저장된 모델 속성과는 달리 세션 속성에 액세스하기 위해. 자세한 내용은 <a href="sessionattribute.html" class="xref page"><code>@SessionAttribute</code></a> 참조.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>@RequestAttribute</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">요청 속성에 액세스하기 위해. 자세한 내용은 <a href="requestattrib.html" class="xref page"><code>@RequestAttribute</code></a> 참조.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">다른 모든 인수</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">이 표의 이전 항목과 일치하지 않는 메소드 인자가 간단한 유형인 경우
  (BeanUtils#isSimpleProperty에 의해 결정됨),
  <code>@RequestParam</code>으로 해결됩니다. 그렇지 않으면 <code>@ModelAttribute</code>로 해결됩니다.</p></td>
</tr>
</tbody>
</table>