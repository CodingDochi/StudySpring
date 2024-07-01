<p>이 가이드는 하이퍼미디어 기반 RESTful 프런트 엔드를 통해 문서 기반 데이터에 액세스하는 애플리케이션을 만드는 과정을 안내합니다.</p>
<h2 id="무엇을-구축할-것인가">무엇을 구축할 것인가</h2>
<p>Spring Data REST를 사용하여 MongoDB NoSQL 데이터베이스에 저장된 Person 객체를 생성하고 검색할 수 있는 Spring 애플리케이션을 구축합니다. Spring Data REST는 Spring HATEOAS와 Spring Data MongoDB의 기능을 가져와 자동으로 결합합니다.</p>
<blockquote>
<p>Spring Data REST는 Spring Data JPA, Spring Data Gemfire 및 Spring Data Neo4j도 백엔드 데이터 저장소로 지원하지만 이는 이 가이드의 일부가 아닙니다.</p>
</blockquote>
<h2 id="starting-with-spring-initializr">Starting with Spring Initializr</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/1ef2fb2e-a6ff-4c33-9f65-d7374f67ef9d/image.png"></p>
<h2 id="터미널에서-로컬에-설치된-몽고db-실행하기">터미널에서 로컬에 설치된 몽고DB 실행하기</h2>
<h3 id="몽고db-ubuntu-2204에-설치">몽고DB Ubuntu 22.04에 설치</h3>
<p><a href="https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-ubuntu/#std-label-install-mdb-community-ubuntu">https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-ubuntu/#std-label-install-mdb-community-ubuntu</a></p>
<h3 id="몽고db-실행">몽고DB 실행</h3>
<pre><code class="language-shell">// 몽고 데이터베이스 서버 시작
<span class="token function">sudo</span> systemctl start mongod

// 상태 확인
<span class="token function">sudo</span> systemctl status mongod</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/8167018c-a1b0-47de-827c-02f3d95c79d9/image.png"></p>
<h2 id="도메인-객체-생성">도메인 객체 생성</h2>
<p>다음 예제(src/main/java/guides/accessingmongodbdatarest/Person.java)에 표시된 대로 사람을 표시하는 새 도메인 개체를 만듭니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>accessingmongodbdatarest</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Id</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Person</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Id</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> id<span class="token punctuation">;</span>

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
<p><code>Person</code> 객체에는 이름과 성이 있습니다. (자동으로 생성되도록 설정한 ID 객체도 있으므로 다룰 필요가 없습니다.)</p>
<h2 id="개인-저장소-생성">개인 저장소 생성</h2>
<p>다음으로, 다음 목록(src/main/java/guides/accessingmongodbdatarest/PersonRepository.java)에 표시된 대로 간단한 저장소를 생성해야 합니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>accessingmongodbdatarest</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>mongodb<span class="token punctuation">.</span>repository</span><span class="token punctuation">.</span><span class="token class-name">MongoRepository</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>repository<span class="token punctuation">.</span>query</span><span class="token punctuation">.</span><span class="token class-name">Param</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>rest<span class="token punctuation">.</span>core<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RepositoryRestResource</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util</span><span class="token punctuation">.</span><span class="token class-name">List</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@RepositoryRestResource</span><span class="token punctuation">(</span>collectionResourceRel <span class="token operator">=</span> <span class="token string">"people"</span><span class="token punctuation">,</span> path<span class="token operator">=</span><span class="token string">"people"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">PersonRepository</span> <span class="token keyword">extends</span> <span class="token class-name">MongoRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">,</span> <span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

    <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastName</span><span class="token punctuation">(</span><span class="token annotation punctuation">@Param</span><span class="token punctuation">(</span><span class="token string">"name"</span><span class="token punctuation">)</span> <span class="token class-name">String</span> name<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token punctuation">}</span></code></pre>
<p>이 저장소는 인터페이스이며 <code>Person</code> 객체와 관련된 다양한 작업을 수행할 수 있게 해줍니다. <code>MongoRepository</code>를 확장하여 이러한 작업을 수행하고, 이는 Spring Data Commons에 정의된 <code>PagingAndSortingRepository</code> 인터페이스를 확장합니다.</p>
<hr>
<p><code>MongoRepository</code>는 <code>PagingAndSortingRepository</code>와 <code>CrudRepository</code> 인터페이스를 상속하고 있습니다.</p>
<hr>
<p>런타임 시 Spring Data REST는 자동으로 이 인터페이스의 구현을 생성합니다. 그런 다음 <code>@RepositoryRestResource</code> 주석을 사용하여 Spring MVC가 <code>/people</code>에 RESTful 엔드포인트를 생성하도록 지시합니다.</p>
<blockquote>
<p>저장소를 내보내는 데 <code>@RepositoryRestResource</code>가 필요하지 않습니다. <code>/persons</code>의 기본값 대신 <code>/people</code>을 사용하는 등 내보내기 세부 사항을 변경하는 데에만 사용됩니다.</p>
</blockquote>
<p>여기서는 <code>lastName</code> 값을 기반으로 <code>Person</code> 개체 목록을 검색하는 사용자 지정 쿼리도 정의했습니다. 이 가이드에서 이를 호출하는 방법을 자세히 알아볼 수 있습니다.</p>
<blockquote>
<p>기본적으로 Spring Boot는 로컬로 호스팅되는 MongoDB 인스턴스에 연결을 시도합니다. 애플리케이션이 다른 곳에서 호스팅되는 MongoDB 인스턴스를 가리키도록 하는 방법에 대한 참조 문서를 읽어보세요.</p>
</blockquote>
<h2 id="애플리케이션-테스트">애플리케이션 테스트</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/5763d38f-6e6c-4111-ae86-49a51f0b3f94/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/95275e9f-2d91-47e5-b6d8-86c13ce0bfd0/image.png"></p>
<p>이제 애플리케이션이 실행되었으므로 테스트할 수 있습니다. 원하는 REST 클라이언트를 사용할 수 있습니다. 다음 예에서는 *nix 도구 <code>curl</code>을 사용합니다.</p>
<p>먼저 다음 예제와 같이 최상위 서비스를 확인하려고 합니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/fc392469-c9af-426a-968b-1e5e3a51b15a/image.png"></p>
<pre><code class="language-null">$ curl http://localhost:8080
{
  "_links" : {
    "people" : {
      "href" : "http://localhost:8080/people{?page,size,sort}",
      "templated" : true
    }
  }
}</code></pre>
<p>앞의 예에서는 이 서버가 제공하는 기능을 간략하게 보여줍니다. <a href="http://localhost:8080/people%EC%97%90">http://localhost:8080/people에</a> <code>people</code> 링크가 있습니다. <code>?page</code>, <code>?size</code> 및 <code>?sort</code>와 같은 몇 가지 옵션이 있습니다.</p>
<blockquote>
<p>Spring Data REST는 JSON 출력에 HAL 형식을 사용합니다. 이는 유연하며 제공되는 데이터에 인접한 링크를 제공하는 편리한 방법을 제공합니다.</p>
</blockquote>
<p>people 링크를 사용하면 데이터베이스에 <code>Person</code> 레코드가 표시됩니다(현재 없음).</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/96e8cd47-0f5b-42af-9f30-a3a45a7f9ca0/image.png"></p>
<pre><code class="language-null">$ curl http://localhost:8080/people
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
<p>현재 요소가 없으므로 페이지도 없습니다. 이제 새로운 <code>Person</code>을 생성할 시간입니다!</p>
<blockquote>
<p>이 가이드를 여러 번 실행하면 남은 데이터가 있을 수 있습니다. 새로 시작해야 하는 경우 데이터베이스를 찾고 삭제하는 명령은 MongoDB 셸 빠른 참조를 참조하세요.</p>
</blockquote>
<p>다음 명령은 "Frodo Baggins"라는 사람을 만듭니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/936d793b-9688-431a-b137-99b1a4858913/image.png"></p>
<pre><code class="language-null">$ curl -i -X POST -H "Content-Type:application/json" -d "{  \"firstName\" : \"Frodo\",  \"lastName\" : \"Baggins\" }" http://localhost:8080/people
HTTP/1.1 201 Created
Server: Apache-Coyote/1.1
Location: http://localhost:8080/people/53149b8e3004990b1af9f229
Content-Length: 0
Date: Mon, 03 Mar 2014 15:08:46 GMT</code></pre>
<ul>
<li>
<p><code>-i</code>: 헤더를 포함한 응답 메시지를 볼 수 있는지 확인합니다. 새로 생성된 <code>Person</code>의 URI가 표시됩니다.</p>
</li>
<li>
<p><code>-X POST</code>: 새 항목을 만드는 데 사용되는 <code>POST</code> 신호입니다.</p>
</li>
<li>
<p><code>-H "Content-Type:application/json"</code>: 애플리케이션이 페이로드에 JSON 개체가 포함되어 있음을 알 수 있도록 콘텐츠 유형을 설정합니다.</p>
</li>
<li>
<p><code>-d "{ \"firstName\" : \"Frodo\", \"lastName\" : \"Baggins\" }"</code>: 전송되는 데이터입니다.</p>
</li>
</ul>
<hr>
<p>몽고DB와 네오4제이(또는 일반적으로 Spring Data REST)는 각각 JSON 데이터를 curl 명령어의 <code>-d</code> 옵션을 통해 서버에 전달하는 방식에서 조금 다른 표기법을 사용합니다.</p>
<h3 id="몽고db에서의-이스케이핑">몽고DB에서의 이스케이핑</h3>
<ul>
<li>몽고DB의 REST API는 데이터를 이스케이핑하여 문자열로 전달하는 방식을 사용합니다. </li>
<li>이 경우에는 이중 따옴표 안에 백슬래시(<code>\</code>)를 사용하여 JSON 형태의 데이터를 문자열로 감싸 전달합니다.</li>
</ul>
<h3 id="네오4제이sdr에서의-표기법">네오4제이(SDR)에서의 표기법</h3>
<ul>
<li>네오4제이나 Spring Data REST는 일반적으로 단일 따옴표를 사용하여 JSON 데이터를 curl 명령어에 전달합니다.</li>
<li>이러한 경우에는 JSON 형태의 데이터를 단일 따옴표 안에 넣어 전달합니다.</li>
</ul>
<hr>
<p>네오4제이와 몽고DB의 REST API가 생성한 Location URL의 형식이 다른 것은 각 데이터베이스가 생성한 리소스 식별자(Identifier)의 표현 방식에 따른 것입니다.</p>
<ul>
<li>
<p>네오4제이: 일반적으로 ID 기반의 숫자로 된 URL을 생성합니다. 예를 들어, <code>http://localhost:8080/people/1</code>, <code>http://localhost:8080/people/2</code>와 같이 숫자 식별자를 사용합니다. 이것은 각 데이터 엔터티의 ID를 리소스 URL에 반영하여 나타냅니다.</p>
</li>
<li>
<p>몽고DB: 몽고DB의 리소스 식별자는 일반적으로 ObjectId 형식으로 생성됩니다. ObjectId는 MongoDB에서 사용하는 고유 식별자로, 24개의 16진수 문자로 구성된 문자열입니다. 이 ObjectId가 리소스의 ID로 사용되며, REST API에서는 이를 URL에 반영하여 리소스를 식별합니다.</p>
</li>
</ul>
<hr>
<blockquote>
<p>이전 <code>POST</code> 작업에 <code>Location</code> 헤더가 어떻게 포함되어 있는지 확인하세요. 여기에는 새로 생성된 리소스의 URI가 포함됩니다. Spring Data REST에는 또한 방금 생성/업데이트된 리소스의 표현을 즉시 반환하도록 프레임워크를 구성하는 데 사용할 수 있는 두 가지 메서드(<code>RepositoryRestConfiguration.setReturnBodyOnCreate(…)</code> 및 <code>setReturnBodyOnUpdate(…)</code>)가 있습니다.</p>
</blockquote>
<p>다음 예와 같이 여기에서 모든 사람에 대해 쿼리할 수 있습니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/da0ad51f-80f6-4579-a2eb-87ba2333c5c4/image.png"></p>
<pre><code class="language-null">$ curl http://localhost:8080/people
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
  "_embedded" : {
    "persons" : [ {
      "firstName" : "Frodo",
      "lastName" : "Baggins",
      "_links" : {
        "self" : {
          "href" : "http://localhost:8080/people/53149b8e3004990b1af9f229"
        }
      }
    } ]
  },
  "page" : {
    "size" : 20,
    "totalElements" : 1,
    "totalPages" : 1,
    "number" : 0
  }
}</code></pre>
<p><code>person</code> 객체에는 <code>Frodo</code>가 포함된 목록이 포함되어 있습니다. <code>self</code> 링크가 어떻게 포함되어 있는지 확인하세요. Spring Data REST는 또한 Evo Inflector를 사용하여 그룹화를 위한 엔터티 이름을 복수화합니다.</p>
<p>다음 예제와 같이 개별 레코드를 직접 쿼리할 수 있습니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/b11a345f-3aab-4804-966d-58a6412ad8c1/image.png"></p>
<pre><code class="language-null">$ curl http://localhost:8080/people/53149b8e3004990b1af9f229
{
  "firstName" : "Frodo",
  "lastName" : "Baggins",
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/people/53149b8e3004990b1af9f229"
    }
  }
}</code></pre>
<blockquote>
<p>이는 순전히 웹 기반인 것처럼 보일 수 있지만 뒤에서는 시작한 MongoDB 데이터베이스와 통신하고 있습니다.</p>
</blockquote>
<p>이 가이드에는 도메인 개체가 하나만 있습니다. 도메인 객체가 서로 관련되어 있는 보다 복잡한 시스템에서 Spring Data REST는 연결된 레코드를 탐색하는 데 도움이 되는 추가 링크를 렌더링합니다.</p>
<p>다음 예와 같이 모든 사용자 정의 쿼리를 찾습니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/6c4b8cf8-38c3-4651-8741-7fb5b1318bda/image.png"></p>
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
<p><code>findByLastName</code> 쿼리를 사용하려면 다음 명령을 실행합니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/6debff4b-eece-467e-a025-3f9284ad26a7/image.png"></p>
<pre><code class="language-null">$ curl http://localhost:8080/people/search/findByLastName?name=Baggins
{
  "_embedded" : {
    "persons" : [ {
      "firstName" : "Frodo",
      "lastName" : "Baggins",
      "_links" : {
        "self" : {
          "href" : "http://localhost:8080/people/53149b8e3004990b1af9f229"
        }
      }
    } ]
  }
}</code></pre>
<p>코드에서 <code>List&lt;Person&gt;</code>을 반환하도록 정의했기 때문에 모든 결과를 반환합니다. <code>Person</code>만 반환하도록 정의한 경우 반환할 <code>Person</code> 객체 중 하나를 선택합니다. 이는 예측할 수 없으므로 여러 항목을 반환할 수 있는 쿼리에 대해서는 이를 수행하고 싶지 않을 것입니다.</p>
<p><code>PUT</code>, <code>PATCH</code> 및 <code>DELETE</code> REST 호출을 실행하여 각각 기존 레코드를 교체, 업데이트 또는 삭제할 수도 있습니다. 다음 예에서는 <code>PUT</code> 호출을 사용합니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/9e84d3ed-865e-44d0-9797-1672fc99a3c7/image.png"></p>
<pre><code class="language-null">$ curl -X PUT -H "Content-Type:application/json" -d "{ \"firstName\": \"Bilbo\", \"lastName\": \"Baggins\" }" http://localhost:8080/people/53149b8e3004990b1af9f229
$ curl http://localhost:8080/people/53149b8e3004990b1af9f229
{
  "firstName" : "Bilbo",
  "lastName" : "Baggins",
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/people/53149b8e3004990b1af9f229"
    }
  }
}</code></pre>
<p>다음 예에서는 <code>PATCH</code> 호출을 사용합니다.<br>
<img src="https://velog.velcdn.com/images/dev_hammy/post/2f5c4e5e-812e-4502-ae61-23e7d3af2b25/image.png"></p>
<pre><code class="language-null">$ curl -X PATCH -H "Content-Type:application/json" -d "{ \"firstName\": \"Bilbo Jr.\" }" http://localhost:8080/people/53149b8e3004990b1af9f229
$ curl http://localhost:8080/people/53149b8e3004990b1af9f229
{
  "firstName" : "Bilbo Jr.",
  "lastName" : "Baggins",
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/people/53149b8e3004990b1af9f229"
    }
  }
}</code></pre>
<blockquote>
<p><code>PUT</code>은 전체 레코드를 대체합니다. 제공되지 않은 필드는 <code>null</code>로 대체됩니다. <code>PATCH</code>를 사용하여 항목의 하위 집합을 업데이트할 수 있습니다.</p>
</blockquote>
<p>다음 예시와 같이 레코드를 삭제할 수도 있습니다.</p>
<pre><code class="language-null">$ curl -X DELETE http://localhost:8080/people/53149b8e3004990b1af9f229
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
<p>이 하이퍼미디어 기반 인터페이스의 편리한 측면은 curl(또는 원하는 REST 클라이언트)을 사용하여 모든 RESTful 엔드포인트를 검색할 수 있다는 것입니다. 고객과 공식적인 계약서나 인터페이스 문서를 교환할 필요가 없습니다.</p>