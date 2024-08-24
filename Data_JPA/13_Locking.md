<p>사용할 잠금 모드를 지정하려면 다음 예제와 같이 쿼리 메서드에 <code>@Lock</code> annotation을 사용할 수 있습니다.</p>
<p>Example 1. Defining lock metadata on query methods</p>
<pre><code class="language-java"><span class="token keyword">interface</span> <span class="token class-name">UserRepository</span> <span class="token keyword">extends</span> <span class="token class-name">Repository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token comment">// Plain query method</span>
  <span class="token annotation punctuation">@Lock</span><span class="token punctuation">(</span><span class="token class-name">LockModeType</span><span class="token punctuation">.</span>READ<span class="token punctuation">)</span>
  <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastname</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastname<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이 메소드 선언으로 인해 트리거되는 쿼리에 <code>LockModeType</code>이 <code>READ</code>로 제공됩니다. 다음 예와 같이 저장소 인터페이스에서 CRUD 메서드를 다시 선언하고 <code>@Lock</code> annotation을 추가하여 해당 메서드에 대한 잠금을 정의할 수도 있습니다.</p>
<p>Example 2. Defining lock metadata on CRUD methods</p>
<pre><code class="language-java"><span class="token keyword">interface</span> <span class="token class-name">UserRepository</span> <span class="token keyword">extends</span> <span class="token class-name">Repository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token comment">// Redeclaration of a CRUD method</span>
  <span class="token annotation punctuation">@Lock</span><span class="token punctuation">(</span><span class="token class-name">LockModeType</span><span class="token punctuation">.</span>READ<span class="token punctuation">)</span>
  <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">findAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>