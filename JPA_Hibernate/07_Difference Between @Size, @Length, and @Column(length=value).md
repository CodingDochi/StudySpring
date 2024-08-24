<p>JSR-330의 @Size 어노테이션, Hibernate의 @Length 어노테이션, 그리고 JPA @Column어노테이션의 length 속성에 대해 알아보자. </p>
<blockquote>
<p>JSR(Java Specification Request) : 자바 기술 표준 규격</p>
</blockquote>
<blockquote>
<p>JSR-330 : 이 JSR은 자바 의존성 주입(Dependency Injection)을 위한 표준화된 방법을 정의하고 프레임워크 간의 호환성과 개발자들의 일관된 코드 작성을 촉진하기 위해 개발되었습니다.</p>
<p>주로 javax.inject 패키지를 통해 정의된 어노테이션들로 구성되어 있습니다. </p>
<ul>
<li>@Inject: 의존성 주입을 요청하는 필드, 생성자, 메서드에 사용되며, 해당 자원이 자동으로 주입되도록 지정합니다.</li>
<li>@Qualifier: 동일한 타입의 여러 의존성을 구분하기 위해 사용됩니다.</li>
<li>@Named: 특정 이름의 의존성을 주입하는 데 사용됩니다.</li>
<li>@Singleton: 싱글톤 스코프의 빈을 정의할 때 사용됩니다.</li>
</ul>
</blockquote>
<h2 id="origins">Origins</h2>
<p>필드의 크기를 검증할 때 @Size, @Length 어노테이션을 사용할 수 있다. 전자는 JPA 표준 어노테이션이고 후자는 Hibernate에 한정된 것이다. JPA 어노테이션인 @Column은 DDL 명령문을 조정하기 위해 사용한다.</p>
<h2 id="size">@Size</h2>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">User</span> <span class="token punctuation">{</span>

    <span class="token comment">// ...</span>

    <span class="token annotation punctuation">@Size</span><span class="token punctuation">(</span>min <span class="token operator">=</span> <span class="token number">3</span><span class="token punctuation">,</span> max <span class="token operator">=</span> <span class="token number">15</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> middleName<span class="token punctuation">;</span>

    <span class="token comment">// ...</span>

<span class="token punctuation">}</span></code></pre>
<p>검증을 위해 빈(bean) validation 어노테이션인 @Size를 사용한다. middleName 프로퍼티에 @Size를 annotate 하여 min, max 속성들의 값을 검증한다.<br>
@Size는 JPA와 그 벤더(예를 들어 Hibernate)에 독립적인 bean을 만든다. 결과적으로 @Length보다 이식성이 좋다.</p>
<h2 id="length">@Length</h2>
<p>예시) lastName 필드를 @Length를 사용하여 범위 조정</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">User</span> <span class="token punctuation">{</span>

    <span class="token comment">// ...</span>
      
    <span class="token annotation punctuation">@Length</span><span class="token punctuation">(</span>min <span class="token operator">=</span> <span class="token number">3</span><span class="token punctuation">,</span> max <span class="token operator">=</span> <span class="token number">15</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> lastName<span class="token punctuation">;</span>

    <span class="token comment">// ...</span>

<span class="token punctuation">}</span></code></pre>
<h2 id="columnlengthvalue">@Column(length=value)</h2>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">User</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Column</span><span class="token punctuation">(</span>length <span class="token operator">=</span> <span class="token number">3</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> firstName<span class="token punctuation">;</span>

    <span class="token comment">// ...</span>

<span class="token punctuation">}</span></code></pre>
<p>데이터 베이스 열의 물리적 특성을 지정하기 위해 @Column을 사용한다. @Column의 length 속성을 이용해 열의 string 값 길이를 제한할 수 있다.<br>
위 코드는 VARCHAR(3)으로 컬럼을 생성하고, 이보다 긴 스트링 값을 삽입하려 하면 SQL 에러를 발생시킬 것이다.<br>
@Column은 테이블 열 속성을 지정할 때 사용할 뿐, 검증 기능을 제공하지는 않는다. 데이터베이스 열에 bean validation을 지정하고 싶다면 @Size를 같이 사용할 수 있다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">User</span> <span class="token punctuation">{</span>

    <span class="token comment">// ... </span>
    
    <span class="token annotation punctuation">@Column</span><span class="token punctuation">(</span>length <span class="token operator">=</span> <span class="token number">5</span><span class="token punctuation">)</span>
    <span class="token annotation punctuation">@Size</span><span class="token punctuation">(</span>min <span class="token operator">=</span> <span class="token number">3</span><span class="token punctuation">,</span> max <span class="token operator">=</span> <span class="token number">5</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> city<span class="token punctuation">;</span>

    <span class="token comment">// ...</span>

<span class="token punctuation">}</span></code></pre>