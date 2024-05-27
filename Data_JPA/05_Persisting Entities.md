<p>이 섹션에서는 Spring Data JPA를 사용하여 엔터티를 유지(저장)하는 방법을 설명합니다.</p>
<h1 id="saving-entities">Saving Entities</h1>
<p><code>CrudRepository.save(…)</code> 메소드를 사용하여 엔터티 저장을 수행할 수 있습니다. 기본 JPA <code>EntityManager</code>를 사용하여 지정된 엔터티를 유지하거나 병합합니다. 엔터티가 아직 지속되지 않은 경우 Spring Data JPA는 <code>entityManager.persist(…)</code> 메서드를 호출하여 엔터티를 저장합니다. 그렇지 않으면 <code>entityManager.merge(…)</code> 메서드를 호출합니다.</p>
<h2 id="entity-state-detection-strategies">Entity State-detection Strategies</h2>
<p>Spring Data JPA는 엔터티가 새로운지 여부를 감지하기 위해 다음 전략을 제공합니다.</p>
<ol>
<li>
<p>Version-Property 및 Id-Property 검사(기본값): 기본적으로 Spring Data JPA는 non-primitive type의 버전 속성이 있는지 먼저 검사합니다. 있는 경우 해당 속성의 값이 <code>null</code>이면 엔터티는 새로운 것으로 간주됩니다. 이러한 버전 속성이 없으면 Spring Data JPA는 지정된 엔터티의 식별자 속성을 검사합니다. 식별자 속성이 <code>null</code>이면 엔터티는 새로운 엔터티로 간주됩니다. 그렇지 않으면 새로운 것이 아닌 것으로 간주됩니다.</p>
</li>
<li>
<p><code>Persistable</code> 구현: 엔터티가 <code>Persistable</code>을 구현하는 경우 Spring Data JPA는 새로운 감지를 엔터티의 <code>isNew(…)</code> 메서드에 위임합니다. 자세한 내용은 <a href="https://docs.spring.io/spring-data/data-commons/docs/current/api/index.html?org/springframework/data/domain/Persistable.html">JavaDoc</a>을 참조하세요.</p>
</li>
<li>
<p><code>EntityInformation</code> 구현: <code>JpaRepositoryFactory</code>의 하위 클래스를 생성하고 이에 따라 <code>getEntityInformation(…)</code> 메서드를 재정의하여 <code>SimpleJpaRepository</code> 구현에 사용되는 <code>EntityInformation</code> 추상화를 사용자 정의할 수 있습니다. 그런 다음 <code>JpaRepositoryFactory</code>의 사용자 정의 구현을 Spring Bean으로 등록해야 합니다. 이는 거의 필요하지 않습니다. 자세한 내용은 <a href="https://docs.spring.io/spring-data/data-jpa/docs/current/api/index.html?org/springframework/data/jpa/repository/support/JpaRepositoryFactory.html">JavaDoc</a>을 참조하세요.</p>
</li>
</ol>
<p>옵션 1은 수동으로 할당된 식별자를 사용하고 버전 속성이 없는 엔터티에 대한 옵션이 아닙니다. 식별자는 항상 <code>null</code>이 아니기 때문입니다. 해당 시나리오의 일반적인 패턴은 기본적으로 새 인스턴스를 표시하는 transient 플래그가 있는 공통 기본 클래스를 사용하고 JPA lifecycle 콜백을 사용하여 영속성 작업에서 해당 플래그를 뒤집는 것입니다.</p>
<p>Example 1. A base class for entities with manually assigned identifiers</p>
<pre><code class="language-java"><span class="token annotation punctuation">@MappedSuperclass</span>
<span class="token keyword">public</span> <span class="token keyword">abstract</span> <span class="token keyword">class</span> <span class="token class-name">AbstractEntity</span><span class="token generics"><span class="token punctuation">&lt;</span>ID<span class="token punctuation">&gt;</span></span> <span class="token keyword">implements</span> <span class="token class-name">Persistable</span><span class="token generics"><span class="token punctuation">&lt;</span>ID<span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@Transient</span>
  <span class="token keyword">private</span> <span class="token keyword">boolean</span> isNew <span class="token operator">=</span> <span class="token boolean">true</span><span class="token punctuation">;</span> <span class="token comment">// (1)</span>

  <span class="token annotation punctuation">@Override</span>
  <span class="token keyword">public</span> <span class="token keyword">boolean</span> <span class="token function">isNew</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> isNew<span class="token punctuation">;</span> <span class="token comment">// (2) </span>
  <span class="token punctuation">}</span>

  <span class="token annotation punctuation">@PrePersist</span> <span class="token comment">// (3)</span>
  <span class="token annotation punctuation">@PostLoad</span>
  <span class="token keyword">void</span> <span class="token function">markNotNew</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">this</span><span class="token punctuation">.</span>isNew <span class="token operator">=</span> <span class="token boolean">false</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

  <span class="token comment">// More code…</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) 새로운 상태를 유지하기 위한 플래그를 선언합니다. 데이터베이스에 유지되지 않도록 일시적입니다.<br>
(2) Spring Data 저장소가 <code>EntityManager.persist()</code> 또는 …<code>.merge()</code>를 호출할지 여부를 알 수 있도록 <code>Persistable.isNew()</code> 구현에서 플래그를 반환합니다.<br>
(3) <code>save(...)</code>에 대한 저장소 호출 또는 지속성 공급자에 의한 인스턴스 생성 후에 플래그가 기존 엔터티를 나타내도록 전환되도록 JPA 엔터티 콜백을 사용하여 메서드를 선언합니다.</p>