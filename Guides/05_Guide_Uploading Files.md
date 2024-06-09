<p>HTTP multi-part 파일 업로드를 받을 수 있는 서버 애플리케이션을 생성해보겠습니다.</p>
<h2 id="what-you-will-build">What You Will Build</h2>
<p>파일 업로드 요청을 받아들일 수 있는 스프링 부트 웹 애플리케이션을 생성합니다. 테스트 파일을 올릴 수 있는 간단한 HTML 인터페이스도 빌드할 것입니다. </p>
<h2 id="starting-with-spring-initializr">Starting with Spring Initializr</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/dd62fc4a-f40d-433d-b54e-47f9dcf69eca/image.png"></p>
<h3 id="buildgradle">build.gradle</h3>
<pre><code class="language-groovy">plugins {
    id 'java'
    id 'org.springframework.boot' version '3.2.0'
    id 'io.spring.dependency-management' version '1.1.4'
}

group = 'guides'
version = '0.0.1-SNAPSHOT'

java {
    sourceCompatibility = '17'
}

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

tasks.named('test') {
    useJUnitPlatform()
}</code></pre>
<h2 id="애플리케이션-개발-배경-지식">애플리케이션 개발 배경 지식</h2>
<h3 id="spring-에서-제공하는-http-처리용-어노테이션">Spring 에서 제공하는 HTTP 처리용 어노테이션</h3>
<p><strong>클래스 레벨 어노테이션</strong>:</p>
<ol>
<li><code>@Controller</code>, <code>@RestController</code>: 클래스가 컨트롤러임을 지정합니다. <code>@RestController</code>는 <code>@Controller</code>와 <code>@ResponseBody</code>를 합친 것으로 RESTful 서비스에서 사용됩니다.</li>
<li><code>@Service</code>: 비즈니스 로직을 담당하는 서비스 클래스임을 지정합니다.</li>
<li><code>@Repository</code>: 데이터베이스와의 상호 작용을 담당하는 DAO(Data Access Object) 클래스임을 지정합니다.</li>
<li><code>@Configuration</code>: Spring Bean 구성을 담당하는 클래스임을 지정합니다.</li>
</ol>
<p><strong>메서드 레벨 어노테이션</strong>:</p>
<ol>
<li><code>@ResponseBody</code>: 메서드가 반환하는 값을 HTTP 응답 본문으로 사용하도록 지정합니다.</li>
<li><code>@GetMapping</code>, <code>@PostMapping</code>, <code>@PutMapping</code>, <code>@DeleteMapping</code>: 각각 GET, POST, PUT, DELETE 요청을 처리하는 메서드로 지정합니다.</li>
<li><code>@ExceptionHandler</code>: 특정 예외를 처리하는 메서드로 사용됩니다.</li>
</ol>
<p><strong>메서드 및 클래스 레벨 어노테이션</strong><br>
이러한 애너테이션들은 메서드 레벨에서는 해당 메서드에 적용되며, 클래스 레벨에서는 클래스 내의 모든 메서드에 적용됩니다. 이를 통해 특정한 기능이나 설정을 일괄적으로 적용할 수 있습니다.</p>
<ol>
<li><code>@RequestMapping</code>: 이미 언급한 것처럼 URL 경로를 매핑하며, 메서드 레벨과 클래스 레벨에서 모두 사용될 수 있습니다.</li>
<li><code>@Transactional</code>: 데이터베이스 트랜잭션 처리를 위한 애너테이션으로, 메서드 또는 클래스에 적용할 수 있습니다.</li>
<li><code>@Secured</code>, <code>@PreAuthorize</code>, <code>@PostAuthorize</code>: 스프링 시큐리티를 이용하여 메서드 또는 클래스의 접근 권한을 제어하는 애너테이션입니다.</li>
<li><code>@CrossOrigin</code>: CORS(Cross-Origin Resource Sharing)을 허용하기 위해 사용되며, 메서드 또는 클래스에 적용할 수 있습니다.</li>
</ol>
<p><strong>메서드 매개변수 레벨 어노테이션</strong></p>
<ol>
<li>
<p><code>@RequestParam</code>: HTTP 요청에서 쿼리 매개변수나 폼 매개변수를 추출합니다. 이는 메서드의 매개변수로 전달된다. 예를 들어, <code>?id=1</code>과 같은 쿼리 매개변수를 처리할 때 사용됩니다.</p>
</li>
<li>
<p><code>@PathVariable</code>: URI 경로의 일부인 경로 변수를 추출합니다. <code>/users/{userId}</code>와 같은 경로에서 <code>userId</code>를 추출할 때 사용됩니다.</p>
</li>
<li>
<p><code>@RequestBody</code>: HTTP 요청의 본문을 자바 객체로 변환합니다. 주로 JSON 또는 XML과 같은 형태의 요청 본문을 자바 객체로 변환할 때 사용됩니다.</p>
</li>
<li>
<p><code>@RequestHeader</code>: HTTP 요청의 헤더 값을 추출합니다. 특정 헤더 값을 추출하거나 헤더 값을 메서드의 매개변수로 전달할 때 사용됩니다.</p>
</li>
<li>
<p><code>@RequestAttribute</code>: HTTP 요청의 어트리뷰트 값을 추출합니다. 주로 프레임워크 수준의 요청 어트리뷰트 값을 가져올 때 사용됩니다.</p>
</li>
<li>
<p><code>@RequestParamMap</code>: HTTP 요청의 모든 쿼리 매개변수를 Map으로 받습니다.</p>
</li>
</ol>
<h3 id="데이터-구조-및-데이터-전송-관련-객체">데이터 구조 및 데이터 전송 관련 객체</h3>
<ul>
<li>
<p>Buffer: 임시 데이터 저장 공간으로 입출력 속도 조절이나 데이터 버퍼링에 사용됩니다.</p>
</li>
<li>
<p>List: 데이터를 순차적으로 저장하고, 삽입, 삭제 등의 연산에 유용한 데이터 구조입니다.</p>
</li>
<li>
<p>Channel: 입출력을 위한 연결 통로로, 파일이나 네트워크 등 데이터를 읽고 쓰는데 사용됩니다.</p>
</li>
<li>
<p>Queue: 선입선출(FIFO) 구조로 데이터를 저장하며, 대기열을 다루거나 작업을 조율할 때 유용합니다.</p>
</li>
<li>
<p>Stream: 연속적인 데이터의 흐름을 다루며, 데이터의 처리 및 전송에 사용됩니다.</p>
</li>
</ul>
<table><thead><tr><th>구분</th><th>특징</th><th>사용 목적</th><th>예시</th></tr></thead><tbody><tr><td>Buffer</td><td>데이터를 일시적으로 저장하는 영역</td><td>데이터를 임시로 버퍼링하거나 입출력 속도를 조절하기 위해 사용</td><td>ByteBuffer, CharBuffer 등</td></tr><tr><td>List</td><td>순차적인 데이터 구조</td><td>데이터를 순차적으로 저장하고 관리하는 목적에 사용</td><td>ArrayList, LinkedList 등</td></tr><tr><td>Channel</td><td>입출력을 위한 연결 통로</td><td>데이터를 읽고 쓰는데 사용되는 입출력 채널</td><td>FileChannel, SocketChannel 등</td></tr><tr><td>Queue</td><td>FIFO(First-In-First-Out) 구조</td><td>데이터를 먼저 넣은 순서대로 꺼내는 구조</td><td>LinkedList, ArrayDeque 등</td></tr><tr><td>Stream</td><td>데이터의 연속적인 흐름을 다룸</td><td>데이터를 연속적으로 처리하거나 전송하는 용도에 사용</td><td>InputStream, OutputStream, FileInputStream 등</td></tr></tbody></table>
<h3 id="javanio-패키지와-javaio-패키지">java.nio 패키지와 java.io 패키지</h3>
<p><code>java.nio</code>와 <code>java.io</code> 패키지는 Java에서 파일과 입출력을 다루는 데 사용되는 패키지들입니다. 각각의 패키지는 다음과 같은 차이점이 있습니다:</p>
<ol>
<li>
<p><strong>버퍼 지향(I/O) vs 스트림 지향(I/O):</strong></p>
<ul>
<li><code>java.io</code> 패키지는 스트림 기반의 입출력을 제공합니다. <code>InputStream</code>과 <code>OutputStream</code>을 사용하여 데이터를 읽고 쓸 수 있습니다. 이는 데이터가 연속적으로 흐르는 스트림을 통해 이루어집니다.</li>
<li><code>java.nio</code> 패키지는 버퍼 지향의 입출력을 제공합니다. <code>Buffer</code> 클래스와 <code>Channel</code> 인터페이스를 사용하여 데이터를 버퍼에 읽고 쓰며, 데이터를 읽고 쓸 때 더욱 유연한 작업이 가능합니다.</li>
<li>버퍼 지향 접근 방식은 데이터를 스트림으로부터 읽어와 버퍼에 쓰고, 버퍼에서 데이터를 읽어오는 방식으로 데이터를 처리합니다.</li>
</ul>
</li>
<li>
<p><strong>블로킹 vs 논블로킹 I/O:</strong></p>
<ul>
<li><code>java.io</code>는 주로 블로킹 I/O를 사용합니다. I/O 작업이 완료될 때까지 프로그램이 대기하며, 데이터가 도착하기 전까지 다른 작업을 수행할 수 없습니다.</li>
<li><code>java.nio</code>는 논블로킹 I/O를 지원합니다. 비동기적인 입출력 작업을 통해 다중 채널을 단일 스레드로 관리할 수 있으며, 채널이 데이터를 기다리는 동안에도 다른 작업을 수행할 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>스케일러블한 I/O:</strong></p>
<ul>
<li><code>java.nio</code>는 다중 스레드로 작업을 처리하는 경우에 더 효율적입니다. 대용량 데이터 처리나 다중 채널 관리 시에 <code>java.nio</code>는 더 나은 성능을 보일 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>새로운 기능들:</strong></p>
<ul>
<li><code>java.nio</code>는 비동기 파일 채널, 멀티플렉서, 셀렉터 등의 새로운 기능을 제공합니다. 이러한 기능들은 네트워킹과 관련된 작업이나 파일 I/O 작업을 더욱 효과적으로 다룰 수 있게 도와줍니다.</li>
</ul>
</li>
</ol>
<p>일반적으로, <code>java.nio</code>는 더욱 많은 기능과 유연성을 제공하지만, 사용하기에는 더 복잡할 수 있습니다. 작은 파일이나 간단한 입출력 작업을 처리할 때는 <code>java.io</code>가 간단하고 편리할 수 있습니다.</p>
<h3 id="코드에-쓰일-주요-요소">코드에 쓰일 주요 요소</h3>
<ol>
<li>
<p><strong>Files</strong>: 자바 NIO(Non-blocking I/O) 파일 관련 유틸리티를 제공하는 클래스로, 파일의 복사, 이동, 삭제 등 다양한 파일 작업을 지원합니다.</p>
</li>
<li>
<p><strong>MultipartFile</strong>: HTTP 멀티파트 요청에서 업로드된 파일을 나타내는 인터페이스로, 스프링에서 파일 업로드 처리를 위해 사용됩니다. <code>InputStreamSource</code>를 통해 데이터를 읽을 수 있습니다.  </p>
</li>
</ol>
<ul>
<li>멀티파트는 HTTP 요청에서 여러 종류의 데이터를 한꺼번에 전송하는 방식을 의미합니다. 각 파일은 멀티파트 형식으로 요청에 포함되며, 이러한 요청은 파일의 확장자에 관계없이 동일한 HTTP 요청 내에서 처리됩니다.</li>
<li>멀티파트를 사용하여 파일을 전송한다고 해서 그 자체가 멀티스레딩을 의미하지는 않습니다. 멀티스레딩은 요청을 처리하는 방식이며, 이는 요청을 동기적으로 처리할지 아니면 비동기적으로 처리할지에 따라 결정됩니다. 파일 업로드는 멀티파트를 통해 여러 파일을 동시에 전송할 수 있게 해주지만, 요청의 처리 방식은 서버 측의 구현 및 설정에 따라 달라집니다.</li>
</ul>
<ol start="3">
<li><strong>Resource</strong>: 파일이나 리소스를 추상화하는 인터페이스로, 스프링 프레임워크에서 리소스를 로드하고 읽는 기능을 제공합니다. <code>InputStreamSource</code>를 확장하여 스트림으로부터 데이터를 읽을 수 있도록 합니다.</li>
</ol>
<ul>
<li>Resource의 추상화는 절대경로 또는 상대경로 같은 파일 경로나, 하드링크 도는 심벌릭 링크 같은 파일시스템과 관련 된 것이 아니라, 개발자가 현재 개발중인 애플리케이션 내부의 파일, URL, ServeletContext 상의 리소스에 접근하여 읽기, 쓰기 를 할 수 있는 표준화된 방법을 제공한다</li>
<li>Resource를 사용하면 애플리케이션 코드에서 이러한 리소스에 정적이고 표준화된 방식으로 접근할 수 있지만, 런타임에 바이트코드를 수정하거나 프록시를 생성하는 등의 동적인 작업을 하는 것은 아닙니다. </li>
</ul>
<ol start="4">
<li>
<p><strong>Model</strong>: 스프링에서 데이터를 뷰로 전달하기 위한 인터페이스로, 뷰에 전달할 데이터를 담는 역할을 합니다.</p>
</li>
<li>
<p><strong>MvcUriComponentsBuilder</strong>: 스프링 MVC에서 URI(Uniform Resource Identifier)를 생성하는 빌더 클래스로, 컨트롤러 및 메서드에 대한 URI를 동적으로 생성하는데 사용됩니다.</p>
</li>
</ol>
<ul>
<li>URI는 Uniform Resource Identifier의 약자로, 인터넷에서 리소스를 고유하게 식별하는 방법을 나타냅니다. URL(Uniform Resource Locator)의 일종으로, 웹 페이지, 파일, 서비스 등을 가리키는 주소입니다. 예를 들어, <code>https://www.example.com/about</code>와 같은 웹 주소가 URI의 한 형태입니다.</li>
<li>컨트롤러 및 메서드에 대한 동적 URI 생성은 주로 웹 애플리케이션에서 사용되는데, 이것은 코드의 하드 코딩된 URL을 피하고, 대신 컨트롤러와 연결된 메서드에 대한 URI를 더 유연하게 생성하는 것을 의미합니다. 이를 통해 URL 구조가 변경되거나 컨트롤러 메서드의 이름이 변경되더라도, 동적으로 생성된 URI는 해당 변경 사항에 대응하여 자동으로 업데이트됩니다. 이는 유지보수성을 높이고, 재사용 가능한 코드를 작성하는 데 도움이 됩니다.  </li>
</ul>
<ol start="6">
<li><strong>RedirectAttributes</strong>: 리다이렉트 요청을 처리하기 위한 인터페이스로, 모델 데이터를 유지하고 리다이렉트 요청으로 전달하는 데 사용됩니다. <code>Model</code>을 확장하여 모델 데이터를 리다이렉트 시에도 유지할 수 있게 합니다.</li>
</ol>
<ul>
<li>
<p>리다이렉트 요청은 클라이언트의 요청을 다른 위치로 전환하는 데 사용됩니다. 일반적으로, 이전에 요청한 URL에서 새로운 URL로 사용자를 전송하거나, 웹 애플리케이션에서 다른 페이지로 이동할 때 사용됩니다. 이는 사용자가 새로운 위치로 이동하거나, 새로운 리소스를 찾을 수 있도록 돕는 데 사용됩니다. 종종 HTTP 상태 코드 중 하나인 3xx 코드와 함께 사용되며, 서버에서 클라이언트로 리다이렉션 정보를 전송하여 처리합니다.</p>
</li>
<li>
<p>Flash attribute는 일시적으로 데이터를 저장하고 다음 요청으로 전달하는 메커니즘입니다. 스프링 프레임워크에서는 주로 리다이렉트 후에 데이터를 유지하고 싶을 때 사용됩니다. Flash attribute를 사용하면 다음과 같은 상황에서 데이터를 전달할 수 있습니다:</p>
<ul>
<li>한 번의 요청에서만 데이터를 유지하고자 할 때</li>
<li>리다이렉트 후에 데이터를 전달하고자 할 때</li>
</ul>
</li>
<li>
<p>Flash attribute는 일시적으로 유지되기 때문에 한 번의 요청에 대한 응답 이후 소멸됩니다. 이를 통해 리다이렉트 직후에 데이터를 전달하고 이후에는 필요하지 않을 때 사용됩니다.</p>
</li>
<li>
<p>일반적으로 Flash attribute는 주로 사용자에게 성공 또는 실패 메시지를 표시하거나 리다이렉트 후에 데이터를 전달할 때 활용됩니다.</p>
</li>
</ul>
<ol start="7">
<li>
<p><strong>Path</strong>: 파일 시스템 경로를 나타내는 인터페이스로, 파일 또는 디렉토리의 경로를 추상화합니다. <code>Comparable</code>로 비교 가능하며, <code>Iterable</code>로 요소를 순회하고, <code>Watchable</code>로 파일 시스템의 변경을 감시할 수 있습니다.</p>
</li>
<li>
<p><strong>Paths</strong>: 파일 시스템의 경로를 생성하는 유틸리티 클래스로, 파일 시스템 경로를 다루기 쉽게 만들어 줍니다.</p>
</li>
<li>
<p><strong>StandardCopyOption</strong>: 파일 복사에 대한 옵션을 제공하는 열거형 클래스로, 파일을 복사할 때 동작을 지정하는 옵션들을 정의합니다.</p>
</li>
<li>
<p><strong>FileSystemUtils</strong>: 파일 시스템 관련 유틸리티를 제공하는 추상 클래스로, 파일 및 디렉토리 관련 작업을 수행하는 메서드를 제공합니다.</p>
</li>
</ol>
<h3 id="주요-메서드">주요 메서드</h3>
<ol>
<li>
<p><strong>Files.walk(Path start, int maxDepth, FileVisitOption... options)</strong></p>
<ul>
<li>시작 경로부터 하위 디렉토리를 재귀적으로 탐색하는 메서드입니다.</li>
<li><code>start</code> 경로부터 시작하여 <code>maxDepth</code> 깊이까지 탐색하며, <code>FileVisitOption</code>으로 탐색 옵션을 설정할 수 있습니다.</li>
<li>디렉토리 내의 모든 하위 경로를 방문하고, <code>Stream&lt;Path&gt;</code> 형태로 반환하여 각 경로를 처리할 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>Path.resolve(Path other)</strong></p>
<ul>
<li>현재 경로와 다른 경로를 결합하여 새로운 경로를 생성하는 메서드입니다.</li>
<li>현재 경로가 디렉토리라면, 주어진 다른 경로를 현재 경로에 덧붙여 새로운 경로를 반환합니다.</li>
<li>상대 경로를 절대 경로로 변환하거나, 경로를 결합하여 새로운 경로를 생성할 때 사용합니다.</li>
</ul>
</li>
<li>
<p><strong>Path.relativize(Path other)</strong></p>
<ul>
<li>두 경로 간의 상대적인 경로를 생성하는 메서드입니다.</li>
<li>현재 경로를 기준으로 다른 경로와의 상대적인 경로를 계산하여 반환합니다.</li>
<li>두 경로가 같은 루트인 경우 상대적인 경로를 찾을 수 없을 때 예외가 발생합니다.</li>
</ul>
</li>
<li>
<p><strong>Path.normalize()</strong></p>
<ul>
<li>경로의 정규화된 형태를 반환하는 메서드입니다.</li>
<li>상대 경로("..")나 현재 경로(".") 등을 처리하여 경로를 더욱 의미 있는 형태로 바꿉니다.</li>
<li>경로의 중복된 요소를 제거하고, 상대 경로를 절대 경로로 변환하여 반환합니다.</li>
</ul>
</li>
</ol>
<h2 id="creating-an-html-template">Creating an HTML Template</h2>
<p>다음 Thymeleaf 템플릿(src/main/resources/templates/uploadForm.html)은 파일을 업로드하는 방법과 업로드된 내용을 보여주는 예를 보여줍니다.</p>
<p>이 템플릿은 세 부분으로 구성됩니다.</p>
<ul>
<li>Spring MVC가 flash-scoped 메시지를 작성하는 상단의 선택적 메시지입니다.</li>
<li>사용자가 파일을 업로드할 수 있는 양식입니다.</li>
<li>백엔드에서 제공되는 파일 목록입니다.</li>
</ul>
<pre><code class="language-html"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>html</span> <span class="token attr-name"><span class="token namespace">xmlns:</span>th</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>https://www.thymeleaf.org<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>body</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>div</span> <span class="token attr-name"><span class="token namespace">th:</span>if</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${message}<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>h2</span> <span class="token attr-name"><span class="token namespace">th:</span>text</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${message}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>div</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>div</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>form</span> <span class="token attr-name">method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>POST<span class="token punctuation">"</span></span> <span class="token attr-name">enctype</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>multipart/form-data<span class="token punctuation">"</span></span> <span class="token attr-name">action</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>/<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>table</span><span class="token punctuation">&gt;</span></span>
				<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>tr</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>td</span><span class="token punctuation">&gt;</span></span>File to upload:<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>td</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>td</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>input</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>file<span class="token punctuation">"</span></span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>file<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>td</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>tr</span><span class="token punctuation">&gt;</span></span>
				<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>tr</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>td</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>td</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>td</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>input</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>submit<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>Upload<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>td</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>tr</span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>table</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>form</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>div</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>div</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>ul</span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>li</span> <span class="token attr-name"><span class="token namespace">th:</span>each</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>file : ${files}<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
				<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>a</span> <span class="token attr-name"><span class="token namespace">th:</span>href</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${file}<span class="token punctuation">"</span></span> <span class="token attr-name"><span class="token namespace">th:</span>text</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${file}<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>li</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>ul</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>div</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>body</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>html</span><span class="token punctuation">&gt;</span></span></code></pre>
<h2 id="애플리케이션-기능과-구조">애플리케이션 기능과 구조</h2>
<ol>
<li>
<p>서비스 : 기능<br>
(1) 저장소 초기화 : initialize 할 때 필요한 것? 초기값, 기존에 저장된 파일 목록</p>
<ul>
<li>저장된 파일 목록 보이기</li>
<li>새로 추가한 파일 반영하여 리스트 갱신하기</li>
<li><strong>에러 발생시</strong> 이전 상태로 돌아가기 (<strong>리다이렉션</strong> = HTTP 3XX)</li>
</ul>
<p>(2) 파일 열기 : 파일을 검색하고 선택하고 가져오기 위해 필요한 단서가 무엇일까? 경로, 이름, 유형 </p>
<ul>
<li>특정 위치 내에서 파일 이름으로 지정하기</li>
<li>특정 위치 내의 파일 여러 개 지정하기</li>
</ul>
<p>(3) 파일 저장하기</p>
<ul>
<li>데이터베이스 연동(??)</li>
</ul>
<p>(4) 저장소의 파일 지우기</p>
</li>
<li>
<p>기능 별 요소</p>
<p>(1-1) 초기화 : 기존에 저장된 파일 목록</p>
<ul>
<li>프론트와 관련된 부분 : 템플릿으로 보여지는 부분</li>
<li>Rest, HTTP 관련 GetMapping, ResponseBody, ResponseEntity 등 어노테이션 예상</li>
<li>예상 반환 타입 : <code>List&lt;?&gt;</code> </li>
<li>메서드 레벨에서 HTTP 관련 어노테이션으로 사용할 것 같음</li>
<li>예상 메서드 : get</li>
</ul>
<p>(1-2) 초기화 : 리다이렉션 - 에러, 실패</p>
<ul>
<li>프론트와 관련된 부분 : Flash-Scoped 메시지 처리</li>
<li>뷰에 전달되는 데이터를 담는 'Model' 객체에 flash-scoped 메시지 관련 속성 더하기</li>
<li>Http Header 메시지에 리다이렉션 코드 및 메세지를 기재하기 위해 ResponseEntity 사용   </li>
<li>에러 유형 : 찾기 실패, 업로드 실패</li>
<li>에러 구현 : IOException 또는 RuntimeException 상속 또는 구현</li>
<li>예상 반환 타입 : ResponseEntity</li>
<li>예상 메서드 : </li>
</ul>
<p>(1-3) 초기화 : 올린 파일 찾기 </p>
<ul>
<li>애플리케이션에서 추상화할 수 있도록 <code>Resource</code> 형태로 추상화</li>
<li>추상화가 완료 후 변경 및 반영된 내역 반환 : 헤더메시지</li>
<li>예상 반환 타입 : <code>ResponseEntity</code></li>
<li>예상 메서드 : 저장소 클래스 내부 검색 기능</li>
</ul>
<p>(1-4) 초기화 : 리디렉션 : 업로드, 성공</p>
<ul>
<li>프론트와 관련된 부분 : Flash-Scoped 메시지 처리</li>
<li>뷰에 전달되는 데이터를 담는 '<code>Model</code>' 객체에 flash-scoped 메시지 관련 속성 더하기</li>
<li>Http Header 메시지에 리다이렉션 코드 및 메세지를 기재하기 위해 <code>ResponseEntity</code> 사용</li>
<li>예상 반환 타입 : <code>ResponseEntity</code></li>
<li>예상 메서드 : 저장소 저장 기능</li>
</ul>
<p>(2-1) 파일 열기 : 초기값 경로(루트) 가져오기</p>
<ul>
<li>예상 타입 : <code>Path</code></li>
<li>경로를 나타내는 주소를 스트링으로 표현하여 직접 조작하기보다 이미 만들어진 <code>Path</code> 의 메서드를 활용하는 것이 편해보임.</li>
<li>저장소로 쓸 디렉터리를 직접 기재 : <code>String</code></li>
<li><code>String</code>으로 표현된 디렉터리를 <code>Path</code>로 변환</li>
</ul>
<p>(2-2) 파일 열기 : 파일 이름으로 가져오기</p>
<ul>
<li>예상 반환 타입 : <code>Path</code></li>
<li>매개변수 타입 : <code>String</code></li>
<li>매개변수 : 파일 이름</li>
<li>예상 메서드 : 현재 경로와 다른 경로 결합하여 새로운 경로를 생성하거나 상대 경로를 절대 경로로 변환하는 <code>Path.resolve</code></li>
</ul>
<p>(2-3) 파일 열기 : 저장된 파일 전체 가져오기</p>
<ul>
<li>예상 반환 타입 : 여러 개의 경로를 가져오므로 기본타입이 <code>Path</code>인 Buffer 또는 Stream</li>
<li>예상 메서드 : 경로를 재귀적으로 검색하고 <code>Stream&lt;Path&gt;</code> 형태로 반환하여 각 경로를 처리하는 <code>Files.walk</code>, 현재 경로 기준 다른 경로와의 상대 경로 계산하는 <code>Path.relativize</code></li>
</ul>
<p>(2-4) 파일 열기 : 파일을 리소스 형태로 변환하기</p>
<ul>
<li>예상 반환 타입 : <code>Resource</code></li>
<li>filename으로 찾은 <code>Path</code>를 URI로 변환 후 이것을 다시 URL로 만들기</li>
</ul>
<p>(3-1) 파일 저장하기</p>
<ul>
<li>예산 반환 타입 : <code>void</code></li>
<li>매개변수 타입 : <code>MultipartFile</code></li>
<li>매개변수 : 파일 자체</li>
<li>저장 지점 : 루트에서 resolve하여 normalize </li>
<li>저장 작업 : InputStream으로 file을 받아서 저장 지점에 복사/덮어씌우기</li>
</ul>
</li>
</ol>
<h2 id="create-an-application-class">Create an Application Class</h2>
<p>Spring Boot MVC 애플리케이션을 시작하려면 먼저 스타터가 필요합니다. 이 샘플에서는 <code>spring-boot-starter-thymeleaf</code> 및 <code>spring-boot-starter-web</code>이 이미 종속성으로 추가되었습니다. 서블릿 컨테이너로 파일을 업로드하려면 <code>MultipartConfigElement</code> 클래스(<code>web.xml</code>의 <code>&lt;multipart-config&gt;</code>)를 등록해야 합니다. Spring Boot 덕분에 모든 것이 자동으로 구성됩니다!</p>
<p>이 애플리케이션을 시작하는 데 필요한 것은 다음 <code>UploadingFilesApplication</code> 클래스(src/main/java/guides/uploadingfiles/UploadingFilesApplication.java)입니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>uploadingfiles</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">UploadingFilesApplication</span> <span class="token punctuation">{</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">UploadingFilesApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
<span class="token punctuation">}</span></code></pre>
<p>Spring MVC 자동 구성(auto-configure)의 일부로 Spring Boot는 <code>MultipartConfigElement</code> 빈을 생성하고 파일 업로드를 준비합니다.</p>
<h3 id="스프링-부트에서-사용하는-주요-컨테이너">스프링 부트에서 사용하는 주요 컨테이너</h3>
<p>Spring Boot는 주로 서블릿 컨테이너를 내장하여 사용되지만, 필요에 따라 리액티브 컨테이너를 사용할 수도 있고, 외부의 웹 서버나 컨테이너 오케스트레이션 플랫폼과 통합하여 애플리케이션을 실행할 수 있습니다. Spring Boot는 이러한 다양한 환경에서 유연하게 실행될 수 있도록 다양한 설정 옵션을 제공합니다.</p>
<ol>
<li>
<p><strong>리액티브 웹 컨테이너</strong>:</p>
<ul>
<li>리액티브 웹 애플리케이션을 위한 컨테이너로, 비동기적이고 논블로킹 I/O를 사용하여 웹 요청을 처리합니다. Netty와 같은 컨테이너가 리액티브 애플리케이션을 지원합니다.</li>
</ul>
</li>
<li>
<p><strong>웹 서버</strong>:</p>
<ul>
<li>웹 서버는 HTTP 요청을 받아들이고 처리하는 서버입니다. 이 서버는 정적 파일을 제공하거나 웹 애플리케이션을 실행할 수 있습니다. Nginx, Apache HTTP Server 등이 대표적인 웹 서버입니다.</li>
</ul>
</li>
<li>
<p><strong>컨테이너 오케스트레이션 플랫폼</strong>:</p>
<ul>
<li>컨테이너 오케스트레이션 플랫폼은 여러 컨테이너를 관리하고 배포하는데 사용됩니다. Kubernetes, Docker Swarm, Apache Mesos 등이 있으며, 이러한 플랫폼을 사용하여 마이크로서비스를 구축하고 운영할 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>애플리케이션 서버</strong>:</p>
<ul>
<li>WAS(웹 애플리케이션 서버)는 여러 기능을 포함하고 있는 서버로, Java EE(Enterprise Edition) 애플리케이션을 실행하는 데 사용됩니다. 대표적으로는 Oracle WebLogic, IBM WebSphere 등이 있습니다.</li>
</ul>
</li>
</ol>
<h2 id="create-a-fileuploadcontroller-controller">Create a <code>FileUploadController</code> Controller</h2>
<h3 id="클래스--fileuploadcontroller">클래스 : FileUploadController</h3>
<p>초기 애플리케이션에는 업로드된 파일을 디스크에 저장하고 로드하는 작업을 처리하는 몇 가지 클래스가 이미 포함되어 있습니다. 모두 <code>guides.uploadingfiles.storage</code> 패키지에 있습니다. 이를 새 <code>FileUploadController</code>에서 사용하게 됩니다. 다음 목록(src/main/java/guides/uploadingfiles/FileUploadController.java)은 파일 업로드 컨트롤러를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>uploadingfiles</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">guides<span class="token punctuation">.</span>uploadingfiles<span class="token punctuation">.</span>storage</span><span class="token punctuation">.</span><span class="token class-name">StorageFileNotFoundException</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Autowired</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>core<span class="token punctuation">.</span>io</span><span class="token punctuation">.</span><span class="token class-name">Resource</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>http</span><span class="token punctuation">.</span><span class="token class-name">HttpHeaders</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>http</span><span class="token punctuation">.</span><span class="token class-name">ResponseEntity</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype</span><span class="token punctuation">.</span><span class="token class-name">Controller</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>ui</span><span class="token punctuation">.</span><span class="token class-name">Model</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span>*<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>multipart</span><span class="token punctuation">.</span><span class="token class-name">MultipartFile</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>mvc<span class="token punctuation">.</span>method<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">MvcUriComponentsBuilder</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>mvc<span class="token punctuation">.</span>support</span><span class="token punctuation">.</span><span class="token class-name">RedirectAttributes</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>io</span><span class="token punctuation">.</span><span class="token class-name">IOException</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>stream</span><span class="token punctuation">.</span><span class="token class-name">Collectors</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">guides<span class="token punctuation">.</span>uploadingfiles<span class="token punctuation">.</span>storage</span><span class="token punctuation">.</span><span class="token class-name">StorageFileNotFoundException</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">guides<span class="token punctuation">.</span>uploadingfiles<span class="token punctuation">.</span>storage</span><span class="token punctuation">.</span><span class="token class-name">StorageService</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Controller</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">FileUploadController</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">StorageService</span> storageService<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">public</span> <span class="token class-name">FileUploadController</span><span class="token punctuation">(</span><span class="token class-name">StorageService</span> storageService<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>storageService <span class="token operator">=</span> storageService<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">listUploadFiles</span><span class="token punctuation">(</span><span class="token class-name">Model</span> model<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">IOException</span><span class="token punctuation">{</span>

        model<span class="token punctuation">.</span><span class="token function">addAttribute</span><span class="token punctuation">(</span>
                <span class="token string">"files"</span><span class="token punctuation">,</span>
                storageService<span class="token punctuation">.</span><span class="token function">loadAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">map</span><span class="token punctuation">(</span>
                        path <span class="token operator">-&gt;</span> <span class="token class-name">MvcUriComponentsBuilder</span><span class="token punctuation">.</span><span class="token function">fromMethodName</span><span class="token punctuation">(</span>
                                <span class="token class-name">FileUploadController</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span>
                                <span class="token string">"serveFile"</span><span class="token punctuation">,</span>
                                path<span class="token punctuation">.</span><span class="token function">getFileName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
                        <span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">toUri</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
                <span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">collect</span><span class="token punctuation">(</span><span class="token class-name">Collectors</span><span class="token punctuation">.</span><span class="token function">toList</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token keyword">return</span> <span class="token string">"uploadForm"</span><span class="token punctuation">;</span>

    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/files/{filename:.+}"</span><span class="token punctuation">)</span>
    <span class="token annotation punctuation">@ResponseBody</span>
    <span class="token keyword">public</span> <span class="token class-name">ResponseEntity</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Resource</span><span class="token punctuation">&gt;</span></span> <span class="token function">serveFile</span><span class="token punctuation">(</span><span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">String</span> filename<span class="token punctuation">)</span> <span class="token punctuation">{</span>

        <span class="token class-name">Resource</span> file <span class="token operator">=</span> storageService<span class="token punctuation">.</span><span class="token function">loadAsResource</span><span class="token punctuation">(</span>filename<span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token keyword">if</span> <span class="token punctuation">(</span>file<span class="token operator">==</span><span class="token keyword">null</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">return</span> <span class="token class-name">ResponseEntity</span><span class="token punctuation">.</span><span class="token function">notFound</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        <span class="token keyword">return</span> <span class="token class-name">ResponseEntity</span><span class="token punctuation">.</span><span class="token function">ok</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">header</span><span class="token punctuation">(</span><span class="token class-name">HttpHeaders</span><span class="token punctuation">.</span>CONTENT_DISPOSITION<span class="token punctuation">,</span>
                        <span class="token string">"attachment; filename=\""</span> <span class="token operator">+</span> file<span class="token punctuation">.</span><span class="token function">getFilename</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">+</span> <span class="token string">"\""</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">body</span><span class="token punctuation">(</span>file<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@PostMapping</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">handleFileUpload</span><span class="token punctuation">(</span><span class="token annotation punctuation">@RequestParam</span><span class="token punctuation">(</span><span class="token string">"file"</span><span class="token punctuation">)</span> <span class="token class-name">MultipartFile</span> file<span class="token punctuation">,</span> <span class="token class-name">RedirectAttributes</span> redirectAttributes<span class="token punctuation">)</span> <span class="token punctuation">{</span>

        storageService<span class="token punctuation">.</span><span class="token function">store</span><span class="token punctuation">(</span>file<span class="token punctuation">)</span><span class="token punctuation">;</span>
        redirectAttributes<span class="token punctuation">.</span><span class="token function">addFlashAttribute</span><span class="token punctuation">(</span>
                <span class="token string">"message"</span><span class="token punctuation">,</span>
                <span class="token string">"You successfully uploaded "</span> <span class="token operator">+</span> file<span class="token punctuation">.</span><span class="token function">getOriginalFilename</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">+</span> <span class="token string">"!"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token keyword">return</span> <span class="token string">"redirect:/"</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@ExceptionHandler</span><span class="token punctuation">(</span><span class="token class-name">StorageFileNotFoundException</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token class-name">ResponseEntity</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token operator">?</span><span class="token punctuation">&gt;</span></span> <span class="token function">handleStorageFileNotFound</span><span class="token punctuation">(</span><span class="token class-name">StorageFileNotFoundException</span> exc<span class="token punctuation">)</span> <span class="token punctuation">{</span>

        <span class="token keyword">return</span> <span class="token class-name">ResponseEntity</span><span class="token punctuation">.</span><span class="token function">notFound</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p><code>FileUploadController</code> 클래스는 <code>@Controller</code>로 주석 처리되어 Spring MVC가 이를 선택하고 경로를 찾을 수 있습니다. 각 메서드에는 <code>@GetMapping</code> 또는 <code>@PostMapping</code> 태그가 지정되어 경로와 HTTP 작업을 특정 컨트롤러 작업에 연결합니다.</p>
<p>이 경우:</p>
<ul>
<li>
<p><code>GET /</code>: <code>StorageService</code>에서 현재 업로드된 파일 목록을 조회하고 이를 Thymeleaf 템플릿에 로드합니다. <code>MvcUriComponentsBuilder</code>를 사용하여 실제 리소스에 대한 링크를 계산합니다.</p>
</li>
<li>
<p><code>GET /files/{filename}</code>: 리소스(존재하는 경우)를 로드하고 Content-Disposition 응답 헤더를 사용하여 다운로드할 브라우저로 보냅니다.</p>
</li>
<li>
<p><code>POST /</code>: 여러 부분으로 구성된 메시지 파일을 처리하고 저장을 위해 StorageService에 제공합니다.</p>
</li>
</ul>
<blockquote>
<p>프로덕션 시나리오에서는 파일을 임시 위치, 데이터베이스 또는 NoSQL 저장소(예: Mongo의 GridFS)에 저장할 가능성이 높습니다. 애플리케이션의 파일 시스템에 콘텐츠를 로드하지 않는 것이 가장 좋습니다.</p>
</blockquote>
<hr>
<ol>
<li>
<p><code>@Controller</code><br>
컨트롤러는 웹 애플리케이션에서 주로 사용자 인터페이스와 비즈니스 로직 간의 중간 매개체로 작동하며, 클<br>
라이언트의 요청에 따라 적절한 데이터를 검색하고, 가공하여 적절한 뷰에 전달합니다.</p>
<p> 웹 애플리케이션에서 컨트롤러는 다양한 엔드포인트에 매핑되어 각각의 요청을 처리합니다. 엔드포인트는 네트워크 통신에서 특정한 서비스, 기능 또는 리소스에 접근하기 위한 경로나 URL을 나타냅니다. </p>
<p> 이러한 요청에는 HTTP 메소드(GET, POST, PUT, DELETE 등)와 함께 오는 데이터가 포함됩니다. 컨트롤러는 이러한 요청을 처리하고, 비즈니스 로직이나 서비스를 호출하여 데이터를 가져오거나 변경한 뒤, 적절한 뷰를 렌더링하여 사용자에게 응답을 제공합니다.</p>
</li>
<li>
<p><code>@Autowired</code>: 생성자 주입 방식을 사용하여 <code>StorageService</code>의 인스턴스를 주입합니다.</p>
</li>
<li>
<p><code>@GetMapping("/")</code>: 루트 경로(<code>/</code>)에 대한 GET 요청을 처리합니다. <code>Model</code>을 인자로 받아 파일 목록을 불러와 <code>uploadForm</code> 템플릿을 렌더링합니다.</p>
</li>
</ol>
<ul>
<li><code>Model</code>은 스프링에서 데이터를 뷰에 전달하는 데 사용되는 클래스입니다. </li>
<li><code>uploadForm</code>은 뷰를 나타내는 문자열이며, 스프링에서는 이것을 템플릿의 이름으로 사용합니다. 이 이름은 렌더링할 실제 템플릿 파일을 찾기 위해 사용됩니다. 일반적으로 <code>uploadForm</code>은 프론트엔드에서 사용되는 HTML 템플릿 파일의 이름이 될 수 있습니다.</li>
<li><code>model.addAttribute("files", ...)</code>: 모델에 <code>files</code>라는 속성을 추가합니다.</li>
<li><code>storageService.loadAll().map(...).collect(Collectors.toList())</code> : <code>storageService</code>에서 파일 목록을 가져오는 메서드를 호출하고, 파일 목록의 각 파일에 대해 <code>(...)</code>에 해당하는 내용을 처리 합니다. 그리고 처리한 파일을 리스트로 수집합니다.</li>
<li><code>MvcUriComponentsBuilder.fromMethodName</code>은 컨트롤러 클래스와 메서드 이름을 기반으로 URI를 생성하는 데 사용됩니다. 위 코드에서는 <code>FileUploadController</code> 클래스의 <code>serveFile</code> 메서드에 대한 URI를 생성하고 있어요. <code>serveFile</code> 메서드는 <code>/files/{filename:.+}</code>와 연결되어 있고, <code>path.getFileName().toString()</code>은 파일 이름을 의미합니다. 따라서 <code>MvcUriComponentsBuilder.fromMethodName</code>은 파일을 다운로드하기 위한 URI를 생성하는 데 사용됩니다.<ul>
<li><code>path.getFileName()</code>은 <code>Path</code>의 파일 이름을 나타내는 <code>Path</code> 객체를 반환합니다. 이 객체는 <code>toString()</code> 메서드를 통해 문자열로 변환될 수 있습니다</li>
<li><code>build()</code> 메서드 호출로 <code>UriComponents</code> 객체가 만들어집니다. 이 객체는 URI를 나타내는데, <code>.toUri()</code>를 호출하면 실제 <code>java.net.URI</code> 객체로 변환됩니다. 그리고 이렇게 만들어진 <code>java.net.URI</code> 객체를 문자열로 변환하기 위해 <code>.toString()</code>을 사용할 수 있습니다. </li>
<li><code>UriComponents</code>는 스프링 프레임워크의 URI 관련 유틸리티 클래스입니다. 이 클래스는 URI의 각 부분을 개별적으로 가져오고 조작할 수 있는 메서드들을 제공합니다. 반면 <code>java.net.URI</code> 클래스는 Java 표준 라이브러리에서 제공하는 URI를 다루기 위한 클래스입니다. 두 클래스는 비슷한 목적으로 URI를 다루지만, <code>UriComponents</code>는 스프링에서 제공하는 확장된 기능과 유틸리티를 제공하는 반면, <code>java.net.URI</code>는 Java 표준 기능만을 포함하고 있습니다.</li>
<li><code>UriComponents</code>의 <code>toString()</code> 메서드를 호출하면 URI의 문자열 표현을 얻을 수 있으므로, <code>toUri()</code> 메서드를 따로 사용하지 않아도 됩니다</li>
</ul></li>
</ul>
<p>4.<code>@GetMapping("/files/{filename:.+}")</code>: <code>/files/{filename}</code> </p>
<ul>
<li><code>{filename}</code>: 경로 변수. 여기에는 실제 파일 이름이 올 것입니다.</li>
<li><code>:.+</code>: <code>.</code>은 임의의 문자 하나를 의미하고, <code>+</code>는 하나 이상의 문자가 있는지를 나타냅니다. 따라서 <code>:.+</code>는 파일 이름에 마침표를 포함한 어떤 문자열이든 허용한다는 의미입니다. </li>
<li>즉, <code>/files/</code> 다음에 오는 경로 변수(<code>filename</code>)에는 확장자를 포함한 모든 파일 이름이 올 수 있도록 정의되었습니다.</li>
</ul>
<p>5.<code>@ResponseBody</code>: HTTP 응답 본문으로 직접 데이터를 작성하는 데 사용되는 어노테이션입니다.</p>
<p>6.<code>@PathVariable</code>: Spring MVC에서 URL 경로의 일부를 매개변수로 캡처하기 위해 사용되는 어노테이션입니다. <code>@PathVariable</code>은 URL 경로에서 특정한 부분을 추출하여 메서드의 매개변수로 전달합니다. 이 경우, <code>"/files/{filename:.+}"</code>에서 <code>{filename}</code>에 해당하는 값을 <code>String filename</code> 매개변수로 받아옵니다.</p>
<ul>
<li>
<p><code>ResponseEntity</code>: HTTP 응답을 나타내는 Spring 프레임워크의 클래스입니다. <code>ResponseEntity</code>는 HTTP 응답 코드, 헤더, 본문 등을 포함할 수 있는 강력한 반환 유형을 제공합니다. 이 클래스를 사용하면 응답을 특정 상태 코드와 함께 보낼 수 있고, 헤더 및 본문을 추가적으로 조작할 수 있습니다.</p>
</li>
<li>
<p><code>HttpHeaders</code>: HTTP 헤더를 나타내는 Spring 프레임워크의 클래스입니다. <code>HttpHeaders</code>를 사용하여 HTTP 응답 또는 요청의 헤더를 설정할 수 있습니다. 위 코드에서는 <code>HttpHeaders.CONTENT_DISPOSITION</code>을 사용하여 응답의 Content-Disposition 헤더를 설정하여 다운로드 파일의 이름을 지정하고 있습니다.</p>
</li>
</ul>
<p>7.<code>@PostMapping("/")</code>: 루트 경로(<code>/</code>)에 대한 POST 요청을 처리합니다. 업로드된 파일을 저장하고, <code>redirectAttributes</code>를 사용하여 메시지를 추가한 뒤 루트 경로로 리다이렉션합니다.</p>
<p>8.<code>@ExceptionHandler(StorageFileNotFoundException.class)</code>: <code>StorageFileNotFoundException</code> 예외를 처리하는 메소드로, 해당 예외가 발생하면 404 상태 코드를 반환합니다.</p>
<hr>
<h2 id="create-a-filesystemstorageservice-service">Create a <code>FileSystemStorageService</code> Service</h2>
<h3 id="인터페이스--storageservice">인터페이스 : StorageService</h3>
<p>컨트롤러가 스토리지 계층(예: 파일 시스템)과 상호 작용할 수 있도록 <code>StorageService</code>를 제공해야 합니다. 다음 목록(src/main/java/guides/uploadingfiles/storage/StorageService.java)은 해당 인터페이스를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>uploadingfiles<span class="token punctuation">.</span>storage</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>core<span class="token punctuation">.</span>io</span><span class="token punctuation">.</span><span class="token class-name">Resource</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>multipart</span><span class="token punctuation">.</span><span class="token class-name">MultipartFile</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>nio<span class="token punctuation">.</span>file</span><span class="token punctuation">.</span><span class="token class-name">Path</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>stream</span><span class="token punctuation">.</span><span class="token class-name">Stream</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">StorageService</span> <span class="token punctuation">{</span>

	<span class="token keyword">void</span> <span class="token function">init</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token keyword">void</span> <span class="token function">store</span><span class="token punctuation">(</span><span class="token class-name">MultipartFile</span> file<span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token class-name">Stream</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Path</span><span class="token punctuation">&gt;</span></span> <span class="token function">loadAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token class-name">Path</span> <span class="token function">load</span><span class="token punctuation">(</span><span class="token class-name">String</span> filename<span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token class-name">Resource</span> <span class="token function">loadAsResource</span><span class="token punctuation">(</span><span class="token class-name">String</span> filename<span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token keyword">void</span> <span class="token function">deleteAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token punctuation">}</span></code></pre>
<p>또한 <code>StorageService</code>를 지원하려면 네 가지 클래스가 필요합니다.</p>
<h3 id="클래스--storageproperties">클래스 : StorageProperties</h3>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>uploadingfiles<span class="token punctuation">.</span>storage</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>context<span class="token punctuation">.</span>properties</span><span class="token punctuation">.</span><span class="token class-name">ConfigurationProperties</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@ConfigurationProperties</span><span class="token punctuation">(</span><span class="token string">"storage"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">StorageProperties</span> <span class="token punctuation">{</span>

	<span class="token comment">/**
	 * Folder location for storing files
	 */</span>
	<span class="token keyword">private</span> <span class="token class-name">String</span> location <span class="token operator">=</span> <span class="token string">"upload-dir"</span><span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getLocation</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> location<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setLocation</span><span class="token punctuation">(</span><span class="token class-name">String</span> location<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>location <span class="token operator">=</span> location<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<h3 id="클래스--storageexception">클래스 : StorageException</h3>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>uploadingfiles<span class="token punctuation">.</span>storage</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">StorageException</span> <span class="token keyword">extends</span> <span class="token class-name">RuntimeException</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token class-name">StorageException</span><span class="token punctuation">(</span><span class="token class-name">String</span> message<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">super</span><span class="token punctuation">(</span>message<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token class-name">StorageException</span><span class="token punctuation">(</span><span class="token class-name">String</span> message<span class="token punctuation">,</span> <span class="token class-name">Throwable</span> cause<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">super</span><span class="token punctuation">(</span>message<span class="token punctuation">,</span> cause<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h3 id="클래스--storagenotfoundexception">클래스 : StorageNotFoundException</h3>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>uploadingfiles<span class="token punctuation">.</span>storage</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">StorageFileNotFoundException</span> <span class="token keyword">extends</span> <span class="token class-name">StorageException</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token class-name">StorageFileNotFoundException</span><span class="token punctuation">(</span><span class="token class-name">String</span> message<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">super</span><span class="token punctuation">(</span>message<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token class-name">StorageFileNotFoundException</span><span class="token punctuation">(</span><span class="token class-name">String</span> message<span class="token punctuation">,</span> <span class="token class-name">Throwable</span> cause<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">super</span><span class="token punctuation">(</span>message<span class="token punctuation">,</span> cause<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h3 id="클래스--filesystemstorageservice">클래스 : FileSystemStorageService</h3>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>uploadingfiles<span class="token punctuation">.</span>storage</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Autowired</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>core<span class="token punctuation">.</span>io</span><span class="token punctuation">.</span><span class="token class-name">Resource</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>core<span class="token punctuation">.</span>io</span><span class="token punctuation">.</span><span class="token class-name">UrlResource</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype</span><span class="token punctuation">.</span><span class="token class-name">Service</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>util</span><span class="token punctuation">.</span><span class="token class-name">FileSystemUtils</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>multipart</span><span class="token punctuation">.</span><span class="token class-name">MultipartFile</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>io</span><span class="token punctuation">.</span><span class="token class-name">IOException</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>io</span><span class="token punctuation">.</span><span class="token class-name">InputStream</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>net</span><span class="token punctuation">.</span><span class="token class-name">MalformedURLException</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>nio<span class="token punctuation">.</span>file</span><span class="token punctuation">.</span><span class="token class-name">Files</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>nio<span class="token punctuation">.</span>file</span><span class="token punctuation">.</span><span class="token class-name">Path</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>nio<span class="token punctuation">.</span>file</span><span class="token punctuation">.</span><span class="token class-name">Paths</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>nio<span class="token punctuation">.</span>file</span><span class="token punctuation">.</span><span class="token class-name">StandardCopyOption</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>stream</span><span class="token punctuation">.</span><span class="token class-name">Stream</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Service</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">FileSystemStorageService</span> <span class="token keyword">implements</span>  <span class="token class-name">StorageService</span> <span class="token punctuation">{</span>
    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">Path</span> rootLocation<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">public</span> <span class="token class-name">FileSystemStorageService</span><span class="token punctuation">(</span><span class="token class-name">StorageProperties</span> properties<span class="token punctuation">)</span> <span class="token punctuation">{</span>

        <span class="token keyword">if</span> <span class="token punctuation">(</span>properties<span class="token punctuation">.</span><span class="token function">getLocation</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">trim</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">length</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">==</span> <span class="token number">0</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">StorageException</span><span class="token punctuation">(</span><span class="token string">"File upload location can not be Empty"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        <span class="token keyword">this</span><span class="token punctuation">.</span>rootLocation <span class="token operator">=</span> <span class="token class-name">Paths</span><span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span>properties<span class="token punctuation">.</span><span class="token function">getLocation</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">store</span><span class="token punctuation">(</span><span class="token class-name">MultipartFile</span> file<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">try</span> <span class="token punctuation">{</span>
            <span class="token keyword">if</span> <span class="token punctuation">(</span>file<span class="token punctuation">.</span><span class="token function">isEmpty</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
                <span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">StorageException</span><span class="token punctuation">(</span><span class="token string">"Failed to storage empty file."</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>

            <span class="token class-name">Path</span> destinationFile <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">.</span>rootLocation<span class="token punctuation">.</span><span class="token function">resolve</span><span class="token punctuation">(</span>
                    <span class="token class-name">Paths</span><span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span>file<span class="token punctuation">.</span><span class="token function">getOriginalFilename</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
            <span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">normalize</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">toAbsolutePath</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

            <span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token operator">!</span>destinationFile<span class="token punctuation">.</span><span class="token function">getParent</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">equals</span><span class="token punctuation">(</span>
                    <span class="token keyword">this</span><span class="token punctuation">.</span>rootLocation<span class="token punctuation">.</span><span class="token function">toAbsolutePath</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
            <span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
                <span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">StorageException</span><span class="token punctuation">(</span><span class="token string">"Cannot store file outside current directory."</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>

            <span class="token keyword">try</span> <span class="token punctuation">(</span><span class="token class-name">InputStream</span> inputStream <span class="token operator">=</span> file<span class="token punctuation">.</span><span class="token function">getInputStream</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
                <span class="token class-name">Files</span><span class="token punctuation">.</span><span class="token function">copy</span><span class="token punctuation">(</span>
                        inputStream<span class="token punctuation">,</span>
                        destinationFile<span class="token punctuation">,</span>
                        <span class="token class-name">StandardCopyOption</span><span class="token punctuation">.</span>REPLACE_EXISTING
                <span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
        <span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">IOException</span> e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
                <span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">StorageException</span><span class="token punctuation">(</span><span class="token string">"Failed to store file."</span><span class="token punctuation">,</span> e<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token class-name">Stream</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Path</span><span class="token punctuation">&gt;</span></span> loadAll <span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">try</span> <span class="token punctuation">{</span>
            <span class="token keyword">return</span> <span class="token class-name">Files</span><span class="token punctuation">.</span><span class="token function">walk</span><span class="token punctuation">(</span><span class="token keyword">this</span><span class="token punctuation">.</span>rootLocation<span class="token punctuation">,</span> <span class="token number">1</span><span class="token punctuation">)</span>
                    <span class="token punctuation">.</span><span class="token function">filter</span><span class="token punctuation">(</span>path <span class="token operator">-&gt;</span> <span class="token operator">!</span>path<span class="token punctuation">.</span><span class="token function">equals</span><span class="token punctuation">(</span><span class="token keyword">this</span><span class="token punctuation">.</span>rootLocation<span class="token punctuation">)</span><span class="token punctuation">)</span>
                    <span class="token punctuation">.</span><span class="token function">map</span><span class="token punctuation">(</span><span class="token keyword">this</span><span class="token punctuation">.</span>rootLocation<span class="token operator">:</span><span class="token operator">:</span><span class="token function">relativize</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">IOException</span> e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">StorageException</span><span class="token punctuation">(</span><span class="token string">"Failed to read stored files"</span><span class="token punctuation">,</span> e<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token class-name">Path</span> load <span class="token punctuation">(</span><span class="token class-name">String</span> filename<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> rootLocation<span class="token punctuation">.</span><span class="token function">resolve</span><span class="token punctuation">(</span>filename<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token class-name">Resource</span> loadAsResource <span class="token punctuation">(</span><span class="token class-name">String</span> filename<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">try</span> <span class="token punctuation">{</span>
            <span class="token class-name">Path</span> file <span class="token operator">=</span> <span class="token function">load</span><span class="token punctuation">(</span>filename<span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token class-name">Resource</span> resource <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">UrlResource</span><span class="token punctuation">(</span>file<span class="token punctuation">.</span><span class="token function">toUri</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token keyword">if</span> <span class="token punctuation">(</span>resource<span class="token punctuation">.</span><span class="token function">exists</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">||</span> resource<span class="token punctuation">.</span><span class="token function">isReadable</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
                <span class="token keyword">return</span> resource<span class="token punctuation">;</span>
            <span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token punctuation">{</span>
                <span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">StorageFileNotFoundException</span><span class="token punctuation">(</span><span class="token string">"Could not read file: "</span> <span class="token operator">+</span> filename<span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
        <span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">MalformedURLException</span> e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">StorageFileNotFoundException</span><span class="token punctuation">(</span><span class="token string">"Could not read file: "</span> <span class="token operator">+</span> filename<span class="token punctuation">,</span> e<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> deleteAll <span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">FileSystemUtils</span><span class="token punctuation">.</span><span class="token function">deleteRecursively</span><span class="token punctuation">(</span>rootLocation<span class="token punctuation">.</span><span class="token function">toFile</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> init <span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">try</span> <span class="token punctuation">{</span>
            <span class="token class-name">Files</span><span class="token punctuation">.</span><span class="token function">createDirectories</span><span class="token punctuation">(</span>rootLocation<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">IOException</span> e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">StorageException</span><span class="token punctuation">(</span><span class="token string">"Could not initialize storage"</span><span class="token punctuation">,</span> e<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<ol>
<li><code> public void store(MultipartFile file) {...}</code></li>
</ol>
<ul>
<li>
<p><code>this.rootLocation.resolve(...).normalize().toAbsolutePath</code> : rootLocation에 새로운 경로<code>(...)</code>를 연결하여 표준 경로로 정규화 한 후 절대 경로로 변환 합니다. </p>
<ul>
<li><code>rootLocation</code>은 파일이 저장될 기본 디렉터리입니다. 이 저장소는 파일 시스템의 특정 위치를 가리키며, 데이터베이스가 아닌 파일 시스템에 파일을 저장합니다. 사용자가 지정한 위치에 파일을 저장하거나 불러올 수 있게끔 구성되어 있습니다.</li>
<li>normalize로 중복된 경로 구분자를 삭제 : <code>/folder1//folder2///file.txt</code>와 같이 중복된 슬래시가 있는 경우, 정규화를 하면 <code>/folder1/folder2/file.txt</code>와 같이 중복된 슬래시를 제거</li>
<li>normalize로 상대 경로를 간결하게 :  <code>/myFolder/../myFile.txt</code>와 같은 경로는 상위 디렉토리로 이동하는 <code>..</code>을 포함하고 있습니다. normalize를 하게 되면 이런 상위 경로 이동 구문을 해석하여 경로를 더 간결하게 만들어줍니다. 따라서 이 경우 <code>/myFile.txt</code>로 경로가 정규화</li>
<li><code>toAbsolutePath()</code>는 좀 더 철저한 절대 경로</li>
</ul>
</li>
<li>
<p><code>destinationFile.getParent()</code> : <code>getParent()</code>는 현재 경로의 부모 경로를 나타내는 <code>Path</code> 객체를 반환합니다. 이 메서드는 파일 시스템에 직접 접근하지 않으며, 경로 또는 해당 부모가 존재하지 않을 수 있습니다. 또한, 이 메서드는 구현에 따라 "<code>.</code>", "<code>..</code>"과 같은 특수 이름을 제거하지 않습니다. 예를 들어 UNIX 시스템에서 <code>/a/b/c</code>의 부모는 <code>/a/b</code>이며, <code>x/y/.</code>의 부모는 <code>x/y</code>입니다.</p>
</li>
<li>
<p><code>try (InputStream inputStream = file.getInputStream()) {...}</code> : Java 7에서 추가된 기능 중 하나인 'try-with-resources'입니다. 이를 사용하면 <code>try</code> 블록 내에서 자동으로 리소스를 닫을 수 있습니다. </p>
<ul>
<li>try-with-resources :  이 방식은 <code>AutoCloseable</code> 인터페이스를 구현하는 리소스를 사용할 때 특히 유용합니다. 이것을 사용하면 코드 블록이 끝나는 시점에 리소스를 자동으로 닫아 <strong>메모리 누수를 방지</strong>할 수 있습니다. Java에서 파일, 소켓 및 데이터베이스 연결과 같은 외부 리소스를 다룰 때 유용하게 활용됩니다.</li>
<li>이 경우 <code>inputStream</code>은 <code>getInputStream()</code>으로 얻은 값을 파일에 복사할 때 사용됩니다</li>
</ul>
</li>
</ul>
<ol start="2">
<li><code>public Stream&lt;Path&gt; loadAll () {...}</code> </li>
</ol>
<ul>
<li><code>Files.walk(this.rootLocation, 1)</code>: 지정된 경로(<code>this.rootLocation</code>)에서 하위 파일과 디렉토리를 포함하여 최대 1단계까지 탐색합니다.<ul>
<li><code>.filter(path -&gt; !path.equals(this.rootLocation))</code>: 시작 경로(<code>this.rootLocation</code>)를 제외한 모든 경로를 필터링합니다.</li>
<li><code>.map(this.rootLocation::relativize)</code>: 시작 경로(<code>this.rootLocation</code>)로부터 상대 경로를 가져옵니다.<ul>
<li>두 경로가 모두 루트 컴포넌트를 가지지 않는다는 것은 서로 동일한 루트를 공유하지 않는 부모를 가지고 있다는 것을 의미합니다. 예를 들어, 경로 A가 <code>/root/folder1</code>이고 경로 B가 <code>/root/folder2</code>인 경우, 이 두 경로는 같은 루트인 <code>/root</code>를 가지고 있으며, 서로 다른 하위 디렉토리인 <code>folder1</code>과 <code>folder2</code>를 부모로 가지고 있습니다. 따라서 이런 상황에서 <code>relativize</code> 메서드를 사용하여 상대 경로를 만들 수 있습니다.</li>
</ul></li>
</ul></li>
<li><code>catch (IOException e) { throw new StorageException("Failed to read stored files", e); }</code>: 입출력 예외가 발생하면 <code>StorageException</code>을 던집니다.</li>
</ul>
<ol start="3">
<li><code>deleteRecursively(rootLocation.toFile())</code></li>
</ol>
<ul>
<li><code>deleteRecursively()</code>는 스프링 프레임워크의 <code>FileSystemUtils</code> 클래스에서 제공되는 메서드이며, 지정된 경로를 재귀적으로 삭제하는데 사용됩니다. 지정된 경로가 디렉터리인 경우, 해당 디렉터리와 그 내용물을 모두 삭제합니다.</li>
<li><code>deleteRecursively(rootLocation.toFile())</code>는 <code>rootLocation</code>에 지정된 디렉터리를 모두 삭제합니다.</li>
<li><code>rootLocation</code> 자체는 삭제하지 않습니다. </li>
</ul>
<ol start="4">
<li><code>Files.createDirectories(rootLocation)</code></li>
</ol>
<ul>
<li><code>createDirectories()</code> 메서드는 파일 시스템에 디렉터리를 생성할 때 사용됩니다. 해당 경로에 디렉터리가 없으면 디렉터리를 생성하고, 디렉터리가 이미 존재하는 경우에는 아무런 동작을 하지 않습니다.</li>
<li><code>Files.createDirectories(rootLocation)</code>은 <code>rootLocation</code>에 해당하는 디렉터리가 존재하지 않으면 그 디렉터리를 생성합니다. </li>
</ul>
<ol start="5">
<li><code>public Resource loadAsResource (String filename) {...}</code></li>
</ol>
<ul>
<li><code>Path file = load(filename)</code> : 같은 클래스 내부의 <code>load</code> 메서드를 사용한 것이며 <code>rootLocation.resolve(filename)</code> 과 같다. </li>
<li><code>Resource resource = new UrlResource(file.toUri())</code> : <code>UrlResource</code>는 <code>Resource</code> 인터페이스를 구현한 추상 클래스 <code>AbstractResource</code>를 상속한 <code>AbstractFileResolvingResource</code>를 상속한 클래스이다.   - 업캐스팅을 위한 <code>(Resource)</code>가 생략되었다. </li>
</ul>
<ol start="6">
<li><code>public Path load (String filename) { return rootLocation.resolve(filename);}</code> </li>
</ol>
<ul>
<li><code>load</code> 의 반환 값을 보았을 때, 컨트롤러에서 model에 <code>files</code> 라는 속성명으로 <code>storageService.loadAll()</code>로 가져온 <code>Stream&lt;Path&gt;</code>에 <code>map</code>으로 처리하는 일련의 가공을 단순화 할 수도 있을 것 같다. <ul>
<li><code>model.addAttribute( "files", storageService.loadAll().map(path -&gt; path.toUri()).collect(Collectors.toList()));</code></li>
<li>여기서 더 나아가 람다 표현식<code>path -&gt; path.toUri()</code>를 메서드 참조 <code>Path::toUri</code>로 교체 가능하다. <ul>
<li>둘 다 <code>Path</code> 객체를 가져와 <code>toUri()</code> 메서드를 호출하여 해당 경로를 URI로 변환합니다. 이 두 가지 방법은 기능적으로 동등하다.</li>
<li>함수형 인터페이스를 통한 람다 표현식 또는 메서드 참조를 사용하여 같은 결과를 얻을 수 있습니다.</li>
</ul></li>
</ul></li>
</ul>
<hr>
<h2 id="tuning-file-upload-limits">Tuning File Upload Limits</h2>
<p>파일 업로드를 구성할 때 파일 크기에 대한 제한을 설정하는 것이 유용한 경우가 많습니다. 5GB 파일 업로드를 처리한다고 상상해보세요! Spring Boot를 사용하면 일부 속성 설정으로 자동 구성된 <code>MultipartConfigElement</code>를 조정할 수 있습니다.</p>
<p>기존 속성 설정(src/main/resources/application.properties)에 다음 속성을 추가합니다.</p>
<pre><code class="language-null">spring.servlet.multipart.max-file-size=128KB
spring.servlet.multipart.max-request-size=128KB</code></pre>
<p>멀티파트 설정은 다음과 같이 제한됩니다.</p>
<ul>
<li><code>spring.servlet.multipart.max-file-size</code>는 128KB로 설정됩니다. 이는 총 파일 크기가 128KB를 초과할 수 없음을 의미합니다.</li>
<li><code>spring.servlet.multipart.max-request-size</code>는 128KB로 설정됩니다. 이는 <code>multipart/form-data</code>에 대한 총 요청 크기가 128KB를 초과할 수 없음을 의미합니다.<ul>
<li>HTML<code>&lt;form&gt;</code> 태그의 <code>enctype</code> 속성은 폼 데이터(form data)가 서버로 제출될 때 해당 데이터가 인코딩되는 방법을 명시합니다.</li>
<li><code>multipart/form-data</code> 이 속성값은 모든 문자를 인코딩하지 않음을 명시하며 <code>&lt;form&gt;</code> 요소가 파일이나 이미지를 서버로 전송할 때 주로 사용합니다.</li>
</ul></li>
</ul>
<h2 id="update-the-application">Update the Application</h2>
<p>파일을 업로드할 대상 폴더가 필요하므로 Spring Initializr가 생성한 기본 <code>UploadingFilesApplication</code> 클래스를 강화하고 Boot <code>CommandLineRunner</code>를 추가하여 시작 시 해당 폴더를 삭제하고 다시 생성해야 합니다. 다음 목록(src/main/java/guides/uploadingfiles/UploadingFilesApplication.java)은 이를 수행하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>uploadingfiles</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">CommandLineRunner</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>context<span class="token punctuation">.</span>properties</span><span class="token punctuation">.</span><span class="token class-name">EnableConfigurationProperties</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Bean</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">guides<span class="token punctuation">.</span>uploadingfiles<span class="token punctuation">.</span>storage</span><span class="token punctuation">.</span><span class="token class-name">StorageProperties</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">guides<span class="token punctuation">.</span>uploadingfiles<span class="token punctuation">.</span>storage</span><span class="token punctuation">.</span><span class="token class-name">StorageService</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token annotation punctuation">@EnableConfigurationProperties</span><span class="token punctuation">(</span><span class="token class-name">StorageProperties</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">UploadingFilesApplication</span> <span class="token punctuation">{</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">UploadingFilesApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Bean</span>
    <span class="token class-name">CommandLineRunner</span> <span class="token function">init</span><span class="token punctuation">(</span><span class="token class-name">StorageService</span> storageService<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token punctuation">(</span>args<span class="token punctuation">)</span> <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
            storageService<span class="token punctuation">.</span><span class="token function">deleteAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            storageService<span class="token punctuation">.</span><span class="token function">init</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<ul>
<li>
<p><code>@ConfigurationProperties</code> : 외부화된 구성에 대한 애너테이션입니다. 일부 외부 속성(예: <code>.properties</code> 파일에서)을 바인딩하고 유효성을 검사하려면 <code>@Configuration</code> 클래스의 <code>@Bean</code> 메서드 또는 클래스 정의에 이를 추가하세요.<br>
바인딩은 애너테이션이 달린 클래스에서 <code>setter</code>를 호출하거나 <code>@ConstructorBinding</code>을 사용하는 경우 생성자 매개 변수에 바인딩하여 수행됩니다.<br>
<code>@Value</code>와는 반대로 SpEL 표현식은 속성 값이 외부화되므로 평가되지 않습니다.</p>
</li>
<li>
<p><code>@ConfigurationProperties("storage")</code>는 속성 파일(<code>application.properties</code>)이나 환경 변수에서 <code>storage.*</code>와 일치하는 속성을 찾습니다. 이 코드에서는 <code>StorageProperties</code> 클래스의 필드와 매핑하려고 시도합니다. 만약 <code>storage.*</code>와 매치되는 속성이 없다면 그 필드들은 디폴트 값(null 또는 primitive type의 default 값)으로 초기화됩니다.</p>
</li>
<li>
<p><code>@EnableConfigurationProperties</code>는 <code>@ConfigurationProperties</code>로 설정된 클래스들을 스프링에서 사용할 수 있도록 빈으로 등록합니다. 설정 클래스에 정의된 필드들과 매칭하여, 애플리케이션의 설정 파일에 있는 값을 읽어올 수 있게 됩니다. </p>
</li>
<li>
<p><code>@Bean CommandLineRunner init(StorageService storageService)</code> : <code>init</code> 메서드를 통해 <code>StorageService</code>를 주입받아 빈을 생성하고 있음. <code>@Bean CommandLineRunner run(@Autowired StorageService storageService)</code> 는 생성자 주입 annotation인 <code>@Autowired</code>를 통해 <code>StorageService</code>를 주입받는 <code>run</code> 메서드를 통해 빈을 생성하므로 두 코드가 기능적으로 같음.</p>
</li>
</ul>
<h2 id="테스트-관련-개발-지식">테스트 관련 개발 지식</h2>
<p>1.<code>@SpringBootTest</code> : Spring Boot 기반 테스트를 실행하는 테스트 클래스에 지정할 수 있는 주석입니다. 일반 Spring TestContext Framework 외에 다음 기능을 제공합니다.</p>
<ul>
<li>특정 <code>@ContextConfiguration(loader=...)</code>이 정의되지 않은 경우 <code>SpringBootContextLoader</code>를 기본 <code>ContextLoader</code>로 사용합니다.</li>
<li>중첩된 <code>@Configuration</code>이 사용되지 않고 명시적 클래스가 지정되지 않은 경우 <code>@SpringBootConfiguration</code>을 자동으로 검색합니다.</li>
<li><code>properties</code> 속성을 사용하여 사용자 정의 <code>Environment</code> 속성을 정의할 수 있습니다.</li>
<li><code>args</code> 속성을 사용하여 애플리케이션 인수를 정의할 수 있습니다.</li>
<li>정의된 포트 또는 임의의 포트에서 수신 대기하는 완전히 실행되는 웹 서버를 시작하는 기능을 포함하여 다양한 <code>webEnvironment</code> 모드에 대한 지원을 제공합니다.</li>
<li>완전히 실행되는 웹 서버를 사용하는 웹 테스트에 사용할 <code>TestRestTemplate</code> 및/또는 <code>WebTestClient</code> Bean을 등록합니다.</li>
</ul>
<p>2.Mockito 와 BDDMockito</p>
<ul>
<li><a href="https://www.baeldung.com/mockito-series">Mockito</a></li>
<li><a href="https://en.wikipedia.org/wiki/Behavior-driven_development">Behavior Driven Development Mockito</a></li>
</ul>
<p>3.<code>@MockBean</code><br>
Spring ApplicationContext에 Mock을 추가하는 데 사용할 수 있는 어노테이션입니다. </p>
<ul>
<li>클래스 레벨 어노테이션, </li>
<li><code>@Configuration</code> 클래스, </li>
<li><code>SpringRunner</code>로 실행되는(<code>@RunWith</code>) 테스트 클래스의 필드에 사용할 수 있습니다.</li>
</ul>
<p>Mock의 등록 방식 </p>
<ul>
<li>유형(type)별로 등록하면 컨텍스트에 있는 해당 유형의 기존 단일 빈(하위 클래스 포함)은 Mock으로 대체됩니다. </li>
<li>이름(name)별로 등록하면 특정 빈을 명시적으로 Mock으로 대상화할 수 있습니다. </li>
<li>어느 경우에도 기존 빈이 정의되지 않은 경우 새로운 빈이 추가됩니다. </li>
<li>애플리케이션 컨텍스트에 알려진 하지만 빈이 아닌 종속성(직접 등록된 것과 같은)은 찾을 수 없으며 기존 종속성 옆에 Mock된 빈이 추가됩니다.</li>
</ul>
<p>(1) 일반적인 사용 예시</p>
<pre><code class="language-java"><span class="token annotation punctuation">@RunWith</span><span class="token punctuation">(</span><span class="token class-name">SpringRunner</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ExampleTests</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@MockBean</span>
    <span class="token keyword">private</span> <span class="token class-name">ExampleService</span> service<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> <span class="token class-name">UserOfService</span> userOfService<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">testUserOfService</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token function">given</span><span class="token punctuation">(</span><span class="token keyword">this</span><span class="token punctuation">.</span>service<span class="token punctuation">.</span><span class="token function">greet</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">willReturn</span><span class="token punctuation">(</span><span class="token string">"Hello"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">String</span> actual <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">.</span>userOfService<span class="token punctuation">.</span><span class="token function">makeUse</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"Was: Hello"</span><span class="token punctuation">,</span> actual<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Configuration</span>
    <span class="token annotation punctuation">@Import</span><span class="token punctuation">(</span><span class="token class-name">UserOfService</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span> <span class="token comment">// ExampleService로 주입되는 @Component</span>
    <span class="token keyword">static</span> <span class="token keyword">class</span> <span class="token class-name">Config</span> <span class="token punctuation">{</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>테스트의 목적은 <code>UserOfService</code>를 테스트하고, 이 때 <code>ExampleService</code>를 Mock으로 대체하여 특정 상황을 시뮬레이션하는 것으로 보입니다.</p>
<ul>
<li>
<p><code>@RunWith(SpringRunner.class)</code>: 이 어노테이션은 테스트를 실행할 때 스프링의 테스트 컨텍스트와 함께 JUnit을 실행하도록 지시합니다. <code>SpringRunner</code>는 JUnit에 통합된 스프링 테스트 지원을 활용하여 테스트를 실행합니다.</p>
</li>
<li>
<p><code>@MockBean</code>: 이 어노테이션은 테스트에서 사용할 Mock 빈을 선언합니다. 여기서는 <code>ExampleService</code>를 Mock으로 선언하고 있습니다. <code>MockBean</code> 어노테이션을 사용하면 해당 빈이 스프링 애플리케이션 컨텍스트에 Mock으로 등록되고, 필요한 경우 실제 빈 대신 Mock이 사용됩니다.</p>
</li>
<li>
<p><code>@Autowired</code>: 이 어노테이션은 필드 또는 생성자에 사용되어 해당 타입의 빈을 주입받습니다. 여기서는 <code>UserOfService</code>를 주입받고 있습니다. 스프링은 <code>@Autowired</code>를 통해 필요한 빈을 찾아서 주입해줍니다.</p>
</li>
<li>
<p><code>@Configuration</code>: 이 어노테이션은 스프링의 자바 기반 설정 클래스를 나타냅니다. 여기서는 <code>Config</code> 클래스가 <code>@Configuration</code> 어노테이션을 가지고 있어서 스프링에게 이 클래스가 애플리케이션의 설정을 포함하고 있다고 알려줍니다.</p>
</li>
<li>
<p><code>@Import(UserOfService.class)</code>: 이 어노테이션은 다른 설정 클래스나 컴포넌트를 현재의 설정 클래스에 추가로 포함하도록 합니다. 여기서는 <code>UserOfService</code> 클래스를 추가로 포함시켜서 해당 클래스에 정의된 빈들을 사용할 수 있도록 합니다.</p>
</li>
</ul>
<p>(2) 동일한 타입의 빈을 여러개 생성한 경우 : 필드 레벨에서 한정자(<code>@Qulaifier</code>) 메타데이터를 지정해야 합니다.</p>
<p>여러 데이터베이스 연결을 위해 동일한 타비의 빈을 생성한 상황</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Component</span>
<span class="token annotation punctuation">@Qualifier</span><span class="token punctuation">(</span><span class="token string">"mysql"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MySQLDataSource</span> <span class="token keyword">implements</span> <span class="token class-name">DataSource</span> <span class="token punctuation">{</span>
    <span class="token comment">// MySQL 데이터베이스 연결에 필요한 설정</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@Component</span>
<span class="token annotation punctuation">@Qualifier</span><span class="token punctuation">(</span><span class="token string">"postgresql"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">PostgreSQLDataSource</span> <span class="token keyword">implements</span> <span class="token class-name">DataSource</span> <span class="token punctuation">{</span>
    <span class="token comment">// PostgreSQL 데이터베이스 연결에 필요한 설정</span>
<span class="token punctuation">}</span></code></pre>
<p>위의 코드에서 <code>MySQLDataSource</code>와 <code>PostgreSQLDataSource</code>는 모두 <code>DataSource</code> 인터페이스를 구현하고 있습니다. 그러나 이 두 개의 빈은 각각 <code>MySQL</code>과 <code>PostgreSQL</code>과 같은 다른 데이터베이스와 연관되어 있습니다.</p>
<p>이제 두 개의 빈을 사용하여 다른 클래스에서 주입할 때 <code>@Qualifier</code>를 사용할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Service</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">DataService</span> <span class="token punctuation">{</span>
    
    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">DataSource</span> mysqlDataSource<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">DataSource</span> postgresqlDataSource<span class="token punctuation">;</span>
    
    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">public</span> <span class="token class-name">DataService</span><span class="token punctuation">(</span><span class="token annotation punctuation">@Qualifier</span><span class="token punctuation">(</span><span class="token string">"mysql"</span><span class="token punctuation">)</span> <span class="token class-name">DataSource</span> mysqlDataSource<span class="token punctuation">,</span> 
                       <span class="token annotation punctuation">@Qualifier</span><span class="token punctuation">(</span><span class="token string">"postgresql"</span><span class="token punctuation">)</span> <span class="token class-name">DataSource</span> postgresqlDataSource<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>mysqlDataSource <span class="token operator">=</span> mysqlDataSource<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>postgresqlDataSource <span class="token operator">=</span> postgresqlDataSource<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="testing-your-application">Testing Your Application</h2>
<h3 id="fileuploadtests">FileUploadTests</h3>
<p>우리 애플리케이션에서 이 특정 기능을 테스트하는 방법에는 여러 가지가 있습니다. 다음 목록(src/test/java/guides/uploadingfiles/FileUploadTests.java)은 서블릿 컨테이너를 시작할 필요가 없도록 <code>MockMvc</code>를 사용하는 한 가지 예를 보여줍니다.</p>
<p>이러한 테스트에서는 다양한 모의 객체를 사용하여 컨트롤러 및 <code>StorageService</code>와의 상호 작용을 설정하지만 <code>MockMultipartFile</code>을 사용하여 서블릿 컨테이너 자체와의 상호 작용도 설정합니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>uploadingfiles</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>nio<span class="token punctuation">.</span>file</span><span class="token punctuation">.</span><span class="token class-name">Paths</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>stream</span><span class="token punctuation">.</span><span class="token class-name">Stream</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>hamcrest</span><span class="token punctuation">.</span><span class="token class-name">Matchers</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>junit<span class="token punctuation">.</span>jupiter<span class="token punctuation">.</span>api</span><span class="token punctuation">.</span><span class="token class-name">Test</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Autowired</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>autoconfigure<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet</span><span class="token punctuation">.</span><span class="token class-name">AutoConfigureMockMvc</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>context</span><span class="token punctuation">.</span><span class="token class-name">SpringBootTest</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>mock<span class="token punctuation">.</span>mockito</span><span class="token punctuation">.</span><span class="token class-name">MockBean</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>mock<span class="token punctuation">.</span>web</span><span class="token punctuation">.</span><span class="token class-name">MockMultipartFile</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet</span><span class="token punctuation">.</span><span class="token class-name">MockMvc</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>mockito</span><span class="token punctuation">.</span><span class="token class-name">BDDMockito</span><span class="token punctuation">.</span>given<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>mockito</span><span class="token punctuation">.</span><span class="token class-name">BDDMockito</span><span class="token punctuation">.</span>then<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>request</span><span class="token punctuation">.</span><span class="token class-name">MockMvcRequestBuilders</span><span class="token punctuation">.</span>get<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>request</span><span class="token punctuation">.</span><span class="token class-name">MockMvcRequestBuilders</span><span class="token punctuation">.</span>multipart<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>result</span><span class="token punctuation">.</span><span class="token class-name">MockMvcResultMatchers</span><span class="token punctuation">.</span>header<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>result</span><span class="token punctuation">.</span><span class="token class-name">MockMvcResultMatchers</span><span class="token punctuation">.</span>model<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>result</span><span class="token punctuation">.</span><span class="token class-name">MockMvcResultMatchers</span><span class="token punctuation">.</span>status<span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">guides<span class="token punctuation">.</span>uploadingfiles<span class="token punctuation">.</span>storage</span><span class="token punctuation">.</span><span class="token class-name">StorageFileNotFoundException</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">guides<span class="token punctuation">.</span>uploadingfiles<span class="token punctuation">.</span>storage</span><span class="token punctuation">.</span><span class="token class-name">StorageService</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@AutoConfigureMockMvc</span>
<span class="token annotation punctuation">@SpringBootTest</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">FileUploadTests</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> <span class="token class-name">MockMvc</span> mvc<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@MockBean</span>
    <span class="token keyword">private</span> <span class="token class-name">StorageService</span> storageService<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">shouldListAllFiles</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
        <span class="token function">given</span><span class="token punctuation">(</span><span class="token keyword">this</span><span class="token punctuation">.</span>storageService<span class="token punctuation">.</span><span class="token function">loadAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">willReturn</span><span class="token punctuation">(</span><span class="token class-name">Stream</span><span class="token punctuation">.</span><span class="token function">of</span><span class="token punctuation">(</span><span class="token class-name">Paths</span><span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token string">"first.txt"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token class-name">Paths</span><span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token string">"second.txt"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token keyword">this</span><span class="token punctuation">.</span>mvc<span class="token punctuation">.</span><span class="token function">perform</span><span class="token punctuation">(</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">andExpect</span><span class="token punctuation">(</span><span class="token function">status</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isOk</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">andExpect</span><span class="token punctuation">(</span><span class="token function">model</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">attribute</span><span class="token punctuation">(</span><span class="token string">"files"</span><span class="token punctuation">,</span>
                        <span class="token class-name">Matchers</span><span class="token punctuation">.</span><span class="token function">contains</span><span class="token punctuation">(</span><span class="token string">"http://localhost/files/first.txt"</span><span class="token punctuation">,</span>
                                <span class="token string">"http://localhost/files/second.txt"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">shouldSaveUploadedFile</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
        <span class="token class-name">MockMultipartFile</span> multipartFile <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">MockMultipartFile</span><span class="token punctuation">(</span><span class="token string">"file"</span><span class="token punctuation">,</span> <span class="token string">"test.txt"</span><span class="token punctuation">,</span>
                <span class="token string">"text/plain"</span><span class="token punctuation">,</span> <span class="token string">"Spring Framework"</span><span class="token punctuation">.</span><span class="token function">getBytes</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>mvc<span class="token punctuation">.</span><span class="token function">perform</span><span class="token punctuation">(</span><span class="token function">multipart</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">file</span><span class="token punctuation">(</span>multipartFile<span class="token punctuation">)</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">andExpect</span><span class="token punctuation">(</span><span class="token function">status</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isFound</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">andExpect</span><span class="token punctuation">(</span><span class="token function">header</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">string</span><span class="token punctuation">(</span><span class="token string">"Location"</span><span class="token punctuation">,</span> <span class="token string">"/"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token function">then</span><span class="token punctuation">(</span><span class="token keyword">this</span><span class="token punctuation">.</span>storageService<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">should</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">store</span><span class="token punctuation">(</span>multipartFile<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@SuppressWarnings</span><span class="token punctuation">(</span><span class="token string">"unchecked"</span><span class="token punctuation">)</span>
    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">should404WhenMissingFile</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
        <span class="token function">given</span><span class="token punctuation">(</span><span class="token keyword">this</span><span class="token punctuation">.</span>storageService<span class="token punctuation">.</span><span class="token function">loadAsResource</span><span class="token punctuation">(</span><span class="token string">"test.txt"</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">willThrow</span><span class="token punctuation">(</span><span class="token class-name">StorageFileNotFoundException</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token keyword">this</span><span class="token punctuation">.</span>mvc<span class="token punctuation">.</span><span class="token function">perform</span><span class="token punctuation">(</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token string">"/files/test.txt"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">andExpect</span><span class="token punctuation">(</span><span class="token function">status</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isNotFound</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<ul>
<li>여기 코드는 Spring 프레임워크에서 제공하는 테스트 환경과 함께 사용하는 테스트 클래스입니다. 각 부분을 요약하겠습니다.</li>
</ul>
<ol>
<li>
<p><code>@AutoConfigureMockMvc</code>와 <code>@SpringBootTest</code>: 이 테스트 클래스는 Spring의 MockMvc를 사용하여 웹 애플리케이션을 테스트하며, <code>@SpringBootTest</code>를 통해 Spring Boot 애플리케이션의 전체적인 컨텍스트를 로드합니다.</p>
</li>
<li>
<p><code>@Autowired private MockMvc mvc</code>: <code>MockMvc</code>를 주입받아 사용할 수 있도록 합니다. </p>
</li>
</ol>
<ul>
<li>MockMvc 객체는 스프링 MVC를 테스트하기 위한 프레임워크입니다. <ul>
<li>HTTP 요청을 보내고 응답을 수신합니다.</li>
<li>요청 헤더, 요청 본문, 응답 코드, 응답 본문 등을 확인합니다.</li>
<li>컨트롤러의 요청 매핑, 핸들러 메서드, 파라미터 바인딩 등을 테스트합니다.</li>
</ul></li>
</ul>
<ol start="3">
<li>
<p><code>@MockBean private StorageService storageService</code>: <code>StorageService</code>의 Mock 객체를 주입받습니다. 이는 실제 파일 저장소를 대체하여 테스트에서 사용됩니다.</p>
</li>
<li>
<p><code>shouldListAllFiles()</code>: 특정 경로에서 파일 목록을 불러오는지 테스트하는 메서드입니다. <code>given</code>을 통해 <code>storageService.loadAll()</code>이 호출되면 몇 가지 파일을 반환하도록 설정하고, 이를 테스트합니다.</p>
</li>
<li>
<p><code>shouldSaveUploadedFile()</code>: 업로드된 파일을 저장하는지 확인하는 메서드입니다. <code>MockMultipartFile</code>을 사용하여 가짜 파일을 만들고, 해당 파일을 업로드하고 저장되었는지 확인합니다.</p>
</li>
<li>
<p><code>should404WhenMissingFile()</code>: 파일이 없을 때 404 응답을 반환하는지 테스트합니다. 특정 파일을 요청했을 때 <code>StorageFileNotFoundException</code>을 던지도록 Mock 객체를 설정하고, 이에 대한 404 응답을 테스트합니다.</p>
</li>
</ol>
<h3 id="fileuploadintegrationtests">FileUploadIntegrationTEsts</h3>
<p>통합 테스트의 예는 <code>FileUploadIntegrationTests</code> 클래스(src/test/java/guides/uploadingfiles에 있음)를 참조하세요.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>uploadingfiles</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>junit<span class="token punctuation">.</span>jupiter<span class="token punctuation">.</span>api</span><span class="token punctuation">.</span><span class="token class-name">Test</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Autowired</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>context</span><span class="token punctuation">.</span><span class="token class-name">SpringBootTest</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>mock<span class="token punctuation">.</span>mockito</span><span class="token punctuation">.</span><span class="token class-name">MockBean</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>client</span><span class="token punctuation">.</span><span class="token class-name">TestRestTemplate</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>server</span><span class="token punctuation">.</span><span class="token class-name">LocalServerPort</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>core<span class="token punctuation">.</span>io</span><span class="token punctuation">.</span><span class="token class-name">ClassPathResource</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>http</span><span class="token punctuation">.</span><span class="token class-name">HttpHeaders</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>http</span><span class="token punctuation">.</span><span class="token class-name">HttpStatus</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>http</span><span class="token punctuation">.</span><span class="token class-name">ResponseEntity</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>util</span><span class="token punctuation">.</span><span class="token class-name">LinkedMultiValueMap</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>util</span><span class="token punctuation">.</span><span class="token class-name">MultiValueMap</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>multipart</span><span class="token punctuation">.</span><span class="token class-name">MultipartFile</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>assertj<span class="token punctuation">.</span>core<span class="token punctuation">.</span>api</span><span class="token punctuation">.</span><span class="token class-name">Assertions</span><span class="token punctuation">.</span>assertThat<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>mockito</span><span class="token punctuation">.</span><span class="token class-name">BDDMockito</span><span class="token punctuation">.</span>given<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>mockito</span><span class="token punctuation">.</span><span class="token class-name">BDDMockito</span><span class="token punctuation">.</span>then<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>mockito</span><span class="token punctuation">.</span><span class="token class-name">ArgumentMatchers</span><span class="token punctuation">.</span>any<span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">guides<span class="token punctuation">.</span>uploadingfiles<span class="token punctuation">.</span>storage</span><span class="token punctuation">.</span><span class="token class-name">StorageService</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootTest</span><span class="token punctuation">(</span>webEnvironment <span class="token operator">=</span> <span class="token class-name">SpringBootTest</span><span class="token punctuation">.</span><span class="token class-name">WebEnvironment</span><span class="token punctuation">.</span>RANDOM_PORT<span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">FileUploadIntegrationTests</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> <span class="token class-name">TestRestTemplate</span> restTemplate<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@MockBean</span>
    <span class="token keyword">private</span> <span class="token class-name">StorageService</span> storageService<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@LocalServerPort</span>
    <span class="token keyword">private</span> <span class="token keyword">int</span> port<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">shouldUploadFile</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
        <span class="token class-name">ClassPathResource</span> resource <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ClassPathResource</span><span class="token punctuation">(</span><span class="token string">"testupload.txt"</span><span class="token punctuation">,</span> <span class="token function">getClass</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token class-name">MultiValueMap</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">Object</span><span class="token punctuation">&gt;</span></span> map <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">LinkedMultiValueMap</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">Object</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        map<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token string">"file"</span><span class="token punctuation">,</span> resource<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">ResponseEntity</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> response <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">.</span>restTemplate<span class="token punctuation">.</span><span class="token function">postForEntity</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">,</span> map<span class="token punctuation">,</span>
                <span class="token class-name">String</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token function">assertThat</span><span class="token punctuation">(</span>response<span class="token punctuation">.</span><span class="token function">getStatusCode</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEqualTo</span><span class="token punctuation">(</span><span class="token class-name">HttpStatus</span><span class="token punctuation">.</span>FOUND<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token function">assertThat</span><span class="token punctuation">(</span>response<span class="token punctuation">.</span><span class="token function">getHeaders</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getLocation</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">startsWith</span><span class="token punctuation">(</span><span class="token string">"http://localhost:"</span> <span class="token operator">+</span> <span class="token keyword">this</span><span class="token punctuation">.</span>port <span class="token operator">+</span> <span class="token string">"/"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token function">then</span><span class="token punctuation">(</span>storageService<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">should</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">store</span><span class="token punctuation">(</span><span class="token function">any</span><span class="token punctuation">(</span><span class="token class-name">MultipartFile</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">shouldDownloadFile</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
        <span class="token class-name">ClassPathResource</span> resource <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ClassPathResource</span><span class="token punctuation">(</span><span class="token string">"testupload.txt"</span><span class="token punctuation">,</span> <span class="token function">getClass</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token function">given</span><span class="token punctuation">(</span><span class="token keyword">this</span><span class="token punctuation">.</span>storageService<span class="token punctuation">.</span><span class="token function">loadAsResource</span><span class="token punctuation">(</span><span class="token string">"testupload.txt"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">willReturn</span><span class="token punctuation">(</span>resource<span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token class-name">ResponseEntity</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> response <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">.</span>restTemplate
                <span class="token punctuation">.</span><span class="token function">getForEntity</span><span class="token punctuation">(</span><span class="token string">"/files/{filename}"</span><span class="token punctuation">,</span> <span class="token class-name">String</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> <span class="token string">"testupload.txt"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token function">assertThat</span><span class="token punctuation">(</span>response<span class="token punctuation">.</span><span class="token function">getStatusCodeValue</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEqualTo</span><span class="token punctuation">(</span><span class="token number">200</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token function">assertThat</span><span class="token punctuation">(</span>response<span class="token punctuation">.</span><span class="token function">getHeaders</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getFirst</span><span class="token punctuation">(</span><span class="token class-name">HttpHeaders</span><span class="token punctuation">.</span>CONTENT_DISPOSITION<span class="token punctuation">)</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">isEqualTo</span><span class="token punctuation">(</span><span class="token string">"attachment; filename=\"testupload.txt\""</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token function">assertThat</span><span class="token punctuation">(</span>response<span class="token punctuation">.</span><span class="token function">getBody</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEqualTo</span><span class="token punctuation">(</span><span class="token string">"Spring Framework"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p>파일 업로드를 수신하는 서버 측 부분을 실행합니다. 로깅 출력이 표시됩니다. 서비스는 몇 초 내에 시작되어 실행되어야 합니다.</p>
<p>서버가 실행 중인 상태에서 브라우저를 열고 <code>http://localhost:8080/</code>을 방문하여 업로드 양식을 확인해야 합니다. (작은) 파일을 선택하고 업로드를 누르세요. 컨트롤러에서 성공 페이지가 표시되어야 합니다. 너무 큰 파일을 선택하면 보기 흉한 오류 페이지가 표시됩니다.</p>
<p>그러면 브라우저 창에 다음과 유사한 줄이 표시됩니다.</p>
<p>“&lt;파일 이름&gt;을(를) 성공적으로 업로드했습니다!”</p>