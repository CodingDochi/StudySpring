<p>SQL 테이블에 직접 정의하는 것과 마찬가지로 엔터티에 기본 속성을 부여하겠다.</p>
<h2 id="엔터티를-생성하는-동안">엔터티를 생성하는 동안</h2>
<p>default 컬럼 값을 지정하는 첫번째 방법은 엔터티의 속성값으로 직접 부여하는 것이다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">User</span> <span class="token punctuation">{</span>
    <span class="token annotation punctuation">@Id</span>
    <span class="token keyword">private</span> <span class="token class-name">Long</span> id<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> firstName <span class="token operator">=</span> <span class="token string">"John Snow"</span><span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">Integer</span> age <span class="token operator">=</span> <span class="token number">25</span><span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">Boolean</span> locked <span class="token operator">=</span> <span class="token boolean">false</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이제 new 연산자로 새 엔터티를 생성할 때마다 우리가 부여한 기본 값이 지정될 것이다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">void</span> <span class="token function">saveUser_shouldSaveWithDefaultFieldValues</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">User</span> user <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">User</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    user <span class="token operator">=</span> userRepository<span class="token punctuation">.</span><span class="token function">save</span><span class="token punctuation">(</span>user<span class="token punctuation">)</span><span class="token punctuation">;</span>
    
    <span class="token function">assertEquals</span><span class="token punctuation">(</span>user<span class="token punctuation">.</span><span class="token function">getName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token string">"John Snow"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">assertEquals</span><span class="token punctuation">(</span>user<span class="token punctuation">.</span><span class="token function">getAge</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token number">25</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">assertFalse</span><span class="token punctuation">(</span>user<span class="token punctuation">.</span><span class="token function">getLocked</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이 방식에는 한가지 단점이 있다.<br>
SQL 테이블 정의에는 default 값이 지정되어 있지 않다.</p>
<pre><code class="language-java">create table user
<span class="token punctuation">(</span>
    id     bigint not <span class="token keyword">null</span> constraint user_pkey primary key<span class="token punctuation">,</span>
    name   <span class="token function">varchar</span><span class="token punctuation">(</span><span class="token number">255</span><span class="token punctuation">)</span><span class="token punctuation">,</span>
    age    integer<span class="token punctuation">,</span>
    locked <span class="token keyword">boolean</span>
<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>그러므로 null로 오버라이드 하면 엔터티는 에러 없이 저장될 것이다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">void</span> <span class="token function">saveUser_shouldSaveWithNullName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">User</span> user <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">User</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    user<span class="token punctuation">.</span><span class="token function">setName</span><span class="token punctuation">(</span><span class="token keyword">null</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    user<span class="token punctuation">.</span><span class="token function">setAge</span><span class="token punctuation">(</span><span class="token keyword">null</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    user<span class="token punctuation">.</span><span class="token function">setLocked</span><span class="token punctuation">(</span><span class="token keyword">null</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    user <span class="token operator">=</span> userRepository<span class="token punctuation">.</span><span class="token function">save</span><span class="token punctuation">(</span>user<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token function">assertNull</span><span class="token punctuation">(</span>user<span class="token punctuation">.</span><span class="token function">getName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">assertNull</span><span class="token punctuation">(</span>user<span class="token punctuation">.</span><span class="token function">getAge</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">assertNull</span><span class="token punctuation">(</span>user<span class="token punctuation">.</span><span class="token function">getLocked</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="스키마-정의에서">스키마 정의에서</h2>
<p>SQL 테이블 정의에서 직접 default 값을 생성하려면 @Column 어노테이션을 사용하여 columnDefinition 매개변수에 지정할 수 있다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">User</span> <span class="token punctuation">{</span>
    <span class="token annotation punctuation">@Id</span>
    <span class="token class-name">Long</span> id<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Column</span><span class="token punctuation">(</span>columnDefinition <span class="token operator">=</span> <span class="token string">"varchar(255) default 'John Snow'"</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> name<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Column</span><span class="token punctuation">(</span>columnDefinition <span class="token operator">=</span> <span class="token string">"integer default 25"</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">Integer</span> age<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Column</span><span class="token punctuation">(</span>columnDefinition <span class="token operator">=</span> <span class="token string">"boolean default false"</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">Boolean</span> locked<span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이 메소드를 사용하면 SQL 테이블 정의에 default 값이 표시된다.</p>
<pre><code class="language-java">create table user
<span class="token punctuation">(</span>
    id     bigint not <span class="token keyword">null</span> constraint user_pkey primary key<span class="token punctuation">,</span>
    name   <span class="token function">varchar</span><span class="token punctuation">(</span><span class="token number">255</span><span class="token punctuation">)</span> <span class="token keyword">default</span> <span class="token string">'John Snow'</span><span class="token punctuation">,</span>
    age    integer      <span class="token keyword">default</span> <span class="token number">35</span><span class="token punctuation">,</span>
    locked <span class="token keyword">boolean</span>      <span class="token keyword">default</span> <span class="token boolean">false</span>
<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">void</span> <span class="token function">saveUser_shouldSaveWithDefaultSqlValues</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">User</span> user <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">User</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    user <span class="token operator">=</span> userRepository<span class="token punctuation">.</span><span class="token function">save</span><span class="token punctuation">(</span>user<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token function">assertEquals</span><span class="token punctuation">(</span>user<span class="token punctuation">.</span><span class="token function">getName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token string">"John Snow"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">assertEquals</span><span class="token punctuation">(</span>user<span class="token punctuation">.</span><span class="token function">getAge</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token number">25</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">assertFalse</span><span class="token punctuation">(</span>user<span class="token punctuation">.</span><span class="token function">getLocked</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이 메소드를 사용하면 엔터티를 처음 저장할 때 column을 null로 지정할 수 없다. 아무 값도 제공하지 않는다면 자동적으로 default 값이 지정된다. </p>