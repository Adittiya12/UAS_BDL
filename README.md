# Sistem Database Reservasi Hotel

## Deskripsi Project

Proyek ini adalah implementasi dari sistem database untuk reservasi hotel. Sistem ini mencakup beberapa tabel utama, yaitu:

- Customer
- Room
- Booking
- Payment

Setiap tabel tersebut memiliki atribut-atribut yang relevan untuk mendukung fungsi dari sistem reservasi hotel.

## Entity Relationship Diagram (ERD)

(https://github.com/Adittiya12/UAS_BDL/blob/main/erd-sistem-reservasi-hotel.png)  <!--  link ke gambar ERD yang diunggah ke repository -->

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

#index
CREATE INDEX idx_customer_email ON Customer(email);
CREATE INDEX idx_room_number ON Room(room_number);
CREATE INDEX idx_booking_dates ON Booking(start_date, end_date);

#view
CREATE VIEW CustomerBookingView AS
SELECT c.name, r.room_number, b.start_date, b.end_date, b.status
FROM Customer c
JOIN Booking b ON c.id = b.customer_id
JOIN Room r ON b.room_id = r.id;

#trigger
DELIMITER //
CREATE TRIGGER before_booking_update
BEFORE UPDATE ON Booking
FOR EACH ROW
BEGIN
    IF NEW.end_date < CURDATE() THEN
        SET NEW.status = 'Completed';
    END IF;
END;
//
DELIMITER ;

#insertdata
-- Insert data into Customer table
INSERT INTO Customer (name, email, phone)
VALUES ('Aditya', 'aditya@gmail.com', '123-456-7890'),
       ('Putu', 'putu@gmail.com', '098-765-4321');

-- Insert data into Room table
INSERT INTO Room (room_number, type, price)
VALUES ('101', 'Single', 100.00),
       ('102', 'Double', 150.00);

-- Insert data into Booking table
INSERT INTO Booking (customer_id, room_id, start_date, end_date, status)
VALUES (1, 1, '2024-06-20', '2024-06-22', 'Pending'),
       (2, 2, '2024-06-21', '2024-06-23', 'Pending');

-- Insert data into Payment table
INSERT INTO Payment (booking_id, amount, payment_date, payment_method)
VALUES (1, 200.00, '2024-06-19', 'Credit Card'),
       (2, 300.00, '2024-06-20', 'Credit Card');

#left-join
SELECT c.name, r.room_number, b.start_date, b.end_date, b.status
FROM Customer c
LEFT JOIN Booking b ON c.id = b.customer_id
LEFT JOIN Room r ON b.room_id = r.id;

#inner-join
SELECT c.name, p.amount, p.payment_date
FROM Customer c
INNER JOIN Booking b ON c.id = b.customer_id
INNER JOIN Payment p ON b.id = p.booking_id;

#subquery
SELECT name, email 
FROM Customer 
WHERE id IN (SELECT customer_id FROM Booking WHERE status = 'Completed');

#having
SELECT name, email 
FROM Customer 
WHERE id IN (SELECT customer_id FROM Booking WHERE status = 'Completed');

#wildcart
SELECT * FROM Customer WHERE name LIKE 'Aditya%';
SELECT * FROM Customer WHERE name LIKE '%P';
SELECT * FROM Customer WHERE name LIKE 'Putu';

#memeriksa-data
-- Memeriksa data di tabel Room
SELECT * FROM Room;

-- Memeriksa data di tabel Booking
SELECT * FROM Booking;

-- Memeriksa jumlah booking berdasarkan tipe kamar
SELECT r.type, COUNT(*) as booking_count
FROM Room r
JOIN Booking b ON r.id = b.room_id
GROUP BY r.type
HAVING booking_count > 1;

-- Memeriksa customer yang memiliki booking dengan status 'Pending'
SELECT name, email 
FROM Customer 
WHERE id IN (SELECT customer_id FROM Booking WHERE status = 'Pending');
