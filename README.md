# Event Booking - Customer Android App

Ứng dụng đặt vé sự kiện dành cho người dùng cuối, được phát triển bằng Kotlin với kiến trúc MVVM Clean Architecture.

## 📱 Tính năng chính

- **Đăng ký/Đăng nhập**: Xác thực người dùng với JWT token
- **Quên mật khẩu**: Hỗ trợ khôi phục tài khoản
- **Trang chủ**: 
  - Hiển thị danh sách sự kiện với phân loại theo category
  - Tìm kiếm sự kiện theo tên
  - Load more (phân trang)
  - Polling tự động làm mới dữ liệu
- **Chi tiết sự kiện**: Xem thông tin chi tiết về sự kiện và các loại vé
- **Giỏ hàng**: Thêm vé vào giỏ, quản lý số lượng
- **Đặt hàng**: Thanh toán và tạo đơn hàng
- **Vé của tôi**: Xem lịch sử vé đã mua
- **Thông tin cá nhân**: Xem và cập nhật hồ sơ người dùng
- **Lịch sử đơn hàng**: Theo dõi các đơn hàng đã đặt

## 🏗 Kiến trúc & Công nghệ

### Architecture
- **MVVM Pattern** (Model-View-ViewModel)
- **Clean Architecture** với 3 lớp: Presentation - Domain - Data
- **Repository Pattern**: Abstraction layer giữa domain và data sources
- **Separation of Concerns**: Tách biệt rõ ràng trách nhiệm từng component

### UI State Management
- **UI State Pattern**: `UiState` data class quản lý trạng thái UI
- **UI Event Pattern**: `sealed class UiEvent` xử lý side effects (navigation, toast, dialog)
- **StateFlow**: Reactive state observation từ ViewModel
- **SharedFlow**: One-time event emission

### Tech Stack

| Category | Technology |
|----------|------------|
| **Language** | Kotlin 100% |
| **UI** | XML Layouts, ViewBinding, Material Components |
| **List** | RecyclerView với ListAdapter + DiffUtil |
| **Navigation** | Navigation Component (Fragment-based) |
| **Networking** | Retrofit 2 + OkHttp3 Interceptor |
| **Local Storage** | Room Database (SQLite abstraction) |
| **Session** | SharedPreferences (token, user info) |
| **Async** | Kotlin Coroutines (suspend, flow, viewModelScope) |
| **DI** | Manual Dependency Injection (AppContainer) |
| **Image Loading** | Glide |
| **JSON Parsing** | Gson Converter |

### Key Libraries
```kotlin
- androidx.lifecycle:lifecycle-viewmodel-ktx (ViewModel + scope)
- androidx.lifecycle:lifecycle-runtime-ktx (lifecycleScope)
- com.squareup.retrofit2:retrofit + converter-gson
- com.squareup.okhttp3:logging-interceptor
- org.jetbrains.kotlinx:kotlinx-coroutines-android
- androidx.room:room-runtime + room-ktx
- androidx.recyclerview:recyclerview
- androidx.navigation:navigation-fragment-ktx + navigation-ui-ktx
- com.github.bumptech.glide:glide
```

## 📁 Project Structure

```
app/src/main/java/com/uzuu/customer/
├── core/                      # Core components
│   ├── di/                    # Manual DI container
│   ├── result/                # ApiResult sealed class + safeApiCall
│   └── MyApp.kt               # Application class
├── data/                      # Data layer
│   ├── local/                 # Room database, DAO, Entity
│   ├── remote/                # Retrofit APIs, DTOs, DataSource
│   ├── repository/            # Repository implementations
│   ├── mapper/                # DTO ↔ Domain mapping
│   └── session/               # SharedPreferences manager
├── domain/                    # Business logic layer
│   ├── model/                 # Domain models
│   └── repository/            # Repository interfaces
├── feature/                   # Presentation layer (by feature)
│   ├── start/                 # Auth flow (Login, Register, Forget)
│   ├── middle/                # Main features (Home, Cart, Ticket, Personal)
│   └── main/                  # Main activity & navigation
└── ui/                        # Shared UI components
    └── adapter/               # RecyclerView adapters
```

## 🔑 Điểm nổi bật

### 1. Clean Architecture Implementation
- **Domain layer**: Pure Kotlin, không phụ thuộc Android framework
- **Data layer**: Implement repositories, handle API/DB operations
- **Presentation layer**: ViewModel + Fragment, observe state flows

### 2. Error Handling
```kotlin
sealed class ApiResult<out T> {
    data class Success<T>(val data: T) : ApiResult<T>()
    data class Error(val message: String, val throwable: Throwable?) : ApiResult<Nothing>()
}

suspend fun <T> safeApiCall(block: suspend () -> T): ApiResult<T>
```

### 3. Authentication Flow
- JWT token stored in SharedPreferences
- AuthInterceptor tự động attach token vào mọi request
- Tự động redirect về login khi token hết hạn (401)

### 4. Efficient List Rendering
```kotlin
class EventAdapter : ListAdapter<Event, RecyclerView.ViewHolder>(DIFF) {
    companion object {
        private val DIFF = object : DiffUtil.ItemCallback<Event>() {
            override fun areItemsTheSame(oldItem: Event, newItem: Event) = 
                oldItem.id == newItem.id
            override fun areContentsTheSame(oldItem: Event, newItem: Event) = 
                oldItem == newItem
        }
    }
}
```

### 5. Coroutine Best Practices
- `viewModelScope` cho tất cả coroutine trong ViewModel
- `async/await` cho parallel API calls
- `MutableStateFlow` + `asStateFlow` cho immutable state exposure
- `MutableSharedFlow` với `extraBufferCapacity` cho one-time events

## 🚀 Setup & Running

1. Clone project về máy
2. Mở bằng Android Studio
3. Sync Gradle files
4. Thay đổi `BASE_URL` trong `RetrofitProvider.kt` nếu cần
5. Run trên emulator hoặc device thật (API 24+)

## 📸 Screenshots

//

## 👤 Author

NguyenUzuu - Android Developer

## 📝 License

This project is for portfolio demonstration purposes.
