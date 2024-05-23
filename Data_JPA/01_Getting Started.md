<p>작업 환경을 부트스트랩 설정하는 쉬운 방법은 start.spring.io를 통해 Spring 기반 프로젝트를 생성하거나 <a href="https://spring.io/tools">Spring Tools</a>에서 Spring 프로젝트를 생성하는 것입니다.</p>
<h1 id="examples-repository">Examples Repository</h1>
<p>GitHub <a href="https://github.com/spring-projects/spring-data-examples">spring-data-examples 리포지토리</a>에는 라이브러리 작동 방식을 파악하기 위해 다운로드하고 사용해 볼 수 있는 몇 가지 예제가 있습니다.</p>
<h1 id="hello-world">Hello World</h1>
<p>간단한 엔터티와 해당 저장소부터 시작해 보겠습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token keyword">class</span> <span class="token class-name">Person</span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@Id</span> <span class="token annotation punctuation">@GeneratedValue</span><span class="token punctuation">(</span>strategy <span class="token operator">=</span> <span class="token class-name">GenerationType</span><span class="token punctuation">.</span>AUTO<span class="token punctuation">)</span>
  <span class="token keyword">private</span> <span class="token class-name">Long</span> id<span class="token punctuation">;</span>
  <span class="token keyword">private</span> <span class="token class-name">String</span> name<span class="token punctuation">;</span>

  <span class="token comment">// getters and setters omitted for brevity</span>
<span class="token punctuation">}</span>

<span class="token keyword">interface</span> <span class="token class-name">PersonRepository</span> <span class="token keyword">extends</span> <span class="token class-name">Repository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token class-name">Person</span> <span class="token function">save</span><span class="token punctuation">(</span><span class="token class-name">Person</span> person<span class="token punctuation">)</span><span class="token punctuation">;</span>

  <span class="token class-name">Optional</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> <span class="token function">findById</span><span class="token punctuation">(</span><span class="token keyword">long</span> id<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 예제와 같이 실행할 기본 애플리케이션을 만듭니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">DemoApplication</span> <span class="token punctuation">{</span>

  <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">DemoApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

  <span class="token annotation punctuation">@Bean</span>
  <span class="token class-name">CommandLineRunner</span> <span class="token function">runner</span><span class="token punctuation">(</span><span class="token class-name">PersonRepository</span> repository<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> args <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>

      <span class="token class-name">Person</span> person <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Person</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
      person<span class="token punctuation">.</span><span class="token function">setName</span><span class="token punctuation">(</span><span class="token string">"John"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

      repository<span class="token punctuation">.</span><span class="token function">save</span><span class="token punctuation">(</span>person<span class="token punctuation">)</span><span class="token punctuation">;</span>
      <span class="token class-name">Person</span> saved <span class="token operator">=</span> repository<span class="token punctuation">.</span><span class="token function">findById</span><span class="token punctuation">(</span>person<span class="token punctuation">.</span><span class="token function">getId</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">orElseThrow</span><span class="token punctuation">(</span><span class="token class-name">NoSuchElementException</span><span class="token operator">:</span><span class="token operator">:</span><span class="token keyword">new</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이 간단한 예에서도 지적해야 할 몇 가지 주목할만한 사항이 있습니다.</p>
<ul>
<li>
<p>리포지토리 인스턴스가 자동으로 구현됩니다. <code>@Bean</code> 메소드의 매개변수로 사용되면 추가 주석이 필요 없이 자동으로 연결됩니다.</p>
</li>
<li>
<p>기본 저장소는 <code>Repository</code>를 확장합니다. 애플리케이션에 노출하려는 API 표면의 양을 고려하는 것이 좋습니다. 더 복잡한 저장소 인터페이스는 <code>ListCrudRepository</code> 또는 <code>JpaRepository</code>입니다.</p>
</li>
</ul>