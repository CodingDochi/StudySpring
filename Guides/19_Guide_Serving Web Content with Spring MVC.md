<p>이 가이드는 Spring을 사용하여 "Hello, World" 웹 사이트를 만드는 과정을 안내합니다.</p>
<h2 id="what-you-will-build">What You Will Build</h2>
<p>정적 홈 페이지가 있고 <code>http://localhost:8080/greeting</code>에서 HTTP GET 요청도 허용하는 애플리케이션을 구축합니다.</p>
<p>HTML을 표시하는 웹 페이지로 응답합니다. HTML 본문에는 "Hello, World!"라는 인사말이 포함됩니다.</p>
<p>쿼리 문자열의 선택적 <code>name</code> 매개변수를 사용하여 인사말을 사용자 정의할 수 있습니다. 그러면 URL은 <code>http://localhost:8080/greeting?name=User</code>가 될 수 있습니다.</p>
<p><code>name</code> 매개변수 값은 <code>World</code>의 기본값을 재정의하고 내용이 "Hello, User!"로 변경되어 응답에 반영됩니다.</p>
<h2 id="starting-with-spring-initializr">Starting with Spring Initializr</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/a1183a5d-1a92-43d7-bd7a-f3b0fe3d6972/image.png"></p>
<h2 id="웹-컨트롤러-만들기">웹 컨트롤러 만들기</h2>
<p>웹 사이트 구축에 대한 Spring의 접근 방식에서 HTTP 요청은 컨트롤러에 의해 처리됩니다. <code>@Controller</code> 주석으로 컨트롤러를 쉽게 식별할 수 있습니다. 다음 예에서 <code>GreetingController</code>는 <code>View</code> 이름(이 경우 <code>greeting</code>)을 반환하여 <code>/greeting</code>에 대한 GET 요청을 처리합니다. 뷰는 HTML 콘텐츠 렌더링을 담당합니다. 다음 목록(src/main/java/guides/servingwebcontent/GreetingController.java)은 컨트롤러를 보여줍니다.</p>
<pre><code class="language-java"></code></pre>
<p>이 컨트롤러는 간결하고 단순하지만 많은 일이 진행되고 있습니다. 우리는 그것을 단계별로 분해합니다.</p>
<p><code>@GetMapping</code> 주석은 <code>/greeting</code>에 대한 HTTP GET 요청이 <code>greeting()</code> 메서드에 매핑되도록 합니다.</p>
<p><code>@RequestParam</code>은 쿼리 문자열 매개변수 <code>name</code> 값을 <code>greeting()</code> 메서드의 <code>name</code> 매개변수에 바인딩합니다. 이 쿼리 문자열 매개변수는 <code>required</code>. 요청에 없으면 <code>defaultValue</code>인 <code>World</code>가 사용됩니다. <code>name</code> 매개변수의 값은 <code>Model</code> 객체에 추가되어 궁극적으로 뷰 템플릿에 액세스할 수 있게 됩니다.</p>
<p>메소드 본문의 구현은 HTML의 서버측 렌더링을 수행하기 위해 뷰 기술(이 경우 Thymeleaf)에 의존합니다. Thymeleaf는 <code>greeting.html</code> 템플릿을 구문 분석하고 <code>th:text</code> 표현식을 평가하여 컨트롤러에 설정된 <code>${name}</code> 매개변수의 값을 렌더링합니다. 다음 목록(src/main/resources/templates/greeting.html)은 다음과 같습니다. <code>greeting.html</code> 템플릿:</p>
<pre><code class="language-html"><span class="token doctype">&lt;!DOCTYPE HTML&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>html</span> <span class="token attr-name"><span class="token namespace">xmlns:</span>th</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.thymeleaf.org<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>head</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>title</span><span class="token punctuation">&gt;</span></span>Getting Started: Serving Web Content<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>title</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>meta</span> <span class="token attr-name">http-equiv</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>Content-Type<span class="token punctuation">"</span></span> <span class="token attr-name">content</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>text/html; charset=UTF-8<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>head</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>body</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>p</span> <span class="token attr-name"><span class="token namespace">th:</span>text</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>|Hello, ${name}!|<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>body</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>html</span><span class="token punctuation">&gt;</span></span></code></pre>
<h2 id="spring-boot-devtools">Spring Boot Devtools</h2>
<p>웹 애플리케이션 개발의 일반적인 기능은 변경 사항을 코딩하고, 애플리케이션을 다시 시작하고, 브라우저를 새로 고쳐 변경 사항을 확인하는 것입니다. 이 전체 과정에는 많은 시간이 걸릴 수 있습니다. 이 새로 고침 주기 속도를 높이기 위해 Spring Boot는 <code>spring-boot-devtools</code>라는 편리한 모듈을 제공합니다. 스프링 부트 개발 도구:</p>
<ul>
<li>핫 스와핑을 활성화합니다.</li>
<li>캐싱을 비활성화하도록 템플릿 엔진을 전환합니다.</li>
<li>LiveReload를 활성화하여 브라우저를 자동으로 새로 고칩니다.</li>
<li>생산(production) 대신 개발(development)을 기반으로 한 기타 합리적인 기본값입니다.</li>
</ul>
<h2 id="애플리케이션-실행">애플리케이션 실행</h2>
<p>Spring 초기화는 당신을 위해 애플리케이션 클래스를 생성합니다. 이 경우 Spring Initializr에서 제공하는 클래스를 더 이상 수정할 필요가 없습니다. 다음 목록(src/main/java/guides/servingwebcontent/ServingWebContentApplication.java)은 애플리케이션 클래스를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>servingwebcontent</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ServingWebContentApplication</span> <span class="token punctuation">{</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">ServingWebContentApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<h2 id="test-the-application">Test the Application</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/eeb4be83-c399-44ad-8b97-ae8d89f946f5/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/cb31536e-26bb-4410-9e58-f22b48cdfdf3/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/fcbfd886-c073-49f4-8aa0-1ec88041a032/image.png"></p>
<p>이제 웹 사이트가 실행되었으므로 <code>http://localhost:8080/greeting</code>을 방문하면 "Hello, World!"가 표시됩니다.</p>
<p><code>http://localhost:8080/greeting?name=User</code>를 방문하여 이름 쿼리 문자열 매개변수를 제공하세요. "Hello, World!" 메시지가 어떻게 변경되는지 확인하세요. "Hello, User!":</p>
<p>이 변경 사항은 <code>GreetingController</code>의 <code>@RequestParam</code> 배열이 예상대로 작동함을 보여줍니다. <code>name</code> 매개변수에는 기본값인 <code>World</code>가 지정되었지만 쿼리 문자열을 통해 명시적으로 재정의할 수 있습니다.</p>
<h2 id="홈-페이지-추가">홈 페이지 추가</h2>
<p>HTML, JavaScript, CSS를 포함한 정적 리소스를 소스 코드의 올바른 위치에 놓아 Spring Boot 애플리케이션에서 제공할 수 있습니다. 기본적으로 Spring Boot는 <code>/static</code>(또는 <code>/public</code>)의 classpath에 있는 리소스의 정적 콘텐츠를 제공합니다. <code>index.html</code> 리소스는 존재하는 경우 "환영 페이지"로 사용되기 때문에 특별합니다. 즉, 루트 리소스(즉, <code>http://localhost:8080/</code>)로 제공된다는 의미입니다. 결과적으로 다음 파일을 생성해야 합니다(src/main/resources/static/index.html에서 찾을 수 있음).</p>
<pre><code class="language-html"><span class="token doctype">&lt;!DOCTYPE HTML&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>html</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>head</span><span class="token punctuation">&gt;</span></span> 
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>title</span><span class="token punctuation">&gt;</span></span>Getting Started: Serving Web Content<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>title</span><span class="token punctuation">&gt;</span></span> 
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>meta</span> <span class="token attr-name">http-equiv</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>Content-Type<span class="token punctuation">"</span></span> <span class="token attr-name">content</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>text/html; charset=UTF-8<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>head</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>body</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>p</span><span class="token punctuation">&gt;</span></span>Get your greeting <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>a</span> <span class="token attr-name">href</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>/greeting<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>here<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>a</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>p</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>body</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>html</span><span class="token punctuation">&gt;</span></span></code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/3105ecf9-6eae-460d-bee4-15b14508c954/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/a2bb0622-1fa6-402e-8a00-eacde4e366aa/image.png"></p>