# Java-project-LMS
package com.library.librarymanagement;

import java.sql.*;
import java.util.Scanner;

public class LibraryManagement {
    static Connection con;
    static Scanner sc = new Scanner(System.in);

    static {
        try {
            con = DriverManager.getConnection("jdbc:mysql://localhost:3306/", "root", "root");
            Statement stmt = con.createStatement();
            stmt.executeUpdate("CREATE DATABASE IF NOT EXISTS lib_management");
            stmt.execute("USE lib_management");

            stmt.executeUpdate("CREATE TABLE IF NOT EXISTS books (bname VARCHAR(40), bcode VARCHAR(10), total INT, subject VARCHAR(40))");
            stmt.executeUpdate("CREATE TABLE IF NOT EXISTS issue (name VARCHAR(40), reg_no VARCHAR(20), bcode VARCHAR(10), issue_date DATE)");
            stmt.executeUpdate("CREATE TABLE IF NOT EXISTS submit (name VARCHAR(40), reg_no VARCHAR(20), bcode VARCHAR(10), submit_date DATE)");
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public static void addBook() {
        try {
            System.out.print("Enter BOOK Name: ");
            String bn = sc.nextLine();
            System.out.print("Enter BOOK Code: ");
            String co = sc.nextLine();
            System.out.print("Total BOOKS: ");
            int t = sc.nextInt();
            sc.nextLine();
            System.out.print("Enter Subject: ");
            String su = sc.nextLine();

            String sql = "INSERT INTO books VALUES (?, ?, ?, ?)";
            PreparedStatement ps = con.prepareStatement(sql);
            ps.setString(1, bn);
            ps.setString(2, co);
            ps.setInt(3, t);
            ps.setString(4, su);
            ps.executeUpdate();
            System.out.println("Data Entered Successfully");
        } catch (SQLException e) {
            e.printStackTrace();
        }
        mainMenu();
    }

    public static void issueBook() {
        try {
            System.out.print("Enter Name: ");
            String n = sc.nextLine();
            System.out.print("Enter Reg No: ");
            String r = sc.nextLine();
            System.out.print("Enter Book Code: ");
            String co = sc.nextLine();
            System.out.print("Enter Date (YYYY-MM-DD): ");
            String d = sc.nextLine();

            String sql = "INSERT INTO issue VALUES (?, ?, ?, ?)";
            PreparedStatement ps = con.prepareStatement(sql);
            ps.setString(1, n);
            ps.setString(2, r);
            ps.setString(3, co);
            ps.setDate(4, Date.valueOf(d));
            ps.executeUpdate();

            System.out.println("Book issued to " + n);
            updateBook(co, -1);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public static void submitBook() {
        try {
            System.out.print("Enter Name: ");
            String n = sc.nextLine();
            System.out.print("Enter Reg No: ");
            String r = sc.nextLine();
            System.out.print("Enter Book Code: ");
            String co = sc.nextLine();
            System.out.print("Enter Date (YYYY-MM-DD): ");
            String d = sc.nextLine();

            String sql = "INSERT INTO submit VALUES (?, ?, ?, ?)";
            PreparedStatement ps = con.prepareStatement(sql);
            ps.setString(1, n);
            ps.setString(2, r);
            ps.setString(3, co);
            ps.setDate(4, Date.valueOf(d));
            ps.executeUpdate();

            System.out.println("Book submitted from: " + n);
            updateBook(co, 1);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public static void updateBook(String co, int u) {
        try {
            String sql = "SELECT total FROM books WHERE bcode = ?";
            PreparedStatement ps = con.prepareStatement(sql);
            ps.setString(1, co);
            ResultSet rs = ps.executeQuery();
            if (rs.next()) {
                int t = rs.getInt("total") + u;
                String updateSql = "UPDATE books SET total = ? WHERE bcode = ?";
                ps = con.prepareStatement(updateSql);
                ps.setInt(1, t);
                ps.setString(2, co);
                ps.executeUpdate();
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        mainMenu();
    }

    public static void deleteBook() {
        try {
            System.out.print("Enter Book Code: ");
            String ac = sc.nextLine();

            String sql = "DELETE FROM books WHERE bcode = ?";
            PreparedStatement ps = con.prepareStatement(sql);
            ps.setString(1, ac);
            ps.executeUpdate();

            System.out.println("Book deleted successfully.");
        } catch (SQLException e) {
            e.printStackTrace();
        }
        mainMenu();
    }

    public static void displayBooks() {
        try {
            String sql = "SELECT * FROM books";
            PreparedStatement ps = con.prepareStatement(sql);
            ResultSet rs = ps.executeQuery();

            while (rs.next()) {
                System.out.println("Book Name: " + rs.getString("bname"));
                System.out.println("Book Code: " + rs.getString("bcode"));
                System.out.println("Total: " + rs.getInt("total"));
                System.out.println("Subject: " + rs.getString("subject"));
                System.out.println("------------------------------------------");
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        mainMenu();
    }

    public static void mainMenu() {
        while (true) {
            System.out.println("LIBRARY MANAGER");
            System.out.println("1. ADD BOOK\n2. ISSUE BOOK\n3. SUBMIT BOOK\n4. DELETE BOOK\n5. DISPLAY BOOKS\n6. EXIT");
            System.out.print("Enter Task No: ");
            int ch = sc.nextInt();
            sc.nextLine();

            switch (ch) {
                case 1:
                    addBook();
                    break;
                case 2:
                    issueBook();
                    break;
                case 3:
                    submitBook();
                    break;
                case 4:
                    deleteBook();
                    break;
                case 5:
                    displayBooks();
                    break;
                case 6:
                    System.out.println("Exiting Program.");
                    System.exit(0);
                default:
                    System.out.println("WRONG CHOICE!!!!");
            }
        }
    }

    public static void passwordPrompt() {
        while (true) {
            System.out.print("Enter Password: ");
            String ps = sc.nextLine();

            if (ps.equals("asdfg")) {
                mainMenu();
                break;
            } else {
                System.out.println("Wrong Password! Try Again.");
            }
        }
    }

    public static void main(String[] args) {
        passwordPrompt();
    }
}
