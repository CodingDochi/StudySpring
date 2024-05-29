<p>이 섹션에서는 Spring Data JPA를 사용하여 쿼리를 생성하는 다양한 방법을 설명합니다</p>
<h1 id="query-lookup-strategies">Query Lookup Strategies</h1>
<p>JPA 모듈은 쿼리를 문자열로 수동으로 정의하거나 메서드 이름에서 파생되도록 지원합니다.</p>
<p>조건자 <code>IsStartingWith</code>, <code>StartingWith</code>, <code>StartsWith</code>, <code>IsEndingWith</code>, <code>EndingWith</code>, <code>EndsWith</code>, <code>IsNotContaining</code>, <code>NotContaining</code>, <code>NotContains</code>, <code>IsContaining</code>, <code>Containing</code>, <code>Contains</code>가 포함된 파생 쿼리는 이러한 쿼리에 대한 해당 인수가 삭제(sanitize)됩니다. 즉, 인수에 실제로 <code>LIKE</code>에서 와일드카드로 인식되는 문자가 포함되어 있으면 이러한 문자는 이스케이프 처리되어 리터럴로만 일치합니다. 사용되는 이스케이프 문자는 <code>@EnableJpaRepositories</code> annotation의 <code>escapeCharacter</code>를 설정하여 구성할 수 있습니다. <a href="https://docs.spring.io/spring-data/jpa/reference/jpa/query-methods.html#jpa.query.spel-expressions">SpEL 표현식 사용</a>과 비교해 보세요.</p>
<h2 id="declared-queries">Declared Queries</h2>
<p>메소드 이름에서 파생된 쿼리를 얻는 것은 매우 편리하지만 메소드 이름 구문 분석기가 사용하려는 키워드를 지원하지 않거나 메소드 이름이 불필요하게 보기 흉해지는 상황에 직면할 수 있습니다. 따라서 명명 규칙을 통해 JPA 명명된 쿼리를 사용하거나(자세한 내용은 <a href="https://docs.spring.io/spring-data/jpa/reference/jpa/query-methods.html#jpa.query-methods.named-queries">JPA 명명된 쿼리 사용</a> 참조) 쿼리 메서드에 <code>@Query</code>로 annotation을 달 수 있습니다(자세한 내용은 <a href="https://docs.spring.io/spring-data/jpa/reference/jpa/query-methods.html#jpa.query-methods.at-query"><code>@Query</code> 사용</a> 참조).</p>
<h1 id="query-creation">Query Creation</h1>
<p>일반적으로 JPA의 쿼리 생성 메커니즘은 <a href="https://docs.spring.io/spring-data/commons/reference/repositories/query-methods.html">쿼리 메서드</a>에 설명된 대로 작동합니다. 다음 예에서는 JPA 쿼리 메서드가 무엇으로 변환되는지 보여줍니다.</p>
<p>Example 1. Query creation from method names</p>
<blockquote>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">UserRepository</span> <span class="token keyword">extends</span> <span class="token class-name">Repository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>
  <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span>  <span class="token function">findByEmailAddressAndLastname</span><span class="token punctuation">(</span><span class="token class-name">String</span>  emailAddress<span class="token punctuation">,</span> <span class="token class-name">String</span> lastname<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>여기에서 JPA 기준 API를 사용하여 쿼리를 생성하지만 기본적으로 이는 다음 쿼리로 변환됩니다. <code>select u from User where u.emailAddress = ?1 and u.lastname = ?2</code>. Spring Data JPA는 <a href="https://docs.spring.io/spring-data/jpa/reference/repositories/query-methods-details.html#repositories.query-methods.query-property-expressions">속성 표현식</a>에 설명된 대로 속성 검사를 수행하고 중첩된 속성을 탐색합니다.</p>
</blockquote>
<p>다음 표에서는 JPA에 지원되는 키워드와 해당 키워드가 포함된 메서드의 변환 내용을 설명합니다.</p>
<p>Table 1. Supported keywords inside method names</p>
<table class="tableblock frame-all grid-all fit-content">
<caption class="title">표 1. 메서드 이름 내에서 지원되는 키워드</caption>





<thead>
<tr>
<th class="tableblock halign-left valign-top">키워드</th>
<th class="tableblock halign-left valign-top">예시</th>
<th class="tableblock halign-left valign-top">JPQL 조각</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>Distinct</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findDistinctByLastnameAndFirstname</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>select distinct …​ where x.lastname = ?1 and x.firstname = ?2</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>And</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findByLastnameAndFirstname</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>… where x.lastname = ?1 and x.firstname = ?2</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>Or</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findByLastnameOrFirstname</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>… where x.lastname = ?1 or x.firstname = ?2</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>Is</code>, <code>Equals</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findByFirstname</code>,<code>findByFirstnameIs</code>,<code>findByFirstnameEquals</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>… where x.firstname = ?1</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>Between</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findByStartDateBetween</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>… where x.startDate between ?1 and ?2</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>LessThan</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findByAgeLessThan</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>… where x.age &lt; ?1</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>LessThanEqual</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findByAgeLessThanEqual</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>… where x.age &lt;= ?1</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>GreaterThan</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findByAgeGreaterThan</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>… where x.age &gt; ?1</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>GreaterThanEqual</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findByAgeGreaterThanEqual</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>… where x.age &gt;= ?1</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>After</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findByStartDateAfter</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>… where x.startDate &gt; ?1</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>Before</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findByStartDateBefore</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>… where x.startDate &lt; ?1</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>IsNull</code>, <code>Null</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findByAge(Is)Null</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>… where x.age is null</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>IsNotNull</code>, <code>NotNull</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findByAge(Is)NotNull</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>… where x.age not null</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>Like</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findByFirstnameLike</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>… where x.firstname like ?1</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>NotLike</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findByFirstnameNotLike</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>… where x.firstname not like ?1</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>StartingWith</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findByFirstnameStartingWith</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>… where x.firstname like ?1</code> (매개변수가 <code>%</code>로 끝남)</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>EndingWith</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findByFirstnameEndingWith</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>… where x.firstname like ?1</code> (매개변수가 <code>%</code>로 시작함)</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>Containing</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findByFirstnameContaining</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>… where x.firstname like ?1</code> (매개변수가 <code>%</code>로 감싸짐)</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>OrderBy</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findByAgeOrderByLastnameDesc</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>… where x.age = ?1 order by x.lastname desc</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>Not</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findByLastnameNot</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>… where x.lastname &lt;&gt; ?1</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>In</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findByAgeIn(Collection&lt;Age&gt; ages)</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>… where x.age in ?1</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>NotIn</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findByAgeNotIn(Collection&lt;Age&gt; ages)</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>… where x.age not in ?1</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>True</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findByActiveTrue()</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>… where x.active = true</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>False</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findByActiveFalse()</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>… where x.active = false</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>IgnoreCase</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findByFirstnameIgnoreCase</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>… where UPPER(x.firstname) = UPPER(?1)</code></p></td>
</tr>
</tbody>
</table>
<blockquote>
<p><strong>[Note]</strong><br>
<code>In</code> 및 <code>NotIn</code>은 또한 <code>Collection</code>의 하위 클래스와 배열 또는 가변 인수를 매개변수로 사용합니다. 동일한 논리 연산자의 다른 구문 버전에 대해서는 리포지토리 쿼리 키워드를 확인하세요.</p>
</blockquote>
<blockquote>
<p><strong>[Warning]</strong><br>
<code>DISTINCT</code>는 까다로울 수 있으며 항상 예상한 결과를 생성하지 못할 수도 있습니다. 예를 들어 <code>select distinct u from User u</code>하면 <code>select distinct u.lastname from User u</code>하는 것과 완전히 다른 결과가 생성됩니다. 첫 번째 경우에는 <code>User.id</code>를 포함하므로 아무것도 중복되지 않으므로 전체 테이블을 얻게 되며 이는 <code>User</code> 개체로 구성됩니다.</p>
<p>그러나 후자의 쿼리는 초점을 <code>User.lastname</code>으로 좁히고 해당 테이블의 고유한 성을 모두 찾습니다. 이는 또한 <code>List&lt;User&gt;</code> 결과 집합 대신 <code>List&lt;String&gt;</code> 결과 집합을 생성합니다.</p>
<p><code>countDistinctByLastname(String lastname)</code>도 예상치 못한 결과를 생성할 수 있습니다. Spring Data JPA는 <code>select count(distinct u.id) from User u where u.lastname = ?1</code>를 파생합니다. 다시 말하지만, <code>u.id</code>는 중복 항목에 도달하지 않으므로 이 쿼리는 바인딩 성을 가진 모든 사용자를 계산합니다. <code>countByLastname(String lastname)</code>과 동일합니다!</p>
<p>어쨌든 이 쿼리의 요점은 무엇입니까? 특정 성을 가진 사람의 수를 찾으려면? 해당 구속력 있는 성을 가진 고유한 사람의 수를 찾으려면? 고유한 성의 수를 찾으려면? (마지막 쿼리는 완전히 다른 쿼리입니다!) <code>distinct</code>를 사용하려면 쿼리를 직접 작성하고 원하는 정보를 가장 잘 캡처하기 위해 <code>@Query</code>를 사용해야 하는 경우도 있습니다. 결과 집합을 캡처하기 위해 프로젝션이 필요할 수도 있기 때문입니다.</p>
</blockquote>
<h2 id="annotation-based-configuration">Annotation-based Configuration</h2>
<p>annotation 기반 구성은 다른 구성 파일을 편집할 필요가 없어 유지 관리 노력이 줄어든다는 장점이 있습니다. 모든 새로운 쿼리 선언에 대해 도메인 클래스를 다시 컴파일해야 하므로 이러한 이점에 대한 비용을 지불합니다.</p>
<p>Example 2. Annotation-based named query configuration</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token annotation punctuation">@NamedQuery</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"User.findByEmailAddress"</span><span class="token punctuation">,</span>
  query <span class="token operator">=</span> <span class="token string">"select u from User u where u.emailAddress = ?1"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">User</span> <span class="token punctuation">{</span>

<span class="token punctuation">}</span></code></pre>
<h1 id="using-jpa-named-queries">Using JPA Named Queries</h1>
<blockquote>
<p><strong>[Note]</strong><br>
예제에서는 <code>&lt;named-query /&gt;</code> 요소와 <code>@NamedQuery</code> annotation을 사용합니다. 이러한 구성 요소에 대한 쿼리는 JPA 쿼리 언어로 정의되어야 합니다. 물론 <code>&lt;named-native-query /&gt;</code>나 <code>@NamedNativeQuery</code>도 사용할 수 있습니다. 이러한 요소를 사용하면 데이터베이스 플랫폼 독립성을 상실하여 기본 SQL에서 쿼리를 정의할 수 있습니다.</p>
</blockquote>
<h2 id="xml-named-query-definition">XML Named Query Definition</h2>
<p>XML 구성을 사용하려면 클래스 경로의 <code>META-INF</code> 폴더에 있는 <code>orm.xml</code> JPA 구성 파일에 필요한 <code>&lt;named-query /&gt;</code> 요소를 추가하세요. 일부 정의된 명명 규칙을 사용하면 명명된 쿼리의 자동 호출이 활성화됩니다. 자세한 내용은 아래를 참조하세요.</p>
<p>예 3. XML 명명된 쿼리 구성</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>named-query</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>User.findByLastname<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>query</span><span class="token punctuation">&gt;</span></span>select u from User u where u.lastname = ?1<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>query</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>named-query</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>쿼리에는 런타임 시 쿼리를 해결하는 데 사용되는 특수 이름이 있습니다.</p>
<h2 id="declaring-interfaces">Declaring Interfaces</h2>
<p>이러한 명명된 쿼리를 허용하려면 다음과 같이 <code>UserRepositoryWithRewriter</code>를 지정합니다.</p>
<p>예 4. UserRepository의 쿼리 메서드 선언</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">UserRepository</span> <span class="token keyword">extends</span> <span class="token class-name">JpaRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastname</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">)</span><span class="token punctuation">;</span>

  <span class="token class-name">User</span> <span class="token function">findByEmailAddress</span><span class="token punctuation">(</span><span class="token class-name">String</span> emailAddress<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>Spring Data는 구성된 도메인 클래스의 간단한 이름으로 시작하고 점으로 구분된 메서드 이름이 뒤따르는 명명된 쿼리에 대한 이러한 메서드에 대한 호출을 해결하려고 시도합니다. 따라서 앞의 예에서는 메서드 이름에서 쿼리를 만드는 대신 이전에 정의한 명명된 쿼리를 사용합니다.</p>
<h1 id="using-query">Using <code>@Query</code></h1>
<p>명명된 쿼리를 사용하여 엔터티에 대한 쿼리를 선언하는 것은 유효한 접근 방식이며 적은 수의 쿼리에 대해 잘 작동합니다. 쿼리 자체는 이를 실행하는 Java 메서드에 연결되어 있으므로 실제로 도메인 클래스에 annotation을 추가하는 대신 Spring Data JPA <code>@Query</code> annotation을 사용하여 쿼리를 직접 바인딩할 수 있습니다. 이렇게 하면 지속성 관련 정보로부터 도메인 클래스가 해방되고 쿼리가 저장소 인터페이스에 같은 위치에 배치됩니다.</p>
<p>쿼리 메서드에 annotation이 달린 쿼리는 <code>@NamedQuery</code>를 사용하여 정의된 쿼리나 <code>orm.xml</code>에 선언된 명명된 쿼리보다 우선합니다.</p>
<p>다음 예에서는 <code>@Query</code> annotation을 사용하여 생성된 쿼리를 보여줍니다.</p>
<p>Example 5. Declare query at the query method using @Query</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">UserRepository</span> <span class="token keyword">extends</span> <span class="token class-name">JpaRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@Query</span><span class="token punctuation">(</span><span class="token string">"select u from User u where u.emailAddress = ?1"</span><span class="token punctuation">)</span>
  <span class="token class-name">User</span> <span class="token function">findByEmailAddress</span><span class="token punctuation">(</span><span class="token class-name">String</span> emailAddress<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="applying-a-queryrewriter">Applying a QueryRewriter</h2>
<p>때로는 적용하려는 기능의 수에 관계없이 쿼리가 <code>EntityManager</code>로 전송되기 전에 Spring Data JPA가 쿼리에 원하는 모든 항목을 적용하도록 하는 것이 불가능해 보일 수 있습니다.</p>
<p>쿼리가 <code>EntityManager</code>로 전송되기 직전에 쿼리를 직접 확인하고 "rewrite" 수 있습니다. 즉, 마지막 순간에 어떤 변경이라도 할 수 있습니다.</p>
<p>예 6. @Query를 사용하여 QueryRewriter 선언</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">MyRepository</span> <span class="token keyword">extends</span> <span class="token class-name">JpaRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

		<span class="token annotation punctuation">@Query</span><span class="token punctuation">(</span>value <span class="token operator">=</span> <span class="token string">"select original_user_alias.* from SD_USER original_user_alias"</span><span class="token punctuation">,</span>
                nativeQuery <span class="token operator">=</span> <span class="token boolean">true</span><span class="token punctuation">,</span>
				queryRewriter <span class="token operator">=</span> <span class="token class-name">MyQueryRewriter</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
		<span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByNativeQuery</span><span class="token punctuation">(</span><span class="token class-name">String</span> param<span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token annotation punctuation">@Query</span><span class="token punctuation">(</span>value <span class="token operator">=</span> <span class="token string">"select original_user_alias from User original_user_alias"</span><span class="token punctuation">,</span>
                queryRewriter <span class="token operator">=</span> <span class="token class-name">MyQueryRewriter</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
		<span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByNonNativeQuery</span><span class="token punctuation">(</span><span class="token class-name">String</span> param<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이 예에서는 동일한 QueryRewriter를 활용하는 기본(순수 SQL) 재작성과 JPQL 쿼리를 모두 보여줍니다. 이 시나리오에서 Spring Data JPA는 해당 유형의 애플리케이션 컨텍스트에 등록된 Bean을 찾습니다.</p>
<p>다음과 같이 쿼리 재작성을 작성할 수 있습니다.</p>
<p>Example 7. Example QueryRewriter</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyQueryRewriter</span> <span class="token keyword">implements</span> <span class="token class-name">QueryRewriter</span> <span class="token punctuation">{</span>

     <span class="token annotation punctuation">@Override</span>
     <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">rewrite</span><span class="token punctuation">(</span><span class="token class-name">String</span> query<span class="token punctuation">,</span> <span class="token class-name">Sort</span> sort<span class="token punctuation">)</span> <span class="token punctuation">{</span>
         <span class="token keyword">return</span> query<span class="token punctuation">.</span><span class="token function">replaceAll</span><span class="token punctuation">(</span><span class="token string">"original_user_alias"</span><span class="token punctuation">,</span> <span class="token string">"rewritten_user_alias"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
     <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>Spring Framework의 <code>@Component</code> 기반 annotation 중 하나를 적용하거나 <code>@Configuration</code> 클래스 내 <code>@Bean</code> 메서드의 일부로 포함하여 <code>QueryRewriter</code>가 애플리케이션 컨텍스트에 등록되었는지 확인해야 합니다.</p>
<p>또 다른 옵션은 저장소 자체가 인터페이스를 구현하도록 하는 것입니다.</p>
<p>Example 8. Repository that provides the QueryRewriter</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">MyRepository</span> <span class="token keyword">extends</span> <span class="token class-name">JpaRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">,</span> <span class="token class-name">QueryRewriter</span> <span class="token punctuation">{</span>

		<span class="token annotation punctuation">@Query</span><span class="token punctuation">(</span>value <span class="token operator">=</span> <span class="token string">"select original_user_alias.* from SD_USER original_user_alias"</span><span class="token punctuation">,</span>
                nativeQuery <span class="token operator">=</span> <span class="token boolean">true</span><span class="token punctuation">,</span>
				queryRewriter <span class="token operator">=</span> <span class="token class-name">MyRepository</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
		<span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByNativeQuery</span><span class="token punctuation">(</span><span class="token class-name">String</span> param<span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token annotation punctuation">@Query</span><span class="token punctuation">(</span>value <span class="token operator">=</span> <span class="token string">"select original_user_alias from User original_user_alias"</span><span class="token punctuation">,</span>
                queryRewriter <span class="token operator">=</span> <span class="token class-name">MyRepository</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
		<span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByNonNativeQuery</span><span class="token punctuation">(</span><span class="token class-name">String</span> param<span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token annotation punctuation">@Override</span>
		<span class="token keyword">default</span> <span class="token class-name">String</span> <span class="token function">rewrite</span><span class="token punctuation">(</span><span class="token class-name">String</span> query<span class="token punctuation">,</span> <span class="token class-name">Sort</span> sort<span class="token punctuation">)</span> <span class="token punctuation">{</span>
			<span class="token keyword">return</span> query<span class="token punctuation">.</span><span class="token function">replaceAll</span><span class="token punctuation">(</span><span class="token string">"original_user_alias"</span><span class="token punctuation">,</span> <span class="token string">"rewritten_user_alias"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>QueryRewriter</code>로 수행하는 작업에 따라 각각 애플리케이션 컨텍스트에 등록된 둘 이상을 갖는 것이 좋습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
CDI 기반 환경에서 Spring Data JPA는 <code>BeanManager</code>에서 <code>QueryRewriter</code> 구현 인스턴스를 검색합니다.</p>
</blockquote>
<h2 id="using-advanced-like-expressions">Using Advanced <code>LIKE</code> Expressions</h2>
<p><code>@Query</code>를 사용하여 생성된 수동으로 정의된 쿼리에 대한 쿼리 실행 메커니즘을 사용하면 다음 예제와 같이 쿼리 정의 내에서 고급 <code>LIKE</code> 표현식을 정의할 수 있습니다.</p>
<p>Example 9. Advanced like expressions in @Query</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">UserRepository</span> <span class="token keyword">extends</span> <span class="token class-name">JpaRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@Query</span><span class="token punctuation">(</span><span class="token string">"select u from User u where u.firstname like %?1"</span><span class="token punctuation">)</span>
  <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByFirstnameEndsWith</span><span class="token punctuation">(</span><span class="token class-name">String</span> firstname<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>앞의 예에서는 <code>LIKE</code> 구분 기호 문자(<code>%</code>)가 인식되고 쿼리가 유효한 JPQL 쿼리로 변환됩니다(<code>%</code> 제거). 쿼리를 실행하면 메서드 호출에 전달된 매개 변수가 이전에 인식된 <code>LIKE</code> 패턴으로 보강됩니다.</p>
<h2 id="native-queries">Native Queries</h2>
<p><code>@Query</code> 어노테이션을 사용하면 다음 예와 같이 <code>nativeQuery</code> 플래그를 true로 설정하여 기본 쿼리를 실행할 수 있습니다.</p>
<p>예 10. @Query를 사용하여 쿼리 메서드에서 native query 선언</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">UserRepository</span> <span class="token keyword">extends</span> <span class="token class-name">JpaRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@Query</span><span class="token punctuation">(</span>value <span class="token operator">=</span> <span class="token string">"SELECT * FROM USERS WHERE EMAIL_ADDRESS = ?1"</span><span class="token punctuation">,</span> nativeQuery <span class="token operator">=</span> <span class="token boolean">true</span><span class="token punctuation">)</span>
  <span class="token class-name">User</span> <span class="token function">findByEmailAddress</span><span class="token punctuation">(</span><span class="token class-name">String</span> emailAddress<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
Spring Data JPA는 현재 네이티브 쿼리에 대한 동적 정렬을 지원하지 않습니다. 왜냐하면 선언된 실제 쿼리를 조작해야 하기 때문입니다. 이는 네이티브 SQL에 대해 안정적으로 수행할 수 없습니다. 그러나 다음 예와 같이 개수 쿼리를 직접 지정하여 페이지 매김에 기본 쿼리를 사용할 수 있습니다.</p>
</blockquote>
<p>예 11. @Query를 사용하여 쿼리 메서드에서 페이지 매김을 위한 기본 개수 쿼리를 선언합니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">UserRepository</span> <span class="token keyword">extends</span> <span class="token class-name">JpaRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@Query</span><span class="token punctuation">(</span>value <span class="token operator">=</span> <span class="token string">"SELECT * FROM USERS WHERE LASTNAME = ?1"</span><span class="token punctuation">,</span>
    countQuery <span class="token operator">=</span> <span class="token string">"SELECT count(*) FROM USERS WHERE LASTNAME = ?1"</span><span class="token punctuation">,</span>
    nativeQuery <span class="token operator">=</span> <span class="token boolean">true</span><span class="token punctuation">)</span>
  <span class="token class-name">Page</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastname</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">,</span> <span class="token class-name">Pageable</span> pageable<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>쿼리 복사본에 <code>.count</code> 접미사를 추가하면 명명된 기본 쿼리에서도 유사한 접근 방식이 작동합니다. 하지만 개수 쿼리에 대한 결과 집합 매핑을 등록해야 할 수도 있습니다.</p>
<h1 id="using-sort">Using Sort</h1>
<p><code>PageRequest</code>를 제공하거나 <code>Sort</code>를 직접 사용하여 정렬을 수행할 수 있습니다. <code>Sort</code>의 <code>Order</code> 인스턴스 내에서 실제로 사용되는 속성은 도메인 모델과 일치해야 합니다. 즉, 쿼리 내에서 사용되는 속성이나 별칭으로 확인되어야 합니다. JPQL은 이를 상태 필드 경로 표현식으로 정의합니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
참조할 수 없는 경로 표현식을 사용하면 <code>Exception</code>가 발생합니다.</p>
</blockquote>
<p>그러나 <a href="https://docs.spring.io/spring-data/jpa/reference/jpa/query-methods.html#jpa.query-methods.at-query"><code>@Query</code></a>와 함께 <code>Sort</code>를 사용하면 <code>ORDER BY</code> 절 내에 함수가 포함된 경로 확인되지 않은 <code>Order</code> 인스턴스를 몰래 가져올 수 있습니다. 이는 주어진 쿼리 문자열에 <code>Order</code>가 추가되기 때문에 가능합니다. 기본적으로 Spring Data JPA는 함수 호출이 포함된 <code>Order</code> 인스턴스를 거부하지만 <code>JpaSort.unsafe</code>를 사용하여 잠재적으로 안전하지 않은 순서를 추가할 수 있습니다.</p>
<p>다음 예제에서는 <code>JpaSort</code>의 안전하지 않은 옵션을 포함하여 <code>Sort</code> 및 <code>JpaSort</code>를 사용합니다.</p>
<p>Example 12. Using Sort and JpaSort</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">UserRepository</span> <span class="token keyword">extends</span> <span class="token class-name">JpaRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@Query</span><span class="token punctuation">(</span><span class="token string">"select u from User u where u.lastname like ?1%"</span><span class="token punctuation">)</span>
  <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByAndSort</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">,</span> <span class="token class-name">Sort</span> sort<span class="token punctuation">)</span><span class="token punctuation">;</span>

  <span class="token annotation punctuation">@Query</span><span class="token punctuation">(</span><span class="token string">"select u.id, LENGTH(u.firstname) as fn_len from User u where u.lastname like ?1%"</span><span class="token punctuation">)</span>
  <span class="token class-name">List</span><span class="token operator">&lt;</span><span class="token class-name">Object</span><span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token operator">&gt;</span> <span class="token function">findByAsArrayAndSort</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">,</span> <span class="token class-name">Sort</span> sort<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

repo<span class="token punctuation">.</span><span class="token function">findByAndSort</span><span class="token punctuation">(</span><span class="token string">"lannister"</span><span class="token punctuation">,</span> <span class="token class-name">Sort</span><span class="token punctuation">.</span><span class="token function">by</span><span class="token punctuation">(</span><span class="token string">"firstname"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// (1)</span>
repo<span class="token punctuation">.</span><span class="token function">findByAndSort</span><span class="token punctuation">(</span><span class="token string">"stark"</span><span class="token punctuation">,</span> <span class="token class-name">Sort</span><span class="token punctuation">.</span><span class="token function">by</span><span class="token punctuation">(</span><span class="token string">"LENGTH(firstname)"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// (2)</span>
repo<span class="token punctuation">.</span><span class="token function">findByAndSort</span><span class="token punctuation">(</span><span class="token string">"targaryen"</span><span class="token punctuation">,</span> <span class="token class-name">JpaSort</span><span class="token punctuation">.</span><span class="token function">unsafe</span><span class="token punctuation">(</span><span class="token string">"LENGTH(firstname)"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// (3)</span>
repo<span class="token punctuation">.</span><span class="token function">findByAsArrayAndSort</span><span class="token punctuation">(</span><span class="token string">"bolton"</span><span class="token punctuation">,</span> <span class="token class-name">Sort</span><span class="token punctuation">.</span><span class="token function">by</span><span class="token punctuation">(</span><span class="token string">"fn_len"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// (4)</span></code></pre>
<p>(1) 도메인 모델의 속성을 가리키는 유효한 낷 표현식입니다.<br>
(2) 함수 호출을 포함하는 잘못된 Sort입니다. 예외가 발생합니다.<br>
(3) 명시적으로 안전하지 않은 Order가 포함된 유효한 Sort입니다.<br>
(4) 별칭이 지정된 함수를 가리키는 유효한 Sort 표현식입니다.</p>
<h1 id="scrolling-large-query-results">Scrolling Large Query Results</h1>
<p>대규모 데이터 세트로 작업할 때 <a href="https://docs.spring.io/spring-data/jpa/reference/jpa/query-methods.html#repositories.scrolling">스크롤</a>은 모든 결과를 메모리에 로드하지 않고도 해당 결과를 효율적으로 처리하는 데 도움이 될 수 있습니다.</p>
<p>대규모 쿼리 결과를 사용할 수 있는 여러 가지 옵션이 있습니다.</p>
<ol>
<li>
<p><a href="https://docs.spring.io/spring-data/jpa/reference/repositories/query-methods-details.html#repositories.paging-and-sorting">페이징</a>. 이전 장에서 <code>Pageable</code> 및 <code>PageRequest</code>에 대해 배웠습니다.</p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-data/jpa/reference/jpa/query-methods.html#repositories.scrolling.offset">오프셋 기반 스크롤</a>. 이는 총 결과 개수가 필요하지 않기 때문에 페이징보다 가벼운 변형입니다.</p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-data/jpa/reference/jpa/query-methods.html#repositories.scrolling.keyset">키셋-베이스 스크롤</a>. 이 방법은 <a href="https://use-the-index-luke.com/no-offset">데이터베이스 인덱스를 활용하여 오프셋 기반 결과 검색의 단점을 방지</a>합니다.</p>
</li>
</ol>
<p><a href="https://docs.spring.io/spring-data/jpa/reference/jpa/query-methods.html#repositories.scrolling.guidance">특정 배치에 가장 적합한 method</a>에 대해 자세히 알아보세요.</p>
<p>쿼리 메서드, <a href="https://docs.spring.io/spring-data/jpa/reference/repositories/query-by-example.html">Query-by-Example</a> 및 <a href="https://docs.spring.io/spring-data/jpa/reference/repositories/core-extensions.html#core.extensions.querydsl">Querydsl</a>과 함께 Scroll API를 사용할 수 있습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
문자열 기반 쿼리 방법을 사용한 스크롤은 아직 지원되지 않습니다. 저장된 <code>@Procedure</code> 쿼리 메서드를 사용한 스크롤도 지원되지 않습니다.</p>
</blockquote>
<h1 id="using-named-parameters">Using Named Parameters</h1>
<p>기본적으로 Spring Data JPA는 이전 예제에서 설명한 대로 위치 기반 매개변수 바인딩을 사용합니다. 이로 인해 매개변수 위치와 관련하여 리팩토링할 때 쿼리 메서드에 약간의 오류가 발생하기 쉽습니다. 이 문제를 해결하려면 다음 예제와 같이 <code>@Param</code> annotation을 사용하여 메서드 매개 변수에 구체적인 이름을 지정하고 쿼리에 이름을 바인딩할 수 있습니다.</p>
<p>Example 13. Using named parameters</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">UserRepository</span> <span class="token keyword">extends</span> <span class="token class-name">JpaRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@Query</span><span class="token punctuation">(</span><span class="token string">"select u from User u where u.firstname = :firstname or u.lastname = :lastname"</span><span class="token punctuation">)</span>
  <span class="token class-name">User</span> <span class="token function">findByLastnameOrFirstname</span><span class="token punctuation">(</span><span class="token annotation punctuation">@Param</span><span class="token punctuation">(</span><span class="token string">"lastname"</span><span class="token punctuation">)</span> <span class="token class-name">String</span> lastname<span class="token punctuation">,</span>
                                 <span class="token annotation punctuation">@Param</span><span class="token punctuation">(</span><span class="token string">"firstname"</span><span class="token punctuation">)</span> <span class="token class-name">String</span> firstname<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
메소드 매개변수는 정의된 쿼리의 순서에 따라 전환됩니다.</p>
</blockquote>
<blockquote>
<p><strong>[Note]</strong><br>
버전 4부터 Spring은 <code>-parameters</code> 컴파일러 플래그를 기반으로 Java 8의 매개변수 이름 검색을 완벽하게 지원합니다. 디버그 정보 대신 빌드에서 이 플래그를 사용하면 명명된 매개변수에 대한 <code>@Param</code> annotation을 생략할 수 있습니다.</p>
</blockquote>
<h1 id="using-spel-expressions">Using SpEL Expressions</h1>
<p>Spring Data JPA 릴리스 1.4부터 <code>@Query</code>로 정의된 수동 정의 쿼리에서 제한된 SpEL 템플릿 표현식의 사용을 지원합니다. 쿼리가 실행되면 이러한 표현식은 미리 정의된 변수 집합에 대해 평가됩니다. Spring Data JPA는 <code>entityName</code>이라는 변수를 지원합니다. 사용법은 <code>select x from #{#entityName} x</code>하는 것입니다. 지정된 저장소와 연결된 도메인 유형의 <code>entityName</code>을 삽입합니다. <code>entityName</code>은 다음과 같이 확인됩니다. 도메인 type이 <code>@Entity</code> annotation에 이름 속성을 설정한 경우 해당 속성이 사용됩니다. 그렇지 않으면 도메인 유형의 단순 클래스 이름이 사용됩니다.</p>
<p>다음 예에서는 쿼리 메서드와 수동으로 정의된 쿼리를 사용하여 저장소 인터페이스를 정의하려는 쿼리 문자열의 <code>#{#entityName}</code> 표현식에 대한 한 가지 사용 사례를 보여줍니다.</p>
<p>Example 14. Using SpEL expressions in repository query methods - entityName</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">User</span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@Id</span>
  <span class="token annotation punctuation">@GeneratedValue</span>
  <span class="token class-name">Long</span> id<span class="token punctuation">;</span>

  <span class="token class-name">String</span> lastname<span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">UserRepository</span> <span class="token keyword">extends</span> <span class="token class-name">JpaRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span><span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@Query</span><span class="token punctuation">(</span><span class="token string">"select u from #{#entityName} u where u.lastname = ?1"</span><span class="token punctuation">)</span>
  <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastname</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p><code>@Query</code> annotation의 쿼리 문자열에 실제 엔터티 이름을 명시하지 않으려면 <code>#{#entityName}</code> 변수를 사용할 수 있습니다.</p>
<blockquote>
<p><strong>[Name]</strong><br>
<code>@Entity</code> annotation을 사용하여 <code>entityName</code>을 사용자 정의할 수 있습니다. SpEL 표현식에는 <code>orm.xml</code>의 사용자 정의가 지원되지 않습니다.</p>
</blockquote>
<p>물론 쿼리 선언에서 <code>User</code>를 직접 사용할 수도 있지만 그렇게 하려면 쿼리도 변경해야 합니다. <code>#entityName</code>에 대한 참조는 향후 <code>User</code> 클래스를 다른 엔터티 이름으로 다시 매핑할 가능성을 선택합니다(예: <code>@Entity(name = "MyUser")</code> 사용).</p>
<p>쿼리 문자열의 <code>#{#entityName}</code> 표현식에 대한 또 다른 사용 사례는 구체적인 도메인 type에 대한 generic 저장소 인터페이스를 사용하여 일반 저장소 인터페이스를 정의하려는 경우입니다. 구체적인 인터페이스에서 사용자 정의 쿼리 메서드 정의를 반복하지 않으려면 다음 예와 같이 generic 저장소 인터페이스의 <code>@Query</code> annotation 쿼리 문자열에 엔터티 이름 표현식을 사용할 수 있습니다.</p>
<p>Example 15. Using SpEL expressions in repository query methods - entityName with inheritance</p>
<pre><code class="language-java"><span class="token annotation punctuation">@MappedSuperclass</span>
<span class="token keyword">public</span> <span class="token keyword">abstract</span> <span class="token keyword">class</span> <span class="token class-name">AbstractMappedType</span> <span class="token punctuation">{</span>
  …
  <span class="token class-name">String</span> attribute<span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@Entity</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ConcreteType</span> <span class="token keyword">extends</span> <span class="token class-name">AbstractMappedType</span> <span class="token punctuation">{</span> … <span class="token punctuation">}</span>

<span class="token annotation punctuation">@NoRepositoryBean</span>
<span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">MappedTypeRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span> <span class="token keyword">extends</span> <span class="token class-name">AbstractMappedType</span><span class="token punctuation">&gt;</span></span>
  <span class="token keyword">extends</span> <span class="token class-name">Repository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@Query</span><span class="token punctuation">(</span><span class="token string">"select t from #{#entityName} t where t.attribute = ?1"</span><span class="token punctuation">)</span>
  <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token function">findAllByAttribute</span><span class="token punctuation">(</span><span class="token class-name">String</span> attribute<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">ConcreteRepository</span>
  <span class="token keyword">extends</span> <span class="token class-name">MappedTypeRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">ConcreteType</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span> … <span class="token punctuation">}</span></code></pre>
<p>앞의 예에서 <code>MappedTypeRepository</code> 인터페이스는 <code>AbstractMappedType</code>을 확장하는 몇 가지 도메인 유형에 대한 공통 상위 인터페이스입니다. 또한 generic 저장소 인터페이스의 인스턴스에 사용할 수 있는 일반 <code>findAllByAttribute(…)</code> 메서드를 정의합니다. 이제 <code>ConcreteRepository</code>에서 <code>findByAllAttribute(…)</code>를 호출하면 쿼리는 <code>select t from ConcreteType t where t.attribute = ?1</code> 됩니다.</p>
<p>인수를 조작하는 SpEL 표현식을 사용하여 메서드 인수를 조작할 수도 있습니다. 이러한 SpEL 표현식에서는 엔터티 이름을 사용할 수 없지만 인수는 사용할 수 있습니다. 다음 예에 설명된 것처럼 이름이나 색인으로 액세스할 수 있습니다.</p>
<p>Example 16. Using SpEL expressions in repository query methods - accessing arguments.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Query</span><span class="token punctuation">(</span><span class="token string">"select u from User u where u.firstname = ?1 and u.firstname=?#{[0]} and u.emailAddress = ?#{principal.emailAddress}"</span><span class="token punctuation">)</span>
<span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByFirstnameAndCurrentUserWithCustomQuery</span><span class="token punctuation">(</span><span class="token class-name">String</span> firstname<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p><code>like-</code> 조건의 경우 문자열 값 매개변수의 시작 또는 끝에 <code>%</code>를 추가하려는 경우가 많습니다. 이는 바인드 매개변수 표시자 또는 SpEL 표현식에 <code>%</code>를 추가하거나 접두사를 추가하여 수행할 수 있습니다. 다음 예제에서는 이를 보여줍니다.</p>
<p>Example 17. Using SpEL expressions in repository query methods - wildcard shortcut.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Query</span><span class="token punctuation">(</span><span class="token string">"select u from User u where u.lastname like %:#{[0]}% and u.lastname like %:lastname%"</span><span class="token punctuation">)</span>
<span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastnameWithSpelExpression</span><span class="token punctuation">(</span><span class="token annotation punctuation">@Param</span><span class="token punctuation">(</span><span class="token string">"lastname"</span><span class="token punctuation">)</span> <span class="token class-name">String</span> lastname<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>안전하지 않은 소스에서 오는 값과 함께 <code>like-</code> 조건을 사용하는 경우 값은 와일드카드를 포함할 수 없도록 삭제되어야 하며 이를 통해 공격자가 가능한 것보다 더 많은 데이터를 선택할 수 있습니다. 이를 위해 SpEL 컨텍스트에서 <code>escape(String)</code> 메소드를 사용할 수 있습니다. 첫 번째 인수에 있는 <code>_</code> 및 <code>%</code>의 모든 인스턴스 앞에는 두 번째 인수의 단일 문자가 붙습니다. JPQL 및 표준 SQL에서 사용할 수 있는 <code>like</code> 표현식의 <code>escape</code> 절과 결합하여 바인드 매개변수를 쉽게 정리할 수 있습니다.</p>
<p>Example 18. Using SpEL expressions in repository query methods - sanitizing input values.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Query</span><span class="token punctuation">(</span><span class="token string">"select u from User u where u.firstname like %?#{escape([0])}% escape ?#{escapeCharacter()}"</span><span class="token punctuation">)</span>
<span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findContainingEscaped</span><span class="token punctuation">(</span><span class="token class-name">String</span> namePart<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>저장소 인터페이스 <code>findContainingEscaped("Peter_")</code>에서 이 메소드 선언이 주어지면 <code>Peter_Parker</code>는 찾지만 <code>Peter Parker</code>는 찾지 않습니다. 사용되는 이스케이프 문자는 <code>@EnableJpaRepositories</code> 주석의 <code>escapeCharacter</code>를 설정하여 구성할 수 있습니다. SpEL 컨텍스트에서 사용할 수 있는 <code>escape(String)</code> 메소드는 SQL 및 JPQL 표준 와일드카드인 <code>_</code> 및 <code>%</code>만 이스케이프합니다. 기본 데이터베이스 또는 JPA 구현이 추가 와일드카드를 지원하는 경우 이러한 와일드카드는 이스케이프되지 않습니다.</p>
<h1 id="other-methods">Other Methods</h1>
<p>Spring Data JPA는 쿼리를 작성하는 다양한 방법을 제공합니다. 그러나 때로는 귀하의 쿼리가 제공된 기술에 비해 너무 복잡할 수도 있습니다. 그러한 상황에서는 다음을 고려하십시오.</p>
<ul>
<li>
<p>아직 작성하지 않았다면 <code>@Query</code>를 사용하여 직접 쿼리를 작성하세요.</p>
</li>
<li>
<p>이것이 귀하의 요구 사항에 맞지 않으면 맞춤 구현을 구현하는 것이 좋습니다. 이를 통해 구현을 완전히 사용자에게 맡기면서 저장소에 메소드를 등록할 수 있습니다. 이는 다음과 같은 기능을 제공합니다.</p>
<ul>
<li>
<p><code>EntityManager</code>와 직접 대화합니다(순수 HQL/JPQL/EQL/네이티브 SQL 작성 또는 Criteria API 사용).</p>
</li>
<li>
<p>Spring Framework의 <code>JdbcTemplate</code>(네이티브 SQL) 활용</p>
</li>
<li>
<p>다른 타사 데이터베이스 도구 키트를 사용하세요.</p>
</li>
</ul>
</li>
<li>
<p>또 다른 옵션은 쿼리를 데이터베이스 내부에 넣은 다음 Spring Data JPA의 <code>@StoredProcedure</code> annotation을 사용하거나 데이터베이스 함수인 경우 <code>@Query</code> annotation을 사용하고 <code>CALL</code>로 호출하는 것입니다.</p>
</li>
</ul>
<p>이러한 전술은 Spring Data JPA가 리소스 관리를 제공하도록 하면서 쿼리를 최대한 제어해야 할 때 가장 효과적일 수 있습니다.</p>
<h1 id="modifying-queries">Modifying Queries</h1>
<p>이전 섹션에서는 모두 특정 엔터티 또는 엔터티 컬렉션에 액세스하기 위한 쿼리를 선언하는 방법을 설명했습니다. <a href="https://docs.spring.io/spring-data/commons/reference/repositories/custom-implementations.html">Spring 데이터 저장소에 대한 사용자 정의 구현</a>에 설명된 사용자 정의 메소드 기능을 사용하여 사용자 정의 수정 동작을 추가할 수 있습니다. 이 접근 방식은 포괄적인 사용자 지정 기능에 적합하므로 다음 예와 같이 쿼리 메서드에 <code>@Modifying</code> annotation을 추가하여 매개 변수 바인딩만 필요한 쿼리를 수정할 수 있습니다.</p>
<p>Example 19. Declaring manipulating queries</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Modifying</span>
<span class="token annotation punctuation">@Query</span><span class="token punctuation">(</span><span class="token string">"update User u set u.firstname = ?1 where u.lastname = ?2"</span><span class="token punctuation">)</span>
<span class="token keyword">int</span> <span class="token function">setFixedFirstnameFor</span><span class="token punctuation">(</span><span class="token class-name">String</span> firstname<span class="token punctuation">,</span> <span class="token class-name">String</span> lastname<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>이렇게 하면 쿼리를 선택하는 대신 업데이트 쿼리로 메서드에 주석이 달린 쿼리가 트리거됩니다. 수정 쿼리 실행 후 <code>EntityManager</code>에 오래된 엔터티가 포함될 수 있으므로 이를 자동으로 지우지 않습니다(자세한 내용은 <code>EntityManager.clear()</code>의 JavaDoc 참조). 이는 <code>EntityManager</code>에 아직 보류 중인 플러시되지 않은 모든 변경 사항을 효과적으로 삭제하기 때문입니다. <code>EntityManager</code>를 자동으로 지우려면 <code>@Modifying</code> annotation의<code>clearAutomatically</code> 속성을 <code>true</code>로 설정하면 됩니다.</p>
<p><code>@Modifying</code> annotation은 <code>@Query</code> annotation과 결합된 경우에만 관련됩니다. 파생된 쿼리 메서드 또는 사용자 지정 메서드에는 이 annotation이 필요하지 않습니다.</p>
<h2 id="derived-delete-queries">Derived Delete Queries</h2>
<p>Spring Data JPA는 다음 예제와 같이 JPQL 쿼리를 명시적으로 선언하지 않아도 되도록 파생된 삭제 쿼리도 지원합니다.</p>
<p>Example 20. Using a derived delete query</p>
<pre><code class="language-java"><span class="token keyword">interface</span> <span class="token class-name">UserRepository</span> <span class="token keyword">extends</span> <span class="token class-name">Repository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token keyword">void</span> <span class="token function">deleteByRoleId</span><span class="token punctuation">(</span><span class="token keyword">long</span> roleId<span class="token punctuation">)</span><span class="token punctuation">;</span>

  <span class="token annotation punctuation">@Modifying</span>
  <span class="token annotation punctuation">@Query</span><span class="token punctuation">(</span><span class="token string">"delete from User u where u.role.id = ?1"</span><span class="token punctuation">)</span>
  <span class="token keyword">void</span> <span class="token function">deleteInBulkByRoleId</span><span class="token punctuation">(</span><span class="token keyword">long</span> roleId<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p><code>deleteByRoleId(…)</code> 메서드는 기본적으로 <code>deleteInBulkByRoleId(…)</code>와 동일한 결과를 생성하는 것처럼 보이지만 실행 방식 측면에서 두 메서드 선언 사이에는 중요한 차이점이 있습니다. 이름에서 알 수 있듯이 후자의 방법은 데이터베이스에 대해 단일 JPQL 쿼리(annotation에 정의된 쿼리)를 실행합니다. 이는 현재 로드된 <code>User</code> 인스턴스라도 호출된 수명 주기 콜백을 볼 수 없음을 의미합니다.</p>
<p>수명 주기 쿼리가 실제로 호출되는지 확인하기 위해 <code>deleteByRoleId(…)</code>를 호출하면 쿼리를 실행한 다음 반환된 인스턴스를 하나씩 삭제하므로 지속성 공급자가 실제로 해당 엔터티에 대해 <code>@PreRemove</code> 콜백을 호출할 수 있습니다.</p>
<p>실제로 파생된 삭제 쿼리는 쿼리를 실행한 다음 결과에 대해 <code>CrudRepository.delete(Iterable&lt;User&gt; users)</code>를 호출하고 동작을 <code>CrudRepository</code>의 다른 <code>delete(…)</code> 메서드 구현과 동기화하는 바로 가기입니다.</p>
<h1 id="applying-query-hints">Applying Query Hints</h1>
<p>저장소 인터페이스에 선언된 쿼리에 JPA 쿼리 힌트를 적용하려면 <code>@QueryHints</code> annotation을 사용할 수 있습니다. 다음 예제와 같이 페이지 매김을 적용할 때 트리거되는 추가 카운트 쿼리에 적용되는 힌트를 잠재적으로 비활성화하려면 JPA <code>@QueryHint</code> annotation 배열과 부울 플래그를 사용합니다.</p>
<p>Example 21. Using QueryHints with a repository method</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">UserRepository</span> <span class="token keyword">extends</span> <span class="token class-name">Repository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@QueryHints</span><span class="token punctuation">(</span>value <span class="token operator">=</span> <span class="token punctuation">{</span> <span class="token annotation punctuation">@QueryHint</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"name"</span><span class="token punctuation">,</span> value <span class="token operator">=</span> <span class="token string">"value"</span><span class="token punctuation">)</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
              forCounting <span class="token operator">=</span> <span class="token boolean">false</span><span class="token punctuation">)</span>
  <span class="token class-name">Page</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastname</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">,</span> <span class="token class-name">Pageable</span> pageable<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이전 선언에서는 실제 쿼리에 대해 구성된 <code>@QueryHint</code>를 적용하지만 총 페이지 수를 계산하기 위해 트리거된 개수 쿼리에는 적용을 생략합니다.</p>
<h2 id="adding-comments-to-queries">Adding Comments to Queries</h2>
<p>때로는 데이터베이스 성능을 기반으로 쿼리를 디버깅해야 하는 경우도 있습니다. 데이터베이스 관리자가 보여주는 쿼리는 <code>@Query</code>를 사용하여 작성한 쿼리와 매우 다르게 보일 수도 있고, 사용자 정의 파인더와 관련하여 Spring Data JPA가 생성했다고 가정하는 쿼리 또는 예제로 쿼리를 사용한 것과 전혀 다르게 보일 수도 있습니다.</p>
<p>이 프로세스를 더 쉽게 만들기 위해 <code>@Meta</code> annotation을 적용하여 쿼리든 다른 작업이든 거의 모든 JPA 작업에 사용자 정의 annotation을 삽입할 수 있습니다.</p>
<p>Example 22. Apply @Meta annotation to repository operations</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">RoleRepository</span> <span class="token keyword">extends</span> <span class="token class-name">JpaRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Role</span><span class="token punctuation">,</span> <span class="token class-name">Integer</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Meta</span><span class="token punctuation">(</span>comment <span class="token operator">=</span> <span class="token string">"find roles by name"</span><span class="token punctuation">)</span>
	<span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Role</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByName</span><span class="token punctuation">(</span><span class="token class-name">String</span> name<span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token annotation punctuation">@Meta</span><span class="token punctuation">(</span>comment <span class="token operator">=</span> <span class="token string">"find roles using QBE"</span><span class="token punctuation">)</span>
	<span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">S</span> <span class="token keyword">extends</span> <span class="token class-name">Role</span><span class="token punctuation">&gt;</span></span> <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">S</span><span class="token punctuation">&gt;</span></span> <span class="token function">findAll</span><span class="token punctuation">(</span><span class="token class-name">Example</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">S</span><span class="token punctuation">&gt;</span></span> example<span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Meta</span><span class="token punctuation">(</span>comment <span class="token operator">=</span> <span class="token string">"count roles for a given name"</span><span class="token punctuation">)</span>
	<span class="token keyword">long</span> <span class="token function">countByName</span><span class="token punctuation">(</span><span class="token class-name">String</span> name<span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token annotation punctuation">@Meta</span><span class="token punctuation">(</span>comment <span class="token operator">=</span> <span class="token string">"exists based on QBE"</span><span class="token punctuation">)</span>
	<span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">S</span> <span class="token keyword">extends</span> <span class="token class-name">Role</span><span class="token punctuation">&gt;</span></span> <span class="token keyword">boolean</span> <span class="token function">exists</span><span class="token punctuation">(</span><span class="token class-name">Example</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">S</span><span class="token punctuation">&gt;</span></span> example<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이 샘플 저장소에는 <code>JpaRepository</code>에서 상속된 작업을 재정의할 뿐만 아니라 사용자 정의 파인더가 혼합되어 있습니다. 어느 쪽이든 <code>@Meta</code> annotation을 사용하면 쿼리가 데이터베이스로 전송되기 전에 쿼리에 삽입될 <code>comment</code>을 추가할 수 있습니다.</p>
<p>이 기능은 쿼리에만 국한되지 않는다는 점도 중요합니다. 이것은 <code>count</code>와 <code>exists</code> 작업까지 확장됩니다. 표시되지는 않지만 특정 <code>delete</code> 작업까지 확장됩니다.</p>
<blockquote>
<p><strong>[Important]</strong><br>
가능한 모든 곳에 이 기능을 적용하려고 시도했지만 기본 <code>EntityManager</code>의 일부 작업은 annotation을 지원하지 않습니다. 예를 들어, <code>entityManager.createQuery()</code>는 지원 annotaion으로 명확하게 문서화되어 있지만 <code>entityManager.find()</code> 작업은 그렇지 않습니다.</p>
</blockquote>
<p>JPQL 로깅이나 SQL 로깅은 JPA의 표준이 아니므로 아래 섹션에 표시된 것처럼 각 공급자에는 사용자 지정 구성이 필요합니다.</p>
<h3 id="activating-hibernate-comments">Activating Hibernate comments</h3>
<p>Hibernate에서 쿼리 comments을 활성화하려면 <code>hibernate.use_sql_comments</code>를 <code>true</code>로 설정해야 합니다. Java 기반 구성 설정을 사용하는 경우 다음과 같이 수행할 수 있습니다.</p>
<p>Example 23. Java-based JPA configuration</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Bean</span>
<span class="token keyword">public</span> <span class="token class-name">Properties</span> <span class="token function">jpaProperties</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>

	<span class="token class-name">Properties</span> properties <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Properties</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	properties<span class="token punctuation">.</span><span class="token function">setProperty</span><span class="token punctuation">(</span><span class="token string">"hibernate.use_sql_comments"</span><span class="token punctuation">,</span> <span class="token string">"true"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token keyword">return</span> properties<span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p><code>persistence.xml</code> 파일이 있는 경우 해당 파일에 적용할 수 있습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>persistence-unit</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>my-persistence-unit<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

   ...registered classes...

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>properties</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>hibernate.use_sql_comments<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>true<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>properties</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>persistence-unit</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>마지막으로 Spring Boot를 사용하는 경우 <code>application.properties</code> 파일 내에서 이를 설정할 수 있습니다.</p>
<p>Example 25. Spring Boot property-based configuration</p>
<pre><code class="language-properties">spring.jpa.properties.hibernate.use_sql_comments=true</code></pre>
<h3 id="activating-eclipselink-comments">Activating EclipseLink comments</h3>
<p>EclipseLink에서 쿼리 주석을 활성화하려면 <code>eclipselink.logging.level.sql</code>을 <code>FINE</code>으로 설정해야 합니다. Java 기반 구성 설정을 사용하는 경우 다음과 같이 수행할 수 있습니다.</p>
<p>Example 26. Java-based JPA configuration</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Bean</span>
<span class="token keyword">public</span> <span class="token class-name">Properties</span> <span class="token function">jpaProperties</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>

	<span class="token class-name">Properties</span> properties <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Properties</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	properties<span class="token punctuation">.</span><span class="token function">setProperty</span><span class="token punctuation">(</span><span class="token string">"eclipselink.logging.level.sql"</span><span class="token punctuation">,</span> <span class="token string">"FINE"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token keyword">return</span> properties<span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p><code>persistence.xml</code> 파일이 있는 경우 해당 파일에 적용할 수 있습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>persistence-unit</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>my-persistence-unit<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

   ...registered classes...

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>properties</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>eclipselink.logging.level.sql<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>FINE<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>properties</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>persistence-unit</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>마지막으로 Spring Boot를 사용하는 경우 application.properties 파일 내에서 이를 설정할 수 있습니다.</p>
<p>Example 28. Spring Boot property-based configuration</p>
<pre><code class="language-properties">spring.jpa.properties.eclipselink.logging.level.sql=FINE</code></pre>
<h1 id="configuring-fetch--and-loadgraphs">Configuring Fetch- and LoadGraphs</h1>
<p>JPA 2.1 사양에는 <code>@NamedEntityGraph</code> 정의를 참조할 수 있는 <code>@EntityGraph</code> annotation을 통해 지원하는 Fetch 및 LoadGraph 지정에 대한 지원이 도입되었습니다. 엔터티에서 해당 annotation을 사용하여 결과 쿼리의 가져오기 계획을 구성할 수 있습니다. 가져오기 type(<code>Fetch</code> 또는 <code>Locad</code>)은 <code>@EntityGraph</code> annotation의 유형 속성을 사용하여 구성할 수 있습니다. 자세한 내용은 JPA 2.1 Spec 3.7.4를 참조하세요.</p>
<p>다음 예에서는 엔터티에 명명된 엔터티 그래프를 정의하는 방법을 보여줍니다.</p>
<p>Example 29. Defining a named entity graph on an entity.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token annotation punctuation">@NamedEntityGraph</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"GroupInfo.detail"</span><span class="token punctuation">,</span>
  attributeNodes <span class="token operator">=</span> <span class="token annotation punctuation">@NamedAttributeNode</span><span class="token punctuation">(</span><span class="token string">"members"</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">GroupInfo</span> <span class="token punctuation">{</span>

  <span class="token comment">// default fetch mode is lazy.</span>
  <span class="token annotation punctuation">@ManyToMany</span>
  <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">GroupMember</span><span class="token punctuation">&gt;</span></span> members <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ArrayList</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">GroupMember</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

  …
<span class="token punctuation">}</span></code></pre>
<p>다음 예에서는 리포지토리 쿼리 메서드에서 명명된 엔터티 그래프를 참조하는 방법을 보여줍니다.</p>
<p>Example 30. Referencing a named entity graph definition on a repository query method.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">GroupRepository</span> <span class="token keyword">extends</span> <span class="token class-name">CrudRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">GroupInfo</span><span class="token punctuation">,</span> <span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@EntityGraph</span><span class="token punctuation">(</span>value <span class="token operator">=</span> <span class="token string">"GroupInfo.detail"</span><span class="token punctuation">,</span> type <span class="token operator">=</span> <span class="token class-name">EntityGraphType</span><span class="token punctuation">.</span>LOAD<span class="token punctuation">)</span>
  <span class="token class-name">GroupInfo</span> <span class="token function">getByGroupName</span><span class="token punctuation">(</span><span class="token class-name">String</span> name<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token punctuation">}</span></code></pre>
<p><code>@EntityGraph</code>를 사용하여 임시 엔터티 그래프를 정의하는 것도 가능합니다. 다음 예제와 같이 <code>@NamedEntityGraph</code>를 도메인 유형에 명시적으로 추가할 필요 없이 제공된 <code>attributePaths</code>가 해당 <code>EntityGraph</code>로 변환됩니다.</p>
<p>Example 31. Using AD-HOC entity graph definition on an repository query method.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">GroupRepository</span> <span class="token keyword">extends</span> <span class="token class-name">CrudRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">GroupInfo</span><span class="token punctuation">,</span> <span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@EntityGraph</span><span class="token punctuation">(</span>attributePaths <span class="token operator">=</span> <span class="token punctuation">{</span> <span class="token string">"members"</span> <span class="token punctuation">}</span><span class="token punctuation">)</span>
  <span class="token class-name">GroupInfo</span> <span class="token function">getByGroupName</span><span class="token punctuation">(</span><span class="token class-name">String</span> name<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token punctuation">}</span></code></pre>
<h1 id="scrolling">Scrolling</h1>
<p>스크롤은 더 큰 결과 세트 청크를 반복하는 보다 세분화된 접근 방식입니다. 스크롤링은 안정적인 정렬, 스크롤 유형(오프셋 또는 키셋 기반 스크롤링) 및 결과 제한으로 구성됩니다. 속성 이름을 사용하여 간단한 정렬 식을 정의하고 쿼리 파생을 통해 <a href="https://docs.spring.io/spring-data/jpa/reference/repositories/query-methods-details.html#repositories.limit-query-result"><code>Top</code> 또는 <code>First</code> 키워드</a>를 사용하여 정적 결과 제한을 정의할 수 있습니다. 표현식을 연결하여 여러 기준을 하나의 표현식으로 수집할 수 있습니다.</p>
<p>스크롤 쿼리는 애플리케이션이 전체 쿼리 결과를 사용할 때까지 다음 <code>Window&lt;T&gt;</code>를 얻기 위해 스크롤 위치를 다시 얻을 수 있는 <code>Window&lt;T&gt;</code>를 반환합니다. 다음 결과 배치를 획득하여 Java <code>Iterator&lt;List&lt;…&gt;&gt;</code>를 사용하는 것과 유사하게 쿼리 결과 스크롤을 사용하면 <code>Window.positionAt(…​)</code>를 통해 <code>ScrollPosition</code>에 액세스할 수 있습니다.</p>
<pre><code class="language-java"><span class="token class-name">Window</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> users <span class="token operator">=</span> repository<span class="token punctuation">.</span><span class="token function">findFirst10ByLastnameOrderByFirstname</span><span class="token punctuation">(</span><span class="token string">"Doe"</span><span class="token punctuation">,</span> <span class="token class-name">ScrollPosition</span><span class="token punctuation">.</span><span class="token function">offset</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token keyword">do</span> <span class="token punctuation">{</span>

  <span class="token keyword">for</span> <span class="token punctuation">(</span><span class="token class-name">User</span> u <span class="token operator">:</span> users<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token comment">// consume the user</span>
  <span class="token punctuation">}</span>

  <span class="token comment">// obtain the next Scroll</span>
  users <span class="token operator">=</span> repository<span class="token punctuation">.</span><span class="token function">findFirst10ByLastnameOrderByFirstname</span><span class="token punctuation">(</span><span class="token string">"Doe"</span><span class="token punctuation">,</span> users<span class="token punctuation">.</span><span class="token function">positionAt</span><span class="token punctuation">(</span>users<span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">-</span> <span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span> <span class="token keyword">while</span> <span class="token punctuation">(</span><span class="token operator">!</span>users<span class="token punctuation">.</span><span class="token function">isEmpty</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">&amp;&amp;</span> users<span class="token punctuation">.</span><span class="token function">hasNext</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p><code>WindowIterator</code>는 다음 <code>Window</code>가 있는지 확인하고 <code>ScrollPosition</code>을 적용할 필요를 없애 <code>Windows</code> 전체에서 스크롤을 단순화하는 유틸리티를 제공합니다.</p>
<pre><code class="language-java"><span class="token class-name">WindowIterator</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> users <span class="token operator">=</span> <span class="token class-name">WindowIterator</span><span class="token punctuation">.</span><span class="token function">of</span><span class="token punctuation">(</span>position <span class="token operator">-&gt;</span> repository<span class="token punctuation">.</span><span class="token function">findFirst10ByLastnameOrderByFirstname</span><span class="token punctuation">(</span><span class="token string">"Doe"</span><span class="token punctuation">,</span> position<span class="token punctuation">)</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">startingAt</span><span class="token punctuation">(</span><span class="token class-name">OffsetScrollPosition</span><span class="token punctuation">.</span><span class="token function">initial</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token keyword">while</span> <span class="token punctuation">(</span>users<span class="token punctuation">.</span><span class="token function">hasNext</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
  <span class="token class-name">User</span> u <span class="token operator">=</span> users<span class="token punctuation">.</span><span class="token function">next</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token comment">// consume the user</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="scrolling-using-offset">Scrolling using Offset</h2>
<p>오프셋 스크롤은 페이지 매김과 유사한 오프셋 카운터를 사용하여 여러 결과를 건너뛰고 데이터 소스가 지정된 오프셋에서 시작하는 결과만 반환하도록 합니다. 이 간단한 메커니즘은 큰 결과가 클라이언트 애플리케이션으로 전송되는 것을 방지합니다. 그러나 대부분의 데이터베이스에서는 서버가 결과를 반환하기 전에 전체 쿼리 결과를 구체화해야 합니다.</p>
<p>Example 32. Using OffsetScrollPosition with Repository Query Methods</p>
<pre><code class="language-java"><span class="token keyword">interface</span> <span class="token class-name">UserRepository</span> <span class="token keyword">extends</span> <span class="token class-name">Repository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token class-name">Window</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findFirst10ByLastnameOrderByFirstname</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">,</span> <span class="token class-name">OffsetScrollPosition</span> position<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token class-name">WindowIterator</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> users <span class="token operator">=</span> <span class="token class-name">WindowIterator</span><span class="token punctuation">.</span><span class="token function">of</span><span class="token punctuation">(</span>position <span class="token operator">-&gt;</span> repository<span class="token punctuation">.</span><span class="token function">findFirst10ByLastnameOrderByFirstname</span><span class="token punctuation">(</span><span class="token string">"Doe"</span><span class="token punctuation">,</span> position<span class="token punctuation">)</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">startingAt</span><span class="token punctuation">(</span><span class="token class-name">OffsetScrollPosition</span><span class="token punctuation">.</span><span class="token function">initial</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// (1)</span></code></pre>
<p>(1) 위치 0의 초기 오프셋부터 시작합니다.</p>
<h2 id="scrolling-using-keyset-filtering">Scrolling using Keyset-Filtering</h2>
<p>오프셋 기반을 사용하려면 대부분의 데이터베이스에서 서버가 결과를 반환하기 전에 전체 결과를 구체화해야 합니다. 따라서 클라이언트는 요청된 결과 중 일부만 볼 수 있지만 서버는 전체 결과를 빌드해야 하므로 추가 로드가 발생합니다.</p>
<p>키 집합 필터링 접근 방식은 개별 쿼리에 대한 계산 및 I/O 요구 사항을 줄이는 것을 목표로 데이터베이스에 내장된 기능을 활용하여 하위 집합을 검색합니다. 이 접근 방식은 키를 쿼리에 전달하여 스크롤을 재개하는 키 세트를 유지 관리하고 필터 기준을 효과적으로 수정합니다.</p>
<p>Keyset-Filtering의 핵심 아이디어는 안정적인 정렬 순서를 사용하여 결과 검색을 시작하는 것입니다. 다음 청크로 스크롤하려면 정렬된 결과 내에서 위치를 재구성하는 데 사용되는 <code>ScrollPosition</code>을 얻습니다. <code>ScrollPosition</code>은 현재 <code>Window</code> 내 마지막 엔터티의 키 세트를 캡처합니다. 쿼리를 실행하기 위해 재구성에서는 데이터베이스가 잠재적인 인덱스를 활용하여 쿼리를 실행할 수 있도록 모든 정렬 필드와 기본 키를 포함하도록 기준 절을 다시 작성합니다. 데이터베이스는 큰 결과를 완전히 구체화한 다음 특정 오프셋에 도달할 때까지 결과를 건너뛸 필요 없이 지정된 키 세트 위치에서 훨씬 작은 결과만 구성하면 됩니다.</p>
<blockquote>
<p><strong>[Warning]</strong><br>
키 세트 필터링에서는 키 세트 속성(정렬에 사용되는 속성)이 <code>null</code>을 허용하지 않아야 합니다. 이 제한은 비교 연산자의 저장소별 null 값 처리와 인덱싱된 소스에 대해 쿼리를 실행해야 하기 때문에 적용됩니다. Null 허용 속성에 대한 키 집합 필터링은 예상치 못한 결과를 초래합니다.</p>
</blockquote>
<p>Using KeysetScrollPosition with Repository Query Methods</p>
<pre><code class="language-java"><span class="token keyword">interface</span> <span class="token class-name">UserRepository</span> <span class="token keyword">extends</span> <span class="token class-name">Repository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token class-name">Window</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findFirst10ByLastnameOrderByFirstname</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">,</span> <span class="token class-name">KeysetScrollPosition</span> position<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token class-name">WindowIterator</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> users <span class="token operator">=</span> <span class="token class-name">WindowIterator</span><span class="token punctuation">.</span><span class="token function">of</span><span class="token punctuation">(</span>position <span class="token operator">-&gt;</span> repository<span class="token punctuation">.</span><span class="token function">findFirst10ByLastnameOrderByFirstname</span><span class="token punctuation">(</span><span class="token string">"Doe"</span><span class="token punctuation">,</span> position<span class="token punctuation">)</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">startingAt</span><span class="token punctuation">(</span><span class="token class-name">ScrollPosition</span><span class="token punctuation">.</span><span class="token function">keyset</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// (1) </span></code></pre>
<p>(1) 처음부터 시작하고 추가 필터링을 적용하지 마십시오.</p>
<p>키 집합 필터링은 데이터베이스에 정렬 필드와 일치하는 인덱스가 포함되어 있을 때 가장 잘 작동하므로 정적 정렬이 잘 작동합니다. Keyset-Filtering을 적용한 스크롤 쿼리는 정렬 순서에 사용된 속성이 쿼리에 의해 반환되어야 하며, 이러한 속성은 반환된 엔터티에 매핑되어야 합니다.</p>
<p>인터페이스 및 DTO 프로젝션을 사용할 수 있지만 키 세트 추출 실패를 방지하려면 정렬한 모든 속성을 포함해야 합니다.</p>
<p><code>Sort</code> 순서를 지정할 때 쿼리와 관련된 정렬 속성을 포함하면 충분합니다. 원하지 않는 경우 고유한 쿼리 결과를 보장할 필요가 없습니다. 키 세트 쿼리 메커니즘은 각 쿼리 결과가 고유하도록 기본 키(또는 나머지 복합 기본 키)를 포함하여 정렬 순서를 수정합니다.</p>