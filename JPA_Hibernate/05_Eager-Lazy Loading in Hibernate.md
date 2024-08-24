<p>ORM에서 데이터를 fetch 또는 load 하는 방식은 크게 eager와 lazy로 나뉜다. </p>
<h2 id="eager-and-lazy-loading">Eager and Lazy Loading</h2>
<ul>
<li>Eager Loading : 데이터 초기화가 즉석에서(on the spot) 발생하는 디자인 패턴</li>
<li>Lazy Loading : 객체의 초기화를 가능한 지연시키는 디자인 패턴</li>
</ul>
<p>예시) UserLazy 클래스</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token annotation punctuation">@Table</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"USER"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">UserLazy</span> <span class="token keyword">implements</span> <span class="token class-name">Serializable</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Id</span>
    <span class="token annotation punctuation">@GeneratedValue</span>
    <span class="token annotation punctuation">@Column</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"USER_ID"</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">Long</span> userId<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@OneToMany</span><span class="token punctuation">(</span>fetch <span class="token operator">=</span> <span class="token class-name">FetchType</span><span class="token punctuation">.</span>LAZY<span class="token punctuation">,</span> mappedBy <span class="token operator">=</span> <span class="token string">"user"</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">Set</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">OrderDetail</span><span class="token punctuation">&gt;</span></span> orderDetail <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">HashSet</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token comment">// standard setters and getters</span>
    <span class="token comment">// also override equals and hashcode</span>

<span class="token punctuation">}</span></code></pre>
<p>예시) OrderDetail 클래스</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token annotation punctuation">@Table</span> <span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"USER_ORDER"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">OrderDetail</span> <span class="token keyword">implements</span> <span class="token class-name">Serializable</span> <span class="token punctuation">{</span>
    
    <span class="token annotation punctuation">@Id</span>
    <span class="token annotation punctuation">@GeneratedValue</span>
    <span class="token annotation punctuation">@Column</span><span class="token punctuation">(</span>name<span class="token operator">=</span><span class="token string">"ORDER_ID"</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">Long</span> orderId<span class="token punctuation">;</span>
    
    <span class="token annotation punctuation">@ManyToOne</span><span class="token punctuation">(</span>fetch <span class="token operator">=</span> <span class="token class-name">FetchType</span><span class="token punctuation">.</span>LAZY<span class="token punctuation">)</span>
    <span class="token annotation punctuation">@JoinColumn</span><span class="token punctuation">(</span>name<span class="token operator">=</span><span class="token string">"USER_ID"</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">UserLazy</span> user<span class="token punctuation">;</span>

    <span class="token comment">// standard setters and getters</span>
    <span class="token comment">// also override equals and hashcode</span>

<span class="token punctuation">}</span></code></pre>
<p>하나의 User는 다수의 OrderDetail을 가질 수 있다. Eager Load 전략에서는 User 데이터를 로드하면, 이와 관련된 모든 order 들을 로드하여 메모리에 적재할 것이다.<br>
하지만 Lazy Loading을 활성화 하는 UserLazy를 가져온다면 명시적 호출을 할 때까지 OrderDetail 데이터가 초기화 되어 메모리에 로드되지 않을 것이다.</p>
<h2 id="loading-configuration">Loading Configuration</h2>
<p>Fetch 전략을 Hibernate에서 설정하는 방법을 알아보자<br>
어노테이션 매개변수를 이용하여 Lazy Loading 또는 Eager Fetching을 활성화 할 수 있다.</p>
<pre><code class="language-java">fetch <span class="token operator">=</span> <span class="token class-name">FetchType</span><span class="token punctuation">.</span>LAZY</code></pre>
<pre><code class="language-java">fetch <span class="token operator">=</span> <span class="token class-name">FetchType</span><span class="token punctuation">.</span>EAGER</code></pre>
<h2 id="두-전략의-차이점">두 전략의 차이점</h2>
<pre><code class="language-java"><span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">UserLazy</span><span class="token punctuation">&gt;</span></span> users <span class="token operator">=</span> sessionLazy<span class="token punctuation">.</span><span class="token function">createQuery</span><span class="token punctuation">(</span><span class="token string">"From UserLazy"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">list</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token class-name">UserLazy</span> userLazyLoaded <span class="token operator">=</span> users<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token number">3</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token keyword">return</span> <span class="token punctuation">(</span>userLazyLoaded<span class="token punctuation">.</span><span class="token function">getOrderDetail</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>Lazy 초기화 접근법에서 orderDetail은 우리가 getter 또는 다른 메서드로 명시적으로 호출할 때까지 초기화되지 않는다.</p>
<pre><code class="language-java"><span class="token class-name">UserLazy</span> userLazyLoaded <span class="token operator">=</span> users<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token number">3</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>하지만 Eager 전략을 사용하는 UserEager에서는 첫번째 줄에서 즉시 초기화 될 것이다.</p>
<pre><code class="language-java"><span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">UserEager</span><span class="token punctuation">&gt;</span></span> user <span class="token operator">=</span> sessionEager<span class="token punctuation">.</span><span class="token function">createQuery</span><span class="token punctuation">(</span><span class="token string">"From UserEager"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">list</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<ul>
<li>Lazy Loading : 프록시 객체를 사용하고 별도의 SQL 쿼리를 실행하여 orderDetailSet을 로드</li>
<li>Lazy Loading을 사용하지 않는다면 필요에 관계없이 많은 데이터를 가져오고 저장하게 된다. </li>
</ul>
<p>예시) Hibernate 기능 테스트</p>
<pre><code class="language-java"><span class="token class-name">Hibernate</span><span class="token punctuation">.</span><span class="token function">isInitialized</span><span class="token punctuation">(</span>orderDetailSet<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>예시) 생성된 SQL 쿼리를 보여주는 fetching.hbm.xml 설정</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>show_sql<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>true<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>예시) 콘솔에 출력된 Lazy Loading 쿼리 </p>
<pre><code class="language-sql"><span class="token keyword">select</span> user0_<span class="token punctuation">.</span>USER_ID <span class="token keyword">as</span> USER_ID1_0_<span class="token punctuation">,</span>  <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> <span class="token keyword">from</span> <span class="token keyword">USER</span> user0_</code></pre>
<p>예시) 콘솔에 출력된 Eager Fetching 쿼리</p>
<pre><code class="language-sql"><span class="token keyword">select</span> orderdetail0_<span class="token punctuation">.</span>USER_ID <span class="token keyword">as</span> USER_ID4_0_0_<span class="token punctuation">,</span> orderdetail0_<span class="token punctuation">.</span>ORDER_ID <span class="token keyword">as</span> ORDER_ID1_1_0_<span class="token punctuation">,</span> orderdetail0_ <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
  <span class="token keyword">from</span> USER_ORDER orderdetail0_ <span class="token keyword">where</span> orderdetail0_<span class="token punctuation">.</span>USER_ID<span class="token operator">=</span>?</code></pre>
<p>Lazy Loading과는 다르게 Eager Fetching에서는 USER_ORDER로 이루어진 JOIN을 한다. 위 쿼리는 모든 User에 대해 생성되므로 다른 접근법 보다 많은 메모리를 사용하게 된다.</p>
<blockquote>
<pre><code class="language-java">from USER_ORDER orderdetail0_ where orderdetail0_<span class="token punctuation">.</span>USER_ID<span class="token operator">=</span><span class="token operator">?</span> </code></pre>
<p>쿼리에서 맨 끝의 ?는 바인딩 변수(parameter)를 나타냅니다.<br>
바인딩 변수는 쿼리 실행 시에 실제 값으로 대체되는 자리 표시자입니다. 이렇게 함으로써 쿼리가 실행되기 전에 어떤 값을 바인딩할지 결정할 수 있습니다. 바인딩 변수는 쿼리의 조건절이나 파라미터화된 쿼리에 사용되어 특정 조건을 만족하는 데이터를 가져오거나 조작하는 데 사용됩니다.</p>
<p>예를 들어, orderdetail0<em>.USER_ID=? 쿼리에서 ?는 orderdetail0</em>의 USER_ID 열에 바인딩될 값이 됩니다. 이 쿼리를 실행할 때, ? 자리에 실제로 사용될 값을 설정하여 쿼리를 완성하게 됩니다. 바인딩 변수를 사용하면 동일한 쿼리를 여러 번 실행할 때마다 다른 값으로 실행할 수 있습니다.</p>
</blockquote>
<h2 id="advantages-and-disadvantages--lazy-loading">Advantages and Disadvantages : Lazy Loading</h2>
<p>장점</p>
<ul>
<li>훨씬 적은 초기화 로드 시간</li>
<li>적은 메모리 소비</li>
</ul>
<p>단점</p>
<ul>
<li>예상하지 못한 순간에 지연된 초기화는 성능에 영향을 미칠 수 있다.</li>
<li>지연 초기화된 객체는 예외를 발생시키거나 추가적인 조치를 필요로 할 수 있다.</li>
</ul>
<h2 id="advantages-and-disadvantages--eager-loading">Advantages and Disadvantages : Eager Loading</h2>
<p>장점</p>
<ul>
<li>지연된 초기화 관련 성능 이슈 없음</li>
</ul>
<p>단점</p>
<ul>
<li>긴 초기화 로드 시간</li>
<li>불필요한 데이터를 지나치게 많이 로드하는 것은 성능에 영향을 미칠 수 있다.</li>
</ul>
<h2 id="jpa-default-fetchtype">JPA Default FetchType</h2>
<p>기본적으로 @OneToMany, @ManyToMany 연관해서는 FetchType.LAZY전략을 사용하는 반면 @OneToOne, @ManyToOne은 FetchType.EAGER 전략을 사용함</p>
<h2 id="lazy-loading-in-hibernate">Lazy Loading in Hibernate</h2>
<p>Hibernate에서는 클래스들의 프록시 구현을 제공함으로서 엔터티와 associations(데이터베이스의 relation과 비슷한 의미)에게 Lazy Loading을 적용한다.<br>
Hibernate는 엔터티를 엔터티의 클래스로부터 나온 프록시로 대체함으로서 엔터티를 향한 호출을 가로챈다. 우리의 예시에서는 제어권이 User 클래스 구현으로 양도되기 전에 데이터베이스에서 누락된 request 정보가 로드 된다.</p>
<blockquote>
<p>엔터티를 실제 사용하는 코드가 실행되기 전에, 데이터베이스에 있는 추가적인 연관 데이터를 프록시를 통해 로드한다</p>
</blockquote>
<p>또한 예시의 Set orderDetailSet 처럼 association이 컬렉션 클래스로 표현될 때, 래퍼클래스가 생성되어 초기의 컬렉션을 대체한다는 점에 주목.</p>
<blockquote>
<p>래퍼 클래스(Wrapper Class)는 기본 데이터 타입(primitive data type)을 객체로 감싸주는 클래스를 말합니다. 기본 데이터 타입(int, float, char, boolean 등)은 원시적인 값을 나타내는 데이터 타입인 반면, 래퍼 클래스(Integer, Float, Character, Boolean)는 이러한 기본 데이터 타입을 객체로 감싸서 객체 지향 프로그래밍에서 활용할 수 있도록 도와줍니다. 제네릭(Generic) 프로그래밍, 컬렉션(Collection) 사용 등에서 유용하게 활용할 수 있습니다.</p>
</blockquote>
<blockquote>
<p>컬렉션 클래스는 기본적으로 래퍼 클래스가 아닙니다. 컬렉션 클래스는 Java의 표준 라이브러리나 다른 라이브러리에서 제공하는 일반적인 데이터 구조를 나타내는 클래스입니다. </p>
</blockquote>
<blockquote>
<p>예를 들어, Hibernate에서는 PersistentSet, PersistentList, PersistentMap 등의 래퍼 클래스를 제공하여 컬렉션의 엔터티 관계를 관리합니다. 각 래퍼 클래스는 컬렉션 클래스를 확장(상속)하고, 내부적으로 Lazy Loading, 관련된 데이터 로딩, 변경 추적 등의 기능을 추가하여 엔터티 간의 관계를 처리합니다.</p>
</blockquote>