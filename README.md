## 1. Desain Master & Form Input

### a. Struktur Tabel (Database MySQL)

```sql
CREATE TABLE mobil (
    id_mobil INT AUTO_INCREMENT PRIMARY KEY,
    merk VARCHAR(50),
    tipe VARCHAR(50),
    tahun INT,
    harga_sewa DECIMAL(10,2),
    status ENUM('Tersedia', 'Disewa') DEFAULT 'Tersedia'
);

CREATE TABLE customer (
    id_customer INT AUTO_INCREMENT PRIMARY KEY,
    nama VARCHAR(100),
    alamat TEXT,
    no_telepon VARCHAR(20)
);

CREATE TABLE transaksi (
    id_transaksi INT AUTO_INCREMENT PRIMARY KEY,
    id_mobil INT,
    id_customer INT,
    tgl_sewa DATE,
    tgl_kembali DATE,
    total_bayar DECIMAL(10,2),
    status ENUM('Disewa', 'Kembali') DEFAULT 'Disewa',
    FOREIGN KEY (id_mobil) REFERENCES mobil(id_mobil),
    FOREIGN KEY (id_customer) REFERENCES customer(id_customer)
);
```

---

### b. Form Input (JSP)

**mobil-form.jsp**

```jsp

    
    
    
    
    Simpan Mobil

```

**customer-form.jsp**

```jsp

    
    
    
    Simpan Customer

```

**transaksi-form.jsp**

```jsp

    
        <%-- Load options dari DB --%>
    
    
        <%-- Load options dari DB --%>
    
    
    
    Simpan Transaksi

```

---

## 2. Proses Penyimpanan Data (Servlet)

**MobilServlet.java**

```java
@WebServlet("/MobilServlet")
public class MobilServlet extends HttpServlet {
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String merk = req.getParameter("merk");
        String tipe = req.getParameter("tipe");
        int tahun = Integer.parseInt(req.getParameter("tahun"));
        double harga = Double.parseDouble(req.getParameter("harga_sewa"));

        try (Connection conn = DBUtil.getConnection()) {
            PreparedStatement stmt = conn.prepareStatement("INSERT INTO mobil (merk, tipe, tahun, harga_sewa) VALUES (?, ?, ?, ?)");
            stmt.setString(1, merk);
            stmt.setString(2, tipe);
            stmt.setInt(3, tahun);
            stmt.setDouble(4, harga);
            stmt.executeUpdate();
            resp.sendRedirect("mobil-list.jsp");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

**CustomerServlet.java** dan **TransaksiServlet.java** mengikuti pola yang sama.

---

## 3. Desain Laporan Transaksi

**laporan-transaksi.jsp**

```jsp

    <%
        Connection conn = DBUtil.getConnection();
        Statement stmt = conn.createStatement();
        ResultSet rs = stmt.executeQuery("SELECT t.*, c.nama, m.merk FROM transaksi t JOIN customer c ON t.id_customer=c.id_customer JOIN mobil m ON t.id_mobil=m.id_mobil");
        int no = 1;
        while(rs.next()) {
    %>
    
    <% } %>
No	Nama Customer	Mobil	Tgl Sewa	Tgl Kembali	Total Bayar	Status
<%= no++ %>	<%= rs.getString("nama") %>	<%= rs.getString("merk") %>	<%= rs.getDate("tgl_sewa") %>	<%= rs.getDate("tgl_kembali") %>	<%= rs.getDouble("total_bayar") %>	<%= rs.getString("status") %>

```

---

## 4. Pendukung

**DBUtil.java**

```java
public class DBUtil {
    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection("jdbc:mysql://localhost:3306/rentcar", "root", "");
    }
}
```
