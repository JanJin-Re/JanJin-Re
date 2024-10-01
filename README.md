<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>สถานะห้องพักและราคา</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #F5DEB3;
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            flex-direction: column;
        }
        h1 {
            text-align: center;
            color: #333;
            margin-bottom: 20px;
        }
        table {
            width: 60%;
            border-collapse: collapse;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            background-color: white;
        }
        table, th, td {
            border: 1px solid #ddd;
        }
        th, td {
            text-align: center;
            vertical-align: middle;
            padding: 10px;
        }
        th {
            background-color: #8B4513;
            color: white;
            font-size: 18px;
        }
        td {
            font-size: 16px;
        }
        .available {
            background-color: #c8e6c9;
            color: #2e7d32;
        }
        .unavailable {
            background-color: #ffccbc;
            color: #d84315;
        }
        button {
            padding: 8px 12px;
            font-size: 16px;
            color: white;
            background-color: #8B4513;
            border: none;
            cursor: pointer;
            border-radius: 5px;
        }
        button:hover {
            background-color: #A0522D;
        }
        .admin-only {
            display: none;
        }
        .show-admin .admin-only {
            display: table-cell;
        }
        .toggle-btn {
            padding: 10px 20px;
            font-size: 18px;
            background-color: #8B4513;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            transition: background-color 0.3s;
            margin-bottom: 20px;
        }
        .toggle-btn:hover {
            background-color: #A0522D;
        }
        #loginModal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.5);
            justify-content: center;
            align-items: center;
        }
        #loginModal.show {
            display: flex;
        }
        #loginForm {
            background-color: white;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
        }
        #loginForm input {
            margin: 10px 0;
            padding: 10px;
            width: 100%;
            font-size: 16px;
        }
        #loginForm button {
            width: 100%;
            padding: 10px;
            background-color: #8B4513;
            color: white;
            border: none;
            border-radius: 5px;
            font-size: 16px;
            cursor: pointer;
        }
        #loginForm button:hover {
            background-color: #A0522D;
        }
    </style>
</head>
<body>

<h1>สถานะห้องพักและราคา</h1>

<!-- ปุ่มสำหรับเปิด/ปิดโหมดผู้ดูแล -->
<button id="adminToggleBtn" class="toggle-btn" onclick="toggleAdminMode()">เข้าสู่โหมดผู้ดูแล</button>

<!-- ตารางแสดงสถานะห้องและราคา -->
<table>
    <thead>
        <tr>
            <th>หมายเลขห้อง</th>
            <th>สถานะ</th>
            <th>ราคา (บาท/คืน)</th>
            <th class="admin-only">อัพเดทสถานะ</th>
        </tr>
    </thead>
    <tbody id="room-status">
        <tr>
            <td>1</td>
            <td class="status available">ว่าง</td>
            <td>500</td>
            <td class="admin-only"><button onclick="updateStatus(this)">อัพเดท</button></td>
        </tr>
        <tr>
            <td>2</td>
            <td class="status available">ว่าง</td>
            <td>500</td>
            <td class="admin-only"><button onclick="updateStatus(this)">อัพเดท</button></td>
        </tr>
        <tr>
            <td>3</td>
            <td class="status available">ว่าง</td>
            <td>500</td>
            <td class="admin-only"><button onclick="updateStatus(this)">อัพเดท</button></td>
        </tr>
        <tr>
            <td>4</td>
            <td class="status available">ว่าง</td>
            <td>500</td>
            <td class="admin-only"><button onclick="updateStatus(this)">อัพเดท</button></td>
        </tr>
        <tr>
            <td>5</td>
            <td class="status available">ว่าง</td>
            <td>450</td>
            <td class="admin-only"><button onclick="updateStatus(this)">อัพเดท</button></td>
        </tr>
        <tr>
            <td>6</td>
            <td class="status available">ว่าง</td>
            <td>600</td>
            <td class="admin-only"><button onclick="updateStatus(this)">อัพเดท</button></td>
        </tr>
        <tr>
            <td>7</td>
            <td class="status available">ว่าง</td>
            <td>600</td>
            <td class="admin-only"><button onclick="updateStatus(this)">อัพเดท</button></td>
        </tr>
    </tbody>
</table>

<!-- Modal สำหรับล็อกอิน -->
<div id="loginModal">
    <div id="loginForm">
        <h2>เข้าสู่ระบบผู้ดูแล</h2>
        <input type="email" id="email" placeholder="อีเมล" required>
        <input type="password" id="password" placeholder="รหัสผ่าน" required>
        <button onclick="login()">เข้าสู่ระบบ</button>
    </div>
</div>

<script>
    const correctEmail = "admin@example.com";
    const correctPassword = "password123";
    let isAdmin = false;

    // ฟังก์ชันเปิด modal ล็อกอิน
    function openLoginModal() {
        document.getElementById("loginModal").classList.add("show");
        document.getElementById("email").value = '';
        document.getElementById("password").value = '';
    }

    // ฟังก์ชันปิด modal ล็อกอิน
    function closeLoginModal() {
        document.getElementById("loginModal").classList.remove("show");
    }

    // ฟังก์ชันล็อกอิน
    function login() {
        const email = document.getElementById("email").value;
        const password = document.getElementById("password").value;

        if (email === correctEmail && password === correctPassword) {
            isAdmin = true;
            document.body.classList.add("show-admin");
            document.getElementById("adminToggleBtn").textContent = "ออกจากโหมดผู้ดูแล";
            closeLoginModal();
        } else {
            alert("อีเมลหรือรหัสผ่านไม่ถูกต้อง");
        }
    }

    // ฟังก์ชันเปิด/ปิดโหมดผู้ดูแล
    function toggleAdminMode() {
        if (!isAdmin) {
            openLoginModal();
        } else {
            isAdmin = false;
            document.body.classList.remove("show-admin");
            document.getElementById("adminToggleBtn").textContent = "เข้าสู่โหมดผู้ดูแล";
        }
    }

    // ฟังก์ชันสำหรับการอัพเดทสถานะห้อง
    function updateStatus(button) {
        const statusCell = button.parentElement.previousElementSibling.previousElementSibling;
        if (statusCell.textContent === 'ว่าง') {
            statusCell.textContent = 'ไม่ว่าง';
            statusCell.classList.remove('available');
            statusCell.classList.add('unavailable');
        } else {
            statusCell.textContent = 'ว่าง';
            statusCell.classList.remove('unavailable');
            statusCell.classList.add('available');
        }
    }
</script>

</body>
</html>
