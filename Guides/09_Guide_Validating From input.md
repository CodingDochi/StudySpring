<p>이 가이드는 유효성 검사를 지원하도록 웹 응용 프로그램 양식을 구성하는 과정을 안내합니다.</p>
<h2 id="what-you-will-build">What You Will Build</h2>
<p>사용자 입력을 받고 표준 유효성 검사 주석을 사용하여 입력을 확인하는 간단한 Spring MVC 애플리케이션을 구축합니다. 또한 사용자가 입력을 다시 입력하여 유효하게 만들 수 있도록 화면에 오류 메시지를 표시하는 방법도 살펴보겠습니다.</p>
<h2 id="starting-with-springinitializr">Starting With SpringInitializr</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/6e82fe29-05b7-4bc0-b473-699714f8f848/image.png"></p>
<h2 id="create-a-personform-object">Create a PersonForm Object</h2>
<p>애플리케이션에는 사용자의 이름과 나이를 확인하는 작업이 포함되므로 먼저 사람을 만드는 데 사용되는 양식을 지원하는 클래스를 만들어야 합니다. 다음 목록(src/main/java/guides/validatingforminput/PersonForm.java)에서는 이를 수행하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>validation<span class="token punctuation">.</span>constraints</span><span class="token punctuation">.</span><span class="token class-name">Min</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>validation<span class="token punctuation">.</span>constraints</span><span class="token punctuation">.</span><span class="token class-name">NotNull</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>validation<span class="token punctuation">.</span>constraints</span><span class="token punctuation">.</span><span class="token class-name">Size</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">PersonForm</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@NotNull</span>
	<span class="token annotation punctuation">@Size</span><span class="token punctuation">(</span>min<span class="token operator">=</span><span class="token number">2</span><span class="token punctuation">,</span> max<span class="token operator">=</span><span class="token number">30</span><span class="token punctuation">)</span>
	<span class="token keyword">private</span> <span class="token class-name">String</span> name<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@NotNull</span>
	<span class="token annotation punctuation">@Min</span><span class="token punctuation">(</span><span class="token number">18</span><span class="token punctuation">)</span>
	<span class="token keyword">private</span> <span class="token class-name">Integer</span> age<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>name<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setName</span><span class="token punctuation">(</span><span class="token class-name">String</span> name<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>name <span class="token operator">=</span> name<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token class-name">Integer</span> <span class="token function">getAge</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> age<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setAge</span><span class="token punctuation">(</span><span class="token class-name">Integer</span> age<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>age <span class="token operator">=</span> age<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token string">"Person(Name: "</span> <span class="token operator">+</span> <span class="token keyword">this</span><span class="token punctuation">.</span>name <span class="token operator">+</span> <span class="token string">", Age: "</span> <span class="token operator">+</span> <span class="token keyword">this</span><span class="token punctuation">.</span>age <span class="token operator">+</span> <span class="token string">")"</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>PersonForm</code> 클래스에는 <code>name</code>과 <code>age</code>라는 두 가지 속성이 있습니다. 몇 가지 표준 유효성 검사 주석으로 플래그가 지정됩니다.</p>
<ul>
<li><code>@Size(min=2, max=30)</code>: 2~30자 길이의 이름을 허용합니다.</li>
<li><code>@NotNull</code>: 항목이 비어 있는 경우 Spring MVC가 생성하는 null 값을 허용하지 않습니다.</li>
<li><code>@Min(18)</code>: 18세 미만은 허용하지 않습니다.</li>
</ul>
<p>그 외에도 <code>name</code>과 <code>age</code>에 대한 getter 및 setter와 편리한 <code>toString()</code> 메서드도 볼 수 있습니다.</p>
<h2 id="create-a-web-controller">Create a Web Controller</h2>
<p>이제 양식 지원(form-backing) 개체를 정의했으므로 간단한 웹 컨트롤러를 만들 차례입니다. 다음 목록(src/main/java/guides/validatingforminput/WebController.java)에서는 이를 수행하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype</span><span class="token punctuation">.</span><span class="token class-name">Controller</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>validation</span><span class="token punctuation">.</span><span class="token class-name">BindingResult</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">GetMapping</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">PostMapping</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>config<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">ViewControllerRegistry</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>config<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">WebMvcConfigurer</span><span class="token punctuation">;</span>


<span class="token annotation punctuation">@Controller</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebController</span> <span class="token keyword">implements</span> <span class="token class-name">WebMvcConfigurer</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">addViewControllers</span><span class="token punctuation">(</span><span class="token class-name">ViewControllerRegistry</span> registry<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		registry<span class="token punctuation">.</span><span class="token function">addViewController</span><span class="token punctuation">(</span><span class="token string">"/results"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">setViewName</span><span class="token punctuation">(</span><span class="token string">"results"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">showForm</span><span class="token punctuation">(</span><span class="token class-name">PersonForm</span> personForm<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token string">"form"</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@PostMapping</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">checkPersonInfo</span><span class="token punctuation">(</span><span class="token annotation punctuation">@Valid</span> <span class="token class-name">PersonForm</span> personForm<span class="token punctuation">,</span> <span class="token class-name">BindingResult</span> bindingResult<span class="token punctuation">)</span> <span class="token punctuation">{</span>

		<span class="token keyword">if</span> <span class="token punctuation">(</span>bindingResult<span class="token punctuation">.</span><span class="token function">hasErrors</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
			<span class="token keyword">return</span> <span class="token string">"form"</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>

		<span class="token keyword">return</span> <span class="token string">"redirect:/results"</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이 컨트롤러에는 GET 방식과 POST 방식이 있습니다. 두 방법 모두 <code>/</code>에 매핑됩니다.</p>
<p><code>showForm</code> 메소드는 <code>form</code> 템플릿을 반환합니다. 템플릿이 양식 속성을 <code>PersonForm</code>과 연결할 수 있도록 메서드 시그니처에 <code>PersonForm</code>을 포함합니다.</p>
<p><code>checkPersonInfo</code> 메소드는 두 가지 인수를 허용합니다:</p>
<ul>
<li>양식에 채워진 속성을 수집하기 위해 <code>@Valid</code>로 표시된 <code>personForm</code> 객체입니다.</li>
<li>유효성 검사 오류를 테스트하고 검색할 수 있는 <code>bindingResult</code> 개체입니다.</li>
</ul>
<p><code>PersonForm </code>객체에 바인딩된 양식에서 모든 속성을 검색할 수 있습니다. 코드에서 오류를 테스트합니다. 오류가 발생하면 사용자를 원래 <code>form</code> 서식 파일로 다시 보낼 수 있습니다. 이러한 상황에서는 모든 오류 속성이 표시됩니다.</p>
<p>개인의 속성이 모두 유효하면 브라우저를 <code>results</code> 템플릿으로 리디렉션합니다.</p>
<h2 id="build-an-html-front-end">Build an HTML Front End</h2>
<p>이제 다음 목록(src/main/resources/templates/form.html)에 표시된 대로 "<code>main</code>" 페이지를 빌드합니다.</p>
<pre><code class="language-html"><span class="token doctype">&lt;!DOCTYPE HTML&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>html</span> <span class="token attr-name"><span class="token namespace">xmlns:</span>th</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.thymeleaf.org<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>body</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>form</span> <span class="token attr-name">action</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>#<span class="token punctuation">"</span></span> <span class="token attr-name"><span class="token namespace">th:</span>action</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>@{/}<span class="token punctuation">"</span></span> <span class="token attr-name"><span class="token namespace">th:</span>object</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${personForm}<span class="token punctuation">"</span></span> <span class="token attr-name">method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>post<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>table</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>tr</span><span class="token punctuation">&gt;</span></span>
            <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>td</span><span class="token punctuation">&gt;</span></span>Name:<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>td</span><span class="token punctuation">&gt;</span></span>
            <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>td</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>input</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>text<span class="token punctuation">"</span></span> <span class="token attr-name"><span class="token namespace">th:</span>field</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>*{name}<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>td</span><span class="token punctuation">&gt;</span></span>
            <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>td</span> <span class="token attr-name"><span class="token namespace">th:</span>if</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${#fields.hasErrors('name')}<span class="token punctuation">"</span></span> <span class="token attr-name"><span class="token namespace">th:</span>errors</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>*{name}<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>Name Error<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>td</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>tr</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>tr</span><span class="token punctuation">&gt;</span></span>
            <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>td</span><span class="token punctuation">&gt;</span></span>Age:<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>td</span><span class="token punctuation">&gt;</span></span>
            <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>td</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>input</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>text<span class="token punctuation">"</span></span> <span class="token attr-name"><span class="token namespace">th:</span>field</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>*{age}<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>td</span><span class="token punctuation">&gt;</span></span>
            <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>td</span> <span class="token attr-name"><span class="token namespace">th:</span>if</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${#fields.hasErrors('age')}<span class="token punctuation">"</span></span> <span class="token attr-name"><span class="token namespace">th:</span>errors</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>*{age}<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>Age Error<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>td</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>tr</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>tr</span><span class="token punctuation">&gt;</span></span>
            <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>td</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>button</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>submit<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>Submit<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>button</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>td</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>tr</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>table</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>form</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>body</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>html</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>페이지에는 테이블의 별도 셀에 각 필드가 있는 간단한 양식이 포함되어 있습니다. 이 양식은 <code>/</code>에 게시하도록 맞춰져 있습니다. 웹 컨트롤러의 <code>GET</code> 메소드에서 본 <code>personForm</code> 객체에 의해 백업되고 있다고 표시되어 있습니다. 이것은 <strong>"bean-backed form"</strong> 로 알려져 있습니다. <code>PersonForm</code> 빈에는 두 개의 필드가 있으며 <code>th:field="*{name}"</code> 및 <code>th:field="*{age}"</code> 태그가 지정되어 있음을 확인할 수 있습니다. 각 필드 옆에는 유효성 검사 오류를 표시하는 데 사용되는 보조 요소가 있습니다.</p>
<p>마지막으로 양식을 제출하는 버튼이 있습니다. 일반적으로 사용자가 <code>@Valid</code> 제약 조건을 위반하는 이름이나 나이를 입력하면 오류 메시지가 표시되면서 이 페이지로 다시 돌아옵니다. 유효한 이름과 나이를 입력하면 사용자는 다음 웹 페이지로 이동됩니다.</p>
<p>다음 예(src/main/resources/templates/results.html)는 결과 페이지를 보여줍니다.</p>
<pre><code class="language-html"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>html</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>body</span><span class="token punctuation">&gt;</span></span>
		Congratulations! You are old enough to sign up for this site.
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>body</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>html</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>이 간단한 예에서 이러한 웹 페이지에는 정교한 CSS나 JavaScript가 없습니다.</p>
<h2 id="애플리케이션-실행">애플리케이션 실행</h2>
<p>이 애플리케이션에서는 <code>Thymeleaf</code>의 템플릿 언어를 사용하고 있습니다. 이 애플리케이션에는 원시 HTML 이상의 것이 필요합니다. Spring initializr는 당신을 위해 애플리케이션 클래스를 생성했습니다. 다음 목록(src/main/java/guides/validatingforminput/ValidatingFormInputApplication.java)은 해당 클래스를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>validatingforminput</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ValidatingFormInputApplication</span> <span class="token punctuation">{</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">ValidatingFormInputApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p>Spring MVC를 활성화하려면 일반적으로 <code>@EnableWebMvc</code>를 Application 클래스에 추가합니다. 그러나 Spring Boot의 <code>@SpringBootApplication</code>은 classpath에서 <code>spring-webmvc</code>를 감지할 때 이미 이 어노테이션을 추가합니다. 이 동일한 주석을 사용하면 주석이 달린 <code>@Controller</code> 클래스와 해당 메서드를 찾을 수 있습니다.</p>
<p><code>Thymeleaf</code> 구성도 <code>@SpringBootApplication</code>에 의해 관리됩니다. 기본적으로 템플릿은 <code>template/</code> 아래의 classpath에 있으며 파일 이름에서 '<code>.html</code>' 접미사를 제거하여 뷰로 해석됩니다. (Thymeleaf 설정은 달성해야 하는 사항에 따라 다양한 방법으로 변경하고 재정의할 수 있지만 자세한 내용은 이 가이드와 관련이 없습니다.)</p>
<p>애플리케이션은 몇 초 내에 실행되어야 합니다. <code>http://localhost:8080/</code>을 방문하면 다음 이미지와 같은 내용이 표시됩니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/26125c36-7882-4d0f-a366-73522ba9ecd7/image.png"></p>
<p>다음 이미지 쌍은 이름에 N을 입력하고 나이에 15를 입력하고 제출을 클릭하면 어떻게 되는지 보여줍니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/98562775-c359-4dd3-bff9-93087e3e1b47/image.png"></p>
<p>앞의 이미지는 값이 <code>PersonForm</code> 클래스의 제약 조건을 위반했기 때문에 "<code>main</code>" 페이지로 되돌아가는 것을 보여줍니다. 입력 상자에 아무것도 입력하지 않고 제출을 클릭하면 다음 이미지와 같이 다른 오류가 발생합니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/5b4fb089-ed77-4391-99bf-4c46482249d8/image.png"></p>
<p>유효한 이름과 나이를 입력하면 다음 이미지와 같이 <code>results</code> 페이지가 표시됩니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/0ad8cfb7-0f8f-499c-bb7c-aade1eede702/image.png"></p>