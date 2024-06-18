<p>이 가이드는 Spring을 사용하여 "Hello, World" 하이퍼미디어 기반 REST 웹 서비스를 생성하는 과정을 안내합니다.</p>
<p>하이퍼미디어는 REST의 중요한 측면(aspect)입니다. 이를 통해 클라이언트와 서버를 크게 분리하고 독립적으로 발전할 수 있는 서비스를 구축할 수 있습니다. REST 리소스에 대해 반환된 표현(representation)에는 데이터뿐만 아니라 관련 리소스에 대한 링크도 포함됩니다. 따라서 표현의 디자인은 전체 서비스 디자인에 매우 중요합니다.</p>
<h2 id="하이퍼미디어-링크">하이퍼미디어 링크</h2>
<p>하이퍼미디어 링크와 웹 링크는 목적과 사용되는 컨텍스트에 따라 구별됩니다. 하이퍼미디어 링크는 RESTful API에서 서버와 클라이언트 간의 상호작용을 위해 사용되는 반면, 웹 링크는 웹 페이지 간의 이동을 위해 사용됩니다. 하이퍼미디어 링크는 프로그래밍적으로 데이터를 주고 받는 데 사용되고, 웹 링크는 사용자가 웹 페이지를 탐색하는 데 사용됩니다.</p>
<ol>
<li>
<p><strong>하이퍼미디어 링크</strong>:</p>
<ul>
<li>RESTful API에서 사용되며, 데이터 포맷(예: JSON 또는 XML) 안에 존재합니다.</li>
<li>클라이언트와 서버 간의 통신을 위한 링크입니다.</li>
<li>리소스 간의 관계와 상호작용을 설명하고, 다음에 수행할 수 있는 작업을 포함하는 URI를 포함합니다.</li>
<li>예를 들어, JSON 형식의 API 응답에서 특정 키(예: "links") 아래에 링크 정보가 포함됩니다.</li>
</ul>
</li>
<li>
<p><strong>웹 링크</strong>:</p>
<ul>
<li>HTML 문서에서 사용됩니다.</li>
<li>일반적으로 <code>&lt;a&gt;</code>(anchor) 태그로 정의되어 웹 페이지 간의 이동을 제공합니다.</li>
<li>웹 브라우저에서 사용자가 클릭하여 다른 웹 페이지로 이동하는 데 사용됩니다.</li>
<li>이 링크들은 주로 사람이 읽고 클릭할 수 있는 형태(텍스트 또는 이미지)로 표시됩니다.</li>
</ul>
</li>
</ol>
<h2 id="hateoas">HATEOAS</h2>
<p>HATEOAS는 REST 아키텍처 스타일에서 중요한 원칙 중 하나로, Hypermedia를 이용하여 애플리케이션 상태를 관리하는 방법을 정의합니다. 이는 "Hypermedia as the Engine of Application State"의 약자로서, RESTful API에서 <strong>클라이언트와 서버 간의 통신과 상태 전이를 위한 규칙을 제공</strong>합니다.</p>
<p>기본적으로 HATEOAS는 <strong>클라이언트에게 어떤 리소스를 요청할 수 있는 링크들을 동적으로 제공</strong>함으로써 RESTful 서비스의 상호작용성을 향상시킵니다. 클라이언트는 리소스의 표현(representation)을 받은 후, 해당 리소스에서 가능한 작업들을 설명하는 하이퍼미디어 링크들을 확인할 수 있습니다. 이를 통해 클라이언트는 서버로부터 받은 리소스 상태에 따라 어떤 작업을 할 수 있는지 동적으로 결정할 수 있습니다.</p>
<p>HATEOAS를 따르는 RESTful 서비스는 다음과 같은 이점을 제공합니다:</p>
<ol>
<li>
<p><strong>자기 기술적인 API</strong>:</p>
<ul>
<li><strong>예시</strong>: RESTful API가 제품 목록을 반환하는데, 각 제품에 대한 상세 정보를 얻기 위한 링크가 포함되어 있습니다. 클라이언트는 이 링크를 통해 어떤 작업을 수행할 수 있는지 이해하고, 각 작업의 결과를 예측할 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>유연성과 확장성</strong>:</p>
<ul>
<li><strong>예시</strong>: 새로운 리소스나 기능이 API에 추가될 때, 클라이언트는 하이퍼미디어 링크를 통해 새로운 작업이나 리소스에 접근할 수 있습니다. 예를 들어, 주문 API에 새로운 상태(취소된 주문)가 추가되더라도, 클라이언트는 해당 상태를 이해하고 상태를 변경할 수 있는 링크를 통해 작업을 수행할 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>디자인의 단순화</strong>:</p>
<ul>
<li><strong>예시</strong>: API 디자인 시, 각 리소스에 대한 하이퍼미디어 링크를 명확하게 정의함으로써 클라이언트와 서버 간의 통신이 명확해집니다. 예를 들어, 주문 리소스에는 주문 상세 정보, 결제 정보, 취소 등의 작업을 위한 링크가 명시적으로 포함될 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>안정성</strong>:</p>
<ul>
<li><strong>예시</strong>: 클라이언트는 하이퍼미디어 링크를 통해 작업을 수행하므로, 서버 측의 변경에 따라 클라이언트가 유연하게 대응할 수 있습니다. 즉, 클라이언트는 리소스에 대한 예상치 못한 상태 변화에 대비할 수 있습니다. 만약 새로운 작업이 추가되거나 변경되더라도, 클라이언트는 링크를 통해 새로운 작업을 찾아내고 수행할 수 있습니다.</li>
</ul>
</li>
</ol>
<h2 id="spring-mvc와-hateoas의-차이">Spring MVC와 HATEOAS의 차이</h2>
<p><a href="https://velog.io/@dev_hammy/GuideUploading-Files">https://velog.io/@dev_hammy/GuideUploading-Files</a> </p>
<p>Spring MVC 코드는 파일 시스템에서의 파일 경로를 URI로 사용하는데, 이는 파일을 식별하고 접근하기 위한 방식입니다. 예를 들어, 클라이언트가 요청하면 서버는 특정 파일에 대한 경로를 URI로 제공하여 해당 파일을 다운로드할 수 있도록 합니다. 이는 파일에 대한 실질적인 위치를 가리키는 것이며, 클라이언트가 이 URI를 사용하여 파일을 다운로드하거나 업로드합니다.</p>
<p>HATEOAS에서의 URI는 조금 다릅니다. 여기서의 URI는 특정 작업을 수행하기 위한 링크로, 리소스 간의 상태 전이를 나타냅니다. 예를 들어, 클라이언트가 서버로부터 받은 응답에 하이퍼미디어 링크가 포함되어 있다면, 이 링크는 다음에 가능한 작업을 가리키는 URI를 포함할 수 있습니다. 이 URI를 클라이언트가 따라가면 해당 작업을 수행하게 됩니다.</p>
<p>중요한 차이는 Spring MVC에서의 URI는 특정 파일이나 위치를 가리키는 것이고, HATEOAS에서의 URI는 클라이언트가 수행할 수 있는 작업을 가리키는 것입니다. Spring MVC의 URI는 파일을 식별하고 접근하는 데 사용되며, HATEOAS의 URI는 API의 상호작용성을 높이기 위해 클라이언트가 다음에 수행할 수 있는 작업을 나타냅니다. </p>
<p>즉, Spring MVC에서의 URI는 서버의 파일이나 리소스를 가리키는 것이고, HATEOAS의 URI는 클라이언트가 다음에 수행할 수 있는 작업을 가리킵니다. 이 두 가지는 목적과 사용되는 컨텍스트에서 차이가 있습니다.</p>
<h2 id="what-you-will-build">What You Will Build</h2>
<p>Spring HATEOAS를 사용하여 하이퍼미디어 기반 REST 서비스를 구축합니다. 이 API 라이브러리는 Spring MVC 컨트롤러를 가리키는 링크를 생성하고 리소스 표현(representation)을 구축하며 지원되는 하이퍼미디어 형식(예: HAL)으로 렌더링되는 방법을 제어하는 데 사용할 수 있습니다. ).</p>
<p>서비스는 <code>http://localhost:8080/greeting</code>에서 HTTP GET 요청을 수락합니다.</p>
<p>리소스 자체를 가리키는 링크인 가능한 가장 간단한 하이퍼미디어 요소가 풍부한 인사말의 JSON 표현으로 응답합니다. 다음 목록은 출력을 보여줍니다.</p>
<pre><code class="language-json">{
  "content":"Hello, World!",
  "_links":{
    "self":{
      "href":"http://localhost:8080/greeting?name=World"
    }
  }
}</code></pre>
<p>응답(response)은 다음 목록에 표시된 것처럼 쿼리 문자열의 선택적 <code>name</code> 매개 변수를 사용하여 인사말을 사용자 지정할 수 있음을 이미 나타냅니다.</p>
<pre><code class="language-null">http://localhost:8080/greeting?name=User</code></pre>
<p><code>name</code> 매개변수 값은 <code>World</code>의 기본값을 재정의하고 다음 목록에 표시된 대로 응답에 반영됩니다.</p>
<pre><code class="language-json">{
  "content":"Hello, User!",
  "_links":{
    "self":{
      "href":"http://localhost:8080/greeting?name=User"
    }
  }
}</code></pre>
<h2 id="starting-with-spring-initializr">Starting with Spring Initializr</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/f7123b66-6881-4326-90ff-dabb68adf958/image.png"></p>
<h2 id="create-a-resource-representation-class">Create a Resource Representation Class</h2>
<p>이제 프로젝트와 빌드 시스템을 설정했으므로 웹 서비스를 생성할 수 있습니다.</p>
<p>서비스 상호 작용에 대해 생각하여 프로세스를 시작하십시오.</p>
<p>서비스는 선택적으로 쿼리 문자열에 <code>name</code> 매개변수를 사용하여 GET 요청을 처리하기 위해 <code>/greeting</code>에 리소스를 노출합니다. GET 요청은 인사말을 나타내기 위해 본문에 JSON이 포함된 <code>200 OK</code> 응답을 반환해야 합니다.</p>
<p>그 외에도 리소스의 JSON 표현은 <code>_links</code> 속성의 하이퍼미디어 요소 목록으로 강화됩니다. 가장 기본적인 형태는 리소스 자체를 가리키는 링크입니다. 표현은 다음 목록과 유사해야 합니다.</p>
<pre><code class="language-json">{
  "content":"Hello, World!",
  "_links":{
    "self":{
      "href":"http://localhost:8080/greeting?name=World"
    }
  }
}</code></pre>
<p><code>content</code>은 <code>greeting</code>의 텍스트 표현입니다. <code>_links</code> 요소에는 링크 목록이 포함되어 있습니다(이 경우 정확히 관계 유형이 <code>rel</code>이고 액세스된 리소스를 가리키는 <code>href</code> 속성이 있는 링크).</p>
<p>greeting 표현을 모델링하려면 리소스 표현(representation) 클래스를 만듭니다. <code>_links</code> 속성은 표현 모델의 기본 속성이므로 Spring HATEOAS는 Link 인스턴스를 추가하고 앞에서 설명한 대로 렌더링되도록 하는 기본 클래스(<code>RepresentationModel</code>이라고 함)와 함께 제공됩니다.</p>
<p>다음 목록(src/main/java/guides/resthateoas/Greeting.java에서)에 표시된 대로 <code>RepresentationModel</code>을 확장하고 콘텐츠에 대한 필드와 접근자와 생성자를 추가하는 기존의 일반 Java 개체를 만듭니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>resthateoas</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>fasterxml<span class="token punctuation">.</span>jackson<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">JsonCreator</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>fasterxml<span class="token punctuation">.</span>jackson<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">JsonProperty</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>hateoas</span><span class="token punctuation">.</span><span class="token class-name">RepresentationModel</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Greeting</span> <span class="token keyword">extends</span> <span class="token class-name">RepresentationModel</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Greeting</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">String</span> content<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@JsonCreator</span>
    <span class="token keyword">public</span> <span class="token class-name">Greeting</span><span class="token punctuation">(</span><span class="token annotation punctuation">@JsonProperty</span><span class="token punctuation">(</span><span class="token string">"content"</span><span class="token punctuation">)</span> <span class="token class-name">String</span> content<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>content <span class="token operator">=</span> content<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getContent</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> content<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<ul>
<li>
<p><code>@JsonCreator</code>: Jackson이 이 POJO의 인스턴스를 생성할 수 있는 방법을 나타냅니다.</p>
</li>
<li>
<p><code>@JsonProperty</code>: Jackson이 이 생성자 인수를 입력해야 하는 필드를 표시합니다.</p>
</li>
</ul>
<blockquote>
<p>이 가이드의 뒷부분에서 볼 수 있듯이 Spring은 Jackson JSON 라이브러리를 사용하여 <code>Greeting</code> 유형의 인스턴스를 JSON으로 자동 마샬링합니다.</p>
</blockquote>
<p>다음으로 이러한 인사말을 제공할 리소스 컨트롤러를 만듭니다.</p>
<h2 id="create-rest-controller">Create REST Controller</h2>
<p>RESTful 웹 서비스를 구축하는 Spring의 접근 방식에서 HTTP 요청은 컨트롤러에 의해 처리됩니다. 구성 요소는 <code>@Controller</code> 및 <code>@ResponseBody</code> 주석을 결합한 <code>@RestController</code> 주석으로 식별됩니다. 다음 GreetingController(src/main/java/guides/resthateoas/GreetingController.java)는 <code>Greeting</code> 클래스의 새 인스턴스를 반환하여 <code>/greeting</code>에 대한 <code>GET</code> 요청을 처리합니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>resthateoas</span><span class="token punctuation">;</span>


<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>http</span><span class="token punctuation">.</span><span class="token class-name">HttpEntity</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>http</span><span class="token punctuation">.</span><span class="token class-name">HttpStatus</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>http</span><span class="token punctuation">.</span><span class="token class-name">ResponseEntity</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RequestMapping</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RequestParam</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RestController</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>hateoas<span class="token punctuation">.</span>server<span class="token punctuation">.</span>mvc</span><span class="token punctuation">.</span><span class="token class-name">WebMvcLinkBuilder</span><span class="token punctuation">.</span>linkTo<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>hateoas<span class="token punctuation">.</span>server<span class="token punctuation">.</span>mvc</span><span class="token punctuation">.</span><span class="token class-name">WebMvcLinkBuilder</span><span class="token punctuation">.</span>methodOn<span class="token punctuation">;</span>

<span class="token annotation punctuation">@RestController</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">GreetingController</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token class-name">String</span> TEMPLATE <span class="token operator">=</span> <span class="token string">"Hello, %s!"</span><span class="token punctuation">;</span>

    <span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span><span class="token string">"/greeting"</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token class-name">HttpEntity</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Greeting</span><span class="token punctuation">&gt;</span></span> <span class="token function">greeting</span><span class="token punctuation">(</span><span class="token annotation punctuation">@RequestParam</span><span class="token punctuation">(</span>value <span class="token operator">=</span> <span class="token string">"name"</span><span class="token punctuation">,</span> defaultValue <span class="token operator">=</span> <span class="token string">"World"</span><span class="token punctuation">)</span> <span class="token class-name">String</span> name<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">Greeting</span> greeting <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Greeting</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">.</span><span class="token function">format</span><span class="token punctuation">(</span>TEMPLATE<span class="token punctuation">,</span> name<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        greeting<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token function">linkTo</span><span class="token punctuation">(</span><span class="token function">methodOn</span><span class="token punctuation">(</span><span class="token class-name">GreetingController</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">greeting</span><span class="token punctuation">(</span>name<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">withSelfRel</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">ResponseEntity</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span>greeting<span class="token punctuation">,</span> <span class="token class-name">HttpStatus</span><span class="token punctuation">.</span>OK<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이 컨트롤러는 간결하고 단순하지만 많은 일이 진행되고 있습니다. 우리는 그것을 단계별로 분해합니다.</p>
<p><code>@RequestMapping</code> 주석은 <code>/greeting</code>에 대한 HTTP 요청이 <code>greeting()</code> 메소드에 매핑되도록 보장합니다.</p>
<blockquote>
<p>위의 예에서는 <code>@RequestMapping</code>이 기본적으로 모든 HTTP 작업을 매핑하기 때문에 <code>GET</code>과 <code>PUT</code>, <code>POST</code> 등을 지정하지 않습니다. 이 매핑 범위를 좁히려면 <code>@GetMapping("/greeting")</code>을 사용하세요. 이 경우 <code>org.springframework.web.bind.annotation.GetMapping;</code>도 가져오는 것입니다.</p>
</blockquote>
<p><code>@RequestParam</code>은 쿼리 문자열 매개변수 <code>name</code> 값을 <code>greeting()</code> 메서드의 <code>name</code> 매개변수에 바인딩합니다. 이 쿼리 문자열 매개변수는 <code>defaultValue</code> 속성을 사용하기 때문에 암시적으로 필요하지 않습니다(implicitly not <code>required</code>). 요청에 없으면 <code>defaultValue</code>인 <code>World</code>가 사용됩니다.</p>
<p><code>@RestController</code> 주석이 클래스에 존재하기 때문에 암시적 <code>@ResponseBody</code> 주석이 인사말 메서드에 추가됩니다. 이로 인해 Spring MVC는 반환된 <code>HttpEntity</code>와 해당 페이로드(<code>Greeting</code>)를 응답(response)에 직접 렌더링합니다.</p>
<p>메서드 구현에서 가장 흥미로운 부분은 컨트롤러 메서드를 가리키는 링크를 생성하는 방법과 이를 표현 모델에 추가하는 방법입니다. <code>linkTo(…)</code> 및 <code>methodOn(…)</code>은 모두 컨트롤러에서 메서드 호출을 가짜로 만들 수 있는 <code>ControllerLinkBuilder</code>의 정적 메서드입니다. 반환된 <code>LinkBuilder</code>는 컨트롤러 메서드의 매핑 주석을 검사하여 메서드가 매핑되는 URI를 정확하게 구축합니다.</p>
<blockquote>
<p>Spring HATEOAS는 다양한 <code>X-FORWARDED-</code> 헤더를 존중합니다. Spring HATEOAS 서비스를 프록시 뒤에 배치하고 <code>X-FORWARDED-HOST</code> 헤더로 올바르게 구성하면 결과 링크의 형식이 올바르게 지정됩니다.</p>
</blockquote>
<p><code>X-Forwarded</code> 헤더는 일반적으로 클라이언트와 서버 간의 HTTP 프록시나 로드 밸런서와 같은 중간 단계에서 사용되는 헤더입니다. 이 헤더는 원래 요청에 대한 정보를 포함하고 있어, 중간에 위치한 프록시나 로드 밸런서가 요청을 전달할 때 클라이언트와 서버 간의 관계를 유지하고 수정하지 않도록 돕습니다.</p>
<p>여러 가지 <code>X-Forwarded</code> 헤더가 있는데, 주요한 것들은 다음과 같습니다:</p>
<ol>
<li>
<p><strong>X-Forwarded-For</strong>: 클라이언트의 실제 IP 주소를 전달하는 데 사용됩니다. 클라이언트가 프록시를 통해 서버에 접근할 때, 클라이언트의 IP를 식별하기 위해 사용됩니다.</p>
</li>
<li>
<p><strong>X-Forwarded-Proto</strong>: 클라이언트와 프록시 간의 프로토콜(예: HTTP 또는 HTTPS)를 전달합니다. 이를 통해 서버는 요청이 보안 연결(HTTPS)을 통해 전달되었는지 확인할 수 있습니다.</p>
</li>
<li>
<p><strong>X-Forwarded-Host</strong>: 클라이언트가 요청한 호스트 이름을 전달합니다. 이는 프록시가 여러 개의 도메인을 처리하는 경우 원래 호스트 이름을 식별하기 위해 사용됩니다.</p>
</li>
</ol>
<p>Spring HATEOAS는 이러한 <code>X-Forwarded</code> 헤더들을 존중하여, 프록시나 로드 밸런서 뒤에 서비스가 배치된 경우 올바른 URL을 생성합니다. 특히, <code>X-Forwarded-Host</code> 헤더를 올바르게 구성하면, Spring HATEOAS는 생성하는 링크들의 형식을 이에 맞게 조정하여 올바른 URI를 제공합니다.</p>
<hr>
<p><code>withSelfRel()</code>을 호출하면 <code>Greeting</code> 표현 모델에 추가하는 <code>Link</code> 인스턴스가 생성됩니다.</p>
<h2 id="test-the-service">Test the Service</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/a0e015b1-de4c-455e-b9d7-dd15f95beb04/image.png"></p>
<p>이제 서비스가 실행되었으므로 <code>http://localhost:8080/greeting</code>을 방문하면 다음 내용이 표시됩니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/92ad17c2-c4ac-40da-93cd-a32268f6e626/image.png"></p>
<p>다음 URL을 방문하여 <code>name</code> 쿼리 문자열 매개변수를 제공하십시오: <code>http://localhost:8080/greeting?name=User</code>. <code>Hello, World!</code>에서 <code>content</code> 속성 값이 어떻게 변경되는지 확인하세요. <code>Hello, User!</code> 다음 목록에 표시된 것처럼 <code>self</code> 링크의 <code>href</code> 속성도 해당 변경 사항을 반영(reflect)합니다.<br>
<img src="https://velog.velcdn.com/images/dev_hammy/post/ad20001c-4736-4da9-844d-8dec28dd8714/image.png"></p>
<p>이 변경 사항은 <code>GreetingController</code>의 <code>@RequestParam</code> 배열이 예상대로 작동함을 보여줍니다. <code>name</code> 매개변수에는 기본값인 <code>World</code>가 지정되었지만 항상 쿼리 문자열을 통해 명시적으로 재정의될 수 있습니다.</p>