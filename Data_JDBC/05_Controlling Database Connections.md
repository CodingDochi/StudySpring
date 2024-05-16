<p>이 섹션에서는 아래 주제를 다룹니다:</p>
<ul>
<li><code>DataSource</code> 사용하기</li>
<li><code>DataSourceUtils</code> 사용하기</li>
<li><code>SmartDataSource</code> 구현하기</li>
<li><code>AbstractDataSource</code> 확장(extend)하기</li>
<li><code>SingleConnectionDataSource</code> 사용하기</li>
<li><code>DriverManagerDataSource</code> 사용하기</li>
<li><code>TransactionAwareDataSourceProxy</code> 사용하기</li>
<li><code>DataSourceTransactionManager</code> / <code>JdbcTransactionManager</code> 사용하기</li>
</ul>
<h1 id="using-datasource">Using <code>DataSource</code></h1>
<p>Spring은 <code>DataSource</code>를 통해 데이터베이스에 대한 연결을 얻습니다. <code>DataSource</code>는 JDBC 사양의 일부이며 일반화된 연결(connection) 팩토리입니다. 이를 통해 컨테이너 또는 프레임워크가 애플리케이션 코드에서 연결(connection) 풀링(pooling) 및 트랜잭션 관리 문제를 숨길 수 있습니다. 개발자는 데이터베이스에 연결하는 방법에 대한 세부 정보를 알 필요가 없습니다. 이는 데이터 소스를 설정하는 관리자의 책임입니다. 코드를 개발하고 테스트할 때 두 역할을 모두 수행할 가능성이 높지만 프로덕션 데이터 소스가 구성(configured)되는 방식을 반드시 알 필요는 없습니다.</p>
<p>Spring의 JDBC 레이어를 사용할 때 JNDI에서 데이터 소스를 얻거나 타사(third party)에서 제공하는 연결 풀(connection pool) 구현을 사용하여 직접 구성할 수 있습니다. 전통적인 선택은 Bean 스타일 <code>DataSource</code> 클래스가 있는 Apache Commons DBCP 및 C3P0입니다. 최신 JDBC 연결 풀의 경우 대신 빌더 스타일 API를 사용하는 HikariCP를 고려하세요.</p>
<blockquote>
<p><strong>[Note]</strong><br>
<code>DriverManagerDataSource</code> 및 <code>SimpleDriverDataSource</code> 클래스(Spring 배포판에 포함됨)는 테스트 목적으로만 사용해야 합니다! 이러한 변형은 풀링을 제공하지 않으며 연결에 대한 요청이 여러 번 있을 때 성능이 저하됩니다.</p>
</blockquote>
<p>다음 섹션에서는 Spring의 <code>DriverManagerDataSource</code> 구현을 사용합니다. 몇 가지 다른 <code>DataSource</code> 변형은 나중에 다룹니다.</p>
<p><code>DriverManagerDataSource</code>를 구성하려면:</p>
<ol>
<li>
<p>일반적으로 JDBC 연결을 얻는 것처럼 <code>DriverManagerDataSource</code>와의 연결을 얻습니다.</p>
</li>
<li>
<p><code>DriverManager</code>가 드라이버 클래스를 로드할 수 있도록 JDBC 드라이버의 정규화된 클래스 이름을 지정합니다.</p>
</li>
<li>
<p>JDBC 드라이버마다 다른 URL을 제공하세요. (올바른 값은 드라이버 설명서를 참조하십시오.)</p>
</li>
<li>
<p>데이터베이스에 연결하기 위한 사용자 이름과 비밀번호를 제공합니다.</p>
</li>
</ol>
<p>다음 예에서는 Java에서 <code>DriverManagerDataSource</code>를 구성하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token class-name">DriverManagerDataSource</span> dataSource <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">DriverManagerDataSource</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
dataSource<span class="token punctuation">.</span><span class="token function">setDriverClassName</span><span class="token punctuation">(</span><span class="token string">"org.hsqldb.jdbcDriver"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
dataSource<span class="token punctuation">.</span><span class="token function">setUrl</span><span class="token punctuation">(</span><span class="token string">"jdbc:hsqldb:hsql://localhost:"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
dataSource<span class="token punctuation">.</span><span class="token function">setUsername</span><span class="token punctuation">(</span><span class="token string">"sa"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
dataSource<span class="token punctuation">.</span><span class="token function">setPassword</span><span class="token punctuation">(</span><span class="token string">""</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>다음 예에서는 해당 XML 구성을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>dataSource<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.jdbc.datasource.DriverManagerDataSource<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>driverClassName<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.driverClassName}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>url<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.url}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>username<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.username}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>password<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.password}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>property-placeholder</span> <span class="token attr-name">location</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>jdbc.properties<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<p>다음 두 예에서는 DBCP 및 C3P0에 대한 기본 연결 및 구성을 보여줍니다. 풀링 기능을 제어하는 데 도움이 되는 추가 옵션에 대해 알아보려면 해당 연결 풀링 구현에 대한 제품 공식 문서를 참조하세요.</p>
<p>다음 예에서는 DBCP 구성을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>dataSource<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.apache.commons.dbcp.BasicDataSource<span class="token punctuation">"</span></span> <span class="token attr-name">destroy-method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>close<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>driverClassName<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.driverClassName}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>url<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.url}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>username<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.username}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>password<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.password}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>property-placeholder</span> <span class="token attr-name">location</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>jdbc.properties<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<p>다음 예에서는 C3P0 구성을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>dataSource<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.mchange.v2.c3p0.ComboPooledDataSource<span class="token punctuation">"</span></span> <span class="token attr-name">destroy-method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>close<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>driverClass<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.driverClassName}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>jdbcUrl<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.url}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>user<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.username}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>password<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.password}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>property-placeholder</span> <span class="token attr-name">location</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>jdbc.properties<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<h1 id="using-datasourceutils">Using <code>DataSourceUtils</code></h1>
<p><code>DataSourceUtils</code> 클래스는 JNDI에서 연결을 얻고 필요한 경우 연결을 닫는 <code>static</code> 메서드를 제공하는 편리하고 강력한 도우미 클래스입니다. 이는 <code>DataSourceTransactionManager</code>뿐만 아니라 <code>JtaTransactionManager</code> 및 <code>JpaTransactionManager</code>를 사용하여 스레드 바인딩된 JDBC 연결을 지원합니다.</p>
<p><code>JdbcTemplate</code>은 암시적으로 진행 중인 트랜잭션에 참여하는 모든 JDBC 작업 뒤에서 <code>DataSourceUtils</code> 연결 액세스를 사용하고 있습니다.</p>
<h1 id="implementing-smartdatasource">Implementing <code>SmartDataSource</code></h1>
<p><code>SmartDataSource</code> 인터페이스는 관계형 데이터베이스에 대한 연결을 제공할 수 있는 클래스에 의해 구현되어야 합니다. 이는 <code>DataSource</code> 인터페이스를 확장하여 이를 사용하는 클래스가 특정 작업 후에 연결을 닫아야 하는지 여부를 쿼리할 수 있도록 합니다. 이 사용법은 연결을 재사용해야 한다는 것을 알고 있을 때 효율적입니다.</p>
<h1 id="extending-abstractdatasource">Extending <code>AbstractDataSource</code></h1>
<p><code>AbstractDataSource</code>는 Spring의 <code>DataSource</code> 구현을 위한 <code>abstract</code> 기본 클래스입니다. 이는 모든 <code>DataSource</code> 구현에 공통적인 코드를 구현합니다. 자신만의 <code>DataSource</code> 구현을 작성하는 경우 <code>AbstractDataSource</code> 클래스를 확장해야 합니다.</p>
<h1 id="using-singleconnectiondatasource">Using <code>SingleConnectionDataSource</code></h1>
<p><code>SingleConnectionDataSource</code> 클래스는 매번 사용 후 닫히지 않는 단일 <code>Connection</code>을 래핑하는 <code>SmartDataSource</code> 인터페이스의 구현입니다. 이는 멀티스레딩을 지원하지 않습니다.</p>
<p>클라이언트 코드가 풀링된 연결을 가정하여(on the assumption of) <code>close</code>를 호출하는 경우(persistence 도구를 사용할 때와 같이) <code>suppressClose</code> 속성을 <code>true</code>로 설정해야 합니다. 이 설정은 물리적 연결을 래핑하는 close-suppressing 프록시를 반환합니다. 더 이상 이를 native Oracle <code>Connection</code> 또는 유사한 개체로 캐스팅할 수 없습니다.</p>
<p><code>SingleConnectionDataSource</code>는 주로 테스트 클래스입니다. 일반적으로 간단한 JNDI 환경과 함께 애플리케이션 서버 외부에서 코드를 쉽게 테스트할 수 있습니다. <code>DriverManagerDataSource</code>와 달리 물리적 연결이 과도하게 생성되는 것을 방지하여 항상 동일한 연결을 재사용합니다.</p>
<h1 id="using-drivermanagerdatasource">Using <code>DriverManagerDataSource</code></h1>
<p><code>DriverManagerDataSource</code> 클래스는 빈 속성을 통해 일반 JDBC 드라이버를 구성하고 매번 <code>Connection</code>을 반환하는 표준 <code>DataSource</code> 인터페이스의 구현입니다.</p>
<p>이 구현은 Spring IoC 컨테이너의 DataSource 빈으로 또는 간단한 JNDI 환경과 함께 Jakarta EE 컨테이너 외부의 테스트 및 독립형 환경에 유용합니다. 풀 가정(pool-assuming) <code>Connection.close()</code> 호출은 연결을 닫으므로 모든 <code>DataSource</code> 인식(-aware) persistence 코드가 작동해야 합니다. 그러나 JavaBean 스타일 연결 풀(예: <code>commons-dbcp</code>)을 사용하는 것은 테스트 환경에서도 매우 쉽기 때문에 <code>DriverManagerDataSource</code>를 통해 이러한 연결 풀을 사용하는 것이 거의 항상 바람직합니다.</p>
<h1 id="using-transactionawaredatasourceproxy">Using <code>TransactionAwareDataSourceProxy</code></h1>
<p><code>TransactionAwareDataSourceProxy</code>는 대상 <code>DataSource</code>에 대한 프록시입니다. 프록시는 해당 대상 <code>DataSource</code>를 래핑하여 Spring 관리 트랜잭션에 대한 인식(awareness)을 추가합니다. 이러한 점에서 이는 Jakarta EE 서버에서 제공하는 트랜잭션 JNDI <code>DataSource</code>와 유사합니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
이미 존재하는 코드가 호출되어 표준 JDBC <code>DataSource</code> 인터페이스 구현을 전달해야 하는 경우를 제외하고는 이 클래스를 사용하는 것이 거의 바람직하지 않습니다. 이 경우 이 코드를 계속 사용할 수 있으며 동시에 이 코드가 Spring 관리 트랜잭션에 참여하도록 할 수 있습니다. 일반적으로 <code>JdbcTemplate</code> 또는 <code>DataSourceUtils</code>와 같은 리소스 관리를 위한 더 높은 수준의 추상화를 사용하여 자신만의 새 코드를 작성하는 것이 좋습니다.</p>
</blockquote>
<p>자세한 내용은 <a href="https://docs.spring.io/spring-framework/docs/6.1.4/javadoc-api/org/springframework/jdbc/datasource/TransactionAwareDataSourceProxy.html"><code>TransactionAwareDataSourceProxy</code></a> javadoc을 참조하세요.</p>
<h1 id="using-datasourcetransactionmanager--jdbctransactionmanager">Using <code>DataSourceTransactionManager</code> / <code>JdbcTransactionManager</code></h1>
<p><code>DataSourceTransactionManager</code> 클래스는 단일 JDBC <code>DataSource</code>에 대한 <code>PlatformTransactionManager</code> 구현입니다. 이는 지정된 <code>DataSource</code>의 JDBC <code>Connection</code>을 현재 실행 중인 스레드에 바인딩하여 잠재적으로 <code>DataSource</code>당 하나의 스레드 바인딩된 연결을 허용합니다.</p>
<p>Java EE의 표준 <code>DataSource.getConnection</code> 대신 <code>DataSourceUtils.getConnection(DataSource)</code>을 통해 JDBC <code>Connection</code>을 검색하려면 애플리케이션 코드가 필요합니다. 확인된 <code>SQLException</code> 대신 확인되지 않은 <code>org.springframework.dao</code> 예외가 발생합니다. <code>JdbcTemplate</code>과 같은 모든 프레임워크 클래스는 이 전략을 암시적으로 사용합니다. 트랜잭션 관리자와 함께 사용되지 않는 경우 조회 전략은 <code>DataSource.getConnection</code>과 동일하게 작동하므로 어떤 경우에도 사용할 수 있습니다.</p>
<hr>
<p>여기서 "checked"와 "unchecked" 예외의 차이는 다음과 같습니다.</p>
<ol>
<li>
<p><strong>Checked 예외</strong>:</p>
<ul>
<li>컴파일 시점에서 처리가 요구되는 예외입니다.</li>
<li>메소드에서 발생할 수 있는 checked 예외를 선언해야 합니다(throws 예약어를 사용하여 또는 try-catch 블록 내에서 처리).</li>
<li>주로 외부 리소스(파일 입출력, 네트워크 연결 등)와 관련된 예외들이 여기에 해당됩니다.</li>
<li>IOException, SQLException 등이 여기에 속합니다.</li>
</ul>
</li>
<li>
<p><strong>Unchecked 예외</strong>:</p>
<ul>
<li>컴파일러가 예외 처리를 강제하지 않는 예외로, 런타임 시점에 발생할 수 있습니다.</li>
<li>주로 프로그래머의 실수나 프로그램 논리상의 오류에 의해 발생하는 예외들이 여기에 해당됩니다.</li>
<li>대부분 RuntimeException 클래스와 그 하위 클래스들이 여기에 속합니다. NullPointerException, ArrayIndexOutOfBoundsException 등이 여기에 해당됩니다.</li>
</ul>
</li>
</ol>
<p>Spring의 DAO 예외들은 보통 unchecked 예외로 분류됩니다. 즉, 컴파일러가 예외 처리를 강제하지 않습니다. 따라서 호출자가 예외를 처리하거나, 예외를 던질 수 있습니다. 이것은 프로그래머가 DAO 계층에서 발생하는 예외를 명시적으로 처리하거나 처리를 회피할 수 있도록 합니다.</p>
<hr>
<p><code>DataSourceTransactionManager</code> 클래스는 저장점(<code>PROPAGATION_NESTED</code>), 사용자 정의 격리 수준 및 적절한 JDBC 문 쿼리 시간 초과로 적용되는 시간 초과를 지원합니다. 후자를 지원하려면 애플리케이션 코드는 <code>JdbcTemplate</code>을 사용하거나 생성된 각 문에 대해 <code>DataSourceUtils.applyTransactionTimeout(..)</code> 메서드를 호출해야 합니다.</p>
<p>single-resource의 경우 <code>JtaTransactionManager</code> 대신 <code>DataSourceTransactionManager</code>를 사용할 수 있는데, 이는 JTA 트랜잭션 코디네이터를 지원하기 위해 컨테이너가 필요하지 않기 때문입니다. 필요한 연결 조회 패턴을 고수한다면 이러한 트랜잭션 관리자 간 전환은 단지 구성의 문제일 뿐입니다. JTA는 저장점이나 사용자 정의 격리 수준을 지원하지 않으며 다른 시간 초과 메커니즘을 가지고 있지만 JDBC 리소스 및 JDBC 커밋/롤백 관리 측면에서 유사한 동작을 노출합니다.</p>
<p>실제 자원 연결의 JTA 스타일 지연 검색(lazy retrieval)을 위해 Spring은 대상 연결 풀에 해당하는 <code>DataSource</code> 프록시 클래스를 제공합니다. <a href="https://docs.spring.io/spring-framework/docs/6.1.4/javadoc-api/org/springframework/jdbc/datasource/LazyConnectionDataSourceProxy.html"><code>LazyConnectionDataSourceProxy</code></a>를 참조하세요. 이는 실제 statement 실행 없이 잠재적으로 비어 있는 트랜잭션(이러한 시나리오에서 실제 리소스를 가져오지 않음)과 트랜잭션 동기화(transaction-synchronized)된 읽기 전용 플래그 및/또는 격리 수준을 고려하는 라우팅 <code>DataSource</code> 앞에 특히 유용합니다. 계정(예: <code>IsolationLevelDataSourceRouter</code>).</p>
<p><code>LazyConnectionDataSourceProxy</code>는 또한 읽기 전용 트랜잭션 중에 사용할 읽기 전용 연결 풀에 대한 특별한 지원을 제공하여 기본(primary) 연결 풀에서 가져올 때 모든 트랜잭션의 시작과 끝에서 JDBC 연결의 읽기 전용 플래그를 전환하는 오버헤드를 방지합니다( JDBC 드라이버에 따라 비용이 많이 들 수 있습니다).</p>
<blockquote>
<p><strong>[Note]</strong><br>
5.3부터 Spring은 커밋/롤백(<code>JdbcTemplate</code>에 맞춰 조정됨) 시 예외 변환 기능을 추가하는 확장된 <code>JdbcTransactionManager</code> 변형을 제공합니다. <code>DataSourceTransactionManager</code>가 <code>TransactionSystemException</code>(JTA와 유사)만 발생시키는 경우 <code>JdbcTransactionManager</code>는 데이터베이스 잠금 실패 등을 해당 <code>DataAccessException</code> 하위 클래스로 변환합니다. <code>TransactionSystemException</code>을 예상하는 것이 아니라 이러한 예외에 대해 애플리케이션 코드를 준비해야 합니다. 이러한 경우에는 <code>JdbcTransactionManager</code>를 선택하는 것이 좋습니다.</p>
</blockquote>
<p>예외 동작 측면에서 <code>JdbcTransactionManager</code>는 <code>JpaTransactionManager</code> 및 <code>R2dbcTransactionManager</code>와 거의 동일하며 서로에 대한 즉각적인 동반(companion)/대체(replacement) 역할을 합니다. 반면에 <code>DataSourceTransactionManager</code>는 <code>JtaTransactionManager</code>와 동일하며 <code>JtaTransactionManager</code>를 직접 대체할 수 있습니다.</p>