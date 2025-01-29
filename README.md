 <?php
$servername = "localhost";
$username = "root"; // default username for localhost
$password = ""; // default password for localhost
$dbname = "hospital_db";

// Create connection
$conn = new mysqli($servername, $username, $password);

// Check connection
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}

// Create database
$sql = "CREATE DATABASE IF NOT EXISTS $dbname";
if ($conn->query($sql) === TRUE) {
    echo "Database created successfully<br>";
} else {
    echo "Error creating database: " . $conn->error;
}

// Select database
$conn->select_db($dbname);

// Create Doctor table
$sql = "CREATE TABLE IF NOT EXISTS Doctor (
    DID INT AUTO_INCREMENT PRIMARY KEY,
    Dname VARCHAR(100) NOT NULL,
    Specialist ENUM('Medicine', 'Surgery', 'Child', 'Cardiologist', 'Oncologist') NOT NULL
)";
if ($conn->query($sql) === TRUE) {
    echo "Table Doctor created successfully<br>";
} else {
    echo "Error creating table Doctor: " . $conn->error;
}

// Create Patient table
$sql = "CREATE TABLE IF NOT EXISTS Patient (
    PID INT AUTO_INCREMENT PRIMARY KEY,
    Pname VARCHAR(100) NOT NULL,
    sex ENUM('Male', 'Female') NOT NULL,
    age INT NOT NULL,
    Phone VARCHAR(15) NOT NULL
)";
if ($conn->query($sql) === TRUE) {
    echo "Table Patient created successfully<br>";
} else {
    echo "Error creating table Patient: " . $conn->error;
}

// Create Treatment table
$sql = "CREATE TABLE IF NOT EXISTS Treatment (
    DID INT,
    PID INT,
    Tdate DATE NOT NULL,
    Diagnosis TEXT NOT NULL,
    PRIMARY KEY (DID, PID, Tdate),
    FOREIGN KEY (DID) REFERENCES Doctor(DID),
    FOREIGN KEY (PID) REFERENCES Patient(PID)
)";
if ($conn->query($sql) === TRUE) {
    echo "Table Treatment created successfully<br>";
} else {
    echo "Error creating table Treatment: " . $conn->error;
}

// Insert records into Doctor table
$sql = "INSERT INTO Doctor (Dname, Specialist) VALUES
('Rajib Mehata', 'Cardiologist'),
('Anjali Singh', 'Medicine'),
('Ravi Kumar', 'Surgery'),
('Priya Sharma', 'Child'),
('Sunil Gupta', 'Oncologist')";
if ($conn->query($sql) === TRUE) {
    echo "Records inserted into Doctor table successfully<br>";
} else {
    echo "Error inserting records into Doctor table: " . $conn->error;
}

// Insert records into Patient table
$sql = "INSERT INTO Patient (Pname, sex, age, Phone) VALUES
('Amit Kumar', 'Male', 70, '1234567890'),
('Sunita Devi', 'Female', 65, '2345678901'),
('Rahul Sharma', 'Male', 40, '3456789012'),
('Priya Singh', 'Female', 30, '4567890123'),
('Vijay Patel', 'Male', 50, '5678901234')";
if ($conn->query($sql) === TRUE) {
    echo "Records inserted into Patient table successfully<br>";
} else {
    echo "Error inserting records into Patient table: " . $conn->error;
}

// Insert records into Treatment table
$sql = "INSERT INTO Treatment (DID, PID, Tdate, Diagnosis) VALUES
(1, 1, '2023-01-10', 'Heart Disease'),
(2, 2, '2023-01-10', 'Fever'),
(2, 3, '2023-01-10', 'Fracture'),
(2, 4, '2023-01-10', 'Cold'),
(3, 5, '2023-01-11', 'Cancer')";
if ($conn->query($sql) === TRUE) {
    echo "Records inserted into Treatment table successfully<br>";
} else {
    echo "Error inserting records into Treatment table: " . $conn->error;
}

// Query (a): List the names of the oldest patient treated by doctor Rajib Mehata
$sql = "SELECT P.Pname 
        FROM Patient P
        JOIN Treatment T ON P.PID = T.PID
        JOIN Doctor D ON T.DID = D.DID
        WHERE D.Dname = 'Rajib Mehata'
        ORDER BY P.age DESC
        LIMIT 1";
$result = $conn->query($sql);

if ($result->num_rows > 0) {
    echo "Oldest patient treated by Dr. Rajib Mehata: ";
    while($row = $result->fetch_assoc()) {
        echo $row["Pname"] . "<br>";
    }
} else {
    echo "No results found for query (a)<br>";
}

// Query (b): List the name of specialist doctor in medicine who have treated more than two patients on 10.01.2023
$sql = "SELECT D.Dname 
        FROM Doctor D
        JOIN Treatment T ON D.DID = T.DID
        WHERE D.Specialist = 'Medicine' AND T.Tdate = '2023-01-10'
        GROUP BY D.DID
        HAVING COUNT(T.PID) > 2";
$result = $conn->query($sql);

if ($result->num_rows > 0) {
    echo "Specialist doctors in Medicine who treated more than two patients on 10.01.2023: ";
    while($row = $result->fetch_assoc()) {
        echo $row["Dname"] . "<br>";
    }
} else {
    echo "No results found for query (b)<br>";
}

$conn->close();
?>
