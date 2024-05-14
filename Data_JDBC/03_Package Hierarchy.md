<p>Spring Framework의 JDBC 추상화 프레임워크는 네 가지 패키지로 구성됩니다.</p>
<ul>
<li>
<p><code>core</code>: <code>org.springframework.jdbc.core</code> 패키지에는 <code>JdbcTemplate</code> 클래스와 다양한 콜백 인터페이스, 다양한 관련 클래스가 포함되어 있습니다. <code>org.springframework.jdbc.core.simple</code>이라는 하위 패키지에는 <code>SimpleJdbcInsert</code> 및 <code>SimpleJdbcCall</code> 클래스가 포함되어 있습니다. <code>org.springframework.jdbc.core.namedparam</code>이라는 또 다른 하위 패키지에는 <code>NamedParameterJdbcTemplate</code> 클래스와 관련 지원 클래스가 포함되어 있습니다. <a href="https://docs.spring.io/spring-framework/reference/data-access/jdbc/core.html">JDBC 핵심 클래스를 사용하여 기본 JDBC 처리 및 오류 처리 제어</a>, <a href="https://docs.spring.io/spring-framework/reference/data-access/jdbc/advanced.html">JDBC 배치 작업</a> 및 <a href="https://docs.spring.io/spring-framework/reference/data-access/jdbc/simple.html"><code>SimpleJdbc</code> 클래스를 사용하여 JDBC 작업 단순화</a>를 참조하세요.</p>
</li>
<li>
<p><code>datasource</code>: <code>org.springframework.jdbc.datasource</code> 패키지에는 손쉬운 <code>DataSource</code> 액세스를 위한 유틸리티 클래스와 Jakarta EE 컨테이너 외부에서 수정되지 않은 JDBC 코드를 테스트하고 실행하는 데 사용할 수 있는 다양하고 간단한 <code>DataSource</code> 구현이 포함되어 있습니다. <code>org.springframework.jdbc.datasource.embedded</code>라는 하위 패키지는 HSQL, H2 및 Derby와 같은 Java 데이터베이스 엔진을 사용하여 임베디드 데이터베이스 생성을 지원합니다. <a href="https://docs.spring.io/spring-framework/reference/data-access/jdbc/connections.html">데이터베이스 연결 제어</a> 및 <a href="https://docs.spring.io/spring-framework/reference/data-access/jdbc/embedded-database-support.html">내장 데이터베이스 지원</a>을 참조하세요.</p>
</li>
<li>
<p><code>object</code>: <code>org.springframework.jdbc.object</code> 패키지에는 RDBMS 쿼리, 업데이트 및 저장 프로시저를 스레드로부터 안전하고 재사용 가능한 객체로 나타내는 클래스가 포함되어 있습니다. <a href="https://docs.spring.io/spring-framework/reference/data-access/jdbc/object.html">JDBC 작업을 Java 객체로 모델링하기</a>를 참조하세요. 이 스타일을 사용하면 쿼리에 의해 반환된 개체가 자연스럽게 데이터베이스에서 연결이 끊어지더라도 보다 개체 지향적인 접근 방식이 됩니다. 이 높은 수준(higher-level)의 JDBC 추상화는 <code>org.springframework.jdbc.core</code> 패키지의 낮은 수준(lower-level) 추상화에 따라 달라집니다.</p>
</li>
<li>
<p><code>support</code> : <code>org.springframework.jdbc.support</code> 패키지는 <code>SQLException</code> 변환(translation) 기능과 일부 유틸리티 클래스를 제공합니다. JDBC 처리 중에 발생한 예외는 <code>org.springframework.dao</code> 패키지에 정의된 예외로 변환됩니다. 이는 Spring JDBC 추상화 계층을 사용하는 코드가 JDBC 또는 RDBMS 관련 오류 처리를 구현할 필요가 없음을 의미합니다. 번역된 모든 예외는 선택 해제(unchecked)되어 복구할 수 있는 예외를 포착할 수 있는 옵션을 제공합니다. 다른 예외가 caller에게 전파되도록 하면서요. <a href="https://docs.spring.io/spring-framework/reference/data-access/jdbc/core.html#jdbc-SQLExceptionTranslator"><code>SQLExceptionTranslator</code> 사용을 참조하세요</a>.</p>
</li>
</ul>