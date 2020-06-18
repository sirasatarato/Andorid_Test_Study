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

