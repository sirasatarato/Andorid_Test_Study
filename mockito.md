# Mockito
> 유닛 테스트를 위한 Java mocking framework  
mockito를 사용하면 대부분의 로직을 검증 할 수 있다.

## dependency
```
testImplementation 'junit:junit:4.12' // junit
androidTestImplementation("org.mockito:mockito-android:2.24.5")
testImplementation 'org.mockito:mockito-core:2.28.2'
```

- kotlin을 사용할 경우
```
android {
    ...
    testOptions {
        unitTests.returnDefaultValues = true
    }
}

testImplementation 'org.mockito:mockito-inline:3.1.0'
// testImplementation 'org.mockito:mockito-core:2.28.2'
```

## 예제
```
class Example {
    fun getInt() : Int = 0
    fun getStr(i: Int) : String = ""
}

//--------------------------------------------------------------------------

@RunWith(MockitoJUnitRunner::class)   // 1
class ExampleUnitTest {

    @Test
    fun example1() {
        val example = Mockito.mock(Example::class.java)   // 2

        Mockito.`when`(example.getInt()).thenReturn(100)   // 3
        Mockito.`when`(example.getStr(100)).thenReturn("string")    //  4

        assertEquals(100, example.getInt())    // 5
        assertEquals("string", example.getStr(example.getInt()))    // 6
    }
}
```

1. Mockito를 사용하려면 @RunWith(MockitoJUnitRunner::class) 추가
    - 안드로이드에서 Mockito는 @RunWith(AndroidJUnit4::class)
2. Example 클래스를 mocking하여 mock 객체 생성
3. 이 객체의 getInt()가 호출되었을 때 100을 리턴하도록 설정
4. 이 객체의 getStr(100)가 호출되었을 때 "string"를 리턴하도록 합니다.
    - 인자가 100일 때만 "string"을 반환함
    - 인자 상관없이 100을 반환하고 싶다면 anyInt()를 넣는다.
    - anyType()은 인자로 어떤 값을 전달하든 상관이 없거나 정확한 값을 모를 경우 사용한다.
    - 단, 인자 타입에 맞는 메소드를 사용한다.
5. getInt()가 100을 리턴하는지 확인
6. getStr(100)이 "string"을 리턴하는지 확인

**팁**  
만약 org.mockito.Mockito.*으로 import하면 Mockito를 아래처럼 호출할 필요가 없다.
```
import org.mockito.Mockito.*

val example = mock(Example::class.java)
`when`(example.getId()).thenReturn(100)
`when`(example.getStr(100)).thenReturn("string")
```

## mock 객체
#### 생성하는 법
1. test메소드 안에 mock() 메소드로 객체를 생성
2. 외부에서 해당 객체로 선언된 변수 위에 @Mock을 추가 
```
@Test
fun example(){
    val p = mock(Person::class.java)
}

//--------------------------------------------------------------------------

@Mock
var p: Person? = null

@Test
fun example() {
    MockitoAnnotations.initMocks(this)
    assertTrue(p != null)
}
```

## When
> when()은 특정 mock 객체부터 특정 조건을 지정하도록 사용하는 메소드

아래 예제는 자바로 코딩되었습니다.
```
public List<String> getList(String name, int age){ // do something code }

when(mockIns.getList(anyString(), anyInt()))
    .thenReturn(
        new ArrayList<String>(){
            { this.add("JDM"); this.add("BLOG"); }
        }
    );
```
**Tip**  
만약 특정 값을 넣어야 한다면 eq() 메소드를 활용한다.
```
when(mockIns.getList(eq("JDM"), anyInt()))
```

#### kotlin에서의 when
> kotlin에는 when이라는 자바로 치자면 switch같은 예약어가 있기 때문에  
kotlin에서 mockito의 when을 ''안에 감싸서 사용해야 한다.

## doThrow
> doThrow()는 예외를 던지고 싶을 때 활용한다.

아래 예제는 객체 안의 name을 특정 문자열로 지정했을시 예외가 발생한다.
```
@Test(expected = IllegalArgumentException.class)
fun example(){
    val p = mock(Person::class.java)
    doThrow(IllegalArgumentException()).`when`(p).setName("JDM")
    p.setName("JDM")
}
```
## doNothing
> void(또는 Unit)로 선언된 메소드에 when()를 사용할 때 doNothing()을 사용한다.
```
@Test
fun example(){
    val p = mock(Person::class.java)
    doNothing().`when`(p).setAge(anyInt())
    p.setAge(20)
    verify(p).setAge(anyInt())
}
```

## Exception
> 어떤 메소드를 호출했을 때 Exception이 발생되도록 설정 할 수 있다.
```
@Test
fun example() {
    val example = mock(Example::class.java)

    `when`(example.getStr(anyInt())).thenReturn("string")
    assertEquals("string", example.getStr(10))

    `when`(example.getStr(20)).thenThrow(IllegalStateException("Exception happened!"))    // 1

    try {
        example.getStr(20)
        fail()    // 2
    } catch (e: IllegalStateException) {
        assertEquals(e.message, "Exception happened!")    // 3
    }
}
```

1. 20을 인자로 getStr 함수를 호출할 경우 예외 발생하도록 설정
2. junit의 실패했다고 알리는 메소드
3. 에러 메시지와 같은지 단언

## verify
> verifiy()는 해당 구문이 호출 되었는지를 체크하는 메소드
```
@Test
fun example(){
    val p = mock(Person::class.java)
    val name = "JDM"
    p.setName(name)

    // n번 호출했는지 체크
    verify(p, times(1)).setName(anyString())            // success

    // 호출 안했는지 체크
    verify(p, never()).getName()                        // success
    verify(p, never()).setName("ETC")                   // success
    verify(p, never()).setName("JDM")                   // fail

    // 최소한 1번 이상 호출했는지 체크
    verify(p, atLeastOnce()).setName(anyString())                 // success

    // 2번 이하 호출 했는지 체크
    verify(p, atMost(2)).setName(anyString())                     // success

    // 2번 이상 호출 했는지 체크
    verify(p, atLeast(2)).setName(anyString())                    // fail

    // 지정된 시간(millis)안으로 메소드를 호출 했는지 체크
    verify(p, timeout(100)).setName(anyString())                  // success

    // 지정된 시간(millis)안으로 1번 이상 메소드를 호출 했는지 체크
    verify(p, timeout(100).atLeast(1)).setName(anyString())       // success
}
```

## InjectMocks
> 클래스 내부에 다른 클래스를 포함하는 경우 외부에서 주입할 수 있도록 @InjectMocks 어노테이션을 사용
```
public class AuthService{
    private var dao: AuthDao? = null

    fun isLogin(id: String): Boolean{
        val isLogin = dao.isLogin(id)
        if( isLogin ){
            // some code...
        }
        return isLogin
    }
}
class AuthDao {
    fun isLogin(id: String): Boolean { //some code ... }
}

//--------------------------------------------------------------------------

@Mock
lateinit var dao: AuthDao

@InjectMocks
lateinit var service: AuthService

@Test
fun example(){
    MockitoAnnotations.initMocks(this)
    `when`(dao.isLogin("JDM")).thenReturn(true)
    assertTrue(service.isLogin("JDM"))
    assertTrue(!service.isLogin("ETC"))
}
```

## Spy
> Spy로 선언된 목 객체는 목 메소드 stub를 별도로 만들지 않고 실제 메소드가 호출
```
val p = spy(Person::class.java)
val p1 = spy(Person())
@Spy
var p2: Person? = null
```