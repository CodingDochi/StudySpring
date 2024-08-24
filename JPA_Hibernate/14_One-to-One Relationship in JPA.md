<h2 id="descripton">Descripton</h2>
<p>사용자 관리 시스템을 빌드할 때 상사가 각 user에 메일 주소를 저장할 것을 요구한다고 가정해보자. user는 단 하나의 address를 가지고 address는 단 하나의 user에 묶일 것이다. user와 address 엔터티 사이는 일대일 관계의 예시이다.</p>
<h2 id="using-a-foreign-key">Using a Foreign Key</h2>
<p>일대일 관계에 기반한 외래키를 나타내는 ER다이어그램을 살펴보자.<br>
<img src="https://www.baeldung.com/wp-content/uploads/2018/12/1-1_FK.png"><br>
users의 addresss_id 컬럼은 address로 이어지는 외래키이다.</p>
<h3 id="jpa에서-외래키-구현하기">JPA에서 외래키 구현하기</h3>
<p>예시) User 클래스 생성하고 적절하게 annotate 하기</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token annotation punctuation">@Table</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"users"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">User</span> <span class="token punctuation">{</span>
    
    <span class="token annotation punctuation">@Id</span>
    <span class="token annotation punctuation">@GeneratedValue</span><span class="token punctuation">(</span>strategy <span class="token operator">=</span> <span class="token class-name">GenerationType</span><span class="token punctuation">.</span>AUTO<span class="token punctuation">)</span>
    <span class="token annotation punctuation">@Column</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"id"</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">Long</span> id<span class="token punctuation">;</span>
    <span class="token comment">//... </span>

    <span class="token annotation punctuation">@OneToOne</span><span class="token punctuation">(</span>cascade <span class="token operator">=</span> <span class="token class-name">CascadeType</span><span class="token punctuation">.</span>ALL<span class="token punctuation">)</span>
    <span class="token annotation punctuation">@JoinColumn</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"address_id"</span><span class="token punctuation">,</span> referencedColumnName <span class="token operator">=</span> <span class="token string">"id"</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">Address</span> address<span class="token punctuation">;</span>

    <span class="token comment">// ... getters and setters</span>
<span class="token punctuation">}</span></code></pre>
<p>@OneToOne 어노테이션을 관련된 엔터티 필드인 Address에 위치시킨 점에 주목.<br>
addresss 테이블의 기본키와 맵핑되는 users 내부 컬럼의 이름을 설정하기 위해 @JoinColumn을 사용한다. 우리가 이름을 제공하지 않으면 Hibernate는 기본 값을 고르기 위해 몇가지 규칙을 적용할 것이다. </p>
<blockquote>
<p>Default (only applies if single join column is being used): The same name as the primary key column of the referenced table.</p>
</blockquote>
<p>@JoinColumn 어노테이션은 외래 키 관계를 소유하는 쪽에만 배치한다. </p>
<p>예시) Address 엔터티</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token annotation punctuation">@Table</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"address"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Address</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Id</span>
    <span class="token annotation punctuation">@GeneratedValue</span><span class="token punctuation">(</span>strategy <span class="token operator">=</span> <span class="token class-name">GenerationType</span><span class="token punctuation">.</span>AUTO<span class="token punctuation">)</span>
    <span class="token annotation punctuation">@Column</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"id"</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">Long</span> id<span class="token punctuation">;</span>
    <span class="token comment">//...</span>

    <span class="token annotation punctuation">@OneToOne</span><span class="token punctuation">(</span>mappedBy <span class="token operator">=</span> <span class="token string">"address"</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">User</span> user<span class="token punctuation">;</span>

    <span class="token comment">//... getters and setters</span>
<span class="token punctuation">}</span></code></pre>
<p>User와 Address는 양방향 관계이기 때문에 @OneToOne 어노테이션을 사용한다.</p>
<h2 id="using-a-shared-primary-key">Using a Shared Primary Key</h2>
<h3 id="shared-primary-key로-모델링하기">Shared Primary Key로 모델링하기</h3>
<p><img src="https://www.baeldung.com/wp-content/uploads/2018/12/1-1-SK.png"><br>
새 컬럼 address_id를 생성하는 대신 address 테이블의 기본키 컬럼(user_id)을 users 테이블의 외래키로 표시한다.<br>
엔터티들 간에 일대일 관계가 성립하는 사실을 이용해 저장 공간을 최적화 했다.</p>
<h3 id="implementing-with-a-shared-primary-key-in-jpa">Implementing With a Shared Primary Key in JPA</h3>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token annotation punctuation">@Table</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"users"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">User</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Id</span>
    <span class="token annotation punctuation">@GeneratedValue</span><span class="token punctuation">(</span>strategy <span class="token operator">=</span> <span class="token class-name">GenerationType</span><span class="token punctuation">.</span>AUTO<span class="token punctuation">)</span>
    <span class="token annotation punctuation">@Column</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"id"</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">Long</span> id<span class="token punctuation">;</span>

    <span class="token comment">//...</span>

    <span class="token annotation punctuation">@OneToOne</span><span class="token punctuation">(</span>mappedBy <span class="token operator">=</span> <span class="token string">"user"</span><span class="token punctuation">,</span> cascade <span class="token operator">=</span> <span class="token class-name">CascadeType</span><span class="token punctuation">.</span>ALL<span class="token punctuation">)</span>
    <span class="token annotation punctuation">@PrimaryKeyJoinColumn</span>
    <span class="token keyword">private</span> <span class="token class-name">Address</span> address<span class="token punctuation">;</span>

    <span class="token comment">//... getters and setters</span>
<span class="token punctuation">}</span></code></pre>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token annotation punctuation">@Table</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"address"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Address</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Id</span>
    <span class="token annotation punctuation">@Column</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"user_id"</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">Long</span> id<span class="token punctuation">;</span>

    <span class="token comment">//...</span>

    <span class="token annotation punctuation">@OneToOne</span>
    <span class="token annotation punctuation">@MapsId</span>
    <span class="token annotation punctuation">@JoinColumn</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"user_id"</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">User</span> user<span class="token punctuation">;</span>
   
    <span class="token comment">//... getters and setters</span>
<span class="token punctuation">}</span></code></pre>
<p>이제 address 테이블에 외래키가 표시되기 때문에 mappedBy 속성은 User 클래스로 옮겨왔다.  Address엔터티와 관계된 외래키로서 쓰이는 User엔터티의 기본키를 가리키는 @PrimaryKeyJoinColumn 어노테이션을 덧붙였다. Address클래스 내부에 @Id를 정의할 필요가 있다. 이것은 user_id 컬럼을 참조하며, 더이상 @GeneratedValue 어노테이션을 사용하지 않는다는 점에 주목. User를 참조하는 필드 위에 User엔터티로부터 복사된 기본키 값을 가리키는 @MapsId 어노테이션을 추가했다.</p>
<h2 id="using-a-join-table">Using a Join Table</h2>
<p>일대일 맵핑은 선택적, 필수적 이 두가지 타입으로 나뉜다. 지금까지 우리는 필수적인 관계만 살펴봤다. 이제 우리 직원들이 워크스테이션과 연결된다고 상상해 본다. 일대일이지만 때로는 직원에게 워크스테이션이 없을 수도 있고 그 반대일 수도 있다.</p>
<h3 id="join-table로-모델링하기">Join Table로 모델링하기</h3>
<p>우리가 방금 논의한 전략은 선택적인 관계를 다루기 위해 null 값을 컬럼에 부여하도록 강제한다. 조인 테이블을 고려할 때, 다대다 관계를 떠올리는 게 전형적이지만, 이런 케이스에서 조인 테이블을 사용하는 것은 null 값을 제거하도록 도와준다.<br>
<img src="https://www.baeldung.com/wp-content/uploads/2018/12/1-1-JT.png"><br>
이제 관계를 생성할 때마다 emp_workstation 테이블에 새로운 entry(레코드)를 만들고 null을 방지할 것입니다.</p>
<h3 id="implementing-with-a-join-table-in-jpa">Implementing With a Join Table in JPA</h3>
<p>앞서의 예시에서 우리는 @JoinColumn을 사용했으나 지금은 @JoinTable을 사용할 것이다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token annotation punctuation">@Table</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"employee"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Employee</span> <span class="token punctuation">{</span>
    <span class="token annotation punctuation">@Id</span>
    <span class="token annotation punctuation">@GeneratedValue</span><span class="token punctuation">(</span>strategy <span class="token operator">=</span> <span class="token class-name">GenerationType</span><span class="token punctuation">.</span>AUTO<span class="token punctuation">)</span>
    <span class="token annotation punctuation">@Column</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"id"</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">Long</span> id<span class="token punctuation">;</span>

    <span class="token comment">//...</span>

    <span class="token annotation punctuation">@OneToOne</span><span class="token punctuation">(</span>cascade <span class="token operator">=</span> <span class="token class-name">CascadeType</span><span class="token punctuation">.</span>ALL<span class="token punctuation">)</span>
    <span class="token annotation punctuation">@JoinTable</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"emp_workstation"</span><span class="token punctuation">,</span> 
      joinColumns <span class="token operator">=</span> 
        <span class="token punctuation">{</span> <span class="token annotation punctuation">@JoinColumn</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"employee_id"</span><span class="token punctuation">,</span> referencedColumnName <span class="token operator">=</span> <span class="token string">"id"</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">,</span>
      inverseJoinColumns <span class="token operator">=</span> 
        <span class="token punctuation">{</span> <span class="token annotation punctuation">@JoinColumn</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"workstation_id"</span><span class="token punctuation">,</span> referencedColumnName <span class="token operator">=</span> <span class="token string">"id"</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">WorkStation</span> workStation<span class="token punctuation">;</span>

    <span class="token comment">//... getters and setters</span>
<span class="token punctuation">}</span></code></pre>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token annotation punctuation">@Table</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"workstation"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WorkStation</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Id</span>
    <span class="token annotation punctuation">@GeneratedValue</span><span class="token punctuation">(</span>strategy <span class="token operator">=</span> <span class="token class-name">GenerationType</span><span class="token punctuation">.</span>AUTO<span class="token punctuation">)</span>
    <span class="token annotation punctuation">@Column</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"id"</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">Long</span> id<span class="token punctuation">;</span>

    <span class="token comment">//...</span>

    <span class="token annotation punctuation">@OneToOne</span><span class="token punctuation">(</span>mappedBy <span class="token operator">=</span> <span class="token string">"workStation"</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">Employee</span> employee<span class="token punctuation">;</span>

    <span class="token comment">//... getters and setters</span>
<span class="token punctuation">}</span></code></pre>
<p>@JoinTable은 관계를 유지하는 동안 Hibernate가 조인테이블 전략을 사용하도록 지시한다. 우리가 JoinTable 어노테이션을 Employee 위에 사용했기 때문에 Employee가 이 관계의 소유자가 된다.</p>