# FrameWork
  - Spring MVC
  - Spring Boot
    - Core
    - Security
    - JPA
    - Spring Boot 2.4.x ↑
  
---


# Spring MVC
  - 이일민, 『토비의 스프링 3.1』, AcornPub(2012)
  
  - 1장, 3장, 4장, 5장 -> DataSource, JDBC, Transaction에 관한 이야기
    - 4장에서 예외(체크 예외, 언체크 예외)에 대한 얘기가 나온다.
  - 2장 -> JUnit에 대한 이야기

## 1장, 오브젝트와 의존관계 (DataSource에 대한 내용)
  - 1 Phase
  ```console
  # User.java
  @Getter
  @Setter
  public class User{
  	String id;
	String name;
	String password;
  }
  
  # UserDao.java
  public class UserDao{
  	public void add(User user) throws ClassNotFoundException, SQLException{
		Class.forName("com.mysql.jdbc.Driver");									# [Problem] 중복코드
		Connection c = DriverManager.getConnection("jdbc:mysql://localhost/springbook", "spring", "book");	# [Problem] 중복코드
		
		PreparedStatement ps = c.prepareStatement("insert into users(id, name, password) values(?, ?, ?)");
		ps.setString(1, user.getId());
		ps.setString(2, user.getName());
		ps.setString(3, user.getPassword());
		
		ps.executeUpdate();
		
		ps.close();
		c.close();
	}
	
	public void get(String id) throws ClassNotFoundException, SQLException{
		Class.forName("com.mysql.jdbc.Driver");									# [Problem] 중복코드
		Connection c = DriverManager.getConnection("jdbc:mysql://localhost/springbook", "spring", "book");	# [Problem] 중복코드
		
		PreparedStatement ps = c.prepareStatement("select * from users where id = ?");
		ps.setString(1, id);
		
		ResultSet rs = ps.executeQuery();
		re.next();
		User user = new User();
		user.setId(rs.getString("id"));
		user.setName(rs.getString("name"));
		user.setPassword(rs.getString("password"));
		
		rs.close();
		ps.close();
		c.close();
		
		return user;
	}
  }
  ```
  
  - 2 Phase, 중복 코드의 메소드 추출
    - 중복코드를 제거한다.
  ```console
  # UserDao.java
  public class UserDao{
  	public void add(User user) throws ClassNotFoundException, SQLException{
		Connection c = getConnection();										# [Solution] 중복코드 제거
		
		PreparedStatement ps = c.prepareStatement("insert into users(id, name, password) values(?, ?, ?)");
		ps.setString(1, user.getId());
		ps.setString(2, user.getName());
		ps.setString(3, user.getPassword());
		
		ps.executeUpdate();
		
		ps.close();
		c.close();
	}
	
	public void get(String id) throws ClassNotFoundException, SQLException{
		Connection c = getConnection();										# [Solution] 중복코드 제거
		
		PreparedStatement ps = c.prepareStatement("select * from users where id = ?");
		ps.setString(1, id);
		
		ResultSet rs = ps.executeQuery();
		re.next();
		User user = new User();
		user.setId(rs.getString("id"));
		user.setName(rs.getString("name"));
		user.setPassword(rs.getString("password"));
		
		rs.close();
		ps.close();
		c.close();
		
		return user;
	}
	
	private Connection getConnection() throws ClassNotFoundException, SQLException{					# [Solution] 중복코드 제거
		Class.forName("com.mysql.jdbc.Driver");									# [Problem] DataBase의 다형성 필요
		Connection c = DriverManager.getConnection("jdbc:mysql://localhost/springbook", "spring", "book");
		return c;
	}
  }
  ```
  
  - 3 Phase, 상속을 통한 확장
    - 배포할 고객사(또는 서버)별 DataBase가 상이할 경우를 대비한다.
  ```console
  # UserDao.java
  public abstract class UserDao{
  	public void add(User user) throws ClassNotFoundException, SQLException{
		Connection c = getConnection();
		...
	}
	
  	public void get(String id) throws ClassNotFoundException, SQLException{
		Connection c = getConnection();
		...
	}
	
	public abstract Connection getConnection() throws ClassNotFoundException, SQLException;
  }
  
  # NUserDao.java
  public class NUserDao extends UserDao{
  	@Override
  	public Connection getConnection() throws ClassNotFoundException, SQLException{
		Class.forName("com.mysql.jdbc.Driver");
		Connection c = DriverManager.getConnection("jdbc:mysql://localhost/springbook", "spring", "book");
		return c;
	}
  }
  
  # DUserDao.java
  public class DUserDao extends UserDao{
  	@Override
  	public Connection getConnection() throws ClassNotFoundException, SQLException{
		Class.forName("com.oracle.jdbc.Driver");
		Connection c = DriverManager.getConnection("jdbc:mysql://localhost/springbook", "spring", "book");
		return c;
	}
  }
  ```
  
  - 4 Phase, 클래스 분리(=관심사 분리)
  ```console
  # UserDao.java
  public class UserDao{
  	public UserDao(){
		simpleConnectionMaker = new SimpleConnectionMaker();
	}
	
  	private SimpleConnectionMaker simpleConnectionMaker;
	
	public void add(User user) throws ClassNotFoundException, SQLException{
		Connection c = simpleConnectionMaker.makeNewConnection();
		...
	}
	
	public void get(String id) throws ClassNotFoundException, SQLException{
		Connection c = simpleConnectionMaker.makeNewConnection();
		...
	}
  }
  
  # SimpleConnectionMaker.java
  public class SimpleConnectionMaker{
  	public Connection makeNewConnection() throws ClassNotFoundException, SQLException{
		Class.forName("com.mysql.jdbc.Driver");
		Connection c = DriverManager.getConnection("jdbc:mysql://localhost/springbook", "spring", "book");
		
		return c;
	}
  }
  ```
  
  - 5 Phase, 인터페이스 도입(=관심사 분리)
  ```console
  # ConnectionMaker.java
  public interface ConnectionMaker{
  	public Connection makeConnection() throws ClassNotFoundException, SQLException;
  }
  
  public class DConnectionMaker implements ConnectionMaker{
  	...
  	public Connection makeConnection() throws ClassNotFoundException, SQLException{
		// to do
	}
  }
  
  # UserDao.java
  public class UserDao{
  	public userDao(){
		connectionMaker = new DConnectionMaker();				# [Problem] 구현체가 여기서 정의되면 안된다.
	}
	
  	private ConnectionMaker connectionMaker;					# 인터페이스를 통해 오브젝트에 접근하므로 구체적인 클래스 정보를 알 필요가 없다.
	
	public void add(User user) throws ClassNotFoundException, SQLException{
		Connection c = connectionMaker.makeConnection();			# 인터페이스에 정의된 메소드를 사용하므로, 클래스가 바뀌어도 수정할 내역이 없다.
		...
	}
	
	public User get(String id) throws ClassNotFoundException, SQLException{
		Connection c = connectionMaker.makeConnection();			# 인터페이스에 정의된 메소드를 사용하므로, 클래스가 바뀌어도 수정할 내역이 없다.
		...
	}
  }
  ```
  
  - 6 Phase, 관계설정 책임의 분리
  ```console
  # UserDao.java
  public UserDao(ConnectionMaker connectionMaker){
  	this.connectionMaker = connectionMaker;
  }
  
  # Client.java
  public class Client{
  	public static void client(...) throws ClassNotFoundException, SQLException{
		ConnectionMaker connectionMaker = new DConnectionMaker();
		UserDao userDao = new UserDao(connectionMaker);
		...
	}
  }
  ```
  
  - 7 Phase, IOC
  ```
  # DaoFactory.java
  public class DaoFactory{
  	public UserDao userDao(){
		ConnectionMaker connectionMaker = new DConnectionMaker();
		UserDao userDao = new UserDao(connectionMaker);
		return userDao;
	}
  }
  
  # Client.java
  public class Client{
  	public static void client(...) throws ClassNotFoundException, SQLException{
		UserDao userDao = DaoFactory().userDao();
		...
	}
  }
  ```
  
  ```
  ### Factory의 활용
  # DaoFactory.java
  public class DaoFactory{
  	public UserDao userDao(){
		return new UserDao(connectionMaker());
	}

	public AccountDao accountDao(){
		return new AccountDao(connectionMaker());
	}
	
	public ConnectionMaker connectionMaker(){
		return new DConnectionMaker();
	}
  }
  
  # Client.java
  public class Client{
  	public static void client(...) throws ClassNotFoundException, SQLException{
		UserDao userDao = DaoFactory().userDao();
		...
	}
  }
  ```
  
  - 8 Phase, 제어권의 이전을 통한 제어관계 역전(Spring의 활용)
    - 현 예제에서는 ApplicationContext 인터페이스를 사용하였지만, 서재에서는 Setter 또는 XML방식을 추가 설명하였다.
    - 다만, 본질은 스프링에서 Singleton Registry를 이용하여, Bean을 관리하고, 관리된 Bean을 DI한다는데 의의를 두면 된다.
  ```console
  # DaoFactory.java
  @Configuration
  public class DaoFactory{
  	@Bean
	public UserDao userDao(){
		return new UserDao(connectionMaker());
	}
	
  	@Bean
	public ConnectionMaker connectionMaker(){
		return new DConnectionMaker();
	}
  }
  
  # Client.java
  public class Client{
  	public static void client(...) throws ClassNotFoundException, SQLException{
		ApplicationContext context = new AnnotationConfigApplicationContext(DaoFactory.class);
		UserDao userDao = context.getBean("userDao", UserDao.class);
		...
	}
  }
  ```
  
  - 9 Phase, DataSource 인터페이스로 변환
    - ConnectionMaker는 DB컨넥션을 생성해주는 기능 하나만을 정의한 매우 단순한 인터페이스다.
    - Java진영에서는 DB컨넥션을 가져오는 오브젝트의 기능을 추상화하여, 다양하게 사용할 수 있게 만들어진 DataSource라는 클래스가 이미 존재한다.
  ```console
  # javax.sql.DataSource.java
  public interface DataSource extends CommonDataSource, Wrapper{
  	Connection getConnection() throws SQLException;
  }
  
  # UserDao.java
  UserDao{
  	private DataSource dataSource;
	
	public void setDataSource(DataSource dataSource){
		this.dataSource = dataSource;
	}
	
	public void add(User user) throws SQLException{
		Connection c = dataSource.getConnection();
		...
	}
  }
  ```
  
## 2장, 테스트 (JUnit에 대한 내용)

## 3장, 템플릿 (JDBC에 대한 내용)
  - 1 Phase
  ```console
  # UserDao.java
  public class UserDao{
  	...
	
  	public void deleteAll() throws SQLException{
		Connection c = dataSource.getConnection();
		
		PreparedStatement ps = c.prepareStatement("delete from users");
		ps.executeUpdate();
		
		ps.close();
		c.close();
	}
	
	...
  }
  ```
  
  - 2 Phase, 예외처리
  ```console
  # UserDao.java
  public class UserDao{
  	...
	
  	public void deleteAll() throws SQLException{
		Connection c = null;
		PreparedStatement ps = null;
		
		try{
			c = dataSource.getConnection();
			ps = c.prepareStatement("delete from users");
			ps.executeUpdate();
		}catch(SQLException se){
			throw e;
		}finally{
			if(ps!=null){
				try{ ps.close(); } catch(SQLException se){}
			}
			if(c!=null){
				try{ c.close(); } catch(SQLException se){}
			}
		}
	}
	
	...
  }
  ```
  
  - 3 Phase, 메소드 추출
  ```console
  # UserDao.java
  public class UserDao{
  	...
	
	public void deleteAll() throws SQLException{
		...
		
		try{
			c = dataSource.getConnection();
			ps = makeStatement(c);
			ps. executeUpdate();
		}catch(SQLException e){
		}
		
		...
	}
	
	private PreparedStatement makeStatement(Connection c) throws SQLException{
		PreparedStatement ps;
		ps = c.prepareStatement("delete from users");
		return ps;
	}
	
	...
  }
  ```
  
  - 4 Phase, 템플릿 메소드 패턴의 적용
  ```console
  # StatementStrategy.java
  public interface StatementStrategy{
  	PreparedStatement makePreparedStatement(Connection c) throws SQLException;
  }
  
  # DeleteAllStatement.java
  public class DeleteAllStatement implements StatementStrategy{
  	public PreparedStatement makePreparedStatement(Connection c) throws SQLException{
		PreparedStatement ps = c.prepareStatement("delete from users");
		return ps;
	}
  }
  
  # UserDao.java
  public class UserDao{
  	...
	
	public void deleteAll() throws SQLException{
		...
		
		try{
			c = dataSource.getConnection();

			StatementStrategy strategy = new DeleteAllStatement();
			ps = strategy.makePreparedStatement(c);

			ps.executeUpdate();
		}catch(SQLException e){
			...
		}
		
		...
	}
	
	...
  }
  ```
  
  - 5 Phase, 메소드 추출
  ```console
  # StatementStrategy.java
  public interface StatementStrategy{
  	PreparedStatement makePreparedStatement(Connection c) throws SQLException;
  }
  
  # DeleteAllStatement.java
  public class DeleteAllStatement implements StatementStrategy{
  	public PreparedStatement makePreparedStatement(Connection c) throws SQLException{
		PreparedStatement ps = c.prepareStatement("delete from users");
		return ps;
	}
  }
  
  # AddStatement.java
  public class AddStatement implements StatementStrategy{
  	public PreparedStatement makePreparedStatement(Connection c) throws SQLException{
		PreparedStatement ps = c.prepareStatement("insert into users(id, name, password) values(?, ?, ?)");
		ps.setString(1, user.getId());
		ps.setString(2, user.getName());
		ps.setString(3, user.getPassword());
		
		return ps;
	}
  }
  
  # UserDao.java
  public class UserDao{
  	...
	
	public void deleteAll() throws SQLException{
		StatementStrategy st = new DeleteAllStatement();
		jdbcContextWithStatementStrategy(st);
	}
	
	public void jdbcContextWithStatementStrategy(StatementStrategy stmt) throws SQLException{
		Connection c = null;
		PreparedStatement ps = null;
		
		try{
			c = dataSource.getConnection();
			
			ps = stmt.makePreparedStatement(c);
			
			ps.executeUpdate();
		}catch(SQLException e){
			throw e;
		}finally{
			if(ps!=null){ try{ps.close();}catch(SQLException e){} }
			if( c!=null){ try{ c.close();}catch(SQLException e){} }
		}
	}
	
	...
  }
  ```
  
  - 6 Phase, 로컬 클래스
  ```console
  public class UserDao{
  	...
  
  	public void add(final User user) throws SQLException{			# User 파라미터를 final로 선언할 경우, 내부 클래스에서 외부의 변수를 사용할 수 있다.
		class AddStatement implements StatementStrategy{
			User user;
			
			public AddStatement(USer user){
				this.user = user;
			}
			
			public PreparedStatement makePreparedStatement(Connection c) throws SQLException{
				PreparedStatement ps = c.preparedStatement("insert into users(id, name, password) values(?, ?, ?);
				ps.setString(1, user.getId());
				ps.setString(2, user.getName());
				ps.setString(3, user.getPassword());
				
				return ps;
			}
		}
		
		StatementStrategy st = new AddStatement();			# User 파라미터를 final로 선언하였기 때문에, AddStatement 생성자에 user를 파라미터로 전달하지 않아도된다.
		//StatementStrategy st = new AddStatement(user);		# USer 파라미터를 final로 선언하지 않았을 경우.
		jdbcContextWithStatementStrategy(st);
	}
	
	public void jdbcContextWithStatementStrategy(StatementStrategy stmt) throws SQLException{
		Connection c = null;
		PreparedStatement ps = null;
		
		try{
			c = dataSource.getConnection();
			
			ps = stmt.makePreparedStatement(c);
			
			ps.executeUpdate();
		}catch(SQLException e){
			throw e;
		}finally{
			if(ps!=null){ try{ps.close();}catch(SQLException e){} }
			if( c!=null){ try{ c.close();}catch(SQLException e){} }
		}
	}
	
	...
  }
  ```
  
  - 7 Phase, 익명 내부 클래스
  ```console
  public class UserDao{
  	...
  
  	public void add(final User user) throws SQLException{
		jdbcContextWithStatementStrategy(
			new StatementStrategy(){
				public PreparedStatement makePreparedStatement(Connection c) throws SQLException{
					PreparedStatement ps = c.prepareStatement("insert into users(id, name, password) values(?, ?, ?)");
					ps.setString(1, user.getId());
					ps.setString(2, user.getName());
					ps.setString(3, user.getPassword());
					
					return ps;
				}
			}
		)
	}
	
  	public void deleteAll() throws SQLException{
		jdbcContextWithStatementStrategy(
			new StatementStrategy(){
				public PreparedStatement makePreparedStatement(Connection c) throws SQLException{
					PreparedStatement ps = c.prepareStatement("delete from users");
				}
			}
		)
	}
	
	...
  }
  ```
  
  - 8 Phase, 클래스 분리
  ```console
  # JdbcContext.java
  public class JdbcContext{
  	private DataSource dataSource;
	
	public void setDataSource(DataSource dataSource){
		this.dataSource = dataSource;
	}
	
	public void workWithStatementStrategy(StatementStrategy stmt) throws SQLException{
		Connection c = null;
		PreparedStatement ps = null;
		
		try{
			c.this.dataSource.getConnection();
			ps = stmt.makePreparedStatement(c);
			ps.executeUpdate();
		}catch(SQLException se){
			throw se;
		}finally{
			if(ps!=null){ try{ps.close();}catch(SQLException e){} }
			if( c!=null){ try{ c.close();}catch(SQLException e){} }
		}
	}
  }
  
  # UserDao.java
  public class UserDao{
  	...
	
	private JdbcContext jdbcContext;
	
	public void setJdbcContext(JdbcContext jdbcContext){
		this.jdbcContext = jdbcContext;
	}
	
	public void add(final User user) throws SQLException{
		this.jdbcContext.workWithStatementStrategy(
			new StatementStrategy(){
				...
			}
		)
	}
	
	public void deleteAll() throws SQLException{
		this.jdbcContext.workWithStatementStrategy(
			new StatementStrategy(){
				...
			}
		)
	}
	
	...
  }
  ```
  
  - 9 Phase, 콜백의 분리와 재활용
  ```console
  # UserDao.java
  public class UserDao{
  	...
	
	public void deleteAll() throws SQLException{
		executeSql("delete from users");
	}
	
	private void executeSql(final String query) throws SQLException{
		this.jdbcContext.workWithStatementStrategy(
			new StatementStrategy(){
				public PreparedStatement makePreparedStatement(Connection c) throws SQLException{
					return c.prepareStatement(query);
				}
			}
		)
	}
	
	...
  }
  ```
  
  - 10 Phase, 콜백과 템플릿의 결합
  ```console
  # UserDao.java
  public class UserDao{
  	...
	
	public void deleteAll() throws SQLException{
		this.jdbcContext.executeSql("delete from users");
	}
	
	...
  }
  
  # JdbcContext.java
  public class JdbcContext{
  	...
	
	public void executeSql(final String query) throws SQLException{
		workWithStatementStrategy(
			new StatementStrategy(){
				public PreparedStatement makePreparedStatement(Connection c) throws SQLException{
					return c.prepareStatement(query);
				}
			}
		)
	}
	
	...
  }
  ```
  
  - 11 Phase, 재사용 가능한 콜백의 분리(javax.sql.JdbcTemplate)
    - 위 예제에서 만든 jdbcContext는 더이상 사용하지 않는다. 다만, 이를 더 java진영에서 우아하게 구현해 놓은 JdbcTemplate클래스를 사용한다.
    - JdbcTemplate 클래스의 주요 메소드들
      - update()
      - query()
      - queryForInt()
      - queryForObject()
    - 단, JdbcTemplate의 몇 메소드는 제네릭 메소드로 타입은 파라미터로 넘기는 RowMapper<T>이기 때문에, RowMapper클래스의 구현을 인지하여야 한다.
  ```console
  # UserDao.java
  public class UserDao{
	public void setDataSource(DataSource dataSource){
		this.jdbcTemplate = new JdbcTemplate(dataSource);
	}
	
	private JdbcTemplate jdbcTemplate;
	
	private RowMapper<User> userMapper = new RowMapper<User>(){
		public User mapRow(ResultSet rs, int rowNum) throws SQLException{
			User user = new USer();
			user.setId(rs.getString("id"));
			user.setName(rs.getString("name"));
			user.setPassword(rs.getString("password"));
			return user;
		}
	}
	
	public List<User> add(final User user){
		return this.jdbcTemplate.update( "insert into users(id, name, password) values(?, ?, ?), user.getId(), user.getName(), user.getPassword() );
	}
	
	public List<User> get(String id){
		return this.jdbcTemplate.update( "select * from users where id = ?", new Object[] {id}, this.userMapper );
	}
	
	public List<User> deleteAll(){
		return this.jdbcTemplate.update( "delete from users" );
	}
	
	public List<User> getCount(){
		return this.jdbcTemplate.update( "select count(*) from users" );
	}
	
	public List<User> getAll(){
		return this.jdbcTemplate.update( "select * from users order by id", this.userMapper );
	}
  }
  ```

## 4장, 예외
  - 예외의 종류
    - java.lang.Error
      - 시스템에 뭔가 비정상적인 상황이 발생하였을 경우 사용.
      - 주로 VM에서 발생시키는 것이고 애플리케이션 코드에서 잡으려고 하면 안된다.
	- 예> OutOfMemoryError, ThreadDeath 등
    - Exception
      - 컴파일 단계에서 추론 가능한 예외
    - RuntimeException
      - 실행시점에 추론 가능한 예외
      - 프로그램의 오류가 있을 때 발생하도록 의도된 것들
  - 예외처리방법
      - 예외 복구: catch문 내, while등을 이용하여 예외 발생 시 N차례 재시도하도록 구현
      - 예외 회피: catch문 내, 로그 등을 출력하도록 한 뒤, 다시 throws하여 예외를 직접 처리치 않고, 호출한 곳에서 처리 할 수 있도록 한다.
      - 예외 전환: catch문 내, 예외를 catch하였을 때, 해당 예외를 잡아 조금 더 구체적인 예외로 변경(Checked Exception)하거나, 무의미한 Exception에서 해방될 수 있도록 새로운 예외(Unchecked Exception)를 throws한다.
	
### 4.2 예외 전환
  - JdbcTemplate으로 구현한 부분을 보면 throws SQLException구문이 모두 빠져있는걸 확인할 수 있다.
  - JdbcTemplate이 SQLException을 DataAccessException(RuntimeException)으로 예외 전환을 하였기 때문이다.
  - 뿐만 아니라, DB메타정보를 참고해서 DB종류를 확인하고 DB별로 미리 준비된 매핑정보를 참고해서 적절한 예외 클래스를 선택(DataAccessException의 child class)하여 예외를 발생한다.
	
#### 4.2.3 DAO인터페이스와 DataAccessException 계층구조

#### 4.2.4 기술에 독립적인 UserDao 만들기
  - 인터페이스 적용

## 5장, 서비스 추상화 (TransactionManager에 대한 내용)
  - 
  ```console
  # Level.java
  class enum Level{
	BASIC(1), SILVER(2), GOLD(3);
	
	private final int value;
	
	Level(int Value){
		return value;
	}
	
	public static Level valueOf(int value){
		switch(value) {
			case 1: return BASIC;
			case 2: return SILVER;
			case 3: return GOLD;
			default: throw new AssertionError("Unknown value: "+value);
		}
	}
  }
	
  # User.java
  @Getter
  @Setter
  public class User{
  	String id;
	String name;
	String password;
	
	Level level;
	
	int login;
	int recommend;
  }
	
  # UserDao.java
  public interface UserDao{
	...
	
	public void update(User user1);
  }
	
  # UserDaoJdbc.java
  public class UserDaoJdbc implements USerDao{
	...
	
	private RowMapper<User> userMapper = new RowMapper<User>(){
		public User mapRow(ResultSet rs, int rowNum) throws SQLException{
			User user = new USer();
			user.setId(rs.getString("id"));
			user.setName(rs.getString("name"));
			user.setPassword(rs.getString("password"));
			user.setLevel(Level.valueOf(rs.getInt("level")));
			user.setLogin(rs.getInt("login"));
			user.setRecommend(rs.getInt("recommend"));
			return user;
		}
	}
	
	public void add(User user){
		this.jdbcTemplate.update(
			"insert into users(id, name, password, level, login, recommend)" + "values(?, ?, ?, ?, ?, ?)", +
			user.getId(), user.getPassword(), user.getLevel().intValue(), user.getLogin(), user.getRecommend()
		);
	}
	
	public void update(User user){
		this.jdbcTemplate.update(
			"update users set name = ?, password = ?, level = ?, login = ?, recommend = ?, where id = ? ",
			user.getName(), user.getPassword(), user.getLevel().intValue(), user.getLogin(), user.getRecommend(), user.getId()
		);
	}
  }
  ```
	
---


# Spring Boot

## core
  - 참고사이트: [https://blog.woniper.net/336](https://blog.woniper.net/336)
  
  
## Spring Project creating
  - Spring Initializr 사용방법(2가지)
    ~~~
    1. Spring 공식 홈페이지(spring.io)에서 제공하는 기능을 사용합니다.
      1-1. spring.io 접속
      1-2. projects(상단메뉴) > spring boot(조회된 메뉴) > Quick start(상세 탭) > Spring Initializr 이용
       
      2. IDE(Intelli-J, STS 등)에서 제공하는 기능을 사용합니다.
        2-1. 각 tools마다 사용법 상이.
    ~~~
      
  - Spring Initializr Description
    ~~~
    - Project: Maven Project 또는 Gradle Project
      - 빌드, 베포 툴인 Maven과 Gradle 중 택
    - Language: Java, Kotlin, Groovy
    - Spring Boot: Spring Boot의 version
    - Project Metadata: group, artifact 등을 지정합니다.
      - 일반적으로 group은 project의 도메인 및 Default package 경로를 뜻하며, artifact는 프로젝트 명을 암시합니다.
    - Dependencies
      - 프로젝트의 의존성을 추가합니다. (간단히 소프트웨어의 플러그인 정도로 생각하면 됩니다.)
    
  - Spring Boot Config
    - 개념
      - .properties
      - yaml
        - 파일명 또는 경로변경/추가
        ```console
        [user@localhost ~]# java -jar myproject.jar --spring.config.name=myproject.yaml
        --------------------------------------
          - Describe
            - 파일명 변경
        
        
        [user@localhost ~]# java -jar myproject.jar --spring.config.location=classpath:/myproject/{또는 파일명 변경 시, classpath:/myproject/myproject.yaml}
        --------------------------------------
          - Describe
            - 파일경로 변경
          - Result: 기존 4개 경로에서, Customizing된 경로 1개로 변경된다.
            - 기존 경로(4개)
              file:./config/
              file:./
              classpath:/config/
              classpath:/
            - Customizing되어 변경된 경로(1개)
              classpath:/myproject/
        
        
        [user@localhost ~]# java -jar myproject.jar --spring.config.additional-location=classpath:/custom-config/,file:./custom-config/
        --------------------------------------
          - Describe
            - 파일경로 추가
          - Result
            - file:./custom-config/
              classpath:custom-config/
              file:./config/
              file:./
              classpath:/config/
              classpath:/
        ```
      - 환경변수
      - command line args
      
    - 사용방법
    ```console
    [user@localhost ~]# java -jar -Dspring.profiles.active=dev board-0.0.1-SNAPSHOT.jar
    
    또는
    
    [user@localhost ~]# java -jar -Dspring-boot.run.profiles=dev board-0.0.1-SNAPSHOT.jar
    ```
      
  - 참고사이트: [https://www.latera.kr/reference/java/2019-09-29-spring-boot-config-externalize/](https://www.latera.kr/reference/java/2019-09-29-spring-boot-config-externalize/)
  
## profiles with maven3
  - You can provide commandline argument like this:
  ```console
  mvn spring-boot:run -Dspring-boot.run.arguments="--spring.profiles.active=dev"
  ```
  - You can provide JVM argument like this:
  ```console
  mvn spring-boot:run -Dspring-boot.run.jvmArguments="-Dspring.profiles.active=dev"
  ```
  - java -jar
  ```console
  java -Dspring.profiles.active=dev -jar app.jar (Note order)
  
  or
  
  java -jar app.jar --spring.profiles.active=dev (Note order)
  ```

## Bean
### Bean Hocker
#### BeanDefinitionRegistryPostProcessor
  - Bean 등록 목적
#### BeanFactoryPostProcessor
  - Bean 정의를 재정의 또는 속성추가 목적
#### BeanPostProcessor
  - Bean 인스턴스를 재정의 목적
  
  - 참고사이트: [https://thecodinglog.github.io/spring/2019/01/29/dynamic-spring-bean-registration.html](https://thecodinglog.github.io/spring/2019/01/29/dynamic-spring-bean-registration.html)

### Conditional Bean Registration(조건부 빈 등록)
#### Java
  - 참고사이트: [https://sodocumentation.net/ko/spring/topic/4732/spring%EC%9D%98-%EC%A1%B0%EA%B1%B4%EB%B6%80-%EB%B9%88-%EB%93%B1%EB%A1%9D](https://sodocumentation.net/ko/spring/topic/4732/spring%EC%9D%98-%EC%A1%B0%EA%B1%B4%EB%B6%80-%EB%B9%88-%EB%93%B1%EB%A1%9D)
  ```console
  public class PropertyCondition implements Condition {
      @Override
      public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
          return context.getEnvironment().getProperty("propertyName") != null;
          // optionally check the property value
      }
  }

  @Configuration
  public class MyAppConfig {
      @Bean
      @Conditional(PropertyCondition.class)
      public MyBean myBean() {
        return new MyBean();
      }
  }

  @Configuration
  public class MyAppConfig {
      @Bean
      @Conditional({PropertyCondition.class, SomeOtherCondition.class})
      public MyBean myBean() {
        return new MyBean();
      }
  }
  ```

#### Annotation
##### Class Conditional
  - @ConditionalOnClass
  - @ConditionalOnMissingClass
  ```console
  @Bean
  @ConditionalOnWebApplication
  @ConditionalOnClass(OObjectDatabaseTx.class)
  @ConditionalOnMissingBean(OrientWebConfigurer.class)
  public OrientWebConfigurer orientWebConfigurer() {
      return new OrientWebConfigurer();
  }
  ```
  
##### Bean Conditional
  - @ConditionalOnBean
  - @ConditionalOnMissingBean
  
##### Property Conditional
  - @ConditionalOnProperty
  ```console
  @ConditionalOnProperty(value='somebean.enabled', matchIfMissing = true, havingValue="yes")
  @Bean 
  public SomeBean someBean(){
  }
  ```
  
##### Resource Conditional
  - @ConditionalOnResource
  ```console
  @ConditionalOnResource(resources = "classpath:init-db.sql") 
  ```
  
##### WebApplication Conditional
  - @@ConditionalOnWebApplication
  - @ConditionalOnNotWebApplication
  ```console
  @Configuration
  @ConditionalOnWebApplication
  public class MyWebMvcAutoConfiguration {...}
  ```
  
##### Expression Conditional
  - @ConditionalOnExpression
  ```console
  @ConditionalOnExpression("${rest.security.enabled}==false")
  ```

## Transactional
### Prior knowledge
  - ACID(원자성, 일관성, 고립성, 지속성): transaction이 안전하게 수행된다는 것을 보장하기 위한 성질
    - Atomicity(원자성): 트랜잭션은 하나의 원자처럼 취급되어야 한다.
	                       즉 부분적으로 실행되어, 특정 부분만 반영되고 나머지는 반영되지 않으면 안된다는 뜻.
	                       예> 돈을 송금할 때, 송금하는 쪽은 성공하고 받는 쪽이 실패했다고 끝내면 안된다.
    - Consistency(일관성) : 트랜잭션이 실행을 성공적으로 완료하면 언제나 일관성 있는 Scheme 상태로 유지하는 것을 의미한다.
	                       예> 돈 100원을 송금할 때, 돈은 int형이여야 하지만 프로그램상 실수로 문자열 자료형으로 보냈을 경우, DB에 문자열 100이 저장되기 때문에 data의 일관성이 깨지기 때문에 트랜잭션이 취소되어야 한다.
    - Isolation(독립성)   : 트랜잭션을 수행 시 다른 트랜잭션의 연산 작업이 끼어들지 못하도록 독립되어 있다는 것을 보장한다.
	                         이것은 트랜잭션 밖에 있는 어떤 연산도 중간 단계의 데이터를 볼 수 없음을 의미한다.
	                         예> 법인카드의 잔여금액인 100원을 Read하여 총 3번 연산을 한다. 각 연산은 100원씩 증가를 시킨다.
	                             100 -> 200 -> 300 -> 400
	                             만약 300원째 계산을 하고 있을 때, 다른 사람이 잔여금액을 확인한다고 해도, 트랜잭션은 독립적으로 움직이기 때문에 100원으로 보인다.

    - Durability(지속성)  : 성공적으로 수행된 트랜잭션은 영원히 반영되어야 함을 의미하며, 시스템 문제, DB 일관성 체크 등을 하더라도 유지되어야 함을 의미한다.
	  			                  전형적으로 모든 트랜잭션은 로그로 남고 시스템 장애 발생 전 상태로 되돌릴 수 있다.
		  		                  트랜잭션은 로그에 모든 것이 저장된 후에만 commit 상태로 간주될 수 있다.
  
### Transactional Annotation
#### Level
  - 0Lv, DEFAULT
  - 1Lv, READ_UNCOMMITED
  - 2Lv, READ_COMMITED
  - 3Lv, REPEATABLE_READ
  - 4Lv, SERIALIZABLE

  - 참고사이트: [https://nesoy.github.io/articles/2019-05/Database-Transaction-isolation](https://nesoy.github.io/articles/2019-05/Database-Transaction-isolation)

#### propagation
  - Spring의 @Transactional의 propagation 속성으로 다음과 같은 설정
  - Propagation
    - REQUIRED : 부모 트랜잭션 내에서 실행하며 부모 트랜잭션이 없을 경우 새로운 트랜잭션을 생성
    - REQUIRES_NEW : 부모 트랜잭션을 무시하고 무조건 새로운 트랜잭션이 생성
    - SUPPORT : 부모 트랜잭션 내에서 실행하며 부모 트랜잭션이 없을 경우 nontransactionally로 실행
    - MANDATORY : 부모 트랜잭션 내에서 실행되며 부모 트랜잭션이 없을 경우 예외가 발생
    - NOT_SUPPORT : nontransactionally로 실행하며 부모 트랜잭션 내에서 실행될 경우 일시 정지
    - NEVER : nontransactionally로 실행되며 부모 트랜잭션이 존재한다면 예외가 발생
    - NESTED : 해당 메서드가 부모 트랜잭션에서 진행될 경우 별개로 커밋되거나 롤백될 수 있음. 둘러싼 트랜잭션이 없을 경우 REQUIRED와 동일하게 작동

    - 참고사이트: [https://supawer0728.github.io/2018/03/22/spring-multi-transaction/](https://supawer0728.github.io/2018/03/22/spring-multi-transaction/)

## Spring Security

## JPA
  - Object-Relational Mapping (객체 관계 매핑)
    - 객체지향(Java)과 관계형(RDBMS)과의 패러다임 불일치를 해결(매핑)하는 기술로, 본 문서는 Hibernate를 기준으로 작성.
    
  - JPA의 동작과정
    - [ [ Java Application ]  <-JPA->  [ JDBC API ] ]  <->  [ Database ]
    - Application과 JDBC 사이에 위치하여, 객체지향형과 관계형에 대한 불일치 패러다임을 해소.
    
### Tech
#### 복합키
  - @EmbededId
    ~~~
    ~~~
    
  - @IdClass
    - Identifying Relationship
      - 참고사이트: [https://woowabros.github.io/experience/2019/01/04/composit-key-jpa.html](https://woowabros.github.io/experience/2019/01/04/composit-key-jpa.html)
      
    - None Identifying Relationship
    ~~~
    @Entity
    @Table(name = "tb_mem_user")
    public class User extends BaseEntity {
        @Id
        @GeneratedValue(
                strategy = GenerationType.AUTO
        )
        @Column(name = "user_id", nullable = false)
        private Long userId;
    
    
        public Long getUserId() {
            return userId;
        }
    
        public User setUserId(Long userId) {
            this.userId = userId;
            return this;
        }
    }
    
    
    @Entity
    @Table(name = "tb_rom_room")
    public class Room extends BaseEntity {
        @Id
        @Column(name = "room_id", nullable = false)
        private String roomId;
    
    
        public String getRoomId() {
            return roomId;
        }
    
        public Room setRoomId(String roomId) {
            this.roomId = roomId;
            return this;
        }
    }
    
    
    public class UserRoomComposite implements Serializable {
        public UserRoomComposite(){
        }
    
        public UserRoomComposite(Long userId, String roomId){
            this.user = userId;
            this.room = roomId;
        }
    
    
          private Long user;    // UserRole Class에 정의된 User ReferenceVariable과 동일한 이름을 사용.
                                // @IdClass는 ReferenceVariable Name으로 UserRole Class에 정의된 ReferenceVariable를 Serche한다.
          private String room;  // UserRole Class에 정의된 Role ReferenceVariable과 동일한 이름을 사용.
                                // @IdClass는 ReferenceVariable Name으로 UserRole Class에 정의된 ReferenceVariable를 Serche한다.
    
    
        public Long getUser() {
            return user;
        }
    
        public UserRoomComposite setUser(Long user) {
            this.user = user;
            return this;
        }
    
        public String getRoom() {
            return room;
        }
    
        public UserRoomComposite setRoom(String room) {
            this.room = room;
            return this;
        }
    
    
        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (o == null || getClass() != o.getClass()) return false;
            UserRoomComposite userRoomComposite = (UserRoomComposite) o;
            return Objects.equals(this.user, userRoomComposite.getUser()) &&
                    Objects.equals(this.room, userRoomComposite.getRoom());
        }
    
        @Override
        public int hashCode() {
            return Objects.hash(this.user, this.room);
        }
    
    
        @Override
        public String toString() {
            return "UserRoomComposite{" +
                    "user=" + user +
                    ", room='" + room + '\'' +
                    '}';
        }
    }
    
    
    @Entity
    @Table(name = "tm_mem_user_room")
    @IdClass(value = UserRoomComposite.class)
    public class UserRoom extends BaseEntity {
        @Id
        @ManyToOne
        @JoinColumn(name = "user_id")
        private User user;
    
        @Id
        @ManyToOne
        @JoinColumn(name = "room_id")
        private Room room;
    
    //    @OneToMany(mappedBy = "userRoom")
    //    private List<SprinklingToken> sprinklingTokenList = new ArrayList<>();
    
    
        public User getUser() {
            return user;
        }
    
        public UserRoom setUser(User user) {
            this.user = user;
            return this;
        }
    
        public Room getRoom() {
            return room;
        }
    
        public UserRoom setRoom(Room room) {
            this.room = room;
            return this;
        }
    }
    ~~~
#### 단방향/양방향 Mapping
  - 단방향 Mapping
    ~~~
    ~~~
    
  - 양방향 Mapping
    ~~~
    @Entity
    @Table( name = "TB_COM_USER" )
    public class User{
      @Id
      @Column( name = "USER_NO" )
      private Long userNo;
    }
    
    @Entity
    @Table( name = "TB_COM_ROLE" )
    public class Role{
      @Id
      @Column( name = "ROLE_CD" )
      private String roleCd;
    }
    
    @Entity
    @Table( name = "TB_USER_ROLE" )
    @IdClass( UserRoleCompositeKey.class )
    public class UserRole{
      @Id
      @ManyToOne
      @JoinColumn( name = "USER_NO", referencedColumnName = "USER_NO" )  // UserRole Table에 'USER_NO" 컬럼생성(name 속성)
                                                                         // User Table(referenceVariable의 Class Type 참조)의 'USER_NO' 컬럼참조(referencedColumnName 속성)
      private User user;
      
      @Id
      @ManyToOne
      @JoinColumn( name = "ROLE_CD", referencedColumnName = "ROLE_CD" )  // UserRole Table에 'ROLE_CD" 컬럼생성(name 속성)
                                                                         // Role Table(referenceVariable의 Class Type 참조)의 'ROLE_CD' 컬럼참조(referencedColumnName 속성)
      private Role role;
    }
    
    public class UserRoleCompositeKey implements Serializable{
      public UserRoleCompositeKey(){
      }
      
      public UserRoleCompositeKey( Long userNo, String roleCd ){
        this.user = userNo;
        this.role = roleCd;
      }
      
      private Long user;
      private String role;
    }
    
    - JoinColumn
      - name : 외래키로 정의 할, 자신의 테이블의 컬럼명(Column Name) 설정
      - referencedColumnName : 외래키로 참조할, 대상 테이블의 컬럼명 설정
      - foreignKey(DDL): 외래 키 제약조건명 설정(테이블을 생성조건에만 사용가능)
      - unique : @Column의 속성과 동일
      - nullable : @Column의 속성과 동일
      - insertable : @Column의 속성과 동일
      - updatable : @Column의 속성과 동일
      - columnDefinition : @Column의 속성과 동일
      - table : @Column의 속성과 동일
    ~~~

## Spring Validator
  - 참고싸이트: [https://kapentaz.github.io/spring/Spring-Boo-Bean-Validation-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EC%95%8C%EA%B3%A0-%EC%93%B0%EC%9E%90/#](https://kapentaz.github.io/spring/Spring-Boo-Bean-Validation-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EC%95%8C%EA%B3%A0-%EC%93%B0%EC%9E%90/#)
  - 참고싸이트: [https://meetup.toast.com/posts/223](https://meetup.toast.com/posts/223)

## Spring Boot 2.4.x
  - 참고싸이트: [https://spring.io/blog/2020/11/12/spring-boot-2-4-0-available-now](https://spring.io/blog/2020/11/12/spring-boot-2-4-0-available-now)

### Properties
  - Kubernetes의 volume mounts를 지원하기 워해, Properties 설정하는 법이 변경되었다.(사실 무슨 뜻인지 모르겠다..)
  - 많은 특징들이 있는데, 실수할 수 있을만한 내용만 기재하며, 이하 공식문서를 참고하자.
  - yml문서 내에서 "---"으로 profile을 나눌 수 있었는데, properties문서 내에서도 "#---"으로 profile을 나누는게 가능해졌다.
  - The Problem with ConfigFileApplicationListener
    - include는 특정 profile이 적용된 곳에서 사용할 수 없다. 즉, on-profile 과 include 가 공존할 수 없다는 뜻.
    - 이유는.. 아래 싸이트의 링크를 접속 후, "The Problem with ConfigFileApplicationListener" 으로 검색하면 알 수 있다.
    - 참고싸이트: [https://spring.io/blog/2020/08/14/config-file-processing-in-spring-boot-2-4](https://spring.io/blog/2020/08/14/config-file-processing-in-spring-boot-2-4)
  - How to use ?
  ```
  mvn clean compile spring-boot:run -Dspring-boot.run.profile={spring.profiles.group의 key값}
  
  # application.yml
  spring:
    config:
      import: classpath:/example-config.yml
    profiles:
      group:
        default: local, logback, example		# 여기서 "example"은 example-config.yml의 'spring.config.activate.on-profile' 값
        local: local, logback
        dev: dev, logback
        prd: prd
      # include: example				# 여기서 "example"은 example-config.yml의 'spring.config.activate.on-profile' 값
      						# include는 사용하는 문서(여기서는 'application.yml')내에 바로 포함할 때 사용한다.
							# 즉, 해당 문서에서 사용할 경우 default, local, dev, prd모든 proilfe에 example이 포함된다.
	
	
  # application-local.yml
  
  
  # application-dev.yml
  
  
  # application-prd.yml
  
  
  # application-logback.yml
  spring:
    output:
      ansi:
        enabled: always

  logging:
    pattern:
      console: "%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-36logger{36} %clr([%-5level]) : %msg%n"
    level:
      root: info
      org.springframework: info
      com.example.demo: debug
      
      
  # example-config.yml
  # 더이상 반드시, application-{profile}.yml의 형태로 기입하지 않아도 된다.
  # 단, 'spring.config.activate.on-profile'으로 profile명을 기재해야 한다.
  spring:
    config:
      activate:
        on-profile: example
  ```
