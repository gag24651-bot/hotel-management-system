# hotel-management-system

#include <iostream>
#include <fstream>
#include <vector>
#include <string>
using namespace std;

// Room Class
class Room {
public:
    int roomNo;
    string type;
    double price;
    bool booked;

    Room(int no, string t, double p) {
        roomNo = no;
        type = t;
        price = p;
        booked = false;
    }

    void display() {
        cout << "\nRoom Number: " << roomNo;
        cout << "\nRoom Type: " << type;
        cout << "\nPrice: Rs." << price;
        cout << "\nStatus: "
             << (booked ? "Booked" : "Available") << endl;
    }
};

// Customer Class
class Customer {
public:
    int id;
    string name;
    string phone;
    int roomNo;

    Customer(int i, string n, string p, int r) {
        id = i;
        name = n;
        phone = p;
        roomNo = r;
    }

    void display() {
        cout << "\nCustomer ID: " << id;
        cout << "\nName: " << name;
        cout << "\nPhone: " << phone;
        cout << "\nRoom Number: " << roomNo << endl;
    }
};

// Hotel Management Class
class Hotel {
private:
    vector<Room> rooms;
    vector<Customer> customers;

public:

    Hotel() {
        rooms.push_back(Room(101, "Single", 1000));
        rooms.push_back(Room(102, "Double", 1500));
        rooms.push_back(Room(103, "Deluxe", 2000));
        rooms.push_back(Room(104, "Single", 1000));
        rooms.push_back(Room(105, "Deluxe", 2500));

        loadData();
    }

    // Load booking data from file
    void loadData() {
        ifstream file("bookings.txt");

        if (!file)
            return;

        int id, roomNo;
        string name, phone;

        while (file >> id) {
            file.ignore();
            getline(file, name, '|');
            getline(file, phone, '|');
            file >> roomNo;
            file.ignore();

            Room* room = findRoom(roomNo);

            if (room != nullptr && !room->booked) {
                customers.push_back(
                    Customer(id, name, phone, roomNo)
                );
                room->booked = true;
            }
        }

        file.close();
    }

    // Save booking data
    void saveData() {
        ofstream file("bookings.txt");

        for (auto &c : customers) {
            file << c.id << " "
                 << c.name << "|"
                 << c.phone << "|"
                 << c.roomNo << endl;
        }

        file.close();
    }

    // Find room
    Room* findRoom(int no) {
        for (auto &r : rooms) {
            if (r.roomNo == no)
                return &r;
        }
        return nullptr;
    }

    // Display rooms
    void displayRooms() {
        cout << "\n===== ROOM DETAILS =====\n";

        for (auto &r : rooms) {
            r.display();
        }
    }

    // Book room
    void bookRoom() {
        int id, roomNo;
        string name, phone;

        cout << "\nEnter Customer ID: ";
        cin >> id;

        for (auto &c : customers) {
            if (c.id == id) {
                cout << "Customer ID already exists!\n";
                return;
            }
        }

        cin.ignore();

        cout << "Enter Customer Name: ";
        getline(cin, name);

        cout << "Enter Phone Number: ";
        getline(cin, phone);

        cout << "Enter Room Number: ";
        cin >> roomNo;

        Room* room = findRoom(roomNo);

        if (room == nullptr) {
            cout << "\nInvalid Room Number!\n";
            return;
        }

        if (room->booked) {
            cout << "\nRoom Already Booked!\n";
            return;
        }

        room->booked = true;

        customers.push_back(
            Customer(id, name, phone, roomNo)
        );

        saveData();

        cout << "\nRoom Booked Successfully!\n";
        cout << "Room Price: Rs." << room->price << endl;
    }

    // Checkout
    void checkout() {
        int roomNo;

        cout << "\nEnter Room Number: ";
        cin >> roomNo;

        for (auto it = customers.begin();
             it != customers.end(); it++) {

            if (it->roomNo == roomNo) {

                Room* room = findRoom(roomNo);

                if (room != nullptr)
                    room->booked = false;

                cout << "\nCheckout Successful!\n";
                cout << "Customer Name: "
                     << it->name << endl;

                customers.erase(it);

                saveData();
                return;
            }
        }

        cout << "\nNo Booking Found!\n";
    }

    // Search customer
    void searchCustomer() {
        int id;

        cout << "\nEnter Customer ID: ";
        cin >> id;

        for (auto &c : customers) {
            if (c.id == id) {
                cout << "\nCustomer Found!\n";
                c.display();
                return;
            }
        }

        cout << "\nCustomer Not Found!\n";
    }

    // Display all customers
    void displayCustomers() {
        cout << "\n===== CUSTOMER RECORDS =====\n";

        if (customers.empty()) {
            cout << "No Customer Records Found!\n";
            return;
        }

        for (auto &c : customers) {
            c.display();
            cout << "-------------------\n";
        }
    }
};

// Main Function
int main() {

    Hotel hotel;
    int choice;

    do {
        cout << "\n\n================================";
        cout << "\n     HOTEL MANAGEMENT SYSTEM";
        cout << "\n================================";
        cout << "\n1. Display Rooms";
        cout << "\n2. Book Room";
        cout << "\n3. Checkout";
        cout << "\n4. Search Customer";
        cout << "\n5. Display Customers";
        cout << "\n6. Exit";
        cout << "\n================================";

        cout << "\nEnter Your Choice: ";
        cin >> choice;

        switch (choice) {

            case 1:
                hotel.displayRooms();
                break;

            case 2:
                hotel.bookRoom();
                break;

            case 3:
                hotel.checkout();
                break;

            case 4:
                hotel.searchCustomer();
                break;

            case 5:
                hotel.displayCustomers();
                break;

            case 6:
                cout << "\nThank You for Using "
                     << "Hotel Management System!\n";
                break;

            default:
                cout << "\nInvalid Choice! Try Again.\n";
        }

    } while (choice != 6);

    return 0;
}
