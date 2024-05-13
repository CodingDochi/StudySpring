<p>JDBC 데이터베이스 액세스의 기반을 형성하기 위해 여러 가지 접근 방식 중에서 선택할 수 있습니다. <code>JdbcTemplate</code>의 세 가지 유형 외에도 <code>SimpleJdbcInsert</code> 및 <code>SimpleJdbcCall</code> 접근 방식은 데이터베이스 메타데이터를 최적화하고 RDBMS 객체 스타일은 보다 객체 지향적인 접근 방식을 제공합니다. 이러한 접근 방식 중 하나를 사용하기 시작하면 여전히 혼합하여 다른 접근 방식의 기능을 포함할 수 있습니다.</p>
<ul>
<li>
<p><code>JdbcTemplate</code>은 가장 클래식하고 가장 널리 사용되는 Spring JDBC 접근 방식입니다. 이 "lowest-level" 접근 방식과 다른 모든 접근 방식은 내부적으로 <code>JdbcTemplate</code>을 사용합니다.</p>
</li>
<li>
<p><code>NamedParameterJdbcTemplate</code>은 <code>JdbcTemplate</code>을 래핑하여 기존 JDBC <code>?</code> 자리 표시자 대신 명명된 매개변수를 제공합니다. 이 접근 방식은 SQL 문에 대한 매개 변수가 여러 개 있는 경우 더 나은 문서화와 사용 편의성을 제공합니다.</p>
</li>
<li>
<p><code>SimpleJdbcInsert</code> 및 <code>SimpleJdbcCall</code>은 데이터베이스 메타데이터를 최적화하여 필요한 구성(configuration)의 양을 제한합니다. 이 접근 방식은 테이블이나 프로시저의 이름과 열 이름과 일치하는 매개 변수 맵만 제공하면 되도록 코딩을 단순화합니다. 이는 데이터베이스가 적절한 메타데이터를 제공하는 경우에만 작동합니다. 데이터베이스가 이 메타데이터를 제공하지 않는 경우 매개변수의 명시적인 구성을 제공해야 합니다.</p>
</li>
<li>
<p><code>MappingSqlQuery</code>, <code>SqlUpdate</code> 및 <code>StoredProcedure</code>를 포함한 RDBMS 객체는 당신에게 데이터 액세스 계층을 초기화하는 동안 재사용 가능하고 스레드로부터 안전한 객체를 생성할 것을 요구합니다. 이 접근 방식을 사용하면 쿼리 문자열을 정의하고, 매개 변수를 선언하고, 쿼리를 컴파일할 수 있습니다. 그렇게 하면, 다양한 매개변수 값을 사용하여 <code>execute(...​)</code>, <code>update(...​)</code> 및 <code>findObject(...​)</code> 메서드를 여러 번 호출할 수 있습니다.</p>
</li>
</ul>