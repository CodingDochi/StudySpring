<p><a href="https://docs.spring.io/spring-framework/docs/6.1.4/javadoc-api/org/springframework/beans/factory/annotation/CustomAutowireConfigurer.html"><code>CustomAutowireConfigurer</code></a>는 Spring의 <code>@Qualifier</code>  어노테이션이 추가되지 않은 경우에도 사용자 정의 한정자 어노테익션 타입을 등록할 수 있는 <code>BeanFactoryPostProcessor</code>입니다. 다음 예에서는 <code>CustomAutowireConfigurer</code>를 사용하는 방법을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>customAutowireConfigurer<span class="token punctuation">"</span></span>
		<span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.beans.factory.annotation.CustomAutowireConfigurer<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>customQualifierTypes<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>set</span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>example.CustomQualifier<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>set</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span></code></pre>
<p><code>AutowireCandidateResolver</code>는 다음을 통해 자동 와이어 후보를 결정합니다.</p>
<ul>
<li>
<p>각 빈 정의의 <code>autowire-candidate</code> 값</p>
</li>
<li>
<p><code>&lt;beans/&gt;</code> 요소에서 사용 가능한 모든 <code>default-autowire-candidates</code> 패턴</p>
</li>
<li>
<p><code>CustomAutowireConfigurer</code>에 등록된 <code>@Qualifier</code> 어노테이션 및 사용자 정의 어노테이션의 존재</p>
</li>
</ul>
<p>다수의 빈이 자동연결 후보로 자격을 갖추었을 때 "기본(primary)"의 결정은 다음과 같습니다. 후보 중 정확히 하나의 빈 정의가 <code>true</code>로 설정된 <code>primary</code> 속성(attribute)을 갖는 경우 해당 빈이 선택됩니다.</p>