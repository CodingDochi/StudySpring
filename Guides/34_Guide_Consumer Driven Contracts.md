<p>이 가이드는 계약 스텁(contract stubs)을 사용하여 Spring REST 애플리케이션을 생성하고 다른 Spring 애플리케이션 내에서 계약(contract)을 사용하는 프로세스를 안내합니다. </p>
<h2 id="contract와-contract-stub">Contract와 Contract Stub</h2>
<p>'Contract'와 'Contract Stub'은 소프트웨어 개발에서 API나 서비스 간의 상호 작용을 정의하고 모의(mocks) 또는 테스트를 위한 기준을 설정하는 데 사용되는 개념입니다.</p>
<ul>
<li>
<p><strong>Contract(계약)</strong>: 소프트웨어 컴포넌트 간의 상호 작용을 정의하는 일종의 규약이나 합의를 나타냅니다. 일반적으로 API 엔드포인트의 요청과 응답에 대한 형식, 데이터 유형, 동작, 예상되는 결과 등을 정의합니다. 이러한 계약은 클라이언트와 서버 사이의 상호 작용을 명확히하고, 각각의 역할 및 책임을 정의하여 개발 과정에서 오류를 줄이고 일관성을 유지하는 데 도움을 줍니다.</p>
</li>
<li>
<p><strong>Contract Stub(계약 스텁)</strong>: 실제 서비스를 대신하여 동작하는, 가짜 또는 목(mock) 객체로, 계약을 기반으로 한 테스트나 모의 환경을 구축할 때 사용됩니다. 이는 테스트를 위해 실제 API를 호출하는 대신에 특정 요청과 해당하는 응답을 제공하거나 시뮬레이트하여, 개발자가 특정 요청에 대한 응답을 테스트하는 데 도움을 줍니다. 주로 테스트 주도 개발(Test-Driven Development, TDD) 또는 테스트 작성 시 사용됩니다.</p>
</li>
</ul>
<p>이러한 계약과 계약 스텁은 개발자들이 서로의 코드를 테스트하거나 통합할 때, 각자가 개발한 코드가 다른 쪽과 기대한 대로 상호 작용하는지 확인하고자 할 때 유용합니다. 이를 통해 통합 문제를 미리 발견하고 해결하는 데 도움이 되며, 개발 프로세스에서 빠른 피드백을 얻을 수 있습니다.</p>
<h2 id="contract와-기타-개념-비교">Contract와 기타 개념 비교</h2>
<h3 id="interface-abstract-class-method-signature">interface, abstract class, method signature</h3>
<ol>
<li>
<p><strong>Interface(인터페이스)</strong>: Spring에서 Interface는 클래스가 구현해야 하는 메서드들의 명세를 정의하는데 사용됩니다. 이를 통해 클래스들은 해당 인터페이스를 구현함으로써 인터페이스에 선언된 메서드들을 갖게 됩니다.</p>
</li>
<li>
<p><strong>Abstract Class(추상 클래스)</strong>: Abstract Class는 일부 메서드의 구현을 포함할 수 있는 클래스입니다. 그러나 인터페이스와는 달리 추상 클래스는 일부 메서드를 구현할 수 있습니다.</p>
</li>
<li>
<p><strong>Method Signature(메서드 시그니처)</strong>: 메서드 시그니처는 메서드의 이름, 매개변수, 반환 유형 등을 정의하는 것을 말합니다. 이는 메서드의 기본적인 형태를 정의하는 것으로 실제로 메서드의 구현 내용은 포함하지 않습니다.</p>
</li>
<li>
<p><strong>Contract</strong>: Spring에서 Contract는 RESTful API나 마이크로서비스 등 서비스 간의 상호 작용을 정의합니다. 이는 요청과 응답의 형식, 데이터 유형, 엔드포인트, 동작 등을 명세화하여 클라이언트와 서버 사이의 계약을 나타냅니다. 이러한 계약은 코드 레벨의 구현보다는 외부 서비스와의 상호 작용을 설명하고 제어하기 위한 목적으로 사용됩니다.</p>
</li>
</ol>
<p>따라서 Contract는 코드 구현이나 클래스의 형태를 정의하는 것이 아니라, 서비스 간의 상호 작용을 정의하고 제어하기 위한 것으로, 인터페이스, 추상 클래스, 메서드 시그니처와는 목적과 사용 방법이 다릅니다.</p>
<h3 id="asciidoc-integration">asciidoc, integration</h3>
<ul>
<li>
<p><strong>Contract(계약)</strong>: Contract는 소프트웨어 개발에서 API나 서비스 간의 상호 작용을 정의하고 모의(mocks) 또는 테스트를 위한 기준을 설정하는 데 사용되는 개념입니다. API의 요청과 응답 형식, 데이터 유형, 동작 등을 정의하여 클라이언트와 서버 간의 상호 작용을 명확히하고, 개발 과정에서 오류를 줄이고 일관성을 유지하는 데 도움을 줍니다.</p>
</li>
<li>
<p><strong>AsciiDoc(adoc)</strong>: AsciiDoc는 문서 작성을 위한 마크업 언어 중 하나입니다. 특히 기술 문서, 사용 설명서, 블로그 게시물 등을 작성하기 위해 사용됩니다. AsciiDoc는 일반적인 텍스트로 작성되며, 일부 특수한 텍스트 구조와 문법을 사용하여 문서를 작성할 수 있습니다. AsciiDoc는 풍부한 마크업 기능을 가지고 있어 문서의 구조를 정의하고 포맷팅할 수 있습니다.</p>
</li>
</ul>
<p>AsciiDoc은 문서화에 사용되는 텍스트 형식을 정의하고 구조화하는 데 중점을 둔 반면, Contract는 서비스 간의 상호 작용을 정의하고 제어하는 데 중점을 둡니다.</p>
<ul>
<li><strong>Integration(통합)</strong>: Integration은 시스템의 다양한 구성 요소, 서비스, 또는 애플리케이션들을 함께 작동하도록 결합하는 과정이나 기술을 나타냅니다. 이는 서로 다른 시스템이나 서비스 간의 통신, 데이터 공유, 동작의 일관성을 유지하거나 서로 통합되어 작동할 수 있도록 하는 것을 의미합니다.</li>
</ul>
<p>Integration은 일반적으로 서로 다른 시스템이나 컴포넌트를 통합하여 하나의 완전한 시스템을 구축하거나 운영하기 위한 프로세스이며, Contract는 개별적인 서비스나 API 간의 상호 작용을 명확히하기 위해 사용됩니다. Integration은 보다 광범위한 시스템의 관점에서 전체적인 통합을 다루는 데 비해, Contract는 더 작은 범위에서의 상호 작용을 정의하고 테스트하는 데 사용됩니다.</p>
<h3 id="typescript의-declaration">typescript의 declaration</h3>
<p>주요 차이점은 TypeScript의 declaration 파일은 주로 타입 정보를 정의하고, 컴파일러에게 타입 정보를 제공하여 타입 체크와 IDE에서의 IntelliSense를 지원하는 데 사용되는 반면, Spring의 Contract는 API나 서비스 간의 상호 작용을 정의하고 제어하기 위한 목적으로 사용됩니다. 개발자와 클라이언트 간의 계약을 나타내고 테스트를 위한 기준을 설정하는 데 중점을 두고 있습니다.</p>
<h2 id="consumer-driven-contracts">Consumer Driven Contracts</h2>
<p>Consumer-Driven Contracts (CDC)는 소비자가 서비스 제공자에게 어떤 형태의 계약(Contract)을 원하는지 정의하는 접근 방식입니다. 이것은 두 서비스 간의 상호 작용을 정의하는데 사용됩니다.</p>
<p>소비자(Clients 또는 Consumers)가 정의한 이 계약은 서비스 제공자(Provider)에 의해 준수되어야 합니다. 이 계약은 주로 API 요청과 응답에 대한 예상 형식, 데이터 유형, 동작, 응답 상태 등을 정의합니다. Consumer가 요청하는 것을 바탕으로 작성되었기 때문에 Consumer-Driven이라고 부릅니다.</p>
<h2 id="무엇을-만들-것인가">무엇을 만들 것인가</h2>
<p>하나는 계약을 제공하고 다른 하나는 이 계약을 사용하여 두 개의 마이크로 서비스를 설정하여 계약 공급자 서비스에 대한 통합이 사양과 일치하는지 확인합니다. 나중에 생산자 서비스의 계약이 변경되면 소비자 서비스의 테스트가 잠재적인 비호환성을 포착하지 못합니다.</p>
<h2 id="starting-with-spring-initializr">Starting with Spring Initializr</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/a9ee2137-7626-4c37-81b0-51c4c689bb15/image.png"></p>
<p>튜토리얼이 너무 오래된 것 같아서 패스 합니다.</p>