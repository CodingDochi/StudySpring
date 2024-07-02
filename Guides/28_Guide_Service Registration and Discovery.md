<p>이 가이드는 Netflix Eureka 서비스 레지스트리를 시작하고 사용하는 과정을 안내합니다.</p>
<h2 id="무엇을-구축할-것인가">무엇을 구축할 것인가</h2>
<p>Netflix Eureka 서비스 레지스트리를 설정한 다음 레지스트리에 자신을 등록하고 이를 사용하여 자체 호스트를 확인하는 클라이언트를 구축합니다. 서비스 레지스트리는 클라이언트 측 로드 밸런싱을 가능하게 하고 DNS 없이 서비스 공급자를 소비자로부터 분리시키기 때문에 유용합니다.</p>
<h2 id="서비스">서비스</h2>
<h3 id="서비스-디스커버리에서의-서비스란">서비스 디스커버리에서의 '서비스'란?</h3>
<ol>
<li>
<p><strong>네트워크 상의 주소</strong>:</p>
<ul>
<li>서비스 디스커버리에서의 서비스는 IP 주소나 도메인 주소와 연결되어 있습니다. 다른 서비스들이 해당 서비스를 찾고 통신하기 위해 필요한 주소 정보를 가지고 있습니다.</li>
</ul>
</li>
<li>
<p><strong>독립성</strong>:</p>
<ul>
<li>서비스는 독립적으로 배포, 실행되며 특정 기능이나 업무를 수행합니다. 이 서비스들은 서로 분리되어 있을 수 있고, 서로 다른 팀 또는 레파지토리에서 관리될 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>마이크로서비스 아키텍처의 구성 요소</strong>:</p>
<ul>
<li>서비스 디스커버리는 주로 마이크로서비스 아키텍처에서 사용됩니다. 여러 마이크로서비스들이 전체 시스템을 구성하며, 각각의 서비스는 특정 기능을 담당합니다.</li>
</ul>
</li>
</ol>
<h3 id="서비스-애플리케이션과-인스턴스">서비스 애플리케이션과 인스턴스</h3>
<ul>
<li>
<p>하나의 서비스 애플리케이션은 논리적으로는 하나의 애플리케이션이지만, 이 애플리케이션은 여러 개의 인스턴스로 실행될 수 있습니다.</p>
</li>
<li>
<p>각 인스턴스는 같은 서비스를 나타내는 하나의 도메인 또는 이름을 공유하지만, 각각의 인스턴스는 다른 IP 주소나 포트를 할당받아 실행됩니다. </p>
<ul>
<li>하나의 서비스에 대해 여러 개의 인스턴스가 있을 때, 로드 밸런싱이나 부하 분산을 통해 트래픽을 이들 인스턴스 사이에서 분배하게 됩니다.</li>
</ul>
</li>
<li>
<p>보통 서버의 인스턴스를 스케일 아웃(Scale-Out)하는 경우, 새로운 인스턴스를 생성하여 같은 애플리케이션 또는 서비스를 복제합니다. 이러한 복제는 일반적으로 독립적인 메모리 공간과 자원을 갖습니다.</p>
</li>
<li>
<p>각각의 복제된 인스턴스는 동일한 애플리케이션 코드를 실행하며, 요청을 병렬로 처리합니다. 이들은 분산된 트래픽을 처리하고 서비스의 확장성을 향상시키는 데 사용됩니다.</p>
</li>
</ul>
<h3 id="서비스-위치-관련-정보">서비스 위치 관련 정보</h3>
<p>서비스 위치 변경이 발생할 때, 변경되는 것은 주로 다음과 같은 정보일 수 있습니다:</p>
<ol>
<li>
<p><strong>IP 주소</strong>:</p>
<ul>
<li>서비스의 인스턴스가 호스트하는 실제 위치를 나타내는데, IP 주소가 변경될 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>포트 번호</strong>:</p>
<ul>
<li>서비스가 리스닝(listening)하고 있는 포트 번호도 변경될 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>도메인 이름</strong>:</p>
<ul>
<li>서비스의 위치를 가리키는 도메인 이름이 변경될 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>서비스 엔드포인트 URI</strong>:</p>
<ul>
<li>서비스의 위치를 고유하게 식별하는 URI가 변경될 수 있습니다. 이 URI에는 서비스의 프로토콜, 호스트, 포트, 경로 등의 정보가 포함될 수 있습니다.</li>
</ul>
</li>
</ol>
<h3 id="서비스-위치-변경-사유">서비스 위치 변경 사유</h3>
<p>서비스 위치는 동적으로 변경될 수 있습니다. 마이크로서비스 아키텍처에서는 서비스의 동적인 확장과 축소, 장애 처리, 새로운 배포 등의 요구 사항을 충족하기 위해 서비스 위치의 변경이 필요할 수 있습니다. 이러한 변경 사항에 유연하게 대응하기 위해 서비스 디스커버리와 같은 기술이 사용됩니다.</p>
<ol>
<li>
<p><strong>스케일 업/다운</strong>:</p>
<ul>
<li>트래픽이 증가하거나 감소할 때, 서비스 인스턴스의 스케일을 업 또는 다운할 수 있습니다. 즉, 더 많은 인스턴스를 추가하거나 일부 인스턴스를 줄일 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>장애 처리 및 복구</strong>:</p>
<ul>
<li>서비스 인스턴스의 장애가 발생하면 해당 인스턴스를 복구하거나 새로운 인스턴스를 생성하여 서비스의 가용성을 유지하려고 할 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>새로운 배포</strong>:</p>
<ul>
<li>애플리케이션의 새로운 버전이 출시될 때, 새로운 서비스 인스턴스를 추가하고 이전 버전의 인스턴스를 제거할 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>로드 밸런싱</strong>:</p>
<ul>
<li>트래픽을 고르게 분산하기 위해 서비스 인스턴스의 위치를 변경하거나 새로운 인스턴스를 추가할 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>자원 최적화</strong>:</p>
<ul>
<li>특정 지역 또는 클라우드 영역에서 자원을 최적화하거나 비용을 절감하기 위해 서비스 인스턴스를 이동할 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>운영 정책 변경</strong>:</p>
<ul>
<li>보안, 네트워크 구성, 데이터 처리 방식 등 운영 정책의 변경으로 인해 서비스 위치가 조정될 수 있습니다.</li>
</ul>
</li>
</ol>
<h3 id="서비스-디스커버리나-레지스트리-없이-마이크로서비스-아키텍처-구현">서비스 디스커버리나 레지스트리 없이 마이크로서비스 아키텍처 구현</h3>
<ol>
<li>
<p><strong>고정된 위치 또는 설정 파일</strong>:</p>
<ul>
<li>서비스들의 위치를 정적으로 설정 파일에 기록하거나, 고정된 주소를 사용하여 서비스들을 찾아야 했습니다.</li>
<li>이 방식은 서비스의 위치가 변경되거나 새로운 인스턴스가 추가될 때 업데이트하기 어렵고 유연성이 떨어졌습니다.</li>
</ul>
</li>
<li>
<p><strong>프록시 서비스</strong>:</p>
<ul>
<li>인터페이스를 통해 서비스에 접근하는 대신, 프록시 서비스를 통해 요청을 라우팅했습니다. 이 프록시 서비스는 서비스 위치를 알고 있어야 했습니다.</li>
<li>이 방식은 분산 환경에서도 서비스 호출을 중재하고 라우팅할 수 있었지만, 서비스 위치 변경에 대응하기 어려웠습니다.</li>
</ul>
</li>
</ol>
<p>이러한 방식은 유연성과 확장성 면에서 한계가 있었기 때문에, 서비스 디스커버리와 레지스트리가 등장하면서 마이크로서비스 아키텍처를 보다 효율적으로 관리할 수 있게 되었습니다. 서비스 디스커버리와 레지스트리는 동적인 환경에서 서비스들을 발견하고 관리하는 기능을 제공하여, 서비스들 간의 통신을 유연하고 안정적으로 만들어주었습니다.</p>
<h3 id="인프라스트럭쳐-레이어와-미들웨어">인프라스트럭쳐 레이어와 미들웨어</h3>
<p>간단하게 말하면, 인프라스트럭처 레이어는 하드웨어 및 네트워크와 같은 기본적인 시스템 리소스와 관련된 부분을 다루는 데 비해, 미들웨어는 서버 애플리케이션 내에서 요청 처리의 중간에 위치하여 다양한 기능을 제공하고 처리합니다</p>
<ul>
<li>인프라스트럭처 레이어:</li>
</ul>
<p>인프라스트럭처 레이어는 애플리케이션의 기반이 되는 하부 구조를 말합니다. 이 레이어에서는 서버의 하드웨어, 네트워크, 운영체제, 데이터베이스 등과 같은 기본적인 시스템 리소스 및 솔루션을 다룹니다.<br>
주로 하드웨어와 네트워크 등 물리적인 요소와 관련된 부분을 다루며, 서버의 운영, 보안, 네트워크 설정, 데이터베이스 관리 등이 여기에 속합니다.</p>
<ul>
<li>미들웨어:</li>
</ul>
<p>미들웨어는 애플리케이션의 다른 구성 요소들 간에 연결 역할을 수행하는 소프트웨어입니다. 주로 서버 애플리케이션 내에서 요청을 처리하는 중간에 위치하여 다양한 기능을 제공합니다.<br>
이는 요청을 가로채고 변형하거나, 로깅, 보안, 인증, 세션 관리, 캐싱, 메시지 큐, 트랜잭션 관리 등의 기능을 수행하는데 사용됩니다.</p>
<h3 id="eureka-서버-설정-애플리케이션">Eureka 서버 설정 애플리케이션</h3>
<p><code>@EnableEurekaServer</code> 어노테이션을 사용하여 Eureka 서버로 설정하고, 이 서버는 다른 서비스들의 등록과 검색을 담당합니다. 즉, 이 서버는 서비스 디스커버리의 역할을 수행하며, 다른 서비스들이 여기에 자신들의 정보를 등록하고 필요할 때 이 정보를 검색할 수 있게 해줍니다.</p>
<h3 id="eureka-클라이언트-설정-애플리케이션">Eureka 클라이언트 설정 애플리케이션</h3>
<p>이 애플리케이션은 서비스 디스커버리에 등록되어야 하는 서비스입니다. Eureka 클라이언트는 Eureka 서버에 자신의 정보를 등록하고, 다른 서비스들의 정보를 필요로 할 때 Eureka 서버로부터 해당 정보를 검색합니다. 클라이언트는 서비스 디스커버리를 통해 다른 서비스들을 찾거나 통신하기 위한 역할을 합니다.</p>
<h3 id="속성값-설정">속성값 설정</h3>
<h4 id="eureka-service">eureka-service</h4>
<ul>
<li><strong><code>server.port</code></strong>: Eureka 서비스 애플리케이션의 포트를 8761로 설정합니다.</li>
<li><strong><code>eureka.client.register-with-eureka</code></strong>: Eureka 서버에 이 애플리케이션을 등록하지 않도록 설정합니다.</li>
<li><strong><code>eureka.client.fetch-registry</code></strong>: Eureka 서버로부터 다른 서비스들의 정보를 가져오지 않도록 설정합니다.</li>
<li><strong><code>logging.level.com.netflix.eureka</code> 및 <code>logging.level.com.netflix.discovery</code></strong>: Eureka와 Discovery 모듈의 로깅을 끕니다.</li>
</ul>
<h4 id="eureka-client">eureka-client</h4>
<ul>
<li><strong><code>spring.application.name</code></strong>: 이 클라이언트 애플리케이션의 이름을 "a-bootiful-client"로 설정합니다.</li>
</ul>
<h3 id="속성값-설정과-코드에-대한-의문">속성값 설정과 코드에 대한 의문</h3>
<p><code>eureka-service</code>의 설정에서는 Eureka 서버에 이 애플리케이션을 등록하지 않도록 설정되어 있고, 또한 Eureka 서버로부터 다른 서비스들의 정보를 가져오지 않도록 설정되어 있습니다. </p>
<p>일반적으로 <code>eureka-client</code>가 <code>eureka-service</code>에서 서비스 인스턴스 정보를 가져오려고 할 때, 설정된 대로라면 해당 정보를 가져오지 못하고 에러가 발생해야 합니다. </p>
<p>그러나 <code>eureka-service</code> 실행 후 로그를 보면 <code>eureka-client</code>는 <code>a-bootiful-client</code>를 Eureka 서버에 정상적으로 등록하고 있습니다. 그리고 <code>eureka-client</code>는 Eureka 서버에서 등록된 서비스 인스턴스 정보를 가져오고 있습니다.</p>
<p>그 이유는 Spring Cloud Netflix Eureka가 기본적으로 Eureka 서버에 대한 설정을 가지고 있기 때문입니다. Spring Cloud Netflix Eureka는 자체적으로 서버에 등록된 서비스 인스턴스를 가져오는 동작을 수행하며, 이는 <code>eureka-client</code>의 실행에 반영됩니다. </p>
<p><code>eureka-client</code>에 <code>@EnableEurekaClient</code> 어노테이션이 없더라도 Spring Cloud Netflix Eureka의 자동 구성 기능을 통해 클래스패스 상에 해당 라이브러리가 존재하고 Eureka 서버의 URL이 설정되어 있다면, 애플리케이션은 Eureka 서버에 자동으로 등록될 수 있습니다.</p>
<p><code>eureka-client</code>가 자체적으로 Eureka 서버에 등록 및 검색을 수행하는 것이 아니라, Spring Cloud Netflix Eureka가 해당 역할을 수행하고 있는 셈입니다. </p>
<h2 id="spring-초기화로-시작하기">Spring 초기화로 시작하기</h2>
<p>이 가이드에는 두 가지 애플리케이션이 필요합니다. 첫 번째 애플리케이션(서비스 애플리케이션)에는 Eureka Server 종속성만 필요합니다.</p>
<p>두 번째 애플리케이션(클라이언트 애플리케이션)에는 Eureka Server 및 Eureka Discovery Client 종속성이 필요합니다.</p>
<p><code>service-registration-and-discovery</code> 프로젝트를 생성한 후 <code>File - Project Structure</code> 메뉴를 선택하여 <code>eureka-service</code>, <code>eureka-client</code> 두 개의 모듈을 생성합니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/e9467b5d-7581-4c66-ac9f-a072b4a44ddc/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/85387046-653f-4218-bc06-81ed8d8c8211/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/8dedeb3e-8fbb-4dde-8277-9a0a5219d5d9/image.png"></p>
<h3 id="eureka-clientbuildgradle">eureka-client/build.gradle</h3>
<pre><code class="language-null">plugins {
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

ext {
	set('springCloudVersion', "2023.0.0")
}

dependencies {
	implementation 'org.springframework.cloud:spring-cloud-starter-netflix-eureka-client'
	implementation 'org.springframework.boot:spring-boot-starter-web'
//	implementation 'org.springframework.cloud:spring-cloud-starter-netflix-eureka-server'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

dependencyManagement {
	imports {
		mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
	}
}

tasks.named('test') {
	useJUnitPlatform()
}</code></pre>
<p>가이드 페이지에는 eureka-server를 의존성으로 추가하라 하지만 완성본에는 eureka-server 대신 spring-web을 추가하고 있으며 spring-web이 없으면 DiscoveryClient를 Autowire 할 수 없음. </p>
<h3 id="eureka-servicebuildgradle">eureka-service/build.gradle</h3>
<pre><code class="language-null">plugins {
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

ext {
	set('springCloudVersion', "2023.0.0")
}

dependencies {
	implementation 'org.springframework.cloud:spring-cloud-starter-netflix-eureka-server'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

dependencyManagement {
	imports {
		mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
	}
}

tasks.named('test') {
	useJUnitPlatform()
}</code></pre>
<h2 id="start-a-eureka-service-registry">Start a Eureka Service Registry</h2>
<p>먼저 Eureka Service 레지스트리가 필요합니다. Spring Cloud의 <code>@EnableEurekaServer</code>를 사용하여 다른 애플리케이션이 통신할 수 있는 레지스트리를 설정할 수 있습니다. 이는 서비스 레지스트리를 활성화하기 위해 하나의 주석(<code>@EnableEurekaServer</code>)이 추가된 일반 Spring Boot 애플리케이션입니다. 다음 목록(eureka-service/src/main/java/guides/eurekaservice/ServiceRegistrationAndDiscoveryServiceApplication.java에서)은 서비스 애플리케이션을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>eurekaservice</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>cloud<span class="token punctuation">.</span>netflix<span class="token punctuation">.</span>eureka<span class="token punctuation">.</span>server</span><span class="token punctuation">.</span><span class="token class-name">EnableEurekaServer</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@EnableEurekaServer</span>
<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ServiceRegistrationAndDiscoveryServiceApplication</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">ServiceRegistrationAndDiscoveryServiceApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

<span class="token punctuation">}</span>
</code></pre>
<p>레지스트리가 시작되면 레지스트리가 연결할 수 있는 복제본 노드가 없다고 스택 추적을 통해 불평합니다. 프로덕션 환경에서는 둘 이상의 레지스트리 인스턴스가 필요합니다. 그러나 간단한 목적을 위해서는 관련 로깅을 비활성화하는 것으로 충분합니다.</p>
<p>기본적으로 레지스트리도 자체 등록을 시도하므로 해당 동작도 비활성화해야 합니다.</p>
<p>이 레지스트리를 로컬로 사용할 때는 별도의 포트에 두는 것이 좋습니다.</p>
<p>다음 목록에 표시된 것처럼 이러한 모든 요구 사항을 처리하려면 <code>eureka-service/src/main/resources/application.properties</code>에 일부 속성을 추가하세요.</p>
<pre><code class="language-null">server.port=8761

eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false

logging.level.com.netflix.eureka=OFF
logging.level.com.netflix.discovery=OFF</code></pre>
<h2 id="talking-to-the-registry">Talking to the Registry</h2>
<p>이제 서비스 레지스트리를 시작했으므로 레지스트리에 자신을 등록하고 Spring Cloud <code>DiscoveryClient</code> 추상화를 사용하여 자체 호스트 및 포트에 대해 레지스트리를 조사하는 클라이언트를 시작할 수 있습니다. <code>@EnableDiscoveryClient</code>는 Netflix Eureka DiscoveryClient 구현을 활성화합니다. (Hashicorp의 Consul 또는 Apache Zookeeper와 같은 다른 서비스 레지스트리에 대한 다른 구현이 있습니다.) 다음 목록(eureka-client/src/main/java/guides/eurekaclient/ServiceRegistrationAndDiscoveryClientApplication.java에서)은 클라이언트 애플리케이션을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>eurekaclient</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util</span><span class="token punctuation">.</span><span class="token class-name">List</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Autowired</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>cloud<span class="token punctuation">.</span>client</span><span class="token punctuation">.</span><span class="token class-name">ServiceInstance</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>cloud<span class="token punctuation">.</span>client<span class="token punctuation">.</span>discovery</span><span class="token punctuation">.</span><span class="token class-name">DiscoveryClient</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">PathVariable</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RequestMapping</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RestController</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ServiceRegistrationAndDiscoveryClientApplication</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">ServiceRegistrationAndDiscoveryClientApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@RestController</span>
<span class="token keyword">class</span> <span class="token class-name">ServiceInstanceRestController</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token keyword">private</span> <span class="token class-name">DiscoveryClient</span> discoveryClient<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span><span class="token string">"/service-instances/{applicationName}"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">ServiceInstance</span><span class="token punctuation">&gt;</span></span> <span class="token function">serviceInstancesByApplicationName</span><span class="token punctuation">(</span>
			<span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">String</span> applicationName<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>discoveryClient<span class="token punctuation">.</span><span class="token function">getInstances</span><span class="token punctuation">(</span>applicationName<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>어떤 구현을 선택하든, <code>spring.application.name</code> 속성에 지정한 이름으로 등록된 <code>eureka-client</code>를 곧 볼 수 있습니다. 이 속성은 Spring Cloud에서 많이 사용되며, 서비스 구성의 초기 단계에서 자주 사용됩니다. 이 속성은 서비스 부트스트랩에 사용되므로 규칙에 따라 <code>src/main/resources/application.properties</code> 앞에 있는 <code>eureka-client/src/main/resources/bootstrap.properties</code>에 있습니다. 다음 목록은 <code>bootstrap.properties</code> 파일을 보여줍니다.</p>
<pre><code class="language-null">Unresolved directive in <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>stdin</span><span class="token punctuation">&gt;</span></span> - include::complete/eureka-client/src/main/resources/bootstrap.properties[]</code></pre>
<p><code>eureka-client</code>는 <code>http://localhost:8080/service-instances/a-bootiful-client</code>에 등록된 모든 <code>ServiceInstance</code> 인스턴스의 열거를 반환하는 Spring MVC REST 엔드포인트(<code>ServiceInstanceRestController</code>)를 정의합니다. Spring MVC 및 Spring Boot를 사용하여 REST 서비스를 구축하는 방법에 대해 자세히 알아보려면 RESTful 웹 서비스 구축 가이드를 참조하세요.</p>
<h2 id="애플리케이션-테스트">애플리케이션 테스트</h2>
<p>먼저 유레카 서비스를 시작한 다음 로드가 완료되면 유레카 클라이언트를 시작하여 엔드투엔드 결과를 테스트합니다.</p>
<p>Gradle을 사용하여 Eureka 서비스를 실행하려면 터미널 창(/complete 디렉터리)에서 다음 명령을 실행하세요.</p>
<pre><code class="language-null">./gradlew :eureka-service:bootRun</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/4bfcf429-d4df-4b6d-895d-fb84444b7989/image.png"><img src="https://velog.velcdn.com/images/dev_hammy/post/f1a141a8-2247-4a68-98a6-627b27213a90/image.png"></p>
<p>(인텔리제이에서는 서비스 패널을 이용해서 각각 실행 가능)</p>
<p>Gradle을 사용하여 Eureka 클라이언트를 실행하려면 터미널 창(/complete 디렉터리)에서 다음 명령을 실행하세요.</p>
<pre><code class="language-null">./gradlew :eureka-client:bootRun</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/c7c9a944-96f3-4f4e-825d-2d3ffa140c44/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/2861dbd4-cfa2-4126-8d07-767b8955f823/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/fdfc1c8f-39f8-4a58-9530-1a5684c3783d/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/8148c1c5-4d4a-4831-bd46-3f27f3baf680/image.png"></p>
<p><code>eureka-client</code>는 레지스트리에 등록하고 레지스트리에서 등록된 인스턴스의 자체 목록을 새로 고치는 데 약 1분 정도 걸립니다. 브라우저에서 <code>http://localhost:8080/service-instances/a-bootiful-client</code>에서 <code>eureka-client</code>를 방문하십시오. 거기에서 응답에 반영된 <code>eureka-client</code>에 대한 <code>ServiceInstance</code>를 볼 수 있습니다. 빈 <code>&lt;List&gt;</code> 요소가 보이면 잠시 기다렸다가 페이지를 새로 고치세요.</p>
<p>이 JSON 출력은 Eureka 서비스 레지스트리에 등록된 특정 서비스의 인스턴스 정보를 담고 있습니다. </p>
<pre><code class="language-json">[{"scheme":"http","host":"192.168.0.25","port":8080,"metadata":{"management.port":"8080"},"secure":false,"instanceId":"192.168.0.25:a-bootiful-client","uri":"http://192.168.0.25:8080","serviceId":"A-BOOTIFUL-CLIENT","instanceInfo":{"instanceId":"192.168.0.25:a-bootiful-client","app":"A-BOOTIFUL-CLIENT","appGroupName":null,"ipAddr":"192.168.0.25","sid":"na","homePageUrl":"http://192.168.0.25:8080/","statusPageUrl":"http://192.168.0.25:8080/actuator/info","healthCheckUrl":"http://192.168.0.25:8080/actuator/health","secureHealthCheckUrl":null,"vipAddress":"a-bootiful-client","secureVipAddress":"a-bootiful-client","countryId":1,"dataCenterInfo":{"@class":"com.netflix.appinfo.InstanceInfo$DefaultDataCenterInfo","name":"MyOwn"},"hostName":"192.168.0.25","status":"UP","overriddenStatus":"UNKNOWN","leaseInfo":{"renewalIntervalInSecs":30,"durationInSecs":90,"registrationTimestamp":1703395119716,"lastRenewalTimestamp":1703395239520,"evictionTimestamp":0,"serviceUpTimestamp":1703395119044},"isCoordinatingDiscoveryServer":false,"metadata":{"management.port":"8080"},"lastUpdatedTimestamp":1703395119716,"lastDirtyTimestamp":1703395118949,"actionType":"ADDED","asgName":null}}]</code></pre>
<p>여기에 포함된 정보:</p>
<ul>
<li><strong>scheme</strong>: 서비스에 대한 통신 프로토콜 (여기서는 HTTP)</li>
<li><strong>host</strong>: 서비스 호스트 IP 주소</li>
<li><strong>port</strong>: 서비스가 실행 중인 포트</li>
<li><strong>metadata</strong>: 서비스에 대한 메타데이터 (여기서는 관리 포트)</li>
<li><strong>instanceId</strong>: 서비스의 고유 인스턴스 식별자</li>
<li><strong>uri</strong>: 서비스의 기본 URI</li>
<li><strong>serviceId</strong>: 서비스의 고유 식별자</li>
<li><strong>instanceInfo</strong>: 서비스 인스턴스의 상세 정보<ul>
<li><strong>app</strong>: 서비스 애플리케이션 이름</li>
<li><strong>ipAddr</strong>: 서비스의 IP 주소</li>
<li><strong>status</strong>: 서비스의 상태 (여기서는 UP)</li>
<li><strong>statusPageUrl</strong>: 서비스 상태 페이지 URL</li>
<li><strong>healthCheckUrl</strong>: 서비스 헬스 체크 URL</li>
<li>그 외 다양한 정보들이 포함될 수 있습니다.</li>
</ul></li>
</ul>
<p>이 정보는 서비스 디스커버리를 통해 등록된 서비스의 상세한 정보를 제공합니다.</p>