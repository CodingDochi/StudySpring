<p>Spring에는 객체의 유효성을 검사하는 데 사용할 수 있는 <code>Validator</code> 인터페이스가 있습니다. <code>Validator</code> 인터페이스는 <code>Errors</code> 객체를 사용하여 작동하므로 유효성을 검사하는 동안 유효성 검사기는 유효성 검사 실패를 <code>Errors</code> 객체에 보고할 수 있습니다.</p>
<p>작은 데이터 객체의 다음 예를 고려하십시오.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Person</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">String</span> name<span class="token punctuation">;</span>
	<span class="token keyword">private</span> <span class="token keyword">int</span> age<span class="token punctuation">;</span>

	<span class="token comment">// the usual getters and setters...</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 예제에서는 <code>org.springframework.validation.Validator</code> 인터페이스의 다음 두 가지 메서드를 구현하여 <code>Person</code> 클래스에 대한 유효성 검사 동작을 제공합니다.</p>
<ul>
<li>
<p><code>support(Class)</code>: 이 <code>Validator</code>가 제공된 <code>Class</code>의 인스턴스를 검증할 수 있습니까?</p>
</li>
<li>
<p><code>validate(Object, org.springframework.validation.Errors)</code>: 주어진 객체의 유효성을 검사하고 유효성 검사 오류가 있는 경우, 해당 객체를 주어진 <code>Errors</code> 객체에 등록합니다.</p>
</li>
</ul>
<p><code>Validator</code>를 구현하는 것은 매우 간단합니다. 특히 Spring Framework가 제공하는 <code>ValidationUtils</code> 도우미 클래스를 알고 있는 경우 더욱 그렇습니다. 다음 예에서는 <code>Person</code> 인스턴스에 대한 <code>Validator</code>를 구현합니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">PersonValidator</span> <span class="token keyword">implements</span> <span class="token class-name">Validator</span> <span class="token punctuation">{</span>

	<span class="token comment">/**
	 * This Validator validates only Person instances
	 */</span>
	<span class="token keyword">public</span> <span class="token keyword">boolean</span> <span class="token function">supports</span><span class="token punctuation">(</span><span class="token class-name">Class</span> clazz<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token class-name">Person</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">.</span><span class="token function">equals</span><span class="token punctuation">(</span>clazz<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">validate</span><span class="token punctuation">(</span><span class="token class-name">Object</span> obj<span class="token punctuation">,</span> <span class="token class-name">Errors</span> e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">ValidationUtils</span><span class="token punctuation">.</span><span class="token function">rejectIfEmpty</span><span class="token punctuation">(</span>e<span class="token punctuation">,</span> <span class="token string">"name"</span><span class="token punctuation">,</span> <span class="token string">"name.empty"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token class-name">Person</span> p <span class="token operator">=</span> <span class="token punctuation">(</span><span class="token class-name">Person</span><span class="token punctuation">)</span> obj<span class="token punctuation">;</span>
		<span class="token keyword">if</span> <span class="token punctuation">(</span>p<span class="token punctuation">.</span><span class="token function">getAge</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">&lt;</span> <span class="token number">0</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
			e<span class="token punctuation">.</span><span class="token function">rejectValue</span><span class="token punctuation">(</span><span class="token string">"age"</span><span class="token punctuation">,</span> <span class="token string">"negativevalue"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token keyword">if</span> <span class="token punctuation">(</span>p<span class="token punctuation">.</span><span class="token function">getAge</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">&gt;</span> <span class="token number">110</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
			e<span class="token punctuation">.</span><span class="token function">rejectValue</span><span class="token punctuation">(</span><span class="token string">"age"</span><span class="token punctuation">,</span> <span class="token string">"too.darn.old"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>ValidationUtils</code> 클래스의 <code>static</code> <code>rejectIfEmpty(..)</code> 메서드는 <code>name</code> 속성(property)이 <code>null</code>이거나 빈 문자열인 경우 해당 속성(property)을 거부하는 데 사용됩니다. 이전에 표시된 예제 외에 어떤 기능을 제공하는지 확인하려면 <a href="https://docs.spring.io/spring-framework/docs/6.1.5/javadoc-api/org/springframework/validation/ValidationUtils.html"><code>ValidationUtils</code></a> javadoc을 살펴보세요.</p>
<p>풍부한(rich) 객체에 포함된 각 개체의 유효성을 검사하기 위해 단일 <code>Validator</code> 클래스를 구현하는 것이 확실히 가능하지만, 자체 <code>Validator</code> 구현에서 개체의 각 중첩 클래스에 대한 유효성 검사 논리를 캡슐화하는 것이 더 나을 수 있습니다. 풍부한(rich) 객체의 간단한 예는 두 개의 <code>String</code> 속성(property)(이름과 두 번째 이름)과 복잡한 <code>Address</code> 객체로 구성된 <code>Customer</code>입니다. <code>Address</code> 객체는 <code>Customer</code> 객체와 독립적으로 사용될 수 있으므로 별도의 <code>AddressValidator</code>가 구현되었습니다. <code>CustomerValidator</code>가 복사하여 붙여넣는 방식을 사용하지 않고 <code>AddressValidator</code> 클래스에 포함된 논리를 재사용하도록 하려면 다음 예제와 같이 <code>CustomerValidator</code> 내에서 <code>AddressValidator</code>를 종속성 주입하거나 인스턴스화할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">CustomerValidator</span> <span class="token keyword">implements</span> <span class="token class-name">Validator</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">Validator</span> addressValidator<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token class-name">CustomerValidator</span><span class="token punctuation">(</span><span class="token class-name">Validator</span> addressValidator<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">if</span> <span class="token punctuation">(</span>addressValidator <span class="token operator">==</span> <span class="token keyword">null</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
			<span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">IllegalArgumentException</span><span class="token punctuation">(</span><span class="token string">"The supplied [Validator] is "</span> <span class="token operator">+</span>
				<span class="token string">"required and must not be null."</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>
		<span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token operator">!</span>addressValidator<span class="token punctuation">.</span><span class="token function">supports</span><span class="token punctuation">(</span><span class="token class-name">Address</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
			<span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">IllegalArgumentException</span><span class="token punctuation">(</span><span class="token string">"The supplied [Validator] must "</span> <span class="token operator">+</span>
				<span class="token string">"support the validation of [Address] instances."</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>addressValidator <span class="token operator">=</span> addressValidator<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">/**
	 * This Validator validates Customer instances, and any subclasses of Customer too
	 */</span>
	<span class="token keyword">public</span> <span class="token keyword">boolean</span> <span class="token function">supports</span><span class="token punctuation">(</span><span class="token class-name">Class</span> clazz<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token class-name">Customer</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">.</span><span class="token function">isAssignableFrom</span><span class="token punctuation">(</span>clazz<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">validate</span><span class="token punctuation">(</span><span class="token class-name">Object</span> target<span class="token punctuation">,</span> <span class="token class-name">Errors</span> errors<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">ValidationUtils</span><span class="token punctuation">.</span><span class="token function">rejectIfEmptyOrWhitespace</span><span class="token punctuation">(</span>errors<span class="token punctuation">,</span> <span class="token string">"firstName"</span><span class="token punctuation">,</span> <span class="token string">"field.required"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token class-name">ValidationUtils</span><span class="token punctuation">.</span><span class="token function">rejectIfEmptyOrWhitespace</span><span class="token punctuation">(</span>errors<span class="token punctuation">,</span> <span class="token string">"surname"</span><span class="token punctuation">,</span> <span class="token string">"field.required"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token class-name">Customer</span> customer <span class="token operator">=</span> <span class="token punctuation">(</span><span class="token class-name">Customer</span><span class="token punctuation">)</span> target<span class="token punctuation">;</span>
		<span class="token keyword">try</span> <span class="token punctuation">{</span>
			errors<span class="token punctuation">.</span><span class="token function">pushNestedPath</span><span class="token punctuation">(</span><span class="token string">"address"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			<span class="token class-name">ValidationUtils</span><span class="token punctuation">.</span><span class="token function">invokeValidator</span><span class="token punctuation">(</span><span class="token keyword">this</span><span class="token punctuation">.</span>addressValidator<span class="token punctuation">,</span> customer<span class="token punctuation">.</span><span class="token function">getAddress</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> errors<span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span> <span class="token keyword">finally</span> <span class="token punctuation">{</span>
			errors<span class="token punctuation">.</span><span class="token function">popNestedPath</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>유효성 검사 오류는 유효성 검사기에 전달된 <code>Errors</code> 객체에 보고됩니다. Spring Web MVC의 경우 <code>&lt;spring:bind/&gt;</code> 태그를 사용하여 오류 메시지를 검사할 수 있지만 <code>Errors</code> 객체를 직접 검사할 수도 있습니다. 제공되는 메소드에 대한 자세한 내용은 <a href="https://docs.spring.io/spring-framework/docs/6.1.5/javadoc-api/org/springframework/validation/Errors.html">javadoc</a>에서 찾을 수 있습니다.</p>
<p>유효성 검사기는 바인딩 프로세스를 포함하지 않고 특정 객체의 즉각적인 유효성 검사를 위해 로컬로 호출될 수도 있습니다. 6.1부터 이는 기본적으로 사용할 수 있는 새로운 <code>Validator.validateObject(Object)</code> 메소드를 통해 단순화되었으며, 검사할 수 있는 간단한 'Errors' 표현을 반환합니다. 일반적으로 <code>hasErrors()</code>를 호출하거나 새로운 <code>fallOnError</code> 메소드를 호출하여 오류 요약 메시지를 예외로 변환합니다(예: <code>validator.validateObject(myObject).failOnError(IllegalArgumentException::new)</code>).</p>