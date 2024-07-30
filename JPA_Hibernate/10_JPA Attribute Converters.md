<p>JPA 3.0 부터 사용가능한 기능이다. JDBC 타입을 자바 클래스에 맵핑할 수 있다. 아래 예시에서는 Hibernate 6를 사용했다.</p>
<blockquote>
<p>JPA는 내부적으로 JDBC를 사용하여 데이터베이스와 통신합니다. 즉, JPA가 데이터베이스와의 연결, SQL 쿼리 실행, 트랜잭션 관리 등을 처리할 때 JDBC를 사용합니다. JPA는 개발자가 직접 JDBC 코드를 작성하지 않아도 데이터베이스와 상호작용할 수 있도록 추상화된 API를 제공하므로, 개발자는 더 간편하게 데이터베이스와 작업할 수 있습니다.</p>
</blockquote>
<h2 id="creating-a-converter">Creating a Converter</h2>
<p>예시) 나중에 변환될 PersonName 클래스</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">PersonName</span> <span class="token keyword">implements</span> <span class="token class-name">Serializable</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token class-name">String</span> name<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> surname<span class="token punctuation">;</span>

    <span class="token comment">// getters and setters</span>
<span class="token punctuation">}</span></code></pre>
<p>예시) @Entity 어노테이션을 사용하는 클래스에 PersonName 타입의 속성 추가</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"PersonTable"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Person</span> <span class="token punctuation">{</span>
   
    <span class="token keyword">private</span> <span class="token class-name">PersonName</span> personName<span class="token punctuation">;</span>

    <span class="token comment">//...</span>
<span class="token punctuation">}</span></code></pre>
<p>이제 PersonName 속성을 데이터베이스 열로 그리고 그와 반대로도 변환할 수 있는 컨버터를 생성해야 한다. @Converter 어노테이션을 사용하고 AttributeConverter 인터페이스를 구현함으로써 가능하다. 인터페이스의 매개변수에 데이터베이스 열과 클래스의 타입을 적용한다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Converter</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">PersonNameConverter</span> <span class="token keyword">implements</span> 
  <span class="token class-name">AttributeConverter</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">PersonName</span><span class="token punctuation">,</span> <span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token class-name">String</span> SEPARATOR <span class="token operator">=</span> <span class="token string">", "</span><span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">convertToDatabaseColumn</span><span class="token punctuation">(</span><span class="token class-name">PersonName</span> personName<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span>personName <span class="token operator">==</span> <span class="token keyword">null</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">return</span> <span class="token keyword">null</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        <span class="token class-name">StringBuilder</span> sb <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">StringBuilder</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span>personName<span class="token punctuation">.</span><span class="token function">getSurname</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">!=</span> <span class="token keyword">null</span> <span class="token operator">&amp;&amp;</span> <span class="token operator">!</span>personName<span class="token punctuation">.</span><span class="token function">getSurname</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
            <span class="token punctuation">.</span><span class="token function">isEmpty</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            sb<span class="token punctuation">.</span><span class="token function">append</span><span class="token punctuation">(</span>personName<span class="token punctuation">.</span><span class="token function">getSurname</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            sb<span class="token punctuation">.</span><span class="token function">append</span><span class="token punctuation">(</span>SEPARATOR<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        <span class="token keyword">if</span> <span class="token punctuation">(</span>personName<span class="token punctuation">.</span><span class="token function">getName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">!=</span> <span class="token keyword">null</span> 
          <span class="token operator">&amp;&amp;</span> <span class="token operator">!</span>personName<span class="token punctuation">.</span><span class="token function">getName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEmpty</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            sb<span class="token punctuation">.</span><span class="token function">append</span><span class="token punctuation">(</span>personName<span class="token punctuation">.</span><span class="token function">getName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        <span class="token keyword">return</span> sb<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token class-name">PersonName</span> <span class="token function">convertToEntityAttribute</span><span class="token punctuation">(</span><span class="token class-name">String</span> dbPersonName<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span>dbPersonName <span class="token operator">==</span> <span class="token keyword">null</span> <span class="token operator">||</span> dbPersonName<span class="token punctuation">.</span><span class="token function">isEmpty</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">return</span> <span class="token keyword">null</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        <span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> pieces <span class="token operator">=</span> dbPersonName<span class="token punctuation">.</span><span class="token function">split</span><span class="token punctuation">(</span>SEPARATOR<span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token keyword">if</span> <span class="token punctuation">(</span>pieces <span class="token operator">==</span> <span class="token keyword">null</span> <span class="token operator">||</span> pieces<span class="token punctuation">.</span>length <span class="token operator">==</span> <span class="token number">0</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">return</span> <span class="token keyword">null</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        <span class="token class-name">PersonName</span> personName <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">PersonName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>        
        <span class="token class-name">String</span> firstPiece <span class="token operator">=</span> <span class="token operator">!</span>pieces<span class="token punctuation">[</span><span class="token number">0</span><span class="token punctuation">]</span><span class="token punctuation">.</span><span class="token function">isEmpty</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">?</span> pieces<span class="token punctuation">[</span><span class="token number">0</span><span class="token punctuation">]</span> <span class="token operator">:</span> <span class="token keyword">null</span><span class="token punctuation">;</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span>dbPersonName<span class="token punctuation">.</span><span class="token function">contains</span><span class="token punctuation">(</span>SEPARATOR<span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            personName<span class="token punctuation">.</span><span class="token function">setSurname</span><span class="token punctuation">(</span>firstPiece<span class="token punctuation">)</span><span class="token punctuation">;</span>

            <span class="token keyword">if</span> <span class="token punctuation">(</span>pieces<span class="token punctuation">.</span>length <span class="token operator">&gt;=</span> <span class="token number">2</span> <span class="token operator">&amp;&amp;</span> pieces<span class="token punctuation">[</span><span class="token number">1</span><span class="token punctuation">]</span> <span class="token operator">!=</span> <span class="token keyword">null</span> 
              <span class="token operator">&amp;&amp;</span> <span class="token operator">!</span>pieces<span class="token punctuation">[</span><span class="token number">1</span><span class="token punctuation">]</span><span class="token punctuation">.</span><span class="token function">isEmpty</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
                personName<span class="token punctuation">.</span><span class="token function">setName</span><span class="token punctuation">(</span>pieces<span class="token punctuation">[</span><span class="token number">1</span><span class="token punctuation">]</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
        <span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token punctuation">{</span>
            personName<span class="token punctuation">.</span><span class="token function">setName</span><span class="token punctuation">(</span>firstPiece<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        <span class="token keyword">return</span> personName<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p>PersonNameConverter 클래스는 AttributeConverter&lt;PersonName, String&gt;를 구현하고 있으므로, PersonName 타입의 속성을 데이터베이스에 String 타입으로 변환하고, 데이터베이스에서 읽어온 String 타입을 다시 PersonName 타입으로 변환하는 작업을 수행합니다.</p>
<p>@Override 어노테이션은 상위 클래스나 인터페이스에 정의된 메서드를 재정의(오버라이드)한다는 것을 나타냅니다. 따라서 convertToDatabaseColumn 메서드는 AttributeConverter 인터페이스에 정의된 메서드인 convertToDatabaseColumn을 재정의하고 있습니다. 이 메서드는 PersonName 객체를 데이터베이스에 저장 가능한 형태로 변환하여 반환하는 역할을 합니다.</p>
</blockquote>
<h2 id="using-the-converter">Using the Converter</h2>
<p>예시) @Convert 어노테이션의 속성으로 사용하고 싶은 컨버터 클래스를 지정</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"PersonTable"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Person</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Convert</span><span class="token punctuation">(</span>converter <span class="token operator">=</span> <span class="token class-name">PersonNameConverter</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">PersonName</span> personName<span class="token punctuation">;</span>
    
    <span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>테스트를 위해 데이터베이스에 Person 객체를 저장</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">givenPersonName_whenSaving_thenNameAndSurnameConcat</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">String</span> name <span class="token operator">=</span> <span class="token string">"name"</span><span class="token punctuation">;</span>
    <span class="token class-name">String</span> surname <span class="token operator">=</span> <span class="token string">"surname"</span><span class="token punctuation">;</span>

    <span class="token class-name">PersonName</span> personName <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">PersonName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    personName<span class="token punctuation">.</span><span class="token function">setName</span><span class="token punctuation">(</span>name<span class="token punctuation">)</span><span class="token punctuation">;</span>
    personName<span class="token punctuation">.</span><span class="token function">setSurname</span><span class="token punctuation">(</span>surname<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Person</span> person <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Person</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    person<span class="token punctuation">.</span><span class="token function">setPersonName</span><span class="token punctuation">(</span>personName<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Long</span> id <span class="token operator">=</span> <span class="token punctuation">(</span><span class="token class-name">Long</span><span class="token punctuation">)</span> session<span class="token punctuation">.</span><span class="token function">save</span><span class="token punctuation">(</span>person<span class="token punctuation">)</span><span class="token punctuation">;</span>

    session<span class="token punctuation">.</span><span class="token function">flush</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    session<span class="token punctuation">.</span><span class="token function">clear</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>컨버터에 정의한 내용대로 PersonName이 저장되었는지, 데이터베이스 테이블에서 필드를 반환함으로써 테스트</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">givenPersonName_whenSaving_thenNameAndSurnameConcat</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token comment">// ...</span>

    <span class="token class-name">String</span> dbPersonName <span class="token operator">=</span> <span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">)</span> session<span class="token punctuation">.</span><span class="token function">createNativeQuery</span><span class="token punctuation">(</span>
      <span class="token string">"select p.personName from PersonTable p where p.id = :id"</span><span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token function">setParameter</span><span class="token punctuation">(</span><span class="token string">"id"</span><span class="token punctuation">,</span> id<span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token function">getSingleResult</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token function">assertEquals</span><span class="token punctuation">(</span>surname <span class="token operator">+</span> <span class="token string">", "</span> <span class="token operator">+</span> name<span class="token punctuation">,</span> dbPersonName<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p>바인딩 변수(?, <code>SELECT * FROM employees WHERE department_id = ?;</code>) 와 쿼리 매개변수(:, <code>String sql = "SELECT * FROM employees WHERE department_id = :deptId"; </code>)는 모두 SQL 쿼리를 더 동적으로 만들고 실행할 때 사용되지만, 바인딩 변수는 데이터베이스 시스템에서 사용되며 쿼리 실행 전에 값이 할당되고, 쿼리 매개변수는 프로그래밍 언어와 데이터베이스 간의 상호작용에서 사용되며 실행 시에 값이 대체됩니다</p>
</blockquote>
<p>데이터베이스 값이 PersonName 클래스에 저장되는 작업도 잘 적용되었는지 Person 클래스 전체를 쿼리로 반환하여 테스트</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">givenPersonName_whenSaving_thenNameAndSurnameConcat</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token comment">// ...</span>

    <span class="token class-name">Person</span> dbPerson <span class="token operator">=</span> session<span class="token punctuation">.</span><span class="token function">createNativeQuery</span><span class="token punctuation">(</span>
      <span class="token string">"select * from PersonTable p where p.id = :id"</span><span class="token punctuation">,</span> <span class="token class-name">Person</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
        <span class="token punctuation">.</span><span class="token function">setParameter</span><span class="token punctuation">(</span><span class="token string">"id"</span><span class="token punctuation">,</span> id<span class="token punctuation">)</span>
        <span class="token punctuation">.</span><span class="token function">getSingleResult</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token function">assertEquals</span><span class="token punctuation">(</span>dbPerson<span class="token punctuation">.</span><span class="token function">getPersonName</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token function">getName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> name<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">assertEquals</span><span class="token punctuation">(</span>dbPerson<span class="token punctuation">.</span><span class="token function">getPersonName</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token function">getSurname</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> surname<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>