<h2 id="키워드-사용-범위">키워드 사용 범위</h2>
<table><thead><tr><th>키워드</th><th>사용 가능한 범위</th></tr></thead><tbody><tr><td>public</td><td>클래스, 인터페이스, 메서드, 필드</td></tr><tr><td>private</td><td>메서드, 필드, 내부 클래스</td></tr><tr><td>protected</td><td>메서드, 필드, 내부 클래스 (동일 패키지 내에서 상속받은 클래스에서도 사용 가능)</td></tr><tr><td>static</td><td>메서드, 필드, 내부 클래스 (탑레벨에서는 사용 불가능)</td></tr><tr><td>final</td><td>클래스, 메서드, 필드, 매개변수 (변경 불가능한 변수, 메서드 오버라이딩 방지, 클래스 확장 방지)</td></tr><tr><td>abstract</td><td>클래스, 메서드, 내부 클래스 (추상 클래스 및 추상 메서드 선언)</td></tr><tr><td>synchronized</td><td>메서드, 블록</td></tr><tr><td>volatile</td><td>필드</td></tr><tr><td>transient</td><td>필드</td></tr><tr><td>default</td><td>인터페이스 메서드 (인터페이스의 기본 구현 제공)</td></tr><tr><td>native</td><td>메서드 (네이티브 메서드, C/C++로 작성된 코드를 자바에서 호출할 때 사용)</td></tr><tr><td>strictfp</td><td>클래스, 메서드 (실수 연산 시 동일한 결과를 보장하기 위한 키워드)</td></tr><tr><td>assert</td><td>메서드 (프로그램의 상태를 검증하기 위한 키워드)</td></tr><tr><td>interface</td><td>탑레벨에서만 사용되는 인터페이스 선언 키워드</td></tr><tr><td>enum</td><td>탑레벨에서만 사용되는 열거형 선언 키워드</td></tr></tbody></table>
<p><strong>필드</strong> : 로컬 변수, 클래스 변수, 인스턴스 변수</p>
<h2 id="값이-결정되는-시점">값이 결정되는 시점</h2>
<table><thead><tr><th><strong>컴파일 타임에 값이 정해짐</strong></th><th><strong>런타임에 값이 정해짐</strong></th></tr></thead><tbody><tr><td>리터럴(상수, 직접 입력한 값)</td><td>사용자 입력 (실행 중 사용자가 입력한 값)</td></tr><tr><td>상수 (final 변수)</td><td>외부 데이터 소스(파일, 데이터베이스 등)로부터 가져온 값</td></tr><tr><td>컴파일 시점에 결정된 값 (조건문, 반복문 등)</td><td>네트워크에서 받은 데이터</td></tr><tr><td>Enum 상수</td><td>실행 중에 동적으로 생성된 값 (동적 할당, Reflection 등으로 생성된 값)</td></tr><tr><td>정적(final) 필드 초기화 (클래스 로딩 시)</td><td>런타임에 결정되는 특정 시점의 환경 변수 및 외부 설정 파일 등</td></tr><tr><td>제네릭 (컴파일 시에 제한된 타입 사용)</td><td>와일드카드 (실행 중에 특정 타입에 제한 없이 동작할 수 있는 타입)</td></tr><tr><td>어노테이션 (컴파일 타임에 소스 코드에 메타데이터 추가)</td><td>리플렉션 (런타임에 클래스나 메서드 등의 정보를 분석 및 조작)</td></tr><tr><td>빈 (스프링 프레임워크에서 컨테이너에 의해 관리되는 객체)</td><td>동적 생성된 빈 (런타임에 DI 컨테이너에 의해 동적으로 생성 및 관리)</td></tr></tbody></table>
<h2 id="팩토리-메서드">팩토리 메서드</h2>
<ol>
<li>
<p><strong>정적 팩토리 메서드(Static Factory Method)</strong>:</p>
<ul>
<li><strong>정적(static) 메서드</strong>로, 해당 클래스의 인스턴스를 반환하는 메서드입니다.</li>
<li>보통 클래스의 생성자 대신에 사용되며, 객체 생성 시에 생성자 대신에 이러한 정적 메서드를 호출합니다.</li>
<li><code>valueOf()</code>, <code>of()</code>, <code>getInstance()</code> 등과 같은 널리 사용되는 명명 규칙이 있습니다.</li>
<li>예시:<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyClass</span> <span class="token punctuation">{</span>
    <span class="token keyword">private</span> <span class="token class-name">MyClass</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token comment">// private 생성자</span>
    <span class="token punctuation">}</span>
    
    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token class-name">MyClass</span> <span class="token function">createInstance</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">MyClass</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre></li>
</ul>
</li>
<li>
<p><strong>동적 팩토리 메서드(Instance Factory Method)</strong>:</p>
<ul>
<li><strong>인스턴스 메서드</strong>로, 이미 생성된 객체의 인스턴스를 반환하는 메서드입니다.</li>
<li>주로 인스턴스화된 객체가 다른 객체의 인스턴스를 생성하는 데 사용됩니다.</li>
<li>예시:<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyClass</span> <span class="token punctuation">{</span>
    <span class="token keyword">public</span> <span class="token class-name">AnotherClass</span> <span class="token function">createAnotherInstance</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">AnotherClass</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre></li>
</ul>
</li>
</ol>
<p>이러한 팩토리 메서드들은 객체 생성을 조율하고 제어하기 위해 사용됩니다. 정적 팩토리 메서드는 객체 생성에 유연성을 제공하고, 생성자와 달리 호출될 때마다 항상 새로운 객체를 생성할 필요가 없습니다. 반면, 동적 팩토리 메서드는 이미 생성된 객체를 이용하여 새로운 객체를 생성하거나 다른 타입의 객체를 반환할 수 있습니다.</p>
<h2 id="supertype--subtype">SuperType / SubType</h2>
<p><img src="https://www3.ntu.edu.sg/home/ehchua/programming/java/images/OOP_WrapperClass.png"></p>
<h2 id="형변환--캐스팅">형변환 / 캐스팅</h2>
<p><img src="https://media.geeksforgeeks.org/wp-content/uploads/20210119153851/UpcastingVsDowncasting.jpg"></p>
<h2 id="primitive-type--reference-type">Primitive Type / Reference Type</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/2ba930f5-0f6e-4328-b22e-b07c578693dd/image.png"></p>
<h2 id="autoboxing--unboxing">Autoboxing / Unboxing</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/f3f282a0-823c-4c5b-90bf-e8280d0b131a/image.png"></p>
<h3 id="issue">Issue</h3>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/af5ab67b-bd08-4aca-8a94-d709b400caee/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/012c8427-460f-4151-bd09-a5ede5c279d3/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/3450d43f-ab2e-4858-809f-072f075737e9/image.png"></p>
<h2 id="제네릭의-특성">제네릭의 특성</h2>
<ol>
<li>
<p><strong>타입 제한(Bounded Type)</strong>: 특정 타입 또는 특정 타입의 하위 타입만을 허용할 수 있도록 제한할 수 있습니다.</p>
</li>
<li>
<p><strong>와일드카드(Wildcards)</strong>: <code>&lt;?&gt;</code>, <code>&lt;? extends T&gt;</code>, <code>&lt;? super T&gt;</code>와 같은 표현을 통해 유연한 타입 매개변수 사용이 가능합니다.</p>
</li>
<li>
<p><strong>타입 소거(Type Erasure)</strong>: 컴파일 후 제네릭 타입의 정보는 소거되고, 필요한 경우 컴파일러는 타입 캐스팅을 추가하여 타입 안정성을 유지합니다.</p>
</li>
<li>
<p><strong>제네릭 메서드(Generic Methods)</strong>: 단일 메서드에 대해 제네릭 타입을 선언할 수 있습니다.</p>
</li>
</ol>
<h3 id="와일드-카드">와일드 카드</h3>
<p>제네릭 타입을 선언할 때 <code>?</code>를 사용하여 특정한 타입을 제한하는 데에 활용됩니다.</p>
<ul>
<li>
<p>Unbounded Wildcard (<code>&lt;?&gt;</code>): 어떤 타입이든지 사용 가능한 와일드카드입니다. 예를 들어, <code>List&lt;?&gt;</code>는 <code>List&lt;String&gt;</code>, <code>List&lt;Integer&gt;</code>, <code>List&lt;Object&gt;</code> 등의 모든 종류의 리스트를 포함할 수 있습니다.</p>
</li>
<li>
<p>Upper Bounded Wildcard (<code>&lt;? extends 타입&gt;</code>): 특정한 상한을 가지는 와일드카드입니다. 예를 들어, <code>List&lt;? extends Number&gt;</code>는 Number 클래스를 확장한 클래스들 (Integer, Double 등)을 포함하는 리스트를 의미합니다.</p>
</li>
<li>
<p>Lower Bounded Wildcard (<code>&lt;? super 타입&gt;</code>): 특정한 하한을 가지는 와일드카드입니다. 예를 들어, <code>List&lt;? super Integer&gt;</code>는 Integer 클래스나 Integer가 상속한 클래스 (Number)의 리스트를 포함합니다.</p>
</li>
</ul>
<h3 id="다이아몬드-연산자">다이아몬드 연산자</h3>
<p>꺾쇠 괄호(<code>&lt;</code>와 <code>&gt;</code>)를 자바에서는 "다이아몬드 연산자"라고 부르기도 합니다. 다이아몬드 연산자는 주로 제네릭 타입을 사용할 때 타입 추론을 돕는데 사용됩니다.</p>
<pre><code class="language-java"><span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> myList <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ArrayList</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>생성자 호출에서는 타입을 직접 명시할 수 없으며, 이 연산자를 사용하여 타입 추론을 유도하는 것이 일반적입니다.</p>
<h3 id="타입-소거">타입 소거</h3>
<p>제네릭의 타입 소거는 컴파일 시에 제네릭 타입에 대한 정보가 삭제되는 것을 의미합니다. 컴파일러는 타입 소거를 통해 제네릭 타입에 대한 정보를 유지하지 않고, 기존 코드를 변환하여 호환성을 유지합니다.</p>
<p>예를 들어, 다음과 같은 제네릭 클래스가 있다고 가정해봅시다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Example</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>
    <span class="token keyword">private</span> <span class="token class-name">T</span> data<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">Example</span><span class="token punctuation">(</span><span class="token class-name">T</span> data<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>data <span class="token operator">=</span> data<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token class-name">T</span> <span class="token function">getData</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> data<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이 클래스를 컴파일하면 컴파일러는 타입 소거를 수행합니다. 실제로 컴파일된 코드에서는 제네릭 타입 정보가 사라지고, Object 타입으로 변환됩니다.</p>
<p>컴파일된 코드는 대략적으로 다음과 같을 것입니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Example</span> <span class="token punctuation">{</span>
    <span class="token keyword">private</span> <span class="token class-name">Object</span> data<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">Example</span><span class="token punctuation">(</span><span class="token class-name">Object</span> data<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>data <span class="token operator">=</span> data<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token class-name">Object</span> <span class="token function">getData</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> data<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이 때문에 컴파일 시점에서는 제네릭 타입 정보가 없으므로, 런타임에 타입 캐스팅을 추가하여 타입 안정성을 유지합니다. 사용자가 <code>getData()</code>를 호출할 때 실제로는 캐스팅이 발생합니다.</p>
<pre><code class="language-java"><span class="token class-name">Example</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> example <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Example</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token string">"Hello"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token class-name">String</span> value <span class="token operator">=</span> example<span class="token punctuation">.</span><span class="token function">getData</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// 컴파일러는 getData()에서 Object를 String으로 캐스팅합니다.</span></code></pre>
<p>이처럼 컴파일러는 타입 소거로 실제 제네릭 타입 정보를 삭제하지만, 코드를 변환하여 타입 안정성을 유지하고 호환성을 보장합니다.</p>
<h3 id="제네릭-메서드">제네릭 메서드</h3>
<p>제네릭 메서드는 특정 메서드 내에서만 사용되는 제네릭 타입을 선언할 수 있게 해줍니다. 이를 통해 해당 메서드에서 다양한 타입의 데이터를 다룰 수 있습니다.</p>
<p>예를 들어, 다음은 배열에서 특정 위치의 두 요소를 교환하는 제네릭 메서드의 예시입니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token keyword">void</span> <span class="token function">swap</span><span class="token punctuation">(</span><span class="token class-name">T</span><span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token punctuation">[</span><span class="token punctuation">]</span> array<span class="token punctuation">,</span> <span class="token keyword">int</span> i<span class="token punctuation">,</span> <span class="token keyword">int</span> j<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">if</span> <span class="token punctuation">(</span>i <span class="token operator">&gt;=</span> <span class="token number">0</span> <span class="token operator">&amp;&amp;</span> i <span class="token generics"><span class="token punctuation">&lt;</span> array<span class="token punctuation">.</span>length <span class="token operator">&amp;</span><span class="token operator">&amp;</span> j <span class="token punctuation">&gt;</span></span><span class="token operator">=</span> <span class="token number">0</span> <span class="token operator">&amp;&amp;</span> j <span class="token operator">&lt;</span> array<span class="token punctuation">.</span>length <span class="token operator">&amp;&amp;</span> array<span class="token punctuation">[</span>i<span class="token punctuation">]</span><span class="token punctuation">.</span>length <span class="token operator">==</span> array<span class="token punctuation">[</span>j<span class="token punctuation">]</span><span class="token punctuation">.</span>length<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">T</span><span class="token punctuation">[</span><span class="token punctuation">]</span> temp <span class="token operator">=</span> array<span class="token punctuation">[</span>i<span class="token punctuation">]</span><span class="token punctuation">;</span>
        array<span class="token punctuation">[</span>i<span class="token punctuation">]</span> <span class="token operator">=</span> array<span class="token punctuation">[</span>j<span class="token punctuation">]</span><span class="token punctuation">;</span>
        array<span class="token punctuation">[</span>j<span class="token punctuation">]</span> <span class="token operator">=</span> temp<span class="token punctuation">;</span>
    <span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token punctuation">{</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"Invalid indices or mismatched array sizes"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이 메서드는 <code>T</code>라는 제네릭 타입을 선언하고, 이 메서드 안에서만 사용됩니다. 호출할 때마다 전달되는 실제 타입에 따라 동작하며, 배열의 원소를 교환하는 일반적인 메서드로 사용될 수 있습니다.</p>
<pre><code class="language-java"><span class="token class-name">Integer</span><span class="token punctuation">[</span><span class="token punctuation">]</span> intArray <span class="token operator">=</span> <span class="token punctuation">{</span><span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">,</span> <span class="token number">4</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
<span class="token class-name">SwapUtil</span><span class="token punctuation">.</span><span class="token function">swap</span><span class="token punctuation">(</span>intArray<span class="token punctuation">,</span> <span class="token number">0</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// 배열의 첫 번째와 세 번째 요소를 교환</span>

<span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> strArray <span class="token operator">=</span> <span class="token punctuation">{</span><span class="token string">"Hello"</span><span class="token punctuation">,</span> <span class="token string">"World"</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
<span class="token class-name">SwapUtil</span><span class="token punctuation">.</span><span class="token function">swap</span><span class="token punctuation">(</span>strArray<span class="token punctuation">,</span> <span class="token number">0</span><span class="token punctuation">,</span> <span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// 배열의 첫 번째와 두 번째 요소를 교환</span>

<span class="token comment">// 이렇게 다양한 타입의 배열에서도 동작합니다.</span></code></pre>
<p>이와 같이 제네릭 메서드를 사용하면 같은 로직을 여러 타입에 대해 재사용할 수 있으며, 타입 안정성을 유지하면서 유연한 코드를 작성할 수 있습니다.</p>
<h3 id="제네릭-사용-위치와-컴파일러의-심볼-해석">제네릭 사용 위치와 컴파일러의 심볼 해석</h3>
<h4 id="cannot-resolve-symbol-t">cannot resolve symbol T</h4>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/61b4d2db-8131-4c4e-a09a-8452790a97d7/image.png"></p>
<p><code>cannot resolve symbol T</code> 오류는 컴파일러가 <code>T</code>라는 심볼(symbol)을 찾을 수 없다는 것을 나타냅니다. 이는 컴파일러가 해당 심볼 <code>T</code>에 대한 정보를 알지 못해 발생하는 오류입니다.</p>
<p>Java에서는 제네릭 타입을 사용할 때, 해당 타입을 선언해야 합니다. 위의 코드에서 <code>T</code>는 메서드 레벨에서 선언되지 않았으며, 클래스 레벨에서도 선언되지 않았기 때문에 컴파일러가 <code>T</code>에 대한 정보를 알지 못합니다.</p>
<h4 id="classnamethis-cannot-be-referenced-from-a-static-context">ClassName.this cannot be referenced from a static context</h4>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/d16446a0-6c0e-4b15-bbff-0e6c788a0148/image.png"></p>
<p>이 코드에서 문제가 발생하는 이유는 <code>createList</code> 메서드가 <code>static</code>으로 선언되어 있지만, 해당 메서드가 <code>T</code>를 사용하려고 하기 때문입니다.</p>
<p><code>static</code> 메서드는 클래스 레벨에 속하며, 인스턴스화되지 않은 상태에서도 호출될 수 있습니다. 하지만 <code>T</code>는 제네릭 타입으로, 해당 타입은 클래스가 실제로 인스턴스화될 때까지 결정되지 않습니다. 그래서 <code>static</code> 메서드에서는 <code>T</code>에 접근할 수 없는데, 이 때문에 컴파일러가 오류를 발생시키는 것입니다.</p>
<p>해결 방법 중 하나는 <code>static</code> 키워드를 제거하여 해당 메서드를 인스턴스 메서드로 변경하는 것입니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/7f186b77-6908-45f9-879d-432d1d32a2b6/image.png"></p>
<h4 id="메서드-레벨에서-제네릭-타입-선언">메서드 레벨에서 제네릭 타입 선언</h4>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/9d7ff20a-6622-414b-b481-415fa7ab8378/image.png"></p>
<p><code>static &lt;T&gt; List&lt;T&gt; createList(T element)</code>에서의 메서드 레벨에서 선언된 <code>&lt;T&gt;</code>는 해당 메서드 내에서만 유효한 새로운 제네릭 타입으로 처리되기 때문에 클래스 레벨에서 선언된 <code>OurClass</code>의 <code>T</code>와는 관련이 없습니다. 이것이 컴파일러가 오류를 발생시키지 않고 문제 없이 코드를 실행하는 이유 중 하나입니다.</p>
<p>결론적으로, <code>&lt;T&gt;</code>가 메서드 레벨에서 새로운 제네릭 타입으로 선언되었기 때문에 이 메서드 내에서는 클래스 레벨의 <code>T</code>와는 독립적으로 동작하게 됩니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/9ecc4758-5c9c-4760-ac5b-4401cdca26c5/image.png"></p>
<p>위 코드처럼 클래스 레벨의 제네릭 타입과 메서드 레벨의 제네릭 타입은 각각 독립적인 유효 범위를 가지므로 타입이 불일치하고, 캐스팅이나 박싱이 가능한 관계가 아니더라도 IDE의 컴파일러는 오류를 표시하지 않는다.  </p>
<h3 id="와일드-카드-사용-위치-제한">와일드 카드 사용 위치 제한</h3>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/a7458d97-a06d-4efb-8478-60d7189080c1/image.png"></p>
<p>제네릭과 와일드카드는 Java에서 타입 유연성과 안정성을 제공하는 데 사용됩니다. 각각의 사용이 결정되는 위치는 일반적으로 다음과 같습니다:</p>
<ol>
<li>
<p><strong>타입 지정이 필요한 부분</strong>:</p>
<ul>
<li><strong>클래스나 메서드의 선언 부분</strong>: 클래스나 메서드를 정의할 때, 제네릭 타입 파라미터를 명시적으로 지정해야 합니다. 이 위치에서는 와일드카드를 사용할 수 없습니다.</li>
<li><strong>변수 선언</strong>: 변수를 선언할 때는 주로 제네릭 타입을 명시하여 사용합니다. 이 역시 와일드카드를 사용할 수 없는 위치입니다.</li>
</ul>
</li>
<li>
<p><strong>유연성이 필요한 부분</strong>:</p>
<ul>
<li><strong>인스턴스 생성 시점</strong>: 컬렉션 또는 제네릭 클래스의 인스턴스를 생성할 때, 다이아몬드 연산자 내에서 와일드카드를 사용하여 타입을 추론하거나, 타입을 명시적으로 지정하는 부분입니다.</li>
<li><strong>제네릭한 데이터 타입을 다루는 코드 내부</strong>: 메서드의 반환 타입, 메서드의 인자로 받는 매개변수, 또는 제네릭 컬렉션의 요소로 사용될 때, 와일드카드를 사용하여 유연성을 제공하고 다양한 타입을 다룰 수 있습니다.</li>
</ul>
</li>
</ol>
<h2 id="컬렉션-프레임워크--제네릭-컬렉션">컬렉션 프레임워크 / 제네릭 컬렉션</h2>
<p>제네릭 컬렉션과 컬렉션 프레임워크는 서로 다른 개념입니다.</p>
<p><strong>컬렉션 프레임워크(Collection Framework):</strong><br>
컬렉션 프레임워크는 자바에서 데이터 그룹을 저장하고 관리하기 위한 클래스들을 모아놓은 API입니다. 이는 여러 종류의 데이터를 저장하고 조작하는데 필요한 인터페이스와 클래스들을 제공합니다. <code>List</code>, <code>Set</code>, <code>Queue</code>, <code>Map</code> 등의 인터페이스와 이를 구현한 <code>ArrayList</code>, <code>LinkedList</code>, <code>HashSet</code>, <code>HashMap</code> 등의 클래스들이 이에 속합니다. 이 컬렉션 프레임워크는 컬렉션들을 다루는데 필요한 여러 유용한 기능들을 제공하여 데이터를 쉽게 저장, 검색, 정렬, 조작할 수 있도록 도와줍니다.</p>
<p><strong>제네릭 컬렉션(Generic Collections):</strong><br>
제네릭 컬렉션은 컬렉션 프레임워크의 일부분으로, 자바 5부터 제네릭 타입을 도입하여 제공됩니다. 이는 제네릭을 이용하여 컬렉션에 저장되는 요소의 타입을 컴파일 시에 체크하여 안정성을 높이는 데 사용됩니다. 이전에는 컬렉션에 <code>Object</code>를 저장하는 방식이었기 때문에 타입 안전성을 보장받지 못했지만, 제네릭 컬렉션을 사용하면 컴파일러가 타입 불일치에 대한 경고를 더욱 명확하게 제공하고, 런타임 시에 <code>ClassCastException</code>과 같은 오류를 방지하는 데 도움이 됩니다.</p>
<p><strong><code>ArrayList</code>, <code>LinkedList</code>, <code>HashSet</code>, <code>HashMap</code> 등 구현체와 제네릭 타입의 도입은 서로 다른 것들입니다.</strong></p>
<h2 id="제네릭-컬렉션의-내부-자료형">제네릭 컬렉션의 내부 자료형</h2>
<h3 id="컬렉션의-제네릭-타입-변경-불가">컬렉션의 제네릭 타입 변경 불가</h3>
<h4 id="inconvertible-types-cannot-cast">Inconvertible types; cannot cast</h4>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/afada0e2-1efe-4cd4-8d32-83f916dd4431/image.png"></p>
<p>제네릭 타입은 컴파일 타임에 결정되며, 한 번 생성된 컬렉션의 제네릭 타입은 런타임 동안 변경될 수 없습니다.</p>
<h3 id="제네릭-컬렉션이-가질-수-있는-요소-타입">제네릭 컬렉션이 가질 수 있는 요소 타입</h3>
<h4 id="제네릭의-하위-타입">제네릭의 하위 타입</h4>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/254eda44-dfd9-47c2-bb5f-76cede32261b/image.png"></p>
<p><code>List&lt;Number&gt;</code>는 제네릭의 특성 상<code>Number</code>의 하위 타입인 <code>Integer</code>를 포함할 수 있다. 그러나 <code>List&lt;Integer&gt;</code>는 <code>Integer</code>의 상위 타입인 <code>Number</code>를 포함할 수 없다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/bc7052bf-d668-4772-962a-f72a3d30b4ce/image.png"></p>
<p>상위 타입인 <code>Number</code> 타입인 요소 <code>element</code>를 <code>(Integer)</code>로 다운 캐스팅하여 <code>List&lt;Integer&gt;</code>에 추가할 수 있다. </p>
<p>단 <code>Number</code>가 <code>Integer</code>가 아닌 하위 클래스의 객체를 참조하고 있을 때, 해당 객체를 <code>Integer</code>로 강제 형변환하여 캐스팅하려고 하면 <code>ClassCastException</code>이 발생합니다. </p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/a49025aa-15d9-4482-ae47-1efd45b17d62/image.png"></p>
<p><code>Number</code>를 <code>Double</code>로 초기화한 후에 <code>Integer</code>로 명시적으로 캐스팅하는 부분이 문제가 되어 노란 밑줄이 그어지며 <code>ClassCastException</code>이 발생할 것이라고 경고하고 있습니다. <code>Double</code>은 <code>Integer</code>의 상위 클래스가 아니기 때문입니다.</p>
<h4 id="타입이-결정되지-않은-리스트와-타입-소거">타입이 결정되지 않은 리스트와 타입 소거</h4>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/046aa350-e4f7-4bca-bfd2-57a5ca336164/image.png"></p>
<p>"타입이 결정되지 않은" 리스트를 생성한 후, 이 리스트에 특정 타입의 요소를 추가하는 경우에는 원하는 타입의 요소를 담을 수 있습니다. </p>
<pre><code class="language-java"><span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Object</span><span class="token punctuation">&gt;</span></span> list <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ArrayList</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// 타입이 결정되지 않은 리스트</span>

list<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token number">10</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// Integer 추가</span>
list<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token string">"Hello"</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// String 추가</span>

<span class="token comment">// 런타임 시점에 타입이 결정되기 때문에, 다양한 타입의 요소를 담을 수 있음</span></code></pre>
<p>위의 코드에서 <code>List&lt;Object&gt; list = new ArrayList&lt;&gt;();</code>는 컴파일 시에는 <code>Object</code> 타입의 리스트로 처리되지만, 리스트에 추가되는 요소들은 런타임 시점에서 실제 객체의 타입에 맞게 동적으로 결정됩니다. </p>
<p>이러한 유연성은 제네릭 타입의 타입 소거로 인해 가능해집니다. 제네릭은 컴파일 시에만 사용되고 런타임에서는 타입 정보가 소거됩니다. 따라서 컴파일러는 제네릭 타입의 실제 유형을 알 수 없지만, 컴파일 시에는 타입 안전성을 유지하기 위해 캐스팅이나 다른 방법을 사용하여 경고를 발생시키거나 적절한 타입 체크를 수행합니다. </p>
<h4 id="오토-박싱">오토 박싱</h4>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/53ccfcf3-01d7-4952-8c8c-ee0651248ab4/image.png"></p>
<p><code>List&lt;Number&gt;</code>는 <code>Number</code>의 서브 타입을 포함할 수 있습니다. 그러나 <code>int</code>는 <code>Number</code>의 서브 타입이 아니라 기본 데이터 타입입니다. 그래서 <code>List&lt;Number&gt;</code>에 <code>int</code>를 바로 추가할 수 없습니다.</p>
<p>하지만, Java에서 <code>int</code>는 <code>Integer</code>로 자동으로 박싱될 수 있습니다. 따라서 <code>int</code>를 <code>Integer</code>로 변환한 후에 <code>List&lt;Number&gt;</code>에 추가할 수 있습니다.</p>
<p>제네릭 컬렉션에서는 Reference Type(참조형)만 제네릭으로 이용할 수 있기 때문에 제네릭 컬렉션을 Primitive Type(기본형)과 같이 사용하면 Autoboxing은 필연적으로 발생하게 된다. </p>
<h2 id="null의-형변환과-nullable">Null의 형변환과 @Nullable</h2>
<ul>
<li>
<p><code>null</code>은 아무 것도 없음을 나타내는 것으로, 아무런 값도 갖지 않는 것입니다. 메모리상에서 아무런 주소도 참조하지 않는 상태를 말합니다. </p>
</li>
<li>
<p><code>(형변환할 타입) null</code>은 기존의 <code>null</code> 값을 다른 형식으로 형변환하는 것을 의미합니다. 이 경우, <code>null</code> 값은 여전히 <code>null</code>이지만, 코드상에서는 특정 타입으로 형변환하여 사용할 수 있게 됩니다.<br>
이것은 실제로 메모리에 있는 값이나 객체를 변경하는 것이 아니라, 컴파일러에게 이 <code>null</code>을 특정 타입으로 취급하라고 알려주는 것에 불과합니다. 실행 시점에서는 여전히 실제 <code>null</code> 값을 가지고 있습니다.</p>
</li>
<li>
<p><code>@Nullable</code>은 주석(annotation)으로, 코드에서 해당 변수나 매개변수가 <code>null</code>일 수 있다는 것을 명시적으로 나타내는 데 사용됩니다. 이것은 해당 요소가 <code>null</code> 또는 <code>null</code>이 아닌 값 둘 다 가질 수 있다는 의미입니다. 이 주석은 코드의 문서화 및 정적 분석을 위해 사용되며, 개발자에게 해당 요소에 대한 정보를 전달합니다.</p>
</li>
</ul>