<p>이 가이드는 하이퍼미디어 기반 RESTful 프런트 엔드를 통해 관계형 JPA 데이터에 액세스하는 애플리케이션을 만드는 과정을 안내합니다.</p>
<h2 id="what-you-will-build">What You Will Build</h2>
<p>Spring Data REST를 사용하여 데이터베이스에 저장된 Person 객체를 생성하고 검색할 수 있는 Spring 애플리케이션을 구축합니다. Spring Data REST는 Spring HATEOAS 및 Spring Data JPA의 기능을 가져와 자동으로 결합합니다.</p>
<blockquote>
<p>Spring Data REST는 Spring Data Neo4j, Spring Data Gemfire 및 Spring Data MongoDB도 백엔드 데이터 저장소로 지원하지만 이는 이 가이드의 일부가 아닙니다.</p>
</blockquote>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/5b84b3cf-9053-414e-b895-ce1b53c66f4c/image.png"></p>
<h2 id="create-domain-object">Create Domain Object</h2>
<p>다음 목록(src/main/java/guides/accessingdatarest/Person.java)에 표시된 대로 사람을 표시하는 새 도메인 개체를 만듭니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>accessingdatarest</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>persistence</span><span class="token punctuation">.</span><span class="token class-name">Entity</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>persistence</span><span class="token punctuation">.</span><span class="token class-name">GeneratedValue</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>persistence</span><span class="token punctuation">.</span><span class="token class-name">GenerationType</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>persistence</span><span class="token punctuation">.</span><span class="token class-name">Id</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Entity</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Person</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Id</span>
    <span class="token annotation punctuation">@GeneratedValue</span><span class="token punctuation">(</span>strategy <span class="token operator">=</span> <span class="token class-name">GenerationType</span><span class="token punctuation">.</span>AUTO<span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token keyword">long</span> id<span class="token punctuation">;</span>

    <span class="token keyword">private</span> <span class="token class-name">String</span> firstName<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> lastName<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getFirstName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> firstName<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setFirstName</span><span class="token punctuation">(</span><span class="token class-name">String</span> firstName<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>firstName <span class="token operator">=</span> firstName<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getLastName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> lastName<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setLastName</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastName<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>lastName <span class="token operator">=</span> lastName<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>Person</code> 객체에는 이름과 성이 있습니다. (자동으로 생성되도록 설정한 ID 객체도 있으니 따로 다루실 필요는 없습니다.)</p>
<h2 id="create-a-person-repository">Create a Person Repository</h2>
<p>다음으로, 다음 목록(src/main/java/guides/accessingdatarest/PersonRepository.java)에 표시된 대로 간단한 저장소를 생성해야 합니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>accessingdatarest</span><span class="token punctuation">;</span>


<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>repository</span><span class="token punctuation">.</span><span class="token class-name">CrudRepository</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>repository</span><span class="token punctuation">.</span><span class="token class-name">PagingAndSortingRepository</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>repository<span class="token punctuation">.</span>query</span><span class="token punctuation">.</span><span class="token class-name">Param</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>rest<span class="token punctuation">.</span>core<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RepositoryRestResource</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util</span><span class="token punctuation">.</span><span class="token class-name">List</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@RepositoryRestResource</span><span class="token punctuation">(</span>collectionResourceRel <span class="token operator">=</span> <span class="token string">"people"</span><span class="token punctuation">,</span> path<span class="token operator">=</span><span class="token string">"people"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">PersonRepository</span> <span class="token keyword">extends</span> <span class="token class-name">PagingAndSortingRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">,</span> <span class="token class-name">CrudRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

    <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastName</span><span class="token punctuation">(</span><span class="token annotation punctuation">@Param</span><span class="token punctuation">(</span><span class="token string">"name"</span><span class="token punctuation">)</span> <span class="token class-name">String</span> name<span class="token punctuation">)</span><span class="token punctuation">;</span>
    
<span class="token punctuation">}</span></code></pre>
<p>이 저장소는 <code>Person</code> 객체와 관련된 다양한 작업을 수행할 수 있는 인터페이스입니다. Spring Data Commons에 정의된 <code>PagingAndSortingRepository</code> 인터페이스를 확장하여 이러한 작업을 수행합니다.</p>
<p>런타임 시 Spring Data REST는 자동으로 이 인터페이스의 구현을 생성합니다. 그런 다음 <code>@RepositoryRestResource</code> 주석을 사용하여 Spring MVC가 <code>/people</code>에 RESTful 엔드포인트를 생성하도록 지시합니다.</p>
<blockquote>
<p>저장소를 내보내는 데 <code>@RepositoryRestResource</code>가 필요하지 않습니다. <code>/persons</code>의 기본값 대신 <code>/people</code>을 사용하는 등 내보내기 세부 사항을 변경하는 데에만 사용됩니다.</p>
</blockquote>
<p>여기서는 <code>lastName</code>을 기반으로 <code>Person</code> 개체 목록을 검색하는 사용자 지정 쿼리도 정의했습니다. 이 가이드의 뒷부분에서 이를 호출하는 방법을 확인할 수 있습니다.</p>
<p>Spring Boot는 자동으로 Spring Data JPA를 가동하여 <code>PersonRepository</code>의 구체적인 구현을 생성하고 JPA를 사용하여 백엔드 인메모리 데이터베이스와 통신하도록 구성합니다.</p>
<p>Spring Data REST는 Spring MVC 위에 구축됩니다. RESTful 프런트 엔드를 제공하기 위해 Spring MVC 컨트롤러, JSON 변환기 및 기타 Bean 컬렉션을 생성합니다. 이러한 구성 요소는 Spring Data JPA 백엔드에 연결됩니다. Spring Boot를 사용하면 이 모든 것이 자동으로 구성됩니다. 이것이 어떻게 작동하는지 조사하려면 Spring Data REST의 <code>RepositoryRestMvcConfiguration</code>을 살펴보세요.</p>
<hr>
<p><code>@RepositoryRestResource(collectionResourceRel = "people", path="people")</code>: 이 어노테이션은 Spring Data REST가 이 <code>PersonRepository</code>를 REST 엔드포인트로 노출할 때 사용하는 설정입니다. </p>
<ul>
<li><code>collectionResourceRel</code>은 클라이언트에서 이 리소스에 접근할 때 사용할 이름을 나타내며, </li>
<li><code>path</code>는 이 리소스의 URL 경로를 지정합니다.   - 예를 들어, <code>http://yourdomain.com/people</code>과 같은 경로로 이 리소스에 액세스할 수 있습니다.</li>
</ul>
<h2 id="애플리케이션-테스트">애플리케이션 테스트</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/43f5fc9a-ee2d-47ff-a151-a1882affaee3/image.png"></p>
<p>이제 애플리케이션이 실행되었으므로 테스트할 수 있습니다. 원하는 REST 클라이언트를 사용할 수 있습니다. 다음 예에서는 *nix 도구인 <code>curl</code>을 사용합니다.</p>
<p>먼저 최상위 서비스를 보고 싶습니다. 다음 예에서는 그 방법을 보여줍니다.</p>
<pre><code class="language-shell">
$ <span class="token function">curl</span> http://localhost:8080

<span class="token punctuation">{</span>
  <span class="token string">"_links"</span> <span class="token builtin class-name">:</span> <span class="token punctuation">{</span>
    <span class="token string">"people"</span> <span class="token builtin class-name">:</span> <span class="token punctuation">{</span>
      <span class="token string">"href"</span> <span class="token builtin class-name">:</span> <span class="token string">"http://localhost:8080/people{?page,size,sort}"</span>,
      <span class="token string">"templated"</span> <span class="token builtin class-name">:</span> <span class="token boolean">true</span>
    <span class="token punctuation">}</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/a04729e6-e1df-4a41-9641-6fe96a9dee80/image.png"></p>
<p>앞의 예에서는 이 서버가 제공하는 기능을 간략하게 보여줍니다. <code>http://localhost:8080/people</code>에 사용자 링크가 있습니다. <code>?page</code>, <code>?size</code> 및 <code>?sort</code>와 같은 몇 가지 옵션이 있습니다.</p>
<blockquote>
<p>Spring Data REST는 JSON 출력에 HAL 형식을 사용합니다. 이는 유연하며 제공되는 데이터에 인접한 링크를 제공하는 편리한 방법을 제공합니다.</p>
</blockquote>
<hr>
<p>HAL(Hypertext Application Language)은 RESTful 웹 서비스를 위한 JSON 기반의 데이터 형식입니다. 이 형식은 링크를 통해 리소스 간 관계를 표현하고, 클라이언트가 서버로부터 받은 응답을 해석하는 데 도움을 주는 것이 목적입니다.</p>
<p>HAL은 주로 링크와 리소스 간의 관계를 설명하기 위해 <code>_links</code>라는 특별한 필드를 사용합니다. 이를 통해 클라이언트는 해당 리소스와 관련된 작업을 수행할 수 있는 링크를 발견할 수 있습니다. 또한, <code>_embedded</code> 필드를 사용하여 리소스의 중첩된 표현을 제공할 수도 있습니다.</p>
<p>Spring Data REST는 HAL 형식을 사용하여 데이터를 JSON으로 표현하는데, 이는 클라이언트가 서버에서 받은 응답을 해석하고 리소스 간의 관계를 이해하기 쉽도록 도와줍니다. HAL은 RESTful API에서 링크를 표현하는 유연하고 편리한 방법을 제공하여 API의 디자인과 상호작용을 단순화하는 데 기여합니다.</p>
<hr>
<p>다음 예에서는 사람 기록(현재는 없음)을 보는 방법을 보여줍니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/c6f0d2b6-f7f9-4f40-a97f-7fd882151140/image.png"></p>
<pre><code class="language-null">$ curl http://localhost:8080/people

{
  "_embedded" : {
    "people" : []
  },
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/people{?page,size,sort}",
      "templated" : true
    },
    "search" : {
      "href" : "http://localhost:8080/people/search"
    }
  },
  "page" : {
    "size" : 20,
    "totalElements" : 0,
    "totalPages" : 0,
    "number" : 0
  }
}</code></pre>
<p>현재 요소가 없으므로 페이지도 없습니다. 새로운 <code>Person</code>을 생성할 시간입니다! 다음 목록에서는 이를 수행하는 방법을 보여줍니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/d799d855-f02c-4f51-9110-8533ae54f517/image.png"></p>
<p>(오타 때문에 두번 입력함)</p>
<pre><code class="language-null">$ curl -i -H "Content-Type:application/json" -d '{"firstName": "Frodo", "lastName": "Baggins"}' http://localhost:8080/people

HTTP/1.1 201 Created
Server: Apache-Coyote/1.1
Location: http://localhost:8080/people/1
Content-Length: 0
Date: Wed, 26 Feb 2014 20:26:55 GMT</code></pre>
<ul>
<li>
<p><code>-i</code>: 헤더를 포함한 응답 메시지를 볼 수 있는지 확인합니다. 새로 생성된 <code>Person</code>의 URI가 표시됩니다.</p>
</li>
<li>
<p><code>-H "Content-Type:application/json"</code>: 애플리케이션이 페이로드에 JSON 개체가 포함되어 있음을 알 수 있도록 콘텐츠 유형을 설정합니다.</p>
</li>
<li>
<p><code>-d '{"firstName": "Frodo", "lastName": "Baggins"}'</code>: 는 전송된 데이터입니다.</p>
</li>
<li>
<p>Windows를 사용하는 경우 위 명령은 WSL에서 작동합니다. WSL을 설치할 수 없는 경우 작은따옴표를 큰따옴표로 바꾸고 기존 큰따옴표를 이스케이프해야 할 수도 있습니다(예: <code>-d "{\"firstName\": \"Frodo\", \"lastName\": \). "배긴스\"}"</code>.</p>
</li>
</ul>
<blockquote>
<p><code>POST</code> 작업에 대한 응답에 <code>Location</code> 헤더가 어떻게 포함되어 있는지 확인하세요. 여기에는 새로 생성된 리소스의 URI가 포함됩니다. Spring Data REST에는 방금 생성된 리소스의 표현을 즉시 반환하도록 프레임워크를 구성하는 데 사용할 수 있는 두 가지 메서드(<code>RepositoryRestConfiguration.setReturnBodyOnCreate(…)</code> 및 <code>setReturnBodyOnUpdate(…)</code>)도 있습니다. <code>RepositoryRestConfiguration.setReturnBodyForPutAndPost(…)</code>는 생성 및 업데이트 작업에 대한 표현 응답을 활성화하는 바로가기 메서드입니다.</p>
</blockquote>
<p>다음 예제와 같이 모든 사람에 대해 쿼리할 수 있습니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/57390ea8-667c-4c19-94f4-fd5da1a42a70/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/d88ff835-2ae6-462c-abf8-01895def9c66/image.png"></p>
<p><code>people</code> 개체에는 <code>Frodo</code>가 포함된 목록이 포함되어 있습니다. <code>self</code> 링크가 어떻게 포함되어 있는지 확인하세요. Spring Data REST는 또한 Evo Inflector를 사용하여 그룹화를 위한 엔터티 이름을 복수형으로 지정합니다.</p>
<p>다음과 같이 개별 레코드에 대해 직접 쿼리할 수 있습니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/0563bd01-0c77-4fe8-afc4-3a0e8a76f6ad/image.png"></p>
<pre><code class="language-null">$ curl http://localhost:8080/people/1
{
  "firstName" : "Frodo",
  "lastName" : "Baggins",
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/people/1"
    }
  }
}</code></pre>
<blockquote>
<p>이는 순전히 웹 기반인 것처럼 보일 수 있습니다. 그러나 그 이면에는 H2 관계형 데이터베이스가 있습니다. 프로덕션 환경에서는 아마도 PostgreSQL과 같은 실제 제품을 사용할 것입니다.</p>
</blockquote>
<blockquote>
<p>이 가이드에는 도메인 개체가 하나만 있습니다. 도메인 객체가 서로 관련되어 있는 보다 복잡한 시스템에서 Spring Data REST는 연결된 레코드를 탐색하는 데 도움이 되는 추가 링크를 렌더링합니다.</p>
</blockquote>
<p>다음 예와 같이 모든 사용자 정의 쿼리를 찾을 수 있습니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/e098e12c-51cd-411d-bb64-e4aa073eacb9/image.png"></p>
<pre><code class="language-null">$ curl http://localhost:8080/people/search
{
  "_links" : {
    "findByLastName" : {
      "href" : "http://localhost:8080/people/search/findByLastName{?name}",
      "templated" : true
    }
  }
}</code></pre>
<p>HTTP 쿼리 매개변수인 <code>name</code>을 포함하여 쿼리의 URL을 볼 수 있습니다. 이는 인터페이스에 포함된 <code>@Param("name")</code> 주석과 일치합니다.</p>
<p>다음 예에서는 <code>findByLastName</code> 쿼리를 사용하는 방법을 보여줍니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/60031c8a-d6c9-4648-9c15-86dfe2d0cefd/image.png"></p>
<pre><code class="language-null">$ curl http://localhost:8080/people/search/findByLastName?name=Baggins
{
  "_embedded" : {
    "persons" : [ {
      "firstName" : "Frodo",
      "lastName" : "Baggins",
      "_links" : {
        "self" : {
          "href" : "http://localhost:8080/people/1"
        }
      }
    } ]
  }
}</code></pre>
<p>코드에서 <code>List&lt;Person&gt;</code>을 반환하도록 정의했기 때문에 모든 결과를 반환합니다. <code>Person</code>만 반환하도록 정의한 경우 반환할 <code>Person</code> 객체 중 하나를 선택합니다. 이는 예측할 수 없으므로 여러 항목을 반환할 수 있는 쿼리에 대해서는 이를 수행하고 싶지 않을 것입니다.</p>
<p>또한 <code>PUT</code>, <code>PATCH</code> 및 <code>DELETE</code> REST 호출을 실행하여 기존 레코드를 각각(respectively) 교체, 업데이트 또는 삭제할 수도 있습니다. 다음 예에서는 <code>PUT</code> 호출을 사용합니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/97781011-4d0f-4e89-9b49-7e44133c7d3d/image.png"></p>
<pre><code class="language-null">$ curl -X PUT -H "Content-Type:application/json" -d '{"firstName": "Bilbo", "lastName": "Baggins"}' http://localhost:8080/people/1
$ curl http://localhost:8080/people/1
{
  "firstName" : "Bilbo",
  "lastName" : "Baggins",
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/people/1"
    }
  }
}</code></pre>
<p>The following example uses a <code>PATCH</code> call:</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/b89b269c-f7d0-459c-a8ec-8ebbf2239654/image.png"></p>
<pre><code class="language-null">$ curl -X PATCH -H "Content-Type:application/json" -d '{"firstName": "Bilbo Jr."}' http://localhost:8080/people/1
$ curl http://localhost:8080/people/1
{
  "firstName" : "Bilbo Jr.",
  "lastName" : "Baggins",
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/people/1"
    }
  }
}</code></pre>
<blockquote>
<p><code>PUT</code>은 전체 레코드를 대체합니다. 제공되지 않은 필드는 <code>null</code>로 대체됩니다. <code>PATCH</code>를 사용하여 항목의 하위 집합을 업데이트할 수 있습니다.</p>
</blockquote>
<p>다음 예시와 같이 레코드를 삭제할 수도 있습니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/9a5e0eed-7cdf-4516-bbe2-8253735adf5d/image.png"></p>
<pre><code class="language-null">$ curl -X DELETE http://localhost:8080/people/1
$ curl http://localhost:8080/people
{
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/people{?page,size,sort}",
      "templated" : true
    },
    "search" : {
      "href" : "http://localhost:8080/people/search"
    }
  },
  "page" : {
    "size" : 20,
    "totalElements" : 0,
    "totalPages" : 0,
    "number" : 0
  }
}</code></pre>
<p>이 하이퍼미디어 기반 인터페이스의 편리한 측면은 <code>curl</code>(또는 원하는 REST 클라이언트)을 사용하여 모든 RESTful 엔드포인트를 검색할 수 있다는 것입니다. 고객과 공식적인 계약서나 인터페이스 문서를 교환할 필요가 없습니다.</p>