<h2 id="interface--abstract-class">interface / abstract class</h2>
<table><thead><tr><th>측면</th><th>인터페이스</th><th>추상 클래스</th></tr></thead><tbody><tr><td><strong>선언 방식</strong></td><td><code>interface</code> 키워드로 선언</td><td><code>abstract class</code> 키워드로 선언</td></tr><tr><td><strong>다중 상속</strong></td><td>다중 상속 지원 (<code>extends</code> 키워드를 통해 여러 인터페이스 상속 가능)</td><td>단일 상속만 지원 (<code>extends</code> 키워드를 통해 하나의 클래스만 상속 가능)</td></tr><tr><td><strong>메서드 구현</strong></td><td>모든 메서드는 추상 메서드로 기본적으로 선언됨</td><td>추상 메서드, 일반 메서드, <code>final</code>, <code>private</code> 등 다양한 접근자 지원</td></tr><tr><td><strong>인스턴스화 가능 여부</strong></td><td>직접적으로 인스턴스화할 수 없음 (객체 생성 불가)</td><td>직접적으로 인스턴스화 불가하지만, 하위 클래스로 상속받아 인스턴스화 가능</td></tr><tr><td><strong>구현체에서의 구현</strong></td><td>모든 메서드는 구현체에서 반드시 구현되어야 함</td><td>추상 메서드는 반드시 하위 클래스에서 오버라이드해야 하지만, 나머지 메서드는 선택적으로 오버라이드 가능</td></tr><tr><td><strong>유틸리티 기능</strong></td><td>메서드 시그니처를 정의하여 구현체 간에 일관성을 제공</td><td>내부적인 동작 로직이나 유틸리티 기능을 제공할 수 있음</td></tr></tbody></table>
<p>추상클래스는 인스턴스 변수, 즉 필드를 가질 수 있지만, 인터페이스는 <code>abstract</code>, <code>static</code>, <code>default</code> 메서드와 <code>static final</code> 형태의 상수만을 가질 수 있습니다.</p>
<p>래퍼 클래스 패턴은 기존 클래스를 상속받거나 변경하지 않고, 새로운 기능을 추가하기 위한 디자인 패턴입니다. 이는 인터페이스를 이용하여 기능을 추가하고, 이를 상속받는 클래스가 해당 기능을 사용하게 하는 것을 의미합니다. </p>
<h2 id="java-8--static-default-메서드-도입">java 8 : static, default 메서드 도입</h2>
<h4 id="default">default</h4>
<ol>
<li>
<p><strong>역방향 호환성(Backward Compatibility)</strong>: </p>
<ul>
<li>기존에 작성된 코드가 새로운 버전이나 변경된 환경에서도 여전히 동작하는 데 있어 중요한 개념입니다. </li>
<li>코드가 업데이트되거나 새로운 기능이 추가되더라도 기존 코드의 수정 없이 여전히 정상적으로 동작해야 합니다. </li>
<li>이는 기존의 소프트웨어와 새로운 버전 간의 호환성을 유지하고 기존 사용자들이 새로운 버전을 사용할 때 발생할 수 있는 문제를 방지하는 데 도움이 됩니다.</li>
</ul>
</li>
<li>
<p><strong>인터페이스의 확장성(Interface Evolution)</strong>:</p>
<ul>
<li>이는 인터페이스의 변경과 관련된 개념으로, 기존의 인터페이스를 확장하거나 변경함으로써 새로운 기능을 추가할 수 있는 능력을 의미합니다.</li>
<li><code>default</code> 메서드와 같은 기능은 이전에 만들어진 인터페이스에 새로운 메서드를 추가하면서도 이미 구현된 클래스에 영향을 미치지 않고 새로운 기능을 도입할 수 있도록 합니다.</li>
<li>기존에 없던 메서드를 추가하거나 새로운 기능을 제공함으로써 인터페이스의 확장성을 높일 수 있습니다.</li>
</ul>
</li>
</ol>
<h4 id="static">static</h4>
<p><strong>유틸리티 메서드 구성(Utility Method Composition)</strong>:</p>
<p>static 메서드를 인터페이스에 추가할 수 있게 되면, 해당 인터페이스를 사용하여 유틸리티 메서드를 그룹화할 수 있습니다. 이렇게 하면 특정 기능을 위한 정적 메서드들을 해당 인터페이스로 묶어 관리할 수 있습니다. Java에서 인터페이스에 정의된 static 메서드는 상속되거나 오버라이드될 수 없습니다. 또한 구현 클래스에서 반드시 호출할 필요는 없습니다.</p>
<h2 id="인터페이스를-import--implement">인터페이스를 import / implement</h2>
<p>인터페이스를 import하는 것과 implement하는 것은 Java에서 서로 다른 개념입니다.</p>
<ol>
<li>
<p><strong>인터페이스를 import하는 것</strong>:</p>
<ul>
<li><code>import</code> 문은 Java에서 다른 패키지에 있는 클래스나 인터페이스를 현재 파일에서 사용하기 위해 선언하는 것입니다.</li>
<li><code>import</code>를 사용하여 패키지에 있는 클래스나 인터페이스의 이름을 짧게 사용할 수 있게 해줍니다. 예를 들어, <code>import java.util.List;</code>를 선언하면 <code>List</code>라고만 써도 <code>java.util</code> 패키지 내의 <code>List</code>를 사용할 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>인터페이스를 implement(구현)하는 것</strong>:</p>
<ul>
<li>클래스가 인터페이스를 구현하기 위해서는 <code>implements</code> 키워드를 사용합니다. 이는 해당 클래스가 특정 인터페이스의 메서드들을 구현하겠다는 것을 의미합니다.</li>
<li>인터페이스를 구현하는 클래스는 인터페이스의 모든 메서드(static, default 메서드 제외)를 반드시 구현해야 합니다. 그렇지 않으면 컴파일 에러가 발생합니다.</li>
</ul>
</li>
</ol>
<h2 id="중첩-interface">중첩 interface</h2>
<p>중첩 인터페이스는 하나의 인터페이스 안에 또 다른 인터페이스가 정의된 것을 말합니다. 이는 코드의 구조를 조직화하고 관련 있는 부분을 묶어서 관리할 수 있게 해줍니다. </p>
<p>중첩 인터페이스는 외부 인터페이스의 멤버로서 동작하며, 외부 클래스나 인터페이스의 이름을 접근할 때는 <code>OuterInterface.InnerInterface</code>와 같이 점으로 구분하여 사용합니다. </p>
<p>중첩 인터페이스는 외부 클래스의 메소드들과 마찬가지로 구현되어 사용될 수 있습니다.</p>
<h3 id="coffeeconfiguration-클래스">CoffeeConfiguration 클래스</h3>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>redis<span class="token punctuation">.</span>serializer</span><span class="token punctuation">.</span><span class="token class-name">Jackson2JsonRedisSerializer</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>redis<span class="token punctuation">.</span>serializer</span><span class="token punctuation">.</span><span class="token class-name">RedisSerializationContext</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>redis<span class="token punctuation">.</span>serializer</span><span class="token punctuation">.</span><span class="token class-name">StringRedisSerializer</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">CoffeeConfiguration</span> <span class="token punctuation">{</span>


<span class="token comment">//... 생략</span>

<span class="token class-name">RedisSerializationContext</span><span class="token punctuation">.</span><span class="token class-name">RedisSerializationContextBuilder</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">Coffee</span><span class="token punctuation">&gt;</span></span> builder <span class="token operator">=</span> 
<span class="token class-name">RedisSerializationContext</span><span class="token punctuation">.</span><span class="token function">newSerializationContext</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">StringRedisSerializer</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">RedisSerializationContext</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">Coffee</span><span class="token punctuation">&gt;</span></span> context <span class="token operator">=</span> builder<span class="token punctuation">.</span><span class="token function">value</span><span class="token punctuation">(</span>serializer<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">//... 중략</span></code></pre>
<h3 id="redisserializationcontext-인터페이스">RedisSerializationContext 인터페이스</h3>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">RedisSerializationContext</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">K</span><span class="token punctuation">,</span> <span class="token class-name">V</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>
    <span class="token keyword">static</span> <span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">K</span><span class="token punctuation">,</span> <span class="token class-name">V</span><span class="token punctuation">&gt;</span></span> <span class="token class-name">RedisSerializationContextBuilder</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">K</span><span class="token punctuation">,</span> <span class="token class-name">V</span><span class="token punctuation">&gt;</span></span> <span class="token function">newSerializationContext</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">DefaultRedisSerializationContext</span><span class="token punctuation">.</span><span class="token class-name">DefaultRedisSerializationContextBuilder</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
<span class="token comment">//... 중략</span>

    <span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">SerializationPair</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>
        <span class="token keyword">static</span> <span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token class-name">SerializationPair</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token function">fromSerializer</span><span class="token punctuation">(</span><span class="token class-name">RedisSerializer</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> serializer<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token class-name">Assert</span><span class="token punctuation">.</span><span class="token function">notNull</span><span class="token punctuation">(</span>serializer<span class="token punctuation">,</span> <span class="token string">"RedisSerializer must not be null"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">RedisSerializerToSerializationPairAdapter</span><span class="token punctuation">(</span>serializer<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

<span class="token comment">//... 중략</span>

    <span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">RedisSerializationContextBuilder</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">K</span><span class="token punctuation">,</span> <span class="token class-name">V</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>
    
    <span class="token comment">//... 중략</span>
    
    	<span class="token class-name">RedisSerializationContextBuilder</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">K</span><span class="token punctuation">,</span> <span class="token class-name">V</span><span class="token punctuation">&gt;</span></span> <span class="token function">value</span><span class="token punctuation">(</span><span class="token class-name">SerializationPair</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">V</span><span class="token punctuation">&gt;</span></span> pair<span class="token punctuation">)</span><span class="token punctuation">;</span>
        
    <span class="token comment">//... 중략</span>
    
    	<span class="token class-name">RedisSerializationContext</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">K</span><span class="token punctuation">,</span> <span class="token class-name">V</span><span class="token punctuation">&gt;</span></span> <span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>클래스 <code>CoffeeConfiguration</code>에서는 <code>RedisSerializationContext</code> 인터페이스를 구현(implement)하지 않고, 해당 인터페이스의 구성 요소 중 일부를 사용하기 위해 <code>import</code>를 통해 가져왔습니다. 이 클래스에서는 <code>RedisSerializationContextBuilder</code>를 사용하기 위해 <code>RedisSerializationContext</code>를 통해 접근하여 <code>builder</code> 변수에 연결했습니다.</p>
<p><code>newSerializationContext()</code> 메서드는 <code>RedisSerializationContextBuilder</code>의 외부에 선언된 <code>static</code> 메서드이므로 <code>RedisSerializationContext</code>에서 직접 호출할 수 있습니다.</p>
<p>그러나 <code>value()</code>와 <code>build()</code>는 <code>RedisSerializationContextBuilder</code>의 내부에 선언된 <code>abstract</code> 메서드이므로, 점 접근법(dot notation)을 사용하여 <code>RedisSerializationContext.RedisSerializationContextBuilder&lt;K, V&gt;</code> 타입을 가진 builder를 통해 접근해야 합니다.</p>
<h2 id="클래스-리터럴과-클래스-시그니처">클래스 리터럴과 클래스 시그니처</h2>
<ul>
<li><strong>"클래스 리터럴"</strong> 이라는 용어는 실제로 해당 클래스의 메타데이터를 나타내는 것으로, Java에서 <strong><code>Class&lt;T&gt;</code></strong> 타입의 객체입니다. 클래스의 리터럴은 클래스의 메타정보를 포함하고 있어서, 시그니처에 대한 정보를 담고 있는 것으로 볼 수 있습니다. 시그니처는 해당 클래스의 구조와 관련된 정보를 포함하는데, 클래스의 리터럴을 통해 이 정보에 접근할 수 있습니다.</li>
</ul>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ClassLiteralExample</span> <span class="token punctuation">{</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">Class</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> stringClass <span class="token operator">=</span> <span class="token class-name">String</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">;</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"stringClass: "</span> <span class="token operator">+</span> stringClass<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// stringClass 출력</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"String.class: "</span> <span class="token operator">+</span> <span class="token class-name">String</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// String.class 출력</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>String.class</code>의 결과로 반환되는 것은 <code>Class&lt;String&gt;</code> 객체입니다. 이것은 <code>String</code> 클래스의 메타정보를 담고 있는 객체이며, 해당 클래스의 구조, 메서드, 필드 등에 대한 정보를 담고 있습니다. <code>stringClass</code>라는 변수가 이 객체를 가리키고 있습니다.</p>
<p>출력 결과는 아래와 같습니다.</p>
<pre><code class="language-java">stringClass<span class="token operator">:</span> <span class="token keyword">class</span> java<span class="token punctuation">.</span>lang<span class="token punctuation">.</span><span class="token class-name">String</span>
<span class="token class-name">String</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token operator">:</span> <span class="token keyword">class</span> java<span class="token punctuation">.</span>lang<span class="token punctuation">.</span><span class="token class-name">String</span></code></pre>
<ul>
<li>반면에 <strong>"시그니처"</strong> 는 일반적으로 클래스, 메서드 또는 필드의 형태와 관련된 정보를 의미합니다. 이것은 해당 항목의 타입, 매개변수, 반환형 등을 설명하는 것을 말합니다.</li>
</ul>
<h2 id="매개변수에서-클래스와-클래스-리터럴의-사용">매개변수에서 클래스와 클래스 리터럴의 사용</h2>
<p>클래스 리터럴을 받는 경우에는 클래스의 메타데이터를 다루거나, 타입 안전성을 보장하기 위해 사용되고, 클래스 자체를 받는 경우에는 클래스의 객체를 다루거나, 다형성을 활용하는 등의 목적으로 활용될 수 있습니다.</p>
<h3 id="클래스-리터럴을-매개변수로">클래스 리터럴을 매개변수로</h3>
<h4 id="팩토리-메서드-패턴">팩토리 메서드 패턴</h4>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">FactoryExample</span> <span class="token punctuation">{</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token class-name">T</span> <span class="token function">createInstance</span><span class="token punctuation">(</span><span class="token class-name">Class</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> clazz<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">IllegalAccessException</span><span class="token punctuation">,</span> <span class="token class-name">InstantiationException</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> clazz<span class="token punctuation">.</span><span class="token function">newInstance</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// 클래스로부터 인스턴스를 생성하는 간단한 팩토리 메서드</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">InstantiationException</span><span class="token punctuation">,</span> <span class="token class-name">IllegalAccessException</span> <span class="token punctuation">{</span>
        <span class="token comment">// 클래스를 매개변수로 전달하여 객체 생성</span>
        <span class="token class-name">String</span> instance <span class="token operator">=</span> <span class="token class-name">FactoryExample</span><span class="token punctuation">.</span><span class="token function">createInstance</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>instance<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// 출력: null (빈 문자열)</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h4 id="타입-안정성-유틸리티">타입 안정성 유틸리티</h4>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">TypeUtilityExample</span> <span class="token punctuation">{</span>
    <span class="token keyword">static</span> <span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token keyword">void</span> <span class="token function">printClassName</span><span class="token punctuation">(</span><span class="token class-name">Class</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> clazz<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"클래스 이름: "</span> <span class="token operator">+</span> clazz<span class="token punctuation">.</span><span class="token function">getName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token comment">// 클래스를 매개변수로 받아서 클래스 이름을 출력하는 유틸리티 예시</span>
        <span class="token class-name">TypeUtilityExample</span><span class="token punctuation">.</span><span class="token function">printClassName</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// 출력: "클래스 이름: java.lang.String"</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h3 id="클래스-자체를-매개변수로">클래스 자체를 매개변수로</h3>
<h4 id="스트래티지-패턴">스트래티지 패턴</h4>
<pre><code class="language-java"><span class="token comment">// 전략(스트래티지) 인터페이스</span>
<span class="token keyword">interface</span> <span class="token class-name">Strategy</span> <span class="token punctuation">{</span>
    <span class="token keyword">void</span> <span class="token function">execute</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token comment">// 전략을 구현한 클래스</span>
<span class="token keyword">class</span> <span class="token class-name">StrategyA</span> <span class="token keyword">implements</span> <span class="token class-name">Strategy</span> <span class="token punctuation">{</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">execute</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"전략 A 수행"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token keyword">class</span> <span class="token class-name">StrategyB</span> <span class="token keyword">implements</span> <span class="token class-name">Strategy</span> <span class="token punctuation">{</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">execute</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"전략 B 수행"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token comment">// 전략을 사용하는 클래스</span>
<span class="token keyword">class</span> <span class="token class-name">Context</span> <span class="token punctuation">{</span>
    <span class="token keyword">private</span> <span class="token class-name">Strategy</span> strategy<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">Context</span><span class="token punctuation">(</span><span class="token class-name">Strategy</span> strategy<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>strategy <span class="token operator">=</span> strategy<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">executeStrategy</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        strategy<span class="token punctuation">.</span><span class="token function">execute</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">StrategyPatternExample</span> <span class="token punctuation">{</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token comment">// 전략을 클래스로 받아 객체 생성</span>
        <span class="token class-name">Context</span> contextA <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Context</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">StrategyA</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">Context</span> contextB <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Context</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">StrategyB</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        contextA<span class="token punctuation">.</span><span class="token function">executeStrategy</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// 출력: "전략 A 수행"</span>
        contextB<span class="token punctuation">.</span><span class="token function">executeStrategy</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// 출력: "전략 B 수행"</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h4 id="상속과-다형성">상속과 다형성</h4>
<pre><code class="language-java"><span class="token keyword">class</span> <span class="token class-name">Animal</span> <span class="token punctuation">{</span>
    <span class="token keyword">void</span> <span class="token function">makeSound</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"동물 소리"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token keyword">class</span> <span class="token class-name">Dog</span> <span class="token keyword">extends</span> <span class="token class-name">Animal</span> <span class="token punctuation">{</span>
    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">void</span> <span class="token function">makeSound</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"멍멍"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">PolymorphismExample</span> <span class="token punctuation">{</span>
    <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">soundOfAnimal</span><span class="token punctuation">(</span><span class="token class-name">Animal</span> animal<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        animal<span class="token punctuation">.</span><span class="token function">makeSound</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token comment">// 상위 클래스를 매개변수로 받아 하위 클래스의 인스턴스를 다룸</span>
        <span class="token class-name">Animal</span> animal1 <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Animal</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">Animal</span> animal2 <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Dog</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token function">soundOfAnimal</span><span class="token punctuation">(</span>animal1<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// 출력: "동물 소리"</span>
        <span class="token function">soundOfAnimal</span><span class="token punctuation">(</span>animal2<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// 출력: "멍멍"</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="생성자-내에서-this로-다른-생성자-호출하기">생성자 내에서 this()로 다른 생성자 호출하기</h2>
<h3 id="jackson2jsonredisserializer-클래스">Jackson2JsonRedisSerializer 클래스</h3>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Jackson2JsonRedisSerializer</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token keyword">implements</span> <span class="token class-name">RedisSerializer</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token class-name">Charset</span> DEFAULT_CHARSET<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">JavaType</span> javaType<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">ObjectMapper</span> mapper<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">JacksonObjectReader</span> reader<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">JacksonObjectWriter</span> writer<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">Jackson2JsonRedisSerializer</span><span class="token punctuation">(</span><span class="token class-name">Class</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> type<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">ObjectMapper</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> type<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token comment">//...중략</span>

    <span class="token keyword">public</span> <span class="token class-name">Jackson2JsonRedisSerializer</span><span class="token punctuation">(</span><span class="token class-name">ObjectMapper</span> mapper<span class="token punctuation">,</span> <span class="token class-name">Class</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> type<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">Assert</span><span class="token punctuation">.</span><span class="token function">notNull</span><span class="token punctuation">(</span>mapper<span class="token punctuation">,</span> <span class="token string">"ObjectMapper must not be null"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">Assert</span><span class="token punctuation">.</span><span class="token function">notNull</span><span class="token punctuation">(</span>type<span class="token punctuation">,</span> <span class="token string">"Java type must not be null"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>javaType <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">.</span><span class="token function">getJavaType</span><span class="token punctuation">(</span>type<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>mapper <span class="token operator">=</span> mapper<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>reader <span class="token operator">=</span> <span class="token class-name">JacksonObjectReader</span><span class="token punctuation">.</span><span class="token function">create</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>writer <span class="token operator">=</span> <span class="token class-name">JacksonObjectWriter</span><span class="token punctuation">.</span><span class="token function">create</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
 <span class="token comment">//...생략   </span>
</code></pre>
<p><code>this</code> 키워드는 클래스의 인스턴스를 가리키는 것이 맞습니다. 그러나 생성자에서 <code>this()</code>를 호출할 때, 이는 같은 클래스 내의 다른 생성자를 의미합니다.</p>
<p><code>this(new ObjectMapper(), type)</code>은 <code>Jackson2JsonRedisSerializer(ObjectMapper mapper, Class&lt;T&gt; type)</code>라는 두 개의 매개변수를 가진 다른 생성자를 호출하려는 의도를 보인 것입니다.</p>
<p>이렇듯 <code>Jackson2JsonRedisSerializer(Class&lt;T&gt; type)</code> 생성자가 호출될 때, 내부적으로 <code>Jackson2JsonRedisSerializer(ObjectMapper mapper, Class&lt;T&gt; type)</code> 생성자를 호출함으로써 코드 중복을 피하도록 합니다. </p>
<h3 id="objectmapper-클래스">ObjectMapper 클래스</h3>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ObjectMapper</span> <span class="token keyword">extends</span> <span class="token class-name">ObjectCodec</span> <span class="token keyword">implements</span> <span class="token class-name">Versioned</span><span class="token punctuation">,</span> <span class="token class-name">Serializable</span> <span class="token punctuation">{</span>

<span class="token comment">//... 중략</span>
    <span class="token keyword">public</span> <span class="token class-name">ObjectMapper</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">(</span><span class="token punctuation">(</span><span class="token class-name">JsonFactory</span><span class="token punctuation">)</span><span class="token keyword">null</span><span class="token punctuation">,</span> <span class="token punctuation">(</span><span class="token class-name">DefaultSerializerProvider</span><span class="token punctuation">)</span><span class="token keyword">null</span><span class="token punctuation">,</span> <span class="token punctuation">(</span><span class="token class-name">DefaultDeserializationContext</span><span class="token punctuation">)</span><span class="token keyword">null</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
    <span class="token keyword">public</span> <span class="token class-name">ObjectMapper</span><span class="token punctuation">(</span><span class="token class-name">JsonFactory</span> jf<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">(</span>jf<span class="token punctuation">,</span> <span class="token punctuation">(</span><span class="token class-name">DefaultSerializerProvider</span><span class="token punctuation">)</span><span class="token keyword">null</span><span class="token punctuation">,</span> <span class="token punctuation">(</span><span class="token class-name">DefaultDeserializationContext</span><span class="token punctuation">)</span><span class="token keyword">null</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token comment">//... 생략    </span></code></pre>
<p>위 코드에서 보이는 두 개의 생성자는 모두 <code>JsonFactory</code>, <code>DefaultSerializerProvider</code>, <code>DefaultDeserializationContext</code> 타입을 가지는 세 개의 매개변수를 사용하는 <code>ObjectMapper</code> 생성자를 <code>this()</code>로 호출하고 있다.</p>
<h2 id="final과-static"><code>final</code>과 <code>static</code></h2>
<h3 id="final-값-할당-시점"><code>final</code> 값 할당 시점</h3>
<p><code>final</code>로 선언된 변수는 선언 시점이 아니라 생성자에서 값이 할당되어야 합니다. 그렇지 않으면 컴파일 오류가 발생합니다. 일반적으로 <code>final</code> 필드는 선언과 동시에 또는 생성자에서 값을 한 번만 할당해야 합니다. 그렇지 않으면 컴파일러가 오류를 내거나 경고를 발생시킬 수 있습니다. </p>
<p>하지만 제공하신 코드에서 <code>_recipe</code>와 <code>_factory</code>는 생성자에서 초기값으로 <code>null</code>을 할당받고 있습니다. 이는 유효한 방법입니다. 생성자 내부에서 <code>final</code> 필드를 한 번만 초기화할 수 있고, 이후에는 해당 값이 변하지 않아야 합니다. 코드에서 보듯이, 생성자에서 한 번만 초기화되고 그 이후에는 변경되지 않는다면 이는 <code>final</code> 필드의 규칙을 지키고 있는 것입니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">abstract</span> <span class="token keyword">class</span> <span class="token class-name">SourceOrigin</span> <span class="token punctuation">{</span>

    <span class="token keyword">protected</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token class-name">BaseSource</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Object</span><span class="token punctuation">&gt;</span></span> BASE_UNKNOWN_SOURCE <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Brewer</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">protected</span> <span class="token keyword">final</span> <span class="token class-name">SourceRecipe</span> _recipe<span class="token punctuation">;</span>
    <span class="token keyword">protected</span> <span class="token keyword">final</span> <span class="token class-name">SourceFactory</span> _factory<span class="token punctuation">;</span>
    <span class="token keyword">protected</span> <span class="token class-name">BaseSource</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Object</span><span class="token punctuation">&gt;</span></span> _unknownSource<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">SourceOrigin</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>_unknownSource <span class="token operator">=</span> BASE_UNKNOWN_SOURCE<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>_recipe <span class="token operator">=</span> <span class="token keyword">null</span><span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>_factory <span class="token operator">=</span> <span class="token keyword">null</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>static final</code>로 선언된 필드인 <code>BASE_UNKNOWN_SOURCE</code>는 해당 클래스에 속한 상수입니다. 이것은 수정될 수 없는 불변의 상태를 갖습니다.</p>
<h2 id="클래스의-상속-관계">클래스의 상속 관계</h2>
<p><a href="https://www.baeldung.com/java-reflection-class-fields">https://www.baeldung.com/java-reflection-class-fields</a></p>
<p>네, 많은 내용을 잘 설명하셨습니다.</p>
<ol>
<li>
<p><strong>부모 클래스의 non-final, non-static, non-private 속성(인스턴스 속성)</strong>: 부모 클래스의 인스턴스 생성자를 통해 값을 할당하고, 자식 클래스에서 상속받아 사용하거나 필요에 따라 재정의할 수 있습니다.</p>
</li>
<li>
<p><strong>부모 클래스의 non-final, non-private, static 속성(스태틱 속성)</strong>: 인스턴스나 자식 클래스에서는 직접 접근할 수 없고, 부모 클래스 이름을 통해서 접근하여 값을 수정할 수 있습니다.</p>
</li>
<li>
<p><strong>부모 클래스의 final 속성</strong>: final 속성은 변경할 수 없는 값으로 취급되며, 자식 클래스, 부모 클래스의 인스턴스, 또는 부모 클래스 이름을 통한 접근을 통해 값을 읽을 수 있지만, 수정은 불가능합니다. final인 속성이 생성자에서 초기화되지 않았다면, 생성자에서 <code>this</code>를 통해 값을 할당할 수 있습니다.</p>
</li>
<li>
<p><strong>부모 클래스의 private 속성</strong>: private 속성은 클래스 내부에서만 접근 가능하며, 부모 클래스의 인스턴스, 자식 클래스, 또는 부모 클래스 이름을 통한 접근을 통해 접근하거나 수정할 수 없습니다.</p>
</li>
</ol>
<h3 id="자식-클래스에서-부모-클래스의-기본-생성자가-호출">자식 클래스에서 부모 클래스의 기본 생성자가 호출</h3>
<h4 id="parent-클래스-생성">Parent 클래스 생성</h4>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Parent</span> <span class="token punctuation">{</span>
    <span class="token keyword">public</span> <span class="token class-name">Parent</span> <span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"나는 Parent 기본 생성자"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<h4 id="child-클래스가-parent-클래스-상속">Child 클래스가 Parent 클래스 상속</h4>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Child</span> <span class="token keyword">extends</span> <span class="token class-name">Parent</span><span class="token punctuation">{</span>
    <span class="token class-name">Child</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"나는 Child 기본 생성자"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h4 id="child-인스턴스-생성-시-parent-기본-생성자-자동-실행">Child 인스턴스 생성 시 Parent 기본 생성자 자동 실행</h4>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">Child</span> child <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Child</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p><strong>출력 결과</strong></p>
<pre><code class="language-shell">나는 Parent 기본 생성자
나는 Child 기본 생성자</code></pre>
<h2 id="추상-클래스의-상속-관계">추상 클래스의 상속 관계</h2>
<p>분리된 추상 클래스를 만드는 이유 중 하나는 개념적으로 관련 있는 일부 기능을 한 곳에 모으는 것입니다. 하나의 추상 클래스가 지나치게 커지거나, 여러 목적을 위한 다양한 초기화를 처리해야 하는 경우에 이를 분리하여 관리하는 것이 가독성과 유지보수성을 높일 수 있습니다.</p>
<h2 id="protected와-super">protected와 super()</h2>
<h3 id="defaultserializerprovider-추상클래스">DefaultSerializerProvider 추상클래스</h3>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">abstract</span> <span class="token keyword">class</span> <span class="token class-name">DefaultSerializerProvider</span> <span class="token keyword">extends</span> <span class="token class-name">SerializerProvider</span> <span class="token keyword">implements</span> <span class="token class-name">Serializable</span> <span class="token punctuation">{</span>
    <span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token keyword">long</span> serialVersionUID <span class="token operator">=</span> <span class="token number">1L</span><span class="token punctuation">;</span>
    <span class="token keyword">protected</span> <span class="token keyword">transient</span> <span class="token class-name">Map</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Object</span><span class="token punctuation">,</span> <span class="token class-name">WritableObjectId</span><span class="token punctuation">&gt;</span></span> _seenObjectIds<span class="token punctuation">;</span>
    <span class="token keyword">protected</span> <span class="token keyword">transient</span> <span class="token class-name">ArrayList</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">ObjectIdGenerator</span><span class="token punctuation">&lt;</span><span class="token operator">?</span><span class="token punctuation">&gt;</span><span class="token punctuation">&gt;</span></span> _objectIdGenerators<span class="token punctuation">;</span>
    <span class="token keyword">protected</span> <span class="token keyword">transient</span> <span class="token class-name">JsonGenerator</span> _generator<span class="token punctuation">;</span>

    <span class="token keyword">protected</span> <span class="token class-name">DefaultSerializerProvider</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">protected</span> <span class="token class-name">DefaultSerializerProvider</span><span class="token punctuation">(</span><span class="token class-name">SerializerProvider</span> src<span class="token punctuation">,</span> <span class="token class-name">SerializationConfig</span> config<span class="token punctuation">,</span> <span class="token class-name">SerializerFactory</span> f<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">super</span><span class="token punctuation">(</span>src<span class="token punctuation">,</span> config<span class="token punctuation">,</span> f<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span></code></pre>
<p> 추상 클래스는 인스턴스화될 수 없기 때문에 자체적인 인스턴스 생성이 불가능합니다. 하지만 추상 클래스 역시 생성자를 가질 수 있습니다.<br>
추상 클래스의 생성자는 <code>protected</code>로 선언되며, 외부에서 직접 호출할 수 없고 하위 클래스 내부에서 <code>super()</code>를 통해 호출됩니다. 하위 클래스에서 반드시 호출해야 하는 초기화 로직이 있을 때, 이러한 목적으로 추상 클래스 내부에 생성자를 구현할 수 있습니다.</p>
<p><code>SerializerProvider</code> 클래스의 생성자인 <code>protected SerializerProvider(SerializerProvider src, SerializationConfig config, SerializerFactory f)</code>는 자식 클래스에서 사용 가능하도록 <code>protected</code>로 선언되어 있습니다. 자식 클래스에서 부모 클래스의 특정 상태를 초기화하거나 확장할 수 있습니다. </p>
<h2 id="같은-클래스-객체를-매개변수로-사용하는-생성자">같은 클래스 객체를 매개변수로 사용하는 생성자</h2>
<h3 id="serializerprovider-추상클래스">SerializerProvider 추상클래스</h3>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">abstract</span> <span class="token keyword">class</span> <span class="token class-name">SerializerProvider</span> <span class="token keyword">extends</span> <span class="token class-name">DatabindContext</span> <span class="token punctuation">{</span>

<span class="token comment">//...중략</span>

    <span class="token keyword">protected</span> <span class="token class-name">SerializerProvider</span><span class="token punctuation">(</span><span class="token class-name">SerializerProvider</span> src<span class="token punctuation">,</span> <span class="token class-name">SerializationConfig</span> config<span class="token punctuation">,</span> <span class="token class-name">SerializerFactory</span> f<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>_unknownTypeSerializer <span class="token operator">=</span> DEFAULT_UNKNOWN_SERIALIZER<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>_nullValueSerializer <span class="token operator">=</span> <span class="token class-name">NullSerializer</span><span class="token punctuation">.</span>instance<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>_nullKeySerializer <span class="token operator">=</span> DEFAULT_NULL_KEY_SERIALIZER<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>_serializerFactory <span class="token operator">=</span> f<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>_config <span class="token operator">=</span> config<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>_serializerCache <span class="token operator">=</span> src<span class="token punctuation">.</span>_serializerCache<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>_unknownTypeSerializer <span class="token operator">=</span> src<span class="token punctuation">.</span>_unknownTypeSerializer<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>_keySerializer <span class="token operator">=</span> src<span class="token punctuation">.</span>_keySerializer<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>_nullValueSerializer <span class="token operator">=</span> src<span class="token punctuation">.</span>_nullValueSerializer<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>_nullKeySerializer <span class="token operator">=</span> src<span class="token punctuation">.</span>_nullKeySerializer<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>_stdNullValueSerializer <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">.</span>_nullValueSerializer <span class="token operator">==</span> DEFAULT_NULL_KEY_SERIALIZER<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>_serializationView <span class="token operator">=</span> config<span class="token punctuation">.</span><span class="token function">getActiveView</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>_attributes <span class="token operator">=</span> config<span class="token punctuation">.</span><span class="token function">getAttributes</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>_knownSerializers <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">.</span>_serializerCache<span class="token punctuation">.</span><span class="token function">getReadOnlyLookupMap</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    <span class="token comment">//... 생략</span></code></pre>
<p>주어진 생성자 코드에서 매개변수로 받은 <code>SerializerProvider src</code>는 생성자의 매개변수로 전달된 것으로, 해당 생성자 내에서 <code>SerializerProvider</code> 타입의 객체를 새로운 인스턴스로 생성하는 것이 아니라, 이미 생성된 <code>src</code> 객체의 참조를 받고 있습니다. </p>
<p><code>this</code>는 현재 객체의 참조를 나타내며, 생성자 내부에서 <code>this</code>는 현재 객체를 가리킵니다. <code>SerializerProvider src</code>는 외부에서 전달된 다른 객체를 가리키며, 이를 생성자 내부에서 사용할 수 있게 됩니다.</p>
<p>따라서 <code>this</code>와 <code>src</code>는 서로 다른 객체를 가리키며, 생성자 내부에서 각각의 객체를 참조하는데 사용됩니다. <code>this</code>는 현재 인스턴스를 나타내고, <code>src</code>는 생성자를 호출할 때 전달된 다른 <code>SerializerProvider</code> 객체를 가리킵니다.</p>
<h3 id="생성자를-이용한-인스턴스의-복사">생성자를 이용한 인스턴스의 복사</h3>
<p><a href="https://blog.naver.com/29java/70187757119">https://blog.naver.com/29java/70187757119</a></p>
<pre><code class="language-java"><span class="token keyword">class</span> <span class="token class-name">Car</span> <span class="token punctuation">{</span>
    <span class="token class-name">String</span> color<span class="token punctuation">;</span>    <span class="token comment">// 생상</span>
    <span class="token class-name">String</span> gearType<span class="token punctuation">;</span>    <span class="token comment">// 변속기 종류 - auto(자동), manual(수동)    </span>
    <span class="token keyword">int</span> door<span class="token punctuation">;</span>    <span class="token comment">// 문의 개수</span>
    
    <span class="token class-name">Car</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">(</span><span class="token string">"white"</span><span class="token punctuation">,</span> <span class="token string">"auto"</span><span class="token punctuation">,</span> <span class="token number">4</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
    <span class="token class-name">Car</span><span class="token punctuation">(</span><span class="token class-name">Car</span> c<span class="token punctuation">)</span> <span class="token punctuation">{</span>    <span class="token comment">// 인스턴스의 복사를 위한 생성자</span>
        color <span class="token operator">=</span> c<span class="token punctuation">.</span>color<span class="token punctuation">;</span>
        gearType <span class="token operator">=</span> c<span class="token punctuation">.</span>gearType<span class="token punctuation">;</span>
        door <span class="token operator">=</span> c<span class="token punctuation">.</span>door<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
    <span class="token class-name">Car</span><span class="token punctuation">(</span><span class="token class-name">String</span> color<span class="token punctuation">,</span> <span class="token class-name">String</span> gearType<span class="token punctuation">,</span> <span class="token keyword">int</span> door<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>color <span class="token operator">=</span> color<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>gearType <span class="token operator">=</span> gearType<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>door <span class="token operator">=</span> door<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
<span class="token keyword">class</span> <span class="token class-name">CarTest3</span> <span class="token punctuation">{</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">Car</span> c1 <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Car</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">Car</span> c2 <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Car</span><span class="token punctuation">(</span>c1<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// c1의 복사본 c2를 생성한다.</span>
        <span class="token comment">// 이하 생략</span></code></pre>
<h2 id="추상클래스-내부에서-해당-추상클래스를-상속하는-final-클래스">추상클래스 내부에서 해당 추상클래스를 상속하는 final 클래스</h2>
<p><a href="https://fasterxml.github.io/jackson-databind/javadoc/2.8/com/fasterxml/jackson/databind/ser/DefaultSerializerProvider.html">DefaultSerializerProvider</a></p>
<p><a href="https://fasterxml.github.io/jackson-databind/javadoc/2.8/com/fasterxml/jackson/databind/ser/DefaultSerializerProvider.Impl.html">DefaultSerializerProvider.Impl</a></p>
<h3 id="defaultserializerprovider">DefaultSerializerProvider</h3>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">abstract</span> <span class="token keyword">class</span> <span class="token class-name">DefaultSerializerProvider</span> <span class="token keyword">extends</span> <span class="token class-name">SerializerProvider</span> <span class="token keyword">implements</span> <span class="token class-name">Serializable</span> <span class="token punctuation">{</span>

<span class="token comment">//... 중략</span>

    <span class="token keyword">protected</span> <span class="token class-name">DefaultSerializerProvider</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">protected</span> <span class="token class-name">DefaultSerializerProvider</span><span class="token punctuation">(</span><span class="token class-name">SerializerProvider</span> src<span class="token punctuation">,</span> <span class="token class-name">SerializationConfig</span> config<span class="token punctuation">,</span> <span class="token class-name">SerializerFactory</span> f<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">super</span><span class="token punctuation">(</span>src<span class="token punctuation">,</span> config<span class="token punctuation">,</span> f<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">protected</span> <span class="token class-name">DefaultSerializerProvider</span><span class="token punctuation">(</span><span class="token class-name">DefaultSerializerProvider</span> src<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">super</span><span class="token punctuation">(</span>src<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>


    <span class="token keyword">public</span> <span class="token keyword">abstract</span> <span class="token class-name">DefaultSerializerProvider</span> <span class="token function">createInstance</span><span class="token punctuation">(</span><span class="token class-name">SerializationConfig</span> var1<span class="token punctuation">,</span> <span class="token class-name">SerializerFactory</span> var2<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">DefaultSerializerProvider</span> <span class="token function">copy</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">IllegalStateException</span><span class="token punctuation">(</span><span class="token string">"DefaultSerializerProvider sub-class not overriding copy()"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token comment">//...생략</span>
    </code></pre>
<h3 id="defaultserializerproviderimpl">DefaultSerializerProvider.Impl</h3>
<pre><code class="language-java"><span class="token comment">//...생략</span>
<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token keyword">class</span> <span class="token class-name">Impl</span> <span class="token keyword">extends</span> <span class="token class-name">DefaultSerializerProvider</span> <span class="token punctuation">{</span>
        <span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token keyword">long</span> serialVersionUID <span class="token operator">=</span> <span class="token number">1L</span><span class="token punctuation">;</span>

        <span class="token keyword">public</span> <span class="token class-name">Impl</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token punctuation">}</span>

        <span class="token keyword">public</span> <span class="token class-name">Impl</span><span class="token punctuation">(</span><span class="token class-name">Impl</span> src<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">super</span><span class="token punctuation">(</span>src<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        <span class="token keyword">protected</span> <span class="token class-name">Impl</span><span class="token punctuation">(</span><span class="token class-name">SerializerProvider</span> src<span class="token punctuation">,</span> <span class="token class-name">SerializationConfig</span> config<span class="token punctuation">,</span> <span class="token class-name">SerializerFactory</span> f<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">super</span><span class="token punctuation">(</span>src<span class="token punctuation">,</span> config<span class="token punctuation">,</span> f<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        <span class="token keyword">public</span> <span class="token class-name">DefaultSerializerProvider</span> <span class="token function">copy</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">return</span> <span class="token punctuation">(</span><span class="token class-name">DefaultSerializerProvider</span><span class="token punctuation">)</span><span class="token punctuation">(</span><span class="token keyword">this</span><span class="token punctuation">.</span><span class="token function">getClass</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">!=</span> <span class="token class-name">Impl</span><span class="token punctuation">.</span><span class="token keyword">class</span> <span class="token operator">?</span> <span class="token keyword">super</span><span class="token punctuation">.</span><span class="token function">copy</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">:</span> <span class="token keyword">new</span> <span class="token class-name">Impl</span><span class="token punctuation">(</span><span class="token keyword">this</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        <span class="token keyword">public</span> <span class="token class-name">Impl</span> <span class="token function">createInstance</span><span class="token punctuation">(</span><span class="token class-name">SerializationConfig</span> config<span class="token punctuation">,</span> <span class="token class-name">SerializerFactory</span> jsf<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">Impl</span><span class="token punctuation">(</span><span class="token keyword">this</span><span class="token punctuation">,</span> config<span class="token punctuation">,</span> jsf<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<h3 id="defaultdeserializationcontext">DefaultDeserializationContext</h3>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">abstract</span> <span class="token keyword">class</span> <span class="token class-name">DefaultDeserializationContext</span> <span class="token keyword">extends</span> <span class="token class-name">DeserializationContext</span> <span class="token keyword">implements</span> <span class="token class-name">Serializable</span> <span class="token punctuation">{</span>
    <span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token keyword">long</span> serialVersionUID <span class="token operator">=</span> <span class="token number">1L</span><span class="token punctuation">;</span>
    <span class="token keyword">protected</span> <span class="token keyword">transient</span> <span class="token class-name">LinkedHashMap</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">ObjectIdGenerator</span><span class="token punctuation">.</span><span class="token class-name">IdKey</span><span class="token punctuation">,</span> <span class="token class-name">ReadableObjectId</span><span class="token punctuation">&gt;</span></span> _objectIds<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">ObjectIdResolver</span><span class="token punctuation">&gt;</span></span> _objectIdResolvers<span class="token punctuation">;</span>

    <span class="token keyword">protected</span> <span class="token class-name">DefaultDeserializationContext</span><span class="token punctuation">(</span><span class="token class-name">DeserializerFactory</span> df<span class="token punctuation">,</span> <span class="token class-name">DeserializerCache</span> cache<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">super</span><span class="token punctuation">(</span>df<span class="token punctuation">,</span> cache<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">protected</span> <span class="token class-name">DefaultDeserializationContext</span><span class="token punctuation">(</span><span class="token class-name">DefaultDeserializationContext</span> src<span class="token punctuation">,</span> <span class="token class-name">DeserializationConfig</span> config<span class="token punctuation">,</span> <span class="token class-name">JsonParser</span> p<span class="token punctuation">,</span> <span class="token class-name">InjectableValues</span> values<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">super</span><span class="token punctuation">(</span>src<span class="token punctuation">,</span> config<span class="token punctuation">,</span> p<span class="token punctuation">,</span> values<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token comment">//... 생략</span></code></pre>
<h3 id="serializable-인터페이스">Serializable 인터페이스</h3>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">java<span class="token punctuation">.</span>io</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">Serializable</span> <span class="token punctuation">{</span>
<span class="token punctuation">}</span></code></pre>
<p>Serializable 인터페이스는 마킹 인터페이스입니다. 어떤 클래스가 직렬화될 수 있다는 것을 나타내는데, 메서드를 가지지 않고 단순히 해당 클래스가 직렬화 가능하다는 표시를 합니다. 이를 통해 객체를 파일에 저장하거나 네트워크로 전송할 수 있도록 합니다.</p>
<h3 id="versioned-인터페이스">Versioned 인터페이스</h3>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">com<span class="token punctuation">.</span>fasterxml<span class="token punctuation">.</span>jackson<span class="token punctuation">.</span>core</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">Versioned</span> <span class="token punctuation">{</span>
    <span class="token class-name">Version</span> <span class="token function">version</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>Versioned 인터페이스에서 version() 메서드를 통해 Version 클래스의 버전 정보를 반환하는 것은 느슨한 결합을 유지하기 위함일 수 있습니다. 이렇게 함으로써 Version 클래스의 변경이 Versioned 인터페이스의 구현에 영향을 덜 주면서도 버전 정보를 요구하는 부분에서 해당 기능을 사용할 수 있습니다. 또한, Version 클래스를 직접 사용하지 않고 Versioned 인터페이스를 통해 버전 정보에 접근하면, 다른 버전 정보를 제공하는 클래스로 손쉽게 교체할 수 있습니다.</p>
<h3 id="version-클래스">Version 클래스</h3>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">com<span class="token punctuation">.</span>fasterxml<span class="token punctuation">.</span>jackson<span class="token punctuation">.</span>core</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>io</span><span class="token punctuation">.</span><span class="token class-name">Serializable</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Version</span> <span class="token keyword">implements</span> <span class="token class-name">Comparable</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Version</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">,</span> <span class="token class-name">Serializable</span> <span class="token punctuation">{</span>

<span class="token comment">//... 중략</span>

    <span class="token keyword">public</span> <span class="token class-name">Version</span><span class="token punctuation">(</span><span class="token keyword">int</span> major<span class="token punctuation">,</span> <span class="token keyword">int</span> minor<span class="token punctuation">,</span> <span class="token keyword">int</span> patchLevel<span class="token punctuation">,</span> <span class="token class-name">String</span> snapshotInfo<span class="token punctuation">,</span> <span class="token class-name">String</span> groupId<span class="token punctuation">,</span> <span class="token class-name">String</span> artifactId<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>_majorVersion <span class="token operator">=</span> major<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>_minorVersion <span class="token operator">=</span> minor<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>_patchLevel <span class="token operator">=</span> patchLevel<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>_snapshotInfo <span class="token operator">=</span> snapshotInfo<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>_groupId <span class="token operator">=</span> groupId <span class="token operator">==</span> <span class="token keyword">null</span> <span class="token operator">?</span> <span class="token string">""</span> <span class="token operator">:</span> groupId<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>_artifactId <span class="token operator">=</span> artifactId <span class="token operator">==</span> <span class="token keyword">null</span> <span class="token operator">?</span> <span class="token string">""</span> <span class="token operator">:</span> artifactId<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token comment">//... 중략    </span>

    <span class="token keyword">public</span> <span class="token keyword">int</span> <span class="token function">compareTo</span><span class="token punctuation">(</span><span class="token class-name">Version</span> other<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span>other <span class="token operator">==</span> <span class="token keyword">this</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token punctuation">{</span>
            <span class="token keyword">int</span> diff <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">.</span>_groupId<span class="token punctuation">.</span><span class="token function">compareTo</span><span class="token punctuation">(</span>other<span class="token punctuation">.</span>_groupId<span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token keyword">if</span> <span class="token punctuation">(</span>diff <span class="token operator">==</span> <span class="token number">0</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
                diff <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">.</span>_artifactId<span class="token punctuation">.</span><span class="token function">compareTo</span><span class="token punctuation">(</span>other<span class="token punctuation">.</span>_artifactId<span class="token punctuation">)</span><span class="token punctuation">;</span>
                <span class="token keyword">if</span> <span class="token punctuation">(</span>diff <span class="token operator">==</span> <span class="token number">0</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
                    diff <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">.</span>_majorVersion <span class="token operator">-</span> other<span class="token punctuation">.</span>_majorVersion<span class="token punctuation">;</span>
                    <span class="token keyword">if</span> <span class="token punctuation">(</span>diff <span class="token operator">==</span> <span class="token number">0</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
                        diff <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">.</span>_minorVersion <span class="token operator">-</span> other<span class="token punctuation">.</span>_minorVersion<span class="token punctuation">;</span>
                        <span class="token keyword">if</span> <span class="token punctuation">(</span>diff <span class="token operator">==</span> <span class="token number">0</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
                            diff <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">.</span>_patchLevel <span class="token operator">-</span> other<span class="token punctuation">.</span>_patchLevel<span class="token punctuation">;</span>
                        <span class="token punctuation">}</span>
                    <span class="token punctuation">}</span>
                <span class="token punctuation">}</span>
            <span class="token punctuation">}</span>

            <span class="token keyword">return</span> diff<span class="token punctuation">;</span>
<span class="token comment">//... 생략</span></code></pre>
<h3 id="comparable-인터페이스">Comparable 인터페이스</h3>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">java<span class="token punctuation">.</span>lang</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util</span><span class="token punctuation">.</span>*<span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">Comparable</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>
    <span class="token keyword">public</span> <span class="token keyword">int</span> <span class="token function">compareTo</span><span class="token punctuation">(</span><span class="token class-name">T</span> o<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h1 id=""></h1>