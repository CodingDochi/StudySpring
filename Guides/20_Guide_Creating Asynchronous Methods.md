<p>이 가이드에서는 GitHub에 대한 비동기 쿼리를 만드는 과정을 안내합니다. 서비스를 확장할 때 자주 사용되는 기능인 비동기식 부분에 중점을 둡니다.</p>
<h2 id="무엇을-구축할-것인가">무엇을 구축할 것인가</h2>
<p>GitHub 사용자 정보를 쿼리하고 GitHub의 API를 통해 데이터를 검색하는 조회 서비스를 구축합니다. 서비스 확장에 대한 한 가지 접근 방식은 백그라운드에서 비용이 많이 드는 작업을 실행하고 Java의 <code>CompletableFuture</code> 인터페이스를 사용하여 결과를 기다리는 것입니다. Java의 <code>CompletableFuture</code>는 일반 <code>Future</code>에서 진화된 것입니다. 이를 통해 여러 비동기 작업을 파이프라인으로 쉽게 만들고 이를 단일 비동기 계산으로 병합할 수 있습니다.</p>
<h2 id="async-어노테이션과-aop">@Async 어노테이션과 AOP</h2>
<p>스프링에서 <code>@Async</code> 어노테이션은 Aspect-Oriented Programming (AOP)을 사용하여 구현됩니다. AOP는 프록시를 사용하여 메서드 호출을 감싸거나 변경할 수 있는 기술입니다. </p>
<p>일반적으로 스프링은 컨테이너에 의해 관리되는 빈(Bean)으로 등록된 클래스들에 대해 AOP를 적용합니다. 스프링은 빈으로 등록된 클래스의 메서드를 프록시로 감싸 비동기적으로 실행하거나 다른 작업을 수행할 수 있게 됩니다. 이 때, <code>@Async</code> 어노테이션이 적용된 메서드 역시 프록시로 감싸져 비동기적으로 실행됩니다.</p>
<p>하지만 로컬 인스턴스를 직접 생성하게 되면 해당 클래스는 스프링이 관리하는 빈이 아니게 됩니다. 스프링의 빈 컨테이너에 의해 생성되지 않은 클래스는 AOP 프록시가 적용되지 않습니다. 즉, 스프링이 해당 클래스의 메서드 호출을 감싸 비동기적으로 실행할 수 없게 됩니다.</p>
<p>따라서 <code>@Async</code>의 효과를 받으려면 해당 클래스는 스프링이 관리하는 빈으로 등록되어야 하며, 직접적으로 로컬 인스턴스를 생성하는 방식은 AOP 프록시가 적용되지 않아 비동기적으로 실행되지 않게 됩니다.</p>
<h2 id="동기--비동기">동기 / 비동기</h2>
<p><img src="https://media.licdn.com/dms/image/C4D12AQGgkbeBMdsliQ/article-inline_image-shrink_400_744/0/1642050446527?e=1707955200&amp;v=beta&amp;t=YeUEaNviPd3KTFMEIA7TXtd3wmckNYHZNKqksK_jO44"></p>
<h2 id="future와-completablefuture">Future와 CompletableFuture</h2>
<h3 id="future">Future:</h3>
<ul>
<li><strong>동기적 API</strong>: <code>Future</code>는 비동기 계산 결과를 표현하는 인터페이스로, 작업의 결과를 가져오기 위해 <code>get()</code> 메서드를 사용합니다. 이 때, 작업이 완료되기를 기다리며 결과가 준비될 때까지 블록될 수 있습니다.</li>
<li><strong>블로킹 호출</strong>: <code>get()</code> 메서드는 작업이 완료되기 전까지 블록되므로, 여러 작업을 동시에 처리할 때 효율성과 유연성이 떨어질 수 있습니다.</li>
<li><strong>에러 처리 어려움</strong>: 예외 처리가 복잡하며, 여러 개의 Future를 조합하거나 조작하기 어려운 한계가 있습니다.</li>
</ul>
<h3 id="completablefuture">CompletableFuture:</h3>
<ul>
<li><strong>비동기 및 동기 API</strong>: <code>CompletableFuture</code>는 비동기적인 작업을 처리하면서, <code>thenApply()</code>, <code>thenCompose()</code> 등의 메서드를 사용하여 연결된 작업을 처리할 수 있습니다. 동시에 <code>get()</code> 메서드를 통해 동기적으로 결과를 얻을 수도 있습니다.</li>
<li><strong>콜백 처리 및 연결</strong>: <code>CompletableFuture</code>는 콜백 형식으로 결과를 처리할 수 있으며, 여러 작업을 순차적으로 연결하는 방식을 제공합니다.</li>
<li><strong>에러 핸들링</strong>: <code>exceptionally()</code>나 <code>handle()</code> 등의 메서드를 통해 예외 처리가 용이하며, 여러 <code>CompletableFuture</code>를 조합하여 조작하는 기능을 제공합니다.</li>
<li><strong>논블로킹 메서드 체이닝</strong>: <code>CompletableFuture</code>는 블로킹되지 않는 메서드 체이닝을 통해 작업의 연결 및 조합이 용이하며, 비동기 작업을 더 효율적으로 처리할 수 있습니다.</li>
</ul>
<h3 id="요약">요약:</h3>
<p><code>CompletableFuture</code>는 <code>Future</code>의 한계를 극복하고 보다 유연하고 효율적인 비동기적인 작업 처리를 위한 클래스입니다. 비동기 작업의 결과를 조작하고 조합하는 데 있어서 훨씬 강력하며, 논블로킹 방식으로 작업을 연결할 수 있어서 성능 면에서도 더 유리합니다.</p>
<h2 id="starting-with-spring-initializr">Starting with Spring Initializr</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/fdb06f8b-9345-4827-96f8-ee807d6915cc/image.png"></p>
<h2 id="create-a-representation-of-a-github-user">Create a Representation of a GitHub User</h2>
<p>GitHub 조회 서비스를 생성하기 전에 GitHub의 API를 통해 검색할 데이터에 대한 표현을 정의해야 합니다.</p>
<p>사용자 표현을 모델링하려면 리소스 표현 클래스를 만듭니다. 그렇게 하려면 다음 예제(src/main/java/guides/asyncmethod/User.java)에서 볼 수 있듯이 필드, 생성자 및 접근자가 포함된 기존의 일반 Java 객체를 제공하세요.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>asyncmethod</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>fasterxml<span class="token punctuation">.</span>jackson<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">JsonIgnoreProperties</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@JsonIgnoreProperties</span><span class="token punctuation">(</span>ignoreUnknown <span class="token operator">=</span> <span class="token boolean">true</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">User</span> <span class="token punctuation">{</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> name<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> blog<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> name<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setName</span><span class="token punctuation">(</span><span class="token class-name">String</span> name<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>name <span class="token operator">=</span> name<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getBlog</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> blog<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setBlog</span><span class="token punctuation">(</span><span class="token class-name">String</span> blog<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>blog <span class="token operator">=</span> blog<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token string">"User [name="</span> <span class="token operator">+</span> name <span class="token operator">+</span> <span class="token string">", blog="</span> <span class="token operator">+</span> blog <span class="token operator">+</span> <span class="token string">"]"</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span>
</code></pre>
<ul>
<li>
<p><code>@JsonIgnore</code>: 이 주석은 해당 필드가 JSON 직렬화 및 역직렬화 과정에서 무시되어야 함을 나타냅니다. 즉, 해당 필드는 JSON으로 변환되거나 JSON에서 역직렬화될 때 무시됩니다.<br>
예를 들어, <code>private String secretCode;</code> 필드가 있고, 이 필드에 <code>@JsonIgnore</code> 주석이 달려있다면, JSON으로 변환될 때 해당 필드는 포함되지 않습니다.</p>
</li>
<li>
<p><code>@JsonIgnoreProperties</code>: 이 주석은 클래스 수준에서 사용되며, 특정 필드들을 무시하도록 지정합니다. <code>ignoreUnknown = true</code>는 이 클래스가 JSON에 포함되지 않은 다른 속성들을 무시하도록 지시합니다. 즉, 이 클래스의 객체를 JSON으로 변환하거나 JSON에서 역직렬화할 때, 알 수 없는 속성들은 무시됩니다.</p>
</li>
<li>
<p>JSON으로 변환하거나 역직렬화할 때, "알 수 없는 속성들"은 다음을 의미합니다:</p>
</li>
</ul>
<p>일반적으로 프로그래밍에서 객체를 JSON 형식으로 변환하거나, JSON을 객체로 다시 역직렬화할 때, 클래스에 정의된 필드 외에 다른 속성들이 JSON에 포함되는 경우가 있습니다. 이런 경우에는 이러한 추가적인 속성들을 처리하는 방법이 필요합니다.</p>
<p>예를 들어, Java 클래스에 <code>name</code>과 <code>age</code>라는 두 가지 속성이 있고, JSON에는 <code>name</code>, <code>age</code> 이외에 <code>address</code>, <code>email</code> 등 다른 속성이 포함된 경우를 상상해보세요. 이 때, Jackson 라이브러리에서 <code>@JsonIgnoreProperties(ignoreUnknown = true)</code>를 사용하면, 클래스에 정의된 속성 이외의 속성들을 객체로 변환할 때 무시하도록 지시할 수 있습니다. 즉, JSON에는 존재하지만 클래스에 매핑되지 않은 속성들을 무시하고 무시된 상태로 진행됩니다.</p>
<p>이것은 애플리케이션이나 시스템에서 외부에서 전달되는 데이터가 예상치 못한 속성을 가질 수 있는 경우에 유용합니다. 이러한 속성들을 그대로 무시하고 처리함으로써 불필요한 예외를 방지하고 안전하게 처리할 수 있습니다.</p>
<hr>
<p>Spring은 Jackson JSON 라이브러리를 사용하여 GitHub의 JSON 응답을 <code>User</code> 객체로 변환합니다. <code>@JsonIgnoreProperties</code> 주석은 Spring에게 클래스에 나열되지 않은 모든 속성을 무시하도록 지시합니다. 이를 통해 쉽게 REST 호출을 수행하고 도메인 개체를 생성할 수 있습니다.</p>
<p>이 가이드에서는 데모 목적으로 <code>name</code>과 <code>blog</code> URL만 가져옵니다.</p>
<h2 id="github-조회-서비스-만들기">GitHub 조회 서비스 만들기</h2>
<p>다음으로 GitHub를 쿼리하여 사용자 정보를 찾는 서비스를 만들어야 합니다. 다음 목록(src/main/java/guides/asyncmethod/GitHubLookupService.java)에서는 이를 수행하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>asyncmethod</span><span class="token punctuation">;</span>


<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>slf4j</span><span class="token punctuation">.</span><span class="token class-name">Logger</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>slf4j</span><span class="token punctuation">.</span><span class="token class-name">LoggerFactory</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>web<span class="token punctuation">.</span>client</span><span class="token punctuation">.</span><span class="token class-name">RestTemplateBuilder</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>scheduling<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Async</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype</span><span class="token punctuation">.</span><span class="token class-name">Service</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>client</span><span class="token punctuation">.</span><span class="token class-name">RestTemplate</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>concurrent</span><span class="token punctuation">.</span><span class="token class-name">CompletableFuture</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Service</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">GitHubLookupService</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token class-name">Logger</span> logger <span class="token operator">=</span>
            <span class="token class-name">LoggerFactory</span><span class="token punctuation">.</span><span class="token function">getLogger</span><span class="token punctuation">(</span><span class="token class-name">GitHubLookupService</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">RestTemplate</span> restTemplate<span class="token punctuation">;</span>
    
    <span class="token keyword">public</span> <span class="token class-name">GitHubLookupService</span><span class="token punctuation">(</span><span class="token class-name">RestTemplateBuilder</span> restTemplateBuilder<span class="token punctuation">)</span><span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>restTemplate <span class="token operator">=</span> restTemplateBuilder<span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Async</span>
    <span class="token keyword">public</span> <span class="token class-name">CompletableFuture</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findUser</span><span class="token punctuation">(</span><span class="token class-name">String</span> user<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">InterruptedException</span> <span class="token punctuation">{</span>
        logger<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"Looking up "</span><span class="token operator">+</span>user<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">String</span> url <span class="token operator">=</span> <span class="token class-name">String</span><span class="token punctuation">.</span><span class="token function">format</span><span class="token punctuation">(</span><span class="token string">"https://api.github.com/users/%s"</span><span class="token punctuation">,</span> user<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">User</span> results <span class="token operator">=</span> restTemplate<span class="token punctuation">.</span><span class="token function">getForObject</span><span class="token punctuation">(</span>url<span class="token punctuation">,</span> <span class="token class-name">User</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token comment">//Artificial delay of 1s for demonstration purposes</span>
        <span class="token class-name">Thread</span><span class="token punctuation">.</span><span class="token function">sleep</span><span class="token punctuation">(</span><span class="token number">1000L</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">return</span> <span class="token class-name">CompletableFuture</span><span class="token punctuation">.</span><span class="token function">completedFuture</span><span class="token punctuation">(</span>results<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>GitHubLookupService</code> 클래스는 Spring의 <code>RestTemplate</code>을 사용하여 원격 REST 지점(api.github.com/users/)을 호출한 다음 응답을 <code>User</code> 객체로 변환합니다. Spring Boot는 자동 구성 비트(즉, <code>MessageConverter</code>)를 사용하여 기본값을 사용자 정의하는 <code>RestTemplateBuilder</code>를 자동으로 제공합니다.</p>
<p>클래스는 <code>@Service</code> 주석으로 표시되어 애플리케이션 컨텍스트를 감지하고 추가하기 위한 Spring의 구성 요소 검색 후보가 됩니다.</p>
<p><code>findUser</code> 메소드는 Spring의 <code>@Async</code> 주석으로 플래그 지정되어 별도의 스레드에서 실행되어야 함을 나타냅니다. 메서드의 반환 유형은 모든 비동기 서비스에 대한 요구 사항인 <code>User</code> 대신 <code>CompletableFuture&lt;User&gt;</code>입니다. 이 코드는<code>completeFuture</code> 메서드를 사용하여 <code>GitHub</code> 쿼리 결과로 이미 완료된 <code>CompletableFuture</code> 인스턴스를 반환합니다.</p>
<blockquote>
<p><code>GitHubLookupService</code> 클래스의 로컬 인스턴스를 생성해도 <code>findUser</code> 메서드가 비동기적으로 실행되는 것은 허용되지 않습니다. <code>@Configuration</code> 클래스 내에서 생성되거나 <code>@ComponentScan</code>에 의해 선택되어야 합니다.</p>
</blockquote>
<p>GitHub API의 출시 시기는 다양할 수 있습니다. 이 가이드의 뒷부분에서 이점을 설명하기 위해 이 서비스에 1초의 추가 지연이 추가되었습니다.</p>
<hr>
<p>"자동 구성 비트"는 Spring Boot에서 제공하는 기능 중 하나로, 설정을 자동으로 처리하고 설정의 일부를 자동으로 구성하는 기능을 가리킵니다.</p>
<p>Spring Boot는 애플리케이션을 구동할 때 많은 부분을 자동으로 설정해줍니다. 예를 들어, <code>RestTemplateBuilder</code>를 이용하여 <code>RestTemplate</code>을 생성할 때, 이 <code>RestTemplate</code>은 기본적으로 HTTP 요청을 보내고 받을 때 사용하는 <code>MessageConverter</code>를 가지고 있습니다.</p>
<p><strong>메시지 컨버터(Message Converter)</strong> 는 HTTP 요청과 응답 본문의 데이터를 변환하는 역할을 합니다. 예를 들어, JSON 형식의 데이터를 객체로 변환하거나, 객체를 JSON으로 변환하는 과정에서 사용됩니다. Spring에서는 이러한 변환 작업을 위해 <code>HttpMessageConverter</code> 인터페이스를 사용합니다. JSON 데이터를 처리하기 위해 Jackson 라이브러리를 사용하는 것도 그 중 하나입니다.</p>
<p><code>RestTemplateBuilder</code>는 Spring Boot에서 제공하는 빌더 클래스로, 여기에는 기본적으로 필요한 메시지 컨버터 등을 설정한 <code>RestTemplate</code>을 생성하는데 사용됩니다. 이 빌더를 사용하면 <code>RestTemplate</code>을 구성하고, 자동으로 필요한 컨버터를 포함시키는 등의 설정을 간단하게 처리할 수 있습니다.</p>
<hr>
<p>REST 호출을 통해 데이터를 가져오는 작업은 일반적으로 동기적으로 수행되며, 결과를 기다린 후에 그 결과를 반환하게 됩니다. 따라서 이 예제에서는 CompletableFuture를 사용하지 않고, 단순히 REST 호출을 통해 데이터를 가져와서 User 객체로 변환하는 작업만으로 충분할 수 있습니다.</p>
<h2 id="애플리케이션을-실행-가능하게-만들기">애플리케이션을 실행 가능하게 만들기</h2>
<p>샘플을 실행하려면 실행 가능한 jar을 생성하면 됩니다. Spring의 <code>@Async</code> 주석은 웹 애플리케이션과 함께 작동하지만 그 이점을 확인하기 위해 웹 컨테이너를 설정할 필요는 없습니다. 다음 목록(src/main/java/guides/asyncmethod/AsyncMethodApplication.java)에서는 이를 수행하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>asyncmethod</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Bean</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>scheduling<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">EnableAsync</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>scheduling<span class="token punctuation">.</span>concurrent</span><span class="token punctuation">.</span><span class="token class-name">ThreadPoolTaskExecutor</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>concurrent</span><span class="token punctuation">.</span><span class="token class-name">Executor</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>concurrent</span><span class="token punctuation">.</span><span class="token class-name">ThreadPoolExecutor</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token annotation punctuation">@EnableAsync</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AsyncMethodApplication</span> <span class="token punctuation">{</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token comment">// close the application context to shut down the custom ExecutorService</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">AsyncMethodApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">close</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Bean</span>
    <span class="token keyword">public</span> <span class="token class-name">Executor</span> <span class="token function">taskExecutor</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">ThreadPoolTaskExecutor</span> executor <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ThreadPoolTaskExecutor</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        executor<span class="token punctuation">.</span><span class="token function">setCorePoolSize</span><span class="token punctuation">(</span><span class="token number">2</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        executor<span class="token punctuation">.</span><span class="token function">setMaxPoolSize</span><span class="token punctuation">(</span><span class="token number">2</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        executor<span class="token punctuation">.</span><span class="token function">setQueueCapacity</span><span class="token punctuation">(</span><span class="token number">500</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        executor<span class="token punctuation">.</span><span class="token function">setThreadNamePrefix</span><span class="token punctuation">(</span><span class="token string">"GithubLookup-"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        executor<span class="token punctuation">.</span><span class="token function">initialize</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        
        <span class="token keyword">return</span> executor<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<blockquote>
<p>Spring Initializr는 <code>AsyncMethodApplication</code> 클래스를 생성했습니다. Spring Initializr(<code>src/main/java/guides/asyncmethod/AsyncMethodApplication.java</code>)에서 다운로드한 zip 파일에서 찾을 수 있습니다. 해당 클래스를 프로젝트에 복사한 다음 수정하거나 이전 목록에서 클래스를 복사할 수 있습니다.</p>
</blockquote>
<p><code>@EnableAsync</code> 주석은 백그라운드 스레드 풀에서 <code>@Async</code> 메서드를 실행하는 Spring의 기능을 활성화합니다. 이 클래스는 또한 새로운 Bean을 정의하여 <code>Executor</code>를 사용자 정의합니다. 여기서 메소드 이름은 <code>taskExecutor</code>입니다. 이는 Spring이 검색하는 특정 메소드 이름이기 때문입니다. 우리의 경우에는 동시 스레드 수를 2개로 제한하고 대기열 크기를 500개로 제한하려고 합니다. 조정할 수 있는 항목이 더 많이 있습니다. <code>Executor</code> 빈을 정의하지 않으면 Spring은 <code>ThreadPoolTaskExecutor</code>를 사용합니다.</p>
<hr>
<p>멀티쓰레드와 쓰레드 풀은 다음과 같이 구분됩니다:</p>
<ol>
<li>
<p><strong>멀티쓰레드 (Multithreading):</strong></p>
<ul>
<li>멀티쓰레딩은 하나의 프로세스 내에서 여러 쓰레드를 동시에 실행하는 것을 가리킵니다. 즉, 하나의 프로그램이 동시에 여러 작업을 처리할 수 있도록 하는 것입니다.</li>
<li>멀티쓰레드를 사용하면 여러 작업을 동시에 처리하여 응용 프로그램의 성능을 향상시키거나, 다양한 작업을 병렬로 수행할 수 있습니다.</li>
<li>각 쓰레드는 독립적으로 실행될 수 있고, 각각의 쓰레드는 자신의 스택을 가지고 있으며, 서로 공유되는 자원을 사용할 때에는 동기화 등의 작업이 필요합니다.</li>
</ul>
</li>
<li>
<p><strong>쓰레드 풀 (Thread Pool):</strong></p>
<ul>
<li>쓰레드 풀은 미리 생성된 쓰레드들의 집합을 말합니다. 이 쓰레드들은 일정한 작업을 처리하기 위해 대기하고 있습니다.</li>
<li>일반적으로 애플리케이션에서 직접 쓰레드를 생성하는 것보다 쓰레드 풀을 사용하는 것이 더 효율적입니다. 왜냐하면 쓰레드 생성 비용이 크기 때문에 미리 쓰레드들을 만들어놓고 필요할 때마다 재사용하는 것이 좋기 때문입니다.</li>
<li>쓰레드 풀은 쓰레드의 수를 관리하고, 쓰레드가 작업을 처리하는 방식을 조절할 수 있습니다. 예를 들어, 쓰레드 풀은 쓰레드의 최대 개수, 쓰레드의 최소 개수, 작업 큐의 크기 등을 설정할 수 있습니다.</li>
</ul>
</li>
</ol>
<p>간단하게 말하자면, 멀티쓰레딩은 여러 작업을 동시에 처리하는 개념이고, 쓰레드 풀은 멀티쓰레딩을 구현하고 관리하는 방법 중 하나입니다. 쓰레드 풀은 쓰레드의 생성과 관리를 추상화하여 효율적인 멀티쓰레드 환경을 제공합니다.</p>
<hr>
<ol>
<li>
<p><strong>@EnableAsync와 @Async 차이:</strong></p>
<ul>
<li><code>@EnableAsync</code>: 이 어노테이션은 Spring 애플리케이션에서 비동기 작업을 지원하도록 활성화합니다. 이를 통해 <code>@Async</code> 어노테이션을 메서드에 적용하여 해당 메서드를 비동기적으로 실행할 수 있습니다.</li>
<li><code>@Async</code>: 이 어노테이션은 메서드에 적용되며, 해당 메서드를 비동기적으로 실행하도록 지시합니다. 메서드가 호출될 때, 호출자는 해당 메서드의 결과를 기다리지 않고 다음 코드를 실행할 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>Executor, ThreadPoolTaskExecutor:</strong></p>
<ul>
<li><code>Executor</code>: Java에서 인터페이스로, 비동기적인 작업을 실행하기 위한 범용적인 인터페이스입니다. <code>Executor</code>를 구현한 클래스는 작업을 받아서 적절한 방식으로 실행합니다.</li>
<li><code>ThreadPoolTaskExecutor</code>: Spring이 제공하는 <code>Executor</code>의 구현체 중 하나입니다. 스레드 풀을 관리하고 스레드들을 만들어서 비동기 작업을 처리합니다. 설정에 따라 스레드 풀의 크기, 큐 용량, 스레드 이름 접두사 등을 지정할 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>main 함수에서 close 하는 이유:</strong></p>
<ul>
<li><code>SpringApplication.run().close()</code>를 호출하는 것은 애플리케이션 컨텍스트를 시작한 후에 즉시 애플리케이션을 종료시키는 역할을 합니다. 여기서 이 작업은 <code>SpringApplication.run()</code>이 호출되면서 애플리케이션을 시작하고, 별도의 HTTP 서버를 시작하지 않고 즉시 종료하기 위한 용도로 사용되었습니다.</li>
<li>주로 테스트 목적이나 특정한 데모 시나리오 등에서 사용되며, 실제 애플리케이션에서는 사용되지 않는 경우가 많습니다. 이 코드는 단지 예제를 빠르게 실행하고 종료하기 위한 목적으로 사용되었을 가능성이 있습니다.</li>
</ul>
</li>
</ol>
<hr>
<p><code>GitHubLookupService</code>를 주입하고 해당 서비스를 세 번 호출하여 메서드가 비동기적으로 실행된다는 것을 보여주는 <code>CommandLineRunner</code>도 있습니다.</p>
<p>애플리케이션을 실행하려면 클래스도 필요합니다. <code>src/main/java/guides/asyncmethod/AppRunner.java</code>에서 찾을 수 있습니다. 다음 목록은 해당 클래스를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>asyncmethod</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>slf4j</span><span class="token punctuation">.</span><span class="token class-name">Logger</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>slf4j</span><span class="token punctuation">.</span><span class="token class-name">LoggerFactory</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">CommandLineRunner</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype</span><span class="token punctuation">.</span><span class="token class-name">Component</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>concurrent</span><span class="token punctuation">.</span><span class="token class-name">CompletableFuture</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Component</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppRunner</span> <span class="token keyword">implements</span> <span class="token class-name">CommandLineRunner</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token class-name">Logger</span> logger <span class="token operator">=</span>
            <span class="token class-name">LoggerFactory</span><span class="token punctuation">.</span><span class="token function">getLogger</span><span class="token punctuation">(</span><span class="token class-name">AppRunner</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">GitHubLookupService</span> gitHubLookupService<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">AppRunner</span><span class="token punctuation">(</span><span class="token class-name">GitHubLookupService</span> gitHubLookupService<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>gitHubLookupService <span class="token operator">=</span> gitHubLookupService<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> args<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
        
        <span class="token comment">//Start the clock</span>
        <span class="token keyword">long</span> start <span class="token operator">=</span> <span class="token class-name">System</span><span class="token punctuation">.</span><span class="token function">currentTimeMillis</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        
        <span class="token comment">//Kick of multiple, asynchronous lookups</span>
        <span class="token class-name">CompletableFuture</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> page1 <span class="token operator">=</span> gitHubLookupService<span class="token punctuation">.</span><span class="token function">findUser</span><span class="token punctuation">(</span><span class="token string">"PivotalSoftware"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">CompletableFuture</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> page2 <span class="token operator">=</span> gitHubLookupService<span class="token punctuation">.</span><span class="token function">findUser</span><span class="token punctuation">(</span><span class="token string">"CloudFoundry"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">CompletableFuture</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> page3 <span class="token operator">=</span> gitHubLookupService<span class="token punctuation">.</span><span class="token function">findUser</span><span class="token punctuation">(</span><span class="token string">"Spring-Projects"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        
        <span class="token comment">//Wait until they are all done</span>
        <span class="token class-name">CompletableFuture</span><span class="token punctuation">.</span><span class="token function">allOf</span><span class="token punctuation">(</span>page1<span class="token punctuation">,</span> page2<span class="token punctuation">,</span> page3<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">join</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        
        <span class="token comment">//Print results, including elapsed time</span>
        logger<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"Elapsed time: "</span><span class="token operator">+</span><span class="token punctuation">(</span><span class="token class-name">System</span><span class="token punctuation">.</span><span class="token function">currentTimeMillis</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">-</span> start<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        logger<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"--&gt; "</span><span class="token operator">+</span>page1<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        logger<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"--&gt; "</span><span class="token operator">+</span>page2<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        logger<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"--&gt; "</span><span class="token operator">+</span>page3<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>애플리케이션은 GitHub에 대한 각 쿼리를 보여주는 로깅 출력을 표시합니다. <code>allOf</code> 팩토리 메소드의 도움으로 <code>CompletableFuture</code> 객체의 배열을 생성합니다. <code>join</code> 메소드를 호출하면 모든 <code>CompletableFuture</code> 객체가 완료될 때까지 기다릴 수 있습니다.</p>
<p>다음 목록은 이 샘플 애플리케이션의 일반적인 출력을 보여줍니다.</p>
<p>처음 두 호출은 별도의 스레드(<code>GithubLookup-2</code>, <code>GithubLookup-1</code>)에서 발생하고 세 번째 호출은 두 스레드 중 하나를 사용할 수 있을 때까지 보류됩니다. 비동기 기능 없이 이 작업에 걸리는 시간을 비교하려면 <code>@Async</code> 애너테이션을 주석 처리하고 서비스를 다시 실행해 보세요. 각 쿼리에 최소 1초가 걸리므로 총 경과 시간이 눈에 띄게 늘어납니다. 예를 들어 <code>Executor</code>를 조정하여 <code>corePoolSize</code> 속성을 늘릴 수도 있습니다.</p>
<p>기본적으로 작업 시간이 길어지고 더 많은 작업이 동시에 호출될수록 비동기화를 통해 더 많은 이점을 얻을 수 있습니다. 단점은 <code>CompletableFuture</code> 인터페이스를 처리하는 것입니다. 더 이상 결과를 직접 처리하지 않기 때문에 간접 계층이 추가됩니다.</p>
<hr>
<p>더 이상 결과를 직접 처리하지 않기 때문에 간접 계층이 추가됩니다"라는 문장은 CompletableFuture를 사용할 때의 상황을 설명하고 있는데, CompletableFuture는 비동기적인 작업의 결과를 가지고 있는 객체입니다. 이 객체를 통해 비동기 작업의 결과를 가져오거나 조합하는 등의 작업을 할 수 있습니다. 따라서 CompletableFuture를 통해 작업을 처리함으로써 결과를 직접적으로 다루는 것이 아니라 간접적인 방식으로 처리하게 됩니다. 결과적으로 이러한 간접적인 처리 방식으로 인해 코드에 일종의 계층이 추가된다고 설명한 것입니다.</p>
<hr>
<p>실제로 <code>main</code> 함수에서 애플리케이션 컨텍스트를 시작한 후 즉시 종료시키는 코드는 이후에 있는 <code>AppRunner</code> 클래스의 <code>run</code> 메서드를 실행하지 못할 수 있습니다. 위 코드에서 <code>AppRunner</code> 클래스는 <code>CommandLineRunner</code>를 구현하고 있어서, Spring Boot 애플리케이션이 시작될 때 <code>run</code> 메서드가 자동으로 실행됩니다.</p>
<p>하지만, <code>SpringApplication.run().close()</code>를 통해 애플리케이션 컨텍스트를 시작한 후 즉시 종료시키는 코드는 <code>AppRunner</code> 클래스의 <code>run</code> 메서드가 실행되는 시점 이후에 애플리케이션을 종료시키므로, <code>run</code> 메서드 내부의 비동기적인 작업들이 완료되기 전에 애플리케이션이 종료될 수 있습니다.</p>
<p>따라서 실제로 <code>AppRunner</code> 클래스의 <code>run</code> 메서드에 있는 비동기 작업들이 완료되기 전에 애플리케이션이 종료되는 상황입니다. 이 경우, <code>CompletableFuture</code>를 사용하여 비동기적인 작업을 수행하고 그 결과를 가져오는 코드가 실행되지 않을 수 있습니다.</p>
<h2 id="전체-실행-과정">전체 실행 과정</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/a4f9dfde-81c4-4d13-bbcb-1fdd461c5742/image.png"></p>
<ol>
<li>
<p><strong>애플리케이션 초기화:</strong></p>
<ul>
<li><code>AsyncMethodApplication</code> 클래스에서 <code>@SpringBootApplication</code>과 <code>@EnableAsync</code>가 활성화됩니다.</li>
<li><code>Executor</code> 빈이 정의되어 쓰레드 풀을 설정합니다.</li>
</ul>
</li>
<li>
<p><strong>비동기 메서드 호출:</strong></p>
<ul>
<li><code>AppRunner</code> 클래스의 <code>run</code> 메서드에서 <code>GitHubLookupService</code>의 <code>findUser</code> 메서드를 비동기적으로 호출합니다.</li>
<li>비동기 메서드 <code>findUser</code>는 <code>@Async</code> 어노테이션이 적용되어 별도의 쓰레드에서 실행됩니다.</li>
<li><code>CompletableFuture</code>를 반환하며, 이를 통해 비동기 작업의 결과를 추적하고 처리할 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>비동기 작업 수행:</strong></p>
<ul>
<li><code>findUser</code> 메서드 내부에서는 <code>RestTemplate</code>을 사용하여 GitHub API에 HTTP 요청을 보냅니다.</li>
<li>해당 요청은 별도의 스레드에서 비동기적으로 실행됩니다.</li>
<li>이 때, <code>Thread.sleep(1000L)</code>이 사용되어 1초간의 인위적인 지연이 발생합니다.</li>
</ul>
</li>
<li>
<p><strong>비동기 작업 결과 대기 및 처리:</strong></p>
<ul>
<li><code>AppRunner</code> 클래스에서는 비동기적으로 호출한 <code>findUser</code> 메서드의 결과를 <code>CompletableFuture.allOf(page1, page2, page3).join()</code>를 사용하여 대기합니다.</li>
<li><code>CompletableFuture.allOf()</code>는 모든 비동기 작업이 완료될 때까지 기다립니다.</li>
<li>이후 각각의 비동기 작업 결과를 가져와 로깅하며, 실행 시간을 측정하여 출력합니다.</li>
</ul>
</li>
<li>
<p><strong>결과 처리 및 로깅:</strong></p>
<ul>
<li><code>AppRunner</code> 클래스에서는 비동기 작업의 결과와 실행 시간을 로깅합니다.</li>
<li>각 비동기 작업의 결과를 가져와서 로깅되며, 실행 시간을 출력합니다.</li>
</ul>
</li>
</ol>
<p>이렇게 <code>@Async</code>와 <code>CompletableFuture</code>를 이용하여 비동기적으로 메서드를 호출하고 결과를 처리하게 됩니다. <code>@Async</code>는 메서드를 비동기적으로 실행하기 위해 프록시를 만들고, 이를 통해 별도의 쓰레드에서 비동기 작업을 처리하고 완료될 때까지 대기하는 구조입니다.</p>