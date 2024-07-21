<p>모든 Hibernate 엔터티는 프레임워크 내에서 Transient, Managed, Detached, Deleted 상태의 라이프 사이클을 갖는다<br>
<img src="https://media.geeksforgeeks.org/wp-content/uploads/20210626212614/GFGHibernateLifecycle.png"></p>
<h2 id="helper-method">Helper Method</h2>
<ul>
<li>HibernateLifecycleUtil.getManagedEntities(session) : 세션 내부 저장소에서 모든 Managed 엔터티 가져오기</li>
<li>DirtyDataInspector.getDirtyEntities() : 'dirty'로 표시된 모든 엔터티 목록 가져오기</li>
<li>HibernateLifecycleUtil.queryCount(query) : 임베디드 데이터베이스에 대해 count(*) 쿼리를 수행하는 편리한 방법</li>
<li>static으로 import하여 사용한다</li>
</ul>
<blockquote>
<p>하이버네이트(Hibernate)에서 "더티"란, 영속성 컨텍스트(Persistence Context)에서 관리되는 엔티티(Entity)의 상태가 변경되었음을 나타내는 개념입니다. 더티 상태의 엔티티는 변경 사항이 발생한 후 영속성 컨텍스트에서 추적되며, 이러한 변경 사항을 데이터베이스에 동기화하는 작업을 말합니다.</p>
</blockquote>
<blockquote>
<p>더티 체킹(Dirty Checking)은 하이버네이트가 엔티티의 상태 변화를 감지하여 자동으로 데이터베이스에 반영하는 메커니즘입니다. 하이버네이트는 트랜잭션이 커밋되거나 flush()되는 시점에 이 변경 사항을 감지하고, 데이터베이스의 해당 레코드를 업데이트합니다. 이렇게 더티 체킹을 통해 개발자는 명시적으로 SQL 쿼리를 작성하지 않고도 엔티티의 변경 사항을 효율적으로 데이터베이스에 반영할 수 있습니다.</p>
</blockquote>
<blockquote>
<p>"영속성 컨텍스트"는 객체와 데이터베이스 간의 매핑을 관리하는 개념으로, ORM 프레임워크에서 사용되며 객체의 상태 관리와 데이터베이스와의 동기화를 담당합니다.<br>
"데이터베이스 세션"은 관계형 데이터베이스에서 클라이언트와 데이터베이스 간의 연결을 나타내는 개념으로, 데이터베이스 서버와의 상호 작용을 관리합니다.</p>
</blockquote>
<h2 id="persistence-context">Persistence Context</h2>
<ul>
<li>persistence context란 클라이언트 코드와 데이터 저장소 사이의 스테이징 영역이다.</li>
<li>persistence context는 <a href="https://martinfowler.com/eaaCatalog/unitOfWork.html">Unit of Work</a>패턴을 구현한 것이다. 모든 데이터의 변경사항을 추적하여 비즈니스 트랜잭션이 끝날 때 데이터베이스에 다시 동기화하는 일을 담당한다.</li>
<li>JPA EntityManager 및 Hibernate의 Session으로 persistence context를 구현할 수 있다.</li>
</ul>
<blockquote>
<p>작업단위(Unit of work) : 비즈니스 트랜잭션의 영향을 받는 개체 목록을 유지 관리하고 변경 사항 작성 및 동시성 문제 해결을 조정</p>
</blockquote>
<blockquote>
<p>개체 모델을 변경할 때마다 데이터베이스를 변경할 수 있지만 이로 인해 매우 작은 데이터베이스 호출이 많이 발생하여 매우 느려질 수 있습니다. 또한 전체 상호 작용에 대해 트랜잭션을 열어야 하는데, 이는 여러 요청에 걸쳐 있는 비즈니스 트랜잭션이 있는 경우 비실용적입니다. 일관되지 않은 읽기를 피하기 위해 읽은 개체를 추적해야 하는 경우 상황은 더욱 악화됩니다.</p>
<p>작업 단위는 데이터베이스에 영향을 줄 수 있는 비즈니스 트랜잭션 중에 수행하는 모든 작업을 추적합니다. 완료되면 작업의 결과로 데이터베이스를 변경하기 위해 수행해야 하는 모든 작업을 파악합니다.</p>
</blockquote>
<h2 id="managed-entity">Managed Entity</h2>
<ul>
<li>Managed 엔터티는 데이터베이스 테이블의 행을 나타냄. (해당 행이 데이터베이스에 아직 존재하지 않더라도)</li>
<li>현재 실행중인 세션에 의해 모든 변경사항은 추적되고 데이터베이스에 자동으로 전파된다.</li>
<li>세션은 Detatched 엔터티를 다시 연결하거나 엔터티를 데이터베이스에서 불러온다.</li>
</ul>
<p>예시) 엔터티 변경사항을 데이터 저장소에 반영하는 과정 </p>
<pre><code class="language-null">+-------------------+-------+
| Name              |  ID   |
+-------------------+-------+
| Cristiano Ronaldo | 1     |
| Lionel Messi      | 2     |
| Gianluigi Buffon  | 3     |
+-------------------+-------+</code></pre>
<p>몇가지 샘플 데이터로 데이터 저장소를 위와 같이 초기화 시킨다.<br>
샘플 애플리케이션은 FootballPlayer 클래스를 엔터티로 정의하고 있다. 엔터티에서 Buffon의 이름을 Gigi Buffon대신 Gianluigi Buffon로 변경하고자 한다.</p>
<pre><code class="language-java"><span class="token class-name">Session</span> session <span class="token operator">=</span> sessionFactory<span class="token punctuation">.</span><span class="token function">openSession</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>세션을 가져와서 Unit of Work을 시작한다.<br>
서버 환경에서는 context-aware 프록시를 통해 세션을 코드에 주입할 수도 있다. Unit of Work의 비즈니스 트랜잭션을 캡슐화 하기 위해 세션이 필요한 원칙은 같다. </p>
<blockquote>
<p>Context-aware 프록시"는 프로그래밍과 관련된 개념으로, 코드 실행 컨텍스트에 따라 프록시 객체를 동적으로 변경하거나 추가 동작을 삽입하는 방법을 다루는 것이다. 프록시 객체는 원본 객체를 대신하여 동작하는 객체이다.</p>
</blockquote>
<blockquote>
<p>"Context-aware 프록시"는 객체 지향 프로그래밍에서 사용되는 개념으로, 주로 AOP (Aspect-Oriented Programming)와 관련이 있습니다. 이는 객체가 어떤 "컨텍스트"에서 실행되는지 인식하고 이를 활용하여 추가적인 동작을 수행하거나 제어하는 데 사용됩니다. </p>
</blockquote>
<blockquote>
<p>Context-aware 프록시는 변경 사항을 추적하여 데이터베이스에 대한 작업을 미루는 것과는 조금 다릅니다. 이는 주로 객체의 동작을 보강하고 관리하는 목적으로 사용되며, 데이터베이스와의 상호 작용 뿐만 아니라 다양한 상황에서 유용하게 활용될 수 있습니다.</p>
</blockquote>
<pre><code class="language-java"><span class="token function">assertThat</span><span class="token punctuation">(</span><span class="token function">getManagedEntities</span><span class="token punctuation">(</span>session<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEmpty</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">FootballPlayer</span><span class="token punctuation">&gt;</span></span> players <span class="token operator">=</span> s<span class="token punctuation">.</span><span class="token function">createQuery</span><span class="token punctuation">(</span><span class="token string">"from FootballPlayer"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getResultList</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token function">assertThat</span><span class="token punctuation">(</span><span class="token function">getManagedEntities</span><span class="token punctuation">(</span>session<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEqualTo</span><span class="token punctuation">(</span><span class="token number">3</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>일반적으로 s라는 변수는 주로 Hibernate 세션(Session) 객체를 가리키는데 사용되는 관례적인 변수명이다.<br>
첫번째 줄의 assert 문에서 볼 수 있듯이 처음 Session을 확보했을 때 persistence context 저장소는 비어있다.<br>
그 다음, 데이터베이스에서 데이터를 가져와, 그 데이터를 표현할 엔터티를 생성하고 반환하는 쿼리를 실행한다.<br>
세션은 내부적으로 persistence context 저장소에 열어둔 모든 엔터티를 추적한다. 위 예시에서는 쿼리 실행 후 3개의 엔터티가 세션의 내부 저장소에 포함될 것이다.</p>
<pre><code class="language-java"><span class="token class-name">Transaction</span> transaction <span class="token operator">=</span> session<span class="token punctuation">.</span><span class="token function">getTransaction</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
transaction<span class="token punctuation">.</span><span class="token function">begin</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">FootballPlayer</span> gigiBuffon <span class="token operator">=</span> players<span class="token punctuation">.</span><span class="token function">stream</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">filter</span><span class="token punctuation">(</span>p <span class="token operator">-&gt;</span> p<span class="token punctuation">.</span><span class="token function">getId</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">==</span> <span class="token number">3</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">findFirst</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

gigiBuffon<span class="token punctuation">.</span><span class="token function">setName</span><span class="token punctuation">(</span><span class="token string">"Gianluigi Buffon"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
transaction<span class="token punctuation">.</span><span class="token function">commit</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token function">assertThat</span><span class="token punctuation">(</span><span class="token function">getDirtyEntities</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEqualTo</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token function">assertThat</span><span class="token punctuation">(</span><span class="token function">getDirtyEntities</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token number">0</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEqualTo</span><span class="token punctuation">(</span><span class="token string">"Gianluigi Buffon"</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>트랜잭션이 commit() 또는 flush()를 호출할 때 세션은 dirty 엔터티를 추적리스트에서 찾아내어 데이터베이스의 상태에 동기화한다.<br>
우리가 엔터티를 변경했다는 것을 세션에 알리기 위한 메소드를 호출할 필요가 없다는 점에 주목. Managed 엔터티는 데이터베이스에 자동적으로 변경사항을 전파하기 때문이다.<br>
Managed 엔터티는 항상 영속(persistent) 엔터티이다. 데이터베이스에 행 표현이 생성되지 않았더라도 데이터베이스 식별자를 가지고 있어야 한다. 즉 Unit of Work의 종료를 INSERT문이 보류하고 있는 것이다.</p>
<blockquote>
<p>"INSERT문이 보류하고 있는 것"은 새로운 데이터를 데이터베이스에 추가하려는 상황에서 해당 데이터의 영속성 컨텍스트에 대한 변경 사항이 트랜잭션의 커밋 시점까지 보류되고 있다는 의미입니다.<br>
이는 객체의 변경 사항이 데이터베이스에 반영될 때, INSERT문을 실행하여 데이터베이스에 데이터를 추가하려고 할 때 발생할 수 있는 상황을 나타냅니다.</p>
</blockquote>
<h2 id="detached-entity">Detached Entity</h2>
<ul>
<li>Managed 엔터티와 달리 Detached 엔터티는 persistence context에 의해 추적되지 않는다.</li>
<li>Detached 엔터티는 데이터베이스 행에 따라 ID 값이 결정되는 POJO</li>
<li>엔터티를 로드하는데 사용된 세션이 닫히거나 Session.evict(entity) 또는 Session.clear()를 호출할 때 엔터티가 Detached 상태가 된다.</li>
<li>Session.merge(entity) 또는 Session.update(entity)로 세션을 다시 연결할 수 있다. </li>
</ul>
<blockquote>
<p>"Detached 엔터티는 데이터베이스 행에 따라 ID 값이 결정되는 POJO"라는 내용은 일반적으로 올바르지 않을 수 있습니다. 엔터티의 ID 값은 주로 데이터베이스와 연결된 영역에서 결정됩니다. 일반적으로 JPA(Java Persistence API)와 같은 ORM 프레임워크를 사용할 때, 엔터티의 ID 값은 다음과 같은 방식으로 결정됩니다:</p>
<ul>
<li>
<p>Assigned ID (수동 할당): 개발자가 직접 ID 값을 할당하는 경우입니다. 데이터베이스와 무관하게 개발자가 ID 값을 지정합니다.</p>
</li>
<li>
<p>Generated ID (자동 생성): 데이터베이스가 자동으로 ID 값을 생성하는 경우입니다. 대표적으로 AUTO_INCREMENT (MySQL), IDENTITY (MS SQL Server), SEQUENCE (Oracle)와 같은 자동 증가 기능을 사용하여 데이터베이스가 ID 값을 생성합니다.</p>
</li>
<li>
<p>Natural ID (자연 키): 비즈니스 로직에서 사용되는 유일한 속성을 엔터티의 ID로 사용하는 경우입니다. 예를 들어, 이메일 주소나 소셜 보안 번호와 같은 자연스러운 식별자를 사용할 수 있습니다.</p>
</li>
<li>
<p>Composite ID (복합 키): 여러 개의 속성을 결합하여 엔터티의 ID를 생성하는 경우입니다. 엔터티가 다른 엔터티와의 관계를 가지고 있을 때 주로 사용됩니다.</p>
</li>
<li>
<p>Detached 엔터티는 영속성 컨텍스트와의 연결이 끊긴 상태를 나타냅니다. 이때 ID 값은 이전에 영속성 컨텍스트에서 관리되었던 값을 가지며, 데이터베이스 행에 따라 결정되는 것은 아닙니다. </p>
</li>
</ul>
</blockquote>
<p>예시) Managed 엔터티가 Session.evict()에 의해 Detached 됨</p>
<blockquote>
<p>여기서 1L의 L은 Long 형식을 나타내며, ID 값으로 사용됩니다. 데이터베이스에 저장된 FootballPlayer 엔터티 중에서 ID 값이 1인 엔터티를 조회하고, 조회한 엔터티 정보를 cr7 변수에 할당하는 것을 의미합니다.</p>
</blockquote>
<pre><code class="language-java"><span class="token class-name">FootballPlayer</span> cr7 <span class="token operator">=</span> session<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token class-name">FootballPlayer</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> <span class="token number">1L</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token function">assertThat</span><span class="token punctuation">(</span><span class="token function">getManagedEntities</span><span class="token punctuation">(</span>session<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEqualTo</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token function">assertThat</span><span class="token punctuation">(</span><span class="token function">getManagedEntities</span><span class="token punctuation">(</span>session<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token number">0</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getId</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEqualTo</span><span class="token punctuation">(</span>cr7<span class="token punctuation">.</span><span class="token function">getId</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

session<span class="token punctuation">.</span><span class="token function">evict</span><span class="token punctuation">(</span>cr7<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token function">assertThat</span><span class="token punctuation">(</span><span class="token function">getManagedEntities</span><span class="token punctuation">(</span>session<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEqualTo</span><span class="token punctuation">(</span><span class="token number">0</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<pre><code class="language-java">cr7<span class="token punctuation">.</span><span class="token function">setName</span><span class="token punctuation">(</span><span class="token string">"CR7"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
transaction<span class="token punctuation">.</span><span class="token function">commit</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token function">assertThat</span><span class="token punctuation">(</span><span class="token function">getDirtyEntities</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEmpty</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>예시) Session.update()에 의해 reattach되는 세션</p>
<pre><code class="language-java"><span class="token class-name">FootballPlayer</span> messi <span class="token operator">=</span> session<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token class-name">FootballPlayer</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> <span class="token number">2L</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

session<span class="token punctuation">.</span><span class="token function">evict</span><span class="token punctuation">(</span>messi<span class="token punctuation">)</span><span class="token punctuation">;</span>
messi<span class="token punctuation">.</span><span class="token function">setName</span><span class="token punctuation">(</span><span class="token string">"Leo Messi"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
transaction<span class="token punctuation">.</span><span class="token function">commit</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token function">assertThat</span><span class="token punctuation">(</span><span class="token function">getDirtyEntities</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEmpty</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

transaction <span class="token operator">=</span> <span class="token function">startTransaction</span><span class="token punctuation">(</span>session<span class="token punctuation">)</span><span class="token punctuation">;</span>
session<span class="token punctuation">.</span><span class="token function">update</span><span class="token punctuation">(</span>messi<span class="token punctuation">)</span><span class="token punctuation">;</span>
transaction<span class="token punctuation">.</span><span class="token function">commit</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token function">assertThat</span><span class="token punctuation">(</span><span class="token function">getDirtyEntities</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEqualTo</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token function">assertThat</span><span class="token punctuation">(</span><span class="token function">getDirtyEntities</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token number">0</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEqualTo</span><span class="token punctuation">(</span><span class="token string">"Leo Messi"</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>아래 예시에서 생성자를 통해 엔터티를 인스턴스화 했다.<br>
ID필드의 값을 setId()를 이용해 3으로 설정했다.<br>
이는 Gigi Buffon에 대한 영속(persistent) 데이터의 아이디 값이다. Session.update()를 호출하는 것은 다른 persistence context에서 엔터티를 로드한 것과 같은 효과를 가진다.<br>
세션은 다시 연결된(re-attached) 엔터티가 어디서 기원했는지 구별하지 않는다.<br>
HTML form 값으로부터 분리된(detached) 엔터티를 생성하는 것은 웹 애플리케이션에서 흔한 시나리오다.<br>
세션의 관점에서, 분리된 엔티티(detached entity)는 식별 값(identity value)이 영속 데이터(persistent data)와 일치하는 단순한 엔티티일 뿐이다.</p>
<h2 id="transient-entity">Transient Entity</h2>
<ul>
<li>Transient 엔터티는 영속(persistent) 저장소에 표현되지 않고, 세션에 의해 관리되지 않는 단순한 엔터티이다.</li>
<li>생성자로 새 엔터티를 초기화하는 경우가 Transient 엔터티의 대표적 예시다. </li>
<li>Transient 엔터티를 persistent로 만들기 위해서는 Session.save(entity) 또는 Session.saveOrUpdate(entity)를 호출해야 한다.</li>
</ul>
<pre><code class="language-java"><span class="token class-name">FootballPlayer</span> neymar <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">FootballPlayer</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
neymar<span class="token punctuation">.</span><span class="token function">setName</span><span class="token punctuation">(</span><span class="token string">"Neymar"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
session<span class="token punctuation">.</span><span class="token function">save</span><span class="token punctuation">(</span>neymar<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token function">assertThat</span><span class="token punctuation">(</span><span class="token function">getManagedEntities</span><span class="token punctuation">(</span>session<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEqualTo</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token function">assertThat</span><span class="token punctuation">(</span>neymar<span class="token punctuation">.</span><span class="token function">getId</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isNotNull</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token keyword">int</span> count <span class="token operator">=</span> <span class="token function">queryCount</span><span class="token punctuation">(</span><span class="token string">"select count(*) from Football_Player where name='Neymar'"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token function">assertThat</span><span class="token punctuation">(</span>count<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEqualTo</span><span class="token punctuation">(</span><span class="token number">0</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

transaction<span class="token punctuation">.</span><span class="token function">commit</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
count <span class="token operator">=</span> <span class="token function">queryCount</span><span class="token punctuation">(</span><span class="token string">"select count(*) from Football_Player where name='Neymar'"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token function">assertThat</span><span class="token punctuation">(</span>count<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEqualTo</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>Session.save(entity)를 실행하면 해당 엔터티에 ID값이 부여되고 세션에 의해 관리(managed)된다.<br>
Unit of Work이 종료될 때까지 INSERT 실행이 지연되기 때문에 데이터베이스에서는 아직 사용할 수 없을 수도 있다.</p>
<h2 id="deleted-entity">Deleted Entity</h2>
<ul>
<li>Session.delete(entity)가 호출되고 세션이 엔터티를 삭제하도록 표시한 경우 엔터티는 삭제(제거) 상태가 된다. </li>
<li>Unit of Work의 끝에 DELETE 명령이 시작된다.</li>
<li>그러나 엔터티는 Unit of Work이 끝날 때까지 Persistence Context 저장소에 남아있다.</li>
</ul>
<pre><code class="language-java">session<span class="token punctuation">.</span><span class="token function">delete</span><span class="token punctuation">(</span>neymar<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token function">assertThat</span><span class="token punctuation">(</span><span class="token function">getManagedEntities</span><span class="token punctuation">(</span>session<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token number">0</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getStatus</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEqualTo</span><span class="token punctuation">(</span><span class="token class-name">Status</span><span class="token punctuation">.</span>DELETED<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>