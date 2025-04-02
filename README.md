# AttendanceTracker
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Attendance Tracker</title>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-firestore.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.17.4/xlsx.full.min.js"></script>
    <style>
        body { font-family: Arial, sans-serif; text-align: center; margin: 50px; }
        table { width: 100%; border-collapse: collapse; margin-top: 20px; }
        th, td { border: 1px solid black; padding: 8px; text-align: left; }
    </style>
</head>
<body>
    <h1>Attendance Tracker</h1>
    <input type="text" id="userId" placeholder="Enter your ID">
    <button onclick="markAttendance()">Check In</button>
    <h2>Attendance Records</h2>
    <table id="attendanceTable">
        <tr><th>User ID</th><th>Date</th></tr>
    </table>
    <button onclick="exportToExcel()">Export to Excel</button>
    
    <script>
        const firebaseConfig = {
            apiKey: "YOUR_API_KEY",
            authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
            projectId: "YOUR_PROJECT_ID",
            storageBucket: "YOUR_PROJECT_ID.appspot.com",
            messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
            appId: "YOUR_APP_ID"
        };
        
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();

        const users = ["user001", "user002", "user003", "user200"]; // Manually added users
        
        function markAttendance() {
            const userId = document.getElementById('userId').value;
            if (!userId || !users.includes(userId)) return alert("Invalid ID");
            const date = new Date().toISOString().split('T')[0];
            db.collection("attendance").add({ userId, date })
                .then(() => alert("Attendance recorded"))
                .catch(error => console.error("Error: ", error));
        }
        
        function loadAttendance() {
            const table = document.getElementById('attendanceTable');
            db.collection("attendance").onSnapshot(snapshot => {
                table.innerHTML = "<tr><th>User ID</th><th>Date</th></tr>";
                snapshot.forEach(doc => {
                    const row = table.insertRow();
                    row.insertCell(0).innerText = doc.data().userId;
                    row.insertCell(1).innerText = doc.data().date;
                });
            });
        }
        
        function exportToExcel() {
            let table = document.getElementById("attendanceTable");
            let wb = XLSX.utils.table_to_book(table, { sheet: "Attendance" });
            XLSX.writeFile(wb, "Attendance.xlsx");
        }
        
        loadAttendance();
    </script>
</body>
</html>
