<h2 id="default-table-names">Default Table Names</h2>
<p>구현에 따라 JPA의 기본 테이블 이름은 달라진다. 예를 들어, Hibernate에서는 클래스 이름의 첫글자를 대문자로 변경하여 기본 테이블 이름으로 쓴다. 이는 ImplicitNamingStrategy 계약으로 결정된다.<br>
PhysicalNamingStrategy 인터페이스로 이를 바꿀 수 있다.</p>
<h2 id="using-table">Using @Table</h2>
<p>@jakarta.persistence.Table 어노테이션과 매개변수를 활용하여 손쉽게 커스텀 SQL 테이블 이름으로 지정할 수 있다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token annotation punctuation">@Table</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"ARTICLES"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Article</span> <span class="token punctuation">{</span>
    <span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>예시) 테이블 이름을 final 변수로 지정하는 방법</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token annotation punctuation">@Table</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token class-name">Article</span><span class="token punctuation">.</span>TABLE_NAME<span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Article</span> <span class="token punctuation">{</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token class-name">String</span> TABLE_NAME<span class="token operator">=</span> <span class="token string">"ARTICLES"</span><span class="token punctuation">;</span>
    <span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="overwriting-the-table-name-in-jpql-queries">Overwriting the Table Name in JPQL Queries</h2>
<p>기본 JPQL 에서는 엔터티 클래스 이름을 사용한다.</p>
<pre><code class="language-sql"><span class="token keyword">select</span> <span class="token operator">*</span> <span class="token keyword">from</span> Article</code></pre>
<p>@jakarta.persistence.Entity 어노테이션의 name 매개변수를 정의하여 변경할 수 있다. </p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"MyArticle"</span><span class="token punctuation">)</span></code></pre>
<p>그후 JPQL을 변경한다.</p>
<pre><code class="language-sql"><span class="token keyword">select</span> <span class="token operator">*</span> <span class="token keyword">from</span> MyArticle</code></pre>