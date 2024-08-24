<p>repository 프록시에는 메소드 이름에서 store별 쿼리를 파생시키는 두 가지 방법이 있습니다.</p>
<ul>
<li>
<p>메소드 이름에서 직접 쿼리를 파생합니다.</p>
</li>
<li>
<p>수동으로 정의된 쿼리를 사용합니다.</p>
</li>
</ul>
<p>사용 가능한 옵션은 실제 store에 따라 다릅니다. 그러나 실제 쿼리가 무엇인지 결정하는 전략이 있어야 합니다. 다음 섹션에서는 사용 가능한 옵션에 대해 설명합니다.</p>
<h1 id="query-lookup-strategies">Query Lookup Strategies</h1>
<p>쿼리를 해결하기 위해 리포지토리 인프라에 다음 전략을 사용할 수 있습니다. XML 구성을 사용하면 <code>query-lookup-strategy</code> 속성(attribute)을 통해 네임스페이스에서 전략을 구성할 수 있습니다. Java 구성의 경우 <code>EnableJpaRepositories</code> annotation의 <code>queryLookupStrategy</code> 속성(attribute)을 사용할 수 있습니다. 특정 데이터스토어에서는 일부 전략이 지원되지 않을 수 있습니다.</p>
<ul>
<li>
<p><code>CREATE</code>는 쿼리 메서드 이름에서 store별 쿼리를 생성하려고 시도합니다. 일반적인 접근 방식은 메소드 이름에서 잘 알려진 접두사 세트를 제거하고 메소드의 나머지 부분을 구문 분석하는 것입니다. 쿼리 생성에 대한 자세한 내용은 "<a href="https://docs.spring.io/spring-data/jpa/reference/repositories/query-methods-details.html#repositories.query-methods.query-creation">쿼리 생성</a>"에서 확인할 수 있습니다.</p>
</li>
<li>
<p><code>USE_DECLARED_QUERY</code> 는 선언된 쿼리를 찾으려고 시도하고 쿼리를 찾을 수 없으면 예외를 발생시킵니다. 쿼리는 어딘가에 annotation으로 정의되거나 다른 방법으로 선언될 수 있습니다. 해당 store에 사용 가능한 옵션을 찾으려면 해당 store의 설명서를 참조하세요. repository 인프라가 부트스트랩 시 메서드에 대해 선언된 쿼리를 찾지 못하면 실패합니다.</p>
</li>
<li>
<p><code>CREATE_IF_NOT_FOUND</code>(기본값)는 <code>CREATE</code>와 <code>USE_DECLARED_QUERY</code>를 결합합니다. 선언된 쿼리를 먼저 조회하고, 선언된 쿼리가 없으면 사용자 지정 메서드 이름 기반 쿼리를 생성합니다. 이는 기본 조회 전략이므로 명시적으로 아무것도 구성하지 않은 경우에 사용됩니다. 메소드 이름으로 쿼리를 빠르게 정의할 수 있을 뿐만 아니라 필요에 따라 선언된 쿼리를 도입하여 이러한 쿼리를 사용자 정의할 수도 있습니다.</p>
</li>
</ul>
<h1 id="query-creation">Query Creation</h1>
<p>Spring Data 저장소 인프라에 내장된 쿼리 빌더 메커니즘은 저장소의 엔터티에 대한 제한 쿼리를 작성하는 데 유용합니다. 다음 예에서는 여러 쿼리를 만드는 방법을 보여줍니다.</p>
<p>메소드 이름에서 쿼리 생성</p>
<pre><code class="language-java"><span class="token keyword">interface</span> <span class="token class-name">PersonRepository</span> <span class="token keyword">extends</span> <span class="token class-name">Repository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByEmailAddressAndLastname</span><span class="token punctuation">(</span><span class="token class-name">EmailAddress</span> emailAddress<span class="token punctuation">,</span> <span class="token class-name">String</span> lastname<span class="token punctuation">)</span><span class="token punctuation">;</span>

  <span class="token comment">// Enables the distinct flag for the query</span>
  <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> <span class="token function">findDistinctPeopleByLastnameOrFirstname</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">,</span> <span class="token class-name">String</span> firstname<span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> <span class="token function">findPeopleDistinctByLastnameOrFirstname</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">,</span> <span class="token class-name">String</span> firstname<span class="token punctuation">)</span><span class="token punctuation">;</span>

  <span class="token comment">// Enabling ignoring case for an individual property</span>
  <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastnameIgnoreCase</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token comment">// Enabling ignoring case for all suitable properties</span>
  <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastnameAndFirstnameAllIgnoreCase</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">,</span> <span class="token class-name">String</span> firstname<span class="token punctuation">)</span><span class="token punctuation">;</span>

  <span class="token comment">// Enabling static ORDER BY for a query</span>
  <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastnameOrderByFirstnameAsc</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastnameOrderByFirstnameDesc</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>파싱 쿼리 메소드 이름은 주어(Subject)와 술어(Predicate)로 구분됩니다. 첫 번째 부분(<code>find…By</code>, <code>exists…By</code>)은 쿼리 subject을 정의하고 두 번째 부분은 predicate를 구성합니다. 도입절(subject)에는 추가 표현이 포함될 수 있습니다. <code>find</code>(또는 기타 도입 키워드)와 <code>By</code> 사이의 모든 텍스트는 생성될 쿼리에 고유한 플래그를 설정하기 위해 <code>Distinct</code>나 쿼리 결과를 제한하기 위해 <a href="https://docs.spring.io/spring-data/jpa/reference/repositories/query-methods-details.html#repositories.limit-query-result"><code>Top</code>/<code>First</code>와 같은 결과 제한 키워드</a> 중 하나를 사용하지 않는 한 설명적인 것으로 간주됩니다.</p>
<p>부록에는 <a href="https://docs.spring.io/spring-data/jpa/reference/repositories/query-keywords-reference.html#appendix.query.method.predicate">정렬 및 대/소문자 구분 modifiers를 포함한 쿼리 method predicate 키워드</a>와 <a href="https://docs.spring.io/spring-data/jpa/reference/repositories/query-keywords-reference.html#appendix.query.method.subject">쿼리 메소드 subject 키워드의 전체 목록</a>이 포함되어 있습니다. 그러나 첫 번째 <code>By</code>는 실제 기준 predicate의 시작을 나타내는 구분 기호 역할을 합니다. 매우 기본적인 수준에서는 엔터티 속성(property)에 대한 조건을 정의하고 이를 <code>And</code> 및 <code>Or</code>로 연결할 수 있습니다.</p>
<p>메서드 구문 분석의 실제 결과는 쿼리를 생성하는 persistence store에 따라 달라집니다. 그러나 주의해야 할 몇 가지 일반적인 사항이 있습니다.</p>
<ul>
<li>
<p>표현식은 일반적으로 연결될 수 있는 연산자와 결합된 property traversal입니다. <code>AND</code> 및 <code>OR</code>를 사용하여 속성(property) 표현식을 결합할 수 있습니다. 속성 표현식에 대해 <code>Between</code>, <code>LessThan</code>, <code>GreaterThan</code> 및 <code>Like</code>와 같은 연산자도 지원됩니다. 지원되는 연산자는 datastore에 따라 다를 수 있으므로 참조 문서의 해당 부분을 참조하세요.</p>
</li>
<li>
<p>메서드 구문 분석기는 개별 속성(예: <code>findByLastnameIgnoreCase(…)</code>) 또는 대소문자 무시를 지원하는 유형의 모든 속성(일반적으로 <code>String</code> 인스턴스 — 예: <code>findByLastnameAndFirstnameAllIgnoreCase(…)</code>)에 대해 <code>IgnoreCase</code> 플래그 설정을 지원합니다. 대소문자 무시 지원 여부는 store별로 다를 수 있으므로, store별 쿼리 방법은 참고문서의 해당 부분을 참고하세요.</p>
</li>
<li>
<p>속성(property)을 참조하는 쿼리 메서드에 <code>OrderBy</code> 절을 추가하고 정렬 방향(<code>Asc</code> 또는 <code>Desc</code>)을 제공하여 정적 순서를 적용할 수 있습니다. 동적 정렬을 지원하는 쿼리 방법을 만들려면 "<a href="https://docs.spring.io/spring-data/jpa/reference/repositories/query-methods-details.html#">페이징, 큰 결과 순회, 정렬 및 제한</a>"을 참조하세요.</p>
</li>
</ul>
<h1 id="property-expressions">Property Expressions</h1>
<p>속성 식은 앞의 예에 표시된 것처럼 관리 엔터티의 직접 속성만 참조할 수 있습니다. 쿼리 생성 시 구문 분석된 속성이 관리되는 도메인 클래스의 속성인지 이미 확인했습니다. 그러나 중첩된 속성을 탐색하여 제약 조건을 정의할 수도 있습니다. 다음 메서드 서명을 고려하세요.</p>
<pre><code class="language-java"><span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByAddressZipCode</span><span class="token punctuation">(</span><span class="token class-name">ZipCode</span> zipCode<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p><code>Person</code>이 <code>ZipCode</code>가 있는 <code>Address</code>를 가지고 있다고 가정합니다. 이 경우 메소드는 <code>x.address.zipCode</code> 속성 순회(property traversal)를 생성합니다. resolution 알고리즘은 전체 부분(<code>AddressZipCode</code>)을 속성으로 해석하는 것으로 시작하고 도메인 클래스에서 해당 이름(대문자화되지 않음)을 가진 속성을 확인합니다. 알고리즘이 성공하면 해당 속성을 사용합니다. 그렇지 않은 경우 알고리즘은 오른쪽의 카멜 케이스 부분에서 소스를 머리와 꼬리로 분할하고 해당 속성(예: <code>AddressZip</code> 및 <code>Code</code>)을 찾으려고 시도합니다. 알고리즘이 해당 헤드가 있는 속성을 찾으면 꼬리를 가져와서 위에서 설명한 대로 꼬리를 분할하여 계속해서 트리를 만듭니다. 첫 번째 분할이 일치하지 않으면 알고리즘은 분할 지점을 왼쪽(<code>Address</code>, <code>ZipCode</code>)으로 이동하고 계속합니다.</p>
<p>이는 대부분의 경우 작동하지만 알고리즘이 잘못된 속성을 선택할 수도 있습니다. <code>Person</code> 클래스에 <code>addressZip</code> 속성도 있다고 가정합니다. 알고리즘은 이미 첫 번째 분할 라운드에서 일치하고 잘못된 속성을 선택하여 실패합니다(<code>addressZip</code> type에는 <code>code</code> 속성이 없을 수 있으므로).</p>
<p>이 모호함을 해결하려면 메서드 이름 안에 <code>_</code>를 사용하여 순회 지점을 수동으로 정의할 수 있습니다. 따라서 메소드 이름은 다음과 같습니다.</p>
<pre><code class="language-java"><span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByAddress_ZipCode</span><span class="token punctuation">(</span><span class="token class-name">ZipCode</span> zipCode<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>밑줄 문자를 예약 문자로 취급하므로 표준 Java 명명 규칙을 따르는 것이 좋습니다(즉, 속성 이름에 밑줄을 사용하지 않고 대신 카멜 표기법을 사용하는 것).</p>
<h1 id="repository-methods-returning-collections-or-iterables">Repository Methods Returning Collections or Iterables</h1>
<p>여러 결과를 반환하는 쿼리 메서드는 표준 Java <code>Iterable</code>, <code>List</code> 및 <code>Set</code>을 사용할 수 있습니다. 그 외에도 우리는 <code>Iterable</code>의 사용자 정의 확장인 Spring Data의 <code>Streamable</code> 반환과 <a href="https://www.vavr.io/">Vavr</a>에서 제공하는 컬렉션 유형을 지원합니다. 가능한 모든 <a href="https://docs.spring.io/spring-data/jpa/reference/repositories/query-return-types-reference.html#appendix.query.return.types">쿼리 메소드 반환 유형</a>을 설명하는 부록을 참조하세요.</p>
<h2 id="using-streamable-as-query-method-return-type">Using Streamable as Query Method Return Type</h2>
<p><code>Iterable</code> 또는 모든 컬렉션 type 대신 <code>Streamable</code>을 사용할 수 있습니다. 비병렬 <code>Stream</code>(<code>Iterable</code>에는 없음)에 액세스하는 편리한 방법과 요소에 대해 직접 <code>....filter(...)</code> 및 <code>....map(...)</code>을 제공하고 <code>Streamable</code>을 다른 요소에 연결하는 기능을 제공합니다.</p>
<p>Using Streamable to combine query method results</p>
<pre><code class="language-java"><span class="token keyword">interface</span> <span class="token class-name">PersonRepository</span> <span class="token keyword">extends</span> <span class="token class-name">Repository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>
  <span class="token class-name">Streamable</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByFirstnameContaining</span><span class="token punctuation">(</span><span class="token class-name">String</span> firstname<span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token class-name">Streamable</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastnameContaining</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token class-name">Streamable</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> result <span class="token operator">=</span> repository<span class="token punctuation">.</span><span class="token function">findByFirstnameContaining</span><span class="token punctuation">(</span><span class="token string">"av"</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">and</span><span class="token punctuation">(</span>repository<span class="token punctuation">.</span><span class="token function">findByLastnameContaining</span><span class="token punctuation">(</span><span class="token string">"ea"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<h2 id="returning-custom-streamable-wrapper-types">Returning Custom Streamable Wrapper Types</h2>
<p>컬렉션에 대한 전용 래퍼 유형을 제공하는 것은 여러 요소를 반환하는 쿼리 결과에 대한 API를 제공하기 위해 일반적으로 사용되는 패턴입니다. 일반적으로 이러한 유형은 컬렉션과 유사한 유형을 반환하는 저장소 메서드를 호출하고 래퍼 유형의 인스턴스를 수동으로 생성하여 사용됩니다. Spring Data를 사용하면 다음 기준을 충족하는 경우 이러한 래퍼 유형을 쿼리 메서드 반환 유형으로 사용할 수 있으므로 추가 단계를 피할 수 있습니다.</p>
<ol>
<li>
<p>type은 <code>Streamable</code>을 구현합니다.</p>
</li>
<li>
<p>이 type은 생성자 또는 <code>Streamable</code>을 인수로 사용하는 <code>of(…)</code> 또는 <code>valueOf(…)</code>라는 정적 팩터리 메서드를 노출합니다.</p>
</li>
</ol>
<p>다음 목록은 예를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">class</span> <span class="token class-name">Product</span> <span class="token punctuation">{</span> <span class="token comment">//(1)                                         </span>
  <span class="token class-name">MonetaryAmount</span> <span class="token function">getPrice</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span> … <span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@RequiredArgsConstructor</span><span class="token punctuation">(</span>staticName <span class="token operator">=</span> <span class="token string">"of"</span><span class="token punctuation">)</span>
<span class="token keyword">class</span> <span class="token class-name">Products</span> <span class="token keyword">implements</span> <span class="token class-name">Streamable</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Product</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span> <span class="token comment">//(2)         </span>

  <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">Streamable</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Product</span><span class="token punctuation">&gt;</span></span> streamable<span class="token punctuation">;</span>

  <span class="token keyword">public</span> <span class="token class-name">MonetaryAmount</span> <span class="token function">getTotal</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>  <span class="token comment">//(3)                   </span>
    <span class="token keyword">return</span> streamable<span class="token punctuation">.</span><span class="token function">stream</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token function">map</span><span class="token punctuation">(</span><span class="token class-name">Priced</span><span class="token operator">:</span><span class="token operator">:</span><span class="token function">getPrice</span><span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token function">reduce</span><span class="token punctuation">(</span><span class="token class-name">Money</span><span class="token punctuation">.</span><span class="token function">of</span><span class="token punctuation">(</span><span class="token number">0</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token class-name">MonetaryAmount</span><span class="token operator">:</span><span class="token operator">:</span><span class="token function">add</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>


  <span class="token annotation punctuation">@Override</span>
  <span class="token keyword">public</span> <span class="token class-name">Iterator</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Product</span><span class="token punctuation">&gt;</span></span> <span class="token function">iterator</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token comment">//(4)                </span>
    <span class="token keyword">return</span> streamable<span class="token punctuation">.</span><span class="token function">iterator</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token keyword">interface</span> <span class="token class-name">ProductRepository</span> <span class="token keyword">implements</span> <span class="token class-name">Repository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Product</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>
  <span class="token class-name">Products</span> <span class="token function">findAllByDescriptionContaining</span><span class="token punctuation">(</span><span class="token class-name">String</span> text<span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">//(5)</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) 제품 가격에 액세스하기 위해 API를 노출하는 <code>Product</code> 엔터티입니다.<br>
(2) <code>Products.of(…)</code>(Lombok annotation으로 생성된 팩토리 메서드)를 사용하여 생성할 수 있는 <code>Streamable&lt;Product&gt;</code>에 대한 래퍼 type입니다. <code>Streamable&lt;Product&gt;</code>를 취하는 표준 생성자도 마찬가지입니다.<br>
(3) 래퍼 type은 <code>Streamable&lt;Product&gt;</code>에서 새 값을 계산하는 추가 API를 노출합니다.<br>
(4) <code>Streamable</code> 인터페이스를 구현하고 실제 결과에 위임합니다.<br>
(5) 해당 래퍼 유형 <code>Products</code>은 쿼리 메서드 반환 유형으로 직접 사용할 수 있습니다. <code>Streamable&lt;Product&gt;</code>를 반환하고 리포지토리 클라이언트에서 쿼리 후에 수동으로 래핑할 필요가 없습니다.</p>
<h2 id="support-for-vavr-collections">Support for Vavr Collections</h2>
<p><a href="https://www.vavr.io/">Vavr</a>은 Java의 함수형 프로그래밍 개념을 수용하는 라이브러리입니다. 다음 표에 표시된 것처럼 쿼리 메서드 반환 유형으로 사용할 수 있는 사용자 지정 컬렉션 유형 집합이 함께 제공됩니다</p>
<table class="tableblock frame-all grid-all stretch">





<thead>
<tr>
<th class="tableblock halign-left valign-top">Vavr 컬렉션 유형</th>
<th class="tableblock halign-left valign-top">사용된 Vavr 구현 유형</th>
<th class="tableblock halign-left valign-top">유효한 Java 소스 유형</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>io.vavr.collection.Seq</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>io.vavr.collection.List</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>java.util.Iterable</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>io.vavr.collection.Set</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>io.vavr.collection.LinkedHashSet</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>java.util.Iterable</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>io.vavr.collection.Map</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>io.vavr.collection.LinkedHashMap</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>java.util.Map</code></p></td>
</tr>
</tbody>
</table>
<p>실제 쿼리 결과(세 번째 열)의 Java 유형에 따라 첫 번째 열의 유형(또는 그 하위 유형)을 쿼리 메소드 반환 유형으로 사용하고 두 번째 열의 유형을 구현 유형으로 가져올 수 있습니다. 또는 <code>Traversable</code>(Vavr <code>Iterable</code>과 동일)을 선언한 다음 실제 반환 값에서 구현 클래스를 파생시킬 수 있습니다. 즉, <code>java.util.List</code>는 Vavr <code>List</code> 또는 <code>Seq</code>로 바뀌고, <code>java.util.Set</code>은 Vavr <code>LinkedHashSet</code> <code>Set</code>이 되는 식입니다.</p>
<h1 id="streaming-query-results">Streaming Query Results</h1>
<p>Java 8 <code>Stream&lt;T&gt;</code>을 반환 유형으로 사용하여 쿼리 메서드의 결과를 증분적으로 처리할 수 있습니다. 쿼리 결과를 <code>Stream</code>으로 래핑하는 대신 다음 예제와 같이 데이터 저장소별 메서드를 사용하여 스트리밍을 수행합니다.</p>
<p>Java 8 <code>Stream&lt;T&gt;</code>을 사용하여 쿼리 결과 스트리밍</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Query</span><span class="token punctuation">(</span><span class="token string">"select u from User u"</span><span class="token punctuation">)</span>
<span class="token class-name">Stream</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findAllByCustomQueryAndStream</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">Stream</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">readAllByFirstnameNotNull</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Query</span><span class="token punctuation">(</span><span class="token string">"select u from User u"</span><span class="token punctuation">)</span>
<span class="token class-name">Stream</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">streamAllPaged</span><span class="token punctuation">(</span><span class="token class-name">Pageable</span> pageable<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
<code>Stream</code>은 잠재적으로 기본 데이터 저장소별 리소스를 래핑하므로 사용 후에는 닫혀야 합니다. 다음 예제와 같이 <code>close()</code> 메서드를 사용하거나 Java 7 <code>try-with-resources</code> 블록을 사용하여 <code>Stream</code>을 수동으로 닫을 수 있습니다.</p>
</blockquote>
<p><code>Stream&lt;T&gt;</code>로 작업하면 <code>try-with-resources</code> 블록이 발생합니다.</p>
<pre><code class="language-java"><span class="token keyword">try</span> <span class="token punctuation">(</span><span class="token class-name">Stream</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> stream <span class="token operator">=</span> repository<span class="token punctuation">.</span><span class="token function">findAllByCustomQueryAndStream</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
  stream<span class="token punctuation">.</span><span class="token function">forEach</span><span class="token punctuation">(</span>…<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
현재 모든 Spring Data 모듈이 <code>Stream&lt;T&gt;</code>을 반환 유형으로 지원하는 것은 아닙니다.</p>
</blockquote>
<h1 id="asynchronous-query-results">Asynchronous Query Results</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/6.1/integration/scheduling.html">Spring의 비동기 메소드 실행 기능</a>을 사용하여 저장소 쿼리를 비동기적으로 실행할 수 있습니다. 이는 Spring <code>TaskExecutor</code>에 제출된 작업에서 실제 쿼리가 발생하는 동안 메서드가 호출 즉시 반환됨을 의미합니다. 비동기 쿼리는 반응 쿼리와 다르므로 혼합되어서는 안 됩니다. 대응적 지원에 대한 자세한 내용은 매장별 설명서를 참조하세요. 다음 예에서는 다양한 비동기 쿼리를 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Async</span>
<span class="token class-name">Future</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByFirstname</span><span class="token punctuation">(</span><span class="token class-name">String</span> firstname<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// (1)</span>

<span class="token annotation punctuation">@Async</span>
<span class="token class-name">CompletableFuture</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findOneByFirstname</span><span class="token punctuation">(</span><span class="token class-name">String</span> firstname<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// (2)</span></code></pre>
<p>(1) 반환 유형으로 <code>java.util.concurrent.Future</code>를 사용합니다.<br>
(2) 반환 유형으로 Java 8 <code>java.util.concurrent.CompletableFuture</code>를 사용합니다.</p>
<h1 id="paging-iterating-large-results-sorting--limiting">Paging, Iterating Large Results, Sorting &amp; Limiting</h1>
<p>쿼리의 매개변수를 처리하려면 이전 예제에서 이미 본 것처럼 메서드 매개변수를 정의합니다. 그 외에도 인프라는 <code>Pageable</code>, <code>Sort</code> 및 <code>Limit</code>과 같은 특정 type을 인식하여 쿼리에 페이지 매김, 정렬 및 제한을 동적으로 적용합니다. 다음 예에서는 이러한 기능을 보여줍니다.</p>
<p>쿼리 메서드에서 Pageable, Slice, Sort 및 Limit 사용</p>
<pre><code class="language-java"><span class="token class-name">Page</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastname</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">,</span> <span class="token class-name">Pageable</span> pageable<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">Slice</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastname</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">,</span> <span class="token class-name">Pageable</span> pageable<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastname</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">,</span> <span class="token class-name">Sort</span> sort<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastname</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">,</span> <span class="token class-name">Sort</span> sort<span class="token punctuation">,</span> <span class="token class-name">Limit</span> limit<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastname</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">,</span> <span class="token class-name">Pageable</span> pageable<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<blockquote>
<p><strong>[Important]</strong><br>
<code>Sort</code>, <code>Pageable</code> 및 <code>Limit</code>을 사용하는 API는 <code>null</code>이 아닌 값이 메서드에 전달될 것으로 예상합니다. 정렬이나 페이지 매김을 적용하지 않으려면 <code>Sort.unsorted()</code>, <code>Pageable.unpaged()</code> 및 <code>Limit.unlimited()</code>를 사용하세요.</p>
</blockquote>
<p>첫 번째 방법을 사용하면 <code>org.springframework.data.domain.Pageable</code> 인스턴스를 쿼리 메서드에 전달하여 정적으로 정의된 쿼리에 페이징을 동적으로 추가할 수 있습니다. <code>Page</code>는 사용 가능한 총 요소 및 페이지 수를 알고 있습니다. 전체 숫자를 계산하기 위해 개수 쿼리를 트리거하는 인프라를 통해 이를 수행합니다. 비용이 많이 들 수 있으므로(사용된 저장소에 따라) 대신 <code>Slice</code>를 반환할 수 있습니다. <code>Slice</code>는 다음 <code>Slice</code>가 사용 가능한지 여부만 알고 있으며 이는 더 큰 결과 세트를 탐색할 때 충분할 수 있습니다.</p>
<p>정렬 옵션도 <code>Pageable</code> 인스턴스를 통해 처리됩니다. 정렬만 필요한 경우 <code>org.springframework.data.domain.Sort</code> 매개변수를 메서드에 추가하세요. 보시다시피 <code>List</code>를 반환하는 것도 가능합니다. 이 경우 실제 <code>Page</code> 인스턴스를 구축하는 데 필요한 추가 메타데이터가 생성되지 않습니다(즉, 필요했던 추가 개수 쿼리가 실행되지 않음을 의미). 오히려 지정된 엔터티 범위만 조회하도록 쿼리를 제한합니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
전체 쿼리에 대해 얻은 페이지 수를 확인하려면 추가 개수 쿼리를 실행해야 합니다. 기본적으로 이 쿼리는 실제로 트리거한 쿼리에서 파생됩니다.</p>
</blockquote>
<blockquote>
<p><strong>[Important]</strong><br>
특수 매개변수는 쿼리 메소드 내에서 한 번만 사용할 수 있습니다.<br>
위에 설명된 일부 특수 매개변수는 상호 배타적입니다. 다음의 잘못된 매개변수 조합 목록을 고려하세요.</p>
<table class="tableblock frame-all grid-all stretch">





<thead>
<tr>
<th class="tableblock halign-left valign-top">매개변수</th>
<th class="tableblock halign-left valign-top">예시</th>
<th class="tableblock halign-left valign-top">이유</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>Pageable</code> 및 <code>Sort</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findBy…​(Pageable page, Sort sort)</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>Pageable</code>은 이미 <code>Sort</code>를 정의하고 있습니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>Pageable</code> 및 <code>Limit</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>findBy…​(Pageable page, Limit limit)</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>Pageable</code>은 이미 한도를 정의하고 있습니다.</p></td>
</tr>
</tbody>
</table>
</blockquote>
<blockquote>
<p>결과를 제한하는 데 사용되는 <code>Top</code> 키워드는 <code>Pageable</code>과 함께 사용할 수 있는 반면 <code>Top</code>은 결과의 총 최대값을 정의하는 반면 Pageable 매개변수는 이 숫자를 줄일 수 있습니다.</p>
</blockquote>
<h2 id="which-method-is-appropriate">Which Method is Appropriate?</h2>
<p>Spring Data 추상화에서 제공하는 값은 아마도 아래 표에 설명된 가능한 쿼리 메서드 반환 유형으로 가장 잘 표시될 것입니다. 표에는 쿼리 메서드에서 반환할 수 있는 유형이 나와 있습니다.</p>
<p>Table 1. Consuming Large Query Results</p>
<table class="tableblock frame-all grid-all stretch">
<caption class="title">표 1. 대용량 쿼리 결과 소비</caption>






<thead>
<tr>
<th class="tableblock halign-left valign-top">방법</th>
<th class="tableblock halign-left valign-top">데이터 검색 양</th>
<th class="tableblock halign-left valign-top">쿼리 구조</th>
<th class="tableblock halign-left valign-top">제약 사항</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="#repositories.collections-and-iterables"><code>List&lt;T&gt;</code></a></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">모든 결과.</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">단일 쿼리.</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">쿼리 결과가 모든 메모리를 소진할 수 있습니다. 모든 데이터를 검색하는 데 시간이 오래 걸릴 수 있습니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="#repositories.collections-and-iterables.streamable"><code>Streamable&lt;T&gt;</code></a></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">모든 결과.</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">단일 쿼리.</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">쿼리 결과가 모든 메모리를 소진할 수 있습니다. 모든 데이터를 검색하는 데 시간이 오래 걸릴 수 있습니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="#repositories.query-streaming"><code>Stream&lt;T&gt;</code></a></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">체크된 (하나씩 또는 일괄 처리로) <code>Stream</code> 소비에 따라 청크화됩니다.</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">일반적으로 커서를 사용한 단일 쿼리.</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">리소스 누수를 피하기 위해 사용 후 스트림을 닫아야 합니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>Flux&lt;T&gt;</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">체크된 (하나씩 또는 일괄 처리로) <code>Flux</code> 소비에 따라 청크화됩니다.</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">일반적으로 커서를 사용한 단일 쿼리.</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">저장 모듈이 반응형 인프라를 제공해야 합니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>Slice&lt;T&gt;</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>Pageable.getOffset()</code>에서 시작하는 데이터 검색 양은 <code>Pageable.getPageSize() + 1</code>입니다.</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>Pageable.getOffset()</code>에서 시작하는 데이터를 검색하는 일대다 쿼리를 적용하여 데이터를 검색합니다.</p></td>
<td class="tableblock halign-left valign-top"><div class="content"><div class="paragraph">
<p><code>Slice</code>는 다음 <code>Slice</code>로 이동할 수 있습니다.</p>
</div>
<div class="ulist">
<ul>
<li>
<p><code>Slice</code>는 더 많은 데이터를 검색할 수 있는지에 대한 세부 정보를 제공합니다.</p>
</li>
<li>
<p>오프셋 기반 쿼리는 오프셋이 너무 큰 경우 효율이 떨어집니다. 왜냐하면 데이터베이스는 여전히 전체 결과를 실체화해야 하기 때문입니다.</p>
</li>
<li>
<p><code>Window</code>는 더 많은 데이터를 검색할 수 있는지에 대한 세부 정보를 제공합니다.</p>
</li>
<li>
<p>오프셋 기반 쿼리는 오프셋이 너무 큰 경우 효율이 떨어집니다. 왜냐하면 데이터베이스는 여전히 전체 결과를 실체화해야 하기 때문입니다.</p>
</li>
</ul>
</div></div></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>Page&lt;T&gt;</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>Pageable.getOffset()</code>에서 시작하는 데이터 검색 양은 <code>Pageable.getPageSize()</code>입니다.</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>Pageable.getOffset()</code>에서 시작하는 일대다 쿼리를 적용하여 데이터를 검색합니다. 추가로, 총 요소 수를 결정하기 위해 <code>COUNT(…)</code> 쿼리가 필요할 수 있습니다.</p></td>
<td class="tableblock halign-left valign-top"><div class="content"><div class="paragraph">
<p>자주 <code>COUNT(…)</code> 쿼리가 필요하며 이는 비용이 많이 듭니다.</p>
</div>
<div class="ulist">
<ul>
<li>
<p>오프셋 기반 쿼리는 오프셋이 너무 큰 경우 효율이 떨어집니다. 왜냐하면 데이터베이스는 여전히 전체 결과를 실체화해야 하기 때문입니다.</p>
</li>
</ul>
</div></div></td>
</tr>
</tbody>
</table>
<h2 id="paging-and-sorting">Paging and Sorting</h2>
<p>속성 이름을 사용하여 간단한 정렬 표현식을 정의할 수 있습니다. 표현식을 연결하여 여러 기준을 하나의 표현식으로 수집할 수 있습니다.</p>
<p>Defining sort expressions</p>
<pre><code class="language-java"><span class="token class-name">Sort</span> sort <span class="token operator">=</span> <span class="token class-name">Sort</span><span class="token punctuation">.</span><span class="token function">by</span><span class="token punctuation">(</span><span class="token string">"firstname"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">ascending</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">and</span><span class="token punctuation">(</span><span class="token class-name">Sort</span><span class="token punctuation">.</span><span class="token function">by</span><span class="token punctuation">(</span><span class="token string">"lastname"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">descending</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>보다 형식에 안전한 정렬 식을 정의하려면 정렬 식을 정의할 형식부터 시작하고 메서드 참조를 사용하여 정렬할 속성을 정의하세요.</p>
<p>유형이 안전한 API를 사용하여 정렬 표현식 정의</p>
<pre><code class="language-java"><span class="token class-name">TypedSort</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> person <span class="token operator">=</span> <span class="token class-name">Sort</span><span class="token punctuation">.</span><span class="token function">sort</span><span class="token punctuation">(</span><span class="token class-name">Person</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">Sort</span> sort <span class="token operator">=</span> person<span class="token punctuation">.</span><span class="token function">by</span><span class="token punctuation">(</span><span class="token class-name">Person</span><span class="token operator">:</span><span class="token operator">:</span><span class="token function">getFirstname</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">ascending</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">and</span><span class="token punctuation">(</span>person<span class="token punctuation">.</span><span class="token function">by</span><span class="token punctuation">(</span><span class="token class-name">Person</span><span class="token operator">:</span><span class="token operator">:</span><span class="token function">getLastname</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">descending</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
<code>TypedSort.by(…)</code>는 (일반적으로) CGlib를 사용하여 런타임 프록시를 사용합니다. 이는 Graal VM Native와 같은 도구를 사용할 때 기본 이미지 컴파일을 방해할 수 있습니다.</p>
</blockquote>
<p>저장소 구현이 Querydsl을 지원하는 경우 생성된 메타모델 유형을 사용하여 정렬 표현식을 정의할 수도 있습니다.</p>
<p>Defining sort expressions by using the Querydsl API</p>
<pre><code class="language-java"><span class="token class-name">QSort</span> sort <span class="token operator">=</span> <span class="token class-name">QSort</span><span class="token punctuation">.</span><span class="token function">by</span><span class="token punctuation">(</span><span class="token class-name">QPerson</span><span class="token punctuation">.</span>firstname<span class="token punctuation">.</span><span class="token function">asc</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">and</span><span class="token punctuation">(</span><span class="token class-name">QSort</span><span class="token punctuation">.</span><span class="token function">by</span><span class="token punctuation">(</span><span class="token class-name">QPerson</span><span class="token punctuation">.</span>lastname<span class="token punctuation">.</span><span class="token function">desc</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<h2 id="limiting-query-results">Limiting Query Results</h2>
<p>페이징 외에도 전용 <code>Limit</code> 매개변수를 사용하여 결과 크기를 제한할 수 있습니다. 또한 <code>First</code> 또는 <code>Top</code> 키워드를 사용하여 쿼리 메서드의 결과를 제한할 수도 있습니다. 이 키워드는 서로 바꿔서 사용할 수 있지만 <code>Limit</code> 매개 변수와 혼합할 수는 없습니다. <code>Top</code> 또는 <code>First</code>에 선택적 숫자 값을 추가하여 반환할 최대 결과 크기를 지정할 수 있습니다. 숫자를 생략하면 결과 크기가 1로 가정됩니다. 다음 예에서는 쿼리 크기를 제한하는 방법을 보여줍니다.</p>
<p>Top 및 First를 사용하여 쿼리 결과 크기 제한</p>
<pre><code class="language-java"><span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastname</span><span class="token punctuation">(</span><span class="token class-name">Limit</span> limit<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">User</span> <span class="token function">findFirstByOrderByLastnameAsc</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">User</span> <span class="token function">findTopByOrderByAgeDesc</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">Page</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">queryFirst10ByLastname</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">,</span> <span class="token class-name">Pageable</span> pageable<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">Slice</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findTop3ByLastname</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">,</span> <span class="token class-name">Pageable</span> pageable<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findFirst10ByLastname</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">,</span> <span class="token class-name">Sort</span> sort<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findTop10ByLastname</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">,</span> <span class="token class-name">Pageable</span> pageable<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>제한 표현식은 고유 쿼리를 지원하는 데이터 저장소에 대해 <code>Distinct</code> 키워드도 지원합니다. 또한 결과 집합을 하나의 인스턴스로 제한하는 쿼리의 경우 <code>Optional</code> 키워드를 사용하여 결과를 래핑하는 것이 지원됩니다.</p>
<p>제한된 쿼리 페이지네이션(및 사용 가능한 페이지 수 계산)에 페이지네이션 또는 슬라이싱을 적용하는 경우 제한된 결과 내에서 적용됩니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
<code>Sort</code> 매개변수를 사용하여 동적 정렬과 함께 결과를 제한하면 'K' 가장 작은 요소뿐만 아니라 'K' 가장 큰 요소에 대한 쿼리 방법을 표현할 수 있습니다.</p>
</blockquote>