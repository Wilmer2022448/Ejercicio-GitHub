package ejerciciorepaso;

import org.h2.tools.Server;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.Statement;
import java.sql.ResultSet;
import java.util.Scanner;

public class Clase12H2 {

    private static final String JDBC_URL = "jdbc:h2:~/testdb;AUTO_SERVER=TRUE";
    private static final String USER = "sa";
    private static final String PASSWORD = "";

    public static void main(String[] args) {

        Server webServer = null;

        try {
            Class.forName("org.h2.Driver");

            webServer = Server.createWebServer(
                    "-web",
                    "-webAllowOthers",
                    "-webPort", "8082"
            ).start();

            System.out.println("H2 Console started at: " + webServer.getURL());

            try (Connection conn = DriverManager.getConnection(JDBC_URL, USER, PASSWORD);
                 Statement stmt = conn.createStatement()) {

                stmt.execute("CREATE TABLE IF NOT EXISTS Users(" +
                        "id INT PRIMARY KEY, " +
                        "name VARCHAR(255))");

                stmt.executeUpdate("MERGE INTO Users KEY(id) VALUES(3, 'Pedro')");

                ResultSet rs = stmt.executeQuery("SELECT * FROM Users");

                System.out.println("Usuarios registrados:");
                while (rs.next()) {
                    System.out.println("ID: " + rs.getInt("id") +
                            " - Nombre: " + rs.getString("name"));
                }

                System.out.println("Database initialized and persisted to file.");
            }

            System.out.println("Press Enter to stop the server and exit...");
            new Scanner(System.in).nextLine();

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (webServer != null) {
                webServer.stop();
            }
        }
    }
}
