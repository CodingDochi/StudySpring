<p>스프링을 이용하여 관계형 데이터에 접근하는 연습</p>
<h2 id="what-you-will-build">What You Will Build</h2>
<p>스프링 <code>JdbcTemplate</code>으로 관계형 데이터베이스에 저장된 데이터를 사용하는 애플리케이션을 빌드합니다</p>
<h2 id="starting-with-spring-initializr">Starting with Spring Initializr</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/f3952418-42c0-4e90-80b9-839a06cb45e6/image.png"></p>
<p>스프링 이니셜라이저를 이용하여 JDBC API와 H2 Database를 의존성으로 추가합니다</p>
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
	implementation 'org.springframework.boot:spring-boot-starter-jdbc'
	runtimeOnly 'com.h2database:h2'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

tasks.named('test') {
	useJUnitPlatform()
}</code></pre>
<h2 id="create-a-customer-object">Create a <code>customer</code> Object</h2>
<p>작업하게 될 간단한 데이터 액세스 논리는 고객의 이름과 성을 관리합니다. 애플리케이션 수준에서 이 데이터를 나타내려면 다음 목록(src/main/java/guides/relationaldataaccess/Customer.java)에 표시된 대로 <code>Customer</code> 클래스를 만듭니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>relationaldataaccess</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Customer</span> <span class="token punctuation">{</span>
  <span class="token keyword">private</span> <span class="token keyword">long</span> id<span class="token punctuation">;</span>
  <span class="token keyword">private</span> <span class="token class-name">String</span> firstName<span class="token punctuation">,</span> lastName<span class="token punctuation">;</span>

  <span class="token keyword">public</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token keyword">long</span> id<span class="token punctuation">,</span> <span class="token class-name">String</span> firstName<span class="token punctuation">,</span> <span class="token class-name">String</span> lastName<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">this</span><span class="token punctuation">.</span>id <span class="token operator">=</span> id<span class="token punctuation">;</span>
    <span class="token keyword">this</span><span class="token punctuation">.</span>firstName <span class="token operator">=</span> firstName<span class="token punctuation">;</span>
    <span class="token keyword">this</span><span class="token punctuation">.</span>lastName <span class="token operator">=</span> lastName<span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

  <span class="token annotation punctuation">@Override</span>
  <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> <span class="token class-name">String</span><span class="token punctuation">.</span><span class="token function">format</span><span class="token punctuation">(</span>
        <span class="token string">"Customer[id=%d, firstName='%s', lastName='%s']"</span><span class="token punctuation">,</span>
        id<span class="token punctuation">,</span> firstName<span class="token punctuation">,</span> lastName<span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

  <span class="token comment">// getters &amp; setters omitted for brevity</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="store-and-retrieve-data">Store and Retrieve Data</h2>
<p>Spring은 SQL 관계형 데이터베이스 및 JDBC 작업을 쉽게 해주는 <code>JdbcTemplate</code>이라는 템플릿 클래스를 제공합니다. 대부분의 JDBC 코드는 코드가 달성하려는 목표와 전혀 관련이 없는 리소스 획득, 연결 관리, 예외 처리 및 일반 오류 검사에 빠져 있습니다. <code>JdbcTemplate</code>이 이 모든 것을 처리해 줍니다. 당신이 해야 할 일은 당면한 일에 집중하는 것뿐입니다. 다음 목록(src/main/java/guidese/relationaldataaccess/RelationalDataAccessApplication.java)은 JDBC를 통해 데이터를 저장하고 검색할 수 있는 클래스를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>relationaldataaccess</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>slf4j</span><span class="token punctuation">.</span><span class="token class-name">Logger</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>slf4j</span><span class="token punctuation">.</span><span class="token class-name">LoggerFactory</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Autowired</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">CommandLineRunner</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>jdbc<span class="token punctuation">.</span>core</span><span class="token punctuation">.</span><span class="token class-name">JdbcTemplate</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util</span><span class="token punctuation">.</span><span class="token class-name">Arrays</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util</span><span class="token punctuation">.</span><span class="token class-name">List</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>stream</span><span class="token punctuation">.</span><span class="token class-name">Collectors</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">RelationalDataAccessApplication</span> <span class="token keyword">implements</span> <span class="token class-name">CommandLineRunner</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token class-name">Logger</span> log <span class="token operator">=</span> <span class="token class-name">LoggerFactory</span><span class="token punctuation">.</span><span class="token function">getLogger</span><span class="token punctuation">(</span><span class="token class-name">RelationalDataAccessApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span> args<span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">RelationalDataAccessApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token class-name">JdbcTemplate</span> jdbcTemplate<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> strings<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>

		log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"Creating tables"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		jdbcTemplate<span class="token punctuation">.</span><span class="token function">execute</span><span class="token punctuation">(</span><span class="token string">"DROP TABLE customers IF EXISTS"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		jdbcTemplate<span class="token punctuation">.</span><span class="token function">execute</span><span class="token punctuation">(</span><span class="token string">"CREATE TABLE customers("</span> <span class="token operator">+</span>
				<span class="token string">"id SERIAL, first_name VARCHAR(255), last_name VARCHAR(255))"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">// Split up the array of whole names into an array of first/last names</span>
		<span class="token class-name">List</span><span class="token operator">&lt;</span><span class="token class-name">Object</span><span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token operator">&gt;</span> splitUpNames <span class="token operator">=</span> <span class="token class-name">Arrays</span><span class="token punctuation">.</span><span class="token function">asList</span><span class="token punctuation">(</span><span class="token string">"John Woo"</span><span class="token punctuation">,</span> <span class="token string">"Jeff Dean"</span><span class="token punctuation">,</span> <span class="token string">"Josh Bloch"</span><span class="token punctuation">,</span> <span class="token string">"Josh Long"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">stream</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
				<span class="token punctuation">.</span><span class="token function">map</span><span class="token punctuation">(</span>name <span class="token operator">-&gt;</span> name<span class="token punctuation">.</span><span class="token function">split</span><span class="token punctuation">(</span><span class="token string">" "</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
				<span class="token punctuation">.</span><span class="token function">collect</span><span class="token punctuation">(</span><span class="token class-name">Collectors</span><span class="token punctuation">.</span><span class="token function">toList</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">// Use a Java 8 stream to print out each tuple of the list</span>
		splitUpNames<span class="token punctuation">.</span><span class="token function">forEach</span><span class="token punctuation">(</span>name <span class="token operator">-&gt;</span> log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">.</span><span class="token function">format</span><span class="token punctuation">(</span><span class="token string">"Inserting customer record for %s %s"</span><span class="token punctuation">,</span> name<span class="token punctuation">[</span><span class="token number">0</span><span class="token punctuation">]</span><span class="token punctuation">,</span> name<span class="token punctuation">[</span><span class="token number">1</span><span class="token punctuation">]</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">// Uses JdbcTemplate's batchUpdate operation to bulk load data</span>
		jdbcTemplate<span class="token punctuation">.</span><span class="token function">batchUpdate</span><span class="token punctuation">(</span><span class="token string">"INSERT INTO customers(first_name, last_name) VALUES (?,?)"</span><span class="token punctuation">,</span> splitUpNames<span class="token punctuation">)</span><span class="token punctuation">;</span>

		log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"Querying for customer records where first_name = 'Josh':"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		jdbcTemplate<span class="token punctuation">.</span><span class="token function">query</span><span class="token punctuation">(</span>
						<span class="token string">"SELECT id, first_name, last_name FROM customers WHERE first_name = ?"</span><span class="token punctuation">,</span>
						<span class="token punctuation">(</span>rs<span class="token punctuation">,</span> rowNum<span class="token punctuation">)</span> <span class="token operator">-&gt;</span> <span class="token keyword">new</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span>rs<span class="token punctuation">.</span><span class="token function">getLong</span><span class="token punctuation">(</span><span class="token string">"id"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> rs<span class="token punctuation">.</span><span class="token function">getString</span><span class="token punctuation">(</span><span class="token string">"first_name"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> rs<span class="token punctuation">.</span><span class="token function">getString</span><span class="token punctuation">(</span><span class="token string">"last_name"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token string">"Josh"</span><span class="token punctuation">)</span>
				<span class="token punctuation">.</span><span class="token function">forEach</span><span class="token punctuation">(</span>customer <span class="token operator">-&gt;</span> log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span>customer<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>Spring Boot는 H2(인메모리 관계형 데이터베이스 엔진)를 지원하고 자동으로 연결을 생성합니다. <code>spring-jdbc</code>를 사용하기 때문에 Spring Boot는 자동으로 <code>JdbcTemplate</code>을 생성합니다. <code>@Autowired JdbcTemplate</code> 필드는 이를 자동으로 로드하여 사용 가능하게 만듭니다.</p>
<p>이 <code>Application</code> 클래스는 Spring Boot의 <code>CommandLineRunner</code>를 구현합니다. 즉, 애플리케이션 컨텍스트가 로드된 후 <code>run()</code> 메서드를 실행합니다.</p>
<p>첫째, <code>JdbcTemplate</code>의 <code>execute</code> 메소드를 사용하여 일부 DDL을 설치합니다.</p>
<p>둘째, 문자열 목록을 가져와 Java 8 스트림을 사용하여 firstname/lastname 쌍으로 분할한 Java 배열을 생성합니다.</p>
<p>그런 다음 <code>JdbcTemplate</code>의 <code>batchUpdate</code> 메서드를 사용하여 새로 생성된 테이블에 일부 레코드를 설치합니다. 메서드 호출의 첫 번째 인수는 쿼리 문자열입니다. 마지막 인수(<code>Object</code> 인스턴스 배열)는 <code>?</code> 글자가 있는 쿼리로 대체될 변수를 보유합니다.</p>
<blockquote>
<p>단일 insert 문에서는 <code>insert</code> 메소드를 사용하지만 다중 insert문에서는 <code>batchUpdate</code>를 사용하는 편이 좋습니다.</p>
</blockquote>
<blockquote>
<p><code>?</code>를 인수를 사용하여 JDBC가 변수에 바인딩 함으로써 SQL injection 공격을 회피하도록 합니다.</p>
</blockquote>
<p>마지막으로 <code>query</code> 메소드를 사용하여 기준과 일치하는 레코드를 테이블에서 검색합니다. 다시 <code>?</code> 인수를 사용하여, 호출할 때 실제 값을 전달할 쿼리 매개변수를 생성합니다. 마지막 인수는 각 결과 행을 새 <code>Customer</code> 개체로 변환하는 데 사용되는 Java 8 람다입니다.</p>
<blockquote>
<p>Java 8 람다는 Spring의 <code>RowMapper</code>와 같은 단일 메서드 인터페이스에 잘 매핑됩니다. Java 7 이하를 사용하는 경우 익명 인터페이스 구현을 연결하고 메서드 본문을 람다 식의 본문과 동일하게 만들 수 있습니다.</p>
</blockquote>
<h3 id="functional-interface인-commandlinerunner">Functional Interface인 CommandLineRunner</h3>
<p>CommandLineRunner는 스프링 부트에서 제공하는 인터페이스이며 다음과 같이 정의되어 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@FunctionalInterface</span>
<span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">CommandLineRunner</span> <span class="token punctuation">{</span>
    <span class="token keyword">void</span> <span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> args<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p><code>CommandLineRunner</code> 인터페이스의 <code>run</code> 메서드는 문자열 가변인수(<code>String... strings</code>)를 매개변수로 가지고 있습니다. 그래서 일반적으로 <code>run</code> 메서드의 매개변수는 문자열의 배열로 받아들이는 것이 일반적입니다.</p>
<p><code>run</code> 메서드는 단일 스레드로 실행되며 명령행에서 전달되는 인수를 받는 데 사용됩니다. 만약 멀티스레드로 여러 요청이 들어와서 미리 예측할 수 없는 요청을 처리해야 한다면 <code>CommandLineRunner</code>를 사용하는 것은 적합하지 않을 수 있습니다.</p>
<p>일반적으로 <code>CommandLineRunner</code>는 스프링 부트 애플리케이션이 시작될 때 한 번 실행되며, 명령행 인수를 처리하는 데 사용됩니다. 만약 여러 요청을 비동기적으로 처리하고자 하거나, 요청이 들어올 때마다 실행되어야 한다면 <code>CommandLineRunner</code>를 사용하기보다는 다른 방법을 고려해야 합니다.</p>
<p>이번 실습에서는 <code>CommandLineRunner</code>를 <code>RelationalDataAccessApplication</code>으로 <code>implement</code>하고 <code>run</code> 메서드를 <code>@Override</code> 했습니다.  </p>
<pre><code class="language-java"><span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">RelationalDataAccessApplication</span> <span class="token keyword">implements</span> <span class="token class-name">CommandLineRunner</span> <span class="token punctuation">{</span>

<span class="token comment">//...중략</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token class-name">JdbcTemplate</span> jdbcTemplate<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> strings<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>

		<span class="token comment">// 생략</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><a href="https://velog.io/@dev_hammy/GuideConsuming-a-RESTful-Web-Service">RESTful 웹 서비스를 사용하기</a>에서는 <code>CommandLineRunner</code>가 어떻게 사용되었는지 다시 살펴봅시다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ConsumingRestApplication</span> <span class="token punctuation">{</span>
    
<span class="token comment">//... 중략</span>
    
    <span class="token annotation punctuation">@Bean</span>
    <span class="token keyword">public</span> <span class="token class-name">RestTemplate</span> <span class="token function">restTemplate</span><span class="token punctuation">(</span><span class="token class-name">RestTemplateBuilder</span> builder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> builder<span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
    <span class="token annotation punctuation">@Bean</span>
    <span class="token annotation punctuation">@Profile</span><span class="token punctuation">(</span><span class="token string">"!test"</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token class-name">CommandLineRunner</span> <span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">RestTemplate</span> restTemplate<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> args <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
            <span class="token class-name">Quote</span> quote <span class="token operator">=</span> restTemplate<span class="token punctuation">.</span><span class="token function">getForObject</span><span class="token punctuation">(</span>
                    <span class="token string">"http://localhost:8080/api/random"</span><span class="token punctuation">,</span>
                    <span class="token class-name">Quote</span><span class="token punctuation">.</span><span class="token keyword">class</span>
            <span class="token punctuation">)</span><span class="token punctuation">;</span>
            log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span>quote<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>@Bean</code> 어노테이션을 사용하는 경우에는 해당 빈의 생성과 초기화를 Spring 컨테이너에게 위임하게 됩니다. 그리고 <code>@Bean</code> 어노테이션으로 설정된 메서드가 리턴하는 객체는 해당 빈의 인스턴스가 됩니다. 이 방법을 사용하면, 런타임 시에 필요한 의존성을 주입하여 인스턴스를 얻을 수 있게 됩니다.</p>
<p><code>CommandLineRunner</code> <strong>인터페이스를 직접 구현하는 대신</strong>, <code>@Bean</code> 어노테이션과 메서드를 사용하여 <strong>해당 인터페이스를 구현한 객체를 반환</strong>하였고 이는 스프링의 IoC(Inversion of Control)을 활용하는 것입니다. 인터페이스의 시그니처와 맞지 않지만, 스프링 IoC 컨테이너가 관리하는 빈을 매개변수로 받아 사용하는 것이 가능합니다.</p>
<h2 id="실행하기">실행하기</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/339336a0-c24f-4978-834a-1aac44e9e2c6/image.png"></p>
<p><code>Creating tables</code> 부터 차례로 로그를 통해 데이터베이스에 insert되고 query 되는 것을 확인할 수 있습니다. </p>
<h3 id="커넥션-풀">커넥션 풀</h3>
<p>Connection pool(연결 풀)은 데이터베이스 연결을 관리하기 위한 기술입니다. 일반적으로 데이터베이스와의 연결은 비용이 많이 드는 작업 중 하나입니다. 따라서 매번 데이터베이스에 새로운 연결을 맺고 끊는 것은 시간과 자원을 많이 소비하게 됩니다.</p>
<p>연결 풀은 이러한 연결 비용을 절감하기 위해 미리 일정 수의 데이터베이스 연결을 생성해 두고, 애플리케이션이 데이터베이스 연결을 요청할 때마다 이 풀에서 사용 가능한 연결을 대여하고, 사용이 끝나면 다시 풀에 반환하는 방식으로 동작합니다.</p>
<p>일반적으로 연결 풀은 다음과 같은 장점을 제공합니다:</p>
<ul>
<li>성능 향상: 미리 연결을 확보하고 있기 때문에 매번 연결을 새로 맺는 데 드는 시간을 절약하고, 애플리케이션의 응답 시간을 줄일 수 있습니다.</li>
<li>자원 관리: 연결 풀은 일정 수의 연결을 관리하며, 이를 통해 과도한 연결 요청으로 인한 데이터베이스 부하를 방지하고, 메모리 누수를 방지할 수 있습니다.</li>
<li>확장성: 애플리케이션의 부하가 증가하더라도 연결 풀은 설정된 한도 내에서 요청을 처리할 수 있어서, 애플리케이션의 확장성을 높일 수 있습니다.</li>
</ul>
<p>Spring Boot는 이러한 연결 풀을 다루기 위한 다양한 설정을 제공하며, 이를 통해 데이터베이스 연결 관리와 관련된 많은 문제를 쉽게 해결할 수 있도록 지원합니다.</p>
<hr>
<p>일반적으로 연결 풀을 관리하는 것은 데이터베이스에 연결하는 연산 자체를 말합니다. 예를 들어, <code>DataSource</code>를 설정하고 이를 통해 데이터베이스와의 연결을 확립하는 것이 연결 풀을 관리하는 것입니다.</p>
<p><code>JdbcTemplate</code>은 실제로 이러한 연결 풀을 내부적으로 관리하고 있어서, 개발자가 직접적으로 연결을 생성하거나 닫을 필요는 없습니다. 그러나 <code>JdbcTemplate</code>을 사용하는 경우에도 스프링 부트는 설정에 따라 내부적으로 데이터베이스와의 연결을 효율적으로 관리합니다.</p>
<p>따라서 <code>CommandLineRunner</code>를 사용하여 <code>JdbcTemplate</code>을 실행하는 것은 데이터베이스 연산을 수행하는 것이지만, 직접적인 연결 풀의 생성 또는 관리는 <code>JdbcTemplate</code>이나 스프링 부트에서 이루어지는 것입니다.</p>