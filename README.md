package com.school;

import java.sql.Date;

public class Student {
    private int id;
    private String firstName;
    private String lastName;
    private String email;
    private String phone;
    private Date enrollmentDate;

    public Student() {}

    public Student(int id, String firstName, String lastName, String email, String phone, Date enrollmentDate) {
        this.id = id;
        this.firstName = firstName;
        this.lastName = lastName;
        this.email = email;
        this.phone = phone;
        this.enrollmentDate = enrollmentDate;
    }

    // Getters and setters
    public int getId() { return id; }
    public void setId(int id) { this.id = id; }
    public String getFirstName() { return firstName; }
    public void setFirstName(String firstName) { this.firstName = firstName; }
    public String getLastName() { return lastName; }
    public void setLastName(String lastName) { this.lastName = lastName; }
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
    public String getPhone() { return phone; }
    public void setPhone(String phone) { this.phone = phone; }
    public Date getEnrollmentDate() { return enrollmentDate; }
    public void setEnrollmentDate(Date enrollmentDate) { this.enrollmentDate = enrollmentDate; }
}
package com.school;

import java.sql.*;
import java.util.ArrayList;
import java.util.List;

public class StudentDAO {
    private Connection getConnection() throws SQLException {
        return DriverManager.getConnection("jdbc:mysql://localhost:3306/school_db", "root", "password");
    }

    public void addStudent(Student student) {
        String query = "INSERT INTO students (first_name, last_name, email, phone, enrollment_date) VALUES (?, ?, ?, ?, ?)";
        try (Connection conn = getConnection();
             PreparedStatement stmt = conn.prepareStatement(query)) {
            stmt.setString(1, student.getFirstName());
            stmt.setString(2, student.getLastName());
            stmt.setString(3, student.getEmail());
            stmt.setString(4, student.getPhone());
            stmt.setDate(5, student.getEnrollmentDate());
            stmt.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public Student getStudent(int id) {
        String query = "SELECT * FROM students WHERE id = ?";
        try (Connection conn = getConnection();
             PreparedStatement stmt = conn.prepareStatement(query)) {
            stmt.setInt(1, id);
            ResultSet rs = stmt.executeQuery();
            if (rs.next()) {
                return new Student(
                    rs.getInt("id"),
                    rs.getString("first_name"),
                    rs.getString("last_name"),
                    rs.getString("email"),
                    rs.getString("phone"),
                    rs.getDate("enrollment_date")
                );
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return null;
    }

    public List<Student> getAllStudents() {
        List<Student> students = new ArrayList<>();
        String query = "SELECT * FROM students";
        try (Connection conn = getConnection();
             PreparedStatement stmt = conn.prepareStatement(query);
             ResultSet rs = stmt.executeQuery()) {
            while (rs.next()) {
                students.add(new Student(
                    rs.getInt("id"),
                    rs.getString("first_name"),
                    rs.getString("last_name"),
                    rs.getString("email"),
                    rs.getString("phone"),
                    rs.getDate("enrollment_date")
                ));
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return students;
    }

    public void updateStudent(Student student) {
        String query = "UPDATE students SET first_name = ?, last_name = ?, email = ?, phone = ?, enrollment_date = ? WHERE id = ?";
        try (Connection conn = getConnection();
             PreparedStatement stmt = conn.prepareStatement(query)) {
            stmt.setString(1, student.getFirstName());
            stmt.setString(2, student.getLastName());
            stmt.setString(3, student.getEmail());
            stmt.setString(4, student.getPhone());
            stmt.setDate(5, student.getEnrollmentDate());
            stmt.setInt(6, student.getId());
            stmt.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public void deleteStudent(int id) {
        String query = "DELETE FROM students WHERE id = ?";
        try (Connection conn = getConnection();
             PreparedStatement stmt = conn.prepareStatement(query)) {
            stmt.setInt(1, id);
            stmt.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
package com.school;

import java.sql.Date;
import java.util.List;
import java.util.Scanner;

public class Main {
    private static StudentDAO studentDAO = new StudentDAO();

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        while (true) {
            System.out.println("1. Add Student");
            System.out.println("2. View Student");
            System.out.println("3. Update Student");
            System.out.println("4. Delete Student");
            System.out.println("5. List All Students");
            System.out.println("6. Exit");
            System.out.print("Choose an option: ");
            int choice = scanner.nextInt();
            scanner.nextLine(); // Consume newline

            switch (choice) {
                case 1:
                    System.out.print("First Name: ");
                    String firstName = scanner.nextLine();
                    System.out.print("Last Name: ");
                    String lastName = scanner.nextLine();
                    System.out.print("Email: ");
                    String email = scanner.nextLine();
                    System.out.print("Phone: ");
                    String phone = scanner.nextLine();
                    System.out.print("Enrollment Date (yyyy-mm-dd): ");
                    Date enrollmentDate = Date.valueOf(scanner.nextLine());
                    Student newStudent = new Student(0, firstName, lastName, email, phone, enrollmentDate);
                    studentDAO.addStudent(newStudent);
                    break;

                case 2:
                    System.out.print("Enter Student ID: ");
                    int id = scanner.nextInt();
                    Student student = studentDAO.getStudent(id);
                    if (student != null) {
                        System.out.println("ID: " + student.getId());
                        System.out.println("First Name: " + student.getFirstName());
                        System.out.println("Last Name: " + student.getLastName());
                        System.out.println("Email: " + student.getEmail());
                        System.out.println("Phone: " + student.getPhone());
                        System.out.println("Enrollment Date: " + student.getEnrollmentDate());
                    } else {
                        System.out.println("Student not found.");
                    }
                    break;

                case 3:
                    System.out.print("Enter Student ID: ");
                    id = scanner.nextInt();
                    scanner.nextLine(); // Consume newline
                    System.out.print("New First Name: ");
                    firstName = scanner.nextLine();
                    System.out.print("New Last Name: ");
                    lastName = scanner.nextLine();
                    System.out.print("New Email: ");
                    email = scanner.nextLine();
                    System.out.print("New Phone: ");
                    phone = scanner.nextLine();
                    System.out.print("New Enrollment Date (yyyy-mm-dd): ");
                    enrollmentDate = Date.valueOf(scanner.nextLine());
                    Student updatedStudent = new Student(id, firstName, lastName, email, phone, enrollmentDate);
                    studentDAO.updateStudent(updatedStudent);
                    break;

                case 4:
                    System.out.print("Enter Student ID to delete: ");
                    id = scanner.nextInt();
                    studentDAO.deleteStudent(id);
                    break;

                case 5:
                    List<Student> students = studentDAO.getAllStudents();
                    for (Student s : students) {
                        System.out.println("ID: " + s.getId() + ", Name: " + s.getFirstName() + " " + s.getLastName() + ", Email: " + s.getEmail());
                    }
                    break;

                case 6:
                    scanner.close();
                    return;

                default:
                    System.out.println("Invalid option. Please try again.");
            }
        }
    }
}
