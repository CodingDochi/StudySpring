<p><code>org.springframework.jdbc.datasource.embedded</code> 패키지는 임베디드 Java 데이터베이스 엔진에 대한 지원을 제공합니다. <a href="https://www.hsqldb.org/">HSQL</a>, <a href="https://www.h2database.com/">H2</a> 및 <a href="https://db.apache.org/derby">Derby</a>에 대한 지원이 기본적으로 제공됩니다. 또한 확장 가능한 API를 사용하여 새로운 내장형 데이터베이스 타입 및 <code>DataSource</code> 구현을 연결할 수도 있습니다.</p>
<h1 id="why-use-an-embedded-database">Why Use an Embedded Database?</h1>
<p>내장된 데이터베이스는 가벼운 특성으로 인해 프로젝트 개발 단계에서 유용할 수 있습니다. 이점에는 구성 용이성, 빠른 시작 시간, 테스트 가능성, 개발 중에 SQL을 빠르게 발전시킬 수 있는 기능이 포함됩니다.</p>
<h1 id="creating-an-embedded-database-by-using-spring-xml">Creating an Embedded Database by Using Spring XML</h1>
<p>Spring <code>ApplicationContext</code>에서 내장된 데이터베이스 인스턴스를 빈으로 노출하려면 <code>spring-jdbc</code> 네임스페이스에서 <code>embedded-database</code> 태그를 사용할 수 있습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">jdbc:</span>embedded-database</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>dataSource<span class="token punctuation">"</span></span> <span class="token attr-name">generate-name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>true<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">jdbc:</span>script</span> <span class="token attr-name">location</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>classpath:schema.sql<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">jdbc:</span>script</span> <span class="token attr-name">location</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>classpath:test-data.sql<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">jdbc:</span>embedded-database</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>이전 구성은 classpath 루트에 있는 <code>schema.sql</code> 및 <code>test-data.sql</code> 리소스의 SQL로 채워지는 임베디드 HSQL 데이터베이스를 생성합니다. 또한 모범 사례에 따라 내장된 데이터베이스에는 고유하게 생성된 이름이 할당됩니다. 내장된 데이터베이스는 필요에 따라 데이터 액세스 객체에 주입될 수 있는 <code>javax.sql.DataSource</code> 유형의 Bean으로 Spring 컨테이너에서 사용할 수 있습니다.</p>
<h1 id="creating-an-embedded-database-programmatically">Creating an Embedded Database Programmatically</h1>
<p><code>EmbeddedDatabaseBuilder</code> 클래스는 프로그래밍 방식으로 임베디드 데이터베이스를 구성하기 위한 원활한 API를 제공합니다. 다음 예와 같이 독립형 환경이나 독립형 통합 테스트에서 임베디드 데이터베이스를 생성해야 할 때 이를 사용할 수 있습니다.</p>
<pre><code class="language-java"><span class="token class-name">EmbeddedDatabase</span> db <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">EmbeddedDatabaseBuilder</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">generateUniqueName</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">setType</span><span class="token punctuation">(</span>H2<span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">setScriptEncoding</span><span class="token punctuation">(</span><span class="token string">"UTF-8"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">ignoreFailedDrops</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">addScript</span><span class="token punctuation">(</span><span class="token string">"schema.sql"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">addScripts</span><span class="token punctuation">(</span><span class="token string">"user_data.sql"</span><span class="token punctuation">,</span> <span class="token string">"country_data.sql"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// perform actions against the db (EmbeddedDatabase extends javax.sql.DataSource)</span>

db<span class="token punctuation">.</span><span class="token function">shutdown</span><span class="token punctuation">(</span><span class="token punctuation">)</span></code></pre>
<p>지원되는 모든 옵션에 대한 자세한 내용은 <a href="https://docs.spring.io/spring-framework/docs/6.1.4/javadoc-api/org/springframework/jdbc/datasource/embedded/EmbeddedDatabaseBuilder.html">EmbeddedDatabaseBuilder</a>에 대한 javadoc를 참조하세요. 다음 예제와 같이 <code>EmbeddedDatabaseBuilder</code>를 사용하여 Java 구성을 사용하여 내장형 데이터베이스를 생성할 수도 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">DataSourceConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">DataSource</span> <span class="token function">dataSource</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">EmbeddedDatabaseBuilder</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
				<span class="token punctuation">.</span><span class="token function">generateUniqueName</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span>
				<span class="token punctuation">.</span><span class="token function">setType</span><span class="token punctuation">(</span>H2<span class="token punctuation">)</span>
				<span class="token punctuation">.</span><span class="token function">setScriptEncoding</span><span class="token punctuation">(</span><span class="token string">"UTF-8"</span><span class="token punctuation">)</span>
				<span class="token punctuation">.</span><span class="token function">ignoreFailedDrops</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span>
				<span class="token punctuation">.</span><span class="token function">addScript</span><span class="token punctuation">(</span><span class="token string">"schema.sql"</span><span class="token punctuation">)</span>
				<span class="token punctuation">.</span><span class="token function">addScripts</span><span class="token punctuation">(</span><span class="token string">"user_data.sql"</span><span class="token punctuation">,</span> <span class="token string">"country_data.sql"</span><span class="token punctuation">)</span>
				<span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h1 id="selecting-the-embedded-database-type">Selecting the Embedded Database Type</h1>
<p>이 섹션에서는 Spring이 지원하는 세 가지 내장 데이터베이스 중 하나를 선택하는 방법을 다룹니다. 여기에는 다음 주제가 포함됩니다. </p>
<ul>
<li>HSQL 사용 </li>
<li>H2 사용 </li>
<li>더비 사용</li>
</ul>
<h2 id="using-hsql">Using HSQL</h2>
<p>Spring은 HSQL 1.8.0 이상을 지원합니다. 타입이 명시적으로 지정되지 않은 경우 HSQL은 기본 내장 데이터베이스입니다. HSQL을 명시적으로 지정하려면 <code>embedded-database</code> 태그의 <code>type</code> 속성(attribute)을 <code>HSQL</code>로 설정하십시오. 빌더 API를 사용하는 경우 <code>EmbeddedDatabaseType.HSQL</code>을 사용하여 <code>setType(EmbeddedDatabaseType)</code> 메서드를 호출하세요.</p>
<h2 id="using-h2">Using H2</h2>
<p>Spring은 H2 데이터베이스를 지원합니다. H2를 활성화하려면 <code>embedded-database</code> 태그의 <code>type</code> 속성(attribute)을 <code>H2</code>로 설정하십시오. 빌더 API를 사용하는 경우 <code>EmbeddedDatabaseType.H2</code>와 함께 <code>setType(EmbeddedDatabaseType)</code> 메서드를 호출하세요.</p>
<h2 id="using-derby">Using Derby</h2>
<p>Spring은 Apache Derby 10.5 이상을 지원합니다. Derby를 활성화하려면 <code>embedded-database</code> 태그의 <code>type</code> 속성을 <code>DERBY</code>로 설정하십시오. 빌더 API를 사용하는 경우 <code>EmbeddedDatabaseType.DERBY</code>를 사용하여 <code>setType(EmbeddedDatabaseType)</code> 메서드를 호출하세요.</p>
<h1 id="testing-data-access-logic-with-an-embedded-database">Testing Data Access Logic with an Embedded Database</h1>
<p>내장된 데이터베이스는 데이터 액세스 코드를 테스트하는 간단한 방법을 제공합니다. 다음 예는 내장된 데이터베이스를 사용하는 데이터 액세스 통합 테스트 템플릿입니다. 이러한 템플릿을 사용하면 내장된 데이터베이스를 테스트 클래스 전체에서 재사용할 필요가 없을 때 일회용(one-offs)으로 유용할 수 있습니다. 그러나 테스트 스위트 내에서 공유되는 임베디드 데이터베이스를 생성하려는 경우 <a href="https://docs.spring.io/spring-framework/reference/testing/testcontext-framework.html">Spring TestContext Framework</a>를 사용하고 <a href="https://docs.spring.io/spring-framework/reference/data-access/jdbc/embedded-database-support.html#jdbc-embedded-database-xml">Spring XML을 사용하여 임베디드 데이터베이스 생성</a>, <a href="https://docs.spring.io/spring-framework/reference/data-access/jdbc/embedded-database-support.html#jdbc-embedded-database-java">프로그래밍 방식으로 임베디드 데이터베이스 생성</a>에 설명된 대로 Spring <code>ApplicationContext</code>에서 임베디드 데이터베이스를 빈으로 구성하는 것을 고려하십시오. 다음 목록은 테스트 템플릿을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">DataAccessIntegrationTestTemplate</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">EmbeddedDatabase</span> db<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@BeforeEach</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setUp</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// creates an HSQL in-memory database populated from default scripts</span>
		<span class="token comment">// classpath:schema.sql and classpath:data.sql</span>
		db <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">EmbeddedDatabaseBuilder</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
				<span class="token punctuation">.</span><span class="token function">generateUniqueName</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span>
				<span class="token punctuation">.</span><span class="token function">addDefaultScripts</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
				<span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Test</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">testDataAccess</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">JdbcTemplate</span> template <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">JdbcTemplate</span><span class="token punctuation">(</span>db<span class="token punctuation">)</span><span class="token punctuation">;</span>
		template<span class="token punctuation">.</span><span class="token function">query</span><span class="token punctuation">(</span> <span class="token comment">/* ... */</span> <span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@AfterEach</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">tearDown</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		db<span class="token punctuation">.</span><span class="token function">shutdown</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<h1 id="generating-unique-names-for-embedded-databases">Generating Unique Names for Embedded Databases</h1>
<p>개발 팀은 테스트 스위트가 실수로 동일한 데이터베이스의 추가 인스턴스를 다시 생성하려고 시도하는 경우 내장된 데이터베이스에 오류가 발생하는 경우가 많습니다. XML 구성 파일 또는 <code>@Configuration</code> 클래스가 내장된 데이터베이스 생성을 담당하고 해당 구성이 동일한 테스트 스위트(즉, 동일한 JVM 프로세스 내) 내의 여러 테스트 시나리오에서 재사용되는 경우 매우 쉽게 발생할 수 있습니다. 예를 들어, 활성화된 Bean 정의 프로파일에 대해서만 <code>ApplicationContext</code> 구성이 다른 내장된 데이터베이스에 대한 통합 테스트같은 경우입니다.</p>
<p>이러한 오류의 근본 원인은 Spring의 <code>EmbeddedDatabaseFactory</code>(<code>&lt;jdbc:embedded-database&gt;</code> XML 네임스페이스 요소와 Java 구성용 <code>EmbeddedDatabaseBuilder</code> 모두에서 내부적으로 사용됨)가 달리 지정되지 않은 경우 내장 데이터베이스의 이름을 <code>testdb</code>로 설정한다는 사실입니다. <code>&lt;jdbc:embedded-database&gt;</code>의 경우 내장된 데이터베이스에는 일반적으로 Bean의 <code>id</code>와 동일한 이름(종종 <code>dataSource</code>와 같은 이름)이 할당됩니다. 따라서 이후에 내장형 데이터베이스를 생성하려고 시도해도 새 데이터베이스가 생성되지 않습니다. 대신, 동일한 JDBC 연결 URL이 재사용되며, 새로운 내장 데이터베이스를 생성하려는 시도는 실제로 동일한 구성에서 생성된 기존 내장 데이터베이스를 가리킵니다.</p>
<p>이 일반적인 문제를 해결하기 위해 Spring Framework 4.2는 임베디드 데이터베이스에 대한 고유 이름 생성을 지원합니다. 생성된 이름을 사용하려면 다음 옵션 중 하나를 사용하십시오.</p>
<ul>
<li>
<p><code>EmbeddedDatabaseFactory.setGenerateUniqueDatabaseName()</code></p>
</li>
<li>
<p><code>EmbeddedDatabaseBuilder.generateUniqueName()</code></p>
</li>
<li>
<p><code>&lt;jdbc:embedded-database generate-name="true" …​ &gt;</code></p>
</li>
</ul>
<h1 id="extending-the-embedded-database-support">Extending the Embedded Database Support</h1>
<p>두 가지 방법으로 Spring JDBC 임베디드 데이터베이스 지원을 확장할 수 있습니다.</p>
<ul>
<li>
<p>새로운 임베디드 데이터베이스 타입을 지원하려면 <code>EmbeddedDatabaseConfigurer</code>를 구현하십시오.</p>
</li>
<li>
<p>내장된 데이터베이스 연결을 관리하기 위한 connection 풀과 같은 새로운 <code>DataSource</code> 구현을 지원하려면 <code>DataSourceFactory</code>를 구현하십시오.</p>
</li>
</ul>
<p>GitHub Issues에서 Spring 커뮤니티에 확장 기능을 제공하는 것이 좋습니다.</p>