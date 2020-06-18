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

