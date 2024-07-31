<p>예시에서 스프링 부트 애플리케이션을 사용할 것이다. </p>
<h2 id="dependencies">Dependencies</h2>
<p>예시) 의존성을 정의하는 pom.xml 파일</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>dependencies</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>dependency</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>groupId</span><span class="token punctuation">&gt;</span></span>org.springframework.boot<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>groupId</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>artifactId</span><span class="token punctuation">&gt;</span></span>spring-boot-starter-data-jpa<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>artifactId</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>dependency</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>dependency</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>groupId</span><span class="token punctuation">&gt;</span></span>org.springframework.boot<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>groupId</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>artifactId</span><span class="token punctuation">&gt;</span></span>spring-boot-starter-validation<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>artifactId</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>dependency</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>dependency</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>groupId</span><span class="token punctuation">&gt;</span></span>com.h2database<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>groupId</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>artifactId</span><span class="token punctuation">&gt;</span></span>h2<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>artifactId</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>dependency</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>dependencies</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>예시) 샘플 엔터티</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Item</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Id</span>
    <span class="token annotation punctuation">@GeneratedValue</span>
    <span class="token keyword">private</span> <span class="token class-name">Long</span> id<span class="token punctuation">;</span>

    <span class="token keyword">private</span> <span class="token class-name">BigDecimal</span> price<span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="the-notnull-annotation">The @NotNull Annotation</h2>
<p>@NotNull은 <strong>Bean Validation 사양</strong>에 정의되어 있으며 엔터티에 한정하지 않고 활용할 수 있다. 어떤 빈에서라도 @NotNull을 사용할 수 있다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Item</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Id</span>
    <span class="token annotation punctuation">@GeneratedValue</span>
    <span class="token keyword">private</span> <span class="token class-name">Long</span> id<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@NotNull</span>
    <span class="token keyword">private</span> <span class="token class-name">BigDecimal</span> price<span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>예시) null 가격을 item에 persist 하는 것을 시도</p>
<pre><code class="language-java"><span class="token annotation punctuation">@SpringBootTest</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ItemIntegrationTest</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> <span class="token class-name">ItemRepository</span> itemRepository<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">shouldNotAllowToPersistNullItemsPrice</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        itemRepository<span class="token punctuation">.</span><span class="token function">save</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Item</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p> <code>@Autowired</code>를 사용하면 스프링은 해당 필드를 자동으로 적절한 빈으로 주입해줍니다. 이를 통해 개발자는 불필요한 초기화나 레퍼런스 관리 등을 신경쓰지 않고 의존성을 주입받아 사용할 수 있게 됩니다.</p>
</blockquote>
<blockquote>
<p>코드에서 <code>itemRepository.save(new Item());</code>를 호출하면 새로운 Item 객체가 데이터베이스에 저장되려고 시도되는데, 이때 price 필드가 null인 상태로 저장하려는 시도입니다.</p>
</blockquote>
<p>이에 대해 Hibernate는 <strong>javax.validation.ConstraintViolationException</strong>을 발생시킨다.<br>
Hiberate가 <strong>SQL insert문을 트리거하지 않았다</strong>는 것에 주목<br>
<strong>Pre-Persist lifecycle 이벤트</strong>가 데이터베이스에 쿼리를 전송하기 전에 <strong>빈 검증(bean validation)을 트리거</strong> 했기 때문이다.</p>
<h2 id="스키마-생성">스키마 생성</h2>
<p>Hibernate가 <strong>데이터베이스에 스키마를 생성한다면</strong> 어떤 일이 일어날까<br>
예시) application.properties 파일에 프로퍼티 추가</p>
<pre><code class="language-java">spring<span class="token punctuation">.</span>jpa<span class="token punctuation">.</span>hibernate<span class="token punctuation">.</span>ddl<span class="token operator">-</span>auto<span class="token operator">=</span>create<span class="token operator">-</span>drop
spring<span class="token punctuation">.</span>jpa<span class="token punctuation">.</span>show<span class="token operator">-</span>sql<span class="token operator">=</span><span class="token boolean">true</span></code></pre>
<p>애플리케이션을 실행하면 DDL 명령문이 실행되는 것을 확인할 수 있다.</p>
<pre><code class="language-sql"><span class="token keyword">create</span> <span class="token keyword">table</span> item <span class="token punctuation">(</span>
   id <span class="token keyword">bigint</span> <span class="token operator">not</span> <span class="token boolean">null</span><span class="token punctuation">,</span>
    price <span class="token keyword">decimal</span><span class="token punctuation">(</span><span class="token number">19</span><span class="token punctuation">,</span><span class="token number">2</span><span class="token punctuation">)</span> <span class="token operator">not</span> <span class="token boolean">null</span><span class="token punctuation">,</span>
    <span class="token keyword">primary</span> <span class="token keyword">key</span> <span class="token punctuation">(</span>id<span class="token punctuation">)</span>
<span class="token punctuation">)</span></code></pre>
<p>Hibernate는 <strong>not null 제약을 price 열 정의에 추가</strong>했다.<br>
Hibernate가 엔터티에 적용된 bean validation을 <strong>DDL 스키마 메타데이터로 번역</strong>해주었기 때문이다.<br>
Hibernate의 이런 특성을 비활성화 하려면 아래와 같이 application.properties를 변경한다</p>
<pre><code class="language-java">spring<span class="token punctuation">.</span>jpa<span class="token punctuation">.</span>hibernate<span class="token punctuation">.</span>ddl<span class="token operator">-</span>auto<span class="token operator">=</span>create<span class="token operator">-</span>drop
spring<span class="token punctuation">.</span>jpa<span class="token punctuation">.</span>show<span class="token operator">-</span>sql<span class="token operator">=</span><span class="token boolean">true</span>
spring<span class="token punctuation">.</span>jpa<span class="token punctuation">.</span>properties<span class="token punctuation">.</span>hibernate<span class="token punctuation">.</span>validator<span class="token punctuation">.</span>apply_to_ddl<span class="token operator">=</span><span class="token boolean">false</span></code></pre>
<p>애플리케이션을 다시 실행하면 not null 제약이 적용되지 않은 DDL이 보인다</p>
<pre><code class="language-sql"><span class="token keyword">create</span> <span class="token keyword">table</span> item <span class="token punctuation">(</span>
   id <span class="token keyword">bigint</span> <span class="token operator">not</span> <span class="token boolean">null</span><span class="token punctuation">,</span>
    price <span class="token keyword">decimal</span><span class="token punctuation">(</span><span class="token number">19</span><span class="token punctuation">,</span><span class="token number">2</span><span class="token punctuation">)</span><span class="token punctuation">,</span>
    <span class="token keyword">primary</span> <span class="token keyword">key</span> <span class="token punctuation">(</span>id<span class="token punctuation">)</span>
<span class="token punctuation">)</span></code></pre>
<h2 id="the-columnnullable--false-annotation">The @Column(nullable = false) Annotation</h2>
<p>@Column은 <strong>JPA 사양</strong> 일부로 정의되었으며 DDL 스키마 메타데이터를 생성하는데 사용된다. 이는 Hibernate가 <strong>데이터베이스 스키마를 자동 생성하면, not null 제약이 특정 데이터베이스 컬럼에 적용</strong>한다는 말이다.</p>
<p>예시) Item 엔터티에 @Column(nullable = false) 적용</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Item</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Id</span>
    <span class="token annotation punctuation">@GeneratedValue</span>
    <span class="token keyword">private</span> <span class="token class-name">Long</span> id<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Column</span><span class="token punctuation">(</span>nullable <span class="token operator">=</span> <span class="token boolean">false</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">BigDecimal</span> price<span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>null price 값을 persist 시도하기</p>
<pre><code class="language-java"><span class="token annotation punctuation">@SpringBootTest</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ItemIntegrationTest</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> <span class="token class-name">ItemRepository</span> itemRepository<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">shouldNotAllowToPersistNullItemsPrice</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        itemRepository<span class="token punctuation">.</span><span class="token function">save</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Item</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>아래는 Hibernate의 출력</p>
<pre><code class="language-java"><span class="token class-name">Hibernate</span><span class="token operator">:</span> 
    
    create table item <span class="token punctuation">(</span>
       id bigint not <span class="token keyword">null</span><span class="token punctuation">,</span>
        price <span class="token function">decimal</span><span class="token punctuation">(</span><span class="token number">19</span><span class="token punctuation">,</span><span class="token number">2</span><span class="token punctuation">)</span> not <span class="token keyword">null</span><span class="token punctuation">,</span>
        primary key <span class="token punctuation">(</span>id<span class="token punctuation">)</span>
    <span class="token punctuation">)</span>

<span class="token punctuation">(</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span>

<span class="token class-name">Hibernate</span><span class="token operator">:</span> 
    insert 
    into
        item
        <span class="token punctuation">(</span>price<span class="token punctuation">,</span> id<span class="token punctuation">)</span> 
    values
        <span class="token punctuation">(</span><span class="token operator">?</span><span class="token punctuation">,</span> <span class="token operator">?</span><span class="token punctuation">)</span>
<span class="token number">2019</span><span class="token operator">-</span><span class="token number">11</span><span class="token operator">-</span><span class="token number">14</span> <span class="token number">13</span><span class="token operator">:</span><span class="token number">23</span><span class="token operator">:</span><span class="token number">03.000</span>  WARN <span class="token number">14580</span> <span class="token operator">--</span><span class="token operator">-</span> <span class="token punctuation">[</span>main<span class="token punctuation">]</span> o<span class="token punctuation">.</span>h<span class="token punctuation">.</span>engine<span class="token punctuation">.</span>jdbc<span class="token punctuation">.</span>spi<span class="token punctuation">.</span><span class="token class-name">SqlExceptionHelper</span>   <span class="token operator">:</span> 
SQL <span class="token class-name">Error</span><span class="token operator">:</span> <span class="token number">23502</span><span class="token punctuation">,</span> <span class="token class-name">SQLState</span><span class="token operator">:</span> <span class="token number">23502</span>
<span class="token number">2019</span><span class="token operator">-</span><span class="token number">11</span><span class="token operator">-</span><span class="token number">14</span> <span class="token number">13</span><span class="token operator">:</span><span class="token number">23</span><span class="token operator">:</span><span class="token number">03.000</span> ERROR <span class="token number">14580</span> <span class="token operator">--</span><span class="token operator">-</span> <span class="token punctuation">[</span>main<span class="token punctuation">]</span> o<span class="token punctuation">.</span>h<span class="token punctuation">.</span>engine<span class="token punctuation">.</span>jdbc<span class="token punctuation">.</span>spi<span class="token punctuation">.</span><span class="token class-name">SqlExceptionHelper</span>   <span class="token operator">:</span> 
NULL not allowed <span class="token keyword">for</span> column <span class="token string">"PRICE"</span></code></pre>
<p>예상대로 not null 제약을 price 열에 적용하여 생성하였다. 그리고 <strong>SQL INSERT 쿼리를 실행하여 기반 데이터베이스에서 에러를 발생</strong>시켰다.</p>
<h2 id="validation">Validation</h2>
<p>Hibernate는 상응하는 필드가 @Column(nullable=false)로 annotated 되었더라도 null 값 가능성에 대한 엔터티 validation을 수행할 수 있다.<br>
이를 활성화 시키기 위해 명시적으로 <strong>hibernate.check_nullability 프로퍼티를 true로 설정</strong>한다.</p>
<pre><code class="language-java">spring<span class="token punctuation">.</span>jpa<span class="token punctuation">.</span>show<span class="token operator">-</span>sql<span class="token operator">=</span><span class="token boolean">true</span>
spring<span class="token punctuation">.</span>jpa<span class="token punctuation">.</span>properties<span class="token punctuation">.</span>hibernate<span class="token punctuation">.</span>check_nullability<span class="token operator">=</span><span class="token boolean">true</span></code></pre>
<p>테스트 실행</p>
<pre><code class="language-java">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>dao<span class="token punctuation">.</span><span class="token class-name">DataIntegrityViolationException</span><span class="token operator">:</span> 
not<span class="token operator">-</span><span class="token keyword">null</span> property references a <span class="token keyword">null</span> or <span class="token keyword">transient</span> value <span class="token operator">:</span> com<span class="token punctuation">.</span>baeldung<span class="token punctuation">.</span>h2db<span class="token punctuation">.</span>springboot<span class="token punctuation">.</span>models<span class="token punctuation">.</span><span class="token class-name">Item</span><span class="token punctuation">.</span>price<span class="token punctuation">;</span> 
nested exception is org<span class="token punctuation">.</span>hibernate<span class="token punctuation">.</span><span class="token class-name">PropertyValueException</span><span class="token operator">:</span> 
not<span class="token operator">-</span><span class="token keyword">null</span> property references a <span class="token keyword">null</span> or <span class="token keyword">transient</span> value <span class="token operator">:</span> com<span class="token punctuation">.</span>baeldung<span class="token punctuation">.</span>h2db<span class="token punctuation">.</span>springboot<span class="token punctuation">.</span>models<span class="token punctuation">.</span><span class="token class-name">Item</span><span class="token punctuation">.</span>price</code></pre>
<p>이번에는 테스트케이스에서 <strong>org.hibernate.PropertyValueException</strong>을 발생시켰다. 이 케이스에서는 Hibernate가 <strong>데이터베이스에 INSERT 쿼리를 전송하지 않은 점</strong>에 주목.</p>