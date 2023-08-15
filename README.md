# VIEW BINDING

___

## CÁCH LIÊN KẾT UI COMPONENT TRONG LAYOUT VỚI DATA SOURCE TRONG APP THÔNG THƯỜNG

- thông thường để liên kết UI component trong layout với data source trong app ta thường làm theo cách sau:
	- định nghĩa ID cho UI component trong layout
	- khai báo biến thuộc kiểu UI component tương ứng trong chương trình
	- liên kết ràng buộc 2 thành phần trên bằng method thuộc UI Framework
- Ví dụ:
- trong layout __activity_main.xml__
```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <!-- định nghĩa id cho View Button trong layout để có thể gọi đến trong chương trình -->
    <Button
        android:id="@+id/btn_show_toast"
        android:text="CLICK ME"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"/>

</androidx.constraintlayout.widget.ConstraintLayout>
```
- trong chương trình __MainActivity.java__
```java
public class MainActivity extends AppCompatActivity {
	// khai báo biến Button
    Button btnShowToast;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // ràng buộc biến Button vào View trong layout
        btnShowToast = findViewById(R.id.btn_show_toast);

        btnShowToast.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Toast.makeText(MainActivity.this, "Đã CLICK", Toast.LENGTH_LONG).show();
            }
        });
    }
}
```

___

## CÁCH SỬ DỤNG VIEW BINDING LIÊN KẾT UI COMPONENT TRONG LAYOUT VỚI DATA SOURCE TRONG APP

- thông thường để làm việc với các __View__ trên __Layout__ ta cần xác định thuộc tính __id__ thông qua method __findViewById()__, với cách này
    - quá trình xây dựng ứng dụng tốn thời gian
    - không được kiểm tra tại thời điểm biên dịch, nên khi xác định sai __id__ sẽ dẫn đến ứng dụng bị crash
- để khắc phục những nhược điểm trên, Google cung cấp giải pháp __View Binding__

- ví dụ ta có file layout __activity_main.xml__ như sau
```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/btn_first"
        android:text="@string/click_me"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"/>

    <Button
        android:id="@+id/btn_second"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="@string/second"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@id/btn_first" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

### CÁC BƯỚC THIẾT LẬP VÀ SỬ DỤNG VIEW BINDING

- Khai báo sử dụng __View Binding__ trong __build.gradle module:app__, trong thẻ __android__ ta thêm vào đoạn code bật __viewBinding__ là __true__, sau đó click __Sync Now__
```js
android {
    // 

    buildFeatures {
        viewBinding true
    }
}
```

> nếu muốn 1 __layout__ nào đó bỏ qua quá trình tạo class Binding (không sử dụng View Binding) ta thêm thuộc tính sau vào __root view__ của __layout__ đó
>> <LinearLayout
>>        ...
>>       tools:viewBindingIgnore="true" >
>>    ...
>> </LinearLayout>

- sau khi bật được __viewBinding__ thì __Android__ sẽ tự sinh ra các file class Binding cho các __layout__ có trong ứng dụng
- ví dụ: file __layout__ __activity_main.xml__ thì sẽ có file Binding là __ActivityMainBinding.java__
- ta sẽ khai báo đối tượng __Binding__ này trong file java tương ứng với layout
- __MainActivity.java__
```java
public class MainActivity extends AppCompatActivity {
    private ActivityMainBinding mActivityMainBinding;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        // nơi khởi tạo đối tượng Binding
        setContentView(R.layout.activity_main);
}
```

> Lưu ý: TA CÓ THỂ KHÔNG CẦN KHAI BÁO BIỂN ĐỂ QUẢN LÝ LIÊN KẾT __id__ các View trên Layout

- sau khi khai báo, ta khởi tạo đối tượng Binding sau ``super.onCreate(savedInstanceState);`` đồng thời chỉnh sửa lại method ``setContentView()``
- __MainActivity.java__
```java
public class MainActivity extends AppCompatActivity {
    private ActivityMainBinding mActivityMainBinding;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        mActivityMainBinding = ActivityMainBinding.inflate(getLayoutInflater());
        setContentView( mActivityMainBinding.getRoot());

        // hoặc có thể viết tách riêng đối tượng View dùng để set content cho giao diện
//        mActivityMainBinding = ActivityMainBinding.inflate(getLayoutInflater());
//        View view = mActivityMainBinding.getRoot();
//        setContentView(view);        
}
```

- sử dụng đối tượng __Binding__ đã khởi tạo để gọi đến __id__ của các __View__ trên __Layout__ và có thể thực hiện thao tác trực tiếp các method
- __MainActivity.java__
```java
public class MainActivity extends AppCompatActivity {
    private ActivityMainBinding mActivityMainBinding;

    Button btn;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        mActivityMainBinding = ActivityMainBinding.inflate(getLayoutInflater());
        setContentView(mActivityMainBinding.getRoot());

        // cách gần giống sử dụng findViewById()
        btn = mActivityMainBinding.btnFirst;
        btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Toast.makeText(MainActivity.this, "FIRST BUTTON", Toast.LENGTH_SHORT).show();
            }
        });

        // cách hoàn toàn sử dụng đối tượng Binding
        mActivityMainBinding.btnSecond.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Toast.makeText(MainActivity.this, "SECOND BUTTON", Toast.LENGTH_SHORT).show();
            }
        });
    }
}
```

- hoặc có thể khai báo đối tượng View sau đó sử dụng đối tượng Binding gọi đến id để gán cho đối tượng View để thao tác gọn hơn, nhưng cách này cũng gần giống như cách sử dụng __findViewById()__

> khi ta sử dụng View Binding thì class Binding của các Layout tương ứng sẽ được sinh ra
>> các class này sẽ quản lý __id__ của các view trong layout tương ứng.
>> khi sử dụng đối tượng Binding, chỉ cần nhập dấu ``.`` phía sau đối tượng đó, thì danh sách id mà nó quản lý sẽ được xổ xuống cho ta chọn.
>> về quy tắc đặt id cho View trên Layout.
>>> phân biệt UPPERCASE và lowercase.
>>> dấu gạch nối ``_`` sẽ bị loại bỏ, những ký tự đầu sau dấu ``_`` sẽ được viết UPPERCASE.
>>> vì vậy tránh trường hợp khi sau khi bỏ đi dấu ``_`` ta có 2 id giống nhau trên layout, lúc này đối tượng Binding sẽ liên kết bị lỗi, xử lý logic sẽ lẫn lộn