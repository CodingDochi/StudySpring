<p>이 가이드는 응답에 CORS(Cross-Origin Resource Sharing)용 헤더를 포함하는 Spring을 사용하여 "Hello, World" RESTful 웹 서비스를 생성하는 과정을 안내합니다. 이 블로그 게시물에서 Spring CORS 지원에 대한 자세한 내용을 확인할 수 있습니다.</p>
<h2 id="cors">CORS</h2>
<h3 id="cross-domain-환경이란">cross-domain 환경이란?</h3>
<p>Cross-domain 환경은 웹에서 다른 도메인 또는 서브도메인, 포트, 프로토콜 등과 같은 다른 출처(origin)를 가진 리소스에 접근하는 상황을 가리킵니다. 웹에서는 보안 상의 이유로 동일 출처 정책(Same-Origin Policy)이라는 보안 메커니즘이 적용되어 있어, 동일 출처에 속하는 리소스만 서로 상호작용할 수 있습니다.</p>
<p>Cross-domain 환경에서는 이 정책을 극복하여 서로 다른 출처에 있는 리소스 간 상호작용이 가능하도록 해야 합니다. 이를 위해 Cross-Origin Resource Sharing (CORS)와 같은 기술이 사용되며, 서버 측에서 헤더를 설정하여 다른 출처의 리소스 접근을 허용하게 됩니다.</p>
<p>예를 들어, 웹 사이트 A에서 웹 사이트 B의 API에 AJAX를 통해 데이터를 요청하는 경우, 이는 cross-domain 상황에 해당합니다. 동일 출처 정책에 따르면 웹 사이트 A의 JavaScript는 웹 사이트 B의 리소스를 직접 요청할 수 없지만, CORS를 통해 서버가 특정 출처에 대한 접근을 허용한다면, 웹 사이트 A는 웹 사이트 B의 API를 사용할 수 있게 됩니다.</p>
<h3 id="origin과-cors-에러">origin과 cors 에러</h3>
<p><img src="https://velog.velcdn.com/images/wjdwl002/post/0955e8bf-45d5-4e82-8a97-d4084a173a5d/image.png"></p>
<p>Origin은 Protocol, Host, Port 까지를 의미한다.</p>
<p><img src="https://velog.velcdn.com/images/wjdwl002/post/ec1b3f3b-3dac-4334-9a15-4235ac104ea8/image.png"></p>
<p>CORS가 발생하는 예시는 다음과 같은 상황이다.</p>
<ul>
<li>백엔드는 EC2로 배포해놓고 프론트는 로컬에서 작업하는 경우</li>
<li>백엔드와 프론트를 다른 url로 배포하는 경우</li>
</ul>
<p>위의 에러는 웹개발을 하다보면 정말 자주 마주하는 에러기때문에 종종 사람들이 CORS 자체가 에러의 유형이라고 생각하기도 하는데, 정확히 말하면 CORS 자체는 “브라우저 정책”이고, 이 정책에 위반하면 has been blocked 에러가 나는것이다. </p>
<h3 id="pre-flight">pre-flight</h3>
<p><img src="https://velog.velcdn.com/images/garcon/post/f56638ae-33e8-4c61-bd6c-a7133d26a784/image.png"></p>
<p>프리플라이트(preflight)는 브라우저가 실제 요청을 보내기 전에 먼저 보내는 요청입니다. 실제 요청을 보내기 전에 서버가 미리 확인하고 허용 여부를 결정하는 프로세스입니다. 이것은 주로 CORS(Cross-Origin Resource Sharing) 요청에서 사용됩니다. 클라이언트가 보낼 실제 요청을 전송하기 전에 브라우저가 OPTIONS 메서드를 사용하여 프리플라이트 요청을 보내고, 서버는 이 요청을 분석하여 요청을 수락할지 거부할지 결정합니다. 이것은 클라이언트가 서버에 실제 요청을 보내기 전에 사전에 서버가 요청을 받아들일 수 있는지 확인하는 것을 의미합니다.</p>
<h3 id="cors-protocol">CORS Protocol</h3>
<p><img src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/ca/Flowchart_showing_Simple_and_Preflight_XHR.svg/1280px-Flowchart_showing_Simple_and_Preflight_XHR.svg.png"></p>
<p>CORS 프로토콜은 응답을 교차 출처로 공유하고 HTML의 폼 요소로는 불가능한 더 유연한 페치(fetch)를 허용하기 위해 존재합니다. 이는 HTTP 위에 층을 이루며 응답이 다른 출처와 공유될 수 있음을 선언할 수 있게 합니다.</p>
<p>방화벽(내부망) 뒤 응답 데이터의 유출을 방지하기 위해 선택적인 메커니즘이어야 합니다. 또한 자격 증명을 포함하는 요청의 경우 민감한 데이터 유출을 방지하기 위해 선택적이어야 합니다.</p>
<p>이 섹션은 서버 개발자에게 관련된 CORS 프로토콜을 설명합니다. 사용자 에이전트에 대한 요구 사항은 페치 알고리즘의 일부이며, 새 HTTP 헤더 구문을 제외한 모든 것입니다.</p>
<h4 id="general">General</h4>
<p>CORS 프로토콜은 응답이 교차 출처에서 공유될 수 있는지를 나타내는 헤더 세트로 구성됩니다.</p>
<p>HTML의 폼 요소로는 할 수 없는 요청에 대해서는 CORS 사전 요청이 수행되어 현재 URL이 CORS 프로토콜을 지원하는지 확인합니다.</p>
<h4 id="http-요청">HTTP 요청</h4>
<p>CORS 요청은 <code>Origin</code> 헤더를 포함하는 HTTP 요청입니다. 이는 <code>GET</code> 또는 <code>HEAD</code>가 아닌 메서드에 대해서도 <code>Origin</code> 헤더가 포함되므로 신뢰할 수 있는 CORS 프로토콜 참여자로 식별할 수 없습니다.</p>
<p>CORS 사전 요청은 CORS 프로토콜을 이해하는지 확인하는 CORS 요청으로, 요청의 현재 URL이 CORS 프로토콜을 지원하는지 확인합니다.</p>
<h4 id="http-응답">HTTP 응답</h4>
<p>CORS 요청에 대한 HTTP 응답은 다음 헤더를 포함할 수 있습니다:</p>
<ul>
<li><code>Access-Control-Allow-Origin</code>: 응답이 공유될 수 있는지 여부를 나타냅니다.</li>
<li><code>Access-Control-Allow-Credentials</code>: 요청의 자격 증명 모드가 "include"인 경우 응답이 공유될 수 있는지 여부를 나타냅니다.</li>
</ul>
<p>CORS 사전 요청에 대한 HTTP 응답은 다음 헤더를 포함할 수 있습니다:</p>
<ul>
<li><code>Access-Control-Allow-Methods</code>: CORS 프로토콜의 목적으로 응답의 URL에서 지원되는 메서드를 나타냅니다.</li>
<li><code>Access-Control-Allow-Headers</code>: CORS 프로토콜의 목적으로 응답의 URL에서 지원되는 헤더를 나타냅니다.</li>
</ul>
<p>HTTP 응답은 성공적일 때 적절한 헤더를 포함하는 것으로 충분합니다. CORS 사전 요청에 대한 성공적인 HTTP 응답은 ok 상태로 제한되며, 200 또는 204와 같은 상태가 됩니다. 그 밖의 HTTP 응답은 성공적이지 않으며 공유되지 않거나 CORS 사전 요청에 실패할 수 있습니다. 그러나 서버가 수행하는 작업은 타이밍과 같은 사이드 채널을 통해 유출될 수 있습니다. 서버 개발자가 명시적으로 이를 나타내고자 하는 경우 403 상태를 사용할 수 있습니다.</p>
<p>경우에 따라 "실패"도 공유되기를 원할 수 있지만, 이는 성공적인 HTTP 응답으로 만들어지게 됩니다. 따라서 CORS 사전 요청이 아닌 CORS 요청에 대한 성공적인 HTTP 응답의 경우 상태는 403를 포함하여 어떤 것이든 될 수 있습니다. 서버 개발자는 HTTP 응답을 다루는 방법에 많은 자유를 가지고 있으며, 이러한 전략은 CORS 사전 요청에 대한 응답과 그 뒤따르는 CORS 요청 간에 다를 수 있습니다. 동적 응답을 제공할 수도 있고, 이는 특정 출처에 맞게 응답을 조정하거나 응답이 특정 출처에 대해 자격 증명이 있고 성공적일 수 있도록 할 수 있습니다.</p>
<h3 id="corsconfiguration-클래스">CorsConfiguration 클래스</h3>
<p>주어진 요청의 실제 출처, HTTP 메소드 및 헤더를 확인하는 메소드와 함께 CORS 구성을 위한 컨테이너입니다.<br>
기본적으로 새로 생성된 CorsConfiguration은 원본(cross-origin) 간 요청을 허용하지 않으며 허용되는 항목을 나타내도록 명시적으로 구성해야 합니다. <code>applyPermitDefaultValues()</code>를 사용하여 <code>GET</code>, <code>HEAD</code> 및 <code>POST</code> 요청에 대한 모든 교차 출처 요청을 허용하는 개방형 기본값으로 시작하도록 초기화 모델을 전환합니다.</p>
<h4 id="cors-spec">CORS SPEC</h4>
<p><a href="https://fetch.spec.whatwg.org/#cors-protocol">https://fetch.spec.whatwg.org/#cors-protocol</a></p>
<h4 id="setallowedoriginsnullable-liststring-origins"><code>setAllowedOrigins(@Nullable List&lt;String&gt; origins)</code></h4>
<p>교차 출처 요청이 허용되는 출처 목록으로, 각 값은 다음 중 하나일 수 있습니다.</p>
<ul>
<li>특정 도메인(예: <code>"https://domain1.com"</code></li>
<li>특정 도메인의 쉼표로 구분된 목록(예: <code>"https://a1.com,https://a2.com";</code> 이는 속성 자리 표시자를 통해 값을 확인할 때 편리합니다.  </li>
<li><code>"${origin}";</code> 이러한 자리 표시자는 외부에서 해결되어야 합니다.</li>
<li>CORS는 모든 출처에 대해 특수 값 "*"을 정의했습니다.</li>
</ul>
<p>일치하는 사전 요청(pre-flight)과 실제 요청(actual-request)의 경우 <code>Access-Control-Allow-Origin</code> 응답 헤더는 일치하는 도메인 값 또는 "<code>*</code>"로 설정됩니다. 그러나 CORS 사양은 <code>AllowCredentials</code>가 <code>true</code>로 설정된 경우 "<code>*</code>"를 허용하지 않으며 5.3부터 <code>allowedOriginPatterns</code>를 대신 사용하기 위해 해당 조합이 거부된다는 점을 명심하세요.<br>
기본적으로 이는 설정되지 않습니다. 이는 원본이 허용되지 않음을 의미합니다. 그러나 이 클래스의 인스턴스는 종종 추가로 초기화됩니다. <code>@CrossOrigin</code>의 경우 <code>applyPermitDefaultValues()</code>를 통해.</p>
<h4 id="applypermitdefaultvalues">applyPermitDefaultValues()</h4>
<p>기본적으로 <code>CorsConfiguration</code>은 원본 간 요청을 허용하지 않으며 명시적으로 구성해야 합니다. 이 방법을 사용하면 <code>GET</code>, <code>HEAD</code> 및 <code>POST</code>에 대한 모든 원본 간 요청을 허용하지만 이미 설정된 값을 재정의하지 않는 기본값으로 전환할 수 있습니다.<br>
설정되지 않은 값에는 다음 기본값이 적용됩니다.</p>
<ul>
<li>CORS 사양에 정의된 특수 값 "<code>*</code>"을 사용하는 모든 원본을 허용합니다. 이는 <code>Origins</code>나 <code>OriginPatterns</code>가 모두 설정되지 않은 경우에만 설정됩니다.</li>
<li>"간단한" 메소드 <code>GET</code>, <code>HEAD</code> 및 <code>POST</code>를 허용합니다.</li>
<li>모든 헤더를 허용합니다.</li>
<li>최대 기간을 1800초(30분)로 설정합니다.</li>
</ul>
<h3 id="corsregistration-클래스">CorsRegistration 클래스</h3>
<p>특정 URL 경로 패턴에 대한 <code>CorsConfiguration</code> 인스턴스 생성을 지원합니다.</p>
<h4 id="addallowedoriginsstring-origins">addAllowedOrigins(String... origins)</h4>
<p>브라우저에서 교차 출처 요청을 허용하는 출처(origins)를 설정합니다. 형식 세부 정보 및 기타 고려 사항은 <code>CorsConfiguration.setAllowedOrigins(List)</code>를 참조하세요.<br>
기본적으로 모든 원본이 허용되지만 <code>allowedOriginPatterns</code>도 설정된 경우 그것이 우선 적용됩니다.</p>
<h3 id="corsregistry-인터페이스">CorsRegistry 인터페이스</h3>
<p>글로벌 URL 패턴 기반 <code>CorsConfiguration</code> 매핑 등록을 지원합니다.</p>
<h4 id="addmappingstring-pathpattern">addMapping(String pathPattern)</h4>
<p>지정된 경로 패턴에 대해 교차 출처 요청 처리를 활성화합니다.<br>
정확한 경로 매핑 URI(예: "<code>/admin</code>")는 물론 Ant 스타일 경로 패턴(예: "<code>/admin/**</code>")도 지원됩니다.<br>
기본적으로 이 매핑에 대한 <code>CorsConfiguration</code>은 <code>CorsConfiguration.applyPermitDefaultValues()</code>에 설명된 대로 기본값으로 초기화됩니다.</p>
<h3 id="webmvcconfigurer-인터페이스">WebMvcConfigurer 인터페이스</h3>
<p><code>@EnableWebMvc</code>를 통해 활성화된 Spring MVC에 대한 Java 기반 구성을 사용자 정의하기 위한 콜백 메서드를 정의합니다.<br>
<code>@EnableWebMvc</code> 주석이 달린 구성 클래스는 이 인터페이스를 구현하여 콜백하고 기본 구성을 사용자 정의할 수 있는 기회를 제공할 수 있습니다.</p>
<h4 id="addcorsmappingscorsregistry-registry">addCorsMappings(CorsRegistry registry)</h4>
<p>"글로벌" 교차 출처 요청 처리를 구성합니다. 구성된 CORS 매핑은 주석이 달린 컨트롤러, 기능 엔드포인트 및 정적 리소스에 적용됩니다.<br>
주석이 달린 컨트롤러는 <code>@CrossOrigin</code>을 통해 더욱 세분화된 구성을 추가로 선언할 수 있습니다. 이러한 경우 여기에 선언된 "전역(global)" CORS 구성은 컨트롤러 메서드에 정의된 로컬 CORS 구성과 결합됩니다.<br>
부터:</p>
<h2 id="cors와-ldap">CORS와 LDAP</h2>
<p>CORS와 LDAP는 접근 권한 관리 측면에서 다른 역할을 합니다.</p>
<p>CORS는 웹 브라우저에서 실행되며, 다른 출처의 리소스에 대한 접근 권한을 부여하거나 제한합니다. 주로 웹 애플리케이션 간의 자원 공유를 관리하고, 브라우저 수준에서 동작하여 클라이언트 측에서 제어됩니다.</p>
<p>반면에 LDAP은 Lightweight Directory Access Protocol의 약자로, 주로 사용자 인증 및 디렉터리 서비스에 대한 액세스를 관리합니다. 보통은 서버 측에서 동작하며, 사용자 계정, 권한 및 리소스의 중앙 집중 관리를 가능하게 합니다.</p>
<p>공통점으로는 두 기술 모두 접근 권한을 관리하며, 특정 자원에 대한 접근을 허용하거나 거부할 수 있다는 점입니다. 하지만 CORS는 클라이언트 측에서 웹 애플리케이션 간의 자원 공유를 제어하고, LDAP은 서버 측에서 사용자 정보와 권한을 관리합니다.</p>
<h2 id="what-you-will-build">What You Will Build</h2>
<p>다음 목록과 같이 <code>http://localhost:8080/greeting</code>에서 HTTP GET 요청을 수락하고 인사말을 JSON으로 표현하여 응답하는 서비스를 구축합니다.</p>
<pre><code class="language-json">{"id":1,"content":"Hello, World!"}</code></pre>
<p>다음 목록에 표시된 것처럼 쿼리 문자열에 선택적 <code>name</code> 매개 변수를 사용하여 인사말을 사용자 지정할 수 있습니다.</p>
<pre><code class="language-shell">http://localhost:8080/greeting?name<span class="token operator">=</span>User</code></pre>
<p><code>name</code> 매개변수 값은 <code>World</code>의 기본값을 재정의하고 다음 목록에 표시된 대로 응답에 반영됩니다.</p>
<pre><code class="language-json">{"id":1,"content":"Hello, User!"}</code></pre>
<p>이 서비스는 Spring Framework CORS 지원을 사용하여 관련 CORS 응답 헤더를 추가한다는 점에서 RESTful 웹 서비스 구축에 설명된 서비스와 약간 다릅니다.</p>
<h2 id="starting-with-spring-initializr">Starting with Spring Initializr</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/cc72fb5c-417e-4911-85d0-b2145a251536/image.png"></p>
<h3 id="adding-the-httpclient-dependency">Adding the 'httpclient' Dependency</h3>
<pre><code class="language-null">plugins {
    id 'java'
    id 'org.springframework.boot' version '3.2.0'
    id 'io.spring.dependency-management' version '1.1.4'
}

group = 'guides'
version = '0.0.1-SNAPSHOT'

java {
    sourceCompatibility = '17'
}

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    testImplementation 'org.apache.httpcomponents:httpclient'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

tasks.named('test') {
    useJUnitPlatform()
}</code></pre>
<h2 id="리소스-표현-클래스-생성">리소스 표현 클래스 생성</h2>
<p>이제 프로젝트와 빌드 시스템을 설정했으므로 웹 서비스를 생성할 수 있습니다.</p>
<p>서비스 상호 작용에 대해 생각하여 프로세스를 시작하십시오.</p>
<p>서비스는 선택적으로 쿼리 문자열에 이름 매개변수를 사용하여 <code>/greeting</code>에 대한 <code>GET</code> 요청을 처리합니다. <code>GET</code> 요청은 인사말을 나타내기 위해 본문에 JSON이 포함된 <code>200 OK</code> 응답을 반환해야 합니다. 다음 목록과 유사해야 합니다.</p>
<pre><code class="language-json">{
    "id": 1,
    "content": "Hello, World!"
}</code></pre>
<p><code>id</code> 필드는 인사말의 고유 식별자이고 <code>content</code>는 인사말의 텍스트 표현입니다.</p>
<p>인사말 표현을 모델링하려면 리소스 표현 클래스를 만듭니다. 다음 목록(src/main/java/guides/restservicecors/Greeting.java에서)에 표시된 대로 <code>id</code> 및 <code>content</code> 데이터에 대한 필드, 생성자 및 접근자가 포함된 일반 기존 Java 개체를 제공합니다.</p>
<pre><code class="language-java">
<span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>restservicecors</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Greeting</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token keyword">long</span> id<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">String</span> content<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">Greeting</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>id <span class="token operator">=</span> <span class="token operator">-</span><span class="token number">1</span><span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>content <span class="token operator">=</span> <span class="token string">""</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token class-name">Greeting</span><span class="token punctuation">(</span><span class="token keyword">long</span> id<span class="token punctuation">,</span> <span class="token class-name">String</span> content<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>id <span class="token operator">=</span> id<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>content <span class="token operator">=</span> content<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">long</span> <span class="token function">getId</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> id<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getContent</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> content<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p>Spring은 Jackson JSON 라이브러리를 사용하여 <code>Greeting</code> 유형의 인스턴스를 JSON으로 자동 마샬링합니다.</p>
</blockquote>
<h2 id="리소스-컨트롤러-생성">리소스 컨트롤러 생성</h2>
<p>RESTful 웹 서비스를 구축하는 Spring의 접근 방식에서 HTTP 요청은 컨트롤러에 의해 처리됩니다. 이러한 구성 요소는 <code>@Controller</code> 주석으로 쉽게 식별되며 다음 목록(src/main/java/guides/restservicecors/GreetingController.java)에 표시된 <code>GreetingController</code>는 <code>Greeting</code> 클래스의 새 인스턴스를 반환하여 <code>/greeting</code>에 대한 <code>GET</code> 요청을 처리합니다. </p>
<pre><code class="language-java"></code></pre>
<p>이 컨트롤러는 간결하고 단순하지만 그 내부에는 많은 일이 벌어지고 있습니다. 우리는 그것을 단계별로 분해합니다.</p>
<p><code>@RequestMapping</code> 주석은 <code>/greeting</code>에 대한 HTTP 요청이 <code>greeting()</code> 메소드에 매핑되도록 보장합니다.</p>
<blockquote>
<p>이전 예제에서는 <code>@RequestMapping(method = RequestMethod.GET)</code>에 대한 바로가기 역할을 하는 <code>@GetMapping</code> 주석을 사용합니다. 이 경우 테스트에 편리하기 때문에 <code>GET</code>을 사용합니다. Spring은 원본이 CORS 구성과 일치하지 않는 GET 요청을 계속 거부합니다. 브라우저는 CORS 실행 전 요청을 보낼 필요가 없지만, pre-flight check을 트리거하려면 <code>@PostMapping</code>을 사용하고 본문에서 일부 JSON을 허용할 수 있습니다.</p>
</blockquote>
<p><code>@RequestParam</code>은 <code>name</code> 쿼리 문자열 매개변수의 값을 <code>greeting()</code> 메서드의 <code>name</code> 매개변수에 바인딩합니다. 이 쿼리 문자열 매개변수는 does not <code>required</code>. 요청에 없으면 <code>defaultValue</code>인 <code>World</code>가 사용됩니다.</p>
<p>메소드 본문의 구현은 <code>counter</code>의 다음 값을 기반으로 하는 <code>id</code> 속성 값과 쿼리 매개변수 또는 기본값을 기반으로 하는 <code>content</code> 값을 사용하여 새 <code>Greeting</code> 객체를 생성하고 반환합니다. 또한 인사말 <code>template</code>을 사용하여 지정된 <code>name</code>의 형식을 지정합니다.</p>
<p>기존 MVC 컨트롤러와 앞서 표시된 RESTful 웹 서비스 컨트롤러 간의 주요 차이점은 HTTP 응답 본문이 생성되는 방식입니다. 인사말 데이터를 HTML로 서버측 렌더링하기 위해 뷰 기술을 사용하는 대신 이 RESTful 웹 서비스 컨트롤러는 <code>Greeting</code> 개체를 채우고 반환합니다. 객체 데이터는 JSON으로 HTTP 응답에 직접 기록됩니다.</p>
<p>이를 달성하기 위해 <code>@RestController</code> 주석은 모든 메서드가 기본적으로 <code>@ResponseBody</code> 의미 체계(semantics)를 상속한다고 가정합니다. 따라서 반환된 개체 데이터는 응답 본문에 직접 삽입됩니다.</p>
<p>Spring의 HTTP 메시지 변환기 지원 덕분에 <code>Greeting</code> 객체는 자연스럽게 JSON으로 변환됩니다. Jackson이 classpath에 있기 때문에 <code>Greeting</code> 인스턴스를 JSON으로 변환하기 위해 Spring의 <code>MappingJackson2HttpMessageConverter</code>가 자동으로 선택됩니다.</p>
<h2 id="enabling-cors">Enabling CORS</h2>
<p>개별 컨트롤러에서 또는 전역적으로 CORS(교차 원본 리소스 공유)를 활성화할 수 있습니다. 다음 주제에서는 이를 수행하는 방법을 설명합니다.</p>
<h4 id="controller-method-cors-configuration">Controller Method CORS Configuration</h4>
<p>RESTful 웹 서비스의 응답에 CORS 액세스 제어 헤더가 포함되도록 하려면 다음 목록(src/main/java/guides/restservicecors/GreetingController.java에서)과 같이 handler 메서드에 <code>@CrossOrigin</code> 주석을 추가해야 합니다. )는 다음을 보여줍니다:</p>
<pre><code class="language-java">	<span class="token annotation punctuation">@CrossOrigin</span><span class="token punctuation">(</span>origins <span class="token operator">=</span> <span class="token string">"http://localhost:8080"</span><span class="token punctuation">)</span>
	<span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/greeting"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">Greeting</span> <span class="token function">greeting</span><span class="token punctuation">(</span><span class="token annotation punctuation">@RequestParam</span><span class="token punctuation">(</span>required <span class="token operator">=</span> <span class="token boolean">false</span><span class="token punctuation">,</span> defaultValue <span class="token operator">=</span> <span class="token string">"World"</span><span class="token punctuation">)</span> <span class="token class-name">String</span> name<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"==== get greeting ===="</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">Greeting</span><span class="token punctuation">(</span>counter<span class="token punctuation">.</span><span class="token function">incrementAndGet</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token class-name">String</span><span class="token punctuation">.</span><span class="token function">format</span><span class="token punctuation">(</span>template<span class="token punctuation">,</span> name<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>이 <code>@CrossOrigin</code> 주석은 이 특정 메서드에 대해서만 교차 출처 리소스 공유를 활성화합니다. 기본적으로 <code>@RequestMapping</code> 주석에 지정된 모든 원본, 모든 헤더 및 HTTP 메서드를 허용합니다. 또한 <code>maxAge</code>는 30분이 사용됩니다. 다음 주석 속성 중 하나의 값을 지정하여 이 동작을 사용자 정의할 수 있습니다.</p>
<ul>
<li><code>origins</code></li>
<li><code>methods</code></li>
<li><code>allowedHeaders</code></li>
<li><code>exposedHeaders</code></li>
<li><code>allowCredentials</code></li>
<li><code>maxAge</code></li>
</ul>
<p>이 예에서는 <code>http://localhost:8080</code>만 교차 출처 요청을 보낼 수 있도록 허용합니다.</p>
<blockquote>
<p>또한 컨트롤러 클래스 수준에서 <code>@CrossOrigin</code> 주석을 추가하여 이 클래스의 모든 핸들러 메서드에서 CORS를 활성화할 수도 있습니다.</p>
</blockquote>
<h2 id="글로벌-cors-구성">글로벌 CORS 구성</h2>
<p>세분화된(fine-grained) 주석 기반 구성(annotation-based configuration)에 추가로(또는 대안으로) 일부 전역 CORS 구성도 정의할 수 있습니다. 이는 <code>Filter</code>를 사용하는 것과 비슷하지만 Spring MVC 내에서 선언할 수 있고 세분화된 <code>@CrossOrigin</code> 구성과 결합할 수 있습니다. 기본적으로 모든 원본과 <code>GET</code>, <code>HEAD</code> 및 <code>POST</code> 메서드가 허용됩니다.</p>
<p>다음 목록(src/main/java/guides/restservicecors/GreetingController.java)은 <code>GreetingController</code> 클래스의 <code>greetingWithJavaconfig</code> 메소드를 보여줍니다.</p>
<pre><code class="language-java">	<span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/greeting-javaconfig"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">Greeting</span> <span class="token function">greetingWithJavaconfig</span><span class="token punctuation">(</span><span class="token annotation punctuation">@RequestParam</span><span class="token punctuation">(</span>required <span class="token operator">=</span> <span class="token boolean">false</span><span class="token punctuation">,</span> defaultValue <span class="token operator">=</span> <span class="token string">"World"</span><span class="token punctuation">)</span> <span class="token class-name">String</span> name<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"==== in greeting ===="</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">Greeting</span><span class="token punctuation">(</span>counter<span class="token punctuation">.</span><span class="token function">incrementAndGet</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token class-name">String</span><span class="token punctuation">.</span><span class="token function">format</span><span class="token punctuation">(</span>template<span class="token punctuation">,</span> name<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<blockquote>
<p><code>greetingWithJavaconfig</code> 메소드와 <code>greeting</code> 메소드(컨트롤러 수준 CORS 구성에 사용됨)의 차이점은 경로(<code>/greeting</code>이 아닌 <code>/greeting-javaconfig</code>)와 <code>@CrossOrigin</code> 원본이 있다는 것입니다.</p>
</blockquote>
<p>다음 목록(src/main/java/guides/restservicecors/RestServiceCorsApplication.java)은 애플리케이션 클래스에 CORS 매핑을 추가하는 방법을 보여줍니다.</p>
<pre><code class="language-java">	<span class="token keyword">public</span> <span class="token class-name">WebMvcConfigurer</span> <span class="token function">corsConfigurer</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">WebMvcConfigurer</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
			<span class="token annotation punctuation">@Override</span>
			<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">addCorsMappings</span><span class="token punctuation">(</span><span class="token class-name">CorsRegistry</span> registry<span class="token punctuation">)</span> <span class="token punctuation">{</span>
				registry<span class="token punctuation">.</span><span class="token function">addMapping</span><span class="token punctuation">(</span><span class="token string">"/greeting-javaconfig"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">allowedOrigins</span><span class="token punctuation">(</span><span class="token string">"http://localhost:8080"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			<span class="token punctuation">}</span>
		<span class="token punctuation">}</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span></code></pre>
<p>모든 속성(예: 예제의 <code>allowedOrigins</code>)을 쉽게 변경할 수 있을 뿐만 아니라 이 CORS 구성을 특정 경로 패턴에 적용할 수도 있습니다.</p>
<blockquote>
<p>전역 및 컨트롤러 수준 CORS 구성을 결합할 수 있습니다.</p>
</blockquote>
<h2 id="애플리케이션-클래스-생성">애플리케이션 클래스 생성</h2>
<p>Spring Initializr는 기본 애플리케이션 클래스를 생성합니다. 원본 간 리소스 공유를 처리하는 방법을 구성하는 방법을 추가해야 합니다. 다음 목록(/src/main/java/guides/restservicecors/RestServiceCorsApplication.java)은 이를 수행하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>restservicecors</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Bean</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>config<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">CorsRegistry</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>config<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">WebMvcConfigurer</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">RestServiceCorsApplication</span> <span class="token punctuation">{</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">RestServiceCorsApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Bean</span>
    <span class="token keyword">public</span> <span class="token class-name">WebMvcConfigurer</span> <span class="token function">corsConfigurer</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">WebMvcConfigurer</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>

            <span class="token annotation punctuation">@Override</span>
            <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">addCorsMappings</span><span class="token punctuation">(</span><span class="token class-name">CorsRegistry</span> registry<span class="token punctuation">)</span> <span class="token punctuation">{</span>
                registry<span class="token punctuation">.</span><span class="token function">addMapping</span><span class="token punctuation">(</span><span class="token string">"/greeting-javaconfig"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">allowedOrigins</span><span class="token punctuation">(</span><span class="token string">"http://localhost:8080"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
        <span class="token punctuation">}</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p>익명 클래스는 이름이 없는 클래스입니다. 클래스를 생성하면서 동시에 인스턴스를 만들어내는데, 주로 한 번만 사용되는 간단한 클래스를 정의할 때 유용합니다. </p>
<p>일반적으로 인터페이스나 추상 클래스를 상속하거나 메서드를 오버라이드할 때 익명 클래스가 사용됩니다. 요청에 따라 클래스를 정의하고 인스턴스화하여 사용할 수 있어 편리하고 코드를 간결하게 작성할 수 있습니다.</p>
<p>위의 코드에서 <code>corsConfigurer()</code> 메서드는 <code>WebMvcConfigurer</code>를 구현하는 익명 클래스를 반환합니다.  이 익명 클래스는 <code>addCorsMappings()</code> 메서드를 오버라이드하여 <code>/greeting-javaconfig</code> 엔드포인트에 대한 CORS 설정을 정의합니다.</p>
<hr>
<p>네, 당신이 말씀하시는 것이 맞습니다. <code>addCorsMappings</code> 메서드는 <code>WebMvcConfigurer</code>의 메서드로, 이를 구현한 익명 클래스 내에서 오버라이드되어 새로운 <code>WebMvcConfigurer</code> 객체를 생성하고 있습니다. 하지만 이 코드 조각 자체는 그 메서드를 직접적으로 호출하고 있지 않습니다. </p>
<p>이 코드는 스프링 컨텍스트에 <code>WebMvcConfigurer</code> 빈을 등록하는 방식으로 CORS 설정을 적용하는데, 실제로 CORS가 적용되려면 스프링이 이 설정을 확인하고 적용해야 합니다. 스프링은 이 빈을 인식하고 해당 설정을 찾아 애플리케이션에 적용합니다.</p>
<p>즉, <code>addCorsMappings</code> 메서드는 스프링이 호출하는 것이며, 이 설정은 스프링 애플리케이션 실행 시 동작합니다. 설정은 스프링이 해당 빈을 찾고 메서드를 호출하여 CORS 정책을 적용합니다.</p>
<hr>
<p>일반적으로 Java에서는 인터페이스를 직접적으로 <code>new</code> 키워드를 사용하여 인스턴스화할 수는 없습니다. 인터페이스는 추상적인 개념이며, 직접적으로 객체로 생성될 수 없습니다. 그러나 익명 클래스를 이용하면 인터페이스의 메서드를 구현하여 즉석에서 인스턴스를 생성할 수 있습니다. </p>
<p>익명 클래스를 사용하면 인터페이스나 추상 클래스의 인스턴스를 생성하면서 필요한 메서드를 구현할 수 있습니다. 따라서 인터페이스를 구현하는 클래스를 생성하고자 할 때 익명 클래스를 활용할 수 있습니다. 이렇게 생성된 익명 클래스의 인스턴스는 해당 인터페이스의 기능을 가지고 있습니다.</p>
<p>일반적으로 Spring에서는 <code>WebMvcConfigurer</code>를 구현하거나 익명 클래스로 오버라이드하여 CORS 설정을 추가하는 방식을 사용합니다. 그러나 이러한 설정을 <code>@Bean</code>으로 직접 노출할 필요는 없습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">CorsConfig</span> <span class="token keyword">implements</span> <span class="token class-name">WebMvcConfigurer</span> <span class="token punctuation">{</span>
    
    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">WebMvcConfigurer</span> configurer <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">WebMvcConfigurer</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token annotation punctuation">@Override</span>
        <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">addCorsMappings</span><span class="token punctuation">(</span><span class="token class-name">CorsRegistry</span> registry<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            registry<span class="token punctuation">.</span><span class="token function">addMapping</span><span class="token punctuation">(</span><span class="token string">"/greeting-javaconfig"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">allowedOrigins</span><span class="token punctuation">(</span><span class="token string">"http://localhost:8080"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span><span class="token punctuation">;</span>
    
    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">addCorsMappings</span><span class="token punctuation">(</span><span class="token class-name">CorsRegistry</span> registry<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        configurer<span class="token punctuation">.</span><span class="token function">addCorsMappings</span><span class="token punctuation">(</span>registry<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이렇게 하면 <code>CorsConfig</code> 클래스가 <code>WebMvcConfigurer</code>를 구현하면서 CORS 설정을 추가할 수 있습니다. 하지만 이렇게 해도 최종적으로는 Spring 애플리케이션 컨텍스트에 이 설정이 등록되어야 하며, 이를 위해 <code>@Configuration</code> 어노테이션이 필요합니다.</p>
<p><code>@Bean</code>으로 설정된 메서드를 통해 바로 빈으로 등록하는 방식은 더 직관적이고 보통의 사용법입니다. 그러나 위와 같이 변수로 설정하고, 그 안에서 익명 클래스로 오버라이드하여 사용하는 것도 가능합니다.</p>
<hr>
<p><code>@CrossOrigin(origins = "http://localhost:8080")</code>과 함께 사용하는 것과 단일 <code>@GetMapping("/greeting-javaconfig")</code>만 사용하는 것의 차이는 주로 CORS(Cross-Origin Resource Sharing) 설정과 관련이 있습니다.</p>
<ol>
<li><strong>@CrossOrigin(origins = "<a href="http://localhost:8080%22">http://localhost:8080"</a>) 사용:</strong><ul>
<li>해당 메서드 (<code>/greeting</code>)에 대한 CORS 설정이 적용됩니다.</li>
<li>허용된 오리진은 "<a href="http://localhost:8080%22%EC%9D%B4%EB%A9%B0">http://localhost:8080"이며</a>, 이 도메인에서의 요청만 허용됩니다.</li>
</ul></li>
</ol>
<pre><code class="language-java"><span class="token annotation punctuation">@CrossOrigin</span><span class="token punctuation">(</span>origins <span class="token operator">=</span> <span class="token string">"http://localhost:8080"</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/greeting"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token class-name">Greeting</span> <span class="token function">greeting</span><span class="token punctuation">(</span><span class="token annotation punctuation">@RequestParam</span><span class="token punctuation">(</span>required <span class="token operator">=</span> <span class="token boolean">false</span><span class="token punctuation">,</span> defaultValue <span class="token operator">=</span> <span class="token string">"World"</span><span class="token punctuation">)</span> <span class="token class-name">String</span> name<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"==== get greeting ===="</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">Greeting</span><span class="token punctuation">(</span>counter<span class="token punctuation">.</span><span class="token function">incrementAndGet</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token class-name">String</span><span class="token punctuation">.</span><span class="token function">format</span><span class="token punctuation">(</span>template<span class="token punctuation">,</span> name<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<ol start="2">
<li><strong>@GetMapping("/greeting-javaconfig")만 사용:</strong><ul>
<li>별도의 CORS 설정이 없으므로, 브라우저의 Same-Origin Policy에 따라 동일한 오리진에서만 해당 엔드포인트에 대한 요청이 가능합니다.</li>
</ul></li>
</ol>
<pre><code class="language-java"><span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/greeting-javaconfig"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token class-name">Greeting</span> <span class="token function">greetingWithJavaconfig</span><span class="token punctuation">(</span><span class="token annotation punctuation">@RequestParam</span><span class="token punctuation">(</span>required <span class="token operator">=</span> <span class="token boolean">false</span><span class="token punctuation">,</span> defaultValue <span class="token operator">=</span> <span class="token string">"World"</span><span class="token punctuation">)</span> <span class="token class-name">String</span> name<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"==== in greeting ===="</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">Greeting</span><span class="token punctuation">(</span>counter<span class="token punctuation">.</span><span class="token function">incrementAndGet</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token class-name">String</span><span class="token punctuation">.</span><span class="token function">format</span><span class="token punctuation">(</span>template<span class="token punctuation">,</span> name<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>일반적으로 CORS 설정을 사용하면, 다른 도메인에서의 요청도 허용할 수 있습니다. 따라서, <code>@CrossOrigin</code>을 사용하는 경우 더 유연한 CORS 정책을 설정할 수 있습니다.</p>
<hr>
<p>네, 맞습니다! <code>@GetMapping("/greeting-javaconfig")</code>만 사용하더라도 별도의 CORS 설정을 하지 않고 있다면 브라우저는 Same-Origin Policy를 적용하게 됩니다. </p>
<p>하지만 코드에서와 같이 <code>RestServiceCorsApplication</code> 클래스 내에서 <code>@Bean</code>으로 <code>WebMvcConfigurer</code>를 설정해 <code>addCorsMappings</code> 메서드를 오버라이드하고 있으므로, 해당 경로 (<code>/greeting-javaconfig</code>)에 대한 CORS 정책이 <code>http://localhost:8080</code>에서의 요청을 허용하도록 구성됩니다. </p>
<p>따라서, 이 설정을 통해 <code>/greeting-javaconfig</code>에 대한 CORS 제약 조건을 추가할 수 있게 됩니다.</p>
<h2 id="test-the-service">Test the service</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/c812d4d5-f400-4abf-b725-ca806fcb888a/image.png"></p>
<p>이제 서비스가 실행되었으므로 브라우저에서 <code>http://localhost:8080/greeting</code>을 방문하면 다음이 표시됩니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/706dbee8-5132-412a-ae99-9c628511d406/image.png"></p>
<p><code>http://localhost:8080/greeting?name=User</code>를 방문하여 <code>name</code> 쿼리 문자열 매개변수를 제공하세요. <code>content</code> 속성의 값이 <code>Hello, World!</code>에서 변경되었습니다. <code>Hello User!</code>에게 다음 목록과 같이 표시합니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/ddff5b13-497c-49d9-86e9-b8371fe10c83/image.png"></p>
<p>이 변경 사항은 <code>GreetingController</code>의 <code>@RequestParam</code> 배열이 예상대로 작동함을 보여줍니다. <code>name</code> 매개변수에는 기본값인 <code>World</code>가 지정되었지만 항상 쿼리 문자열을 통해 명시적으로 재정의될 수 있습니다.</p>
<p>또한 <code>id</code> 속성이 <code>1</code>에서 <code>2</code>로 변경되었습니다. 이는 여러 요청에서 동일한 <code>GreetingController</code> 인스턴스에 대해 작업하고 있으며 해당 <code>counter</code> 필드가 예상대로 각 호출에서 증가하고 있음을 증명합니다.</p>
<p>이제 CORS 헤더가 제자리에 있는지 테스트하고 다른 원본의 Javascript 클라이언트가 서비스에 액세스하도록 허용할 수 있습니다. 이렇게 하려면 서비스를 사용할 Javascript 클라이언트를 생성해야 합니다. 다음 목록은 그러한 클라이언트를 보여줍니다.</p>
<p>먼저 다음 내용을 포함하는 <code>hello.js</code>(/public/hello.js에서)라는 간단한 Javascript 파일을 만듭니다.</p>
<pre><code class="language-js"><span class="token function">$</span><span class="token punctuation">(</span>document<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">ready</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    $<span class="token punctuation">.</span><span class="token function">ajax</span><span class="token punctuation">(</span><span class="token punctuation">{</span>
        url<span class="token punctuation">:</span> <span class="token string">"http://localhost:8080/greeting"</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">then</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span><span class="token parameter">data<span class="token punctuation">,</span> status<span class="token punctuation">,</span> jqxhr</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
       <span class="token function">$</span><span class="token punctuation">(</span><span class="token string">'.greeting-id'</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">append</span><span class="token punctuation">(</span>data<span class="token punctuation">.</span>id<span class="token punctuation">)</span><span class="token punctuation">;</span>
       <span class="token function">$</span><span class="token punctuation">(</span><span class="token string">'.greeting-content'</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">append</span><span class="token punctuation">(</span>data<span class="token punctuation">.</span>content<span class="token punctuation">)</span><span class="token punctuation">;</span>
       console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span>jqxhr<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>이 스크립트는 jQuery를 사용하여 <code>http://localhost:8080/greeting</code>에서 REST 서비스를 사용합니다. 이는 다음 목록(/public/index.html의)과 같이 <code>index.html</code>에 의해 로드됩니다.</p>
<pre><code class="language-html"><span class="token doctype">&lt;!DOCTYPE html&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>html</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>head</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>title</span><span class="token punctuation">&gt;</span></span>Hello CORS<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>title</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>script</span> <span class="token attr-name">src</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>https://ajax.googleapis.com/ajax/libs/jquery/1.10.2/jquery.min.js<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span><span class="token script"></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>script</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>script</span> <span class="token attr-name">src</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>hello.js<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span><span class="token script"></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>script</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>head</span><span class="token punctuation">&gt;</span></span>

    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>body</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>div</span><span class="token punctuation">&gt;</span></span>
            <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>p</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>greeting-id<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>The ID is <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>p</span><span class="token punctuation">&gt;</span></span>
            <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>p</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>greeting-content<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>The content is <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>p</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>div</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>body</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>html</span><span class="token punctuation">&gt;</span></span></code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/95f26b57-d0b5-4169-9ef5-935a960c3111/image.png"></p>
<p>앱이 시작되면 브라우저에서 <code>http://localhost:8080</code>을 열면 다음이 표시됩니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/ea36e82d-4e1c-483a-aac0-94f7e22fb74d/image.png"></p>
<p>CORS 동작을 테스트하려면 다른 서버나 포트에서 클라이언트를 시작해야 합니다. 이렇게 하면 두 애플리케이션 간의 충돌을 피할 수 있을 뿐만 아니라 클라이언트 코드가 서비스와 다른 출처에서 제공되도록 보장할 수 있습니다. 포트 9000(이미 포트 8080에서 실행 중인 앱 포함)의 localhost에서 실행되는 앱을 시작하려면 다음 gradle 명령을 실행하세요.</p>
<pre><code class="language-null">./gradlew bootRun --args="--server.port=9000"</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/b498ec14-ee5c-4dcf-b00d-2e1528054071/image.png"></p>
<p>앱이 시작되면 브라우저에서 <a href="http://localhost:9000%EC%9D%84">http://localhost:9000을</a> 열면 다음이 표시됩니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/c974bda6-da8e-4721-a647-ba9a1bd57d8b/image.png"></p>
<p>서비스 응답에 CORS 헤더가 포함되어 있으면 ID와 콘텐츠가 페이지에 렌더링됩니다. 그러나 CORS 헤더가 누락되거나 클라이언트에 충분하지 않은 경우 브라우저는 요청에 실패하고 값이 DOM에 렌더링되지 않습니다.</p>
<hr>
<p>CORS는 브라우저에서 다른 출처(Origin)로부터의 리소스 요청에 대한 보안 정책입니다. 이 정책에 따라 서버가 브라우저에게 리소스에 대한 접근을 허용하거나 거부할 수 있습니다. 때때로 브라우저에서 리소스에 접근하는 방식(JavaScript, CSS, 이미지 등)에 영향을 미칠 수 있지만, 해당 리소스를 어디에 배치했느냐는 CORS와 직접적인 연관이 없습니다.</p>
<p>정적 리소스에 대한 우선 순위는 Spring Boot의 기본 설정에 따라 정해지며, 이 설정은 기본적으로 내부적으로 지정되어 있습니다. 일반적으로 <code>/static</code>, <code>/public</code>, <code>/resources</code>, <code>/META-INF/resources</code> 디렉터리들은 동일한 우선 순위를 가지며, Spring Boot가 정적 리소스를 찾을 때 이 디렉터리들을 우선적으로 확인합니다.</p>
<p>따라서 <code>public</code> 디렉터리가 <code>resources</code> 디렉터리 내부에 있지 않더라도, Spring Boot는 정적 리소스를 찾을 때 <code>public</code> 디렉터리를 동일한 우선 순위로 처리합니다. 이는 Spring Boot의 자동 구성 기능에 의해 결정되는 것이며, 설정을 변경하거나 추가적으로 조정할 수 있습니다.</p>