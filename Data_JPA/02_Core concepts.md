<p>Spring Data 저장소 추상화의 중앙 인터페이스는 <code>Repository</code>입니다. 관리할 도메인 클래스와 도메인 클래스의 식별자 유형을 유형 인수로 사용합니다. 이 인터페이스는 주로 작업할 유형을 캡처하고 이 인터페이스를 확장하는 인터페이스를 검색하는 데 도움이 되는 마커 인터페이스 역할을 합니다. <a href="https://docs.spring.io/spring-data/commons/docs/3.2.5/api//org/springframework/data/repository/CrudRepository.html"><code>CrudRepository</code></a> 및 <a href="https://docs.spring.io/spring-data/commons/docs/3.2.5/api//org/springframework/data/repository/ListCrudRepository.html"><code>ListCrudRepository</code></a> 인터페이스는 관리되는 엔터티 클래스에 대한 정교한 CRUD 기능을 제공합니다.</p>
<p>CrudRepository Interface</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">CrudRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">,</span> ID<span class="token punctuation">&gt;</span></span> <span class="token keyword">extends</span> <span class="token class-name">Repository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">,</span> ID<span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">S</span> <span class="token keyword">extends</span> <span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token class-name">S</span> <span class="token function">save</span><span class="token punctuation">(</span><span class="token class-name">S</span> entity<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// (1)</span>

  <span class="token class-name">Optional</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token function">findById</span><span class="token punctuation">(</span><span class="token class-name">ID</span> primaryKey<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// (2)</span>

  <span class="token class-name">Iterable</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token function">findAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// (3)</span>

  <span class="token keyword">long</span> <span class="token function">count</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">// (4)</span>

  <span class="token keyword">void</span> <span class="token function">delete</span><span class="token punctuation">(</span><span class="token class-name">T</span> entity<span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">// (5)</span>

  <span class="token keyword">boolean</span> <span class="token function">existsById</span><span class="token punctuation">(</span><span class="token class-name">ID</span> primaryKey<span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">// (6)</span>

  <span class="token comment">// … more functionality omitted.</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) 지정된 엔터티를 저장합니다.<br>
(2) 지정된 ID로 식별되는 엔터티를 반환합니다.<br>
(3) 모든 엔터티를 반환합니다.<br>
(4) 엔터티 수를 반환합니다.<br>
(5) 지정된 엔터티를 삭제합니다.<br>
(6) 지정된 ID를 가진 엔터티가 존재하는지 여부를 나타냅니다.</p>
<p>이 인터페이스에 선언된 메서드를 일반적으로 CRUD 메서드라고 합니다. <code>ListCrudRepository</code>는 동등한 메소드를 제공하지만 <code>CrudRepository</code> 메소드가 <code>Iterable</code>을 반환하는 <code>List</code>를 반환합니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
또한 <code>JpaRepository</code> 또는 <code>MongoRepository</code>와 같은 지속성 기술별 추상화도 제공합니다. 이러한 인터페이스는 <code>CrudRepository</code>를 확장하고 <code>CrudRepository</code>와 같이 다소 일반적인 지속성 기술(persistence technology)에 구애받지 않는 인터페이스 외에도 기본 지속성 기술의 기능을 노출합니다.</p>
</blockquote>
<p><code>CrudRepository</code> 외에도 엔터티에 대한 페이지 매김 액세스를 쉽게 하기 위해 추가 메서드를 추가하는 <a href="https://docs.spring.io/spring-data/commons/docs/3.2.5/api//org/springframework/data/repository/PagingAndSortingRepository.html"><code>PagingAndSortingRepository</code></a> 및 <a href="https://docs.spring.io/spring-data/commons/docs/3.2.5/api//org/springframework/data/repository/ListPagingAndSortingRepository.html"><code>ListPagingAndSortingRepository</code></a>가 있습니다.</p>
<p>PagingAndSortingRepository interface</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">PagingAndSortingRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">,</span> ID<span class="token punctuation">&gt;</span></span>  <span class="token punctuation">{</span>

  <span class="token class-name">Iterable</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token function">findAll</span><span class="token punctuation">(</span><span class="token class-name">Sort</span> sort<span class="token punctuation">)</span><span class="token punctuation">;</span>

  <span class="token class-name">Page</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token function">findAll</span><span class="token punctuation">(</span><span class="token class-name">Pageable</span> pageable<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
확장 인터페이스는 실제 저장소 모듈에서 지원될 수 있습니다. 이 문서에서는 일반적인 계획(scheme)을 설명하지만 저장소 모듈이 사용하려는 인터페이스를 지원하는지 확인하십시오.</p>
</blockquote>
<p>페이지 크기 20으로 <code>User</code>의 두 번째 페이지에 액세스하려면 다음과 같이 할 수 있습니다.</p>
<pre><code class="language-java"><span class="token class-name">PagingAndSortingRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> repository <span class="token operator">=</span> <span class="token comment">// … get access to a bean</span>
<span class="token class-name">Page</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> users <span class="token operator">=</span> repository<span class="token punctuation">.</span><span class="token function">findAll</span><span class="token punctuation">(</span><span class="token class-name">PageRequest</span><span class="token punctuation">.</span><span class="token function">of</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">20</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p><code>ListPagingAndSortingRepository</code>는 동등한 메서드를 제공하지만 <code>PagingAndSortingRepository</code> 메서드가 <code>Iterable</code>을 반환하는 <code>List</code>를 반환합니다.</p>
<p>쿼리 방식 외에도 개수 쿼리와 삭제 쿼리 모두에 대한 쿼리 파생이 가능합니다. 다음 목록은 파생된 카운트 쿼리에 대한 인터페이스 정의를 보여줍니다.</p>
<p>Derived Count Query</p>
<pre><code class="language-java"><span class="token keyword">interface</span> <span class="token class-name">UserRepository</span> <span class="token keyword">extends</span> <span class="token class-name">CrudRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token keyword">long</span> <span class="token function">countByLastname</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 목록은 파생된 삭제 쿼리에 대한 인터페이스 정의를 보여줍니다.</p>
<p>Derived Delete Query</p>
<pre><code class="language-java"><span class="token keyword">interface</span> <span class="token class-name">UserRepository</span> <span class="token keyword">extends</span> <span class="token class-name">CrudRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token keyword">long</span> <span class="token function">deleteByLastname</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">)</span><span class="token punctuation">;</span>

  <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">removeByLastname</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>