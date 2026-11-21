# HƯỚNG DẪN THỰC HÀNH XÂY DỰNG HỆ THỐNG MẠNG TRÊN CISCO PACKET TRACER
> **Sinh viên thực hiện:** Trần Ngọc Mai  
> **Mã sinh viên:** B24DCCN377  
> **Học phần:** Thực hành Mạng máy tính  
> **Nguồn dữ liệu:** Chuẩn hóa 100% theo bảng quy hoạch địa chỉ IP trong file `B24DCCN377.docx`  

---

## MỤC LỤC
1. [Sơ đồ mạng tổng thể](#1-sơ-đồ-mạng-tổng-thể)
2. [Bảng quy hoạch địa chỉ IPv4 (VLSM) chuẩn theo B24DCCN377.docx](#2-bảng-quy-hoạch-địa-chỉ-ipv4-vlsm)
3. [Giai đoạn 1: Chuẩn bị thiết bị và lắp card phần cứng](#giai-đoạn-1-chuẩn-bị-thiết-bị-và-lắp-card-phần-cứng)
4. [Giai đoạn 2: Hướng dẫn cắm dây cáp chuẩn xác](#giai-đoạn-2-hướng-dẫn-cắm-dây-cáp-chuẩn-xác)
5. [Giai đoạn 3: Cấu hình địa chỉ IP cho 4 Router](#giai-đoạn-3-cấu-hình-địa-chỉ-ip-cho-4-router)
6. [Giai đoạn 4: Cấu hình định tuyến động RIP v2](#giai-đoạn-4-cấu-hình-định-tuyến-động-rip-v2)
7. [Giai đoạn 5: Cấu hình 5 máy chủ dịch vụ cá nhân hóa](#giai-đoạn-5-cấu-hình-5-máy-chủ-dịch-vụ)
8. [Giai đoạn 6: Cấu hình LAN 3 và mạng Wifi](#giai-đoạn-6-cấu-hình-lan-3-và-mạng-wifi)
9. [Giai đoạn 7: Quy trình kiểm thử và kịch bản vấn đáp điểm 10](#giai-đoạn-7-quy-trình-kiểm-thử-và-kịch-bản-vấn-đáp)
10. [Bảng tra cứu lỗi thường gặp (Troubleshooting)](#10-bảng-tra-cứu-lỗi-thường-gặp)

---

## 1. SƠ ĐỒ MẠNG TỔNG THỂ

![Sơ đồ hệ thống mạng](BT%20Slink%20MMT/word/media/image1.png)

### Cấu trúc hệ thống:
* **Trung tâm điều phối & Internet:** Router `R0` kết nối ra `DNS Server` ngoài Internet (Subnet 7) và tỏa xuống 3 Router con.
* **Tam giác định tuyến Router:** `R1`, `R2`, `R3` liên kết đa giác với nhau bằng các kết nối Serial ziczac màu đỏ để tạo tính dự phòng (khi 1 đường đứt thì tự tìm đường khác).
* **3 Phân vùng LAN:**
  * **LAN 1 (dưới R1):** Cụm máy chủ Dịch vụ ứng dụng (`Mail Server`, `Web Server`, `FTP Server`) và `PC1`.
  * **LAN 2 (dưới R2):** Mạng người dùng nhận IP tự động qua `DHCP Server`, gồm `PC2` và Laptop `LP1`.
  * **LAN 3 (dưới R3):** Mạng hỗn hợp gồm máy bàn `PC3`, Laptop `LP2` và bộ phát Wifi `Wireless Router 0` (phục vụ `Tablet`, `Smartphone`, `PC4`).

---

## 2. BẢNG QUY HOẠCH ĐỊA CHỈ IPV4 (VLSM)
*(Trích xuất chuẩn xác từ tài liệu cá nhân `B24DCCN377.docx`)*

### 2.1. Quy hoạch 10 Subnet
> [!NOTE]
> * Prefix `/30` (Subnet Mask `255.255.255.252`): Dùng cho các đường Point-to-Point giữa các Router (chỉ cần 2 địa chỉ khả dụng để tránh lãng phí).
> * Prefix `/24` (Subnet Mask `255.255.255.0`): Dùng cho các mạng LAN nội bộ và máy chủ.
> * Quy ước Default Gateway: Cổng Router nối vào mạng LAN luôn lấy địa chỉ khả dụng đầu tiên (`.1`).

| STT | Tên Subnet | Đường mạng (Network ID) | Subnet Mask | Dải IP khả dụng | Ghi chú quy ước |
| :---: | :--- | :--- | :--- | :--- | :--- |
| **1** | Subnet 1 (R0 – R1) | `192.168.1.0/30` | `255.255.255.252` | `192.168.1.1` – `192.168.1.2` | Mạng riêng (Private) |
| **2** | Subnet 2 (R0 – R3) | `192.168.1.4/30` | `255.255.255.252` | `192.168.1.5` – `192.168.1.6` | Mạng riêng (Private) |
| **3** | Subnet 3 (R0 – R2) | `192.168.1.8/30` | `255.255.255.252` | `192.168.1.9` – `192.168.1.10` | Mạng riêng (Private) |
| **4** | Subnet 4 (Serial R1 – R2) | `192.168.1.12/30` | `255.255.255.252` | `192.168.1.13` – `192.168.1.14` | Mạng riêng (Private) |
| **5** | Subnet 5 (Serial R1 – R3) | `192.168.1.16/30` | `255.255.255.252` | `192.168.1.17` – `192.168.1.18` | Mạng riêng (Private) |
| **6** | Subnet 6 (Serial R2 – R3) | `192.168.1.20/30` | `255.255.255.252` | `192.168.1.21` – `192.168.1.22` | Mạng riêng (Private) |
| **7** | Subnet 7 (R0 – DNS Server)| `203.162.1.0/24` | `255.255.255.0` | `203.162.1.1` – `203.162.1.254` | Mạng Internet (Public) |
| **8** | Subnet 8 (LAN 1 dưới R1) | `192.168.8.0/24` | `255.255.255.0` | `192.168.8.1` – `192.168.8.254` | Mạng riêng (Private) |
| **9** | Subnet 9 (LAN 2 dưới R2) | `192.168.9.0/24` | `255.255.255.0` | `192.168.9.1` – `192.168.9.254` | Mạng riêng (Private) |
| **10**| Subnet 10 (LAN 3 dưới R3)| `192.168.10.0/24` | `255.255.255.0` | `192.168.10.1` – `192.168.10.254`| Mạng riêng (Private) |

---

### 2.2. Bảng phân bổ IP chi tiết cho từng thiết bị
* **DNS Server chung cho toàn mạng:** `203.162.1.2`

| Tên thiết bị | Thuộc Subnet | Cổng kết nối | Địa chỉ IPv4 gán | Subnet Mask | Default Gateway | DNS Server |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Router R0** | Subnet 7<br>Subnet 1<br>Subnet 3<br>Subnet 2 | `Fa0/0`<br>`Fa0/1`<br>`Fa1/0`<br>`Fa1/1` | `203.162.1.1`<br>`192.168.1.1`<br>`192.168.1.9`<br>`192.168.1.5` | `255.255.255.0`<br>`255.255.255.252`<br>`255.255.255.252`<br>`255.255.255.252` | *Không có* | `203.162.1.2` |
| **Router R1** | Subnet 1<br>Subnet 8<br>Subnet 4<br>Subnet 5 | `Fa0/0`<br>`Fa0/1`<br>`Se0/0/0`<br>`Se0/0/1`| `192.168.1.2`<br>`192.168.8.1`<br>`192.168.1.13`<br>`192.168.1.17` | `255.255.255.252`<br>`255.255.255.0`<br>`255.255.255.252`<br>`255.255.255.252` | *Không có* | `203.162.1.2` |
| **Router R2** | Subnet 3<br>Subnet 9<br>Subnet 4<br>Subnet 6 | `Fa0/0`<br>`Fa0/1`<br>`Se0/0/0`<br>`Se0/0/1`| `192.168.1.10`<br>`192.168.9.1`<br>`192.168.1.14`<br>`192.168.1.21` | `255.255.255.252`<br>`255.255.255.0`<br>`255.255.255.252`<br>`255.255.255.252` | *Không có* | `203.162.1.2` |
| **Router R3** | Subnet 2<br>Subnet 10<br>Subnet 5<br>Subnet 6 | `Fa0/0`<br>`Fa0/1`<br>`Se0/0/0`<br>`Se0/0/1`| `192.168.1.6`<br>`192.168.10.1`<br>`192.168.1.18`<br>`192.168.1.22` | `255.255.255.252`<br>`255.255.255.0`<br>`255.255.255.252`<br>`255.255.255.252` | *Không có* | `203.162.1.2` |
| **DNS Server** | Subnet 7 | `Fa0` | `203.162.1.2` | `255.255.255.0` | `203.162.1.1` | `203.162.1.2` |
| **Mail Server**| Subnet 8 | `Fa0` | `192.168.8.2` | `255.255.255.0` | `192.168.8.1` | `203.162.1.2` |
| **Web Server** | Subnet 8 | `Fa0` | `192.168.8.3` | `255.255.255.0` | `192.168.8.1` | `203.162.1.2` |
| **FTP Server** | Subnet 8 | `Fa0` | `192.168.8.4` | `255.255.255.0` | `192.168.8.1` | `203.162.1.2` |
| **PC1** | Subnet 8 | `Fa0` | `192.168.8.10` | `255.255.255.0` | `192.168.8.1` | `203.162.1.2` |
| **DHCP Server**| Subnet 9 | `Fa0` | `192.168.9.2` | `255.255.255.0` | `192.168.9.1` | `203.162.1.2` |
| **PC2** | Subnet 9 | `Fa0` | *Nhận DHCP (`.10`)* | `255.255.255.0` | `192.168.9.1` | `203.162.1.2` |
| **LP1 (Laptop 1)**| Subnet 9 | `Fa0` | *Nhận DHCP (`.11`)* | `255.255.255.0` | `192.168.9.1` | `203.162.1.2` |
| **PC3** | Subnet 10 | `Fa0` | `192.168.10.10` | `255.255.255.0` | `192.168.10.1` | `203.162.1.2` |
| **LP2 (Laptop 2)**| Subnet 10 | `Fa0` | `192.168.10.11` | `255.255.255.0` | `192.168.10.1` | `203.162.1.2` |
| **Wireless Router0**| Subnet 10 | `LAN 1` (Ethernet)| `192.168.10.2` | `255.255.255.0` | `192.168.10.1` | `203.162.1.2` |
| **PC4** | Subnet 10 | Cắm LAN Router | `192.168.10.20` | `255.255.255.0` | `192.168.10.1` | `203.162.1.2` |
| **Tablet** | Subnet 10 | Bắt Wifi | `192.168.10.21` | `255.255.255.0` | `192.168.10.1` | `203.162.1.2` |
| **Smartphone** | Subnet 10 | Bắt Wifi | `192.168.10.22` | `255.255.255.0` | `192.168.10.1` | `203.162.1.2` |

---

## GIAI ĐOẠN 1: CHUẨN BỊ THIẾT BỊ VÀ LẮP CARD PHẦN CỨNG

> [!CAUTION]
> Bắt buộc phải **TẮT CÔNG TẮC NGUỒN** của Router trước khi gắn module, sau khi gắn xong mới **BẬT LẠI NGUỒN**. Nếu không tắt nguồn, Packet Tracer sẽ không cho gắn card.

1. **Lấy 4 Router dòng `2811`:** Đặt tên lần lượt là `R0`, `R1`, `R2`, `R3`.
2. **Gắn card mạng mở rộng cho `R0` (cần 4 cổng FastEthernet):**
   * Click vào `R0` $\rightarrow$ Tab **Physical** $\rightarrow$ Bấm công tắc xanh tắt nguồn.
   * Danh sách bên trái: Chọn module **`NM-2FE2W`** $\rightarrow$ Kéo thả vào khe trống lớn ở giữa.
   * Bấm công tắc bật lại nguồn. *(Khi này R0 sẽ có 4 cổng: `Fa0/0`, `Fa0/1`, `Fa1/0`, `Fa1/1`)*.
3. **Gắn card Serial cho `R1`, `R2`, `R3` (cần các cổng cáp đỏ):**
   * Click vào từng Router (`R1`, `R2`, `R3`) $\rightarrow$ Tab **Physical** $\rightarrow$ Tắt nguồn.
   * Chọn module **`WIC-2T`** $\rightarrow$ Kéo thả vào khe cắm nhỏ phía dưới bên phải.
   * Bật lại nguồn cho cả 3 Router.
4. **Lấy các thiết bị còn lại:**
   * 3 Switch dòng **`2960`** (`Switch1`, `Switch2`, `Switch3`).
   * 5 chiếc **`Server-PT`** (`DNS Server`, `Mail Server`, `Web Server`, `FTP Server`, `DHCP Server`).
   * 1 thiết bị **`WRT300N`** (trong mục Wireless Devices) đặt tên là `Wireless Router0`.
   * Các máy PC, Laptop, Tablet, Smartphone như sơ đồ.

---

## GIAI ĐOẠN 2: HƯỚNG DẪN CẮM DÂY CÁP CHUẨN XÁC

### 1. Cáp Serial DCE (Đường ziczac màu đỏ có hình đồng hồ):
* Nối từ **R1** (`Serial0/0/0`) sang **R2** (`Serial0/0/0`)
* Nối từ **R1** (`Serial0/0/1`) sang **R3** (`Serial0/0/0`)
* Nối từ **R2** (`Serial0/0/1`) sang **R3** (`Serial0/0/1`)

### 2. Cáp chéo (Copper Cross-Over - dây nét đứt màu đen):
Nối Router với Router hoặc Router với Server:
* **R0** (`FastEthernet0/0`) $\leftrightarrow$ **DNS Server** (`FastEthernet0`)
* **R0** (`FastEthernet0/1`) $\leftrightarrow$ **R1** (`FastEthernet0/0`)
* **R0** (`FastEthernet1/0`) $\leftrightarrow$ **R2** (`FastEthernet0/0`)
* **R0** (`FastEthernet1/1`) $\leftrightarrow$ **R3** (`FastEthernet0/0`)

### 3. Cáp thẳng (Copper Straight-Through - dây nét liền màu đen):
* **R1** (`Fa0/1`) $\leftrightarrow$ `Switch1` (`Fa0/1`). Switch 1 nối với Mail, Web, FTP Server và PC1.
* **R2** (`Fa0/1`) $\leftrightarrow$ `Switch2` (`Fa0/1`). Switch 2 nối với DHCP Server, PC2 và LP1.
* **R3** (`Fa0/1`) $\leftrightarrow$ `Switch3` (`Fa0/1`). Switch 3 nối với PC3, LP2.
* **Đặc biệt lưu ý tại Wireless Router 0:**
  * Dùng cáp thẳng nối từ **Switch3** vào **CỔNG ETHERNET 1 (LAN)** của `Wireless Router0` *(KHÔNG cắm vào cổng Internet màu vàng để giữ toàn bộ thiết bị cùng Subnet 10)*.
  * Cắm dây từ **PC4** vào **CỔNG ETHERNET 2 (LAN)** của `Wireless Router0`.

---

## GIAI ĐOẠN 3: CẤU HÌNH ĐỊA CHỈ IP CHO 4 ROUTER

Người dùng có thể chọn làm theo **Giao diện tab Config (GUI)** hoặc dán lệnh **CLI**.

### Cách 1: Thao tác giao diện (Tab Config)
Vào tab **Config** của từng Router $\rightarrow$ Bấm chọn từng cổng ở cột **INTERFACE**:
1. Tích vào ô vuông **`[v] On`** ở dòng **Port Status**.
2. Điền **IP Address** và **Subnet Mask**.
3. Cổng Serial nào ở đầu DCE (có hình đồng hồ) thì chọn **Clock Rate: `64000`**.

### Cách 2: Copy-Paste lệnh nhanh qua tab CLI

#### Trên Router R0:
```cisco
enable
configure terminal
hostname R0
interface FastEthernet0/0
 ip address 203.162.1.1 255.255.255.0
 no shutdown
 exit
interface FastEthernet0/1
 ip address 192.168.1.1 255.255.255.252
 no shutdown
 exit
interface FastEthernet1/0
 ip address 192.168.1.9 255.255.255.252
 no shutdown
 exit
interface FastEthernet1/1
 ip address 192.168.1.5 255.255.255.252
 no shutdown
 exit
end
write
```

#### Trên Router R1:
```cisco
enable
configure terminal
hostname R1
interface FastEthernet0/0
 ip address 192.168.1.2 255.255.255.252
 no shutdown
 exit
interface FastEthernet0/1
 ip address 192.168.8.1 255.255.255.0
 no shutdown
 exit
interface Serial0/0/0
 ip address 192.168.1.13 255.255.255.252
 clock rate 64000
 no shutdown
 exit
interface Serial0/0/1
 ip address 192.168.1.17 255.255.255.252
 clock rate 64000
 no shutdown
 exit
end
write
```

#### Trên Router R2:
```cisco
enable
configure terminal
hostname R2
interface FastEthernet0/0
 ip address 192.168.1.10 255.255.255.252
 no shutdown
 exit
interface FastEthernet0/1
 ip address 192.168.9.1 255.255.255.0
 no shutdown
 exit
interface Serial0/0/0
 ip address 192.168.1.14 255.255.255.252
 no shutdown
 exit
interface Serial0/0/1
 ip address 192.168.1.21 255.255.255.252
 clock rate 64000
 no shutdown
 exit
end
write
```

#### Trên Router R3:
```cisco
enable
configure terminal
hostname R3
interface FastEthernet0/0
 ip address 192.168.1.6 255.255.255.252
 no shutdown
 exit
interface FastEthernet0/1
 ip address 192.168.10.1 255.255.255.0
 no shutdown
 exit
interface Serial0/0/0
 ip address 192.168.1.18 255.255.255.252
 no shutdown
 exit
interface Serial0/0/1
 ip address 192.168.1.22 255.255.255.252
 no shutdown
 exit
end
write
```

---

## GIAI ĐOẠN 4: CẤU HÌNH ĐỊNH TUYẾN ĐỘNG RIP V2

### Các mạng khai báo trong RIP:
* **R0:** Add `203.162.1.0` và `192.168.1.0`
* **R1:** Add `192.168.1.0` và `192.168.8.0`
* **R2:** Add `192.168.1.0` và `192.168.9.0`
* **R3:** Add `192.168.1.0` và `192.168.10.0`

> [!IMPORTANT]
> **MẸO BẮT BUỘC ĐỂ KHÔNG BỊ RỚT GÓI TIN DO CHIA VLSM /30:**  
> Sau khi Add mạng trên tab Config, mở tab CLI của cả 4 Router gõ thêm lệnh chuyển sang version 2 và tắt auto-summary:
> ```cisco
> enable
> configure terminal
> router rip
>  version 2
>  no auto-summary
> end
> write
> ```

---

## GIAI ĐOẠN 5: CẤU HÌNH 5 MÁY CHỦ DỊCH VỤ
*(Cá nhân hóa theo tên sinh viên: **Trần Ngọc Mai - B24DCCN377**)*

### 1. DNS Server (Subnet 7: `203.162.1.2`)
* Tab **Desktop** $\rightarrow$ **IP Configuration**: IP: `203.162.1.2`, Gateway: `203.162.1.1`, DNS: `203.162.1.2`.
* Tab **Services** $\rightarrow$ **DNS**: Chuyển **On**.
* Tạo 3 bản ghi A Record:
  * Name: `web.tranngocmai.vn` $\rightarrow$ Address: `192.168.8.3` $\rightarrow$ Bấm **Add**.
  * Name: `mail.tranngocmai.vn` $\rightarrow$ Address: `192.168.8.2` $\rightarrow$ Bấm **Add**.
  * Name: `ftp.tranngocmai.vn` $\rightarrow$ Address: `192.168.8.4` $\rightarrow$ Bấm **Add**.

### 2. Cụm Server tại LAN 1 (Subnet 8)
* **Web Server (`192.168.8.3` - Gateway: `192.168.8.1`, DNS: `203.162.1.2`):**
  * Vào **Services** $\rightarrow$ **HTTP**: Bật cả HTTP và HTTPS sang **On**.
  * Bấm **(edit)** file `index.html`, dán nội dung:
    ```html
    <html>
      <body style="text-align: center; font-family: Arial;">
        <h1 style="color: blue;">Website cua sinh vien Tran Ngoc Mai</h1>
        <p>Ma sinh vien: B24DCCN377 - Bai thuc hanh Mang may tinh</p>
      </body>
    </html>
    ```
    Bấm **Save** $\rightarrow$ Chọn **Yes**.
* **Mail Server (`192.168.8.2` - Gateway: `192.168.8.1`, DNS: `203.162.1.2`):**
  * Vào **Services** $\rightarrow$ **EMAIL**: Bật SMTP & POP3 sang **On**.
  * Domain Name: `tranngocmai.vn` $\rightarrow$ Bấm **Set**.
  * Tạo User:
    * User: `mai`, Password: `123` $\rightarrow$ Bấm `+`.
    * User: `user1`, Password: `123` $\rightarrow$ Bấm `+`.
* **FTP Server (`192.168.8.4` - Gateway: `192.168.8.1`, DNS: `203.162.1.2`):**
  * Vào **Services** $\rightarrow$ **FTP**: Chuyển sang **On**.
  * Username: `mai`, Password: `123`.
  * Tích chọn toàn bộ các quyền: **Read, Write, Delete, Rename, List** $\rightarrow$ Bấm **Add**.
* **PC1 (`192.168.8.10`):** Mask: `255.255.255.0`, Gateway: `192.168.8.1`, DNS: `203.162.1.2`.

### 3. Cụm DHCP Server tại LAN 2 (Subnet 9)
* **DHCP Server (`192.168.9.2` - Gateway: `192.168.9.1`, DNS: `203.162.1.2`):**
  * Vào **Services** $\rightarrow$ **DHCP**: Chuyển **On**.
  * Default Gateway: `192.168.9.1`
  * DNS Server: `203.162.1.2`
  * Start IP Address: `192.168.9.10`, Subnet Mask: `255.255.255.0`, Max users: `50`.
  * Bấm **Save**.
* **PC2 và Laptop LP1:**
  * Mở **IP Configuration** $\rightarrow$ Tích chọn nút tròn **`DHCP`** $\rightarrow$ Máy tự nhận IP `192.168.9.10` và `192.168.9.11`.

---

## GIAI ĐOẠN 6: CẤU HÌNH LAN 3 VÀ MẠNG WIFI

1. **PC3 & LP2 (Laptop 2):**
   * PC3: IP `192.168.10.10`, Mask `255.255.255.0`, Gateway `192.168.10.1`, DNS `203.162.1.2`.
   * LP2: IP `192.168.10.11`, Mask `255.255.255.0`, Gateway `192.168.10.1`, DNS `203.162.1.2`.
2. **Wireless Router 0:**
   * Tab **GUI** $\rightarrow$ **Setup $\rightarrow$ Basic Setup**:
     * Local IP Address: Đổi thành `192.168.10.2` (Mask `255.255.255.0`).
     * DHCP Server: Chọn **Disabled** (vì các thiết bị đã được gán IP tĩnh chuẩn theo bảng quy hoạch).
     * Cuộn xuống dưới bấm **Save Settings**.
   * Tab **GUI** $\rightarrow$ **Wireless $\rightarrow$ Basic Wireless Settings**:
     * Network Name (SSID): Đặt tên là **`WIFI_NGOCMAI`** $\rightarrow$ Bấm **Save Settings**.
3. **PC4:** Cắm cáp vào cổng LAN Wireless Router $\rightarrow$ Đặt IP tĩnh: `192.168.10.20`, Gateway `192.168.10.1`, DNS `203.162.1.2`.
4. **Tablet & Smartphone (SP):**
   * Vào tab **Config** $\rightarrow$ **Wireless0**: Nhập SSID là `WIFI_NGOCMAI` (sẽ thấy các đường sóng wifi xuất hiện kết nối vào Wireless Router).
   * Vào tab **Desktop** $\rightarrow$ **IP Configuration**:
     * **Tablet:** IP `192.168.10.21`, Gateway: **`192.168.10.1`**, DNS: **`203.162.1.2`**.
     * **Smartphone:** IP `192.168.10.22`, Gateway: **`192.168.10.1`**, DNS: **`203.162.1.2`**.

---

## GIAI ĐOẠN 7: QUY TRÌNH KIỂM THỬ VÀ KỊCH BẢN VẤN ĐÁP

### 1. Test Web Server bằng Tên miền cá nhân:
* Mở **Web Browser** trên **Tablet** hoặc **PC2**.
* Gõ địa chỉ: `http://web.tranngocmai.vn`
* Kết quả: Hiển thị trang web *"Website cua sinh vien Tran Ngoc Mai - MSV: B24DCCN377"* $\rightarrow$ Chứng minh DNS, Web và Routing toàn mạng đã thông!

### 2. Test Ping bằng IP vs Ping bằng Tên miền:
* Mở Command Prompt trên **PC3**:
  * `ping 192.168.8.3` $\rightarrow$ Có Reply (Mạng vật lý và RIP thông).
  * `ping web.tranngocmai.vn` $\rightarrow$ Máy tự động dịch tên miền thành `[192.168.8.3]` rồi mới ping (Dịch vụ DNS hoạt động hoàn hảo).

### 3. Test gửi/nhận Email giữa 2 máy:
* Mở **PC1** cấu hình email `mai@tranngocmai.vn`.
* Mở **PC2** cấu hình email `user1@tranngocmai.vn`.
* Soạn thư từ PC1 gửi sang PC2 $\rightarrow$ Trên PC2 bấm **Receive** thấy thư xuất hiện!

### 4. Test FTP Server:
* Mở Command Prompt trên PC1:
  ```cmd
  ftp ftp.tranngocmai.vn
  ```
  Nhập User: `mai`, Pass: `123` $\rightarrow$ Báo `Logged in.` $\rightarrow$ Gõ lệnh `dir` để xem kho dữ liệu.

### 5. Kịch bản vấn đáp: Tính năng tự tìm đường khác khi đứt cáp (Fault Tolerance)
* **Bước 1 (Lúc bình thường):** Trên PC1, gõ lệnh:
  ```cmd
  tracert 192.168.9.10
  ```
  Thấy gói tin đi thẳng qua đường Serial: `R1 (192.168.8.1) -> R2 (192.168.1.14) -> PC2`.
* **Bước 2 (Gây sự cố):** Xóa sợi dây Serial đỏ giữa R1 và R2 (hoặc gõ `shutdown` cổng `Serial0/0/0` trên R1). Bấm nút Fast Forward Time (`>>`) 3 lần.
* **Bước 3 (Chứng minh tự đổi đường):** Gõ lại lệnh:
  ```cmd
  tracert 192.168.9.10
  ```
  Màn hình hiển thị gói tin tự động **đi vòng qua R3 hoặc R0** trước khi tới R2!
* **Câu trả lời vấn đáp:** *"Thưa thầy, nhờ giao thức định tuyến động RIP v2 cùng cấu trúc tam giác dự phòng, khi đường truyền chính bị sự cố, bảng định tuyến tự động cập nhật và điều hướng gói tin sang tuyến đường dự phòng mà không làm gián đoạn liên lạc."*

---

## 10. BẢNG TRA CỨU LỖI THƯỜNG GẶP (TROUBLESHOOTING)

| Hiện tượng lỗi | Nguyên nhân gốc rễ | Cách khắc phục nhanh |
| :--- | :--- | :--- |
| **Tam giác trên dây Router có màu đỏ** | Cổng Router chưa được bật (`Port Status: On`) | Vào tab Config của cổng đó, tích vào ô `[v] On` |
| **Gói ping đầu tiên báo `Request timed out`** | Giao thức ARP đang đi tìm địa chỉ MAC | Ping lại lần thứ 2 sẽ có `Reply` bình thường |
| **Tablet không ping được ra ngoài dù đã bắt Wifi** | Nhầm Default Gateway hoặc cắm nhầm cổng WAN | 1. Điền Gateway Tablet là `192.168.10.1`<br>2. Cắm dây từ Switch 3 vào cổng **LAN** của Wireless Router |
| **Router không học được dải mạng /30 của nhau** | RIP v1 mặc định tự động gộp mạng classful | Vào CLI gõ thêm 2 dòng: `version 2` và `no auto-summary` |
| **Vào web bằng IP được nhưng bằng tên miền báo lỗi** | Máy trạm chưa khai báo DNS Server | Vào IP Configuration của máy điền ô DNS: `203.162.1.2` |
| **Router bị đơ hiện dòng `Translating "..."`** | Gõ nhầm lệnh ở màn hình ngoài | Bấm đồng thời 3 phím: **`Ctrl` + `Shift` + `6`** để hủy ngay |
