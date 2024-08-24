<p>플래시 속성은 한 요청이 다른 요청에서 사용할 속성을 저장하는 방법을 제공합니다. 이는 리디렉션할 때 가장 일반적으로 필요합니다. 예를 들어 Post-Redirect-Get 패턴입니다. Flash 속성은 리디렉션(일반적으로 세션에서) 전에 일시적으로 저장되어 리디렉션 후 요청에 사용할 수 있으며 즉시 제거됩니다.</p>
<p>Spring MVC에는 플래시 속성을 지원하는 두 가지 주요 추상화가 있습니다. <code>FlashMap</code>은 플래시 속성을 보유하는 데 사용되는 반면 <code>FlashMapManager</code>는 <code>FlashMap</code> 인스턴스를 저장, 검색 및 관리하는 데 사용됩니다.</p>
<p>Flash 속성 지원은 항상 "켜져(on)" 있으며 명시적으로 활성화할 필요가 없습니다. 그러나 사용하지 않으면 HTTP 세션 생성이 발생하지 않습니다. 각 요청에는 이전 요청(있는 경우)에서 전달된 속성이 포함된 "입력(input)" <code>FlashMap</code>과 후속 요청을 위해 저장할 속성이 포함된 "출력(output)" <code>FlashMap</code>이 있습니다. 두 <code>FlashMap</code> 인스턴스 모두 <code>RequestContextUtils</code>의 정적 메서드를 통해 Spring MVC의 어느 곳에서나 액세스할 수 있습니다.</p>
<p>annotation이 달린 컨트롤러는 일반적으로 <code>FlashMap</code>과 직접 작동할 필요가 없습니다. 대신 <code>@RequestMapping</code> 메소드는 <code>RedirectAttributes</code> 유형의 인수를 승인하고 이를 사용하여 리디렉션 시나리오에 대한 플래시 속성을 추가할 수 있습니다. <code>RedirectAttributes</code>를 통해 추가된 Flash 속성은 "출력" <code>FlashMap</code>에 자동으로 전파됩니다. 마찬가지로 리디렉션 후 "입력" <code>FlashMap</code>의 속성이 대상 URL을 제공하는 컨트롤러의 <code>Model</code>에 자동으로 추가됩니다.</p>
<blockquote>
<h3 id="플래시-속성에-대한-요청-matching">플래시 속성에 대한 요청 Matching</h3>
<p>플래시 속성의 개념은 다른 많은 웹 프레임워크에 존재하며 때때로 동시성 문제에 노출되는 것으로 입증되었습니다. 이는 정의상 플래시 속성이 다음 요청까지 저장되기 때문입니다. 그러나 바로 "다음" 요청은 의도된 수신자가 아니라 또 다른 비동기 요청(예: 폴링 또는 리소스 요청)일 수 있으며, 이 경우 플래시 속성이 너무 일찍 제거됩니다.</p>
<p>이러한 문제의 가능성을 줄이기 위해 <code>RedirectView</code>는 대상 리디렉션 URL의 경로 및 쿼리 매개변수를 사용하여 <code>FlashMap</code> 인스턴스를 자동으로 "스탬프"합니다. 그러면 기본 <code>FlashMapManager</code>는 "입력" <code>FlashMap</code>을 조회할 때 해당 정보를 들어오는 요청과 일치시킵니다.</p>
<p>이는 동시성 문제의 가능성을 완전히 제거하지는 않지만 리디렉션 URL에서 이미 사용할 수 있는 정보를 사용하여 동시성 문제를 크게 줄입니다. 따라서 주로 리디렉션 시나리오에 플래시 속성을 사용하는 것이 좋습니다.</p>
</blockquote>