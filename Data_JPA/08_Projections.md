<h1 id="projections">Projections</h1>
<p>Spring Data 쿼리 메소드는 일반적으로 저장소에서 관리하는 집계 루트의 하나 또는 여러 인스턴스를 반환합니다. 그러나 때로는 해당 유형의 특정 속성을 기반으로 프로젝션을 생성하는 것이 바람직할 수도 있습니다. Spring Data를 사용하면 전용 반환 유형을 모델링하여 관리되는 집계의 부분 보기를 보다 선택적으로 검색할 수 있습니다.</p>
<p>다음 예와 같은 저장소 및 집계 루트 유형을 상상해 보십시오.</p>
<p>A sample aggregate and repository</p>
<pre><code class="language-java"><span class="token keyword">class</span> <span class="token class-name">Person</span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@Id</span> <span class="token class-name">UUID</span> id<span class="token punctuation">;</span>
  <span class="token class-name">String</span> firstname<span class="token punctuation">,</span> lastname<span class="token punctuation">;</span>
  <span class="token class-name">Address</span> address<span class="token punctuation">;</span>

  <span class="token keyword">static</span> <span class="token keyword">class</span> <span class="token class-name">Address</span> <span class="token punctuation">{</span>
    <span class="token class-name">String</span> zipCode<span class="token punctuation">,</span> city<span class="token punctuation">,</span> street<span class="token punctuation">;</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token keyword">interface</span> <span class="token class-name">PersonRepository</span> <span class="token keyword">extends</span> <span class="token class-name">Repository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">,</span> UUID<span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token class-name">Collection</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastname</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이제 사람의 이름 속성만 검색한다고 가정해 보겠습니다. 이를 달성하기 위해 Spring Data는 무엇을 제공합니까? 이 장의 나머지 부분에서는 그 질문에 답합니다.</p>
<h2 id="interface-based-projections">Interface-based Projections</h2>
<p>쿼리 결과를 name 특성으로만 제한하는 가장 쉬운 방법은 다음 예제와 같이 읽을 속성에 대한 접근자 메서드를 노출하는 인터페이스를 선언하는 것입니다.</p>
<p>A projection interface to retrieve a subset of attributes</p>
<pre><code class="language-java"><span class="token keyword">interface</span> <span class="token class-name">NamesOnly</span> <span class="token punctuation">{</span>

  <span class="token class-name">String</span> <span class="token function">getFirstname</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token class-name">String</span> <span class="token function">getLastname</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>여기서 중요한 점은 여기에 정의된 속성이 집계 루트의 속성과 정확히 일치한다는 것입니다. 이렇게 하면 다음과 같이 쿼리 메서드를 추가할 수 있습니다.</p>
<p>A repository using an interface based projection with a query method</p>
<pre><code class="language-java"><span class="token keyword">interface</span> <span class="token class-name">PersonRepository</span> <span class="token keyword">extends</span> <span class="token class-name">Repository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">,</span> UUID<span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token class-name">Collection</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">NamesOnly</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastname</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>쿼리 실행 엔진은 반환된 각 요소에 대해 런타임 시 해당 인터페이스의 프록시 인스턴스를 생성하고 노출된 메서드에 대한 호출을 대상 개체에 전달합니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
기본 메서드(예: <code>CrudRepository</code>, store 별 repository 인터페이스 또는 <code>Simple…Repository</code>에서 선언됨)를 재정의하는 메서드를 저장소에 선언하면 선언된 반환 유형에 관계없이 기본 메서드가 호출됩니다. 프로젝션에는 기본 메소드를 사용할 수 없으므로 호환 가능한 반환 유형을 사용해야 합니다. 일부 저장소 모듈은 <code>@Query</code> annotation을 지원하여 재정의된 기본 메서드를 쿼리 메서드로 변환한 다음 프로젝션을 반환하는 데 사용할 수 있습니다.</p>
</blockquote>
<p>투영은 재귀적으로 사용될 수 있습니다. <code>Address</code> 정보 중 일부도 포함하려면 다음 예제와 같이 이에 대한 프로젝션 인터페이스를 만들고 <code>getAddress()</code> 선언에서 해당 인터페이스를 반환합니다.</p>
<p>A projection interface to retrieve a subset of attributes</p>
<pre><code class="language-java"><span class="token keyword">interface</span> <span class="token class-name">PersonSummary</span> <span class="token punctuation">{</span>

  <span class="token class-name">String</span> <span class="token function">getFirstname</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token class-name">String</span> <span class="token function">getLastname</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token class-name">AddressSummary</span> <span class="token function">getAddress</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

  <span class="token keyword">interface</span> <span class="token class-name">AddressSummary</span> <span class="token punctuation">{</span>
    <span class="token class-name">String</span> <span class="token function">getCity</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>메서드 호출 시 대상 인스턴스의 주소 속성을 가져와서 프로젝션 프록시에 래핑합니다.</p>
<h3 id="closed-projections">Closed Projections</h3>
<p>접근자 메서드가 모두 대상 집계의 속성과 일치하는 프로젝션 인터페이스는 닫힌 프로젝션으로 간주됩니다. 다음 예제(이 장의 앞부분에서도 사용함)는 닫힌 투영입니다.</p>
<p>A closed projection</p>
<pre><code class="language-java"><span class="token keyword">interface</span> <span class="token class-name">NamesOnly</span> <span class="token punctuation">{</span>

  <span class="token class-name">String</span> <span class="token function">getFirstname</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token class-name">String</span> <span class="token function">getLastname</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>폐쇄형 프로젝션을 사용하는 경우 Spring Data는 프로젝션 프록시를 지원하는 데 필요한 모든 속성을 알고 있으므로 쿼리 실행을 최적화할 수 있습니다. 이에 대한 자세한 내용은 참조 문서의 모듈별 부분을 참조하세요.</p>
<h3 id="open-projections">Open Projections</h3>
<p>프로젝션 인터페이스의 접근자 메서드는 다음 예제와 같이 @Value 주석을 사용하여 새 값을 계산하는 데에도 사용할 수 있습니다.</p>
<p>An Open Projection</p>
<pre><code class="language-java"><span class="token keyword">interface</span> <span class="token class-name">NamesOnly</span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@Value</span><span class="token punctuation">(</span><span class="token string">"#{target.firstname + ' ' + target.lastname}"</span><span class="token punctuation">)</span>
  <span class="token class-name">String</span> <span class="token function">getFullName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  …
<span class="token punctuation">}</span></code></pre>
<p>투영을 뒷받침하는 집계 루트는 <code>target</code> 변수에서 사용할 수 있습니다. <code>@Value</code>를 사용하는 프로젝션 인터페이스는 개방형 프로젝션입니다. 이 경우 Spring Data는 쿼리 실행 최적화를 적용할 수 없습니다. SpEL 표현식이 집계 루트의 모든 속성을 사용할 수 있기 때문입니다.</p>
<p><code>@Value</code>에 사용되는 표현식은 너무 복잡해서는 안 됩니다. 즉, <code>String</code> 변수로 프로그래밍하는 것을 피하는 것이 좋습니다. 매우 간단한 표현식의 경우 다음 예제와 같이 기본 메서드(Java 8에 도입됨)를 사용하는 것이 한 가지 옵션일 수 있습니다.</p>
<p>A projection interface using a default method for custom logic</p>
<pre><code class="language-java"><span class="token keyword">interface</span> <span class="token class-name">NamesOnly</span> <span class="token punctuation">{</span>

  <span class="token class-name">String</span> <span class="token function">getFirstname</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token class-name">String</span> <span class="token function">getLastname</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

  <span class="token keyword">default</span> <span class="token class-name">String</span> <span class="token function">getFullName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> <span class="token function">getFirstname</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">concat</span><span class="token punctuation">(</span><span class="token string">" "</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">concat</span><span class="token punctuation">(</span><span class="token function">getLastname</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이 접근 방식을 사용하려면 프로젝션 인터페이스에 노출된 다른 접근자 메서드를 기반으로 논리를 구현할 수 있어야 합니다. 두 번째로 더 유연한 옵션은 다음 예제와 같이 Spring Bean에서 사용자 정의 로직을 구현한 후 SpEL 표현식에서 이를 호출하는 것입니다.</p>
<p>Sample Person object</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Component</span>
<span class="token keyword">class</span> <span class="token class-name">MyBean</span> <span class="token punctuation">{</span>

  <span class="token class-name">String</span> <span class="token function">getFullName</span><span class="token punctuation">(</span><span class="token class-name">Person</span> person<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    …
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token keyword">interface</span> <span class="token class-name">NamesOnly</span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@Value</span><span class="token punctuation">(</span><span class="token string">"#{@myBean.getFullName(target)}"</span><span class="token punctuation">)</span>
  <span class="token class-name">String</span> <span class="token function">getFullName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  …
<span class="token punctuation">}</span></code></pre>
<p>SpEL 표현식이 어떻게 <code>myBean</code>을 참조하고 <code>getFullName(…)</code> 메소드를 호출하고 프로젝션 대상을 메소드 매개변수로 전달하는지 확인하십시오. SpEL 표현식 평가가 지원되는 메소드는 표현식에서 참조할 수 있는 메소드 매개변수를 사용할 수도 있습니다. 메소드 매개변수는 <code>args</code>라는 <code>Object</code> 배열을 통해 사용할 수 있습니다. 다음 예에서는 <code>args</code> 배열에서 메서드 매개변수를 가져오는 방법을 보여줍니다.</p>
<p>Sample Person object</p>
<pre><code class="language-java"><span class="token keyword">interface</span> <span class="token class-name">NamesOnly</span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@Value</span><span class="token punctuation">(</span><span class="token string">"#{args[0] + ' ' + target.firstname + '!'}"</span><span class="token punctuation">)</span>
  <span class="token class-name">String</span> <span class="token function">getSalutation</span><span class="token punctuation">(</span><span class="token class-name">String</span> prefix<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>다시 말하지만, 더 복잡한 표현식의 경우 <a href="https://docs.spring.io/spring-data/jpa/reference/repositories/projections.html#projections.interfaces.open.bean-reference">앞서</a> 설명한 대로 Spring 빈을 사용하고 표현식이 메서드를 호출하도록 해야 합니다.</p>
<h3 id="nullable-wrappers">Nullable Wrappers</h3>
<p>프로젝션 인터페이스의 Getter는 null 안전성 향상을 위해 nullable 래퍼를 사용할 수 있습니다. 현재 지원되는 래퍼 유형은 다음과 같습니다.</p>
<ul>
<li><code>java.util.Optional</code></li>
<li><code>com.google.common.base.Optional</code></li>
<li><code>scala.Option</code></li>
<li><code>io.vavr.control.Option</code></li>
</ul>
<p>A projection interface using nullable wrappers</p>
<pre><code class="language-java"><span class="token keyword">interface</span> <span class="token class-name">NamesOnly</span> <span class="token punctuation">{</span>

  <span class="token class-name">Optional</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> <span class="token function">getFirstname</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>기본 프로젝션 값이 <code>null</code>이 아닌 경우 래퍼 유형의 현재 표현을 사용하여 값이 반환됩니다. 지원 값이 <code>null</code>인 경우 getter 메서드는 사용된 래퍼 유형의 빈 표현을 반환합니다.</p>
<h2 id="class-based-projectionsdtos">Class-based Projections(DTOs)</h2>
<p>프로젝션을 정의하는 또 다른 방법은 검색할 필드에 대한 속성을 보유하는 값 유형 DTO(데이터 전송 개체)를 사용하는 것입니다. 이러한 DTO 유형은 프록싱이 발생하지 않고 중첩된 프로젝션이 적용될 수 없다는 점을 제외하면 프로젝션 인터페이스가 사용되는 것과 정확히 동일한 방식으로 사용할 수 있습니다.</p>
<p>저장소가 로드할 필드를 제한하여 쿼리 실행을 최적화하는 경우 로드할 필드는 노출되는 생성자의 매개 변수 이름에서 결정됩니다.</p>
<p>다음 예에서는 프로젝션 DTO를 보여줍니다.</p>
<p>A projecting DTO</p>
<pre><code class="language-java">record <span class="token class-name">NamesOnly</span><span class="token punctuation">(</span><span class="token class-name">String</span> firstname<span class="token punctuation">,</span> <span class="token class-name">String</span> lastname<span class="token punctuation">)</span> <span class="token punctuation">{</span>
<span class="token punctuation">}</span></code></pre>
<p>Java 레코드는 값 의미 체계를 준수하므로 DTO 유형을 정의하는 데 이상적입니다. 모든 필드는 <code>private final</code> 필드이며 <code>equals(…)</code>/<code>hashCode()</code>/<code>toString()</code> 메서드가 자동으로 생성됩니다. 또는 프로젝션하려는 속성을 정의하는 모든 클래스를 사용할 수 있습니다.</p>
<h2 id="dynamic-projections">Dynamic Projections</h2>
<p>지금까지 우리는 프로젝션 유형을 컬렉션의 반환 유형 또는 요소 유형으로 사용했습니다. 그러나 호출 시 사용할 유형을 선택하여 동적으로 만들 수도 있습니다. 동적 프로젝션을 적용하려면 다음 예에 표시된 것과 같은 쿼리 메서드를 사용합니다.</p>
<p>A repository using a dynamic projection parameter</p>
<pre><code class="language-java"><span class="token keyword">interface</span> <span class="token class-name">PersonRepository</span> <span class="token keyword">extends</span> <span class="token class-name">Repository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">,</span> UUID<span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token class-name">Collection</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastname</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">,</span> <span class="token class-name">Class</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> type<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이러한 방식으로 메서드를 사용하여 다음 예에 표시된 대로 투영을 적용하거나 있는 그대로 집계를 얻을 수 있습니다.</p>
<p>Using a repository with dynamic projections</p>
<pre><code class="language-java"><span class="token keyword">void</span> <span class="token function">someMethod</span><span class="token punctuation">(</span><span class="token class-name">PersonRepository</span> people<span class="token punctuation">)</span> <span class="token punctuation">{</span>

  <span class="token class-name">Collection</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> aggregates <span class="token operator">=</span>
    people<span class="token punctuation">.</span><span class="token function">findByLastname</span><span class="token punctuation">(</span><span class="token string">"Matthews"</span><span class="token punctuation">,</span> <span class="token class-name">Person</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

  <span class="token class-name">Collection</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">NamesOnly</span><span class="token punctuation">&gt;</span></span> aggregates <span class="token operator">=</span>
    people<span class="token punctuation">.</span><span class="token function">findByLastname</span><span class="token punctuation">(</span><span class="token string">"Matthews"</span><span class="token punctuation">,</span> <span class="token class-name">NamesOnly</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
<code>Class</code> 유형의 쿼리 매개변수가 동적 프로젝션 매개변수로 적합한지 여부를 검사합니다. 쿼리의 실제 반환 유형이 <code>Class</code> 매개변수의 일반 매개변수 유형과 동일한 경우 일치하는 <code>Class</code> 매개변수를 쿼리 또는 SpEL 표현식 내에서 사용할 수 없습니다. <code>Class</code> 매개변수를 쿼리 인수로 사용하려면 <code>Class&lt;?&gt;</code>와 같은 다른 generic 매개변수를 사용해야 합니다.</p>
</blockquote>
<blockquote>
<p><strong>[Note]</strong><br>
JPQL을 사용한 클래스 기반 프로젝션은 JPQL 표현식의 생성자 표현식으로 제한된다는 점에 유의하는 것이 중요합니다. <code>SELECT new com.example.NamesOnly(u.firstname, u.lastname) from User u</code>. (DTO 유형의 FQDN 사용법에 유의하세요!) 이 JPQL 표현식은 명명된 쿼리를 정의하는 <code>@Query</code> annotation에서도 사용할 수 있습니다. 그리고 클래스 기반 프로젝션은 기본 쿼리와 전혀 작동하지 않는다는 점을 지적하는 것이 중요합니다. 해결 방법으로 <code>ResultSetMapping</code> 또는 Hibernate 특정 <code>ResultTransformer</code>와 함께 명명된 쿼리를 사용할 수 있습니다.</p>
</blockquote>