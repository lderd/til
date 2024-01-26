# DAO

Data Access Object, database에 접근하기 위한 객체

db에 접근하기 위한 로직과 비즈니스 로직을 분리하기 위해 사용

서비스 모델과 데이터베이스를 연결하는 역할

데이터의 CRUD를 담당하는 오브젝트

# DTO

Data Transfer Object, 데이터를 전달하기 위한 객체

로직을 가지지 않는 순수한 데이터 객체(getter와 setter만 존재)

가변 객체일 경우 setter를 가지고 불변 객체일 경우 생성자와 getter만으로 구현

# VO

Value Object, 값 자체를 표현하는 객체

read-Only 특징을 가짐. getter와 비지니스 로직을 가실 수 있지만 setter는 가지면 안됨.
