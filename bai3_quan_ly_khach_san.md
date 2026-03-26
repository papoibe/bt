# BÀI 3: QUẢN LÝ KHÁCH SẠN

Một khách sạn cần xây dựng hệ thống thông tin về việc thuê phòng và sử dụng các dịch vụ của khách trọ bao gồm các chức năng sau:

1. Quản lý phòng, quản lý khách trọ
2. Quản lý các dịch vụ
3. Tìm kiếm thông tin về phòng thuê: loại phòng, giá tiền
4. Tìm kiếm thông tin về các dịch vụ của khách sạn: tên dịch vụ, đơn vị tính, đơn giá
5. Tính tiền và in hóa đơn cho khách trọ (tiền thuê phòng + tiền sử dụng dịch vụ)
6. Thống kê số khách trọ theo ngày thuê
7. Thống kê tổng doanh thu theo tháng

- Thông tin về phòng gồm mã phòng, loại phòng
- Thông tin về khách trọ gồm mã khách, họ tên, số CCCD, địa chỉ, quốc tịch
- Một khách trọ có thể đến thuê phòng tại khách sạn này nhiều lần
- Thông tin mỗi lần thuê của một khách gồm phòng thuê, ngày bắt đầu, ngày kết thúc
- Giả sử tất cả các phòng đều là phòng đơn (phòng một người)
- Đơn giá thuê/một ngày của một phòng được ấn định trước tùy theo phòng thuộc loại nào
- Trong mỗi lần thuê phòng, khách trọ có thể trả thêm các khoản tiền về dịch vụ (như điện thoại, ăn uống, karaoke,...)

---

## SƠ ĐỒ PHÂN RÃ CHỨC NĂNG

```mermaid
graph TD
    A["HTQL KHÁCH SẠN"] --> B["1. Cập nhật"]
    A --> C["2. Tìm kiếm"]
    A --> D["3. Thống kê"]
    A --> E["4. Tính toán"]
    A --> F["5. In ấn"]

    %% --- 1. CẬP NHẬT ---
    B --> B1["1.1 Quản lý Loại phòng"]
    B --> B2["1.2 Quản lý Phòng"]
    B --> B3["1.3 Quản lý Khách trọ"]
    B --> B4["1.4 Quản lý Dịch vụ"]
    B --> B5["1.5 Quản lý Phiếu thuê"]
    B --> B6["1.6 Quản lý Nhân viên"]

    B1 --> B1a["Thêm"]
    B1 --> B1b["Sửa"]
    B1 --> B1c["Xóa"]

    B2 --> B2a["Thêm"]
    B2 --> B2b["Sửa"]
    B2 --> B2c["Xóa"]

    B3 --> B3a["Thêm"]
    B3 --> B3b["Sửa"]
    B3 --> B3c["Xóa"]

    B4 --> B4a["Thêm"]
    B4 --> B4b["Sửa"]
    B4 --> B4c["Xóa"]

    B5 --> B5a["Thêm phiếu thuê"]
    B5 --> B5b["Sửa phiếu thuê"]
    B5 --> B5c["Xóa phiếu thuê"]
    B5 --> B5d["Thêm DV sử dụng"]

    B6 --> B6a["Thêm"]
    B6 --> B6b["Sửa"]
    B6 --> B6c["Xóa"]

    %% --- 2. TÌM KIẾM ---
    C --> C1["2.1 Tìm phòng thuê:<br/>loại phòng, giá tiền"]
    C --> C2["2.2 Tìm dịch vụ:<br/>tên DV, đơn vị tính, đơn giá"]
    C --> C3["2.3 Tìm khách trọ"]
    C --> C4["2.4 Tìm phiếu thuê"]

    %% --- 3. THỐNG KÊ ---
    D --> D1["3.1 Thống kê số khách trọ<br/>theo ngày thuê"]
    D --> D2["3.2 Thống kê tổng<br/>doanh thu theo tháng"]

    %% --- 4. TÍNH TOÁN ---
    E --> E1["4.1 Tính tiền thuê phòng<br/>(số ngày × đơn giá loại phòng)"]
    E --> E2["4.2 Tính tiền dịch vụ<br/>(SoLuong × DonGia)"]
    E --> E3["4.3 Tính tổng tiền hóa đơn<br/>(tiền phòng + tiền DV)"]

    %% --- 5. IN ẤN ---
    F --> F1["5.1 In hóa đơn"]
    F --> F2["5.2 In báo cáo thống kê"]
```

---

**LoaiPhong (Loại Phòng):** MaLoaiPhong, TenLoaiPhong, DonGia
→ *(Tách riêng vì đơn giá thuê/ngày được ấn định theo loại phòng)*

**Phong (Phòng):** MaPhong, MaLoaiPhong, MoTa, TinhTrang
→ *(Mỗi phòng thuộc một loại phòng; TinhTrang: Trống/Đang thuê)*

**KhachTro (Khách Trọ):** MaKhach, HoTen, SoCCCD, DiaChi, QuocTich, SDT
→ *(Theo đề: mã khách, họ tên, số CCCD, địa chỉ, quốc tịch; thêm SDT để liên lạc)*

**Thông tin thuê mỗi lần khách thuê (PhieuThue):** MaPhieuThue, MaKhach, MaPhong, NgayBatDau, NgayKetThuc
→ *(Một khách có thể thuê nhiều lần; mỗi lần gồm phòng thuê, ngày bắt đầu, ngày kết thúc)*

**DichVu (Dịch Vụ):** MaDV, TenDV, DonViTinh, DonGiaDV
→ *(Theo chức năng 4: tên dịch vụ, đơn vị tính, đơn giá)*

**CT_SuDungDV (Chi Tiết Sử Dụng Dịch Vụ):** MaPhieuThue, MaDV, SoLuong, DonGia, ThanhTien
→ *(Mỗi lần thuê, khách có thể sử dụng nhiều dịch vụ; ThanhTien = SoLuong × DonGia)*

**NhanVien (Nhân Viên):** MaNV, TenNV, SdtNV
→ *(Nhân viên lập hóa đơn)*

**HoaDon (Hóa Đơn):** MaHD, MaPhieuThue, MaNV, NgayLap, TienPhong, TienDichVu, TongTien
→ *(Chức năng 5: tính tiền = tiền thuê phòng + tiền sử dụng dịch vụ)*

---

## PHẦN 2: SƠ ĐỒ THỰC THỂ - MỐI KẾT HỢP (ERD)

```mermaid
erDiagram
    LOAIPHONG ||--o{ PHONG : "thuộc"
    LOAIPHONG {
        string MaLoaiPhong PK
        string TenLoaiPhong
        float DonGia
    }

    PHONG ||--o{ PHIEUTHUE : "được thuê"
    PHONG {
        string MaPhong PK
        string MaLoaiPhong FK
        string MoTa
        string TinhTrang
    }

    KHACHTRO ||--o{ PHIEUTHUE : "thuê"
    KHACHTRO {
        string MaKhach PK
        string HoTen
        string SoCCCD
        string DiaChi
        string QuocTich
        string SDT
    }

    PHIEUTHUE ||--o| HOADON : "có"
    PHIEUTHUE ||--o{ CT_SUDUNGDV : "sử dụng"
    PHIEUTHUE {
        string MaPhieuThue PK
        string MaKhach FK
        string MaPhong FK
        date NgayBatDau
        date NgayKetThuc
    }

    NHANVIEN ||--o{ HOADON : "lập"
    NHANVIEN {
        string MaNV PK
        string TenNV
        string SdtNV
    }

    HOADON {
        string MaHD PK
        string MaPhieuThue FK
        string MaNV FK
        date NgayLap
        float TienPhong
        float TienDichVu
        float TongTien
    }

    DICHVU ||--o{ CT_SUDUNGDV : "có"
    DICHVU {
        string MaDV PK
        string TenDV
        string DonViTinh
        float DonGiaDV
    }

    CT_SUDUNGDV {
        string MaPhieuThue PK_FK
        string MaDV PK_FK
        int SoLuong
        float DonGia
        float ThanhTien
    }
```

### Giải thích mối quan hệ:

| Thực thể 1 | Quan hệ | Thực thể 2 | Loại | Giải thích |
|---|---|---|---|---|
| LoaiPhong | thuộc | Phòng | 1 - N | Một loại phòng có nhiều phòng |
| Phòng | được thuê | PhieuThue | 1 - N | Một phòng được thuê nhiều lần |
| KhachTro | thuê | PhieuThue | 1 - N | Một khách thuê nhiều lần |
| PhieuThue | có | HoaDon | 1 - 1 | Mỗi lần thuê có một hóa đơn |
| PhieuThue | sử dụng | CT_SuDungDV | 1 - N | Mỗi lần thuê dùng nhiều dịch vụ |
| DichVu | có | CT_SuDungDV | 1 - N | Một dịch vụ được dùng nhiều lần |
| NhanVien | lập | HoaDon | 1 - N | Một nhân viên lập nhiều hóa đơn |
