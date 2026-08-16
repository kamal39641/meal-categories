# meal-categories
Expo meal categories app

import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.net.Socket;
import java.util.Scanner;

public class Client {

    public static void main(String[] args) throws Exception {

        Socket s2 = new Socket("localhost", 7777);

        DataOutputStream ds2 =
                new DataOutputStream(s2.getOutputStream());

        DataInputStream ds1 =
                new DataInputStream(s2.getInputStream());

        Scanner sc = new Scanner(System.in);

        System.out.println("Connected to server.");
        System.out.println("Type Hello, date, or exit.");

        while (true) {

            // Take input from client console
            System.out.print("Client: ");
            String msg = sc.nextLine();

            // Send message to server
            ds2.writeUTF(msg);
            ds2.flush();

            // Receive response from server
            String response = ds1.readUTF();

            System.out.println("Server: " + response);

            // Exit
            if (msg.equalsIgnoreCase("exit")) {
                break;
            }
        }

        sc.close();
        s2.close();
    }
}


import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.net.ServerSocket;
import java.net.Socket;
import java.time.LocalDate;

public class Server {

    public static void main(String[] args) throws Exception {

        ServerSocket ss = new ServerSocket(7777);
        System.out.println("Server started...");
        System.out.println("Waiting for client...");

        Socket s1 = ss.accept();
        System.out.println("Client connected!");

        DataInputStream ds1 =
                new DataInputStream(s1.getInputStream());

        DataOutputStream ds2 =
                new DataOutputStream(s1.getOutputStream());

        while (true) {

            // Read message from client
            String msg = ds1.readUTF();

            System.out.println("Client: " + msg);

            // If client types Hello
            if (msg.equalsIgnoreCase("Hello")) {

                ds2.writeUTF("Hi");
                ds2.flush();

            }

            // If client types date
            else if (msg.equalsIgnoreCase("date")) {

                String currentDate = LocalDate.now().toString();

                ds2.writeUTF(currentDate);
                ds2.flush();

            }

            // Exit condition
            else if (msg.equalsIgnoreCase("exit")) {

                ds2.writeUTF("Connection closed.");
                ds2.flush();

                break;
            }

            // Unknown command
            else {

                ds2.writeUTF("Unknown command. Type Hello, date, or exit.");
                ds2.flush();
            }
        }

        s1.close();
        ss.close();

        System.out.println("Server stopped.");
    }
}
