<h2 id="data-model-context">Data Model Context</h2>
<p>예시) 회사 기본 정보와 담당자 정보가 저장되는 company 테이블 정의하기</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Company</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token class-name">Integer</span> id<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> name<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> address<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> phone<span class="token punctuation">;</span>
    
    <span class="token keyword">private</span> <span class="token class-name">String</span> contactFirstName<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> contactLastName<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> contactPhone<span class="token punctuation">;</span>
    
    <span class="token comment">// standard getters, setters</span>
<span class="token punctuation">}</span></code></pre>
<p>여기서 담당자를 별도의 클래스로 추상화해야 하지만, 세부정보에 대한 별도의 테이블을 만들고 싶지 않다면 다음과 같은 방법을 사용할 수 있다.</p>
<h2 id="embeddable">@Embeddable</h2>
<p>JPA @Embeddable 어노테이션은 다른 엔터티에 클래스가 포함된다는 것을 나타낸다.</p>
<p>예시) 담당자 세부 정보를 클래스에서 추상화 하기</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Embeddable</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ContactPerson</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token class-name">String</span> firstName<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> lastName<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> phone<span class="token punctuation">;</span>

    <span class="token comment">// standard getters, setters</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="embedded">@Embedded</h2>
<p>다른 엔터티로 타입을 포함시켜야 할 때 @Embedded 어노테이션을 사용한다</p>
<p>예시) 별개의 필드 대신 ContactPerson으로 변경</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Company</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Id</span>
    <span class="token annotation punctuation">@GeneratedValue</span>
    <span class="token keyword">private</span> <span class="token class-name">Integer</span> id<span class="token punctuation">;</span>

    <span class="token keyword">private</span> <span class="token class-name">String</span> name<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> address<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> phone<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Embedded</span>
    <span class="token keyword">private</span> <span class="token class-name">ContactPerson</span> contactPerson<span class="token punctuation">;</span>

    <span class="token comment">// standard getters, setters</span>
<span class="token punctuation">}</span></code></pre>
<p>담당자 세부정보를 포함하고, 단일 데이터베이스 테이블과 맵핑되는 Company 엔터티가 되었다.<br>
그러나 이러한 필드들을 데이터베이스 열에 어떻게 맵핑할 것인가?</p>
<h2 id="attributes-override">Attributes Override</h2>
<p>@AttributeOverrides 및 @AttributeOverride를 사용하여 포함된 유형의 열 속성을 재정의할 수 있다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Embedded</span>
<span class="token annotation punctuation">@AttributeOverrides</span><span class="token punctuation">(</span><span class="token punctuation">{</span>
  <span class="token annotation punctuation">@AttributeOverride</span><span class="token punctuation">(</span> name <span class="token operator">=</span> <span class="token string">"firstName"</span><span class="token punctuation">,</span> column <span class="token operator">=</span> <span class="token annotation punctuation">@Column</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"contact_first_name"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">,</span>
  <span class="token annotation punctuation">@AttributeOverride</span><span class="token punctuation">(</span> name <span class="token operator">=</span> <span class="token string">"lastName"</span><span class="token punctuation">,</span> column <span class="token operator">=</span> <span class="token annotation punctuation">@Column</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"contact_last_name"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">,</span>
  <span class="token annotation punctuation">@AttributeOverride</span><span class="token punctuation">(</span> name <span class="token operator">=</span> <span class="token string">"phone"</span><span class="token punctuation">,</span> column <span class="token operator">=</span> <span class="token annotation punctuation">@Column</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"contact_phone"</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
<span class="token punctuation">}</span><span class="token punctuation">)</span>
<span class="token keyword">private</span> <span class="token class-name">ContactPerson</span> contactPerson<span class="token punctuation">;</span></code></pre>