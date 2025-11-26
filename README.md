# YETGIMOracle

-- Müşteriler tablosu
CREATE TABLE Customers (
    CustomerID NUMBER PRIMARY KEY,
    Name VARCHAR2(100),
    Email VARCHAR2(100),
    Phone VARCHAR2(20)
);

-- Ürünler tablosu
CREATE TABLE Products (
    ProductID NUMBER PRIMARY KEY,
    ProductName VARCHAR2(100),
    Price NUMBER(10,2),
    Stock NUMBER
);

-- Siparişler tablosu
CREATE TABLE Orders (
    OrderID NUMBER PRIMARY KEY,
    CustomerID NUMBER,
    OrderDate DATE,
    FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID)
);

-- Sipariş Detayları tablosu
CREATE TABLE OrderDetails (
    OrderDetailID NUMBER PRIMARY KEY,
    OrderID NUMBER,
    ProductID NUMBER,
    Quantity NUMBER,
    FOREIGN KEY (OrderID) REFERENCES Orders(OrderID),
    FOREIGN KEY (ProductID) REFERENCES Products(ProductID)
);

INSERT INTO Customers VALUES (1, 'Ali Yılmaz', 'ali@example.com', '0537xxxxxxx');
INSERT INTO Products VALUES (101, 'Laptop', 15000, 20);
INSERT INTO Orders VALUES (5001, 1, SYSDATE);
INSERT INTO OrderDetails VALUES (9001, 5001, 101, 2);

INSERT INTO Customers VALUES (2, 'Ayşe Demir', 'ayse@example.com', '0541xxxxxxx');
INSERT INTO Customers VALUES (3, 'Mehmet Kaya', 'mehmet@example.com', '0552xxxxxxx');
INSERT INTO Customers VALUES (4, 'Zeynep Çelik', 'zeynep@example.com', '0533xxxxxxx');

INSERT INTO Products VALUES (102, 'Telefon', 8000, 50);
INSERT INTO Products VALUES (103, 'Kulaklık', 500, 200);
INSERT INTO Products VALUES (104, 'Tablet', 6000, 30);

INSERT INTO Orders VALUES (5002, 2, SYSDATE-2); -- Ayşe’nin siparişi
INSERT INTO Orders VALUES (5003, 3, SYSDATE-1); -- Mehmet’in siparişi
INSERT INTO Orders VALUES (5004, 4, SYSDATE);   -- Zeynep’in siparişi

INSERT INTO OrderDetails VALUES (9002, 5002, 102, 1); -- Ayşe 1 Telefon aldı
INSERT INTO OrderDetails VALUES (9003, 5002, 103, 2); -- Ayşe 2 Kulaklık aldı
INSERT INTO OrderDetails VALUES (9004, 5003, 104, 1); -- Mehmet 1 Tablet aldı
INSERT INTO OrderDetails VALUES (9005, 5004, 101, 1); -- Zeynep 1 Laptop aldı
INSERT INTO OrderDetails VALUES (9006, 5004, 103, 3); -- Zeynep 3 Kulaklık aldı

select * from ORDERDETAILS

SELECT c.Name, o.OrderID, o.OrderDate
FROM Customers c
JOIN Orders o ON c.CustomerID = o.CustomerID;

--Sipariş ID'sini ve aylık toplam totali veren sorguyu yazınız.
SELECT o.OrderID, SUM(p.Price * od.Quantity) AS TotalAmount
FROM Orders o
JOIN OrderDetails od ON o.OrderID = od.OrderID
JOIN Products p ON od.ProductID = p.ProductID
GROUP BY o.OrderID;

--Ürün isimlerini, stok miktarlarını ve sipariş miktarlarını getiren sorguyu yazın.
select p.ProductName, p.Stock, o.Quantity
from Products p
left join OrderDetails o
on p.ProductID = o.ProductID

--ortalama stok miktarının üstünde olan ürünleri belirleyen sorguyu yazın.
select ProductName, Stock
from Products
where stock > (select AVG(stock) from Products)

--Müşterilerin isimleri, aldıkları ürünleri ve sipariş tarihlerini getiren sorguyu yazın.
select e.name, p.ProductName, o.OrderDate
from customers e 
inner join Orders o on e.CustomerID = o.CustomerID
inner join orderdetails d on d.OrderID = o.OrderID
inner join Products p on p.ProductID = d.ProductID

--Genel ürün ortalama stok miktarının üstünde kalan ürün bazlı ortalama stok miktarını eblirleyen sorguyu yazın.
select ProductName, AVG(stock) as avg_stock
from Products
group by ProductID
having avg(stock) > (
    select avg(stock)
    from Products
)

--Tüm ürünlerin en yakın sipariş tarihlerini listeleyen sorguyu yazın.
SELECT 
    p.ProductID,
    p.ProductName,
    MIN(o.OrderDate) AS FirstOrderDate
FROM Products p
JOIN OrderDetails od ON p.ProductID = od.ProductID
JOIN Orders o ON od.OrderID = o.OrderID
GROUP BY p.ProductID, p.ProductName
ORDER BY FirstOrderDate;

--Sipariş tarihlerini "dd-mon-yyyy" modeline göre formatlayan sorguyu yazın.
select OrderDate,
to_char( OrderDate,'DD-MON-YYYY') as formated_order_date
from Orders
