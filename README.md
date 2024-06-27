# Sistem Database Reservasi Hotel

## Deskripsi Project

Proyek ini adalah implementasi dari sistem database untuk reservasi hotel. Sistem ini mencakup beberapa tabel utama, yaitu:

- Customer
- Room
- Booking
- Payment

Setiap tabel tersebut memiliki atribut-atribut yang relevan untuk mendukung fungsi dari sistem reservasi hotel.

## Entity Relationship Diagram (ERD)

![ERD](link_ke_gambar_erd)  <!-- Ganti dengan link ke gambar ERD yang diunggah ke repository -->

Diagram ERD di atas menggambarkan hubungan antara tabel-tabel dalam database reservasi hotel. 

- Tabel Customer menyimpan data pelanggan.
- Tabel Room menyimpan data kamar yang tersedia di hotel.
- Tabel Booking menyimpan data reservasi yang dilakukan oleh pelanggan.
- Tabel Payment menyimpan data pembayaran yang dilakukan oleh pelanggan untuk reservasi.

## Deskripsi Skema Basis Data

### CREATE TABLE

```sql
-- Membuat tabel Customer
CREATE TABLE Customer (
    id int primary key auto_increment,
    name varchar(50) not null,
    email varchar(50) unique not null,
    phone varchar(12) not null
);

-- Membuat tabel Room
CREATE TABLE Room (
    id int primary key auto_increment,
    room_number varchar(50) unique not null,
    type varchar(50) not null,
    price decimal(10,2) not null
);

-- Membuat tabel Booking
CREATE TABLE Booking (
    id int primary key auto_increment,
    customer_id int,
    room_id int,
    start_date date not null,
    end_date date not null,
    status varchar(50) not null,
    foreign key (customer_id) references Customer(id),
    foreign key (room_id) references Room(id)
);

-- Membuat tabel Payment
CREATE TABLE Payment (
    id int primary key auto_increment,
    booking_id int,
    amount decimal(10, 2) not null,
    payment_date date not null,
    payment_method varchar(50) not null,
    foreign key (booking_id) references Booking(id)
);
