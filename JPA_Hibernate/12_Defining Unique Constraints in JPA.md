<p>기본키 제약과 유니크 제약이 어떤 차이를 가지고 있는지 알아보고, JPA의 @Column(unique=true)과 @UniqueConstraint 을 살펴본다. </p>
<h2 id="unique-constraints">Unique Constraints</h2>
<p>유니크 키는 테이블의 단일 또는 복수 컬럼으로서 레코드를 식별가능하게 한다. 기본키와 유니크 키는 모두 단일 컬럼 또는 컬럼 세트에 대한 유일성(uniqueness)을 보장한다.</p>
<p>유니크 제약은 컬럼 또는 컬럼 조합 안의 데이터가 각 행마다 모두 유일(unique)함을 보장한다. 기본 키는 묵시적 유니크 제약으로 기능한다. </p>
<p>JPA의 @Column(unique=true)과 @UniqueConstraint는 스키마 생성 프로세스와 제약 자동 생성으로 번역되는 어노테이션이다.</p>
<h2 id="set-up-an-entity">Set Up an Entity</h2>
<p>예시) Person 엔터티 생성</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token annotation punctuation">@Table</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Person</span> <span class="token keyword">implements</span> <span class="token class-name">Serializable</span> <span class="token punctuation">{</span>
    <span class="token annotation punctuation">@Id</span>
    <span class="token annotation punctuation">@GeneratedValue</span>
    <span class="token keyword">private</span> <span class="token class-name">Long</span> id<span class="token punctuation">;</span>  
    <span class="token keyword">private</span> <span class="token class-name">String</span> name<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> password<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> email<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">Long</span> personNumber<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">Boolean</span> isActive<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> securityNumber<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> departmentCode<span class="token punctuation">;</span>
    <span class="token annotation punctuation">@JoinColumn</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"addressId"</span><span class="token punctuation">,</span> referencedColumnName <span class="token operator">=</span> <span class="token string">"id"</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">Address</span> address<span class="token punctuation">;</span>
   <span class="token comment">//getters and setters</span>
 <span class="token punctuation">}</span></code></pre>
<p>address 필드는 Adress 엔터티를 참조하는 필드이다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token annotation punctuation">@Table</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Address</span> <span class="token keyword">implements</span> <span class="token class-name">Serializable</span> <span class="token punctuation">{</span>
    <span class="token annotation punctuation">@Id</span>
    <span class="token annotation punctuation">@GeneratedValue</span>
    <span class="token keyword">private</span> <span class="token class-name">Long</span> id<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> streetAddress<span class="token punctuation">;</span>
    <span class="token comment">//getters and setters</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p>@JoinColumn 어노테이션을 통해 어떤 컬럼을 외래키로 사용할 것인지 지정하는 것입니다. 위의 코드에서는 address 필드가 Address 엔터티의 id 프로퍼티를 참조하는 외래키로 사용되도록 설정되어 있습니다.</p>
</blockquote>
<h2 id="column-constraints">Column Constraints</h2>
<p>Person 엔터티에 id 기본키 외에도 중복 값이 없는 PersonNumber를 가지고 있다고 가정하자. 유니크 제약을 사용하여 이 필드에 중복값이 들어오지 못하게 설정한다. </p>
<p>Column 어노테이션 타입은 persistent 프로퍼티 또는 필드를 데이터베이스 열에 맵핑하는데, 아래와 같이 정의된다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Target</span><span class="token punctuation">(</span>value<span class="token operator">=</span><span class="token punctuation">{</span>METHOD<span class="token punctuation">,</span>FIELD<span class="token punctuation">}</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@Retention</span><span class="token punctuation">(</span>value<span class="token operator">=</span>RUNTIME<span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token annotation punctuation">@interface</span> <span class="token class-name">Column</span> <span class="token punctuation">{</span>
    <span class="token keyword">boolean</span> unique<span class="token punctuation">;</span>
   <span class="token comment">//other elements</span>
 <span class="token punctuation">}</span></code></pre>
<p>unique 속성이 컬럼이 유니크 키인지 특정해준다.<br>
예시) 단일 필드에 대한 유니크 제약 정의</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Column</span><span class="token punctuation">(</span>unique<span class="token operator">=</span><span class="token boolean">true</span><span class="token punctuation">)</span>
<span class="token keyword">private</span> <span class="token class-name">Long</span> personNumber<span class="token punctuation">;</span></code></pre>
<blockquote>
<p>@interface : Java 언어에서 사용되는 특별한 키워드로, 어노테이션을 정의할 때 사용됩니다. 어노테이션은 프로그램 코드에 메타데이터를 추가하는 방법 중 하나입니다. 즉, 클래스, 메소드, 필드 등의 요소에 추가 정보를 부여하거나 표시할 때 사용됩니다.</p>
</blockquote>
<blockquote>
<p>@Target(value={METHOD,FIELD}): 어노테이션을 적용할 수 있는 대상(타겟)을 나타냅니다. 해당 어노테이션은 메소드와 필드에 적용할 수 있습니다.</p>
</blockquote>
<blockquote>
<p>@Retention(value=RUNTIME): 이 어노테이션은 어노테이션 정보를 언제까지 유지할 것인지를 나타냅니다. RUNTIME으로 설정되어 있으므로 런타임 시에도 어노테이션 정보가 유지됩니다. </p>
</blockquote>
<p>스키마 생성 프로세스를 실행하면 로그에서 검증할 수 있다.</p>
<pre><code class="language-sql"><span class="token punctuation">[</span>main<span class="token punctuation">]</span> DEBUG org<span class="token punctuation">.</span>hibernate<span class="token punctuation">.</span><span class="token keyword">SQL</span> <span class="token operator">-</span>
    <span class="token keyword">alter</span> <span class="token keyword">table</span> Person <span class="token keyword">add</span> <span class="token keyword">constraint</span> UK_d44q5lfa9xx370jv2k7tsgsqt <span class="token keyword">unique</span> <span class="token punctuation">(</span>personNumber<span class="token punctuation">)</span></code></pre>
<blockquote>
<p>UK_d44q5lfa9xx370jv2k7tsgsqt와 같은 제약조건 이름은 데이터베이스 시스템에서 자동으로 생성된 것입니다. 이 이름은 데이터베이스 시스템이 유니크 제약조건을 생성할 때 사용되는 내부적인 식별자이며, 보통은 시스템에 의해 자동으로 생성되는 경우가 많습니다.</p>
</blockquote>
<blockquote>
<p>Hibernate와 같은 ORM 도구가 데이터베이스 스키마를 관리할 때, 테이블의 제약조건 이름을 자동으로 생성하거나 지정할 수 있습니다. 이러한 이름은 데이터베이스 시스템의 특정 규칙을 따르거나, 랜덤한 문자열과 숫자의 조합을 사용하여 생성될 수 있습니다.</p>
</blockquote>
<h2 id="table-constraints">Table Constraints</h2>
<p>복합적인 유니크 키를 정의하기 위해 테이블에 제약을 추가한다. JPA에서는 @UniqueConstraint 어노테이션을 제공한다.</p>
<p>UniqueConstraint의 어노테이션 타입은 DDL 생성 시 유니크 제약이 포함되도록 특정한다. </p>
<pre><code class="language-java"><span class="token annotation punctuation">@Target</span><span class="token punctuation">(</span>value<span class="token operator">=</span><span class="token punctuation">{</span><span class="token punctuation">}</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@Retention</span><span class="token punctuation">(</span>value<span class="token operator">=</span>RUNTIME<span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token annotation punctuation">@interface</span> <span class="token class-name">UniqueConstraint</span> <span class="token punctuation">{</span>
    <span class="token class-name">String</span> <span class="token function">name</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">default</span> <span class="token string">""</span><span class="token punctuation">;</span>
    <span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token function">columnNames</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>String과 String[] 자료형의 name, columnNames은 제약조건을 지정하는 요소이다.<br>
Person 엔터티에서 personNumber와 isActive를 조합하여 중복되지 않는 복수의 컬럼 제약 조건을 만든다. @Table 어노테이션 아래 uniqueConstraints 속성으로 @UniqueConstraint 어노테이션을 대입한다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Table</span><span class="token punctuation">(</span>uniqueConstraints <span class="token operator">=</span> <span class="token punctuation">{</span> <span class="token annotation punctuation">@UniqueConstraint</span><span class="token punctuation">(</span>columnNames <span class="token operator">=</span> <span class="token punctuation">{</span> <span class="token string">"personNumber"</span><span class="token punctuation">,</span> <span class="token string">"isActive"</span> <span class="token punctuation">}</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">)</span></code></pre>
<blockquote>
<p>각 어노테이션은 미리 정의된 속성들을 가지고 있고, 이러한 속성들을 사용하여 어노테이션의 동작을 설정하거나 제어할 수 있습니다.</p>
<ul>
<li>
<p>@Table: 이 어노테이션은 클래스가 데이터베이스 테이블과 매핑되는 것을 나타냅니다.</p>
</li>
<li>
<p>uniqueConstraints: 이 속성은 해당 테이블에 유니크 제약조건(Unique Constraint)을 설정하는 역할을 합니다. 유니크 제약조건은 특정 열의 값들이 유일해야 함을 의미합니다.</p>
</li>
<li>
<p>@UniqueConstraint: 이 어노테이션은 유니크 제약조건을 정의하는 역할을 합니다. 여기서는 columnNames 속성을 사용하여 유니크 제약조건이 적용될 열의 이름을 지정합니다. 즉, "personNumber"와 "isActive" 두 열의 조합이 유일해야 합니다.</p>
</li>
</ul>
</blockquote>
<p>스키마 생성</p>
<pre><code class="language-sql"><span class="token punctuation">[</span>main<span class="token punctuation">]</span> DEBUG org<span class="token punctuation">.</span>hibernate<span class="token punctuation">.</span><span class="token keyword">SQL</span> <span class="token operator">-</span>
    <span class="token keyword">alter</span> <span class="token keyword">table</span> Person <span class="token keyword">add</span> <span class="token keyword">constraint</span> UK5e0bv5arhh7jjhsls27bmqp4a <span class="token keyword">unique</span> <span class="token punctuation">(</span>personNumber<span class="token punctuation">,</span> isActive<span class="token punctuation">)</span></code></pre>
<p>제약 조건에 이름을 지정할 수도 있다.</p>
<pre><code class="language-jpa">@Table(uniqueConstraints = { @UniqueConstraint(name = "UniqueNumberAndStatus", columnNames = { "personNumber", "isActive" }) })</code></pre>
<p>스키마 생성</p>
<pre><code class="language-null">[main] DEBUG org.hibernate.SQL -
    alter table Person add constraint UniqueNumberAndStatus unique (personNumber, isActive)</code></pre>
<p>예시) 단일 엔터티에 다수의 유니크 제약조건을 부여 </p>
<pre><code class="language-null">@Table(uniqueConstraints = {
   @UniqueConstraint(name = "UniqueNumberAndStatus", columnNames = {"personNumber", "isActive"}),
   @UniqueConstraint(name = "UniqueSecurityAndDepartment", columnNames = {"securityNumber", "departmentCode"})})</code></pre>
<p>스키마 생성 결과</p>
<pre><code class="language-sql"><span class="token punctuation">[</span>main<span class="token punctuation">]</span> DEBUG org<span class="token punctuation">.</span>hibernate<span class="token punctuation">.</span><span class="token keyword">SQL</span> <span class="token operator">-</span>
    <span class="token keyword">alter</span> <span class="token keyword">table</span> Person <span class="token keyword">add</span> <span class="token keyword">constraint</span> UniqueNumberAndStatus <span class="token keyword">unique</span> <span class="token punctuation">(</span>personNumber<span class="token punctuation">,</span> isActive<span class="token punctuation">)</span>
<span class="token punctuation">[</span>main<span class="token punctuation">]</span> DEBUG org<span class="token punctuation">.</span>hibernate<span class="token punctuation">.</span><span class="token keyword">SQL</span> <span class="token operator">-</span>
   <span class="token keyword">alter</span> <span class="token keyword">table</span> Person <span class="token keyword">add</span> <span class="token keyword">constraint</span> UniqueSecurityAndDepartment <span class="token keyword">unique</span> <span class="token punctuation">(</span>securityNumber<span class="token punctuation">,</span> departmentCode<span class="token punctuation">)</span></code></pre>
<h2 id="unique-constraints-on-a-referenced-table-column">Unique Constraints on a Referenced Table Column</h2>
<p>Person 엔터티에는 Address 엔터티를 참조하는 address 필드가 있다. 이 필드에 유니크 제약을 부여하자</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Column</span><span class="token punctuation">(</span>unique <span class="token operator">=</span> <span class="token boolean">true</span><span class="token punctuation">)</span>
<span class="token keyword">private</span> <span class="token class-name">Address</span> address<span class="token punctuation">;</span></code></pre>
<pre><code class="language-sql"><span class="token punctuation">[</span>main<span class="token punctuation">]</span> DEBUG org<span class="token punctuation">.</span>hibernate<span class="token punctuation">.</span><span class="token keyword">SQL</span> <span class="token operator">-</span>
   <span class="token keyword">alter</span> <span class="token keyword">table</span> Person <span class="token keyword">add</span> <span class="token keyword">constraint</span> UK_7xo3hsusabfaw1373oox9uqoe <span class="token keyword">unique</span> <span class="token punctuation">(</span>address<span class="token punctuation">)</span></code></pre>
<p>컬럼 조합에 유니크 제약 부여하기. personNumber와 address에 유니크 제약을 부여한 후 uniqueConstraint 배열에 추가한다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token annotation punctuation">@Table</span><span class="token punctuation">(</span>uniqueConstraints <span class="token operator">=</span> 
  <span class="token punctuation">{</span> <span class="token comment">//other constraints</span>
  <span class="token annotation punctuation">@UniqueConstraint</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"UniqueNumberAndAddress"</span><span class="token punctuation">,</span> columnNames <span class="token operator">=</span> <span class="token punctuation">{</span> <span class="token string">"personNumber"</span><span class="token punctuation">,</span> <span class="token string">"address"</span> <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">}</span><span class="token punctuation">)</span></code></pre>
<pre><code class="language-sql"><span class="token punctuation">[</span>main<span class="token punctuation">]</span> DEBUG org<span class="token punctuation">.</span>hibernate<span class="token punctuation">.</span><span class="token keyword">SQL</span> <span class="token operator">-</span>
    <span class="token keyword">alter</span> <span class="token keyword">table</span> Person <span class="token keyword">add</span> <span class="token keyword">constraint</span> UniqueNumberAndAddress <span class="token keyword">unique</span> <span class="token punctuation">(</span>personNumber<span class="token punctuation">,</span> address<span class="token punctuation">)</span></code></pre>