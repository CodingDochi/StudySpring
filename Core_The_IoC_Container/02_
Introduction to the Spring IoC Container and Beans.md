<p>이 장에서는 Spring Framework가 구현하는 <span style="background-color:#fff5b1">Inversion of Control(IoC)</span> 원칙을 다룹니다. IoC는 종속성 주입(DI)이라고도 합니다. 객체가 <span style="background-color:#ffdce0">생성자 인수, 팩터리 메서드에 대한 인수</span> 또는 객체 인스턴스가 생성되거나 팩터리 메서드에서 반환된 후 <span style="background-color:#ffdce0">인스턴스에 설정된 속성</span>을 통해서만 <span style="background-color:#dcffe4">객체의 종속성(즉, 상호 작용하는 다른 객체)을 정의하는 프로세스</span>입니다. </p>
<p>그런 다음 <span style="background-color:#fff5b1">컨테이너는 빈(Bean)을 생성할 때 이러한 종속성을 주입</span>합니다. 이 프로세스는 기본적으로 <span style="background-color:#ffdce0">Service Locator 패턴</span>과 같은 메커니즘을 사용하거나 <span style="background-color:#ffdce0">클래스를 직접 생성</span>하여 종속성의 초기화(instantiation) 또는 배치(location)를 제어하는 빈 자체를 뒤집는 것(Inversion of Control)입니다.</p>
<hr>
<p><strong> 팩토리 메서드 패턴 </strong></p>
<p>  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/a/a3/FactoryMethod.svg/800px-FactoryMethod.svg.png"></p>
<p>Factory method는 부모(상위) 클래스에 알려지지 않은 구체 클래스를 생성하는 패턴이며. 자식(하위) 클래스가 어떤 객체를 생성할지를 결정하도록 하는 패턴이기도 하다. 부모(상위) 클래스 코드에 구체 클래스 이름을 감추기 위한 방법으로도 사용한다.  </p>
<p><strong> 서비스 로케이터 패턴 </strong><br>
<img src="https://springframework.guru/wp-content/uploads/2019/05/Service-Locator-Pattern-using-Spring-768x509.png"></p>
<p>서비스 로케이터 뒤에 있는 기본 아이디어는 애플리케이션에 필요할 수 있는 모든 서비스를 보유하는 방법을 아는 개체를 갖는 것입니다. 따라서 이 애플리케이션의 서비스 로케이터에는 필요할 때 '서비스'를 반환하는 메서드가 있습니다.  </p>
<ul>
<li>클라이언트 : 런타임에서 서비스가 필요한 소비자(Consumer).</li>
<li>서비스 로케이터 : 서비스 로케이터는 클라이언트에게 필요한 서비스를 요청 시 제공합니다. 서비스의 조회 또는 생성을 추상화합니다.</li>
<li>초기 컨텍스트 : 서비스를 생성하고 등록하며 캐시합니다. 이는 조회 및 생성의 시작점입니다.</li>
<li>서비스 팩토리 : 서비스 팩토리는 서비스의 라이프사이클 관리를 제공하며 서비스의 생성, 조회 또는 제거를 지원합니다.</li>
<li>서비스 : 클라이언트가 원하는 서비스의 구체적인 구현체입니다.</li>
</ul>
<hr>
<p><code>org.springframework.beans</code> 및 <code>org.springframework.context</code> 패키지는 <span style="background-color:#fff5b1">Spring Framework의 IoC 컨테이너의 기반</span>입니다. <code>BeanFactory</code> 인터페이스는 모든 유형의 객체를 관리할 수 있는 고급 구성(configuration) 메커니즘을 제공합니다. <code>ApplicationContext</code>는 <code>BeanFactory</code>의 하위 인터페이스이며 다음을 추가합니다.</p>
<ul>
<li>Spring의 AOP 기능과 더 쉬운 통합(integration)</li>
<li>메시지 리소스 처리(internationalization 목적)</li>
<li>이벤트 게시(publication)</li>
<li><code>WebApplicationContext</code>처럼 웹 어플리케이션 레이어에 특정 컨텍스트 제공</li>
</ul>
<p><code>BeanFactory</code>는 <span style="background-color:#dcffe4">configuration 프레임워크와 기본 기능</span>을 제공하고, <code>ApplicationContext</code>는 <span style="background-color:#dcffe4">더 많은 기능을 추가</span>합니다. <code>ApplicationContext</code>는 <code>BeanFactory</code>의 완전한 상위 집합이며 Spring IoC를 설명하기 위해 사용됩니다. </p>
<p>Spring에서 <span style="background-color:#dcffe4">애플리케이션의 백본을 형성</span>하고 <span style="background-color:#dcffe4">Spring IoC 컨테이너에 의해 관리되는 객체</span>를 <span style="background-color:#fff5b1">빈</span>이라고 합니다. Bean은 Spring IoC 컨테이너에 의해 인스턴스화, 조립 및 관리되는 객체입니다. 그렇지 않으면 빈은 <span style="background-color:#fff5b1">애플리케이션의 많은 객체 중 하나</span>일 뿐입니다. Bean과 이들 간의 종속성은 <span style="background-color:#ffdce0">컨테이너에서 사용하는 구성 메타데이터에 반영</span>됩니다.</p>
<hr>
<p><strong>자바 빈(Java Bean)</strong><br>
자바 빈(JavaBean)은 1996년에 도입된 개념입니다. 자바 빈은 자바 컴포넌트의 재사용성을 촉진하기 위해 썬 마이크로시스템즈(Sun Microsystems)에서 개발되었습니다.</p>
<p>자바 빈은 일종의 규칙이나 컨벤션으로, 특정한 이름 규칙과 메서드 규칙을 따르는 자바 클래스를 가리킵니다. 일반적으로 자바 빈은 다음과 같은 특징을 가집니다:</p>
<ol>
<li>클래스의 속성을 <code>private</code>으로 선언하고, <code>getter</code>와 <code>setter</code> 메서드를 제공하여 속성에 접근할 수 있도록 합니다.</li>
<li>디폴트 생성자(default constructor)를 제공합니다.</li>
<li><code>Serializable</code> 인터페이스를 구현하여 직렬화(serialization)를 지원합니다.</li>
<li>자바 빈의 속성(property)은 CamelCase 네이밍 규칙을 따릅니다.</li>
</ol>
<p><strong> 스프링 빈과 자바 빈의 차이 </strong></p>
<ul>
<li>
<p>자바 빈은 자바 클래스의 한 형태를 가리키는 것으로, 특정한 이름 규칙과 메서드 규칙을 따르는 일반적인 자바 클래스를 의미합니다. 주로 UI 컴포넌트나 데이터 모델 등을 캡슐화하는 데 사용됩니다. 자바 빈은 단순히 클래스의 형태를 말하며, 그 자체로 특별한 기능을 가지고 있지는 않습니다.</p>
</li>
<li>
<p>스프링에서의 "빈(Bean)"은 스프링 프레임워크의 핵심 개념 중 하나입니다. 스프링 빈은 스프링 컨테이너에 의해 생성, 관리되는 객체를 가리킵니다. 스프링 빈은 스프링 컨테이너에 등록되어 의존성 주입(Dependency Injection)과 라이프사이클 관리 등의 기능을 수행합니다. 스프링 빈은 일반적으로 자바 빈의 형태를 가지고 있지만, 스프링의 컨테이너에서 관리되는 특별한 역할을 수행합니다.</p>
</li>
</ul>
<p><strong> 스프링이 XML을 사용한 이유 </strong></p>
<ol>
<li>
<p><strong>설정의 분리</strong>: XML을 사용하면 애플리케이션의 설정(데이터) 정보를 외부 파일로 분리하여 관리할 수 있으며, 변경이 필요할 때 손쉽게 수정할 수 있습니다. 이는 유연성과 유지보수성을 향상시키는 데 도움이 됩니다.</p>
</li>
<li>
<p><strong>플러그인 아키텍처</strong>: XML을 사용하면 스프링 애플리케이션의 다양한 기능을 플러그인 형태로 추가할 수 있습니다. 스프링은 XML을 사용하여 AOP(Aspect-Oriented Programming), 트랜잭션 관리, 보안 설정 등의 다양한 기능을 쉽게 구성할 수 있습니다.</p>
</li>
</ol>
<hr>
<p><img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&amp;fname=http%3A%2F%2Fcfile30.uf.tistory.com%2Fimage%2F999BF0365A534021248CEE"></p>
<hr>
<ol start="3">
<li>
<p><strong>자바 코드와의 분리</strong>: XML을 사용하면 자바 코드(로직)와 애플리케이션의 설정(데이터)을 명확하게 분리할 수 있습니다. 이를 통해 설정 변경이 필요할 때 자바 코드를 수정하지 않고도 애플리케이션의 동작을 수정할 수 있습니다. 또한, XML을 사용하면 동일한 애플리케이션을 다양한 환경에서 쉽게 재구성할 수 있습니다.</p>
</li>
<li>
<p><strong>기존 기술과의 호환성</strong>: 스프링이 처음 출시된 시기에는 XML이 널리 사용되는 기술이었습니다. 따라서 XML을 사용하여 스프링을 개발하는 것은 기존의 개발자들이 쉽게 적응할 수 있도록 하고, 다른 기술과의 통합을 용이하게 할 수 있었습니다.</p>
</li>
</ol>
<p>그러나 현재는 XML 대신에 자바 기반의 설정을 선호하는 추세가 있습니다. 스프링 3.0부터는 자바 기반의 설정인 JavaConfig가 도입되었고, 스프링 부트(Spring Boot)와 같은 최신 스프링 프로젝트에서는 XML 대신에 주로 자바 기반의 설정을 사용하는 경향이 있습니다. 이는 코드의 가독성과 유지보수성을 향상시키는 데 도움이 됩니다.</p>
<p><strong> XML 대신 자바 기반 설정 선호 이유 </strong></p>
<p>지금은 XML 대신 자바 기반의 설정을 선호하는 이유는 몇 가지가 있습니다:</p>
<ol>
<li>
<p><strong>가독성</strong>: 자바 코드는 XML보다 가독성이 높습니다. 코드의 구조와 의도를 명확하게 표현할 수 있으며, IDE의 지원을 받을 수 있어서 개발자들이 코드를 이해하고 유지보수하는 데 도움이 됩니다.</p>
</li>
<li>
<p><strong>타입 안정성</strong>: 자바 코드는 컴파일 시에 타입 안정성을 보장받을 수 있습니다. XML은 런타임 시에 오류가 발생할 수 있으며, 이를 방지하기 위해서는 추가적인 검증이 필요합니다.</p>
</li>
<li>
<p><strong>코드 중복 최소화</strong>: 자바 기반의 설정은 메서드 호출과 상속을 통해 설정을 재사용할 수 있습니다. 이를 통해 설정의 중복을 최소화하고 코드의 재사용성을 높일 수 있습니다.</p>
</li>
<li>
<p><strong>프로그래밍 언어의 강력한 기능 활용</strong>: 자바 기반의 설정은 프로그래밍 언어의 강력한 기능을 활용할 수 있습니다. 예를 들어, 조건문, 반복문, 변수 등을 사용하여 동적인 설정을 구성할 수 있습니다.</p>
</li>
<li>
<p><strong>통합 개발 환경의 지원</strong>: 대부분의 통합 개발 환경(IDE)은 자바 코드를 지원하고, 코드 자동 완성, 리팩토링, 디버깅 등의 기능을 제공합니다. 이러한 기능을 통해 개발자는 코드를 더욱 효율적으로 작성할 수 있습니다.</p>
</li>
</ol>
<p><strong> XML DTD, DOM, Schema </strong></p>
<p><img src="https://www.researchgate.net/profile/Husam-Alhamad/publication/321038843/figure/fig1/AS:560327970103298@1510604006192/A-GENERAL-ARCHITECTURE-OF-XML-DTD-AND-XML-DOM-APPROACH.png"> <img src="https://www.researchgate.net/profile/Husam-Alhamad/publication/321038843/figure/fig2/AS:560327970754560@1510604006225/A-GENERAL-ARCHITECTURE-OF-CLUSTERING-AND-MATCHING-APPROACH.png"><img src="https://www.researchgate.net/profile/Husam-Alhamad/publication/321038843/figure/fig3/AS:560327969263616@1510604006267/A-GENERAL-ARCHITECTURE-OF-QUERY-APPROACH.png"><img src="https://www.researchgate.net/profile/Husam-Alhamad/publication/321038843/figure/fig4/AS:670388887494658@1536844574039/A-GENERAL-ARCHITECTURE-OF-RELATIONAL-STORAGE-APPROACH.png"><img src="https://www.researchgate.net/profile/Husam-Alhamad/publication/321038843/figure/fig5/AS:670388887490560@1536844574143/A-GENERAL-ARCHITECTURE-OF-CATALOG-BASED-APPROACH.png"></p>
<ol>
<li>
<p><strong>XML DTD (Document Type Definition)</strong>:</p>
<ul>
<li>XML DTD는 XML 문서의 구조와 유효성을 정의하기 위한 형식입니다. DTD는 XML 문서가 어떻게 구성되어야 하는지를 정의하고, 문서가 유효한지를 검사하기 위한 규칙을 제공합니다.</li>
<li>DTD는 XML 문서의 요소(element), 속성(attribute), 그리고 그들 간의 관계를 정의합니다. 예를 들어, 특정 요소가 반드시 포함되어야 하는지, 몇 번 이상 나타날 수 있는지 등을 정의할 수 있습니다.</li>
<li>DTD는 일반적으로 XML 문서의 맨 처음에 선언되며, XML 파서는 이를 사용하여 문서의 유효성을 검사합니다.</li>
</ul>
</li>
<li>
<p><strong>XML DOM (Document Object Model)</strong>:</p>
<ul>
<li>XML DOM은 XML 문서를 트리 구조로 표현하는 방법을 정의한 인터페이스입니다. 이는 XML 문서를 메모리 상에서 표현하고 조작할 수 있는 방법을 제공합니다.</li>
<li>XML DOM은 XML 문서의 요소, 속성, 텍스트 등을 각각 노드(node)로 표현하고, 이러한 노드들 간의 관계를 트리 구조로 표현합니다. 이러한 트리 구조는 XML 문서의 계층적 구조를 반영합니다.</li>
<li>XML DOM을 사용하면 XML 문서를 프로그래밍적으로 읽고 쓰고 수정할 수 있습니다. 예를 들어, 특정 요소를 찾거나 새로운 요소를 추가하는 등의 작업을 할 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>XML Schema</strong>:</p>
<ul>
<li>XML Schema는 DTD보다 더 강력한 유효성 검증 도구로서 XML 문서의 구조와 형식을 정의합니다. XML Schema는 XML 문서의 요소, 속성, 그리고 그들 간의 관계를 보다 상세하게 정의할 수 있으며, 더 다양한 유형의 데이터를 지원합니다. XML Schema를 사용하면 정확한 데이터 타입, 값 범위, 제약 조건 등을 정의하여 XML 문서의 유효성을 검증할 수 있습니다.</li>
</ul>
</li>
</ol>
<p><strong> XML과 JSON의 차이 </strong></p>
<p>XML과 JSON은 모두 데이터를 표현하고 전송하는 데 사용되는 형식이지만, 서로 다른 구조와 문법을 가지고 있습니다. 최근에는 JSON이 XML에 비해 더 간결하고 가벼운 형식이라는 점에서 인기를 얻고 있습니다.</p>
<ol>
<li>
<p><strong>구조</strong>:</p>
<ul>
<li>XML(Extensible Markup Language)은 텍스트 기반의 마크업 언어로, 데이터를 계층적인 트리 구조로 표현합니다. 각 요소는 시작 태그와 끝 태그로 둘러싸여 있으며, 중첩될 수 있습니다.</li>
<li>JSON(JavaScript Object Notation)은 자바스크립트의 객체 리터럴 형식을 따르며, 데이터를 키-값 쌍의 집합으로 표현합니다. 중괄호로 묶인 객체와 대괄호로 묶인 배열을 사용하여 구조를 표현합니다.</li>
</ul>
</li>
<li>
<p><strong>가독성</strong>:</p>
<ul>
<li>XML은 태그 기반의 구조를 사용하기 때문에 가독성이 좋고, 사람이 직접 읽고 이해하기 쉽습니다. 하지만 XML 문서의 구조가 복잡해질수록 가독성이 떨어질 수 있습니다.</li>
<li>JSON은 키-값 쌍을 사용하여 데이터를 표현하기 때문에 가독성이 좋습니다. 또한, 자바스크립트의 객체와 배열을 직접 사용하기 때문에 자바스크립트 코드와의 호환성이 뛰어나며, 다른 프로그래밍 언어에서도 쉽게 해석할 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>용도</strong>:</p>
<ul>
<li>XML은 다양한 종류의 데이터를 표현하고 전송하는 데 사용됩니다. 특히, 웹 서비스의 메시지 형식으로 많이 사용되며, SOAP(Simple Object Access Protocol)와 같은 프로토콜에서도 XML을 사용합니다.</li>
<li>JSON은 주로 웹 애플리케이션에서 데이터 교환을 위해 사용됩니다. AJAX(Asynchronous JavaScript and XML)와 같은 웹 기술에서는 서버와 비동기적으로 데이터를 교환하는 데 JSON을 주로 사용합니다.</li>
</ul>
</li>
</ol>
<p><strong> Groovy, XML, Spring 프레임워크 </strong></p>
<ol>
<li><strong>Groovy</strong></li>
</ol>
<ul>
<li>Groovy는 JVM(Java Virtual Machine) 상에서 실행되는 동적 프로그래밍 언어입니다. Java와의 호환성이 높고, 자바와 유사한 문법을 가지고 있어 Java 개발자들이 쉽게 배우고 사용할 수 있습니다. </li>
<li>Groovy는 스크립트 언어이면서도 객체지향 프로그래밍 언어의 특징을 가지고 있습니다.</li>
<li>Groovy는 자바와 마찬가지로 다양한 용도로 사용될 수 있습니다. 예를 들어, 웹 애플리케이션 개발, 스크립트 작성, 테스트 자동화, 데이터 처리 등에 활용될 수 있습니다.</li>
</ul>
<ol start="2">
<li><strong>Groovy, Spring 프레임워크</strong></li>
</ol>
<ul>
<li>Groovy는 XML과 함께 Spring 애플리케이션을 개발하는 데 사용될 수 있습니다. Groovy는 Java와의 상호 운용성이 높기 때문에, Spring에서 사용되는 XML 설정 파일을 Groovy로 대체하여 더 간결하고 유연한 설정을 작성할 수 있습니다.</li>
<li>Groovy는 Spring 프레임워크와의 통합을 위한 편리한 기능을 제공합니다. 예를 들어, Groovy는 Spock과 같은 테스트 프레임워크와 함께 사용되어 Spring 애플리케이션의 테스트를 더 쉽게 작성할 수 있습니다.</li>
</ul>