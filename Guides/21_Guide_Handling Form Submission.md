<p>이 가이드는 Spring을 사용하여 웹 양식을 생성하고 제출하는 과정을 안내합니다.</p>
<h2 id="무엇을-구축할-것인가">무엇을 구축할 것인가</h2>
<p>이 가이드에서는 다음 URL에서 액세스할 수 있는 웹 양식을 작성합니다: <code>http://localhost:8080/greeting</code></p>
<p>브라우저에서 이 페이지를 보면 양식이 표시됩니다. <code>id</code> 및 <code>content</code> 양식 필드를 채워 인사말을 제출할 수 있습니다. 양식이 제출되면 결과 페이지가 표시됩니다.</p>
<h2 id="starting-with-spring-initializr">Starting with Spring Initializr</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/02927942-fb4c-4f8a-9dfb-4f4de2cd4667/image.png"></p>
<h2 id="create-a-web-controller">Create a Web Controller</h2>
<p>웹 사이트 구축에 대한 Spring의 접근 방식에서 HTTP 요청은 컨트롤러에 의해 처리됩니다. 이러한 구성 요소는 <code>@Controller</code> 주석으로 쉽게 식별됩니다. 다음 목록(<code>src/main/java/guides/handlingformsubmission/GreetingController.java</code>)의 <code>GreetingController</code>는 뷰 이름(이 경우 <code>greeting</code>)을 반환하여 <code>/greeting</code>에 대한 GET 요청을 처리합니다. 다음 <code>View</code>는 HTML 콘텐츠 렌더링을 담당합니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>handlingformsubmission</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype</span><span class="token punctuation">.</span><span class="token class-name">Controller</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>ui</span><span class="token punctuation">.</span><span class="token class-name">Model</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">GetMapping</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">ModelAttribute</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">PostMapping</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Controller</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">GreetingController</span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/greeting"</span><span class="token punctuation">)</span>
  <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">greetingForm</span><span class="token punctuation">(</span><span class="token class-name">Model</span> model<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    model<span class="token punctuation">.</span><span class="token function">addAttribute</span><span class="token punctuation">(</span><span class="token string">"greeting"</span><span class="token punctuation">,</span> <span class="token keyword">new</span> <span class="token class-name">Greeting</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">return</span> <span class="token string">"greeting"</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

  <span class="token annotation punctuation">@PostMapping</span><span class="token punctuation">(</span><span class="token string">"/greeting"</span><span class="token punctuation">)</span>
  <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">greetingSubmit</span><span class="token punctuation">(</span><span class="token annotation punctuation">@ModelAttribute</span> <span class="token class-name">Greeting</span> greeting<span class="token punctuation">,</span> <span class="token class-name">Model</span> model<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    model<span class="token punctuation">.</span><span class="token function">addAttribute</span><span class="token punctuation">(</span><span class="token string">"greeting"</span><span class="token punctuation">,</span> greeting<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">return</span> <span class="token string">"result"</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p>이 컨트롤러는 간결하고 단순하지만 많은 일이 일어나고 있습니다. 이 섹션의 나머지 부분에서는 이를 단계별로 분석합니다.</p>
<p>매핑 주석을 사용하면 HTTP 요청을 특정 컨트롤러 메서드에 매핑할 수 있습니다. 이 컨트롤러의 두 가지 메서드는 모두 <code>/greeting</code>에 매핑됩니다. <code>@RequestMapping</code>(기본적으로 <code>GET</code>, <code>POST</code> 등과 같은 모든 HTTP 작업을 매핑함)을 사용할 수 있습니다. 그러나 이 경우 <code>greetingForm()</code> 메서드는 <code>@GetMapping</code>을 사용하여 구체적으로 <code>GET</code>에 매핑되는 반면 <code>greetingSubmit()</code>은 <code>@PostMapping</code>을 사용하여 <code>POST</code>에 매핑됩니다. 이 매핑을 통해 컨트롤러는 <code>/greeting</code> 엔드포인트에 대한 요청을 구별할 수 있습니다.</p>
<hr>
<p><code>@ModelAttribute</code> 어노테이션은 여러 가지 기능을 수행하지만, 여기서 주로 사용되는 역할은 다음과 같습니다:</p>
<ol>
<li>
<p><strong>요청 매개변수 바인딩:</strong></p>
<ul>
<li>클라이언트로부터 전송된 HTTP 요청의 파라미터 값을 해당 모델 클래스에 바인딩하는 역할을 합니다.</li>
<li>예를 들어, <code>greetingSubmit</code> 메서드의 <code>@ModelAttribute Greeting greeting</code>은 HTTP 요청의 파라미터를 <code>Greeting</code> 객체에 자동으로 매핑합니다.</li>
</ul>
</li>
<li>
<p><strong>모델에 데이터 추가:</strong></p>
<ul>
<li>컨트롤러의 메서드에서 <code>@ModelAttribute</code>가 적용된 파라미터가 있을 경우, 해당 객체를 자동으로 모델에 추가합니다.</li>
<li>위의 코드에서 <code>greetingForm</code> 메서드에서 <code>model.addAttribute("greeting", new Greeting())</code>와 같이 모델에 "greeting" 속성을 추가했던 부분과 연결됩니다.</li>
</ul>
</li>
<li>
<p><strong>자동 객체 생성:</strong></p>
<ul>
<li>해당 클래스의 객체를 자동으로 생성하고 초기화합니다. </li>
<li><code>greetingForm</code> 메서드에서 <code>new Greeting()</code>을 생성하여 <code>greeting</code>이라는 이름으로 모델에 추가할 때 사용되었습니다.</li>
</ul>
</li>
<li>
<p><strong>폼 데이터 바인딩:</strong></p>
<ul>
<li>HTML 폼에서 전송된 데이터를 해당 객체에 바인딩하여 컨트롤러 메서드에 전달합니다.</li>
<li><code>greetingSubmit</code> 메서드의 <code>@ModelAttribute Greeting greeting</code>은 HTTP POST 요청에서 전송된 데이터를 <code>Greeting</code> 객체에 자동으로 매핑합니다.</li>
</ul>
</li>
</ol>
<p>즉, <code>@ModelAttribute</code>는 HTTP 요청을 처리하고 컨트롤러에 전달된 데이터를 객체로 바인딩하거나 모델에 데이터를 추가하는 데 사용됩니다. 이를 통해 컨트롤러에서 사용되는 객체를 자동으로 초기화하고 데이터를 전달할 수 있습니다.</p>
<hr>
<p><code>greetingForm()</code> 메서드는 <code>Model</code> 개체를 사용하여 보기 템플릿에 새 인사말을 노출합니다. 다음 코드(src/main/java/guides/handlingformsubmission/Greeting.java)의 <code>Greeting</code> 개체에는 인사말 보기의 양식 필드에 해당하는 <code>id</code> 및 <code>content</code>와 같은 필드가 포함되어 있으며 다음에서 정보를 캡처하는 데 사용됩니다. </p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>handlingformsubmission</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Greeting</span> <span class="token punctuation">{</span>
    
    <span class="token keyword">private</span> <span class="token keyword">long</span> id<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> content<span class="token punctuation">;</span>
    
    <span class="token keyword">public</span> <span class="token keyword">long</span> <span class="token function">getId</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> id<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setId</span><span class="token punctuation">(</span><span class="token keyword">long</span> id<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>id <span class="token operator">=</span> id<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getContent</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> content<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setContent</span><span class="token punctuation">(</span><span class="token class-name">String</span> content<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>content <span class="token operator">=</span> content<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>메소드 본문의 구현은 뷰 이름(<code>greeting</code>)을 렌더링할 템플릿으로 변환하여 HTML의 서버측 렌더링을 수행하는 뷰 기술에 의존합니다. 이 경우 <code>greeting.html</code> 템플릿을 구문 분석하고 다양한 템플릿 표현식을 평가하여 양식을 렌더링하는 Thymeleaf를 사용합니다. 다음 목록(src/main/resources/templates/greeting.html)은 인사말 템플릿을 보여줍니다.</p>
<pre><code class="language-html"><span class="token doctype">&lt;!DOCTYPE HTML&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>html</span> <span class="token attr-name"><span class="token namespace">xmlns:</span>th</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>https://www.thymeleaf.org<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>head</span><span class="token punctuation">&gt;</span></span> 
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>title</span><span class="token punctuation">&gt;</span></span>Getting Started: Handling Form Submission<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>title</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>meta</span> <span class="token attr-name">http-equiv</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>Content-Type<span class="token punctuation">"</span></span> <span class="token attr-name">content</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>text/html; charset=UTF-8<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>head</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>body</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>h1</span><span class="token punctuation">&gt;</span></span>Form<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>h1</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>form</span> <span class="token attr-name">action</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>#<span class="token punctuation">"</span></span> <span class="token attr-name"><span class="token namespace">th:</span>action</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>@{/greeting}<span class="token punctuation">"</span></span> <span class="token attr-name"><span class="token namespace">th:</span>object</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${greeting}<span class="token punctuation">"</span></span> <span class="token attr-name">method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>post<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
    	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>p</span><span class="token punctuation">&gt;</span></span>Id: <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>input</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>text<span class="token punctuation">"</span></span> <span class="token attr-name"><span class="token namespace">th:</span>field</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>*{id}<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>p</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>p</span><span class="token punctuation">&gt;</span></span>Message: <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>input</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>text<span class="token punctuation">"</span></span> <span class="token attr-name"><span class="token namespace">th:</span>field</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>*{content}<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>p</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>p</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>input</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>submit<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>Submit<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span> <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>input</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>reset<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>Reset<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>p</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>form</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>body</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>html</span><span class="token punctuation">&gt;</span></span></code></pre>
<p><code>th:action="@{/greeting}"</code> 표현식은 양식을 <code>/greeting</code> 엔드포인트에 대한 POST로 지시하는 반면, <code>th:object="${greeting}"</code> 표현식은 양식 데이터를 수집하는 데 사용할 모델 객체를 선언합니다. <code>th:field="{id}"</code> 및 <code>th:field="{content}"</code>로 표현되는 두 개의 양식 필드는 <code>Greeting</code> 개체의 필드에 해당합니다.</p>
<p>여기에는 양식을 표시하기 위한 컨트롤러, 모델 및 뷰가 포함됩니다. 이제 양식 제출 프로세스를 검토할 수 있습니다. 앞에서 설명한 대로 양식은 <code>POST</code> 호출을 사용하여 <code>/greeting</code> 엔드포인트에 제출됩니다. <code>GreetingSubmit()</code> 메서드는 양식으로 채워진 <code>Greeting</code> 개체를 받습니다. <code>Greeting</code>은 <code>@ModelAttribute</code>이므로 수신 양식 콘텐츠에 바인딩됩니다. 또한 제출된 데이터는 이름(기본적으로 메서드 매개 변수의 이름이므로 이 경우 <code>greeting</code>)을 참조하여 <code>result</code> 보기에서 렌더링될 수 있습니다. <code>id</code>는 <code>&lt;p th:text="'id: ' + ${greeting.id}" /&gt;</code> 표현식에서 렌더링됩니다. 마찬가지로 <code>content</code>는 <code>&lt;p th:text="'content: ' + ${greeting.content}" /&gt;</code> 표현식으로 렌더링됩니다. 다음 목록(src/main/resources/templates/result.html)은 결과 템플릿을 보여줍니다.</p>
<pre><code class="language-html"><span class="token doctype">&lt;!DOCTYPE HTML&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>html</span> <span class="token attr-name"><span class="token namespace">xmlns:</span>th</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>https://www.thymeleaf.org<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>head</span><span class="token punctuation">&gt;</span></span> 
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>title</span><span class="token punctuation">&gt;</span></span>Getting Started: Handling Form Submission<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>title</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>meta</span> <span class="token attr-name">http-equiv</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>Content-Type<span class="token punctuation">"</span></span> <span class="token attr-name">content</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>text/html; charset=UTF-8<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>head</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>body</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>h1</span><span class="token punctuation">&gt;</span></span>Result<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>h1</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>p</span> <span class="token attr-name"><span class="token namespace">th:</span>text</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span><span class="token punctuation">'</span>id: ' + ${greeting.id}<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>p</span> <span class="token attr-name"><span class="token namespace">th:</span>text</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span><span class="token punctuation">'</span>content: ' + ${greeting.content}<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>a</span> <span class="token attr-name">href</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>/greeting<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>Submit another message<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>a</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>body</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>html</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>명확성을 위해 이 예에서는 양식을 렌더링하고 제출된 데이터를 표시하기 위해 두 개의 seperate view 템플릿을 사용합니다. 그러나 두 가지 목적 모두에 single view 를 사용할 수 있습니다.</p>
<h2 id="애플리케이션을-실행-가능하게-만들기">애플리케이션을 실행 가능하게 만들기</h2>
<p>외부 애플리케이션 서버에 배포하기 위해 이 서비스를 기존 WAR 파일로 패키징할 수 있지만 더 간단한 접근 방식은 독립 실행형 애플리케이션을 만드는 것입니다. 좋은 오래된 Java <code>main()</code> 메소드에 의해 구동되는 단일 실행 가능한 JAR 파일에 모든 것을 패키지합니다. 그 과정에서 외부 인스턴스에 배포하는 대신 Tomcat 서블릿 컨테이너를 HTTP 런타임으로 내장하기 위한 Spring의 지원을 사용합니다. 다음 목록(src/main/java/guides/handlingformsubmission/HandlingFormSubmissionApplication.java)은 애플리케이션 클래스를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>handlingformsubmission</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">HandlingFormSubmissionApplication</span> <span class="token punctuation">{</span>

  <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">HandlingFormSubmissionApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<h2 id="test-the-service">Test the service</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/8e2c7d00-a62b-41be-a1e8-8e65445edbb9/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/2b573017-c10b-4123-bbce-9c71d3b44d23/image.png"></p>