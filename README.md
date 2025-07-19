# Student-Registration-System
The Student Registration System is a simple full-stack web application designed to allow users to manage a list of students. Users can add new students with their name and email, view all registered students, and delete existing records. 
1. StudentApplication.java

@SpringBootApplication
public class StudentApplication {
    public static void main(String[] args) {
        SpringApplication.run(StudentApplication.class, args);
    }
}
2. Student.java 

@Entity
public class Student {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String email;
3. StudentRepository.java

public interface StudentRepository extends JpaRepository<Student, Long> {}
4. StudentController.java

@RestController
@CrossOrigin(origins = "*")
@RequestMapping("/api/students")
public class StudentController {
@Autowired
    private StudentRepository studentRepository;

    @GetMapping
    public List<Student> getAllStudents() {
        return studentRepository.findAll();
    }

    @PostMapping
    public Student createStudent(@RequestBody Student student) {
        return studentRepository.save(student);
    }

    @DeleteMapping("/{id}")
    public void deleteStudent(@PathVariable Long id) {
        studentRepository.deleteById(id);
    }}
5. application.properties
properties
CopyEdit
spring.datasource.url=jdbc:sqlserver://localhost:1433;databaseName=StudentDB
spring.datasource.username=your_sqlserver_username
spring.datasource.password=your_password
spring.datasource.driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.SQLServerDialect

server.port=8080


pom.xml:

<dependency>
    <groupId>com.microsoft.sqlserver</groupId>
    <artifactId>mssql-jdbc</artifactId>
    <version>12.2.0.jre11</version> <!-- match with your JDK version -->
</dependency>


Frontend Code
index.html

<!DOCTYPE html>
<html>
<head>
    <title>Student Registration</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <h1>Student Registration</h1>
<input type="text" id="name" placeholder="Name">
    <input type="email" id="email" placeholder="Email">
    <button onclick="addStudent()">Add Student</button>
    <ul id="studentList"></ul>

    <script src="script.js"></script>
</body>
</html>

style.css

body {
    font-family: Arial, sans-serif;
    margin: 40px;
    text-align: center;
}
input {
    margin: 5px;
    padding: 10px;
}
button {
    padding: 10px;
}
li {
margin-top: 10px;
}
script.js

const API_URL = 'http://localhost:8080/api/students';
async function fetchStudents() {
    const response = await fetch(API_URL);
    const students = await response.json();
    const list = document.getElementById("studentList");
    list.innerHTML = "";
    students.forEach(student => {
        const li = document.createElement("li");
        li.innerHTML = `${student.name} (${student.email}) 
            <button onclick="deleteStudent(${student.id})">Delete</button>`;
        list.appendChild(li);
    });
}

async function addStudent() {
    const name = document.getElementById("name").value;
const email = document.getElementById("email").value;

    await fetch(API_URL, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ name, email })
    });

     document.getElementById("name").value = "";
    document.getElementById("email").value = "";

    fetchStudents();
}
async function deleteStudent(id) {
    await fetch(`${API_URL}/${id}`, {
        method: "DELETE"
    });
    fetchStudents();
}

window.onload = fetchStudents;

