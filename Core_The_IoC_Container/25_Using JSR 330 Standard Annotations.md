<p>Spring은 JSR-330 표준 어노테이션(종속성 주입)을 지원합니다. 이러한 어노테이션은 Spring 어노테이션과 동일한 방식으로 스캔됩니다. 이를 사용하려면 클래스패스에 관련 jar 파일이 있어야 합니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
Maven을 사용하는 경우 <code>jakarta.inject</code> 아티팩트는 표준 Maven 저장소(<a href="https://repo.maven.apache.org/maven2/jakarta/inject/jakarta.inject-api/2.0.0/">https://repo.maven.apache.org/maven2/jakarta/inject/jakarta.inject-api/2.0.0/</a>)에서 사용할 수 있습니다. pom.xml 파일에 다음 종속성을 추가할 수 있습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>dependency</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>groupId</span><span class="token punctuation">&gt;</span></span>jakarta.inject<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>groupId</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>artifactId</span><span class="token punctuation">&gt;</span></span>jakarta.inject-api<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>artifactId</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>version</span><span class="token punctuation">&gt;</span></span>2.0.0<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>version</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>dependency</span><span class="token punctuation">&gt;</span></span></code></pre>
</blockquote>
<h1 id="dependency-injection-with-inject-and-named">Dependency Injection with <code>@Inject</code> and <code>@Named</code></h1>
<p><code>@Autowired</code> 대신 다음과 같이 <code>@jakarta.inject.Inject</code>를 사용할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>inject</span><span class="token punctuation">.</span><span class="token class-name">Inject</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SimpleMovieLister</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">MovieFinder</span> movieFinder<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Inject</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setMovieFinder</span><span class="token punctuation">(</span><span class="token class-name">MovieFinder</span> movieFinder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>movieFinder <span class="token operator">=</span> movieFinder<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">listMovies</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>movieFinder<span class="token punctuation">.</span><span class="token function">findMovies</span><span class="token punctuation">(</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>@Autowired</code>와 마찬가지로 필드 수준, 메서드 수준 및 생성자-인수 수준에서 <code>@Inject</code>를 사용할 수 있습니다. 또한 주입 지점을 <code>Provider</code>로 선언하여 더 짧은 범위의 Bean에 대한 on-demand 액세스를 허용하거나 <code>Provider.get()</code> 호출을 통해 다른 Bean에 대한 lazy 액세스를 허용할 수 있습니다. 다음 예에서는 이전 예의 변형을 제공합니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>inject</span><span class="token punctuation">.</span><span class="token class-name">Inject</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>inject</span><span class="token punctuation">.</span><span class="token class-name">Provider</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SimpleMovieLister</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">Provider</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">MovieFinder</span><span class="token punctuation">&gt;</span></span> movieFinder<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Inject</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setMovieFinder</span><span class="token punctuation">(</span><span class="token class-name">Provider</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">MovieFinder</span><span class="token punctuation">&gt;</span></span> movieFinder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>movieFinder <span class="token operator">=</span> movieFinder<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">listMovies</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>movieFinder<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">findMovies</span><span class="token punctuation">(</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>주입되어야 하는 종속성에 대해 정규화된 이름을 사용하려면 다음 예제와 같이 <code>@Named</code> 어노테이션을 사용해야 합니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>inject</span><span class="token punctuation">.</span><span class="token class-name">Inject</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>inject</span><span class="token punctuation">.</span><span class="token class-name">Named</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SimpleMovieLister</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">MovieFinder</span> movieFinder<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Inject</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setMovieFinder</span><span class="token punctuation">(</span><span class="token annotation punctuation">@Named</span><span class="token punctuation">(</span><span class="token string">"main"</span><span class="token punctuation">)</span> <span class="token class-name">MovieFinder</span> movieFinder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>movieFinder <span class="token operator">=</span> movieFinder<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p><code>@Autowired</code>와 마찬가지로 <code>@Inject</code>도 <code>java.util.Optional</code> 또는 <code>@Nullable</code>과 함께 사용할 수 있습니다. <code>@Inject</code>에는 필수 속성(attribute)이 없기 때문에 이는 여기에서 훨씬 더 적용 가능합니다. 다음 예제 쌍은 <code>@Inject</code> 및 <code>@Nullable</code>을 사용하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SimpleMovieLister</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Inject</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setMovieFinder</span><span class="token punctuation">(</span><span class="token class-name">Optional</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">MovieFinder</span><span class="token punctuation">&gt;</span></span> movieFinder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SimpleMovieLister</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Inject</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setMovieFinder</span><span class="token punctuation">(</span><span class="token annotation punctuation">@Nullable</span> <span class="token class-name">MovieFinder</span> movieFinder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h1 id="named-and-managedbean-standard-equivalents-to-the-component-annotation"><code>@Named</code> and <code>@ManagedBean</code>: Standard Equivalents to the <code>@Component</code> Annotation</h1>
<p><code>@Component</code> 대신 다음 예제와 같이 <code>@jakarta.inject.Named</code> 또는 <code>jakarta.annotation.ManagedBean</code>을 사용할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>inject</span><span class="token punctuation">.</span><span class="token class-name">Inject</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>inject</span><span class="token punctuation">.</span><span class="token class-name">Named</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Named</span><span class="token punctuation">(</span><span class="token string">"movieListener"</span><span class="token punctuation">)</span>  <span class="token comment">// @ManagedBean("movieListener") could be used as well</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SimpleMovieLister</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">MovieFinder</span> movieFinder<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Inject</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setMovieFinder</span><span class="token punctuation">(</span><span class="token class-name">MovieFinder</span> movieFinder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>movieFinder <span class="token operator">=</span> movieFinder<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>컴포넌트의 이름을 지정하지 않고 <code>@Component</code>를 사용하는 것은 매우 일반적입니다. <code>@Named</code>는 다음 예제와 같이 비슷한 방식으로 사용될 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>inject</span><span class="token punctuation">.</span><span class="token class-name">Inject</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>inject</span><span class="token punctuation">.</span><span class="token class-name">Named</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Named</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SimpleMovieLister</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">MovieFinder</span> movieFinder<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Inject</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setMovieFinder</span><span class="token punctuation">(</span><span class="token class-name">MovieFinder</span> movieFinder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>movieFinder <span class="token operator">=</span> movieFinder<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p><code>@Named</code> 또는 <code>@ManagedBean</code>을 사용하면 다음 예제와 같이 Spring 어노테이션을 사용할 때와 정확히 동일한 방식으로 구성 요소 검색을 사용할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@ComponentScan</span><span class="token punctuation">(</span>basePackages <span class="token operator">=</span> <span class="token string">"org.example"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span>  <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
<code>@Component</code>와 달리 JSR-330 <code>@Named</code> 및 JSR-250 <code>@ManagedBean</code> 어노테이션은 구성 가능하지 않습니다(not composable). 사용자 정의 컴포넌트 어노테이션을 작성하려면 Spring의 스테레오타입 모델을 사용해야 합니다.</p>
</blockquote>
<h1 id="limitations-of-jsr-330-standard-annotations">Limitations of JSR-330 Standard Annotations</h1>
<p>표준 어노테이션으로 작업할 때 다음 표에 표시된 것처럼 일부 중요한 기능을 사용할 수 없다는 점을 알아야 합니다.</p>
<table id="annotations-comparison" class="tableblock frame-all grid-all stripes-odd stretch">
<caption class="title">표 1. Spring 컴포넌트 모델 요소 대 JSR-330 변형</caption>





<thead>
<tr>
<th class="tableblock halign-left valign-top">Spring</th>
<th class="tableblock halign-left valign-top">jakarta.inject.*</th>
<th class="tableblock halign-left valign-top">jakarta.inject 제한 사항 / 설명</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">@Autowired</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">@Inject</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>@Inject</code>에는 'required' 속성이 없습니다. 대신 Java 8의 <code>Optional</code>을 사용할 수 있습니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">@Component</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">@Named / @ManagedBean</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">JSR-330은 구성 가능한 모델을 제공하지 않고 명명된 구성 요소를 식별하는 방법만 제공합니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">@Scope("singleton")</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">@Singleton</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">JSR-330의 기본 범위는 Spring의 <code>prototype</code>과 유사합니다. 그러나 Spring의 일반적인 기본값과 일관성을 유지하기 위해 Spring 컨테이너에서 선언된 JSR-330 빈은 기본적으로 <code>singleton</code>입니다. <code>singleton</code> 이외의 범위를 사용하려면 Spring의 <code>@Scope</code> 주석을 사용해야 합니다. <code>jakarta.inject</code>은 사용자 지정 주석을 생성하는 데 사용되는 <code>jakarta.inject.Scope</code> 주석도 제공합니다. 그러나 이 주석은 사용자 지정 주석 생성에만 사용되어야 합니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">@Qualifier</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">@Qualifier / @Named</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>jakarta.inject.Qualifier</code>는 사용자 정의 한정자를 작성하기 위한 메타 주석입니다. 구체적인 <code>String</code> 한정자(값이 있는 Spring의 <code>@Qualifier</code>와 같은)는 <code>jakarta.inject.Named</code>를 통해 연관시킬 수 있습니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">@Value</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">-</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">해당 없음</p><p></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">@Lazy</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">-</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">해당 없음</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">ObjectFactory</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">Provider</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>jakarta.inject.Provider</code>는 Spring의 <code>ObjectFactory</code>와 직접적인 대체품입니다. 단지 더 짧은 <code>get()</code> 메서드 이름을 가지고 있습니다. 또한 Spring의 <code>@Autowired</code>나 주석이 없는 생성자 및 setter 메서드와 함께 사용할 수 있습니다.</p></td>
</tr>
</tbody>
</table>