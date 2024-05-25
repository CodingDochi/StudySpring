<p>저장소 인터페이스를 정의하려면 먼저 도메인 클래스별 저장소 인터페이스를 정의해야 합니다. 인터페이스는 <code>Repository</code>를 확장해야 하며 도메인 클래스 및 ID 유형으로 입력되어야 합니다. 해당 도메인 유형에 대한 CRUD 메소드를 노출하려면 <code>Repository</code> 대신 <code>CrudRepository</code> 또는 그 변형 중 하나를 확장할 수 있습니다.</p>
<h1 id="fine-tuning-repository-definition">Fine-tuning Repository Definition</h1>
<p>저장소 인터페이스를 시작하는 방법에는 몇 가지 변형이 있습니다.</p>
<p>일반적인 접근 방식은 CRUD 기능에 대한 메서드를 제공하는 <code>CrudRepository</code>를 확장하는 것입니다. CRUD는 생성(Create), 읽기(Read), 업데이트(Update), 삭제(Delete)를 의미합니다. 버전 3.0에서는 <code>CrudRepository</code>와 매우 유사한 <code>ListCrudRepository</code>도 도입했지만 여러 엔터티를 반환하는 메서드의 경우 사용하기 더 쉬운 <code>Iterable</code> 대신 <code>List</code>를 반환합니다.</p>
<p>reactive store를 사용하는 경우 사용 중인 반응 프레임워크에 따라 <code>ReactiveCrudRepository</code> 또는 <code>RxJava3CrudRepository</code>를 선택할 수 있습니다.</p>
<p>Kotlin을 사용하는 경우 Kotlin의 코루틴을 활용하는 <code>CoroutineCrudRepository</code>를 선택할 수 있습니다.</p>
<p>추가로 <code>Sort</code> 추상화를 지정하거나 첫 번째 경우 <code>Pageable</code> 추상화를 지정할 수 있는 메서드가 필요한 경우 <code>PagingAndSortingRepository</code>, <code>ReactiveSortingRepository</code>, <code>RxJava3SortingRepository</code> 또는 <code>CoroutineSortingRepository</code>를 확장할 수 있습니다. 다양한 정렬 저장소는 3.0 이전의 Spring Data 버전에서처럼 더 이상 해당 CRUD 저장소를 확장하지 않습니다. 따라서 두 인터페이스의 기능을 모두 사용하려면 두 인터페이스를 모두 확장해야 합니다.</p>
<p>Spring Data 인터페이스를 확장하지 않으려면 <code>@RepositoryDefinition</code>을 사용하여 저장소 인터페이스에 주석을 달 수도 있습니다. CRUD 저장소 인터페이스 중 하나를 확장하면 엔터티를 조작하기 위한 전체 메서드 세트가 노출됩니다. 노출되는 메서드를 선택적으로 선택하려면 CRUD 저장소에서 도메인 저장소로 노출하려는 메서드를 복사하세요. 이때 메소드의 반환 유형을 변경할 수 있습니다. Spring Data는 가능하다면 반환 유형을 존중합니다. 예를 들어 여러 엔터티를 반환하는 메서드의 경우 <code>Iterable&lt;T&gt;</code>, <code>List&lt;T&gt;</code>, <code>Collection&lt;T&gt;</code> 또는 VAVR 목록을 선택할 수 있습니다.</p>
<p>애플리케이션의 많은 저장소에 동일한 메소드 세트가 있어야 하는 경우 상속할 자체 기본 인터페이스를 정의할 수 있습니다. 이러한 인터페이스에는 <code>@NoRepositoryBean</code>으로 annotation을 달아야 합니다. 이렇게 하면 Spring Data가 인스턴스를 직접 생성하려고 시도하고 해당 저장소에 대한 엔터티를 결정할 수 없기 때문에 실패하는 것을 방지할 수 있습니다. 왜냐하면 여전히 generic type 변수가 포함되어 있기 때문입니다.</p>
<p>다음 예에서는 CRUD 메서드(이 경우 <code>findById</code> 및 <code>save</code>)를 선택적으로 노출하는 방법을 보여줍니다.</p>
<p>Selectively exposing CRUD methods</p>
<pre><code class="language-java"><span class="token annotation punctuation">@NoRepositoryBean</span>
<span class="token keyword">interface</span> <span class="token class-name">MyBaseRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">,</span> ID<span class="token punctuation">&gt;</span></span> <span class="token keyword">extends</span> <span class="token class-name">Repository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">,</span> ID<span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

  <span class="token class-name">Optional</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token function">findById</span><span class="token punctuation">(</span><span class="token class-name">ID</span> id<span class="token punctuation">)</span><span class="token punctuation">;</span>

  <span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">S</span> <span class="token keyword">extends</span> <span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token class-name">S</span> <span class="token function">save</span><span class="token punctuation">(</span><span class="token class-name">S</span> entity<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token keyword">interface</span> <span class="token class-name">UserRepository</span> <span class="token keyword">extends</span> <span class="token class-name">MyBaseRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>
  <span class="token class-name">User</span> <span class="token function">findByEmailAddress</span><span class="token punctuation">(</span><span class="token class-name">EmailAddress</span> emailAddress<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이전 예에서는 모든 도메인 저장소에 대한 공통 기본 인터페이스를 정의하고 <code>findById(...)</code> 및 <code>save(...)</code>를 노출했습니다. 이러한 메소드는 Spring Data에서 제공하는 선택한 저장소의 기본 저장소 구현으로 라우팅됩니다( 예를 들어 JPA를 사용하는 경우 구현은 <code>SimpleJpaRepository</code>입니다. 이는 <code>CrudRepository</code>의 메서드 서명과 일치하기 때문입니다. 따라서 <code>UserRepository</code>는 이제 사용자를 저장하고, ID로 개별 사용자를 찾고, 이메일 주소로 <code>Users</code>를 찾는 쿼리를 실행할 수 있습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
중간 저장소 인터페이스에는 <code>@NoRepositoryBean</code>이라는 annotation이 붙습니다. Spring Data가 런타임에 인스턴스를 생성하지 말아야 하는 모든 저장소 인터페이스에 해당 annotation을 추가했는지 확인하세요.</p>
</blockquote>
<h1 id="using-repositories-with-multiple-spring-data-modules">Using Repositories with Multiple Spring Data Modules</h1>
<p>애플리케이션에서 고유한 Spring Data 모듈을 사용하면 정의된 범위의 모든 저장소 인터페이스가 Spring Data 모듈에 바인딩되므로 작업이 간단해집니다. 때때로 애플리케이션에서는 둘 이상의 Spring Data 모듈을 사용해야 합니다. 이러한 경우 저장소 정의는 지속성 기술을 구별해야 합니다. 클래스 경로에서 여러 저장소 팩토리를 감지하면 Spring Data는 엄격한 저장소 구성 모드로 들어갑니다. 엄격한 구성은 저장소 또는 도메인 클래스에 대한 세부 정보를 사용하여 저장소 정의에 대한 Spring Data 모듈 바인딩을 결정합니다.</p>
<ol>
<li>
<p>저장소 정의가<a href="https://docs.spring.io/spring-data/jpa/reference/repositories/definition.html#repositories.multiple-modules.types"> 모듈별 저장소를 확장하는 경우</a> 이는 특정 Spring Data 모듈에 대한 유효한 후보입니다.</p>
</li>
<li>
<p>도메인 클래스에 <a href="https://docs.spring.io/spring-data/jpa/reference/repositories/definition.html#repositories.multiple-modules.annotations">모듈별 type annotation이 달린 경우</a> 이는 특정 Spring Data 모듈에 대한 유효한 후보입니다. Spring Data 모듈은 제3자 annotation(예: JPA의 <code>@Entity</code>)을 허용하거나 자체 annotation(예: Spring Data MongoDB 및 Spring Data Elasticsearch의 <code>@Document</code>)을 제공합니다.</p>
</li>
</ol>
<p>다음 예에서는 모듈별 인터페이스(이 경우 JPA)를 사용하는 저장소를 보여줍니다.</p>
<p>Example 1. Repository definitions using module-specific interfaces</p>
<pre><code class="language-java"><span class="token keyword">interface</span> <span class="token class-name">MyRepository</span> <span class="token keyword">extends</span> <span class="token class-name">JpaRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span> <span class="token punctuation">}</span>

<span class="token annotation punctuation">@NoRepositoryBean</span>
<span class="token keyword">interface</span> <span class="token class-name">MyBaseRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">,</span> ID<span class="token punctuation">&gt;</span></span> <span class="token keyword">extends</span> <span class="token class-name">JpaRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">,</span> ID<span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span> … <span class="token punctuation">}</span>

<span class="token keyword">interface</span> <span class="token class-name">UserRepository</span> <span class="token keyword">extends</span> <span class="token class-name">MyBaseRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span> … <span class="token punctuation">}</span></code></pre>
<p><code>MyRepository</code> 및 <code>UserRepository</code>는 type 계층 구조에서 <code>JpaRepository</code>를 확장합니다. 이는 Spring Data JPA 모듈의 유효한 후보입니다.</p>
<p>다음 예에서는 generic 인터페이스를 사용하는 저장소를 보여줍니다.</p>
<p>Example 2. Repository definitions using generic interfaces</p>
<pre><code class="language-java"><span class="token keyword">interface</span> <span class="token class-name">AmbiguousRepository</span> <span class="token keyword">extends</span> <span class="token class-name">Repository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span> … <span class="token punctuation">}</span>

<span class="token annotation punctuation">@NoRepositoryBean</span>
<span class="token keyword">interface</span> <span class="token class-name">MyBaseRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">,</span> ID<span class="token punctuation">&gt;</span></span> <span class="token keyword">extends</span> <span class="token class-name">CrudRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">,</span> ID<span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span> … <span class="token punctuation">}</span>

<span class="token keyword">interface</span> <span class="token class-name">AmbiguousUserRepository</span> <span class="token keyword">extends</span> <span class="token class-name">MyBaseRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span> … <span class="token punctuation">}</span></code></pre>
<p><code>AmbiguousRepository</code> 및 <code>AmbiguousUserRepository</code>는 유형 계층 구조에서 <code>Repository</code> 및 <code>CrudRepository</code>만 확장합니다. 고유한 Spring Data 모듈을 사용할 때는 괜찮지만 여러 모듈이 이러한 저장소를 바인딩해야 하는 특정 Spring Data를 구별할 수 없습니다.</p>
<p>다음 예에서는 annotation이 포함된 도메인 클래스를 사용하는 저장소를 보여줍니다.</p>
<p>Example 3. Repository definitions using domain classes with annotations</p>
<pre><code class="language-java"><span class="token keyword">interface</span> <span class="token class-name">PersonRepository</span> <span class="token keyword">extends</span> <span class="token class-name">Repository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span> … <span class="token punctuation">}</span>

<span class="token annotation punctuation">@Entity</span>
<span class="token keyword">class</span> <span class="token class-name">Person</span> <span class="token punctuation">{</span> … <span class="token punctuation">}</span>

<span class="token keyword">interface</span> <span class="token class-name">UserRepository</span> <span class="token keyword">extends</span> <span class="token class-name">Repository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span> … <span class="token punctuation">}</span>

<span class="token annotation punctuation">@Document</span>
<span class="token keyword">class</span> <span class="token class-name">User</span> <span class="token punctuation">{</span> … <span class="token punctuation">}</span></code></pre>
<p><code>PersonRepository</code>는 JPA <code>@Entity</code> annotation이 달린 <code>Person</code>을 참조하므로 이 저장소는 분명히 Spring Data JPA에 속합니다. <code>UserRepository</code>는 Spring Data MongoDB의 <code>@Document</code> annotation이 달린 <code>User</code>를 참조합니다.</p>
<p>다음 나쁜 예는 annotation이 혼합된 도메인 클래스를 사용하는 저장소를 보여줍니다.</p>
<p>Example 4. Repository definitions using domain classes with mixed annotations</p>
<pre><code class="language-java"><span class="token keyword">interface</span> <span class="token class-name">JpaPersonRepository</span> <span class="token keyword">extends</span> <span class="token class-name">Repository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span> … <span class="token punctuation">}</span>

<span class="token keyword">interface</span> <span class="token class-name">MongoDBPersonRepository</span> <span class="token keyword">extends</span> <span class="token class-name">Repository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span> … <span class="token punctuation">}</span>

<span class="token annotation punctuation">@Entity</span>
<span class="token annotation punctuation">@Document</span>
<span class="token keyword">class</span> <span class="token class-name">Person</span> <span class="token punctuation">{</span> … <span class="token punctuation">}</span></code></pre>
<p>이 예에서는 JPA 및 Spring Data MongoDB 주석을 모두 사용하는 도메인 클래스를 보여줍니다. <code>JpaPersonRepository</code>와 <code>MongoDBPersonRepository</code>라는 두 개의 저장소를 정의합니다. 하나는 JPA용이고 다른 하나는 MongoDB용입니다. Spring Data는 더 이상 리포지토리를 구분할 수 없으므로 정의되지 않은 동작이 발생합니다.</p>
<p>특정 Spring Data 모듈에 대한 저장소 후보를 식별하기 위한 엄격한 저장소 구성에 <a href="https://docs.spring.io/spring-data/jpa/reference/repositories/definition.html#repositories.multiple-modules.types">저장소 type 세부사항</a> 및 <a href="https://docs.spring.io/spring-data/jpa/reference/repositories/definition.html#repositories.multiple-modules.annotations">구별되는 도메인 클래스 annotation</a>이 사용됩니다. 동일한 도메인 유형에 여러 영속성 기술별 annotation을 사용하는 것이 가능하며 여러 영속성 기술에서 도메인 type을 재사용할 수 있습니다. 그러나 Spring Data는 더 이상 저장소를 바인딩할 고유 모듈을 결정할 수 없습니다.</p>
<p>저장소를 구별하는 마지막 방법은 저장소 기본 패키지의 범위를 지정하는 것입니다. base package는 저장소 인터페이스 정의를 검색하기 위한 시작점을 정의합니다. 이는 저장소 정의가 적절한 패키지에 있다는 것을 의미합니다. 기본적으로 anootation 기반 구성은 configuration 클래스 패키지를 사용합니다. <a href="https://docs.spring.io/spring-data/jpa/reference/repositories/create-instances.html#repositories.create-instances.xml">XML 기반 구성의 base package</a>는 필수입니다.</p>
<p>다음 예에서는 base package의 annotation 기반 구성을 보여줍니다.</p>
<p>Annotation-driven configuration of base packages</p>
<pre><code class="language-java"><span class="token annotation punctuation">@EnableJpaRepositories</span><span class="token punctuation">(</span>basePackages <span class="token operator">=</span> <span class="token string">"com.acme.repositories.jpa"</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@EnableMongoRepositories</span><span class="token punctuation">(</span>basePackages <span class="token operator">=</span> <span class="token string">"com.acme.repositories.mongo"</span><span class="token punctuation">)</span>
<span class="token keyword">class</span> <span class="token class-name">Configuration</span> <span class="token punctuation">{</span> … <span class="token punctuation">}</span></code></pre>