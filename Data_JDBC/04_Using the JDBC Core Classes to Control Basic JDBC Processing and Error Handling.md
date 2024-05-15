<p>이 섹션에서는 JDBC core 클래스들을 사용하여 오류 처리를 포함한 기본 JDBC 처리를 제어하는 방법을 다룹니다. 여기에는 다음 주제가 포함됩니다.</p>
<ul>
<li><code>JdbcTemplate</code> 사용</li>
<li><code>NamedParameterJdbcTemplate</code> 사용</li>
<li>통합 JDBC 쿼리/업데이트 작업: <code>JdbcClient</code></li>
<li><code>SQLExceptionTranslator</code> 사용</li>
<li>Statements 실행</li>
<li>쿼리 실행</li>
<li>데이터베이스 업데이트</li>
<li>자동 생성된(auto-generated) 키 검색</li>
</ul>
<h1 id="using-jdbctemplate">Using <code>JdbcTemplate</code></h1>
<p><code>JdbcTemplate</code>은 JDBC 코어 패키지의 중심 클래스입니다. 이는 리소스 생성 및 해제를 처리하므로 연결을 닫는 것을 잊어버리는 등의 일반적인 오류를 방지하는 데 도움이 됩니다. 이는 핵심 JDBC 워크플로우의 기본 작업(예: 명령문 생성 및 실행)을 수행하고, 애플리케이션 코드는 SQL을 제공하고 결과를 추출하도록 남겨둡니다.	<code>JdbcTemplate</code> 클래스:</p>
<ul>
<li>SQL 쿼리를 실행합니다.</li>
<li>명령문 및 저장된 프로시저 호출을 업데이트합니다.</li>
<li><code>ResultSet</code> 인스턴스에 대해 반복을 수행하고 반환된 매개변수 값을 추출합니다.</li>
<li>JDBC 예외를 포착하고 이를 <code>org.springframework.dao</code> 패키지에 정의된 일반적이고 더 많은 정보를 제공하는 예외 계층 구조로 변환합니다. (<a href="https://docs.spring.io/spring-framework/reference/data-access/dao.html#dao-exceptions">일관된 예외 계층 구조를 참조하세요.</a>)</li>
</ul>
<p>코드에 <code>JdbcTemplate</code>을 사용할 때 콜백 인터페이스를 구현하고 명확하게 정의된 계약(contract)을 제공하기만 하면 됩니다. <code>JdbcTemplate</code> 클래스에서 제공하는 <code>Connection</code>이 주어지면, <code>preparedStatementCreator</code> 콜백 인터페이스는 SQL 및 필요한 매개변수를 제공하는 준비된 명령문을 생성합니다. 호출 가능한 명령문을 생성하는 <code>CallableStatementCreator</code> 인터페이스의 경우에도 마찬가지입니다. <code>RowCallbackHandler</code> 인터페이스는 <code>ResultSet</code>의 각 행에서 값을 추출합니다.</p>
<p><code>DataSource</code> 참조로 직접 인스턴스화를 통해 DAO 구현 내에서 <code>JdbcTemplate</code>을 사용하거나 Spring IoC 컨테이너에서 구성하여 DAO에 Bean 참조로 제공할 수 있습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
<code>DataSource</code>는 항상 Spring IoC 컨테이너에서 빈으로 구성되어야 합니다. 첫 번째 경우에는 Bean이 서비스에 직접 제공됩니다. 두 번째 경우에는 준비된 템플릿에 제공됩니다.</p>
</blockquote>
<p>이 클래스에 의해 실행된 모든 SQL은 템플릿 인스턴스(일반적으로 <code>JdbcTemplate</code>이지만 <code>JdbcTemplate</code> 클래스의 사용자 정의 하위 클래스를 사용하는 경우 다를 수 있음)의 정규화된 클래스 이름에 해당하는 범주 아래 <code>DEBUG</code> 수준에 기록(logged)됩니다.</p>
<p>다음 섹션에서는 <code>JdbcTemplate</code> 사용의 몇 가지 예를 제공합니다. 이 예제는 <code>JdbcTemplate</code>에 의해 노출된 모든 기능의 완전한 목록이 아닙니다. 이에 대해서는 따라오는 <a href="https://docs.spring.io/spring-framework/docs/6.1.4/javadoc-api/org/springframework/jdbc/core/JdbcTemplate.html">javadoc</a>을 참조하십시오.</p>
<h2 id="querying-select">Querying (<code>SELECT</code>)</h2>
<p>다음 쿼리는 관계(relation)의 행(row) 수를 가져옵니다.</p>
<pre><code class="language-java"><span class="token keyword">int</span> rowCount <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">.</span>jdbcTemplate<span class="token punctuation">.</span><span class="token function">queryForObject</span><span class="token punctuation">(</span><span class="token string">"select count(*) from t_actor"</span><span class="token punctuation">,</span> <span class="token class-name">Integer</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>다음 쿼리는 바인드(bind) 변수를 사용합니다.</p>
<pre><code class="language-java"><span class="token keyword">int</span> countOfActorsNamedJoe <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">.</span>jdbcTemplate<span class="token punctuation">.</span><span class="token function">queryForObject</span><span class="token punctuation">(</span>
		<span class="token string">"select count(*) from t_actor where first_name = ?"</span><span class="token punctuation">,</span> <span class="token class-name">Integer</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> <span class="token string">"Joe"</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>다음 쿼리는 <code>String</code>을 찾습니다.</p>
<pre><code class="language-java"><span class="token class-name">String</span> lastName <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">.</span>jdbcTemplate<span class="token punctuation">.</span><span class="token function">queryForObject</span><span class="token punctuation">(</span>
		<span class="token string">"select last_name from t_actor where id = ?"</span><span class="token punctuation">,</span>
		<span class="token class-name">String</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> <span class="token number">1212L</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>다음 쿼리는 단일 도메인 객체를 찾아서 채웁(populate)니다.</p>
<pre><code class="language-java"><span class="token class-name">Actor</span> actor <span class="token operator">=</span> jdbcTemplate<span class="token punctuation">.</span><span class="token function">queryForObject</span><span class="token punctuation">(</span>
		<span class="token string">"select first_name, last_name from t_actor where id = ?"</span><span class="token punctuation">,</span>
		<span class="token punctuation">(</span>resultSet<span class="token punctuation">,</span> rowNum<span class="token punctuation">)</span> <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
			<span class="token class-name">Actor</span> newActor <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Actor</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			newActor<span class="token punctuation">.</span><span class="token function">setFirstName</span><span class="token punctuation">(</span>resultSet<span class="token punctuation">.</span><span class="token function">getString</span><span class="token punctuation">(</span><span class="token string">"first_name"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			newActor<span class="token punctuation">.</span><span class="token function">setLastName</span><span class="token punctuation">(</span>resultSet<span class="token punctuation">.</span><span class="token function">getString</span><span class="token punctuation">(</span><span class="token string">"last_name"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			<span class="token keyword">return</span> newActor<span class="token punctuation">;</span>
		<span class="token punctuation">}</span><span class="token punctuation">,</span>
		<span class="token number">1212L</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>다음 쿼리는 도메인 객체 목록을 찾아서 채웁니다.</p>
<pre><code class="language-java"><span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Actor</span><span class="token punctuation">&gt;</span></span> actors <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">.</span>jdbcTemplate<span class="token punctuation">.</span><span class="token function">query</span><span class="token punctuation">(</span>
		<span class="token string">"select first_name, last_name from t_actor"</span><span class="token punctuation">,</span>
		<span class="token punctuation">(</span>resultSet<span class="token punctuation">,</span> rowNum<span class="token punctuation">)</span> <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
			<span class="token class-name">Actor</span> actor <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Actor</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			actor<span class="token punctuation">.</span><span class="token function">setFirstName</span><span class="token punctuation">(</span>resultSet<span class="token punctuation">.</span><span class="token function">getString</span><span class="token punctuation">(</span><span class="token string">"first_name"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			actor<span class="token punctuation">.</span><span class="token function">setLastName</span><span class="token punctuation">(</span>resultSet<span class="token punctuation">.</span><span class="token function">getString</span><span class="token punctuation">(</span><span class="token string">"last_name"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			<span class="token keyword">return</span> actor<span class="token punctuation">;</span>
		<span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>마지막 두 코드 조각이 실제로 동일한 애플리케이션에 존재하는 경우 두 개의 <code>RowMapper</code> 람다 식에 있는 중복을 제거하고 필요에 따라 DAO 메서드에서 참조할 수 있는 단일 필드로 추출하는 것이 합리적입니다. 예를 들어 앞의 코드 조각을 다음과 같이 작성하는 것이 더 나을 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">RowMapper</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Actor</span><span class="token punctuation">&gt;</span></span> actorRowMapper <span class="token operator">=</span> <span class="token punctuation">(</span>resultSet<span class="token punctuation">,</span> rowNum<span class="token punctuation">)</span> <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
	<span class="token class-name">Actor</span> actor <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Actor</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	actor<span class="token punctuation">.</span><span class="token function">setFirstName</span><span class="token punctuation">(</span>resultSet<span class="token punctuation">.</span><span class="token function">getString</span><span class="token punctuation">(</span><span class="token string">"first_name"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	actor<span class="token punctuation">.</span><span class="token function">setLastName</span><span class="token punctuation">(</span>resultSet<span class="token punctuation">.</span><span class="token function">getString</span><span class="token punctuation">(</span><span class="token string">"last_name"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token keyword">return</span> actor<span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Actor</span><span class="token punctuation">&gt;</span></span> <span class="token function">findAllActors</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>jdbcTemplate<span class="token punctuation">.</span><span class="token function">query</span><span class="token punctuation">(</span><span class="token string">"select first_name, last_name from t_actor"</span><span class="token punctuation">,</span> actorRowMapper<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="updating-insert-update-and-delete-with-jdbctemplate">Updating (<code>INSERT</code>, <code>UPDATE</code> and <code>DELETE</code>) with <code>jdbcTemplate</code></h2>
<p><code>update(..)</code> 메서드를 사용하여 삽입, 업데이트 및 삭제 작업을 수행할 수 있습니다. 매개변수 값은 일반적으로 가변(variable) 인수로 제공되거나 객체 배열로 제공됩니다. 다음 예에서는 새 항목(entry)을 삽입합니다.</p>
<pre><code class="language-java"><span class="token keyword">this</span><span class="token punctuation">.</span>jdbcTemplate<span class="token punctuation">.</span><span class="token function">update</span><span class="token punctuation">(</span>
		<span class="token string">"insert into t_actor (first_name, last_name) values (?, ?)"</span><span class="token punctuation">,</span>
		<span class="token string">"Leonor"</span><span class="token punctuation">,</span> <span class="token string">"Watling"</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>다음 예에서는 기존 항목을 업데이트합니다.</p>
<pre><code class="language-java"><span class="token keyword">this</span><span class="token punctuation">.</span>jdbcTemplate<span class="token punctuation">.</span><span class="token function">update</span><span class="token punctuation">(</span>
		<span class="token string">"update t_actor set last_name = ? where id = ?"</span><span class="token punctuation">,</span>
		<span class="token string">"Banjo"</span><span class="token punctuation">,</span> <span class="token number">5276L</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>다음 예에서는 항목을 삭제합니다.</p>
<pre><code class="language-java"><span class="token keyword">this</span><span class="token punctuation">.</span>jdbcTemplate<span class="token punctuation">.</span><span class="token function">update</span><span class="token punctuation">(</span>
		<span class="token string">"delete from t_actor where id = ?"</span><span class="token punctuation">,</span>
		<span class="token class-name">Long</span><span class="token punctuation">.</span><span class="token function">valueOf</span><span class="token punctuation">(</span>actorId<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<h2 id="other-jdbctemplate-operations">Other <code>jdbcTemplate</code> Operations</h2>
<p>임의의 SQL을 실행하려면 <code>execute(..)</code> 메서드를 사용할 수 있습니다. 결과적으로 이 메서드는 DDL 문에 자주 사용됩니다. 콜백 인터페이스, 변수 배열에 바인딩하기 등을 사용하는 변형으로 인해 과부하가 심하게 발생합니다(또는 overload 되었습니다.) 다음 예시에서는 테이블을 생성합니다.</p>
<pre><code class="language-java"><span class="token keyword">this</span><span class="token punctuation">.</span>jdbcTemplate<span class="token punctuation">.</span><span class="token function">execute</span><span class="token punctuation">(</span><span class="token string">"create table mytable (id integer, name varchar(100))"</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>다음 예에서는 저장된 프로시저를 호출합니다.</p>
<pre><code class="language-java"><span class="token keyword">this</span><span class="token punctuation">.</span>jdbcTemplate<span class="token punctuation">.</span><span class="token function">update</span><span class="token punctuation">(</span>
		<span class="token string">"call SUPPORT.REFRESH_ACTORS_SUMMARY(?)"</span><span class="token punctuation">,</span>
		<span class="token class-name">Long</span><span class="token punctuation">.</span><span class="token function">valueOf</span><span class="token punctuation">(</span>unionId<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>보다 정교한 저장 프로시저 지원은 <a href="https://docs.spring.io/spring-framework/reference/data-access/jdbc/object.html#jdbc-StoredProcedure">나중에</a> 다룹니다.</p>
<h2 id="jdbctemplate-best-practices"><code>JdbcTemplate</code> Best Practices</h2>
<p><code>JdbcTemplate</code> 클래스의 인스턴스는 일단 구성되면 스레드로부터 안전합니다. 이는 JdbcTemplate의 단일 인스턴스를 구성한 다음 이 공유(shared) 참조를 여러 DAO(또는 저장소)에 안전하게 삽입할 수 있다는 의미이므로 중요합니다. <code>JdbcTemplate</code>은 <code>DataSource</code>에 대한 참조를 유지한다는 점에서 stateful이지만 이 상태는 conversational state가 아닙니다.</p>
<p><code>JdbcTemplate</code> 클래스(및 관련 <code>NamedParameterJdbcTemplate</code> 클래스)를 사용할 때 일반적인 방법은 Spring 구성 파일에서 <code>DataSource</code>를 구성한 다음 해당 공유 <code>DataSource</code> 빈을 DAO 클래스에 종속성 주입하는 것입니다. <code>JdbcTemplate</code>은 <code>DataSource</code>의 setter에서 생성됩니다. 이로 인해 다음과 유사한 DAO가 생성됩니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">JdbcCorporateEventDao</span> <span class="token keyword">implements</span> <span class="token class-name">CorporateEventDao</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">JdbcTemplate</span> jdbcTemplate<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setDataSource</span><span class="token punctuation">(</span><span class="token class-name">DataSource</span> dataSource<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>jdbcTemplate <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">JdbcTemplate</span><span class="token punctuation">(</span>dataSource<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// JDBC-backed implementations of the methods on the CorporateEventDao follow...</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 예에서는 해당 XML 구성을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token prolog">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>context</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/context<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>
		http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context
		https://www.springframework.org/schema/context/spring-context.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>corporateEventDao<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.example.JdbcCorporateEventDao<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>dataSource<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>dataSource<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>dataSource<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.apache.commons.dbcp.BasicDataSource<span class="token punctuation">"</span></span> <span class="token attr-name">destroy-method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>close<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>driverClassName<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.driverClassName}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>url<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.url}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>username<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.username}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>password<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.password}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>property-placeholder</span> <span class="token attr-name">location</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>jdbc.properties<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>명시적 구성의 대안은 종속성 주입을 위한 구성 요소 검색 및 어노테이션 지원을 사용하는 것입니다. 이 경우 클래스에 <code>@Repository</code>(컴포넌트 검색 후보가 됨)로 어노테이션을 달고 <code>DataSource</code> setter 메서드에 <code>@Autowired</code>로 어노테이션을 달 수 있습니다. 다음 예에서는 그 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Repository</span> <span class="token comment">// (1)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">JdbcCorporateEventDao</span> <span class="token keyword">implements</span> <span class="token class-name">CorporateEventDao</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">JdbcTemplate</span> jdbcTemplate<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Autowired</span> <span class="token comment">// (2)</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setDataSource</span><span class="token punctuation">(</span><span class="token class-name">DataSource</span> dataSource<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>jdbcTemplate <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">JdbcTemplate</span><span class="token punctuation">(</span>dataSource<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// (3)</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// JDBC-backed implementations of the methods on the CorporateEventDao follow...</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) <code>@Repository</code>로 클래스에 어노테이션을 답니다.<br>
(2) <code>@Autowired</code>로 <code>DataSource</code> setter 메소드에 어노테이션을 답니다.<br>
(3) <code>DataSource</code>를 사용하여 새 <code>JdbcTemplate</code>을 만듭니다. </p>
<p>다음 예에서는 해당 XML 구성을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token prolog">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>context</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/context<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>
		http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context
		https://www.springframework.org/schema/context/spring-context.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token comment">&lt;!-- Scans within the base package of the application for @Component classes to configure as beans --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>component-scan</span> <span class="token attr-name">base-package</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.docs.test<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>dataSource<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.apache.commons.dbcp.BasicDataSource<span class="token punctuation">"</span></span> <span class="token attr-name">destroy-method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>close<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>driverClassName<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.driverClassName}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>url<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.url}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>username<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.username}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>password<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.password}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>property-placeholder</span> <span class="token attr-name">location</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>jdbc.properties<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>Spring의 <code>JdbcDaoSupport</code> 클래스를 사용하고 다양한 JDBC 지원 DAO 클래스가 이 클래스에서 확장되면 하위 클래스는 <code>JdbcDaoSupport</code> 클래스에서 <code>setDataSource(..)</code> 메서드를 상속합니다. 이 클래스에서 상속할지 여부를 선택할 수 있습니다. <code>JdbcDaoSupport</code> 클래스는 편의를 위해서만 제공됩니다.</p>
<p>위의 템플릿 초기화 스타일 중 어떤 것을 사용하기로 선택했는지 여부에 관계없이 SQL을 실행할 때마다 <code>JdbcTemplate</code> 클래스의 새 인스턴스를 생성할 필요가 거의 없습니다. 일단 구성되면 <code>JdbcTemplate</code> 인스턴스는 스레드로부터 안전합니다. 애플리케이션이 여러 데이터베이스에 액세스하는 경우 여러 <code>JdbcTemplate</code> 인스턴스가 필요할 수 있으며, 이를 위해서는 여러 <code>DataSource</code>가 필요하고 이후에는 다르게 구성된 여러 <code>JdbcTemplate</code> 인스턴스가 필요합니다.</p>
<h1 id="using-namedparameterjdbctemplate">Using <code>NamedParameterJdbcTemplate</code></h1>
<p><code>NamedParameterJdbcTemplate</code> 클래스는 기존 자리 표시자(<code>'?'</code>) 인수만 사용하여 JDBC 문을 프로그래밍하는 것과 달리 명명된 매개 변수를 사용하여 JDBC 문 프로그래밍에 대한 지원을 추가합니다. <code>NamedParameterJdbcTemplate</code> 클래스는 <code>JdbcTemplate</code>을 래핑하고 래핑된 <code>JdbcTemplate</code>에 위임하여 많은 작업을 수행합니다. 이 섹션에서는 <code>JdbcTemplate</code> 자체와 다른 <code>NamedParameterJdbcTemplate</code> 클래스의 영역, 즉 명명된 매개 변수를 사용하여 JDBC 문을 프로그래밍하는 영역만 설명합니다. 다음 예에서는 <code>NamedParameterJdbcTemplate</code>을 사용하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token comment">// some JDBC-backed DAO class...</span>
<span class="token keyword">private</span> <span class="token class-name">NamedParameterJdbcTemplate</span> namedParameterJdbcTemplate<span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setDataSource</span><span class="token punctuation">(</span><span class="token class-name">DataSource</span> dataSource<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">this</span><span class="token punctuation">.</span>namedParameterJdbcTemplate <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">NamedParameterJdbcTemplate</span><span class="token punctuation">(</span>dataSource<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token keyword">public</span> <span class="token keyword">int</span> <span class="token function">countOfActorsByFirstName</span><span class="token punctuation">(</span><span class="token class-name">String</span> firstName<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token class-name">String</span> sql <span class="token operator">=</span> <span class="token string">"select count(*) from t_actor where first_name = :first_name"</span><span class="token punctuation">;</span>
	<span class="token class-name">SqlParameterSource</span> namedParameters <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">MapSqlParameterSource</span><span class="token punctuation">(</span><span class="token string">"first_name"</span><span class="token punctuation">,</span> firstName<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>namedParameterJdbcTemplate<span class="token punctuation">.</span><span class="token function">queryForObject</span><span class="token punctuation">(</span>sql<span class="token punctuation">,</span> namedParameters<span class="token punctuation">,</span> <span class="token class-name">Integer</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p><code>sql</code> 변수에 할당된 값과 <code>namedParameters</code> 변수(<code>MapSqlParameterSource</code> 타입)에 연결(plugged into)되는 해당 값에서 명명된 매개 변수 표기법을 사용합니다.</p>
<p>또는 <code>Map</code> 기반 스타일을 사용하여 명명된 매개 변수와 해당 값을 <code>NamedParameterJdbcTemplate</code> 인스턴스에 전달할 수 있습니다. <code>NamedParameterJdbcOperations</code>에 의해 노출되고 <code>NamedParameterJdbcTemplate</code> 클래스에 의해 구현되는 나머지 메서드는 비슷한 패턴을 따르므로 여기서는 다루지 않습니다.</p>
<p>다음 예에서는 <code>Map</code> 기반 스타일의 사용을 보여줍니다.</p>
<pre><code class="language-java"><span class="token comment">// some JDBC-backed DAO class...</span>
<span class="token keyword">private</span> <span class="token class-name">NamedParameterJdbcTemplate</span> namedParameterJdbcTemplate<span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setDataSource</span><span class="token punctuation">(</span><span class="token class-name">DataSource</span> dataSource<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">this</span><span class="token punctuation">.</span>namedParameterJdbcTemplate <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">NamedParameterJdbcTemplate</span><span class="token punctuation">(</span>dataSource<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token keyword">public</span> <span class="token keyword">int</span> <span class="token function">countOfActorsByFirstName</span><span class="token punctuation">(</span><span class="token class-name">String</span> firstName<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token class-name">String</span> sql <span class="token operator">=</span> <span class="token string">"select count(*) from t_actor where first_name = :first_name"</span><span class="token punctuation">;</span>
	<span class="token class-name">Map</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> namedParameters <span class="token operator">=</span> <span class="token class-name">Collections</span><span class="token punctuation">.</span><span class="token function">singletonMap</span><span class="token punctuation">(</span><span class="token string">"first_name"</span><span class="token punctuation">,</span> firstName<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>namedParameterJdbcTemplate<span class="token punctuation">.</span><span class="token function">queryForObject</span><span class="token punctuation">(</span>sql<span class="token punctuation">,</span> namedParameters<span class="token punctuation">,</span> <span class="token class-name">Integer</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p><code>NamedParameterJdbcTemplate</code>(및 동일한 Java 패키지에 존재)과 관련된 유용한 기능 중 하나는 <code>SqlParameterSource</code> 인터페이스입니다. 이전 코드 조각(<code>MapSqlParameterSource</code> 클래스) 중 하나에서 이 인터페이스 구현의 예를 이미 확인했습니다. <code>SqlParameterSource</code>는 <code>NamedParameterJdbcTemplate</code>에 대한 명명된 매개 변수 값의 소스입니다. <code>MapSqlParameterSource</code> 클래스는 <code>java.util.Map</code> 주변의 어댑터의 간단한 구현입니다. 여기서 키는 매개변수 이름이고 값은 매개변수 값입니다.</p>
<p>또 다른 <code>SqlParameterSource</code> 구현은 <code>BeanPropertySqlParameterSource</code> 클래스입니다. 이 클래스는 임의의 JavaBean(즉, <a href="https://www.oracle.com/technetwork/java/javase/documentation/spec-136004.html">JavaBean 규칙을 준수하는 클래스</a>의 인스턴스)을 래핑하고 래핑된 JavaBean의 속성(property)을 명명된 매개변수 값의 소스로 사용합니다.</p>
<p>다음 예는 전형적인 JavaBean을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Actor</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">Long</span> id<span class="token punctuation">;</span>
	<span class="token keyword">private</span> <span class="token class-name">String</span> firstName<span class="token punctuation">;</span>
	<span class="token keyword">private</span> <span class="token class-name">String</span> lastName<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getFirstName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>firstName<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getLastName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>lastName<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token class-name">Long</span> <span class="token function">getId</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>id<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// setters omitted...</span>
<span class="token punctuation">}</span></code></pre>
<p><code>NamedParameterJdbcTemplate</code>을 사용하여 이전 예제에 표시된 클래스 멤버 수를 반환합니다.</p>
<pre><code class="language-java"><span class="token comment">// some JDBC-backed DAO class...</span>
<span class="token keyword">private</span> <span class="token class-name">NamedParameterJdbcTemplate</span> namedParameterJdbcTemplate<span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setDataSource</span><span class="token punctuation">(</span><span class="token class-name">DataSource</span> dataSource<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">this</span><span class="token punctuation">.</span>namedParameterJdbcTemplate <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">NamedParameterJdbcTemplate</span><span class="token punctuation">(</span>dataSource<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token keyword">public</span> <span class="token keyword">int</span> <span class="token function">countOfActors</span><span class="token punctuation">(</span><span class="token class-name">Actor</span> exampleActor<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// notice how the named parameters match the properties of the above 'Actor' class</span>
	<span class="token class-name">String</span> sql <span class="token operator">=</span> <span class="token string">"select count(*) from t_actor where first_name = :firstName and last_name = :lastName"</span><span class="token punctuation">;</span>
	<span class="token class-name">SqlParameterSource</span> namedParameters <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">BeanPropertySqlParameterSource</span><span class="token punctuation">(</span>exampleActor<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>namedParameterJdbcTemplate<span class="token punctuation">.</span><span class="token function">queryForObject</span><span class="token punctuation">(</span>sql<span class="token punctuation">,</span> namedParameters<span class="token punctuation">,</span> <span class="token class-name">Integer</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p><code>NamedParameterJdbcTemplate</code> 클래스는 클래식 <code>JdbcTemplate</code> 템플릿을 래핑한다는 점을 기억하세요. <code>JdbcTemplate</code> 클래스에만 존재하는 기능에 액세스하기 위해 래핑된 <code>JdbcTemplate</code> 인스턴스에 액세스해야 하는 경우 <code>getJdbcOperations()</code> 메서드를 사용하여 <code>JdbcOperations</code> 인터페이스를 통해 래핑된 <code>JdbcTemplate</code>에 액세스할 수 있습니다.</p>
<p>애플리케이션 컨텍스트에서 <code>NamedParameterJdbcTemplate</code> 클래스를 사용하는 방법에 대한 지침은 <a href="https://docs.spring.io/spring-framework/reference/data-access/jdbc/core.html#jdbc-JdbcTemplate-idioms"><code>JdbcTemplate</code> 모범 사례</a>도 참조하세요.</p>
<h1 id="unified-jdbc-queryupdate-operations-jdbcclient">Unified JDBC Query/Update Operations: <code>JdbcClient</code></h1>
<p>6.1부터 <code>NamedParameterJdbcTemplate</code>의 명명된 매개 변수 문과 일반 <code>JdbcTemplate</code>의 위치 매개 변수 문은 유려한 상호 작용 모델을 갖춘 통합 클라이언트 API를 통해 사용할 수 있습니다.</p>
<p>예를 들어, 위치 매개변수의 경우:</p>
<pre><code class="language-java"><span class="token keyword">private</span> <span class="token class-name">JdbcClient</span> jdbcClient <span class="token operator">=</span> <span class="token class-name">JdbcClient</span><span class="token punctuation">.</span><span class="token function">create</span><span class="token punctuation">(</span>dataSource<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">int</span> <span class="token function">countOfActorsByFirstName</span><span class="token punctuation">(</span><span class="token class-name">String</span> firstName<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>jdbcClient<span class="token punctuation">.</span><span class="token function">sql</span><span class="token punctuation">(</span><span class="token string">"select count(*) from t_actor where first_name = ?"</span><span class="token punctuation">)</span>
			<span class="token punctuation">.</span><span class="token function">param</span><span class="token punctuation">(</span>firstName<span class="token punctuation">)</span>
			<span class="token punctuation">.</span><span class="token function">query</span><span class="token punctuation">(</span><span class="token class-name">Integer</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">single</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>예를 들어 명명된 매개변수를 사용하면 다음과 같습니다.</p>
<pre><code class="language-java"><span class="token keyword">private</span> <span class="token class-name">JdbcClient</span> jdbcClient <span class="token operator">=</span> <span class="token class-name">JdbcClient</span><span class="token punctuation">.</span><span class="token function">create</span><span class="token punctuation">(</span>dataSource<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">int</span> <span class="token function">countOfActorsByFirstName</span><span class="token punctuation">(</span><span class="token class-name">String</span> firstName<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>jdbcClient<span class="token punctuation">.</span><span class="token function">sql</span><span class="token punctuation">(</span><span class="token string">"select count(*) from t_actor where first_name = :firstName"</span><span class="token punctuation">)</span>
			<span class="token punctuation">.</span><span class="token function">param</span><span class="token punctuation">(</span><span class="token string">"firstName"</span><span class="token punctuation">,</span> firstName<span class="token punctuation">)</span>
			<span class="token punctuation">.</span><span class="token function">query</span><span class="token punctuation">(</span><span class="token class-name">Integer</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">single</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>유연한 결과 resolution을 통해 <code>RowMapper</code> 기능도 사용할 수 있습니다.</p>
<pre><code class="language-java"><span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Actor</span><span class="token punctuation">&gt;</span></span> actors <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">.</span>jdbcClient<span class="token punctuation">.</span><span class="token function">sql</span><span class="token punctuation">(</span><span class="token string">"select first_name, last_name from t_actor"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">query</span><span class="token punctuation">(</span><span class="token punctuation">(</span>rs<span class="token punctuation">,</span> rowNum<span class="token punctuation">)</span> <span class="token operator">-&gt;</span> <span class="token keyword">new</span> <span class="token class-name">Actor</span><span class="token punctuation">(</span>rs<span class="token punctuation">.</span><span class="token function">getString</span><span class="token punctuation">(</span><span class="token string">"first_name"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> rs<span class="token punctuation">.</span><span class="token function">getString</span><span class="token punctuation">(</span><span class="token string">"last_name"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">list</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>사용자 정의 <code>RowMapper</code> 대신 매핑할 클래스를 지정할 수도 있습니다. 예를 들어 <code>Actor</code>가 레코드 클래스, 사용자 정의 생성자, Bean 속성 또는 일반 필드로 <code>firstName</code> 및 <code>lastName</code> 속성(property)을 가지고 있다고 가정합니다.</p>
<pre><code class="language-java"><span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Actor</span><span class="token punctuation">&gt;</span></span> actors <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">.</span>jdbcClient<span class="token punctuation">.</span><span class="token function">sql</span><span class="token punctuation">(</span><span class="token string">"select first_name, last_name from t_actor"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">query</span><span class="token punctuation">(</span><span class="token class-name">Actor</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">list</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>필수 단일 객체 결과:</p>
<pre><code class="language-java"><span class="token class-name">Actor</span> actor <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">.</span>jdbcClient<span class="token punctuation">.</span><span class="token function">sql</span><span class="token punctuation">(</span><span class="token string">"select first_name, last_name from t_actor where id = ?"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">param</span><span class="token punctuation">(</span><span class="token number">1212L</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">query</span><span class="token punctuation">(</span><span class="token class-name">Actor</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">single</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p><code>java.util.Optional</code> 결과:</p>
<pre><code class="language-java"><span class="token class-name">Optional</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Actor</span><span class="token punctuation">&gt;</span></span> actor <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">.</span>jdbcClient<span class="token punctuation">.</span><span class="token function">sql</span><span class="token punctuation">(</span><span class="token string">"select first_name, last_name from t_actor where id = ?"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">param</span><span class="token punctuation">(</span><span class="token number">1212L</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">query</span><span class="token punctuation">(</span><span class="token class-name">Actor</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">optional</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>그리고 업데이트 문의 경우:</p>
<pre><code class="language-java"><span class="token keyword">this</span><span class="token punctuation">.</span>jdbcClient<span class="token punctuation">.</span><span class="token function">sql</span><span class="token punctuation">(</span><span class="token string">"insert into t_actor (first_name, last_name) values (?, ?)"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">param</span><span class="token punctuation">(</span><span class="token string">"Leonor"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">param</span><span class="token punctuation">(</span><span class="token string">"Watling"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">update</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>또는 명명된 매개변수가 있는 업데이트 문:</p>
<pre><code class="language-java"><span class="token keyword">this</span><span class="token punctuation">.</span>jdbcClient<span class="token punctuation">.</span><span class="token function">sql</span><span class="token punctuation">(</span><span class="token string">"insert into t_actor (first_name, last_name) values (:firstName, :lastName)"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">param</span><span class="token punctuation">(</span><span class="token string">"firstName"</span><span class="token punctuation">,</span> <span class="token string">"Leonor"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">param</span><span class="token punctuation">(</span><span class="token string">"lastName"</span><span class="token punctuation">,</span> <span class="token string">"Watling"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">update</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>개별 명명된 매개변수 대신 매개변수 소스 객체(예: 레코드 클래스, Bean 속성(property)이 있는 클래스 또는 위의 <code>Actor</code> 클래스처럼 <code>firstName</code> 및 <code>lastName</code> 속성(property)을 제공하는 일반 필드 홀더)를 지정할 수도 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">this</span><span class="token punctuation">.</span>jdbcClient<span class="token punctuation">.</span><span class="token function">sql</span><span class="token punctuation">(</span><span class="token string">"insert into t_actor (first_name, last_name) values (:firstName, :lastName)"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">paramSource</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Actor</span><span class="token punctuation">(</span><span class="token string">"Leonor"</span><span class="token punctuation">,</span> <span class="token string">"Watling"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">update</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>매개변수에 대한 자동 <code>Actor</code> 클래스 매핑과 위의 쿼리 결과는 직접 사용할 수도 있는 암시적 <code>SimplePropertySqlParameterSource</code> 및 <code>SimplePropertyRowMapper</code> 전략을 통해 제공됩니다. 이는 <code>JdbcTemplate</code> 및 <code>NamedParameterJdbcTemplate</code> 자체와 함께 <code>BeanPropertySqlParameterSource</code> 및 <code>BeanPropertyRowMapper</code>/<code>DataClassRowMapper</code>에 대한 일반적인 대체(common replacement) 역할을 할 수 있습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
<code>JdbcClient</code>는 JDBC 쿼리/업데이트 문을 위한 유연하지만 단순화된 facade입니다. 일괄(batch) 삽입 및 저장된 프로시저 호출과 같은 고급 기능에는 일반적으로 추가 사용자 정의가 필요합니다. <code>JdbcClient</code>에서 사용할 수 없는 이러한 기능에 대해서는 Spring의 <code>SimpleJdbcInsert</code> 및 <code>SimpleJdbcCall</code> 클래스 또는 일반 직접 <code>JdbcTemplate</code> 사용을 고려하세요.</p>
</blockquote>
<h1 id="using-sqlecveptiontranslator">Using <code>SQLEcveptionTranslator</code></h1>
<p><code>SQLExceptionTranslator</code>는 <code>SQLException</code>과 Spring 자체의 <code>org.springframework.dao.DataAccessException</code> 사이를 변환(translate)할 수 있는 클래스에 의해 구현되는 인터페이스입니다. 이는 데이터 액세스 전략과 관련하여 불가지론적(agnostic)입니다. 정밀도를 높이기 위해 구현은 일반적(generic)(예: JDBC용 SQLState 코드 사용) 또는 독점적(proprietary)(예: Oracle 오류 코드 사용)일 수 있습니다. 이 예외 변환 메커니즘은 <code>SQLException</code>을 전파하지 않고 <code>DataAccessException</code>을 전파하는 공통 <code>JdbcTemplate</code> 및 <code>JdbcTransactionManager</code> 진입점 뒤에서 사용됩니다.</p>
<hr>
<p>여기서 "generic"과 "proprietary"는 SQLExceptionTranslator 인터페이스의 구현체가 다양한 방식으로 SQLException을 처리하는 방식을 설명합니다.</p>
<p>Generic: Generic 구현은 JDBC의 SQLState 코드 등과 같은 일반적인 정보를 사용하여 SQLException을 처리합니다. 이는 특정 데이터베이스 제품에 종속되지 않고, 일반적인 에러 상황을 다룰 수 있도록 합니다. 예를 들어, SQLExceptionTranslator 구현체가 JDBC의 SQLState 코드에 따라 예외를 변환할 수 있습니다. 이것은 여러 데이터베이스에서 유사한 예외를 처리하는 데 유용합니다.</p>
<p>Proprietary: Proprietary 구현은 특정 데이터베이스 제품의 오류 코드 등과 같은 고유한 정보를 사용하여 SQLException을 처리합니다. 이는 특정 데이터베이스 벤더의 특수한 예외 처리를 지원합니다. 예를 들어, Oracle 데이터베이스의 경우 특정 오류 코드가 있는데, 이를 이용하여 SQLException을 정확하게 처리할 수 있습니다.</p>
<p>즉, generic 구현은 데이터베이스 종류에 관계없이 일반적인 방식으로 SQLException을 처리하고, proprietary 구현은 특정 데이터베이스 제품에 종속된 방식으로 SQLException을 처리합니다.</p>
<hr>
<blockquote>
<p><strong>[Note]</strong><br>
6.0부터 기본 예외 변환기는 <code>SQLExceptionSubclassTranslator</code>로, 몇 가지 추가 검사를 통해 JDBC 4 <code>SQLException</code> 하위 클래스를 감지하고 <code>SQLStateSQLExceptionTranslator</code>를 통해 <code>SQLState</code> 자체 검사를 수행합니다. 이는 일반적으로 일반적인 데이터베이스 액세스에 충분하며 공급업체별 감지가 필요하지 않습니다. 이전 버전과의 호환성을 위해 아래 설명된 대로 잠재적으로 사용자 지정 오류 코드 매핑과 함께 <code>SQLErrorCodeSQLExceptionTranslator</code>를 사용하는 것을 고려하세요.</p>
</blockquote>
<p><code>SQLErrorCodeSQLExceptionTranslator</code>는 classpath의 루트에 <code>sql-error-codes.xml</code>이라는 파일이 있을 때 기본적으로 사용되는 <code>SQLExceptionTranslator</code>의 구현입니다. 이 구현에서는 특정 공급업체 코드를 사용합니다. 이는 <code>SQLState</code> 또는 <code>SQLException</code> 하위 클래스 변환보다 더 정확합니다. 오류 코드 변환은 <code>SQLErrorCodes</code>라는 JavaBean 타입 클래스에 포함된 코드를 기반으로 합니다. 이 클래스는 <code>sql-error-codes.xml</code>이라는 구성 파일의 내용을 기반으로 <code>SQLErrorCode</code>를 생성하기 위한 팩토리인 <code>SQLErrorCodesFactory</code>에 의해 생성되고 채워집니다. 이 파일은 공급업체 코드로 채워지며 <code>DatabaseMetaData</code>에서 가져온 <code>DatabaseProductName</code>을 기반으로 합니다. 사용하고 있는 실제 데이터베이스의 코드가 사용됩니다.</p>
<p><code>SQLErrorCodeSQLExceptionTranslator</code>는 다음 순서로 일치(matching) 규칙을 적용합니다.</p>
<ol>
<li>
<p>서브클래스에 의해 구현된 모든 사용자 정의 번역입니다. 일반적으로 제공된 구체(concrete) <code>SQLErrorCodeSQLExceptionTranslator</code>가 사용되므로 이 규칙은 적용되지 않습니다. 실제로 하위 클래스 구현을 제공받은 경우에만 적용됩니다.</p>
</li>
<li>
<p><code>SQLErrorCodes</code> 클래스의 <code>customSqlExceptionTranslator</code> 속성(property)으로 제공되는 <code>SQLExceptionTranslator</code> 인터페이스의 사용자 정의 구현입니다.</p>
</li>
<li>
<p><code>CustomSQLErrorCodesTranslation</code> 클래스(<code>SQLErrorCodes</code> 클래스의 <code>customTranslations</code> 속성(property)에 제공됨)의 인스턴스 목록에서 일치 항목이 검색됩니다.</p>
</li>
<li>
<p>오류 코드 일치(matching)가 적용됩니다.</p>
</li>
<li>
<p>대체(fallback) 변환기(translator)를 사용하십시오. <code>SQLExceptionSubclassTranslator</code>는 기본 대체 변환기입니다. 이 변환을 사용할 수 없는 경우 다음 대체 변환기는 <code>SQLStateSQLExceptionTranslator</code>입니다.</p>
</li>
</ol>
<blockquote>
<p><strong>[Note]</strong><br>
<code>SQLErrorCodesFactory</code>는 기본적으로 오류 코드 및 사용자 정의 예외 변환을 정의하는 데 사용됩니다. 이는 classpath의 <code>sql-error-codes.xml</code> 파일에서 조회되며, 일치하는 <code>SQLErrorCodes</code> 인스턴스는 사용 중인 데이터베이스의 데이터베이스 메타데이터에 있는 데이터베이스 이름을 기반으로 찾습니다.</p>
</blockquote>
<p>다음 예제와 같이 <code>SQLErrorCodeSQLExceptionTranslator</code>를 확장할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">CustomSQLErrorCodesTranslator</span> <span class="token keyword">extends</span> <span class="token class-name">SQLErrorCodeSQLExceptionTranslator</span> <span class="token punctuation">{</span>

	<span class="token keyword">protected</span> <span class="token class-name">DataAccessException</span> <span class="token function">customTranslate</span><span class="token punctuation">(</span><span class="token class-name">String</span> task<span class="token punctuation">,</span> <span class="token class-name">String</span> sql<span class="token punctuation">,</span> <span class="token class-name">SQLException</span> sqlEx<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">if</span> <span class="token punctuation">(</span>sqlEx<span class="token punctuation">.</span><span class="token function">getErrorCode</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">==</span> <span class="token operator">-</span><span class="token number">12345</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
			<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">DeadlockLoserDataAccessException</span><span class="token punctuation">(</span>task<span class="token punctuation">,</span> sqlEx<span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>
		<span class="token keyword">return</span> <span class="token keyword">null</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>앞의 예에서는 특정 오류 코드(<code>-12345</code>)가 변환(translate)되고 다른 오류는 기본 변환기 구현에 의해 변환됩니다. 이 사용자 정의 변환기를 사용하려면 <code>setExceptionTranslator</code> 메소드를 통해 이를 <code>JdbcTemplate</code>에 전달해야 하며 이 변환기가 필요한 모든 데이터 액세스 처리에 이 <code>JdbcTemplate</code>을 사용해야 합니다. 다음 예에서는 이 사용자 정의 변환기를 사용하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">private</span> <span class="token class-name">JdbcTemplate</span> jdbcTemplate<span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setDataSource</span><span class="token punctuation">(</span><span class="token class-name">DataSource</span> dataSource<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// create a JdbcTemplate and set data source</span>
	<span class="token keyword">this</span><span class="token punctuation">.</span>jdbcTemplate <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">JdbcTemplate</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token keyword">this</span><span class="token punctuation">.</span>jdbcTemplate<span class="token punctuation">.</span><span class="token function">setDataSource</span><span class="token punctuation">(</span>dataSource<span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token comment">// create a custom translator and set the DataSource for the default translation lookup</span>
	<span class="token class-name">CustomSQLErrorCodesTranslator</span> tr <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">CustomSQLErrorCodesTranslator</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	tr<span class="token punctuation">.</span><span class="token function">setDataSource</span><span class="token punctuation">(</span>dataSource<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token keyword">this</span><span class="token punctuation">.</span>jdbcTemplate<span class="token punctuation">.</span><span class="token function">setExceptionTranslator</span><span class="token punctuation">(</span>tr<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">updateShippingCharge</span><span class="token punctuation">(</span><span class="token keyword">long</span> orderId<span class="token punctuation">,</span> <span class="token keyword">long</span> pct<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// use the prepared JdbcTemplate for this update</span>
	<span class="token keyword">this</span><span class="token punctuation">.</span>jdbcTemplate<span class="token punctuation">.</span><span class="token function">update</span><span class="token punctuation">(</span><span class="token string">"update orders"</span> <span class="token operator">+</span>
		<span class="token string">" set shipping_charge = shipping_charge * ? / 100"</span> <span class="token operator">+</span>
		<span class="token string">" where id = ?"</span><span class="token punctuation">,</span> pct<span class="token punctuation">,</span> orderId<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p><code>sql-error-codes.xml</code>에서 오류 코드를 조회하기 위해 사용자 정의 변환기에 데이터 소스가 전달됩니다.</p>
<h1 id="running-statements">Running Statements</h1>
<p>SQL 문을 실행하려면 코드가 거의 필요하지 않습니다. <code>JdbcTemplate</code>과 함께 제공되는 편리한 메소드를 포함하여 <code>DataSource</code> 및 <code>JdbcTemplate</code>이 필요합니다. 다음 예에서는 새 테이블을 생성하는 최소한이지만 완전한 기능을 갖춘 클래스에 포함해야 할 사항을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">javax<span class="token punctuation">.</span>sql</span><span class="token punctuation">.</span><span class="token class-name">DataSource</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>jdbc<span class="token punctuation">.</span>core</span><span class="token punctuation">.</span><span class="token class-name">JdbcTemplate</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ExecuteAStatement</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">JdbcTemplate</span> jdbcTemplate<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setDataSource</span><span class="token punctuation">(</span><span class="token class-name">DataSource</span> dataSource<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>jdbcTemplate <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">JdbcTemplate</span><span class="token punctuation">(</span>dataSource<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">doExecute</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>jdbcTemplate<span class="token punctuation">.</span><span class="token function">execute</span><span class="token punctuation">(</span><span class="token string">"create table mytable (id integer, name varchar(100))"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h1 id="running-queries">Running Queries</h1>
<p>일부 쿼리 메서드는 단일 값을 반환합니다. 한 행에서 개수(count)나 특정 값을 검색하려면 <code>queryForObject(..)</code>를 사용하세요. 후자는 반환된 JDBC <code>Type</code>을 인수로 전달되는 Java 클래스로 변환합니다. 유형 변환이 유효하지 않은 경우 <code>InvalidDataAccessApiUsageException</code>이 발생합니다. 다음 예제에는 두 개의 쿼리 메서드가 포함되어 있습니다. 하나는 <code>int</code>에 대한 것이고 다른 하나는 <code>String</code>에 대해 쿼리하는 것입니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">javax<span class="token punctuation">.</span>sql</span><span class="token punctuation">.</span><span class="token class-name">DataSource</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>jdbc<span class="token punctuation">.</span>core</span><span class="token punctuation">.</span><span class="token class-name">JdbcTemplate</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">RunAQuery</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">JdbcTemplate</span> jdbcTemplate<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setDataSource</span><span class="token punctuation">(</span><span class="token class-name">DataSource</span> dataSource<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>jdbcTemplate <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">JdbcTemplate</span><span class="token punctuation">(</span>dataSource<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">int</span> <span class="token function">getCount</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>jdbcTemplate<span class="token punctuation">.</span><span class="token function">queryForObject</span><span class="token punctuation">(</span><span class="token string">"select count(*) from mytable"</span><span class="token punctuation">,</span> <span class="token class-name">Integer</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>jdbcTemplate<span class="token punctuation">.</span><span class="token function">queryForObject</span><span class="token punctuation">(</span><span class="token string">"select name from mytable"</span><span class="token punctuation">,</span> <span class="token class-name">String</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>단일 결과 쿼리 메서드 외에도 여러 메서드가 쿼리가 반환한 각 행(row)에 대한 항목(entry)이 포함된 목록을 반환합니다. 가장 일반적인 방법은 <code>queryForList(..)</code>입니다. 이는 각 요소가 열(column) 이름을 키로 사용하여 각 열(column)에 대해 하나의 항목(entry)을 포함하는 <code>Map</code>인 <code>List</code>를 반환합니다. 모든 행의 목록을 검색하기 위해 이전 예제에 메서드를 추가하는 경우 다음과 같을 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">private</span> <span class="token class-name">JdbcTemplate</span> jdbcTemplate<span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setDataSource</span><span class="token punctuation">(</span><span class="token class-name">DataSource</span> dataSource<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">this</span><span class="token punctuation">.</span>jdbcTemplate <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">JdbcTemplate</span><span class="token punctuation">(</span>dataSource<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token keyword">public</span> <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Map</span><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">Object</span><span class="token punctuation">&gt;</span><span class="token punctuation">&gt;</span></span> <span class="token function">getList</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>jdbcTemplate<span class="token punctuation">.</span><span class="token function">queryForList</span><span class="token punctuation">(</span><span class="token string">"select * from mytable"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>반환된 목록은 다음과 유사합니다.</p>
<pre><code class="language-shell"><span class="token punctuation">[</span><span class="token punctuation">{</span>name<span class="token operator">=</span>Bob, <span class="token assign-left variable">id</span><span class="token operator">=</span><span class="token number">1</span><span class="token punctuation">}</span>, <span class="token punctuation">{</span>name<span class="token operator">=</span>Mary, <span class="token assign-left variable">id</span><span class="token operator">=</span><span class="token number">2</span><span class="token punctuation">}</span><span class="token punctuation">]</span></code></pre>
<h1 id="updating-the-database">Updating the Database</h1>
<p>다음 예에서는 특정 기본 키에 대한 열을 업데이트합니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">javax<span class="token punctuation">.</span>sql</span><span class="token punctuation">.</span><span class="token class-name">DataSource</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>jdbc<span class="token punctuation">.</span>core</span><span class="token punctuation">.</span><span class="token class-name">JdbcTemplate</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ExecuteAnUpdate</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">JdbcTemplate</span> jdbcTemplate<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setDataSource</span><span class="token punctuation">(</span><span class="token class-name">DataSource</span> dataSource<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>jdbcTemplate <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">JdbcTemplate</span><span class="token punctuation">(</span>dataSource<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setName</span><span class="token punctuation">(</span><span class="token keyword">int</span> id<span class="token punctuation">,</span> <span class="token class-name">String</span> name<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>jdbcTemplate<span class="token punctuation">.</span><span class="token function">update</span><span class="token punctuation">(</span><span class="token string">"update mytable set name = ? where id = ?"</span><span class="token punctuation">,</span> name<span class="token punctuation">,</span> id<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>앞의 예에서 SQL 문에는 행(row) 매개변수에 대한 자리 표시자가 있습니다. 매개변수 값을 가변 인수(varargs) 또는 객체 배열로 전달할 수 있습니다. 따라서 primitive 래퍼 클래스에서 primitive 형식을 명시적으로 래핑하거나 auto-boxing을 사용해야 합니다.</p>
<h1 id="retrieving-auto-generated-keys">Retrieving Auto-generated Keys</h1>
<p><code>update()</code> 편의 메서드는 데이터베이스에서 생성된 기본 키 검색을 지원합니다. 이 지원은 JDBC 3.0 표준의 일부입니다. 자세한 내용은 사양의 13.6장을 참조하세요. 이 메소드는 첫 번째 인수로 <code>PreparedStatementCreator</code>를 사용하며 이것이 필수(required) 삽입(insert) 문이 지정되는 방식입니다. 다른 인수는 업데이트에서 성공적으로 반환될 때 생성된 키를 포함하는 <code>KeyHolder</code>입니다. 적절한 <code>PreparedStatement</code>를 생성하는 표준적인 단일 방법은 없습니다(메서드 시그니처가 현재와 같은 방식인 이유를 설명함). 다음 예는 Oracle에서는 작동하지만 다른 플랫폼에서는 작동하지 않을 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">final</span> <span class="token class-name">String</span> INSERT_SQL <span class="token operator">=</span> <span class="token string">"insert into my_test (name) values(?)"</span><span class="token punctuation">;</span>
<span class="token keyword">final</span> <span class="token class-name">String</span> name <span class="token operator">=</span> <span class="token string">"Rob"</span><span class="token punctuation">;</span>

<span class="token class-name">KeyHolder</span> keyHolder <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">GeneratedKeyHolder</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
jdbcTemplate<span class="token punctuation">.</span><span class="token function">update</span><span class="token punctuation">(</span>connection <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
	<span class="token class-name">PreparedStatement</span> ps <span class="token operator">=</span> connection<span class="token punctuation">.</span><span class="token function">prepareStatement</span><span class="token punctuation">(</span>INSERT_SQL<span class="token punctuation">,</span> <span class="token keyword">new</span> <span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token punctuation">{</span> <span class="token string">"id"</span> <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	ps<span class="token punctuation">.</span><span class="token function">setString</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">,</span> name<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token keyword">return</span> ps<span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">,</span> keyHolder<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// keyHolder.getKey() now contains the generated key</span></code></pre>