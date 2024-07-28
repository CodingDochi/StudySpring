<p>자바에서는 Object.equals()와 Object.hasCode() 메소드를 오버라이딩하여 동등성(equality)의 정의를 변경할 수 있다. 이를 위해서는 몇가지 고려할 사항이 있다.</p>
<h2 id="collections">Collections</h2>
<p>객체들을 그룹화 하는 것이 Collection이다. grouping 로직에서는 해시 코드를 사용하고 있다. 모든 엔터티의 hashCode()메소드를 적용 반환 값이 같다면 의도하지 않은 행위를 초래할 수 있다. </p>
<h2 id="transient-entities">Transient Entities</h2>
<p>persistence context와 연결되지 않은 새 JPA 엔터티 객체는 Transient 상태에 있다. 이들은 @Id 값을 부여받지 못한다. Transient 상태의 객체 @Id는 모두 null이므로 hashCode() 또는 equals() 연산에서 id를 사용한다면 그들을 구별할 수 없다.</p>
<h2 id="subclasses">Subclasses</h2>
<p>getClass()메소드를 equals() 메소드에 포함시켜 객체들을 비교할 때 서브클래스들을 필터링할 수 있다. </p>
<pre><code class="language-java"><span class="token annotation punctuation">@Override</span>
<span class="token keyword">public</span> <span class="token keyword">boolean</span> <span class="token function">equals</span><span class="token punctuation">(</span><span class="token class-name">Object</span> o<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">if</span> <span class="token punctuation">(</span>o <span class="token operator">==</span> <span class="token keyword">null</span> <span class="token operator">||</span> <span class="token keyword">this</span><span class="token punctuation">.</span><span class="token function">getClass</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">!=</span> o<span class="token punctuation">.</span><span class="token function">getClass</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token boolean">false</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    <span class="token keyword">return</span> o<span class="token punctuation">.</span>id<span class="token punctuation">.</span><span class="token function">equals</span><span class="token punctuation">(</span><span class="token keyword">this</span><span class="token punctuation">.</span>id<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="defining-equality">Defining Equality</h2>
<p>객체를 어떻게 사용할 것인가에 따라 동등성을 정의하는 방식이 달라진다.</p>
<h3 id="no-overrides">No Overrides</h3>
<p>자바에서는 모든 객체들에 Object 클래스로부터 내려오는 equals(), hashCode() 메소드를 제공하기 때문에 오버라이드 하지 않아도 된다. 이 방식에서는 하나의 객체를 표현하는 두개의 분리된 인스턴스는 서로 동등하지 않다고 평가된다.</p>
<h3 id="using-a-database-key">Using a Database Key</h3>
<p>데이터베이스에 저장된 JPA 엔터티는 unique 값의 기본 키를 가지고 있다. 그러므로 기본키의 값이 같은 엔터티의 모든 인스턴스는 동일한 것으로 평가된다. 기본 키를 사용하여 hashCode()를 오버라이드 하거나, equals()를 오버라이드 하여 서브 클래스를 식별할 수 있다.</p>
<h3 id="using-a-business-key">Using a Business Key</h3>
<p>JPA 엔터티를 비교하기 위해 비즈니스 키를 사용하는 대안도 있다. 이 경우에는 기본키와 달리 엔터티의 member로서 key가 구성되어 있다. @Id를 사용하지 않더라도 unique한 이메일을 사용하여 기본키 또는 데이터베이스 생성 키를 활용한 식별법과 동일한 효과를 낼 수 있다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">EqualByBusinessKey</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token class-name">String</span> email<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token keyword">int</span> <span class="token function">hashCode</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> java<span class="token punctuation">.</span>util<span class="token punctuation">.</span><span class="token class-name">Objects</span><span class="token punctuation">.</span><span class="token function">hashCode</span><span class="token punctuation">(</span>email<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token keyword">boolean</span> <span class="token function">equals</span><span class="token punctuation">(</span><span class="token class-name">Object</span> obj<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token keyword">this</span> <span class="token operator">==</span> obj<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">return</span> <span class="token boolean">true</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span>obj <span class="token operator">==</span> <span class="token keyword">null</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">return</span> <span class="token boolean">false</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span>obj <span class="token keyword">instanceof</span> <span class="token class-name">EqualByBusinessKey</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token punctuation">(</span><span class="token punctuation">(</span><span class="token class-name">EqualByBusinessKey</span><span class="token punctuation">)</span> obj<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getEmail</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">equals</span><span class="token punctuation">(</span><span class="token function">getEmail</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
                <span class="token keyword">return</span> <span class="token boolean">true</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
        <span class="token punctuation">}</span>

        <span class="token keyword">return</span> <span class="token boolean">false</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>