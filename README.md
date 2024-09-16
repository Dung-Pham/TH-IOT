
## 3.2 
***Viết chương trình đọc dữ liệu khoảng cách gửi lên từ cảm biến siêu âm US-015 và hiển thị kết quả lên màn hình Serial Monoitor của máy tính***


#### Bước 1: Cài đặt thư viện

1. Mở Arduino IDE.
2. Vào **Sketch > Include Library > Manage Libraries...**
3. Tìm kiếm và cài đặt thư viện **DHT sensor library by Adafruit**.
4. Thêm thư viện **Wire.h**.

####  Kết nối phần cứng

- DATA (chân tín hiệu của DHT11) nối với IO 04 của ESP32.
- Cấp nguồn cho cảm biến DHT11 bằng cách kết nối chân VCC và GND.

#### Bước 2: Code chương trình

```cpp
#include <DHT.h>

// Định nghĩa chân kết nối và loại cảm biến DHT
#define DHTPIN 4      // Chân DATA của DHT11 kết nối với chân IO 04 của ESP32
#define DHTTYPE DHT11 // Sử dụng cảm biến DHT11

DHT dht(DHTPIN, DHTTYPE);  // Tạo đối tượng DHT với chân kết nối và loại cảm biến

void setup() {
  Serial.begin(115200);  // Khởi tạo giao tiếp Serial với tốc độ baudrate là 115200
  dht.begin();           // Bắt đầu sử dụng cảm biến DHT
}

void loop() {
  // Đọc dữ liệu nhiệt độ và độ ẩm từ cảm biến
  float humidity = dht.readHumidity();    // Đọc độ ẩm
  float temperature = dht.readTemperature(); // Đọc nhiệt độ theo độ C

  // Kiểm tra nếu đọc dữ liệu thất bại
  if (isnan(humidity) || isnan(temperature)) {
    Serial.println("Lỗi đọc dữ liệu từ cảm biến DHT11!");
    return;
  }

  // Hiển thị nhiệt độ và độ ẩm lên Serial Monitor
  Serial.print("Nhiệt độ: ");
  Serial.print(temperature);
  Serial.println(" °C");

  Serial.print("Độ ẩm: ");
  Serial.print(humidity);
  Serial.println(" %");

  // Đợi 2 giây trước khi đọc lại
  delay(2000);
}
```
#### Nạp chương trình 
- Sau khi viết xong chương trình, chọn đúng board ESP32 và cổng COM tương ứng trên Tools.
- Nhấn Upload để biên dịch và nạp chương trình vào ESP32.
- Mở Serial Monitor để xem dữ liệu nhiệt độ và độ ẩm được đọc từ cảm biến DHT11.
- Chương trình này sẽ đọc và hiển thị giá trị nhiệt độ và độ ẩm từ cảm biến DHT11 cứ mỗi 2 giây một lần trên Serial Monitor.


#### Bước 4: Thay đổi chân kết nối và tốc độ cổng Serial
Bạn có thể thay đổi chân kết nối và tốc độ cổng Serial theo yêu cầu. Dưới đây là phiên bản sửa đổi của chương trình, sử dụng chân IO 5 của ESP32 và thay đổi tốc độ Serial thành 9600 baud:

```cpp
#include <DHT.h>

// Thay đổi chân kết nối và loại cảm biến DHT
#define DHTPIN 5      // Chân DATA của DHT11 kết nối với chân IO 05 của ESP32
#define DHTTYPE DHT11 // Sử dụng cảm biến DHT11

DHT dht(DHTPIN, DHTTYPE);  // Tạo đối tượng DHT với chân kết nối và loại cảm biến

void setup() {
  Serial.begin(9600);  // Thay đổi tốc độ giao tiếp Serial thành 9600
  dht.begin();         // Bắt đầu sử dụng cảm biến DHT
}

void loop() {
  // Đọc dữ liệu nhiệt độ và độ ẩm từ cảm biến
  float humidity = dht.readHumidity();    // Đọc độ ẩm
  float temperature = dht.readTemperature(); // Đọc nhiệt độ theo độ C

  // Kiểm tra nếu đọc dữ liệu thất bại
  if (isnan(humidity) || isnan(temperature)) {
    Serial.println("Lỗi đọc dữ liệu từ cảm biến DHT11!");
    return;
  }

  // Hiển thị nhiệt độ và độ ẩm lên Serial Monitor
  Serial.print("Nhiệt độ: ");
  Serial.print(temperature);
  Serial.println(" °C");

  Serial.print("Độ ẩm: ");
  Serial.print(humidity);
  Serial.println(" %");

  // Đợi 2 giây trước khi đọc lại
  delay(2000);
}
```

- **Chân DHT11**: Bây giờ kết nối chân DATA của DHT11 với IO 5 thay vì IO 4.
- **Tốc độ Serial**: Tốc độ giao tiếp qua Serial Monitor đã được thay đổi thành 9600 baud.

### Nhận xét
--------------------------------------------------------------------------------------------------------------------------------
- Sau khi thay đổi chân kết nối và tốc độ Serial, chương trình hoạt động đúng như mong đợi. Dữ liệu nhiệt độ và độ ẩm được cập nhật mỗi 2 giây, và hiển thị rõ ràng trên Serial Monitor. Tốc độ 9600 baud cho kết quả ổn định và dễ theo dõi.
--------------------------------------------------------------------------------------------------------------------------------

### Nguyên lý cơ bản của giao thức truyền tin **One-Wire**
--------------------------------------------------------------------------------------------------------------------------------
- **One-Wire** là giao thức truyền thông dữ liệu **đơn giản** sử dụng **một dây tín hiệu** để truyền thông giữa thiết bị chủ (master) và thiết bị con (slave), ngoài dây **đất (GND)**. Giao thức này được phát triển bởi **Dallas Semiconductor** (nay thuộc Maxim Integrated).
  
- **Cấu trúc giao tiếp**: Giao thức chỉ yêu cầu **một dây duy nhất** để truyền cả dữ liệu và đồng hồ (clock), điều này làm giảm số lượng dây cần thiết. Thêm vào đó, nó có thể truyền thông tin với nhiều thiết bị cùng lúc trên cùng một dây, nhờ sử dụng địa chỉ 64-bit duy nhất cho mỗi thiết bị.

- **Quá trình truyền dữ liệu**:
  1. **Thiết bị chủ** sẽ gửi các tín hiệu điều khiển đến thiết bị con để bắt đầu giao tiếp.
  2. Dữ liệu được truyền dưới dạng các chuỗi bit đơn lẻ theo thời gian, thường với tốc độ thấp (khoảng 16 kbps).
  3. **One-Wire** có thể hỗ trợ cả truyền thông hai chiều (đọc và ghi dữ liệu) trên cùng một dây.
  
- **Ưu điểm**:
  - **Tiết kiệm dây**: Chỉ cần một dây dữ liệu và một dây đất.
  - **Đơn giản hóa thiết kế**: Ít phức tạp và dễ triển khai.
  
- **Nhược điểm**:
  - **Tốc độ truyền chậm**: Không phù hợp cho các ứng dụng yêu cầu tốc độ cao.
  - **Khoảng cách ngắn**: Giao tiếp chỉ có thể thực hiện trong khoảng cách ngắn.

--------------------------------------------------------------------------------------------------------------------------------

## 3.3
***Viết chương trình đọc dữ liệu khoảng cách gửi lên từ cảm biến siêu âm US-015 và hiển thị kết quả lên màn hình Serial Monoitor của máy tính***

### Bước 1: Kết nối dây
Kết nối chân cảm biến siêu âm US-015 với ESP32 theo bảng sau:

| Chân của US-015 | Chân của ESP32 |
|-----------------|----------------|
| TRIGGER         | IO 02          |
| ECHO            | IO 04          |

### Bước 2: Chương trình đọc dữ liệu khoảng cách

Dưới đây là chương trình Arduino IDE để đọc và hiển thị dữ liệu khoảng cách từ cảm biến siêu âm US-015:

```cpp
#define TRIG_PIN 2  // Chân TRIGGER nối với IO 02 của ESP32
#define ECHO_PIN 4  // Chân ECHO nối với IO 04 của ESP32

void setup() {
  Serial.begin(115200);          // Khởi tạo giao tiếp Serial với tốc độ 115200
  pinMode(TRIG_PIN, OUTPUT);     // Đặt TRIG_PIN làm đầu ra
  pinMode(ECHO_PIN, INPUT);      // Đặt ECHO_PIN làm đầu vào
}

void loop() {
  // Phát xung trigger (kích hoạt cảm biến)
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);         // Xung trigger kéo dài 10 micro giây
  digitalWrite(TRIG_PIN, LOW);

  // Đo thời gian echo
  long duration = pulseIn(ECHO_PIN, HIGH);  // Đo thời gian phản hồi của sóng siêu âm

  // Tính toán khoảng cách dựa trên thời gian sóng siêu âm đi và về
  float distance = duration * 0.034 / 2;

  // Hiển thị khoảng cách lên Serial Monitor
  Serial.print("Khoảng cách: ");
  Serial.print(distance);
  Serial.println(" cm");

  // Đợi 500ms trước khi đo tiếp
  delay(500);
}
```

### Bước 3: Thử nghiệm với vật cản
--------------------------------------------------------------------------------------------------------------------------------
- Khi thay đổi khoảng cách của vật cản với cảm biến siêu âm, giá trị khoảng cách hiển thị trên Serial Monitor thay đổi theo thời gian thực.
- Khoảng cách đo được chính xác với những vật có bề mặt phẳng và góc tới vuông góc với sóng siêu âm. Khi vật cản ở xa hoặc có góc xiên, giá trị có thể ít chính xác hơn.
--------------------------------------------------------------------------------------------------------------------------------

### Bước 4: Giải thích mã nguồn và công thức tính khoảng cách

- **Phát xung trigger**: 
  - Xung kích hoạt được gửi từ chân TRIGGER của ESP32 bằng cách đặt mức điện áp HIGH trong 10 micro giây, kích hoạt cảm biến siêu âm gửi sóng siêu âm ra ngoài.
  
- **Đo thời gian phản hồi**: 
  - Chân ECHO nhận tín hiệu phản xạ từ vật cản và `pulseIn()` đo thời gian từ lúc sóng siêu âm phát đi đến khi phản hồi trở lại.

- **Công thức tính khoảng cách**:
  ```cpp
  float distance = duration * 0.034 / 2;
  ```
  - **duration** là thời gian sóng siêu âm đi và về (tính bằng micro giây).
  - Tốc độ âm thanh trong không khí là khoảng **343 m/s** hoặc **0.034 cm/µs**. Do đó, khoảng cách tính bằng công thức:
    \[
    \text{Distance} = \frac{\text{Duration} \times \text{Speed of Sound}}{2}
    \]
  - Chia cho 2 vì thời gian đo là cho cả hành trình đi và về.

- **Độ chính xác của cảm biến phụ thuộc vào tốc độ sóng siêu âm**:
  - Tốc độ sóng siêu âm trong không khí phụ thuộc vào nhiệt độ, áp suất, và độ ẩm của môi trường. Nếu các điều kiện môi trường thay đổi, tốc độ sóng siêu âm cũng thay đổi, ảnh hưởng đến độ chính xác của phép đo.
  - Hiệu chỉnh tốc độ sóng giúp đảm bảo các phép đo chính xác hơn trong các điều kiện môi trường khác nhau.

### Bước 5: Thực nghiệm để xác định vùng mù của cảm biến

Kết quả xác định vùng mù cho cảm biến US-015 thông qua thực nghiệm:

| Khoảng cách nhỏ nhất đo được | Khoảng cách lớn nhất đo được |
|------------------------------|------------------------------|
| 2 cm                          | 400 cm                       |

- **Vùng mù**: Vùng mù của cảm biến siêu âm US-015 thường nằm ở khoảng **dưới 2 cm** (khoảng cách mà cảm biến không thể đo được chính xác). Với khoảng cách lớn hơn **400 cm**, cảm biến sẽ không phản hồi tín hiệu chính xác.

### Bước 6: Thay đổi chân kết nối và tốc độ Serial

Chúng ta có thể thay đổi chân kết nối của **TRIGGER** sang **IO 12** và **ECHO** sang **IO 14**, đồng thời thay đổi tốc độ Serial thành **9600 baud**. Dưới đây là chương trình sửa đổi:

```cpp
#define TRIG_PIN 12  // Thay đổi chân TRIGGER sang IO 12
#define ECHO_PIN 14  // Thay đổi chân ECHO sang IO 14

void setup() {
  Serial.begin(9600);          // Thay đổi tốc độ Serial thành 9600 baud
  pinMode(TRIG_PIN, OUTPUT);   // Đặt TRIG_PIN làm đầu ra
  pinMode(ECHO_PIN, INPUT);    // Đặt ECHO_PIN làm đầu vào
}

void loop() {
  // Phát xung trigger
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);       // Xung trigger kéo dài 10 micro giây
  digitalWrite(TRIG_PIN, LOW);

  // Đo thời gian echo
  long duration = pulseIn(ECHO_PIN, HIGH);  // Đo thời gian phản hồi của sóng siêu âm

  // Tính khoảng cách
  float distance = duration * 0.034 / 2;

  // Hiển thị kết quả
  Serial.print("Khoảng cách: ");
  Serial.print(distance);
  Serial.println(" cm");

  // Đợi 500ms trước khi đo tiếp
  delay(500);
}
```

### Nhận xét:
--------------------------------------------------------------------------------------------------------------------------------
- Sau khi thay đổi chân kết nối và tốc độ Serial, chương trình vẫn hoạt động bình thường. Kết quả đo khoảng cách được hiển thị chính xác và ổn định trên Serial Monitor ở tốc độ 9600 baud.
--------------------------------------------------------------------------------------------------------------------------------

### Nguyên lý cơ bản của giao thức truyền tin I2C
--------------------------------------------------------------------------------------------------------------------------------
- **I2C (Inter-Integrated Circuit)** là giao thức truyền thông nối tiếp được sử dụng để giao tiếp giữa vi điều khiển và các thiết bị ngoại vi như cảm biến, màn hình, và bộ nhớ.

- **Cấu trúc giao tiếp**:
  - **2 dây**: Sử dụng **SCL (Serial Clock)** và **SDA (Serial Data)**.
    - **SCL**: Dây đồng hồ (clock), được sử dụng để đồng bộ dữ liệu giữa các thiết bị.
    - **SDA**: Dây dữ liệu, để truyền tải các bit dữ liệu.
  - Một thiết bị **master** (ví dụ: ESP32) điều khiển việc giao tiếp và nhiều thiết bị **slave** có thể kết nối trên cùng một bus.
  - Mỗi thiết bị slave có một **địa chỉ duy nhất** (7-bit hoặc 10-bit) để nhận diện.

- **Quá trình truyền dữ liệu**:
  1. Thiết bị master phát đi tín hiệu bắt đầu (start condition).
  2. Master gửi địa chỉ của thiết bị slave kèm theo một bit chỉ định đọc hoặc ghi.
  3. Slave tương ứng sẽ phản hồi và giao tiếp bắt đầu bằng việc gửi hoặc nhận dữ liệu.
  4. Khi hoàn thành, master phát tín hiệu dừng (stop condition) để kết thúc giao tiếp.

- **Ưu điểm**:
  - **Tiết kiệm chân**: Chỉ cần 2 dây để giao tiếp với nhiều thiết bị.
  - **Đồng bộ hóa**: Dữ liệu được truyền theo xung nhịp, giúp đảm bảo tính đồng bộ giữa các thiết bị.

- **Nhược điểm**:
  - **Tốc độ truyền thấp hơn** so với SPI.
  - **Độ phức tạp tăng** khi số lượng thiết bị kết nối trên bus I2C tăng lên.
  
- **Ứng dụng**: I2C được sử dụng rộng rãi trong các hệ thống nhúng nhỏ gọn như cảm biến nhiệt độ, màn hình OLED, và EEPROM.
--------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------