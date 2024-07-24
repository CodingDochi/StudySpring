<p>@Basic 어노테이션을 살펴보고 @Basic과 @Column JPA 어노테이션의 차이를 논의할 것이다.</p>
<h2 id="basic-types">Basic Types</h2>
<p>JPA는 엔터티의 persistable한 필드 타입으로 다양한 Java 자료형을 지원한다. Basic 타입은 데이터베이스의 열에 직접 맵핑된다. 자바 원시 자료형과 그들의 래퍼(Wrapper) 클래스들, String, java.math.BigInteger 및 java.math.BigDecimal, 다양한 date-time 클래스들, enum, 그리고 java.io.Serializable을 구현하는 모든 타입을 포함한다. 여느 ORM 벤더와 마찬가지로 Hibernate도 컬럼의 특정org.hibernate.type.Type을 resolve 하기 위해 Basic 타입 레지스트리를 유지한다. </p>
<blockquote>
<p>Basic Type Registry<br>
ORM은 자바 객체와 관계형 데이터베이스 간의 데이터를 변환하고 매핑하는 작업을 처리해야 합니다. 이때 자바의 기본 데이터 타입과 데이터베이스의 데이터 타입 간의 매핑은 중요한 역할을 합니다. 예를 들어, 자바의 int 타입을 데이터베이스의 정수 타입으로 매핑하거나, 자바의 String 타입을 데이터베이스의 VARCHAR 타입으로 매핑하는 등의 작업이 필요합니다.</p>
<p>이때 베이직 타입 레지스트리는 다음과 같은 역할을 수행합니다:</p>
<ul>
<li>
<p>타입 변환: 자바의 기본 데이터 타입과 데이터베이스의 데이터 타입 간의 변환 작업을 관리합니다. 이를 통해 객체와 데이터베이스 간의 데이터 일관성을 유지하며, 데이터 변환이 필요한 경우에 적절한 변환을 수행합니다.</p>
</li>
<li>
<p>매핑 정보 제공: 각 데이터 타입에 대한 매핑 정보를 제공하여 어떤 자바 타입이나 클래스가 어떤 데이터베이스 데이터 타입과 매핑되는지를 정의합니다.</p>
</li>
<li>
<p>사용자 정의 타입 지원: 사용자가 직접 정의한 커스텀 데이터 타입을 지원하고 매핑할 수 있도록 합니다.</p>
</li>
</ul>
</blockquote>
<h2 id="basic">@Basic</h2>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Course</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Basic</span>
    <span class="token annotation punctuation">@Id</span>
    <span class="token keyword">private</span> <span class="token keyword">int</span> id<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Basic</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> name<span class="token punctuation">;</span>
    <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
<span class="token punctuation">}</span></code></pre>
<p>필드 또는 프로퍼티(property)에 붙은 @Basic 어노테이션은 이것이 Basic 타입이며 Hibernate가 영속성(persistence)을 위해 표준 맵핑을 사용해야 한다는 것을 의미한다.<br>
이것은 선택적인 어노테이션임에 주목. 위의 Course 엔터티를 아래처럼 쓸 수 있다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Course</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Id</span>
    <span class="token keyword">private</span> <span class="token keyword">int</span> id<span class="token punctuation">;</span>

    <span class="token keyword">private</span> <span class="token class-name">String</span> name<span class="token punctuation">;</span>
    <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
<span class="token punctuation">}</span></code></pre>
<p>@Basic 어노테이션을 사용하지 않더라도 default 값이 묵시적으로 가정되어 있다.</p>
<h2 id="언제-basic-어노테이션을-사용하는가">언제 @Basic 어노테이션을 사용하는가</h2>
<p>@Basic 어노테이션은 optional, fetch라는 두가지 속성을 가지고 있다.optional 속성은 표시된 필드 또는 프로퍼티가 null을 허용하는 여부에 대한 boolean 매개변수다. 기본값은 true. 필드가 원시 타입이 아니라면 nullable이 기본 값이라고 가정한다.<br>
fetch는 Fetch의 enumeration(열거형) 멤버를 받아서 필드 또는 프로퍼티가 느리게 로드(lazy load)되어야 하는지 또는 즉시 fetch(eager fetch) 해야 하는지 지정하는 속성이다. 기본 값은 FetchType.EAGER이지만 FetchType.LAZY로 수정하여 Lazy Loading을 허용할 수 있다.<br>
대형 직렬화 객체를 Basic 타입으로 맵핑한다면 필드에 액세스하는 비용이 증가하므로, 이러한 경우에는 Lazy Loading을 사용하는 것이 합리적</p>
<blockquote>
<p>FetchType.EAGER:</p>
<ul>
<li>EAGER는 "즉시 로딩"을 의미합니다.</li>
<li>엔터티를 조회할 때 연관된 다른 엔터티도 함께 조회됩니다.</li>
<li>예를 들어, 엔터티 A가 엔터티 B와 관계를 가지고 있고, A를 조회할 때 EAGER로 설정된다면, A를 조회할 때 B도 함께 조회됩니다.</li>
<li>EAGER는 데이터베이스의 조인 등을 이용해 한 번의 쿼리로 모든 연관 엔터티를 가져오므로, 데이터를 미리 로딩해야 하는 경우에 사용됩니다. 그러나 &gt; - 연관 엔터티의 수가 많아지면 성능 이슈가 발생할 수 있습니다.</li>
</ul>
</blockquote>
<blockquote>
<p>FetchType.LAZY:</p>
<ul>
<li>LAZY는 "지연 로딩"을 의미합니다.</li>
<li>엔터티를 조회할 때 연관된 다른 엔터티는 초기에 로딩되지 않습니다. 대신에 실제로 접근할 때 해당 연관 엔터티를 로딩합니다.</li>
<li>예를 들어, 엔터티 A가 엔터티 B와 관계를 가지고 있고, A를 조회할 때 LAZY로 설정된다면, A를 조회할 때 B는 로딩되지 않으며, B에 처음 접근할 때에만 실제로 데이터베이스에서 로딩됩니다.</li>
<li>LAZY는 초기 로딩 시에 필요하지 않은 데이터베이스 쿼리를 줄이고, 성능을 최적화할 수 있습니다. 그러나 연관 엔터티에 접근할 때 추가적인 쿼리가 발생할 수 있습니다.</li>
</ul>
</blockquote>
<p>예시) Course의 name으로 null을 허용하지 않고 Lazy Loading 하는 예시</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Course</span> <span class="token punctuation">{</span>
    
    <span class="token annotation punctuation">@Id</span>
    <span class="token keyword">private</span> <span class="token keyword">int</span> id<span class="token punctuation">;</span>
    
    <span class="token annotation punctuation">@Basic</span><span class="token punctuation">(</span>optional <span class="token operator">=</span> <span class="token boolean">false</span><span class="token punctuation">,</span> fetch <span class="token operator">=</span> <span class="token class-name">FetchType</span><span class="token punctuation">.</span>LAZY<span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> name<span class="token punctuation">;</span>
    <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
<span class="token punctuation">}</span></code></pre>
<p>기본값으로 설정되어 있는 optional과 fetch 매개변수를 변경하려면 @Basic 어노테이션을 명시적으로 사용해야 한다. </p>
<h2 id="jpa-basic-vs-column">JPA @Basic vs @Column</h2>
<ul>
<li>@Basic 어노테이션은 JPA 엔터티에 적용되며 @Column은 데이터베이스 열에 적용됨</li>
<li>엔터티 필드값의 null 허용여부를 결정할 때 @Basic 어노테이션은 optional 매개변수의 값을 정의하며, @Column 어노테이션은 nullable 속성으로 데이터베이스 열의 null 허용 여부를 결정함.</li>
<li>필드가 Lazy Load 되야한다고 지정하려면 @Basic 어노테이션을 사용해야 함.</li>
<li>@Column 어노테이션으로 맵핑된 데이터베이스 열의 name을 지정할 수 있음</li>
</ul>