<p>기본적으로 <code>CrudRepository</code>에서 상속된 메서드는 <code>SimpleJpaRepository</code>에서 트랜잭션 구성을 상속합니다. 읽기 작업의 경우 트랜잭션 구성 <code>readOnly</code> 플래그가 <code>true</code>로 설정됩니다. 다른 모든 항목은 기본 트랜잭션 구성이 적용되도록 일반 <code>@Transactional</code>로 구성됩니다. 트랜잭션 저장소 조각이 지원하는 저장소 메서드는 실제 조각 메서드에서 트랜잭션 속성을 상속합니다.</p>
<p>저장소에 선언된 메서드 중 하나에 대한 트랜잭션 구성을 조정해야 하는 경우 다음과 같이 저장소 인터페이스에서 해당 메서드를 다시 선언하세요.</p>
<p>Example 1. Custom transaction configuration for CRUD</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">UserRepository</span> <span class="token keyword">extends</span> <span class="token class-name">CrudRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@Override</span>
  <span class="token annotation punctuation">@Transactional</span><span class="token punctuation">(</span>timeout <span class="token operator">=</span> <span class="token number">10</span><span class="token punctuation">)</span>
  <span class="token keyword">public</span> <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

  <span class="token comment">// Further query method declarations</span>
<span class="token punctuation">}</span></code></pre>
<p>그렇게 하면 <code>findAll()</code> 메서드가 <code>readOnly</code> 플래그 없이 10초의 제한 시간으로 실행됩니다.</p>
<p>트랜잭션 동작을 변경하는 또 다른 방법은 (일반적으로) 둘 이상의 저장소를 포함하는 Facade 또는 서비스 구현을 사용하는 것입니다. 그 목적은 CRUD가 아닌 작업에 대한 트랜잭션 경계를 정의하는 것입니다. 다음 예는 둘 이상의 저장소에 대해 이러한 Facade를 사용하는 방법을 보여줍니다.</p>
<p>Example 2. Using a facade to define transactions for multiple repository calls</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Service</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">UserManagementImpl</span> <span class="token keyword">implements</span> <span class="token class-name">UserManagement</span> <span class="token punctuation">{</span>

  <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">UserRepository</span> userRepository<span class="token punctuation">;</span>
  <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">RoleRepository</span> roleRepository<span class="token punctuation">;</span>

  <span class="token keyword">public</span> <span class="token class-name">UserManagementImpl</span><span class="token punctuation">(</span><span class="token class-name">UserRepository</span> userRepository<span class="token punctuation">,</span>
    <span class="token class-name">RoleRepository</span> roleRepository<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">this</span><span class="token punctuation">.</span>userRepository <span class="token operator">=</span> userRepository<span class="token punctuation">;</span>
    <span class="token keyword">this</span><span class="token punctuation">.</span>roleRepository <span class="token operator">=</span> roleRepository<span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

  <span class="token annotation punctuation">@Transactional</span>
  <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">addRoleToAllUsers</span><span class="token punctuation">(</span><span class="token class-name">String</span> roleName<span class="token punctuation">)</span> <span class="token punctuation">{</span>

    <span class="token class-name">Role</span> role <span class="token operator">=</span> roleRepository<span class="token punctuation">.</span><span class="token function">findByName</span><span class="token punctuation">(</span>roleName<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token keyword">for</span> <span class="token punctuation">(</span><span class="token class-name">User</span> user <span class="token operator">:</span> userRepository<span class="token punctuation">.</span><span class="token function">findAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
      user<span class="token punctuation">.</span><span class="token function">addRole</span><span class="token punctuation">(</span>role<span class="token punctuation">)</span><span class="token punctuation">;</span>
      userRepository<span class="token punctuation">.</span><span class="token function">save</span><span class="token punctuation">(</span>user<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이 예에서는 <code>addRoleToAllUsers(…)</code>에 대한 호출이 트랜잭션 내에서 실행되도록 합니다(기존 트랜잭션에 참여하거나 이미 실행 중인 트랜잭션이 없는 경우 새 트랜잭션 생성). 외부 트랜잭션 구성이 실제 사용되는 트랜잭션 구성을 결정하므로 저장소의 트랜잭션 구성은 무시됩니다. 작동할 Facade의 주석 기반 구성을 얻으려면 <code>&lt;tx:annotation-driven /&gt;</code>를 활성화하거나 <code>@EnableTransactionManagement</code>를 명시적으로 사용해야 합니다. 이 예에서는 구성 요소 검색을 사용한다고 가정합니다.</p>
<p><code>save</code> 호출은 JPA 관점에서 꼭 필요한 것은 아니지만 Spring Data가 제공하는 저장소 추상화의 일관성을 유지하려면 여전히 존재해야 합니다.</p>
<h1 id="transactional-query-methods">Transactional query methods</h1>
<p>선언된 쿼리 메서드(기본 메서드 포함)에는 기본적으로 트랜잭션 구성이 적용되지 않습니다. 이러한 메서드를 트랜잭션 방식으로 실행하려면 다음 예제와 같이 정의한 저장소 인터페이스에서 <code>@Transactional</code>을 사용하세요.</p>
<p>Example 3. Using @Transactional at query methods</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Transactional</span><span class="token punctuation">(</span>readOnly <span class="token operator">=</span> <span class="token boolean">true</span><span class="token punctuation">)</span>
<span class="token keyword">interface</span> <span class="token class-name">UserRepository</span> <span class="token keyword">extends</span> <span class="token class-name">JpaRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastname</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">)</span><span class="token punctuation">;</span>

  <span class="token annotation punctuation">@Modifying</span>
  <span class="token annotation punctuation">@Transactional</span>
  <span class="token annotation punctuation">@Query</span><span class="token punctuation">(</span><span class="token string">"delete from User u where u.active = false"</span><span class="token punctuation">)</span>
  <span class="token keyword">void</span> <span class="token function">deleteInactiveUsers</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>일반적으로 대부분의 쿼리 메서드는 데이터만 읽기 때문에 <code>readOnly</code> 플래그를 <code>true</code>로 설정하려고 합니다. 이와 대조적으로 <code>deleteInactiveUsers()</code>는 <code>@Modifying</code> annotation을 사용하고 트랜잭션 구성을 재정의합니다. 따라서 메서드는 <code>readOnly</code> 플래그가 <code>false</code>로 설정된 상태로 실행됩니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
읽기 전용 쿼리에 트랜잭션을 사용하고 <code>readOnly</code> 플래그를 설정하여 트랜잭션을 표시할 수 있습니다. 그러나 이렇게 해도 조작 쿼리를 트리거하지 않는지 확인하는 역할을 하지 않습니다(일부 데이터베이스는 읽기 전용 트랜잭션 내에서 <code>INSERT</code> 및 <code>UPDATE</code> 문을 거부하지만). 대신 <code>readOnly</code> 플래그는 성능 최적화를 위해 기본 JDBC 드라이버에 대한 힌트로 전파됩니다. 게다가 Spring은 기본 JPA 제공자에 대해 일부 최적화를 수행합니다. 예를 들어, Hibernate와 함께 사용될 때, 트랜잭션을 <code>readOnly</code>로 구성할 때 플러시(flush) 모드는 <code>NEVER</code>로 설정됩니다. 이는 Hibernate가 더티 검사를 건너뛰게 합니다(대형 객체 트리에서 눈에 띄는 개선).</p>
</blockquote>