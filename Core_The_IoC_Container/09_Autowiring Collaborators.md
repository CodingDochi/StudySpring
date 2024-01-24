<p>Spring 컨테이너는 공동 작업하는 Bean 간의 관계를 자동으로 연결(autowire)할 수 있습니다. <code>ApplicationContext</code>의 내용을 검사(inspect)하여 Spring이 빈에 대한 협력자(다른 빈)를 자동으로 resolve하도록 할 수 있습니다. Autowiring에는 다음과 같은 장점이 있습니다.</p>
<ul>
<li>
<p>Autowiring을 사용하면 속성(property)이나 생성자 인수를 지정할 필요성이 크게 줄어들 수 있습니다. (<a href="https://docs.spring.io/spring-framework/reference/core/beans/child-bean-definitions.html">이 장의 다른 곳에서 논의된</a> 빈 템플릿과 같은 다른 메커니즘도 이와 관련하여(in this regard) 중요합니다.)</p>
</li>
<li>
<p>Autowiring은 객체가 발전함에 따라 구성(configuration)을 업데이트할 수 있습니다. 예를 들어 클래스에 종속성을 추가해야 하는 경우,  구성을 수정하지 않고도 해당 종속성을 자동으로 충족할 수 있습니다. 따라서 Autowiring은 코드 기반이 더욱 안정되었을 때 명시적 wiring으로 전환하는 옵션을 부정하지 않아(without negating) 개발 중에 특히 유용할 수 있습니다.</p>
</li>
</ul>
<p>XML 기반 구성 메타데이터(<a href="https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-collaborators.html">종속성 주입</a> 참조)를 사용할 때 <code>&lt;bean/&gt;</code> 요소의 <code>autowire</code> 속성(attribute)을 사용하여 Bean 정의에 대한 자동 연결(autowire) 모드를 지정할 수 있습니다. Autowiring 기능에는 네 가지 모드가 있습니다. bean 별로 autowiring을 지정하고 autowirie할 항목을 선택할 수 있습니다. 다음 표에서는 네 가지 autowire 모드에 대해 설명합니다.</p>
<table id="beans-factory-autowiring-modes-tbl" class="tableblock 프레임-모든 그리드-모든 스트레치">
<caption class="title">표 1. autowiring 모드</caption>




<thead>
<tr>
<th class="tableblock haalign-left valign-top">  모드  </th>
<th class="tableblock haalign-left valign-top">설명</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock haalign-left valign-top"><p class="tableblock"><code>no</code></p></td>
<td class="tableblock haalign-left valign-top"><p class="tableblock">(기본값) 자동 연결이 없습니다. Bean 참조는 <code>ref</code> 요소로 정의되어야 합니다. 바꾸다
   대규모 배포에는 기본 설정을 변경하는 것을 권장하지 않습니다.
   collaborator를 지정하는 것은 명시적으로 더 큰 제어력과 명확성을 제공하기 때문입니다. 어느 정도는,
   시스템 구조를 문서화합니다.</p></td>
</tr>
<tr>
<td class="tableblock haalign-left valign-top"><p class="tableblock"><code>byName</code></p></td>
<td class="tableblock haalign-left valign-top"><p class="tableblock">속성(property) 이름별 Autowiring. Spring은 autowire될 필요가 있는 속성(property)과 같은 이름을 가진 bean을 찾습니다. 예를 들어, Bean 정의가 이름으로 autowire되도록 설정되어 있고, 그것이 <code>master</code> 속성(property)을 포함(즉,
   <code>setMaster(..)</code> 메서드를 가짐)한다면, Spring은 <code>master</code>라는 이름의 bean 정의를 찾고 속성(property)를 설정(set)하는 데에 사용합니다. </p></td>
</tr>
<tr>
<td class="tableblock haalign-left valign-top"><p class="tableblock"><code>byType</code></p></td>
<td class="tableblock haalign-left valign-top"><p class="tableblock">컨테이너에 해당 속성(property) 타입의 빈이 정확히 하나만 존재하는 경우 속성(property)가 자동 연결되도록 합니다. 같은 타입이 둘 이상이 존재하는 경우 치명적인 예외가 발생하며, 이는 해당 Bean에 대해 <code>byType</code> autowire를 사용할 수 없음을 가리킵니다. 일치하는 항목이 없는 경우, 아무 일도 일어나지 않습니다(속성이 설정되지 않음).</p></td>
</tr>
<tr>
<td class="tableblock haalign-left valign-top"><p class="tableblock"><code>constructor</code></p></td>
<td class="tableblock haalign-left valign-top"><p class="tableblock"><code>byType</code>과 유사하지만 생성자 인수에 적용됩니다. 컨테이너에 생성자 인수 타입의 빈이 정확히 한 개가 아니라면 치명적인 오류가 발생합니다.</p></td>
</tr>
</tbody>
</table>
<p><code>byType</code> 또는 <code>constructor</code> 자동 연결 모드를 사용하면 배열(array)과 타입 컬렉션(typed collection)을 연결할 수 있습니다. 그러한 경우, 예상 유형과 일치하는 컨테이너 내의 모든 자동 연결 후보가 종속성을 충족하기 위해 제공됩니다. 예상되는 키 type이 <code>String</code>인 경우 강력한 타입의 <code>Map</code> 인스턴스를 자동 연결할 수 있습니다. 자동 연결된 <code>Map</code> 인스턴스의 값은 예상 유형과 일치하는 모든 Bean 인스턴스들로 구성되며 <code>Map</code> 인스턴스의 키에는 해당 Bean들의 이름이 포함됩니다.</p>
<h1 id="limitations-and-disadvantages-of-autowiring">Limitations and Disadvantages of Autowiring</h1>
<p>Autowiring은 프로젝트 전반에 걸쳐 일관되게 사용될 때 가장 잘 작동합니다. autowiring이 일반적으로 사용되지 않는다면, 하나 또는 두 개의 bean 정의만을 연결하기 위해 이를 사용하는 것인지 개발자에게 혼란을 야기할 수 있습니다.</p>
<p>Autowirie의 한계와 단점을 고려하십시오.</p>
<ul>
<li>
<p><code>property</code> 및 <code>constructor-arg</code> 설정의 명시적 종속성은 항상 자동 연결을 재정의(override)합니다. primitives, <code>String</code>, <code>Class</code>(및 이러한 단순 property의 array)와 같은 단순 속성(property)은 자동 연결할 수 없습니다. 이 제한은 의도적으로 설계된 것입니다.</p>
</li>
<li>
<p>Autowiring은 명시적 wiring보다 덜 정확합니다. 이전 표에서 언급했듯이 Spring은 예상치 못한 결과가 발생할 수 있는 모호한 경우 추측을 피하도록 주의합니다. Spring 관리 객체 간의 관계는 더 이상 명시적으로 문서화되지 않습니다.</p>
</li>
<li>
<p>Spring 컨테이너에서 문서를 생성할 수 있는 도구에는 연결 정보가 제공되지 않을 수 있습니다.</p>
</li>
<li>
<p>컨테이너 내의 여러 Bean 정의는 자동 연결될 setter 메소드 또는 생성자 인수에 의해 지정된 type과 일치할 수 있습니다. 배열, 컬렉션 또는 <code>Map</code> 인스턴스의 경우 이것이 반드시 문제가 되는 것은 아닙니다. 그러나 단일 값이 필요한 종속성의 경우 이 모호성(ambiguity)은 임의로(arbitrarily) 해결되지 않습니다. 고유한 Bean 정의를 사용할 수 없으면 예외가 발생합니다.</p>
</li>
</ul>
<p>후자의 시나리오에는 몇 가지 옵션이 있습니다.</p>
<ul>
<li>
<p>명시적 연결을 위해 자동 연결을 포기합니다.</p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-autowire.html#beans-factory-autowire-candidate">다음 섹션</a>에 설명된 대로 <code>autowire-candidate</code> 속성(attribute)을 <code>false</code>로 설정하여 빈 정의에 대한 자동 연결을 피하세요.</p>
</li>
<li>
<p><code>&lt;bean/&gt;</code> 요소의 <code>primary</code> 속성(attribute)을 <code>true</code>로 설정하여 단일 Bean 정의를 기본 후보로(primary candidate) 지정합니다.</p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/reference/core/beans/annotation-config.html">어노테이션 기반 컨테이너 구성</a>에 설명된 대로, 어노테이션 기반 구성을 사용해, 세분화된 제어 구현을 가능하게 합니다. </p>
</li>
</ul>
<h1 id="excluding-a-bean-from-autowiring">Excluding a Bean from Autowiring</h1>
<p>빈 단위(per-bean basis)로 자동 연결에서 빈을 제외할 수 있습니다. Spring의 XML 형식에서 <code>&lt;bean/&gt;</code> 요소의 <code>autowire-candidate</code> 속성(attribute)을 <code>false</code>로 설정합니다. 컨테이너는 autowiring 인프라(<a href="https://docs.spring.io/spring-framework/reference/core/beans/annotation-config/autowired.html"><code>@Autowired</code></a>와 같은 어노테이션 스타일 구성 포함)에서 해당 특정 Bean 정의를 사용할 수 없도록 만듭니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
<code>autowire-candidate</code> 속성(attribute)은 type 기반 autowiring에만 영향을 미치도록 설계되었습니다. 지정된 Bean이 autowire 후보로 표시되지 않은 경우에도 resolve되는 <code>byName</code> 명시적 참조에는 영향을 미치지 않습니다. 결과적으로 이름을 사용한 자동 연결은 이름이 일치하면 Bean을 주입합니다.</p>
</blockquote>
<p>빈 이름에 대한 패턴 matching를 기반으로 자동 연결 후보를 제한할 수도 있습니다. 최상위 <code>&lt;beans/&gt;</code> 요소는 <code>default-autowire-candidates</code> 속성(attribute) 내에서 하나 이상의 패턴을 허용합니다. 예를 들어 자동 연결 후보 상태를 이름이 <code>Repository</code>로 끝나는 모든 Bean으로 제한하려면 <code>*Repository</code> 값을 제공합니다. 여러 패턴을 제공하려면 쉼표로 구분된 목록으로 정의하세요. Bean 정의의 <code>autowire-candidate</code> 속성(attribute)에 대한 명시적인 <code>true</code> 또는 <code>false</code> 값이 항상 우선합니다. 이러한 Bean의 경우 패턴 일치 규칙이 적용되지 않습니다.</p>
<p>이러한 기술은 autowring을 통해 다른 Bean에 주입되기를 원하지 않는 Bean에 유용합니다. 제외된 Bean이 autowiring을 사용하여 자체적으로 구성될 수 없다는 의미는 아닙니다. 오히려 빈 자체는 다른 빈을 자동 연결하기 위한 후보가 아닙니다.</p>