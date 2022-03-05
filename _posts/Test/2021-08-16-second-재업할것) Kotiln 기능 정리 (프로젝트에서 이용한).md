---
title:  "재업할것) Kotlin 기능 정리 (프로젝트에서 이용한)"
categories: [Kotlin, Revise]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---

# Kotlin 기능 정리 (프로젝트에서 이용한)

### onCreate와 onCreateView와 onViewCreated 구별

**onCreate**부분은 Fragment가 생성될때 호출되는 부분이고, **onCreateView**는 onCreate 이후에 화면을 구성할때 호출하는 부분이고, **onViewCreated**는 onCreateView에서 inflater를 이용한 view를 불러와서 return 하여서 onViewCreated 안에서 **view?**.findViewById~~ 해서 사용하는것으로 구성되어 있다.

#### 바인딩 ( onViewCreated내부) - 지역

여기선 생성자로 리턴한 **view**를 이용하기 위해 **onViewCreated** 메소드 이용 

    Fragment(R.layout.fragment_home) )
        => val binding = FragmentHomeBinding.bind(view)

#### 바인딩 ( 외부 ) - 전역

전역 바인딩 하려면 lateinit으로 선언! https://flow9.net/bbs/board.php?bo_table=android&wr_id=27 참고!
onCreateView 오버로딩해서 binding 적용필요 

    class ....
    lateinit var binding:FragmentHomeBinding
    
    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        binding = FragmentHomeBinding.inflate(inflater, container,false)
        return binding.root
    }
    
또는 Activity에서 하는것과 마찬가지로

    class ....
    val binding by lazy { ActivityBloodPressureBinding.inflate(layoutInflater)}

## Room

#### 데이터 구성 예시

**1.User.kt**

    @Entity
    data class User(
     var date: String,
     var bloodPressure: String
    ) {
     @PrimaryKey(autoGenerate = true) var id : Int = 0
    }

**2.UserDao.interface**

    @Dao
    interface UserDao {
    @Query("SELECT * FROM User")
    fun getAll(): List<User>
        
    // @Query("SELECT count FROM Todo")
    // fun getCount(): Int
    
    @Insert
    fun insert(todo: User)
    @Update
    fun update(todo: User)
    
    @Delete
    fun delete(todo: User)
    
    @Query("DELETE FROM User ")
    fun deleteAll()
    }

**3.UserDatabase.kt** - 함수 만들어줘서 코드가 길지, 함수 없다면 코드 매우 짧음.

```
@Database(entities = [User::class], version = 1)
abstract class UserDatabase : RoomDatabase() {
abstract fun userDao(): UserDao

 companion object {
     private var instance: UserDatabase? = null
@Synchronized
 fun getInstance(context: Context): UserDatabase? {
     if (instance == null) {
         synchronized(UserDatabase::class){
             instance = Room.databaseBuilder(
                 context.applicationContext,
                 UserDatabase::class.java,
                 "database-name"
             ).allowMainThreadQueries() // 여기서도 임시방편
                 .build()
         }
     }
     return instance
 }
 }
}
```

#### room 데이터 이용법(만드는것 말고 사용법)

    val db : UserDatabase? = UserDatabase.getInstance(requireContext())

=> UserDatabase.kt에 getInstance라는 함수를 만들어서 간단히 사용했음. 안만들었다면,

            val db = Room.databaseBuilder(
                applicationContext,
                UserDatabase::class.java, "database-name" // 여기가 테이블 이름
            ).allowMainThreadQueries() // 임시로 허용시킨 Thread (실제론 비동기로 돌리는게 좋음)
                .build()
#### Context - 통로

**this** -> 액티비티에서 사용
**requireContext()** -> fragment에서 사용
**binding.root.context** -> 바인딩한곳 어디든 사용

#### 금일 날짜 가져오기

```
        var date = LocalDateTime.now(ZoneId.of("Asia/Seoul")).format(
            DateTimeFormatter.ofPattern(
                "MM월dd일"
            )
        ).toString()
=> yyyy-MM-dd HH:mm:ss 요런식(참고 요일 : E) ex) "E요일"
한국어로 가져오고 싶으면 "E요일" , Locale.KOREAN 적용
```
#### 반복문에 이름지정해서 탈출하고 싶을때

```
one@while(true) {
	break@one		
}
```

#### Invisible, Gone - 숨기기, 나타내기

**isVisible** 사용시 gone역할(완전 빈공간 만듬 즉 걍 없어진다고 보면 됨) **isInvisible** 사용시 그 공백은 유지해준체 빈공간 만듬

```
binding.bloodPressureCircleProgressBar.isInvisible = true // 안보이게 하는것.
binding.bloodPressureProgressBar.isVisible = true // 보이게 하는것.
```

#### Drawable같이 경로지정해서 적용시키는법 - 경로로 값 지정

```
private val drawableOne: Drawable = context?.getDrawable(R.drawable.ic_custom_draw1)!!
```

#### 상태바 색상 변경 - 바로 값 지정

```
window.statusBarColor = Color.parseColor("#DEF8FF") // color를 context이용해서 불러오는것 말고, 직접 parseColor하는 방법
```

#### spinner 설정

```
val adapter = ArrayAdapter(this,R.layout.support_simple_spinner_dropdown_item,spinnerItemList)
binding.spinner.adapter = adapter // spinner 어뎁터를 연결
binding.spinner.onItemSelectedListener = object : AdapterView.OnItemSelectedListener{
            override fun onItemSelected( // 선택된값
                parent: AdapterView<*>?,
                view: View?,
                position: Int,
                id: Long
            ) {
                positions = position // 여기선 전역으로 index값 저장해서 나가서 사용
            }
            override fun onNothingSelected(parent: AdapterView<*>?) { // 아무것도 선택안했을시 보여주는 값? 비워두기
            }
        }
```

### MaterialCalendar 설정

**month**생각 잘하기. 0~11인 부분이 있음.
**decorate**와 **shouldDecorate** 메소드를 오버라이딩 할 수 있다.
shouldDecorate 메소드 에서 리턴 트루일때만 decorate 실행.
=> 여기서 날짜에 데이터 입힐수 있는것.

```
binding.calendar.state().edit()
.setFirstDayOfWeek(Calendar.SUNDAY) // 일 월 화 수 목 금 토
.setMaximumDate(CalendarDay.from(crCalendarDay.year, crCalendarDay.month, 31))
.commit()
binding.calendar.topbarVisible = false // toolbar 제거
binding.calendar.isDynamicHeightEnabled = true // 다이나믹하게 높이지정
binding.calendar.setHeaderTextAppearance() // title 스타일 지정 => style로 지정해야함
binding.calendar.setWeekDayTextAppearance() // 요일 스타일 지정
binding.calendar.setDateTextAppearance() // 일 스타일 지정
binding.calendar.foregroundGravity = Gravity.TOP

// 처음 달력 title format
binding.calendar.setTitleFormatter({
val simpleDateFormat = SimpleDateFormat("MM월", Locale.KOREA)
simpleDateFormat.format(startTimeCalendar.time)
})
// 달력 넘길때 format
binding.calendar.setOnMonthChangedListener { widget, date ->
binding.calendar.setTitleFormatter({
val simpleDateFormat = SimpleDateFormat("MM월", Locale.KOREA)
simpleDateFormat.format(date.date)
})
initChangeMonthCalendar(date) // 캘린더 달 변경때마다 리사이클러뷰 재설정
}
// Decorator 적용
binding.calendar.addDecorators( // Decorator에 s를 붙여서 여러 Decorator를 추가함.
defaultDecorator,
}
view?.setDaysDisabled(true) 날짜 클릭불가(view를 날짜로 봤을때)
view?.setBackgroundDrawable(drawableFive) // 말그대로
```

#### 해당 달의 최대 날짜 구하는 함수 getActualMaximum

```
var cal = Calendar.getInstance()
cal.set(currentYear,currentMonth-1,currentDay)
var maxDayOfMonth = cal.getActualMaximum(Calendar.DAY_OF_MONTH) // 최대날짜 설정
```

#### addSpan이용 (textAppearence와 사용가능한게 비슷)

style 지정할때 style=로 지정하지않고 TextAppearance=같은걸로 지정할시 style의 하위호환으로 생각하면된다. 안되는 속성많다. 적용할때 보통 style.xml만들어서 적용한다. 아래는 addSpan

```
view?.addSpan(object:ForegroundColorSpan(Color.WHITE){}) // 텍스트 흰색으로 변경
view?.addSpan(object:RelativeSizeSpan(2.4f){}) // 텍스트 크기 변경
view?.addSpan(object:StyleSpan(Typeface.BOLD){}) // 텍스트 진하게
```

### 리사이클러뷰 - RecyclerView ( 2중 중첩 )

https://android-dev.tistory.com/59 => UI형성
https://kumgo1d.tistory.com/44 => RecyclerView에서 Intent사용법

#### 기본 recyclerView 만드는 법

adapter, data class, holder로 구성해서 만든다.

자세히...

#### 이중(중첩) recyclerVeiw 만드는 법

  1. recyclerViewA를 만든다
  2. recyclerViewA 어답터에서 ViewHolder에 recyclerViewB를 정의해준다.
     아이템 클릭시 삭제부분 -> https://mechacat.tistory.com/7

### alarmManager 간단한 예시

**매니피스트에 처음 리시브 해야함**

#### alarmManager 사용

    val calender = Calendar.getInstance().apply {
                            set(Calendar.HOUR_OF_DAY, hour) // 시간 설정
                            set(Calendar.MINUTE, minute) // 분 설정
                            // 지나간 시간의 경우 다음날 알람으로 울리도록 함으로써 그냥 안울리게 하기.
                            if (before(Calendar.getInstance())) {
                                add(Calendar.DATE, 1) // 하루 더하기
                            }
                        }
                        //알람 매니저 가져오기.
                        val alarmManager = getSystemService(Context.ALARM_SERVICE) as AlarmManager
    val intent = Intent(this, AlarmReceiver::class.java)
                    intent.putExtra("key", "approximately")
                    intent.putExtra("code", codeOne)
                    val pendingIntent = PendingIntent.getBroadcast(
                        this,
                        codeOne, // id값이라 생각하고 이걸로 다수의 알람 등록
                        intent,
                        PendingIntent.FLAG_UPDATE_CURRENT // 있으면 새로 만든거로 업데이트
                    )
                    alarmManager.setAndAllowWhileIdle( // 정시에 반복 ( 잠자기 모드에서도 허용 )
                        AlarmManager.RTC_WAKEUP, // RTC_WAKEUP : 실제 시간 기준으로 wakeup , ELAPSED_REALTIME_WAKEUP : 부팅 시간 기준으로 wakeup
                        calender.timeInMillis, // 언제 알람이 발동할지.
                        pendingIntent
                    )
                    codeOne++
    private fun cancelAlarm(code : Int) {
        val pendingIntent = PendingIntent.getBroadcast(
            this,
            code, // code
            Intent(this, AlarmReceiver::class.java),
            PendingIntent.FLAG_NO_CREATE) // 있으면 가져오고 없으면 안만든다 (null)
        pendingIntent?.cancel() // 기존 알람 삭제
    }

#### AlarmReceiver 세팅

    class AlarmReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent) {
        var name : String = intent.getStringExtra("key").toString()
        var codeOne : Int = intent.getIntExtra("code", 0)
        createNotificationChannelApproximately(context, codeOne) // 채널 생성
        notifyNotificationApproximately(context, codeOne) // 알림
    }
    private fun createNotificationChannelApproximately(context: Context, code : Int) {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            val notificationChannel = NotificationChannel(
                "${code}",
                "약 복용 알람",
                NotificationManager.IMPORTANCE_HIGH
            )
    
            NotificationManagerCompat.from(context)
                .createNotificationChannel(notificationChannel)
        }
    }
    }
### SharedPreference 설정

바로 사용하거나 따로 코틀린 파일에 지정해서 사용하는법이 있는데 따로 지정이 사용 효율적임.

여기 홈페이지 방법 이용할거면 매니피스트에 처음 name지정해야함. -> 따로 파일 지정방법임.
https://riapapa-collection.tistory.com/41
https://leveloper.tistory.com/133
핵심은 MyAppllication가 Main이 onCreate되기전 먼저 실행되어야 하기때문에 ManiFest에 name을 추가해준다.

#### MyApplication.kt

```
class MyApplication : Application() {
    companion object {
        lateinit var prefs: PreferenceUtil
    }
    override fun onCreate() {
        prefs = PreferenceUtil(applicationContext)
        super.onCreate()
    }
}
```

#### PreferenceUtil.kt

```
class PreferenceUtil(context: Context) {
    private val prefs: SharedPreferences = context.getSharedPreferences("prefs_name", Context.MODE_PRIVATE)
    fun getString(key: String, defValue: String): String { // get함수는 불러오기
        return prefs.getString(key, defValue).toString()
    }
    fun setString(key: String, str: String) { // set함수는 저장
        prefs.edit().putString(key, str).apply()
    }
}
```

