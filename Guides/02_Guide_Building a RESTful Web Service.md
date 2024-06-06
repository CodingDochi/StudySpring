<p><a href="https://spring.io/guides/gs/rest-service/">https://spring.io/guides/gs/rest-service/</a></p>
<p>이 가이드는 Spring으로 "Hello, World" RESTful 웹 서비스를 생성하는 과정을 안내합니다.</p>
<h2 id="what-you-will-build">What You Will Build</h2>
<p>이 서비스는 <code>http://localhost:8080/greeting</code> 주소에서 HTTP GET 요청을 받습니다.</p>
<p>이는 다음과 같은 형태의 JSON 인사를 반환합니다:</p>
<pre><code class="language-json">{"id":1,"content":"Hello, World!"}</code></pre>
<p>쿼리 문자열의 선택적인 <code>name</code> 매개변수로 인사말을 사용자 정의할 수 있습니다. 다음은 이에 대한 예시입니다:</p>
<p><code>http://localhost:8080/greeting?name=User</code></p>
<p><code>name</code> 매개변수 값은 기본값인 <code>World</code>를 대체하며, 다음과 같이 응답에 반영됩니다:</p>
<pre><code class="language-json">{"id":1,"content":"Hello, User!"}</code></pre>
<h2 id="starting-with-spring-initializr">Starting with Spring Initializr</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/35a6e4ab-0a09-4566-87cf-46a8e562829c/image.png"><br>
<img src="https://velog.velcdn.com/images/dev_hammy/post/1290b48d-2d45-4f94-b70e-2fb81bd105f6/image.png"></p>
<p>Spring Initializr 를 이용하여 <code>Spring Web</code>을 의존성으로 추가한 프로젝트를 생성합니다. </p>
<h3 id="buildgradle">build.gradle</h3>
<pre><code class="language-groovy">plugins {
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
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

tasks.named('test') {
    useJUnitPlatform()
}</code></pre>
<h2 id="create-a-resource-representation-class">Create a Resource Representation Class</h2>
<p>이제 프로젝트와 빌드 시스템을 설정했으니, 웹 서비스를 만들어볼 차례입니다.</p>
<p>서비스 상호작용에 대해 생각해봅시다.</p>
<p>이 서비스는 <code>/greeting</code>으로의 GET 요청을 처리하며, 선택적으로 쿼리 문자열에 <code>name</code> 매개변수를 포함할 수 있습니다. GET 요청은 본문에 JSON으로 표현된 <code>200 OK</code> 응답을 반환해야 합니다. 다음은 해당 출력의 예시입니다:</p>
<pre><code class="language-json">{
    "id": 1,
    "content": "Hello, World!"
}</code></pre>
<p>id 필드는 인사말을 식별하는 고유 식별자이며, <code>content</code>는 인사말의 텍스트 표현입니다.</p>
<p>인사말 표현을 모델링하기 위해, 리소스 표현 클래스를 생성해야 합니다. 이를 위해 <code>id</code>와 <code>content</code> 데이터에 대한 Java 레코드 클래스를 제공하세요. 다음은 (src/main/java/guides/restservice/Greeting.java에서 가져온) 예시입니다:</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>restservice</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> record <span class="token class-name">Greeting</span><span class="token punctuation">(</span><span class="token keyword">long</span> id<span class="token punctuation">,</span> <span class="token class-name">String</span> content<span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token punctuation">}</span></code></pre>
<blockquote>
<p>이 애플리케이션은 Jackson JSON 라이브러리를 사용하여 Greeting 유형의 인스턴스를 JSON으로 자동으로 변환합니다. Jackson은 웹 스타터에 기본적으로 포함되어 있습니다.</p>
</blockquote>
<h4 id="record-데이터-형식과-lombok-value의-차이">record 데이터 형식과 lombok @Value의 차이</h4>
<p><code>record</code>와 Lombok 라이브러리에서 제공하는 <code>@Value</code>는 둘 다 불변한 값(immutable value) 객체를 생성하는 데 사용됩니다.</p>
<p>차이점은 다음과 같습니다:</p>
<ol>
<li>
<p><strong>언어 차원에서의 지원:</strong></p>
<ul>
<li><code>record</code>는 Java 14부터 공식적으로 도입된 기능으로, Java 언어 자체에서 지원됩니다.</li>
<li><code>@Value</code>는 Lombok 라이브러리에서 제공하는 어노테이션으로, 컴파일 시에 Lombok이 코드를 변경하여 해당 클래스를 생성합니다.</li>
</ul>
</li>
<li>
<p><strong>코드 확장성:</strong></p>
<ul>
<li><code>record</code>는 자바 언어의 기능으로, 컴파일러가 자동으로 필드, 생성자, 접근자 메서드, <code>equals()</code>, <code>hashCode()</code>, <code>toString()</code> 등을 생성합니다.</li>
<li><code>@Value</code>는 Lombok이 제공하는 어노테이션으로, <code>@Value</code>를 사용한 클래스에 <code>equals()</code>, <code>hashCode()</code>, <code>toString()</code> 등의 메서드를 자동으로 생성합니다.</li>
</ul>
</li>
<li>
<p><strong>커스터마이징:</strong></p>
<ul>
<li><code>record</code>는 자동 생성된 메서드에 대한 커스터마이징을 제한적으로 지원합니다. 사용자가 직접 이 메서드들을 오버라이드할 수 없습니다.</li>
<li><code>@Value</code>는 Lombok의 <code>@EqualsAndHashCode</code>, <code>@ToString</code>, <code>@Getter</code> 등의 어노테이션을 통해 메서드들을 사용자가 직접 오버라이드하거나 커스터마이징할 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>의존성 및 라이브러리:</strong></p>
<ul>
<li><code>record</code>는 자바의 표준 기능으로, 별도의 라이브러리나 의존성 없이 사용 가능합니다.</li>
<li><code>@Value</code>는 Lombok 라이브러리에 의존하며, 프로젝트에 Lombok을 추가해야 사용할 수 있습니다.</li>
</ul>
</li>
</ol>
<p>기능적으로 둘 다 불변한 값 객체를 생성하는 목적은 동일하지만, Java의 표준인 <code>record</code>는 최신 버전의 Java에서 기본적으로 제공되므로, 추가적인 라이브러리 없이 사용할 수 있습니다. 반면에 <code>@Value</code>는 프로젝트에 Lombok을 추가해야 하고, 기능 확장성 측면에서 더 많은 옵션을 제공합니다.</p>
<h2 id="create-a-resource-controller">Create a Resource Controller</h2>
<p><code>src/main/java/guides/restservice</code>에 GreetingController 클래스를 생성합니다. </p>
<p>Spring에서 RESTful 웹 서비스를 구축하는 방식은 HTTP 요청을 컨트롤러가 처리합니다. 이러한 구성 요소는 <code>@RestController</code> 어노테이션으로 식별되며, 아래의 <code>GreetingController</code>는 <code>/greeting</code>으로의 GET 요청을 처리하여 <code>Greeting</code> 클래스의 새 인스턴스를 반환합니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>restservice</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>concurrent<span class="token punctuation">.</span>atomic</span><span class="token punctuation">.</span><span class="token class-name">AtomicLong</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">GetMapping</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RequestParam</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RestController</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@RestController</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">GreetingController</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token class-name">String</span> template <span class="token operator">=</span> <span class="token string">"Hello, %s!"</span><span class="token punctuation">;</span>
	<span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">AtomicLong</span> counter <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">AtomicLong</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/greeting"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">Greeting</span> <span class="token function">greeting</span><span class="token punctuation">(</span><span class="token annotation punctuation">@RequestParam</span><span class="token punctuation">(</span>value <span class="token operator">=</span> <span class="token string">"name"</span><span class="token punctuation">,</span> defaultValue <span class="token operator">=</span> <span class="token string">"World"</span><span class="token punctuation">)</span> <span class="token class-name">String</span> name<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">Greeting</span><span class="token punctuation">(</span>counter<span class="token punctuation">.</span><span class="token function">incrementAndGet</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token class-name">String</span><span class="token punctuation">.</span><span class="token function">format</span><span class="token punctuation">(</span>template<span class="token punctuation">,</span> name<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>@GetMapping</code> 어노테이션은 HTTP GET 요청을 <code>/greeting</code> 경로에 매핑시킵니다.</p>
<blockquote>
<p>다른 HTTP 동사에 대한 동반자 어노테이션이 있습니다 (예: POST를 위한 <code>@PostMapping</code>). 또한 이들은 모두 <code>@RequestMapping</code> 어노테이션에서 파생되며, 동의어로 사용될 수 있습니다 (예: <code>@RequestMapping(method=GET)</code>).</p>
</blockquote>
<p><code>@RequestParam</code>은 쿼리 문자열 파라미터인 <code>name</code> 값을 <code>greeting()</code> 메서드의 <code>name</code> 파라미터에 바인딩합니다. 요청에 <code>name</code> 파라미터가 없으면 기본값으로 <code>World</code>가 사용됩니다.</p>
<p>메서드 본문의 구현은 <code>counter</code>에서 다음 값에 기반하여 <code>id</code>와 <code>content</code> 속성을 가진 새로운 <code>Greeting</code> 객체를 생성하고 반환합니다. 또한 주어진 이름을 <code>greeting</code> 템플릿을 사용하여 포맷팅합니다.</p>
<p>전통적인 MVC 컨트롤러와 이전에 표시된 RESTful 웹 서비스 컨트롤러 간의 주요한 차이점은 HTTP 응답 본문이 생성되는 방식입니다. HTML에 대한 서버 측 렌더링을 위한 뷰 기술에 의존하는 대신, 이 RESTful 웹 서비스 컨트롤러는 <code>Greeting</code> 객체를 생성하여 반환합니다. 객체 데이터는 JSON으로 직접 HTTP 응답으로 작성됩니다.</p>
<p>이 코드는 Spring의 <code>@RestController</code> 어노테이션을 사용합니다. 이 어노테이션은 클래스를 컨트롤러로 표시하며 모든 메서드가 뷰 대신 도메인 객체를 반환합니다. 이것은 <code>@Controller</code>와 <code>@ResponseBody</code>를 모두 포함한 축약형입니다.</p>
<p><code>Greeting</code> 객체는 JSON으로 변환되어야 합니다. Spring의 HTTP 메시지 컨버터 지원 덕분에 이 변환을 수동으로 수행할 필요가 없습니다. Jackson 2가 클래스패스에 있기 때문에 Spring의 <code>MappingJackson2HttpMessageConverter</code>가 자동으로 <code>Greeting</code> 인스턴스를 JSON으로 변환합니다.</p>
<p><code>@SpringBootApplication</code>은 다음과 같은 모든 것들을 추가하는 편리한 어노테이션입니다:</p>
<ul>
<li><code>@Configuration</code>: 애플리케이션 컨텍스트의 빈 정의 원천으로 클래스를 태그합니다.</li>
<li><code>@EnableAutoConfiguration</code>: Spring Boot에게 클래스패스 설정, 다른 빈 및 여러 속성 설정에 따라 빈을 추가 시작하도록 지시합니다. 예를 들어, spring-webmvc가 클래스패스에 있으면 이 어노테이션은 애플리케이션을 웹 애플리케이션으로 표시하고 DispatcherServlet 설정과 같은 핵심 동작을 활성화합니다.</li>
<li><code>@ComponentScan</code>: Spring에게 <code>guides</code> 패키지 내의 다른 컴포넌트, 구성 및 서비스를 찾아 컨트롤러를 찾도록 합니다.</li>
</ul>
<p><code>main()</code> 메서드는 Spring Boot의 <code>SpringApplication.run()</code> 메서드를 사용하여 애플리케이션을 시작합니다. XML이나 <code>web.xml</code> 파일이 없습니다. 이 웹 애플리케이션은 100% 순수한 자바이며 플러머 및 인프라 구성을 다룰 필요가 없습니다.</p>