# JUnit
> 자바 계열 언어로 유닛 테스트를 할 수 있게 도와주는 테스팅 도구

## gradle
```
    dependencies {
        // Required -- JUnit 4 framework
        testImplementation 'junit:junit:4.12'
        // Optional -- Robolectric environment
        testImplementation 'androidx.test:core:1.0.0'
        // Optional -- Mockito framework
        testImplementation 'org.mockito:mockito-core:1.10.19'
    }
```

## annotation
- @Test: 해당 메소드가 테스트할 메소드임을 알림
- @Before: 매번 테스트하기 전에 호출되는 메소드
- @After: 매번 테스트가 끝난 후 에 호출되는 메소드
- @BeforeClass: 테스트 클래스에서 테스트하기 전에 딱 한 번만 호출되는 메소드
- @AfterClass: 테스트 클래스에서 테스트가 끝난 후에 딱 한 번만 호출되는 메소드
- @RunWith: 지정한 러너를 사용, 안드로이드에서 테스트 모음이 JUnit3, 4 라이브러리의 혼합에 종속된 경우 @RunWith(AndroidJUnit4::class)을 추가
- @Rule: 규칙을 지정
- @Test(exception=Exception.class): 해당 테스트에서 기대하는 예외를 지정
- @Test(timeout=10000): 테스트 통과시간을 지정, 타임아웃을 지정
- @RequiresDevice: 에뮬레이터를 사용하지 않고 기기만 사용
- @SdkSupress: minSdkVersion을 지정
- @SmallTest, @MediumTest, @LargeTest: 테스트 성격을 구분하여 테스트

## assert
- assertEquals: 같은 객체인지 단언
- assertArrayEquals: 같은 배열인지 단언
- assrtTrue: 참인지 단언
- assertFalse: 거짓인지 단언
- assertNull; null인지 단언
- assertNotNull: null이 아닌지 단언
- assertSame: 두 변수가 같은 인스턴스를 가리키고 있는지 단언
- assertNotSame: 두 변수가 다른 인스턴스를 가리키고 있는지 단언
- assertThat: 주어진 객체가 특정 조건을 만족한지 단언

## Rule
> AndroidX 테스트에는 AndroidJUnitRunner와 함께 사용되는 JUnit 규칙 세트가 포함되어 있다.

- ActivityTestRule
```
    @RunWith(AndroidJUnit4::class.java)
    @LargeTest
    class MyClassTest {
        @get:Rule
        val activityRule = ActivityTestRule(MyClass::class.java)

        @Test fun myClassMethod_ReturnsTrue() { ... }
    }
```
- ServiceTestRule
```
    @RunWith(AndroidJUnit4::class.java)
    @MediumTest
    class MyServiceTest {
        @get:Rule
        val serviceRule = ServiceTestRule()

        @Test fun testWithStartedService() {
            serviceRule.startService(
                Intent(ApplicationProvider.getApplicationContext<Context>(),
                MyService::class.java))

            // Add your test code here.
        }

        @Test fun testWithBoundService() {
            val binder = serviceRule.bindService(
                Intent(ApplicationProvider.getApplicationContext(),
                MyService::class.java))
            val service = (binder as MyService.LocalBinder).service
            assertThat(service.doSomethingToReturnTrue()).isTrue()
        }
    }
```

#### Add Robolectric
> 로컬 JVM 또는 실제 기기에서 실제 Android 프레임워크 코드 및 가짜 네이티브 프레임워크 코드를 실행
```
    android {
        // ...
        testOptions {
            unitTests.includeAndroidResources = true
        }
    }

//--------------------------------------------------------------------------

    import android.content.Context
    import androidx.test.core.app.ApplicationProvider
    import com.google.common.truth.Truth.assertThat
    import org.junit.Test

    private const val FAKE_STRING = "HELLO_WORLD"

    class UnitTestSample {
        val context = ApplicationProvider.getApplicationContext<Context>()

        @Test fun readStringFromContext_LocalizedString() {
            // Given a Context object retrieved from Robolectric...
            val myObjectUnderTest = ClassUnderTest(context)

            // ...when the string is returned from the object under test...
            val result: String = myObjectUnderTest.getHelloWorldString()

            // ...then the result should be the expected one.
            assertThat(result).isEqualTo(FAKE_STRING)
        }
    }
    
```