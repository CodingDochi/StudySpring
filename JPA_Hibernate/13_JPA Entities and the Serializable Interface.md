<h2 id="serializable-interface">Serializable Interface</h2>
<p>Serializable은 메소드나 상수가 없는 Marker Interface이다. 객체 직렬화는 자바 객체를 byte 스트림으로 변환하는 과정이다. 직렬화를 거쳐야 persistent 메모리에 이를 저장할 수 있다. 역직렬화는 이와 반대되는 작업이다. 직렬화 인터페이스를 구현하지 않는다면 java.io.NotSerializableException 예외가 발생한다. 직렬화는 RMI, JPA, EJB등 여러 기술에서 사용되고 있다. </p>
<blockquote>
<p>마커 인터페이스(Marker Interface)는 아무 메서드도 선언하지 않은 빈 인터페이스로서, 주로 어떤 특정한 행동 또는 속성을 가진 클래스임을 나타내기 위해 사용됩니다. Serializable 인터페이스는 아무 메서드도 가지고 있지 않지만, 클래스가 Serializable 인터페이스를 구현하면, 해당 클래스의 인스턴스는 직렬화 가능한 객체로 취급될 수 있습니다. 마커 인터페이스의 장점은 코드를 더 명확하고 의미 있는 방식으로 구조화할 수 있다는 것입니다. 또한 리플렉션(Reflection) 등을 통해 클래스의 특성을 동적으로 파악할 때 유용하게 사용될 수 있습니다.</p>
</blockquote>
<h2 id="jpa-specification">JPA Specification</h2>
<blockquote>
<p>If an entity instance is to be passed by value as a detached object (e.g., through a remote interface), the entity class must implement the <em>Serializable</em> interface.</p>
</blockquote>
<p>객체가 JVM 실행 영역을 벗어나려면 직렬화가 필요하다. 엔터티는 persistent 필드와 프로퍼티로 구성되어 있다. 사양은 엔터티의 필드가 자바 원시 타입이거나 serializable 타입 또는 사용자 정의 serializable 타입이어야 한다고 요구한다.<br>
<strong>기본 키는 원시 타입(단일 persistent 필드)이지만 복합 키는 직렬화되어야 한다.</strong> </p>
<p><img src="https://www.edureka.co/blog/wp-content/uploads/2019/08/Serialization-in-Java-Edureka-Picture-1-2.png"></p>
<p>예시) UserId를 복합 키로 가진 User 객체, H2 in-memory 데이터베이스 사용</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">User</span> <span class="token punctuation">{</span>
    <span class="token annotation punctuation">@EmbeddedId</span> <span class="token class-name">UserId</span> userId<span class="token punctuation">;</span>
    <span class="token class-name">String</span> email<span class="token punctuation">;</span>
    
    <span class="token comment">// constructors, getters and setters</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@Embeddable</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">UserId</span> <span class="token keyword">implements</span> <span class="token class-name">Serializable</span><span class="token punctuation">{</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> name<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> lastName<span class="token punctuation">;</span>
    
    <span class="token comment">// getters and setters</span>
<span class="token punctuation">}</span></code></pre>
<p>통합(integration)테스트 실행</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">givenUser_whenPersisted_thenOperationSuccessful</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">UserId</span> userId <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">UserId</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    userId<span class="token punctuation">.</span><span class="token function">setName</span><span class="token punctuation">(</span><span class="token string">"John"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    userId<span class="token punctuation">.</span><span class="token function">setLastName</span><span class="token punctuation">(</span><span class="token string">"Doe"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">User</span> user <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">User</span><span class="token punctuation">(</span>userId<span class="token punctuation">,</span> <span class="token string">"johndoe@gmail.com"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    entityManager<span class="token punctuation">.</span><span class="token function">persist</span><span class="token punctuation">(</span>user<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">User</span> userDb <span class="token operator">=</span> entityManager<span class="token punctuation">.</span><span class="token function">find</span><span class="token punctuation">(</span><span class="token class-name">User</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> userId<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">assertEquals</span><span class="token punctuation">(</span>userDb<span class="token punctuation">.</span>email<span class="token punctuation">,</span> <span class="token string">"johndoe@gmail.com"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>UserId 클래스가 Serializable 인터페이스를 구현하지 않았다면 <strong>MappingException</strong>를 발생시켰을 것이다.</p>
<h2 id="hibernate-joincolumn-annotation">Hibernate @JoinColumn Annotation</h2>
<p>Hibernate는 <strong>@JoinColumn</strong> 어노테이션에서 <strong>referencedColumnName</strong>을 사용할 때는 <strong>참조한 필드가 반드시 직렬화되어야 한다</strong>고 명시</p>
<p>User 클래스의 email이 String 필드가 아니라 독립적인 엔터티라 가정하자. 필드 type을 가진 Account 클래스를 추가하고 그것이 user를 참조하게 할 것이다. User 들은 각기 다른 type을 가진 복수의 account를 가질 수 있다. Account를 email과 맵핑할 것이다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">User</span> <span class="token punctuation">{</span>
    <span class="token annotation punctuation">@EmbeddedId</span> <span class="token keyword">private</span> <span class="token class-name">UserId</span> userId<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">Email</span> email<span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@Entity</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Email</span> <span class="token keyword">implements</span> <span class="token class-name">Serializable</span> <span class="token punctuation">{</span>
    <span class="token annotation punctuation">@Id</span>
    <span class="token keyword">private</span> <span class="token keyword">long</span> id<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> name<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> domain<span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@Entity</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Account</span> <span class="token punctuation">{</span>
    <span class="token annotation punctuation">@Id</span>
    <span class="token keyword">private</span> <span class="token keyword">long</span> id<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> type<span class="token punctuation">;</span>
    <span class="token annotation punctuation">@ManyToOne</span>
    <span class="token annotation punctuation">@JoinColumn</span><span class="token punctuation">(</span>referencedColumnName <span class="token operator">=</span> <span class="token string">"email"</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">User</span> user<span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>user에 대응하는 account 두개를 생성하고 email 객체로 쿼리하는 테스트를 작성</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">givenAssociation_whenPersisted_thenMultipleAccountsWillBeFoundByEmail</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token comment">// object creation </span>

    entityManager<span class="token punctuation">.</span><span class="token function">persist</span><span class="token punctuation">(</span>user<span class="token punctuation">)</span><span class="token punctuation">;</span>
    entityManager<span class="token punctuation">.</span><span class="token function">persist</span><span class="token punctuation">(</span>account<span class="token punctuation">)</span><span class="token punctuation">;</span>
    entityManager<span class="token punctuation">.</span><span class="token function">persist</span><span class="token punctuation">(</span>account2<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Account</span><span class="token punctuation">&gt;</span></span> userAccounts <span class="token operator">=</span> entityManager<span class="token punctuation">.</span><span class="token function">createQuery</span><span class="token punctuation">(</span><span class="token string">"select a from Account a join fetch a.user where a.user.email = :email"</span><span class="token punctuation">,</span> <span class="token class-name">Account</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token function">setParameter</span><span class="token punctuation">(</span><span class="token string">"email"</span><span class="token punctuation">,</span> email<span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token function">getResultList</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    
    <span class="token function">assertEquals</span><span class="token punctuation">(</span>userAccounts<span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<ul>
<li>a.user는 Account 엔터티의 user 필드를 뜻하며, user는 User 엔터티의 인스턴스입니다.</li>
<li>a.user.email은 User 엔터티의 email 필드를 나타내며, 이 필드는 Email 엔터티의 인스턴스입니다.</li>
<li>'where a.user.email = :email'은 쿼리 매개변수 :email과 같은 값을 가지는 a.user.email을 말합니다. 이는 User 엔터티의 email 필드를 조회 조건으로 사용합니다.</li>
<li>'setParameter("email", email)에 의해 쿼리 매개변수 :email은 email 변수로 설정되고, 이는 User 엔터티의 email 필드와 연관된 컬럼을 참조하는데 사용됩니다.</li>
<li>@JoinColumn(referencedColumnName = "email")은 Account 엔터티의 user 필드가 User 엔터티의 email 컬럼과 조인되는 것을 의미합니다.</li>
</ul>
</blockquote>
<p>H2 데이터베이스에서 user는 예약어이기 때문에 엔터티 이름으로 사용할 수 없다.<br>
Email 클래스가 Serializable 인터페이스를 구현하지 않으면 MappingException이 발생하겠지만 이번에는 <strong>“Could not determine type”</strong> 이라는 다소 애매한 메세지가 나타난다.</p>
<h2 id="exposing-entities-to-the-presentation-layer">Exposing Entities to the Presentation Layer</h2>
<p>HTTP 통신으로 객체를 전송할 때 흔히 DTO(data trasfer object)를 생성하여 내부 도메인 객체들을 외부 서비스들로부터 분리하려고 한다. presentation layer에 DTO 없이 엔터티를 직접 노출하려면 엔터티는 반드시 직렬화되어야 한다. </p>
<blockquote>
<p>Presentation Layer: 사용자와 상호작용하여 입력을 받고 결과를 표시하는 역할을 합니다. DTO를 통해 비즈니스 로직에서 필요한 데이터를 효율적으로 전달하며, DTO의 데이터를 기반으로 사용자 인터페이스를 구성합니다.</p>
</blockquote>
<blockquote>
<p>DTO (Data Transfer Object): Presentation Layer와 비즈니스 로직 간 데이터 흐름을 관리합니다. Presentation Layer에서 사용자 입력을 받아 DTO를 생성하고, DAO에서 조회한 데이터를 DTO로 변환하여 Presentation Layer로 전달합니다.</p>
</blockquote>
<blockquote>
<p>DAO (Data Access Object): 데이터베이스와의 상호작용을 추상화하여 데이터를 관리합니다. DTO를 활용하여 데이터를 조회하고 비즈니스 로직에 필요한 형태로 가공하여 반환합니다.</p>
</blockquote>
<blockquote>
<p>Serialization: DTO나 엔터티 클래스의 상태를 유지하거나 다른 시스템과의 데이터 교환을 위해 사용됩니다. DTO 객체나 엔터티 객체를 직렬화하여 Presentation Layer나 DAO에서 필요에 따라 사용할 수 있게 합니다.</p>
</blockquote>
<p>웹사이트 내 <strong>페이지 방문</strong> 가운데 <strong>user를 식별</strong>해야 할 때 <strong>연관 데이터를 HttpSession 객체에 저장</strong>한다. 클러스터 환경의 다른 웹서버로 세션 데이터를 전송하거나 정상적으로 종료할 때 <strong>웹서버는 세션 데이터를 디스크에 저장</strong>한다. 엔터티가 이 프로세스의 일부라면 반드시 <strong>직렬화</strong> 되어야 한다. 그렇지 않다면 NotSerializableException이 발생할 것이다.</p>