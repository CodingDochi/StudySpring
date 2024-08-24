<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux-view.html#webflux-view-httpmessagewriter">반응형 스택에서 이에 상응하는 내용 보기</a> </p>
<p>Spring은 Jackson JSON 라이브러리를 지원합니다.</p>
<h1 id="jackson-based-json-mvc-views">Jackson-based JSON MVC Views</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux-view.html#webflux-view-httpmessagewriter">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p><code>MappingJackson2JsonView</code>는 Jackson 라이브러리의 <code>ObjectMapper</code>를 사용하여 응답 콘텐츠를 JSON으로 렌더링합니다. 기본적으로 모델 맵의 전체 콘텐츠(프레임워크별 클래스 제외)는 JSON으로 인코딩됩니다. 맵의 콘텐츠를 필터링해야 하는 경우 <code>modelKeys</code> 속성을 사용하여 인코딩할 특정 모델 속성 세트를 지정할 수 있습니다. 또한 <code>extractValueFromSingleKeyModel</code> 속성을 사용하면 단일 키 모델의 값을 모델 속성 맵이 아닌 직접 추출하고 직렬화할 수 있습니다.</p>
<p>Jackson이 제공한 주석을 사용하여 필요에 따라 JSON 매핑을 사용자 정의할 수 있습니다. 추가 제어가 필요한 경우 특정 유형에 대한 사용자 지정 JSON 직렬 변환기 및 역직렬 변환기를 제공해야 하는 경우 <code>ObjectMapper</code> 속성을 통해 사용자 지정 <code>ObjectMapper</code>를 주입할 수 있습니다.</p>
<h1 id="jackson-based-xml-views">Jackson-based XML Views</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux-view.html#webflux-view-httpmessagewriter">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p><code>MappingJackson2XmlView</code>는 <a href="https://github.com/FasterXML/jackson-dataformat-xml">Jackson XML 확장</a>의 <code>XmlMapper</code>를 사용하여 응답 콘텐츠를 XML로 렌더링합니다. 모델에 여러 항목이 포함된 경우 <code>modelKey</code> Bean 속성을 사용하여 직렬화할 객체를 명시적으로 설정해야 합니다. 모델에 단일 항목이 포함되어 있으면 자동으로 직렬화됩니다.</p>
<p>JAXB 또는 Jackson이 제공하는 주석을 사용하여 필요에 따라 XML 매핑을 사용자 정의할 수 있습니다. 추가 제어가 필요한 경우 <code>ObjectMapper</code> 속성을 통해 사용자 지정 <code>XmlMapper</code>를 삽입할 수 있습니다. 사용자 지정 XML이 특정 유형에 대한 직렬 변환기 및 역직렬 변환기를 제공해야 하는 경우입니다.</p>