<p>이 가이드는 반응형 데이터베이스 드라이버를 사용하여 관계형 데이터베이스에 데이터를 저장하고 검색하기 위해 Spring Data R2DBC를 사용하는 애플리케이션을 구축하는 과정을 안내합니다.</p>
<hr>
<p>R2DBC는 "Reactive Relational Database Connectivity"의 약자로, 반응형 프로그래밍 스타일을 지원하는 비동기적인 자바 프레임워크입니다. 이 프레임워크는 관계형 데이터베이스와의 상호 작용을 위한 비동기 API를 제공하여, 기존의 JDBC(Java Database Connectivity)의 블로킹 방식 대신에 비동기적인 방식으로 데이터베이스와 통신할 수 있게 합니다. 이는 대규모 또는 고성능 애플리케이션에서 더 효율적인 I/O 처리를 가능하게 합니다. 이러한 비동기 접근 방식은 대규모 동시 요청을 처리할 때 성능 및 확장성을 향상시킬 수 있습니다.</p>
<hr>
<h2 id="무엇을-구축할-것인가">무엇을 구축할 것인가</h2>
<p>메모리 기반 데이터베이스에 <code>Customer</code> POJO(Plain Old Java Objects)를 저장하는 애플리케이션을 구축합니다.</p>
<h3 id="buildgradle">build.gradle</h3>
<pre><code class="language-null">plugins {
	id 'java'
	id 'org.springframework.boot' version '3.2.2'
	id 'io.spring.dependency-management' version '1.1.4'
}

group = 'guide'
version = '0.0.1-SNAPSHOT'

java {
	sourceCompatibility = '17'
}

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-data-r2dbc'
	runtimeOnly 'com.h2database:h2'
	runtimeOnly 'io.r2dbc:r2dbc-h2'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
	testImplementation 'io.projectreactor:reactor-test'
}

tasks.named('test') {
	useJUnitPlatform()
}</code></pre>
<h2 id="define-a-schema">Define a Schema</h2>
<p>이 예에서는 각각 R2DBC 엔터티로 주석(annotated)이 달린 <code>Customer</code> 개체를 저장합니다. 다음 목록은 SQL 스키마 클래스(<code>src/main/resources/schema.sql</code>에 있음)를 보여줍니다.</p>
<pre><code class="language-sql"><span class="token keyword">CREATE</span> <span class="token keyword">TABLE</span> <span class="token keyword">IF</span> <span class="token operator">NOT</span> <span class="token keyword">EXISTS</span> customer <span class="token punctuation">(</span>id <span class="token keyword">SERIAL</span> <span class="token keyword">PRIMARY</span> <span class="token keyword">KEY</span><span class="token punctuation">,</span> first_name <span class="token keyword">VARCHAR</span><span class="token punctuation">(</span><span class="token number">255</span><span class="token punctuation">)</span><span class="token punctuation">,</span> last_name <span class="token keyword">VARCHAR</span><span class="token punctuation">(</span><span class="token number">255</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>여기에는 <code>id</code>, <code>first_name</code> 및 <code>last_name</code>이라는 세 개의 열이 있는 <code>customer</code> 테이블이 있습니다. <code>id</code> 열은 자동으로 증가(auto-incremented)하며, 다른 열은 기본 스네이크 케이스 명명 체계를 따릅니다. 나중에 데이터베이스 스키마를 초기화하기 위해 애플리케이션 시작 중에 <code>schema.sql</code> 파일을 선택(pick up)하려면 <code>ConnectionFactoryInitializer</code>를 등록해야 합니다. H2 드라이버가 클래스 경로에 있고 연결 URL을 지정하지 않았기 때문에 Spring Boot는 내장된 H2 데이터베이스를 시작합니다.</p>
<hr>
<p>H2 데이터베이스가 클래스 경로에 있는 경우와 <code>ConnectionFactoryInitializer</code>를 등록하지 않으면 <code>schema.sql</code>을 선택하지 못하는 이유는 스프링 부트의 자동 설정 메커니즘과 관련이 있습니다.</p>
<p>스프링 부트는 클래스 경로에 특정 데이터베이스 드라이버가 존재하면 해당 데이터베이스를 사용할 수 있는 것으로 간주합니다. 따라서 H2 데이터베이스 드라이버가 클래스 경로에 있으면 스프링 부트는 내장 H2 데이터베이스를 사용하도록 설정됩니다.</p>
<p>그러나 <code>ConnectionFactoryInitializer</code>를 등록하지 않으면 스프링 부트는 자동으로 데이터베이스 스키마 초기화를 수행하지 않습니다. <code>ConnectionFactoryInitializer</code>는 스프링 데이터 R2DBC에서 제공하는 기능 중 하나로, 애플리케이션 시작 시 데이터베이스 스키마를 초기화하기 위해 사용됩니다. 이를 통해 <code>schema.sql</code> 파일을 실행하여 데이터베이스의 초기 스키마를 설정할 수 있습니다.</p>
<p>따라서 <code>ConnectionFactoryInitializer</code>를 등록하면 스프링 부트는 내장 H2 데이터베이스를 사용하고, <code>schema.sql</code> 파일을 찾아서 초기화할 수 있습니다. 반면에 <code>ConnectionFactoryInitializer</code>를 등록하지 않으면 스프링 부트는 데이터베이스 스키마 초기화를 수행하지 않으므로 <code>schema.sql</code> 파일이 사용되지 않습니다.</p>
<hr>
<h2 id="define-a-simple-entity">Define a Simple Entity</h2>
<p>이 예에서는 각각 R2DBC 엔터티로 주석이 달린 <code>Customer</code> 개체를 저장합니다. 다음 목록은 Customer 클래스(src/main/java/com/example/accessingdatar2dbc/Customer.java에 있음)를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">com<span class="token punctuation">.</span>example<span class="token punctuation">.</span>accessingdatar2dbc</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Id</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Customer</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Id</span>
    <span class="token keyword">private</span> <span class="token class-name">Long</span> id<span class="token punctuation">;</span>

    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">String</span> firstName<span class="token punctuation">;</span>

    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">String</span> lastName<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token class-name">String</span> firstName<span class="token punctuation">,</span> <span class="token class-name">String</span> lastName<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>firstName <span class="token operator">=</span> firstName<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>lastName <span class="token operator">=</span> lastName<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token class-name">Long</span> <span class="token function">getId</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>id<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setId</span><span class="token punctuation">(</span><span class="token class-name">Long</span> id<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>id <span class="token operator">=</span> id<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getFirstName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>firstName<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getLastName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>lastName<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token class-name">String</span><span class="token punctuation">.</span><span class="token function">format</span><span class="token punctuation">(</span>
            <span class="token string">"Customer[id=%d, firstName='%s', lastName='%s']"</span><span class="token punctuation">,</span>
            id<span class="token punctuation">,</span> firstName<span class="token punctuation">,</span> lastName<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<p>여기에는 <code>id</code>, <code>firstName</code> 및 <code>lastName</code>의 세 가지 속성이 있는 <code>Customer</code> 클래스가 있습니다. <code>Customer</code> 클래스에는 최소한의 주석(annotated)이 추가됩니다. <code>id</code> 속성에는 <code>@Id</code>라는 주석이 달려 있어 Spring Data R2DBC가 기본 키를 식별할 수 있습니다. 기본적으로 기본 키는 <code>INSERT</code> 시 데이터베이스에 의해 생성되는 것으로 가정됩니다.</p>
<p>다른 두 속성인 <code>firstName</code>과 <code>lastName</code>은 주석이 추가되지 않은 상태로 유지됩니다. 속성 자체와 동일한 이름을 공유하는 열에 매핑된다고 가정합니다.</p>
<p>편리한 <code>toString()</code> 메소드는 고객의 속성을 인쇄합니다.</p>
<h2 id="create-simple-queries">Create Simple Queries</h2>
<p>Spring Data R2DBC는 R2DBC를 관계형 데이터베이스에 데이터를 저장하는 기본 기술로 사용하는 데 중점을 둡니다. 가장 강력한 기능은 저장소 인터페이스에서 런타임에 저장소 구현을 생성하는 기능입니다.</p>
<p>이것이 어떻게 작동하는지 보려면 다음 목록(src/main/java/com/example/accessingdatar2dbc/CustomerRepository.java)에 표시된 대로 고객 엔터티와 작동하는 저장소 인터페이스를 만듭니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>r2dbc<span class="token punctuation">.</span>repository</span><span class="token punctuation">.</span><span class="token class-name">Query</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>repository<span class="token punctuation">.</span>reactive</span><span class="token punctuation">.</span><span class="token class-name">ReactiveCrudRepository</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">reactor<span class="token punctuation">.</span>core<span class="token punctuation">.</span>publisher</span><span class="token punctuation">.</span><span class="token class-name">Flux</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">CustomerRepository</span> <span class="token keyword">extends</span> <span class="token class-name">ReactiveCrudRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Customer</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Query</span> <span class="token punctuation">(</span><span class="token string">"SELECT * FROM customer WHERE last_name = :lastname"</span><span class="token punctuation">)</span>
    <span class="token class-name">Flux</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Customer</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastName</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastName<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p><code>CustomerRepository</code>는 <code>ReactiveCrudRepository</code> 인터페이스를 확장합니다. 작업하는 엔터티 유형과 ID인 <code>Customer</code> 및 <code>Long</code>은 <code>ReactiveCrudRepository</code>의 generic 매개변수에 지정됩니다. <code>ReactiveCrudRepository</code>를 확장함으로써 <code>CustomerRepository</code>는 reactive 유형을 사용하여 <code>Customer</code> 엔터티를 저장, 삭제 및 찾는 방법을 포함하여 <code>Customer</code> persistence 작업을 위한 여러 방법을 상속합니다.</p>
<p>Spring Data R2DBC를 사용하면 <code>@Query</code>로 주석을 달아 다른 쿼리 메서드를 정의할 수도 있습니다. 예를 들어 <code>CustomerRepository</code>에는 <code>findByLastName()</code> 메서드가 포함되어 있습니다.</p>
<p>일반적인 Java 애플리케이션에서는 <code>CustomerRepository</code>를 구현하는 클래스를 작성할 것으로 예상할 수 있습니다. 그러나 이것이 Spring Data R2DBC를 매우 강력하게 만드는 이유입니다. 저장소 인터페이스의 구현을 작성할 필요가 없습니다. Spring Data R2DBC는 애플리케이션을 실행할 때 구현을 생성합니다.</p>
<p>이제 이 예제를 연결하고 어떻게 보이는지 확인할 수 있습니다!</p>
<h2 id="create-an-application-class">Create an Application Class</h2>
<p>Spring initializr는 애플리케이션을 위한 간단한 클래스를 생성합니다. 다음 목록은 이 예제를 위해 Initializr가 생성한 클래스(src/main/java/com/example/accessingdatar2dbc/AccessingDataR2dbcApplication.java)를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">com<span class="token punctuation">.</span>example<span class="token punctuation">.</span>accessingdatar2dbc</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AccessingDataR2dbcApplication</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">AccessingDataR2dbcApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p>이제 Initializr가 생성한 간단한 클래스를 수정해야 합니다. (이 예에서는 콘솔로) 출력을 얻으려면 로거를 설정해야 합니다. 그런 다음 초기화 프로그램을 설정하여 스키마와 일부 데이터를 설정하고 이를 사용하여 출력을 생성해야 합니다. 다음 목록은 완성된 AccessingDataR2dbcApplication 클래스(src/main/java/com/example/accessingdatar2dbc/AccessingDataR2dbcApplication.java에 있음)를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">com<span class="token punctuation">.</span>example<span class="token punctuation">.</span>accessingdatar2dbc</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">io<span class="token punctuation">.</span>r2dbc<span class="token punctuation">.</span>spi</span><span class="token punctuation">.</span><span class="token class-name">ConnectionFactory</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>slf4j</span><span class="token punctuation">.</span><span class="token class-name">Logger</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>slf4j</span><span class="token punctuation">.</span><span class="token class-name">LoggerFactory</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">CommandLineRunner</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Bean</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>core<span class="token punctuation">.</span>io</span><span class="token punctuation">.</span><span class="token class-name">ClassPathResource</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>r2dbc<span class="token punctuation">.</span>connection<span class="token punctuation">.</span>init</span><span class="token punctuation">.</span><span class="token class-name">ConnectionFactoryInitializer</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>r2dbc<span class="token punctuation">.</span>connection<span class="token punctuation">.</span>init</span><span class="token punctuation">.</span><span class="token class-name">ResourceDatabasePopulator</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>time</span><span class="token punctuation">.</span><span class="token class-name">Duration</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util</span><span class="token punctuation">.</span><span class="token class-name">Arrays</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AccessingDataR2dbcApplication</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token class-name">Logger</span> log <span class="token operator">=</span> <span class="token class-name">LoggerFactory</span><span class="token punctuation">.</span><span class="token function">getLogger</span><span class="token punctuation">(</span><span class="token class-name">AccessingDataR2dbcApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">AccessingDataR2dbcApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Bean</span>
    <span class="token keyword">public</span> <span class="token class-name">CommandLineRunner</span> <span class="token function">demo</span><span class="token punctuation">(</span><span class="token class-name">CustomerRepository</span> repository<span class="token punctuation">)</span> <span class="token punctuation">{</span>

        <span class="token keyword">return</span> <span class="token punctuation">(</span>args<span class="token punctuation">)</span> <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
            <span class="token comment">// save a few customers</span>
            repository<span class="token punctuation">.</span><span class="token function">saveAll</span><span class="token punctuation">(</span><span class="token class-name">Arrays</span><span class="token punctuation">.</span><span class="token function">asList</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token string">"Jack"</span><span class="token punctuation">,</span> <span class="token string">"Bauer"</span><span class="token punctuation">)</span><span class="token punctuation">,</span>
                <span class="token keyword">new</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token string">"Chloe"</span><span class="token punctuation">,</span> <span class="token string">"O'Brian"</span><span class="token punctuation">)</span><span class="token punctuation">,</span>
                <span class="token keyword">new</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token string">"Kim"</span><span class="token punctuation">,</span> <span class="token string">"Bauer"</span><span class="token punctuation">)</span><span class="token punctuation">,</span>
                <span class="token keyword">new</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token string">"David"</span><span class="token punctuation">,</span> <span class="token string">"Palmer"</span><span class="token punctuation">)</span><span class="token punctuation">,</span>
                <span class="token keyword">new</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token string">"Michelle"</span><span class="token punctuation">,</span> <span class="token string">"Dessler"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">blockLast</span><span class="token punctuation">(</span><span class="token class-name">Duration</span><span class="token punctuation">.</span><span class="token function">ofSeconds</span><span class="token punctuation">(</span><span class="token number">10</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

            <span class="token comment">// fetch all customers</span>
            log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"Customers found with findAll():"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"-------------------------------"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            repository<span class="token punctuation">.</span><span class="token function">findAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">doOnNext</span><span class="token punctuation">(</span>customer <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
                log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span>customer<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">blockLast</span><span class="token punctuation">(</span><span class="token class-name">Duration</span><span class="token punctuation">.</span><span class="token function">ofSeconds</span><span class="token punctuation">(</span><span class="token number">10</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

            log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">""</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

            <span class="token comment">// fetch an individual customer by ID</span>
			repository<span class="token punctuation">.</span><span class="token function">findById</span><span class="token punctuation">(</span><span class="token number">1L</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">doOnNext</span><span class="token punctuation">(</span>customer <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
				log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"Customer found with findById(1L):"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
				log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"--------------------------------"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
				log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span>customer<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
				log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">""</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			<span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">block</span><span class="token punctuation">(</span><span class="token class-name">Duration</span><span class="token punctuation">.</span><span class="token function">ofSeconds</span><span class="token punctuation">(</span><span class="token number">10</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>


            <span class="token comment">// fetch customers by last name</span>
            log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"Customer found with findByLastName('Bauer'):"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"--------------------------------------------"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            repository<span class="token punctuation">.</span><span class="token function">findByLastName</span><span class="token punctuation">(</span><span class="token string">"Bauer"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">doOnNext</span><span class="token punctuation">(</span>bauer <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
                log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span>bauer<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">blockLast</span><span class="token punctuation">(</span><span class="token class-name">Duration</span><span class="token punctuation">.</span><span class="token function">ofSeconds</span><span class="token punctuation">(</span><span class="token number">10</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span><span class="token punctuation">;</span>
            log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">""</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p><code>AccessingDataR2dbcApplication</code> 클래스에는 몇 가지 테스트를 통해 <code>CustomerRepository</code>를 배치하는 <code>main()</code> 메서드가 포함되어 있습니다. 먼저 Spring 애플리케이션 컨텍스트에서 <code>CustomerRepository</code>를 가져옵니다. 그런 다음 소수의 <code>Customer</code> 개체를 저장하고 <code>save()</code> 메서드를 시연하고 사용할 일부 데이터를 설정합니다. 다음으로 <code>findAll()</code>을 호출하여 데이터베이스에서 모든 <code>Customer</code> 개체를 가져옵니다. 그런 다음 <code>findById()</code>를 호출하여 해당 ID로 단일 <code>Customer</code>를 가져옵니다. 마지막으로 <code>findByLastName()</code>을 호출하여 성이 "Bauer"인 모든 고객을 찾습니다.</p>
<p>R2DBC는 반응형(reactive) 프로그래밍 기술입니다. 동시에 우리는 동기화(synchronized)된 명령(imperative) 흐름에서 이를 사용하고 있으므로 각 호출을 <code>block(…)</code> 메서드 변형으로 동기화해야 합니다. 일반적인 반응형 애플리케이션에서 결과 <code>Mono</code> 또는 <code>Flux</code>는 호출 스레드를 차단하지 않고 반응 시퀀스를 구독하는 웹 컨트롤러 또는 이벤트 프로세서로 다시 전달되는 연산자의 파이프라인을 나타냅니다.</p>
<blockquote>
<p>기본적으로 Spring Boot는 R2DBC 저장소 지원을 활성화하고 <code>@SpringBootApplication</code>이 있는 패키지(및 해당 하위 패키지)를 찾습니다. 구성에 표시되지 않는 패키지에 R2DBC 저장소 인터페이스 정의가 있는 경우 <code>@EnableR2dbcRepositories</code> 및 해당 유형 안전 <code>basePackageClasses=MyRepository.class</code> 매개변수를 사용하여 대체 패키지를 가리킬 수 있습니다.</p>
</blockquote>
<h3 id="test">Test</h3>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>junit<span class="token punctuation">.</span>jupiter<span class="token punctuation">.</span>api</span><span class="token punctuation">.</span><span class="token class-name">Test</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>junit<span class="token punctuation">.</span>jupiter<span class="token punctuation">.</span>api<span class="token punctuation">.</span>extension</span><span class="token punctuation">.</span><span class="token class-name">ExtendWith</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Autowired</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>autoconfigure<span class="token punctuation">.</span>data<span class="token punctuation">.</span>r2dbc</span><span class="token punctuation">.</span><span class="token class-name">DataR2dbcTest</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>r2dbc<span class="token punctuation">.</span>core</span><span class="token punctuation">.</span><span class="token class-name">R2dbcEntityTemplate</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>r2dbc<span class="token punctuation">.</span>dialect</span><span class="token punctuation">.</span><span class="token class-name">H2Dialect</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>r2dbc<span class="token punctuation">.</span>core</span><span class="token punctuation">.</span><span class="token class-name">DatabaseClient</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>context<span class="token punctuation">.</span>junit<span class="token punctuation">.</span>jupiter</span><span class="token punctuation">.</span><span class="token class-name">SpringExtension</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">reactor<span class="token punctuation">.</span>core<span class="token punctuation">.</span>publisher</span><span class="token punctuation">.</span><span class="token class-name">Flux</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">reactor<span class="token punctuation">.</span>test</span><span class="token punctuation">.</span><span class="token class-name">StepVerifier</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>assertj<span class="token punctuation">.</span>core<span class="token punctuation">.</span>api</span><span class="token punctuation">.</span><span class="token class-name">Assertions</span><span class="token punctuation">.</span>assertThat<span class="token punctuation">;</span>

<span class="token annotation punctuation">@ExtendWith</span><span class="token punctuation">(</span><span class="token class-name">SpringExtension</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@DataR2dbcTest</span>
<span class="token keyword">class</span> <span class="token class-name">CustomerRepositoryTests</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token keyword">private</span> <span class="token class-name">DatabaseClient</span> databaseClient<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token keyword">private</span> <span class="token class-name">CustomerRepository</span> customers<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Test</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">testFindByLastName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">Customer</span> customer <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token string">"first"</span><span class="token punctuation">,</span> <span class="token string">"last"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token class-name">R2dbcEntityTemplate</span> template <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">R2dbcEntityTemplate</span><span class="token punctuation">(</span>databaseClient<span class="token punctuation">,</span> <span class="token class-name">H2Dialect</span><span class="token punctuation">.</span>INSTANCE<span class="token punctuation">)</span><span class="token punctuation">;</span>
		template<span class="token punctuation">.</span><span class="token function">insert</span><span class="token punctuation">(</span><span class="token class-name">Customer</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">using</span><span class="token punctuation">(</span>customer<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">then</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">as</span><span class="token punctuation">(</span><span class="token class-name">StepVerifier</span><span class="token operator">:</span><span class="token operator">:</span><span class="token function">create</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">verifyComplete</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token class-name">Flux</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Customer</span><span class="token punctuation">&gt;</span></span> findByLastName <span class="token operator">=</span> customers<span class="token punctuation">.</span><span class="token function">findByLastName</span><span class="token punctuation">(</span>customer<span class="token punctuation">.</span><span class="token function">getLastName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		findByLastName<span class="token punctuation">.</span><span class="token function">as</span><span class="token punctuation">(</span><span class="token class-name">StepVerifier</span><span class="token operator">:</span><span class="token operator">:</span><span class="token function">create</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">assertNext</span><span class="token punctuation">(</span>actual <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
			<span class="token function">assertThat</span><span class="token punctuation">(</span>actual<span class="token punctuation">.</span><span class="token function">getFirstName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEqualTo</span><span class="token punctuation">(</span><span class="token string">"first"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			<span class="token function">assertThat</span><span class="token punctuation">(</span>actual<span class="token punctuation">.</span><span class="token function">getLastName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEqualTo</span><span class="token punctuation">(</span><span class="token string">"last"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">verifyComplete</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<hr>
<p>이 코드는 스프링 부트와 리액티브 스프링 데이터 R2DBC를 사용하여 데이터베이스 작업을 테스트하는 코드입니다. 각 부분을 순서대로 분석해보겠습니다.</p>
<ol>
<li>
<p><code>@ExtendWith(SpringExtension.class)</code>: 이 어노테이션은 JUnit 5에서 스프링 테스트 확장을 지정합니다. 이것은 스프링 애플리케이션 컨텍스트를 테스트에 사용할 수 있도록 확장을 활성화합니다.</p>
</li>
<li>
<p><code>@DataR2dbcTest</code>: 이 어노테이션은 리액티브 R2DBC 데이터베이스 테스트를 위한 스프링 부트 테스트 슬라이스를 활성화합니다. 이것은 테스트를 실행하는 동안 리액티브 R2DBC 관련 빈들을 자동으로 구성하고 데이터베이스 연결을 설정합니다.</p>
</li>
<li>
<p><code>DatabaseClient</code>는 스프링 데이터 R2DBC에서 제공하는 리액티브한 데이터베이스 클라이언트입니다. 이를 사용하여 리액티브한 방식으로 데이터베이스에 대한 쿼리 및 작업을 실행할 수 있습니다.</p>
</li>
<li>
<p><code>@Autowired</code>를 사용할 수 있는 기준은 해당 필드나 메서드가 스프링 애플리케이션 컨텍스트에서 자동으로 주입될 수 있는 빈으로 등록되어 있어야 합니다. 이는 주로 스프링 컨테이너가 해당 빈을 찾을 수 있고, 타입에 맞는 빈을 주입할 수 있는 경우에 해당합니다.</p>
</li>
</ol>
<p>코드에서 <code>DatabaseClient</code>를 주입하기 위해 <code>@Autowired</code> 어노테이션을 사용했는데, 이것은 스프링 부트에서 기본적으로 리액티브 R2DBC를 지원하는 데이터베이스에 대한 클라이언트를 자동으로 설정합니다. 때문에 <code>DatabaseClient</code> 빈은 프로젝트에 명시적으로 정의되어 있지 않더라도 스프링 부트의 자동 구성으로 인해 사용 가능합니다.</p>
<p>스프링 부트는 클래스 패스에 있는 라이브러리와 기본적인 설정을 바탕으로 여러 가지 자동 구성을 제공합니다. 이에 따라 리액티브 R2DBC를 사용하는 경우, 스프링 부트가 기본적인 리액티브 데이터베이스 클라이언트를 자동으로 구성하여 사용할 수 있습니다. 따라서 <code>DatabaseClient</code>와 같은 리액티브 데이터베이스 관련 빈을 명시적으로 정의하지 않아도 됩니다.</p>
<ol start="3">
<li>
<p><code>H2Dialect.Instance</code>는 H2 데이터베이스를 위한 R2DBC 데이터베이스 다이얼렉트(Dialect)의 인스턴스를 의미합니다. 이것은 H2 데이터베이스에 특화된 SQL 문법이나 데이터베이스 기능을 사용하기 위해 사용됩니다.</p>
</li>
<li>
<p><code>StepVerifier</code>는 리액티브한 코드를 테스트하기 위한 리액티브 테스트 도구입니다. 리액티브 스트림의 각 이벤트를 검증하고 예상된 결과를 확인할 수 있습니다.</p>
</li>
<li>
<p><code>R2dbcEntityTemplate template = new R2dbcEntityTemplate(databaseClient, H2Dialect.INSTANCE)</code>: <code>R2dbcEntityTemplate</code>을 생성하고 H2 데이터베이스에 대한 R2DBC 연결을 설정합니다.</p>
</li>
<li>
<p><code>template.insert(Customer.class).using(customer).then().as(StepVerifier::create).verifyComplete()</code>: 새로운 Customer 객체를 데이터베이스에 삽입합니다.</p>
</li>
<li>
<p><code>Flux&lt;Customer&gt; findByLastName = customers.findByLastName(customer.getLastName())</code>: 리포지토리를 사용하여 마지막 이름에 해당하는 Customer를 찾습니다.</p>
</li>
<li>
<p><code>findByLastName.as(StepVerifier::create).assertNext(actual -&gt; { ... }).verifyComplete()</code>: <code>findByLastName</code> Flux를 구독하고 검증합니다. 예상된 결과와 일치하는지 확인합니다.</p>
</li>
</ol>
<p>이 코드는 리액티브 스프링 데이터 R2DBC를 사용하여 리액티브하게 데이터베이스 작업을 테스트하는 전형적인 방법을 보여줍니다.</p>
<h2 id="build-a-executable-jar">Build a executable JAR</h2>
<p>Gradle을 사용하는 경우 <code>./gradlew bootRun</code>을 사용하여 애플리케이션을 실행할 수 있습니다. 또는 다음과 같이 <code>./gradlew build</code>를 사용하여 JAR 파일을 빌드한 후 JAR 파일을 실행할 수 있습니다.</p>
<pre><code class="language-shell">java -jar build/libs/<span class="token punctuation">{</span>project_id<span class="token punctuation">}</span>-0.1.0.jar</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/74a2321b-b993-46e1-a355-0b147c7e509e/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/2be77334-b5ec-4af0-8166-49d4c4414667/image.png"></p>
<p>축하해요! 구체적인 저장소 구현을 작성하지 않고도 Spring Data R2DBC를 사용하여 데이터베이스에 객체를 저장하고 데이터베이스에서 가져오는 간단한 애플리케이션을 작성했습니다.</p>