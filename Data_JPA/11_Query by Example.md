<h1 id="introduction">Introduction</h1>
<p>이 장에서는 Query by Example를 소개하고 사용 방법을 설명합니다.</p>
<p>QBE(Query by example)는 간단한 인터페이스를 갖춘 사용자 친화적인 쿼리 기술입니다. 동적 쿼리 생성이 가능하며 필드 이름이 포함된 쿼리를 작성할 필요가 없습니다. 실제로 Query by example에서는 store별 쿼리 언어를 사용하여 쿼리를 작성할 필요가 전혀 없습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
이 장에서는 Query By Example의 핵심 개념을 설명합니다. 정보는 Spring Data Commons 모듈에서 가져옵니다. 데이터베이스에 따라 문자열 일치 지원이 제한될 수 있습니다.</p>
</blockquote>
<h1 id="usage">Usage</h1>
<p>QBE API는 네 부분으로 구성됩니다.</p>
<ul>
<li>
<p>Probe: 채워진 필드가 있는 도메인 객체의 실제 예입니다.</p>
</li>
<li>
<p><code>ExampleMatcher</code>: <code>ExampleMatcher</code>는 특정 필드를 일치시키는 방법에 대한 세부 정보를 전달합니다. 여러 예제에서 재사용할 수 있습니다.</p>
</li>
<li>
<p><code>Example</code> : <code>Example</code>는 프로브와 <code>ExampleMatcher</code>로 구성됩니다. 쿼리를 생성하는 데 사용됩니다.</p>
</li>
<li>
<p><code>FetchableFluentQuery</code>: <code>FetchableFluentQuery</code>는 <code>Example</code>에서 파생된 쿼리를 추가로 사용자 정의할 수 있는 Fetchable API를 제공합니다. Fluent API를 사용하면 쿼리에 대한 순서 예측 및 결과 처리를 지정할 수 있습니다.</p>
</li>
</ul>
<p>QBE는 여러 사용 사례에 매우 적합합니다.</p>
<ul>
<li>
<p>일련의 정적 또는 동적 제약 조건을 사용하여 데이터 store를 쿼리합니다.</p>
</li>
<li>
<p>기존 쿼리 중단에 대한 걱정 없이 도메인 개체를 자주 리팩터링합니다.</p>
</li>
<li>
<p>기본 데이터 store API와 독립적으로 작동합니다.</p>
</li>
</ul>
<p>사례별 쿼리에는 다음과 같은 몇 가지 제한 사항도 있습니다.</p>
<ul>
<li>
<p><code>firstname = ?0 or (fistname =?1 and lastname = ?2)</code>와 같은 중첩되거나 그룹화된 속성 제약 조건은 지원되지 않습니다.</p>
</li>
<li>
<p>문자열 일치에 대한 store별 지원. 데이터베이스에 따라 문자열 일치는 문자열에 대한 시작/포함/끝/정규식을 지원할 수 있습니다.</p>
</li>
<li>
<p>다른 property type과 정확히 일치합니다.</p>
</li>
</ul>
<p>사례별 쿼리를 시작하기 전에 도메인 개체가 있어야 합니다. 시작하려면 다음 예와 같이 repository에 대한 인터페이스를 생성하십시오.</p>
<p>Sample Person object</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Person</span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@Id</span>
  <span class="token keyword">private</span> <span class="token class-name">String</span> id<span class="token punctuation">;</span>
  <span class="token keyword">private</span> <span class="token class-name">String</span> firstname<span class="token punctuation">;</span>
  <span class="token keyword">private</span> <span class="token class-name">String</span> lastname<span class="token punctuation">;</span>
  <span class="token keyword">private</span> <span class="token class-name">Address</span> address<span class="token punctuation">;</span>

  <span class="token comment">// … getters and setters omitted</span>
<span class="token punctuation">}</span></code></pre>
<p>앞의 예에서는 간단한 도메인 개체를 보여줍니다. 이를 사용하여 <code>Example</code>를 만들 수 있습니다. 기본적으로 <code>null</code> 값이 있는 필드는 무시되고 문자열은 store별 기본값을 사용하여 일치됩니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
Query by Example criteria 에 속성을 포함하는 것은 Null 허용 여부를 기반으로 합니다. 기본 유형(<code>int</code>, <code>double</code>, …)을 사용하는 속성은 <a href="https://docs.spring.io/spring-data/jpa/reference/repositories/query-by-example.html#query-by-example.matchers"><code>ExampleMatcher</code>가 속성 경로를 무시</a>하지 않는 한 항상 포함됩니다.</p>
</blockquote>
<p>Examples는 <code>of</code> 팩토리 메서드를 사용하거나 <a href="https://docs.spring.io/spring-data/jpa/reference/repositories/query-by-example.html#query-by-example.matchers"><code>ExampleMatcher</code></a>를 사용하여 빌드할 수 있습니다. <code>Example</code>는 변경할 수 없습니다(immutable). 다음 목록은 간단한 예를 보여줍니다.</p>
<p>Example 1. Simple Example</p>
<pre><code class="language-java"><span class="token class-name">Person</span> person <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Person</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
person<span class="token punctuation">.</span><span class="token function">setFirstname</span><span class="token punctuation">(</span><span class="token string">"Dave"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">Example</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> example <span class="token operator">=</span> <span class="token class-name">Example</span><span class="token punctuation">.</span><span class="token function">of</span><span class="token punctuation">(</span>person<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>(1) 도메인 개체의 새 인스턴스를 만듭니다.<br>
(2) 쿼리할 속성을 설정합니다.<br>
(3) Example를 생성합니다.</p>
<p>리포지토리를 사용하여 예제 쿼리를 실행할 수 있습니다. 이렇게 하려면 저장소 인터페이스가 <code>QueryByExampleExecutor&lt;T&gt;</code>를 확장하도록 하세요. 다음 목록은 <code>QueryByExampleExecutor</code> 인터페이스에서 발췌한 내용을 보여줍니다.</p>
<p>The QueryByExampleExecutor</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">QueryByExampleExecutor</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">S</span> <span class="token keyword">extends</span> <span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token class-name">S</span> <span class="token function">findOne</span><span class="token punctuation">(</span><span class="token class-name">Example</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">S</span><span class="token punctuation">&gt;</span></span> example<span class="token punctuation">)</span><span class="token punctuation">;</span>

  <span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">S</span> <span class="token keyword">extends</span> <span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token class-name">Iterable</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">S</span><span class="token punctuation">&gt;</span></span> <span class="token function">findAll</span><span class="token punctuation">(</span><span class="token class-name">Example</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">S</span><span class="token punctuation">&gt;</span></span> example<span class="token punctuation">)</span><span class="token punctuation">;</span>

  <span class="token comment">// … more functionality omitted.</span>
<span class="token punctuation">}</span></code></pre>
<h1 id="example-matchers">Example Matchers</h1>
<p>Examples는 기본 설정에만 국한되지 않습니다. 다음 예에 표시된 대로 <code>ExampleMatcher</code>를 사용하여 문자열 일치, null 처리 및 속성별 설정에 대한 고유한 기본값을 지정할 수 있습니다.</p>
<p>Example 2. Example matcher with customized matching</p>
<pre><code class="language-java"><span class="token class-name">Person</span> person <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Person</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// (1)</span>
person<span class="token punctuation">.</span><span class="token function">setFirstname</span><span class="token punctuation">(</span><span class="token string">"Dave"</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// (2)</span>

<span class="token class-name">ExampleMatcher</span> matcher <span class="token operator">=</span> <span class="token class-name">ExampleMatcher</span><span class="token punctuation">.</span><span class="token function">matching</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token comment">// (3)</span>
  <span class="token punctuation">.</span><span class="token function">withIgnorePaths</span><span class="token punctuation">(</span><span class="token string">"lastname"</span><span class="token punctuation">)</span> <span class="token comment">// (4)</span>
  <span class="token punctuation">.</span><span class="token function">withIncludeNullValues</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token comment">// (5)</span>
  <span class="token punctuation">.</span><span class="token function">withStringMatcher</span><span class="token punctuation">(</span><span class="token class-name">StringMatcher</span><span class="token punctuation">.</span>ENDING<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// (6)</span>

<span class="token class-name">Example</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> example <span class="token operator">=</span> <span class="token class-name">Example</span><span class="token punctuation">.</span><span class="token function">of</span><span class="token punctuation">(</span>person<span class="token punctuation">,</span> matcher<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// (7)</span></code></pre>
<p>(1) 도메인 개체의 새 인스턴스를 만듭니다.<br>
(2) 속성을 설정합니다.<br>
(3) 모든 값이 일치할 것으로 예상하는 <code>ExampleMatcher</code>를 만듭니다. 추가 구성 없이도 이 단계에서 사용할 수 있습니다.<br>
(4) <code>lastname</code> 속성 경로를 무시하도록 새 <code>ExampleMatcher</code>를 생성합니다.<br>
(5) <code>lastname</code> 속성 경로를 무시하고 null 값을 포함하도록 새 <code>ExampleMatcher</code>를 생성합니다.<br>
(6) <code>lastname</code> 속성 경로를 무시하고, null 값을 포함하고, 접미사 문자열 일치를 수행하도록 새 <code>ExampleMatcher</code>를 생성합니다.<br>
(7) 도메인 객체와 구성된 <code>ExampleMatcher</code>를 기반으로 새 <code>Example</code>를 생성합니다.</p>
<p>기본적으로 <code>ExampleMatcher</code>는 프로브에 설정된 모든 값이 일치할 것으로 예상합니다. 암시적으로 정의된 predicate 중 하나와 일치하는 결과를 얻으려면 <code>ExampleMatcher.matchingAny()</code>를 사용하세요.</p>
<p>개별 속성(예: "firstname" 및 "lastname" 또는 중첩 속성의 경우 "address.city")에 대한 동작을 지정할 수 있습니다. 다음 예와 같이 일치 옵션과 대소문자 구분을 사용하여 조정할 수 있습니다.</p>
<p>Configuring matcher options</p>
<pre><code class="language-java"><span class="token class-name">ExampleMatcher</span> matcher <span class="token operator">=</span> <span class="token class-name">ExampleMatcher</span><span class="token punctuation">.</span><span class="token function">matching</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">withMatcher</span><span class="token punctuation">(</span><span class="token string">"firstname"</span><span class="token punctuation">,</span> <span class="token function">endsWith</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">withMatcher</span><span class="token punctuation">(</span><span class="token string">"lastname"</span><span class="token punctuation">,</span> <span class="token function">startsWith</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">ignoreCase</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>matcher 옵션을 구성하는 또 다른 방법은 람다(Java 8에 도입됨)를 사용하는 것입니다. 이 접근 방식은 implementor에게 matcher를 수정하도록 요청하는 콜백을 생성합니다. 구성 옵션이 matcher 인스턴스 내에 보관되므로 matcher를 반환할 필요가 없습니다. 다음 예에서는 람다를 사용하는 매처를 보여줍니다.</p>
<p>Configuring matcher options with lambdas</p>
<pre><code class="language-java"><span class="token class-name">ExampleMatcher</span> matcher <span class="token operator">=</span> <span class="token class-name">ExampleMatcher</span><span class="token punctuation">.</span><span class="token function">matching</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">withMatcher</span><span class="token punctuation">(</span><span class="token string">"firstname"</span><span class="token punctuation">,</span> match <span class="token operator">-&gt;</span> match<span class="token punctuation">.</span><span class="token function">endsWith</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">withMatcher</span><span class="token punctuation">(</span><span class="token string">"firstname"</span><span class="token punctuation">,</span> match <span class="token operator">-&gt;</span> match<span class="token punctuation">.</span><span class="token function">startsWith</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p><code>Example</code>에서 생성된 쿼리는 구성의 merged view를 사용합니다. 기본 matching 설정은 <code>ExampleMatcher</code> 수준에서 설정할 수 있으며, 개별 설정은 특정 속성 경로에 적용될 수 있습니다. <code>ExampleMatcher</code>에 설정된 설정은 명시적으로 정의되지 않는 한 속성 경로 설정에 의해 상속됩니다. 속성 패치의 설정은 기본 설정보다 우선 순위가 높습니다. 다음 표에서는 다양한 <code>ExampleMatcher</code> 설정의 범위를 설명합니다.</p>
<p>Table 1. Scope of ExampleMatcher settings</p>
<table class="tableblock frame-all grid-all stretch">
<caption class="title">표 1. <code>ExampleMatcher</code> 설정 범위</caption>




<thead>
<tr>
<th class="tableblock halign-left valign-top">설정</th>
<th class="tableblock halign-left valign-top">범위</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">Null 처리</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ExampleMatcher</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">문자열 매칭</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ExampleMatcher</code> 및 속성 경로</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">속성 무시</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">속성 경로</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">대소문자 구분</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ExampleMatcher</code> 및 속성 경로</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">값 변환</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">속성 경로</p></td>
</tr>
</tbody>
</table>
<h1 id="fluent-api">Fluent API</h1>
<p><code>QueryByExampleExecutor</code>는 지금까지 언급하지 않은 메서드를 하나 더 제공합니다. <code>&lt;S extends T, R&gt; R findBy(Example&lt;S&gt; example, Function&lt;FluentQuery.FetchableFluentQuery&lt;S&gt;, R&gt; queryFunction)</code>. 다른 방법과 마찬가지로 <code>Example</code>에서 파생된 쿼리를 실행합니다. 그러나 두 번째 인수를 사용하면 동적으로 제어할 수 없는 실행 측면을 제어할 수 있습니다. 두 번째 인수에서 <code>FetchableFluentQuery</code>의 다양한 메서드를 호출하면 됩니다. <code>sortBy</code>를 사용하면 결과의 순서를 지정할 수 있습니다. <code>as</code>를 사용하면 결과를 변환하려는 유형을 지정할 수 있습니다. <code>project</code>는 쿼리된 속성을 제한합니다. <code>first</code>, <code>firstValue</code>, <code>one</code>, <code>oneValue</code>, <code>all</code>, <code>page</code>, <code>stream</code>, <code>count</code> 및 <code>presents</code>는 얻을 수 있는 결과의 종류와 예상보다 많은 결과를 사용할 수 있을 때 쿼리가 작동하는 방식을 정의합니다.</p>
<p>fluent API를 사용하여 lastname순으로 정렬된 잠재적으로 많은 결과 중 마지막 결과를 얻으세요.</p>
<pre><code class="language-java"><span class="token class-name">Optional</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> match <span class="token operator">=</span> repository<span class="token punctuation">.</span><span class="token function">findBy</span><span class="token punctuation">(</span>example<span class="token punctuation">,</span>
    q <span class="token operator">-&gt;</span> q
        <span class="token punctuation">.</span><span class="token function">sortBy</span><span class="token punctuation">(</span><span class="token class-name">Sort</span><span class="token punctuation">.</span><span class="token function">by</span><span class="token punctuation">(</span><span class="token string">"lastname"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">descending</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
        <span class="token punctuation">.</span><span class="token function">first</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>Spring Data JPA에서는 다음 예제와 같이 리포지토리와 함께 QBE를 사용할 수 있습니다.</p>
<p>Example 3. Query by Example using a Repository</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">PersonRepository</span> <span class="token keyword">extends</span> <span class="token class-name">JpaRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">,</span> <span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span> … <span class="token punctuation">}</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">PersonService</span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@Autowired</span> <span class="token class-name">PersonRepository</span> personRepository<span class="token punctuation">;</span>

  <span class="token keyword">public</span> <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> <span class="token function">findPeople</span><span class="token punctuation">(</span><span class="token class-name">Person</span> probe<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> personRepository<span class="token punctuation">.</span><span class="token function">findAll</span><span class="token punctuation">(</span><span class="token class-name">Example</span><span class="token punctuation">.</span><span class="token function">of</span><span class="token punctuation">(</span>probe<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
현재 <code>SingularAttribute</code> 속성만 속성 일치에 사용할 수 있습니다.</p>
</blockquote>
<p>속성 지정자는 속성 이름(예: <code>firstname</code>, <code>lastname</code>)을 허용합니다. 속성을 점(<code>address.city</code>)으로 연결하여 탐색할 수 있습니다. 일치하는 옵션과 대소문자 구분을 사용하여 조정할 수도 있습니다.</p>
<p>다음 표에서는 사용할 수 있는 다양한 <code>StringMatcher</code> 옵션과 이를 <code>firstname</code>이라는 필드에 사용한 결과를 보여줍니다.</p>
<p>Table 2. StringMatcher options</p>
<table class="tableblock frame-all grid-all stretch">
<caption class="title">표 2. <code>StringMatcher</code> 옵션</caption>




<thead>
<tr>
<th class="tableblock halign-left valign-top">매칭</th>
<th class="tableblock halign-left valign-top">논리적 결과</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>DEFAULT</code> (대소문자 구분)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>firstname = ?0</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>DEFAULT</code> (대소문자 무시)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>LOWER(firstname) = LOWER(?0)</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>EXACT</code> (대소문자 구분)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>firstname = ?0</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>EXACT</code> (대소문자 무시)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>LOWER(firstname) = LOWER(?0)</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>STARTING</code> (대소문자 구분)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>firstname like ?0 + '%'</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>STARTING</code> (대소문자 무시)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>LOWER(firstname) like LOWER(?0) + '%'</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ENDING</code> (대소문자 구분)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>firstname like '%' + ?0</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ENDING</code> (대소문자 무시)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>LOWER(firstname) like '%' + LOWER(?0)</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>CONTAINING</code> (대소문자 구분)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>firstname like '%' + ?0 + '%'</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>CONTAINING</code> (대소문자 무시)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>LOWER(firstname) like '%' + LOWER(?0) + '%'</code></p></td>
</tr>
</tbody>
</table>
<blockquote>
<p><strong>[Note]</strong><br>
정규식 matching은 JPA에서 지원되지 않습니다.</p>
</blockquote>