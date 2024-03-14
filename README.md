# JDBC-Assignment
SQL:
CREATE TABLE tasks (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    description VARCHAR(1000),
    due_date DATE
);
import java.io.*;
import java.sql.*;
import javax.servlet.*;
import javax.servlet.http.*;

public class TaskServlet extends HttpServlet {
    private Connection conn;

    public void init() throws ServletException {
        // Establish database connection
        conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/task_db", "username", "password");
    }

    public void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // Display tasks
        try {
            Statement stmt = conn.createStatement();
            ResultSet rs = stmt.executeQuery("SELECT * FROM tasks");
            request.setAttribute("tasks", rs);
            RequestDispatcher dispatcher = request.getRequestDispatcher("tasks.jsp");
            dispatcher.forward(request, response);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // Handle form submissions
        String action = request.getParameter("action");
        if (action.equals("add")) {
            // Add new task
            String title = request.getParameter("title");
            String description = request.getParameter("description");
            String dueDate = request.getParameter("due_date");

            try {
                PreparedStatement pstmt = conn.prepareStatement("INSERT INTO tasks (title, description, due_date) VALUES (?, ?, ?)");
                pstmt.setString(1, title);
                pstmt.setString(2, description);
                pstmt.setString(3, dueDate);
                pstmt.executeUpdate();
                response.sendRedirect(request.getContextPath() + "/tasks");
            } catch (SQLException e) {
                e.printStackTrace();
            }
        } else if (action.equals("update")) {
            // Update task
            // Similar logic as add, but using UPDATE statement
        } else if (action.equals("delete")) {
            // Delete task
            // Similar logic as add, but using DELETE statement
        }
    }

    public void destroy() {
        // Close database connection
        try {
            conn.close();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
