<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-requestmapping.html">반응형 스택에서 이에 상응하는 내용 보기</a> </p>
<p>이 섹션에서는 annotation이 달린 컨트롤러에 대한 요청 매핑에 대해 설명합니다.</p>
<h1 id="requestmapping"><code>@RequestMapping</code></h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-requestmapping.html#webflux-ann-requestmapping-annotation">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p><code>@RequestMapping</code> annotation을 사용하여 요청을 컨트롤러 메서드에 매핑할 수 있습니다. URL, HTTP 메소드, 요청 매개변수, 헤더 및 미디어 유형별로 일치시킬 수 있는 다양한 속성이 있습니다. 클래스 수준에서 이를 사용하여 공유 매핑을 표현하거나 메서드 수준에서 특정 끝점 매핑으로 범위를 좁힐 수 있습니다.</p>
<p><code>@RequestMapping</code>의 HTTP 메소드별 단축 변형도 있습니다:</p>
<ul>
<li>
<p><code>@GetMapping</code></p>
</li>
<li>
<p><code>@PostMapping</code></p>
</li>
<li>
<p><code>@PutMapping</code></p>
</li>
<li>
<p><code>@DeleteMapping</code></p>
</li>
<li>
<p><code>@PatchMapping</code></p>
</li>
</ul>
<p>바로 가기는 기본적으로 모든 HTTP 메서드와 일치하는 <code>@RequestMapping</code>을 사용하는 대신 대부분의 컨트롤러 메서드가 특정 HTTP 메서드에 매핑되어야 하기 때문에 제공되는 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-requestmapping.html#mvc-ann-requestmapping-composed">사용자 지정 annotation</a>입니다. 공유 매핑을 표현하려면 클래스 수준에서 <code>@RequestMapping</code>이 여전히 필요합니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
<code>@RequestMapping</code>은 동일한 요소(클래스, 인터페이스 또는 메소드)에 선언된 다른 <code>@RequestMapping</code> annotation과 함께 사용할 수 없습니다. 동일한 요소에서 여러 <code>@RequestMapping</code> annotation이 감지되면 경고가 기록되고 첫 번째 매핑만 사용됩니다. 이는 <code>@GetMapping</code>, <code>@PostMapping</code> 등과 같이 구성된 <code>@RequestMapping</code> annotation에도 적용됩니다.</p>
</blockquote>
<p>다음 예에는 유형 및 메서드 수준 매핑이 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@RestController</span>
<span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span><span class="token string">"/persons"</span><span class="token punctuation">)</span>
<span class="token keyword">class</span> <span class="token class-name">PersonController</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/{id}"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">Person</span> <span class="token function">getPerson</span><span class="token punctuation">(</span><span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">Long</span> id<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@PostMapping</span>
	<span class="token annotation punctuation">@ResponseStatus</span><span class="token punctuation">(</span><span class="token class-name">HttpStatus</span><span class="token punctuation">.</span>CREATED<span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">add</span><span class="token punctuation">(</span><span class="token annotation punctuation">@RequestBody</span> <span class="token class-name">Person</span> person<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h1 id="uri-patterns">URI patterns</h1>
<p>[반응형 스택에서 이에 상응하는 내용 보기</p>
<p><code>@RequestMapping</code> 메소드는 URL 패턴을 사용하여 매핑될 수 있습니다. 두 가지 대안이 있습니다:</p>
<ul>
<li>
<p><code>PathPattern </code> -  <code>PathContainer</code>로도 사전 구문 분석된 URL 경로와 일치하는 사전 구문 분석된 패턴입니다. 웹용으로 설계된 이 솔루션은 인코딩 및 경로 매개변수를 효과적으로 처리하고 효율적으로 일치시킵니다.</p>
</li>
<li>
<p><code>AntPathMatcher</code>  -  문자열 패턴을 문자열 경로와 일치시킵니다. 이는 클래스 경로, 파일 시스템 및 기타 위치에서 리소스를 선택하기 위해 Spring 구성에서도 사용되는 원래 솔루션입니다. 이는 덜 효율적이며 문자열 경로 입력은 인코딩 및 URL 관련 기타 문제를 효과적으로 처리하는 데 어려움이 있습니다.</p>
</li>
</ul>
<p><code>PathPattern</code>은 웹 애플리케이션에 권장되는 솔루션이며 Spring WebFlux의 유일한 선택입니다. 버전 5.3부터 Spring MVC에서 사용할 수 있도록 활성화되었으며 버전 6.0부터 기본적으로 활성화됩니다. 경로 일치 옵션의 사용자 정의는 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-config/path-matching.html">MVC 구성</a>을 참조하세요.</p>
<p><code>PathPattern</code>은 <code>AntPathMatcher</code>와 동일한 패턴 구문을 지원합니다. 또한 캡처 패턴도 지원합니다. <code>{*spring}</code> 경로 끝에서 0개 이상의 경로 세그먼트를 일치시킵니다. <code>PathPattern</code>은 또한 패턴 끝에서만 허용되도록 여러 경로 세그먼트를 일치시키기 위해 <code>**</code> 사용을 제한합니다. 이는 주어진 요청에 가장 적합한 패턴을 선택할 때 모호한 경우를 많이 제거합니다. 전체 패턴 구문은 <a href="https://docs.spring.io/spring-framework/docs/6.1.6/javadoc-api/org/springframework/web/util/pattern/PathPattern.html">PathPattern</a> 및 <a href="https://docs.spring.io/spring-framework/docs/6.1.6/javadoc-api/org/springframework/util/AntPathMatcher.html">AntPathMatcher</a>를 참조하세요.</p>
<p>몇 가지 예시 패턴은 다음과 같습니다.</p>
<ul>
<li>
<p><code>"/resources/ima?e.png"</code> - 경로 세그먼트에서 한 문자와 일치합니다.</p>
</li>
<li>
<p><code>"/resources/*.png"</code> - 경로 세그먼트에서 0개 이상의 문자와 일치합니다.</p>
</li>
<li>
<p><code>"/resources/**"</code> - 여러 경로 세그먼트 일치</p>
</li>
<li>
<p><code>"/projects/{project}/versions"</code> - 경로 세그먼트를 일치시키고 이를 변수로 캡처합니다.</p>
</li>
<li>
<p><code>"/projects/{project:[a-z]}/versions"+</code> - 정규식을 사용하여 변수를 일치시키고 캡처합니다.</p>
</li>
</ul>
<p>캡처된 URI 변수는 <code>@PathVariable</code>을 사용하여 액세스할 수 있습니다. 예를 들어:</p>
<pre><code class="language-java"><span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/owners/{ownerId}/pets/{petId}"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token class-name">Pet</span> <span class="token function">findPet</span><span class="token punctuation">(</span><span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">Long</span> ownerId<span class="token punctuation">,</span> <span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">Long</span> petId<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 예제와 같이 클래스 및 메서드 수준에서 URI 변수를 선언할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Controller</span>
<span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span><span class="token string">"/owners/{ownerId}"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">OwnerController</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/pets/{petId}"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">Pet</span> <span class="token function">findPet</span><span class="token punctuation">(</span><span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">Long</span> ownerId<span class="token punctuation">,</span> <span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">Long</span> petId<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>URI 변수는 자동으로 적절한 유형으로 변환되거나 <code>TypeMismatchException</code>이 발생합니다. 기본적으로 단순 유형(<code>int</code>, <code>long</code>, <code>Date</code> 등)이 지원되며 다른 데이터 유형에 대한 지원을 등록할 수 있습니다. <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/typeconversion.html">type 변환</a> 및 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-initbinder.html"><code>DataBinder</code></a>를 참조하세요.</p>
<p>URI 변수의 이름을 명시적으로 지정할 수 있습니다(예: <code>@PathVariable("customId")</code>). 그러나 이름이 동일하고 코드가 <code>-parameters</code> 컴파일러 플래그를 사용하여 컴파일되는 경우 해당 세부 정보를 생략할 수 있습니다.</p>
<p><code>{varName:regex}</code> 구문은 <code>{varName:regex}</code> 구문이 있는 정규식을 사용하여 URI 변수를 선언합니다. 예를 들어, URL이 <code>"/spring-web-3.0.5.jar"</code>인 경우 다음 메서드는 이름, 버전 및 파일 확장자를 추출합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/{name:[a-z-]+}-{version:\\d\\.\\d\\.\\d}{ext:\\.[a-z]+}"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">handle</span><span class="token punctuation">(</span><span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">String</span> name<span class="token punctuation">,</span> <span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">String</span> version<span class="token punctuation">,</span> <span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">String</span> ext<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>URI 경로 패턴에는 로컬, 시스템, 환경 및 기타 속성 소스에 대해 <code>PropertySourcesPlaceholderConfigurer</code>를 사용하여 시작 시 확인되는 내장된 <code>${…​}</code> 자리 표시자가 있을 수도 있습니다. 예를 들어, 이를 사용하여 일부 외부 구성을 기반으로 기본 URL을 매개변수화할 수 있습니다.</p>
<h1 id="pattern-comparison">Pattern Comparison</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-requestmapping.html#webflux-ann-requestmapping-pattern-comparison">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>여러 패턴이 URL과 일치하는 경우 가장 일치하는 패턴을 선택해야 합니다. 이는 구문 분석된 <code>PathPattern</code> 사용이 활성화되었는지 여부에 따라 다음 중 하나로 수행됩니다.</p>
<ul>
<li>
<p><code>PathPattern.SPECIFICITY_COMPARATOR</code></p>
</li>
<li>
<p><code>AntPathMatcher.getPatternComparator(String path)</code></p>
</li>
</ul>
<p>둘 다 패턴을 더 구체적인 패턴으로 정렬하는 데 도움이 됩니다. URI 변수 수가 더 적고(1로 계산), 단일 와일드카드(1로 계산), 이중 와일드카드(2로 계산)가 있으면 패턴이 더 구체적입니다. 동일한 점수가 주어지면 더 긴 패턴이 선택됩니다. 동일한 점수와 길이가 주어지면 와일드카드보다 URI 변수가 더 많은 패턴이 선택됩니다.</p>
<p>기본 매핑 패턴(<code>/**</code>)은 채점에서 제외되며 항상 마지막에 정렬됩니다. 또한 접두사 패턴(예: <code>/public/**</code>)은 이중 와일드카드가 없는 다른 패턴보다 덜 구체적인 것으로 간주됩니다.</p>
<p>자세한 내용을 보려면 위의 패턴 비교기 링크를 따르세요.</p>
<h1 id="suffix-match">Suffix Match</h1>
<p>5.3부터 기본적으로 Spring MVC는 <code>/person</code>에 매핑된 컨트롤러가 암시적으로 <code>/person.*</code>에 매핑되는 <code>.*</code> 접미사 패턴 일치를 더 이상 수행하지 않습니다. 결과적으로 경로 확장은 응답에 대해 요청된 콘텐츠 유형(예: <code>/person.pdf</code>, <code>/person.xml</code> 등)을 해석하는 데 더 이상 사용되지 않습니다.</p>
<p>브라우저가 일관되게 해석하기 어려운 <code>Accept</code> 헤더를 보내는 데 사용되는 경우 이러한 방식으로 파일 확장자를 사용하는 것이 필요했습니다. 현재로서는 더 이상 필요하지 않으며 <code>Accept</code> 헤더를 사용하는 것이 선호되는 선택입니다.</p>
<p>시간이 지남에 따라 파일 이름 확장자를 사용하면 다양한 방식으로 문제가 있는 것으로 입증되었습니다. URI 변수, 경로 매개변수 및 URI 인코딩을 사용하면 모호함이 발생할 수 있습니다. URL 기반 인증 및 보안(자세한 내용은 다음 섹션 참조)에 대한 추론도 더욱 어려워집니다.</p>
<p>5.3 이전 버전에서 경로 확장 사용을 완전히 비활성화하려면 다음을 설정하십시오.</p>
<ul>
<li>
<p><code>useSuffixPatternMatching(false)</code>, <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-config/path-matching.html">PathMatchConfigurer</a> 참조</p>
</li>
<li>
<p><code>favourPathExtension(false)</code>, <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-config/content-negotiation.html">ContentNegotiationConfigurer</a> 참조</p>
</li>
</ul>
<p><code>"Accept"</code> 헤더를 통하지 않고 콘텐츠 유형을 요청하는 방법이 있으면 여전히 유용할 수 있습니다. 브라우저에 URL을 입력할 때 경로 확장에 대한 안전한 대안은 쿼리 매개변수 전략을 사용하는 것입니다. 파일 확장자를 사용해야 하는 경우 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-config/content-negotiation.html">ContentNegotiationConfigurer</a>의 <code>mediaTypes</code> 속성을 통해 명시적으로 등록된 확장자 목록으로 제한하는 것이 좋습니다.</p>
<h1 id="suffix-match-and-rfd">Suffix Match and RFD</h1>
<p>반사 파일 다운로드(RFD) 공격은 응답에 반영되는 요청 입력(예: 쿼리 매개변수 및 URI 변수)에 의존한다는 점에서 XSS와 유사합니다. 그러나 RFD 공격은 JavaScript를 HTML에 삽입하는 대신 브라우저를 사용하여 다운로드를 수행하고 나중에 두 번 클릭할 때 응답을 실행 가능한 스크립트로 처리합니다.</p>
<p>Spring MVC에서 <code>@ResponseBody</code> 및 <code>ResponseEntity</code> 메소드는 클라이언트가 URL 경로 확장을 통해 요청할 수 있는 다양한 컨텐츠 유형을 렌더링할 수 있기 때문에 위험합니다. 접미사 패턴 일치를 비활성화하고 콘텐츠 협상을 위해 경로 확장을 사용하면 위험이 낮아지지만 RFD 공격을 예방하기에는 충분하지 않습니다.</p>
<p>RFD 공격을 방지하기 위해 응답 본문을 렌더링하기 전에 Spring MVC는 <code>Content-Disposition:inline;filename=f.txt</code> 헤더를 추가하여 고정되고 안전한 다운로드 파일을 제안합니다. 이는 URL 경로에 안전한 것으로 허용되지 않거나 콘텐츠 협상에 명시적으로 등록되지 않은 파일 확장자가 포함된 경우에만 수행됩니다. 그러나 URL을 브라우저에 직접 입력하면 부작용이 발생할 수 있습니다.</p>
<p>많은 공통 경로 확장은 기본적으로 안전한 것으로 허용됩니다. 사용자 정의 <code>HttpMessageConverter</code> 구현을 사용하는 애플리케이션은 해당 확장에 대해 <code>Content-Disposition</code> 헤더가 추가되는 것을 방지하기 위해 콘텐츠 협상을 위한 파일 확장자를 명시적으로 등록할 수 있습니다. <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-config/content-negotiation.html">콘텐츠 type</a>을 참조하세요.</p>
<p>RFD와 관련된 추가 권장 사항은 <a href="https://spring.io/security/cve-2015-5211">CVE-2015-5211</a>을 참조하세요.</p>
<h1 id="consumable-media-types">Consumable Media Types</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-requestmapping.html#webflux-ann-requestmapping-consumes">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>다음 예와 같이 요청의 <code>Content-Type</code>을 기반으로 요청 매핑 범위를 좁힐 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@PostMapping</span><span class="token punctuation">(</span>path <span class="token operator">=</span> <span class="token string">"/pets"</span><span class="token punctuation">,</span> consumes <span class="token operator">=</span> <span class="token string">"application/json"</span><span class="token punctuation">)</span> <span class="token comment">// (1)</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">addPet</span><span class="token punctuation">(</span><span class="token annotation punctuation">@RequestBody</span> <span class="token class-name">Pet</span> pet<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) 소비 속성을 사용하여 콘텐츠 유형별로 매핑 범위를 좁힙니다.</p>
<p><code>consumes</code> 속성은 부정 표현식도 지원합니다. 예를 들어 <code>!text/plain</code>은 <code>text/plain</code>이 아닌 모든 콘텐츠 유형을 의미합니다.</p>
<p>클래스 수준에서 공유 <code>consumes</code> 속성을 선언할 수 있습니다. 그러나 대부분의 다른 요청 매핑 속성과 달리 클래스 수준에서 사용될 때 메서드 수준은 클래스 수준 선언을 확장하는 대신 <code>consumes</code> 속성 재정의를 사용합니다.</p>
<blockquote>
<p><strong>[Tip]</strong><br>
<code>MediaType</code>은 <code>APPLICATION_JSON_VALUE</code> 및 <code>APPLICATION_XML_VALUE</code>와 같이 일반적으로 사용되는 미디어 유형에 대한 상수를 제공합니다.</p>
</blockquote>
<h1 id="producible-media-types">Producible Media Types</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-requestmapping.html#webflux-ann-requestmapping-produces">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>다음 예제와 같이 <code>Accept</code> 요청 헤더와 컨트롤러 메서드가 생성하는 콘텐츠 유형 목록을 기반으로 요청 매핑 범위를 좁힐 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span>path <span class="token operator">=</span> <span class="token string">"/pets/{petId}"</span><span class="token punctuation">,</span> produces <span class="token operator">=</span> <span class="token string">"application/json"</span><span class="token punctuation">)</span>  <span class="token comment">// (1)</span>
<span class="token annotation punctuation">@ResponseBody</span>
<span class="token keyword">public</span> <span class="token class-name">Pet</span> <span class="token function">getPet</span><span class="token punctuation">(</span><span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">String</span> petId<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) 생산 속성을 사용하여 콘텐츠 유형별로 매핑 범위를 좁힙니다.</p>
<p>미디어 유형은 문자 집합을 지정할 수 있습니다. 부정 표현식이 지원됩니다. 예를 들어 <code>!text/plain</code>은 "text/plain" 이외의 모든 콘텐츠 유형을 의미합니다.</p>
<p>클래스 수준에서 공유 <code>produces</code> 속성을 선언할 수 있습니다. 그러나 대부분의 다른 요청 매핑 속성과 달리 클래스 수준에서 사용될 때 메서드 수준은 클래스 수준 선언을 확장하는 대신 <code>produces</code> 속성 재정의를 생성합니다.</p>
<blockquote>
<p><strong>[Tip]</strong><br>
<code>MediaType</code>은 <code>APPLICATION_JSON_VALUE</code> 및 <code>APPLICATION_XML_VALUE</code>와 같이 일반적으로 사용되는 미디어 유형에 대한 상수를 제공합니다.</p>
</blockquote>
<h1 id="parameters-headers">Parameters, headers</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-requestmapping.html#webflux-ann-requestmapping-params-and-headers">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>요청 매개변수 조건을 기반으로 요청 매핑의 범위를 좁힐 수 있습니다. 요청 매개변수가 있는지(<code>myParam</code>), 존재하지 않는지(<code>!myParam</code>) 또는 특정 값(<code>myParam=myValue</code>)을 테스트할 수 있습니다. 다음 예에서는 특정 값을 테스트하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span>path <span class="token operator">=</span> <span class="token string">"/pets/{petId}"</span><span class="token punctuation">,</span> params <span class="token operator">=</span> <span class="token string">"myParam=myValue"</span><span class="token punctuation">)</span>  <span class="token comment">// (1)</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">findPet</span><span class="token punctuation">(</span><span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">String</span> petId<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) <code>myParam</code>이 <code>myValue</code>와 같은지 테스트합니다. </p>
<p>다음 예에 표시된 것처럼 요청 헤더 조건에도 동일한 내용을 사용할 수도 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span>path <span class="token operator">=</span> <span class="token string">"/pets/{petId}"</span><span class="token punctuation">,</span> headers <span class="token operator">=</span> <span class="token string">"myHeader=myValue"</span><span class="token punctuation">)</span> <span class="token comment">// (1)</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">findPet</span><span class="token punctuation">(</span><span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">String</span> petId<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) <code>myHeader</code>가 <code>myValue</code>와 같은지 테스트합니다.</p>
<blockquote>
<p><strong>[Tip]</strong><br>
<code>Content-Type</code> 및 <code>Accept</code>를 헤더 조건과 일치시킬 수 있지만 대신 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-requestmapping.html#mvc-ann-requestmapping-consumes">소비</a> 및 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-requestmapping.html#mvc-ann-requestmapping-produces">생산</a>을 사용하는 것이 더 좋습니다.</p>
</blockquote>
<h1 id="http-head-options">HTTP HEAD, OPTIONS</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-requestmapping.html#webflux-ann-requestmapping-head-options">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p><code>@GetMapping</code>(및 <code>@RequestMapping(method=HttpMethod.GET)</code>)은 요청 매핑을 위해 HTTP HEAD를 투명하게 지원합니다. 컨트롤러 메서드는 변경할 필요가 없습니다. <code>jakarta.servlet.http.HttpServlet</code>에 적용된 응답 래퍼는 <code>Content-Length</code> 헤더가 작성된 바이트 수로 설정되도록 합니다(실제로 응답에 쓰지 않고).</p>
<p>기본적으로 HTTP OPTIONS는 URL 패턴이 일치하는 모든 <code>@RequestMapping</code> 메소드에 나열된 HTTP 메소드 목록에 <code>Allow</code> 응답 헤더를 설정하여 처리됩니다.</p>
<p>HTTP 메소드 선언이 없는 <code>@RequestMapping</code>의 경우 <code>Allow</code> 헤더는 <code>GET,HEAD,POST,PUT,PATCH,DELETE,OPTIONS</code>로 설정됩니다. 컨트롤러 메서드는 항상 지원되는 HTTP 메서드를 선언해야 합니다(예: <code>@GetMapping</code>, <code>@PostMapping</code> 등의 HTTP 메서드별 변형을 사용하여).</p>
<p><code>@RequestMapping</code> 메소드를 HTTP HEAD 및 HTTP OPTIONS에 명시적으로 매핑할 수 있지만 일반적인 경우에는 이것이 필요하지 않습니다.</p>
<h1 id="custom-annotations">Custom Annotations</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-requestmapping.html#mvc-ann-requestmapping-head-options">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>Spring MVC는 요청 매핑을 위해 <a href="https://docs.spring.io/spring-framework/reference/core/beans/classpath-scanning.html#beans-meta-annotations">composed annotation</a>의 사용을 지원합니다. 이는 <code>@RequestMapping</code>으로 메타 annotation을 달고 더 좁고 구체적인 목적으로 <code>@RequestMapping</code> 속성의 하위 집합(또는 전체)을 다시 선언하도록 구성된 annotation입니다.</p>
<p><code>@GetMapping</code>, <code>@PostMapping</code>, <code>@PutMapping</code>, <code>@DeleteMapping</code> 및 <code>@PatchMapping</code>은 구성된 주석의 예입니다. 이는 대부분의 컨트롤러 메서드가 기본적으로 모든 HTTP 메서드와 일치하는 <code>@RequestMapping</code>을 사용하는 대신 특정 HTTP 메서드에 매핑되어야 하기 때문에 제공됩니다. composed annotation을 구현하는 방법에 대한 예가 필요한 경우 해당 annotation이 선언된 방법을 살펴보세요.</p>
<blockquote>
<p><strong>[Note]</strong><br>
<code>@RequestMapping</code>은 동일한 요소(클래스, 인터페이스 또는 메소드)에 선언된 다른 <code>@RequestMapping</code> annotation과 함께 사용할 수 없습니다. 동일한 요소에서 여러 <code>@RequestMapping</code> annotation이 감지되면 경고가 기록되고 첫 번째 매핑만 사용됩니다. 이는 <code>@GetMapping</code>, <code>@PostMapping</code> 등과 같이 구성된 <code>@RequestMapping</code> annotation에도 적용됩니다.</p>
</blockquote>
<p>Spring MVC는 또한 사용자 정의 request-matching 논리를 사용하여 사용자 정의 reques-mapping 속성을 지원합니다. 이는 <code>RequestMappingHandlerMapping</code>을 서브클래싱하고 <code>getCustomMethodCondition</code> 메소드를 재정의해야 하는 고급 옵션입니다. 여기서 사용자 정의 속성을 확인하고 자체 <code>RequestCondition</code>을 반환할 수 있습니다.</p>
<h1 id="explicit-registrations">Explicit Registrations</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-requestmapping.html#webflux-ann-requestmapping-registration">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>동적 등록이나 고급 사례(예: 다른 URL 아래에 있는 동일한 처리기의 다른 인스턴스)에 사용할 수 있는 처리기 메서드를 프로그래밍 방식으로 등록할 수 있습니다. 다음 예제에서는 핸들러 메서드를 등록합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setHandlerMapping</span><span class="token punctuation">(</span><span class="token class-name">RequestMappingHandlerMapping</span> mapping<span class="token punctuation">,</span> <span class="token class-name">UserHandler</span> handler<span class="token punctuation">)</span> <span class="token comment">// (1)</span>
			<span class="token keyword">throws</span> <span class="token class-name">NoSuchMethodException</span> <span class="token punctuation">{</span>

		<span class="token class-name">RequestMappingInfo</span> info <span class="token operator">=</span> <span class="token class-name">RequestMappingInfo</span>
				<span class="token punctuation">.</span><span class="token function">paths</span><span class="token punctuation">(</span><span class="token string">"/user/{id}"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">methods</span><span class="token punctuation">(</span><span class="token class-name">RequestMethod</span><span class="token punctuation">.</span>GET<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// (2)</span>

		<span class="token class-name">Method</span> method <span class="token operator">=</span> <span class="token class-name">UserHandler</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">.</span><span class="token function">getMethod</span><span class="token punctuation">(</span><span class="token string">"getUser"</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// (3)</span>

		mapping<span class="token punctuation">.</span><span class="token function">registerMapping</span><span class="token punctuation">(</span>info<span class="token punctuation">,</span> handler<span class="token punctuation">,</span> method<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// (4)</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) 컨트롤러에 대한 대상 핸들러와 핸들러 매핑을 삽입합니다.<br>
(2) 요청 매핑 메타데이터를 준비합니다.<br>
(3) 핸들러 메서드를 가져옵니다.<br>
(4) 등록을 추가합니다.</p>
<h1 id="httpexchange"><code>@HttpExchange</code></h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-requestmapping.html#webflux-ann-httpexchange-annotation">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p><code>@HttpExchange</code>의 주요 목적은 생성된 프록시로 HTTP 클라이언트 코드를 추상화하는 것이지만, 이러한 주석이 배치되는 <a href="https://docs.spring.io/spring-framework/reference/integration/rest-clients.html#rest-http-interface">HTTP 인터페이스</a>는 클라이언트와 서버 사용에 중립적인 계약입니다. 클라이언트 코드를 단순화하는 것 외에도 HTTP 인터페이스가 서버가 클라이언트 액세스를 위해 API를 노출하는 편리한 방법일 수 있는 경우도 있습니다. 이로 인해 클라이언트와 서버 간의 결합이 증가하고 특히 공개 API의 경우 좋은 선택이 아니지만 내부 API의 경우 정확히 목표일 수 있습니다. 이는 Spring Cloud에서 일반적으로 사용되는 접근 방식이며 컨트롤러 클래스에서 서버 측 처리를 위해 <code>@RequestMapping</code> 대신 <code>@HttpExchange</code>가 지원되는 이유입니다.</p>
<p>For example:</p>
<pre><code class="language-java"><span class="token annotation punctuation">@HttpExchange</span><span class="token punctuation">(</span><span class="token string">"/persons"</span><span class="token punctuation">)</span>
<span class="token keyword">interface</span> <span class="token class-name">PersonService</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@GetExchange</span><span class="token punctuation">(</span><span class="token string">"/{id}"</span><span class="token punctuation">)</span>
	<span class="token class-name">Person</span> <span class="token function">getPerson</span><span class="token punctuation">(</span><span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">Long</span> id<span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token annotation punctuation">@PostExchange</span>
	<span class="token keyword">void</span> <span class="token function">add</span><span class="token punctuation">(</span><span class="token annotation punctuation">@RequestBody</span> <span class="token class-name">Person</span> person<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@RestController</span>
<span class="token keyword">class</span> <span class="token class-name">PersonController</span> <span class="token keyword">implements</span> <span class="token class-name">PersonService</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token class-name">Person</span> <span class="token function">getPerson</span><span class="token punctuation">(</span><span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">Long</span> id<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@ResponseStatus</span><span class="token punctuation">(</span><span class="token class-name">HttpStatus</span><span class="token punctuation">.</span>CREATED<span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">add</span><span class="token punctuation">(</span><span class="token annotation punctuation">@RequestBody</span> <span class="token class-name">Person</span> person<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>@HttpExchange</code>와 <code>@RequestMapping</code>에는 차이점이 있습니다. <code>@RequestMapping</code>은 경로 패턴, HTTP 메소드 등을 통해 원하는 수의 요청에 매핑할 수 있는 반면, <code>@HttpExchange</code>는 구체적인 HTTP 메소드, 경로 및 콘텐츠 유형을 사용하여 단일 엔드포인트를 선언합니다.</p>
<p>메소드 매개변수 및 반환 값의 경우 일반적으로 <code>@HttpExchange</code>는 <code>@RequestMapping</code>이 수행하는 메소드 매개변수의 하위 집합을 지원합니다. 특히 서버측 특정 매개변수 유형은 제외됩니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/integration/rest-clients.html#rest-http-interface-method-parameters">@HttpExchange</a> 및 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/arguments.html">@RequestMapping</a> 목록을 참조하세요.</p>