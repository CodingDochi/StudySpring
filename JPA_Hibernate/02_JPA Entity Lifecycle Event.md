<p>JPA를 사용할 때 엔터티의 수명주기 내에서 몇가지 이벤트를 통지(notify) 받는다.<br>
JPA 엔터티 lifecycle 이벤트와 어노테이션으로 콜백을 처리하고 이벤트가 발생했을 때 코드를 실행하는 방법을 논의한다.</p>
<h2 id="jpa-entity-lifecycle-events">JPA Entity Lifecycle Events</h2>
<blockquote>
<p>자바의 Optional은 Java 8부터 도입된 클래스로서, 값이 존재할 수도 있고 없을 수도 있는 상황에서 사용되는 컨테이너 타입입니다. Optional은 "값이 있을 수도 있고 없을 수도 있음"을 명시적으로 나타내며, null 체크를 피하고 코드를 더 안전하게 만들기 위해 사용됩니다.</p>
<ul>
<li>
<p>Null 대체: Optional은 null 대신 사용할 수 있는 대체 수단으로 사용됩니다. null로 인한 예기치 않은 NullPointerException을 방지할 수 있습니다.</p>
</li>
<li>
<p>값의 존재 여부 확인: isPresent() 메서드를 사용하여 값의 존재 여부를 확인할 수 있습니다.</p>
</li>
<li>
<p>값 가져오기: get() 메서드를 사용하여 값이 존재할 경우 해당 값을 가져올 수 있습니다. 하지만 get() 메서드를 호출하기 전에 isPresent()를 사용하여 값의 존재 여부를 체크하는 것이 좋습니다.</p>
</li>
<li>
<p>기본값 설정: orElse() 메서드를 사용하여 값이 없을 경우 기본값을 설정할 수 있습니다.</p>
</li>
<li>
<p>함수형 조작: map(), filter(), flatMap() 등의 메서드를 사용하여 Optional의 값을 함수형으로 변형하거나 조작할 수 있습니다.</p>
</li>
</ul>
</blockquote>
<p>JPA는 7가지 선택적(optional) Lifecycle을 규정한다.</p>
<ul>
<li>@PrePersist : 새 엔터티에 persist가 호출되기 전</li>
<li>@PostPersist : 새 엔터티에 persist가 호출된 후</li>
<li>@PreRemove : 엔터티가 삭제되기 전</li>
<li>@PostRemove : 엔터티가 삭제된 후</li>
<li>@PreUpdate : update 작업 전</li>
<li>@PostUpdate : update 작업 후</li>
<li>@PostLoad : 엔터티가 로드된 후</li>
</ul>
<p><strong> Lifecycle 어노테이션을 사용하는 두가지 접근법 </strong><br>
엔터티 내부에 메소드를 annotating 하는 것과 annotated된 콜백 메소드로 EntityListener를 생성하는 것이다. 동시에 두가지 방법을 모두 사용할 수도 있다. 메소드 위치에 관계없이 콜백 메소드는 void 반환 타입을 가져야 한다.</p>
<blockquote>
<p>컨텍스트 프록시 (Context Proxy):<br>
컨텍스트 프록시는 일반적으로 AOP(Aspect-Oriented Programming)와 관련된 개념으로, 주로 보안, 트랜잭션 관리, 로깅 등과 같은 관심사를 분리하여 코드의 중복을 줄이고 모듈화하는데 사용됩니다. 컨텍스트 프록시는 원본 객체를 감싸서 필요한 추가 기능을 제공하며, 원본 객체에 직접 접근하는 대신 프록시를 통해 간접적으로 접근하게 됩니다.</p>
<p>콜백 (Callback):<br>
콜백은 프로그래밍에서 어떤 이벤트가 발생했을 때 호출되는 함수나 메서드를 의미합니다. 콜백은 주로 비동기 작업이나 이벤트 처리와 관련하여 사용됩니다. 예를 들어, 사용자의 입력에 응답하는 UI 이벤트 핸들러나 비동기 작업의 완료를 처리하는 함수는 콜백으로 동작합니다.</p>
<p>컨텍스트 프록시와 콜백은 각각 다른 컨셉을 나타내지만, 특정 상황에서 유사한 역할을 할 수 있습니다. 예를 들어, AOP를 사용하여 특정 메서드 호출 시점에 콜백을 추가하거나 보안 체크를 수행하는 것도 가능합니다. 또한 프록시를 통해 콜백 메서드를 호출하거나, 콜백을 등록하여 특정 이벤트 발생 시 프록시를 활용할 수도 있습니다.</p>
</blockquote>
<p>새 엔터티를 생성하고 레파지토리의 save 메소드를 호출하면 @PrePersist로 annotated된 메소드가 호출된다. 그리고 레코드가 데이터베이스에 삽입되면 @PostPersist 메소드가 호출된다.<br>
기본 키를 자동 생성하기 위해 @GenteratedValue를 사용한다면 @PostPersist 메소드 내부에서 그 키를 사용할 수 있다.</p>
<p>문서에는 트랜잭션 종료 시점 또는 flush 이후, @PostPersist, @PostRemove와 @PostUpdate 작업이 실행된 직후에 이러한 이벤트가 발생할 수 있다고 언급하고 있다.<br>
@PreUpdate 콜백은 데이터가 실제로 변경된 이후에만 호출될 수 있다는 점에 주목해야 한다. 즉, 실제 SQL update 문이 실행되어야만 한다는 것이다.<br>
@PostUpdate 콜백은 실제 변경 여부와 관계 없이 호출될 수 있다.<br>
엔터티를 persist 하거나 삭제하는 어떤 콜백이라도 예외를 발생시키면 트랜잭션은 roll back 한다. </p>
<blockquote>
<p>"@PreUpdate" 콜백은 엔터티의 데이터가 변경되어 SQL의 실제 "UPDATE" 문을 실행할 때 호출되는 콜백 메서드입니다. 그러므로 "Pre"는 실제 데이터베이스 업데이트 작업이 이전에 일어나기 직전에 실행되는 것을 의미합니다.</p>
</blockquote>
<h2 id="annotating-the-entity">Annotating the Entity</h2>
<p>엔터티에 직접적으로 콜백 어노테이션을 사용하는 것으로 시작한다. 예시에서는 User 레코드가 변경되면 로그를 남길 것이다. 콜백 메소드 안에 로깅 명령문을 추가할 것이다. 데이터베이스에 로드된 후 user의 full name을 assemble할 것이다. @PostLoad 어노테이션으로 구현할 것이다.</p>
<p>예시) User 엔터티 정의</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Entity</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">User</span> <span class="token punctuation">{</span>
    <span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token class-name">Log</span> log <span class="token operator">=</span> <span class="token class-name">LogFactory</span><span class="token punctuation">.</span><span class="token function">getLog</span><span class="token punctuation">(</span><span class="token class-name">User</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Id</span>
    <span class="token annotation punctuation">@GeneratedValue</span>
    <span class="token keyword">private</span> <span class="token keyword">int</span> id<span class="token punctuation">;</span>
    
    <span class="token keyword">private</span> <span class="token class-name">String</span> userName<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> firstName<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> lastName<span class="token punctuation">;</span>
    <span class="token annotation punctuation">@Transient</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> fullName<span class="token punctuation">;</span>

    <span class="token comment">// Standard getters/setters</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p>주어진 코드에서 User.class는 User 클래스의 클래스 리터럴을 나타냅니다. User.class를 사용하여 User 클래스에 대한 Class 객체에 접근하고, 해당 클래스의 로그(Log)를 생성하는데 사용되는 것으로 보입니다.</p>
<p>보통 로깅 라이브러리에서는 클래스 이름을 로그 메시지에 포함하여 어떤 클래스에서 로그가 발생했는지 추적할 수 있도록 합니다. 따라서 LogFactory.getLog(User.class)는 User 클래스의 로그를 생성하기 위해 로깅 라이브러리에서 사용되며, 클래스 이름을 전달하여 해당 클래스의 로그를 관리하는데 활용됩니다.</p>
</blockquote>
<p>예시) UserRepository 인터페이스 생성</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">UserRepository</span> <span class="token keyword">extends</span> <span class="token class-name">JpaRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Integer</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>
    <span class="token keyword">public</span> <span class="token class-name">User</span> <span class="token function">findByUserName</span><span class="token punctuation">(</span><span class="token class-name">String</span> userName<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p>레파지토리 (Repository):</p>
<ul>
<li>객체 지향 프로그래밍에서 데이터베이스와의 상호 작용을 추상화한 인터페이스 또는 클래스입니다.</li>
<li>엔터티의 생성, 조회, 수정, 삭제 등 데이터베이스 조작을 담당하며, 개발자가 더 쉽게 데이터 조작을 할 수 있도록 합니다.</li>
<li>레파지토리는 주로 JPA와 함께 사용되며, 쿼리 메서드(Query Methods)를 활용하여 데이터베이스 쿼리를 선언적으로 작성하는 기능을 제공합니다.</li>
</ul>
</blockquote>
<blockquote>
<p>데이터베이스 세션 (Database Session):</p>
<ul>
<li>주로 하이버네이트(Hibernate)와 같은 JPA 구현체에서 사용되는 용어로, 영속성 컨텍스트를 관리하고 데이터베이스와 통신하는 인터페이스입니다.</li>
<li>데이터베이스 세션을 통해 엔터티의 조회, 저장, 수정, 삭제 등 데이터베이스와의 상호 작용을 수행합니다.</li>
<li>데이터베이스 세션은 일반적으로 트랜잭션을 다루는 작업도 수행하며, 영속성 컨텍스트를 제어합니다.</li>
</ul>
</blockquote>
<blockquote>
<p>영속성 컨텍스트 (Persistence Context):</p>
<ul>
<li>JPA에서 사용되는 중요한 개념으로, 엔터티(Entity)의 상태를 관리하는 공간입니다.</li>
<li>엔터티 객체의 생명주기를 추적하고, 엔터티의 변경을 감지하여 데이터베이스와 동기화합니다.</li>
<li>영속성 컨텍스트는 트랜잭션 범위 내에서 엔터티의 상태를 보존하고, 트랜잭션이 커밋될 때 변경된 내용을 데이터베이스에 반영합니다.</li>
</ul>
</blockquote>
<p>예시) User 클래스에 콜백 메소드 추가하기</p>
<pre><code class="language-java"><span class="token annotation punctuation">@PrePersist</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">logNewUserAttempt</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"Attempting to add new user with username: "</span> <span class="token operator">+</span> userName<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
    
<span class="token annotation punctuation">@PostPersist</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">logNewUserAdded</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"Added user '"</span> <span class="token operator">+</span> userName <span class="token operator">+</span> <span class="token string">"' with ID: "</span> <span class="token operator">+</span> id<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
    
<span class="token annotation punctuation">@PreRemove</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">logUserRemovalAttempt</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"Attempting to delete user: "</span> <span class="token operator">+</span> userName<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
    
<span class="token annotation punctuation">@PostRemove</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">logUserRemoval</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"Deleted user: "</span> <span class="token operator">+</span> userName<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@PreUpdate</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">logUserUpdateAttempt</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"Attempting to update user: "</span> <span class="token operator">+</span> userName<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@PostUpdate</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">logUserUpdate</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"Updated user: "</span> <span class="token operator">+</span> userName<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@PostLoad</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">logUserLoad</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    fullName <span class="token operator">=</span> firstName <span class="token operator">+</span> <span class="token string">" "</span> <span class="token operator">+</span> lastName<span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>테스트를 실행하면 annotated 메소드로부터 나오는 로깅 명령문이 연속적으로 이어지는 것을 볼 수 있다. 데이터베이스에서 user를 로드할 때 user의 full name이 채워질 것을 예상할 수 있다.</p>
<h2 id="annotating-an-entitylistener">Annotating an EntityListener</h2>
<p>update 콜백을 처리하기 위해 별개의 EntityListener를 사용하는 예시로 확장하겠다. 모든 엔터티에 적용하고 싶은 작업이 있다면 엔터티 내부에 메소드를 배치하는 것보다 이 접근법을 더 선호할 수 있다.</p>
<p>예시) User 테이블의 모든 활동을 로그로 남기기 위해 AuditTrailListener를 생성</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AuditTrailListener</span> <span class="token punctuation">{</span>
    <span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token class-name">Log</span> log <span class="token operator">=</span> <span class="token class-name">LogFactory</span><span class="token punctuation">.</span><span class="token function">getLog</span><span class="token punctuation">(</span><span class="token class-name">AuditTrailListener</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    
    <span class="token annotation punctuation">@PrePersist</span>
    <span class="token annotation punctuation">@PreUpdate</span>
    <span class="token annotation punctuation">@PreRemove</span>
    <span class="token keyword">private</span> <span class="token keyword">void</span> <span class="token function">beforeAnyUpdate</span><span class="token punctuation">(</span><span class="token class-name">User</span> user<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span>user<span class="token punctuation">.</span><span class="token function">getId</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">==</span> <span class="token number">0</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"[USER AUDIT] About to add a user"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token punctuation">{</span>
            log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"[USER AUDIT] About to update/delete user: "</span> <span class="token operator">+</span> user<span class="token punctuation">.</span><span class="token function">getId</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
    
    <span class="token annotation punctuation">@PostPersist</span>
    <span class="token annotation punctuation">@PostUpdate</span>
    <span class="token annotation punctuation">@PostRemove</span>
    <span class="token keyword">private</span> <span class="token keyword">void</span> <span class="token function">afterAnyUpdate</span><span class="token punctuation">(</span><span class="token class-name">User</span> user<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"[USER AUDIT] add/update/delete complete for user: "</span> <span class="token operator">+</span> user<span class="token punctuation">.</span><span class="token function">getId</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
    <span class="token annotation punctuation">@PostLoad</span>
    <span class="token keyword">private</span> <span class="token keyword">void</span> <span class="token function">afterLoad</span><span class="token punctuation">(</span><span class="token class-name">User</span> user<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"[USER AUDIT] user loaded from database: "</span> <span class="token operator">+</span> user<span class="token punctuation">.</span><span class="token function">getId</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>예시) User 엔터티로 돌아가 @EntityListener 어노테이션을 클래스에 추가</p>
<pre><code class="language-java"><span class="token annotation punctuation">@EntityListeners</span><span class="token punctuation">(</span><span class="token class-name">AuditTrailListener</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@Entity</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">User</span> <span class="token punctuation">{</span>
    <span class="token comment">//...</span>
<span class="token punctuation">}</span></code></pre>
<p>테스트를 실행하면 update 액션 각각에 따른 두 세트의 로그 메시지와 데이터베이스에서 user가 로드된 후의 로그 메시지를 얻을 수 있다.</p>