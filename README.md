# OOPs_MINI_PROJECT.#include <iostream>
#include <fstream>
#include <iomanip>
#include <string>
using namespace std;

class Hotel {
    int room_no;
    string name;
    string address;
    string phone;

public:
    void mainMenu();
    void addCustomer();
    void displayRooms();
    void displayCustomer(int);
    void editCustomer(int);
    void deleteCustomer(int);
    void checkOut(int);
};

void Hotel::mainMenu() {
    int choice;
    while (true) {
        system("cls"); // clear screen (works on Windows)
        cout << "\n\t\t======================================";
        cout << "\n\t\t   HOTEL MANAGEMENT SYSTEM";
        cout << "\n\t\t======================================";
        cout << "\n\n\t1. Book a Room";
        cout << "\n\t2. Display All Booked Rooms";
        cout << "\n\t3. Display Customer Record";
        cout << "\n\t4. Edit Customer Record";
        cout << "\n\t5. Delete Customer Record";
        cout << "\n\t6. Check-Out";
        cout << "\n\t7. Exit";
        cout << "\n\n\tEnter your choice: ";
        cin >> choice;

        switch (choice) {
        case 1: addCustomer(); break;
        case 2: displayRooms(); break;
        case 3: {
            int r;
            cout << "\nEnter Room No.: ";
            cin >> r;
            displayCustomer(r);
            break;
        }
        case 4: {
            int r;
            cout << "\nEnter Room No.: ";
            cin >> r;
            editCustomer(r);
            break;
        }
        case 5: {
            int r;
            cout << "\nEnter Room No.: ";
            cin >> r;
            deleteCustomer(r);
            break;
        }
        case 6: {
            int r;
            cout << "\nEnter Room No.: ";
            cin >> r;
            checkOut(r);
            break;
        }
        case 7:
            cout << "\nExiting... Thank you!";
            exit(0);
        default:
            cout << "\nInvalid choice. Try again!";
        }
        cout << "\n\nPress Enter to continue...";
        cin.ignore();
        cin.get();
    }
}

void Hotel::addCustomer() {
    ofstream fout("hotel.dat", ios::app | ios::binary);

    cout << "\nEnter Room No.: ";
    cin >> room_no;
    cout << "Enter Name: ";
    cin.ignore();
    getline(cin, name);
    cout << "Enter Address: ";
    getline(cin, address);
    cout << "Enter Phone No.: ";
    getline(cin, phone);

    fout.write((char*)this, sizeof(Hotel));
    fout.close();

    cout << "\nRoom Booked Successfully!";
}

void Hotel::displayRooms() {
    ifstream fin("hotel.dat", ios::in | ios::binary);
    if (!fin) {
        cout << "\nNo records found!";
        return;
    }

    cout << "\n\n\t\tList of Booked Rooms\n";
    cout << "-------------------------------------------------------------\n";
    cout << setw(10) << "Room No" << setw(20) << "Name" << setw(25) << "Address" << setw(15) << "Phone\n";
    cout << "-------------------------------------------------------------\n";

    while (fin.read((char*)this, sizeof(Hotel))) {
        cout << setw(10) << room_no << setw(20) << name << setw(25) << address << setw(15) << phone << endl;
    }

    fin.close();
}

void Hotel::displayCustomer(int r) {
    ifstream fin("hotel.dat", ios::in | ios::binary);
    bool found = false;

    while (fin.read((char*)this, sizeof(Hotel))) {
        if (room_no == r) {
            cout << "\nRoom No.: " << room_no;
            cout << "\nName: " << name;
            cout << "\nAddress: " << address;
            cout << "\nPhone: " << phone;
            found = true;
            break;
        }
    }
    fin.close();

    if (!found)
        cout << "\nCustomer not found!";
}

void Hotel::editCustomer(int r) {
    fstream file("hotel.dat", ios::in | ios::out | ios::binary);
    bool found = false;

    while (file.read((char*)this, sizeof(Hotel))) {
        if (room_no == r) {
            cout << "\nEnter New Name: ";
            cin.ignore();
            getline(cin, name);
            cout << "Enter New Address: ";
            getline(cin, address);
            cout << "Enter New Phone: ";
            getline(cin, phone);

            int pos = -1 * sizeof(Hotel);
            file.seekp(pos, ios::cur);
            file.write((char*)this, sizeof(Hotel));

            cout << "\nRecord Updated Successfully!";
            found = true;
            break;
        }
    }

    file.close();
    if (!found)
        cout << "\nCustomer not found!";
}

void Hotel::deleteCustomer(int r) {
    ifstream fin("hotel.dat", ios::in | ios::binary);
    ofstream fout("temp.dat", ios::out | ios::binary);
    bool found = false;

    while (fin.read((char*)this, sizeof(Hotel))) {
        if (room_no != r)
            fout.write((char*)this, sizeof(Hotel));
        else
            found = true;
    }

    fin.close();
    fout.close();

    remove("hotel.dat");
    rename("temp.dat", "hotel.dat");

    if (found)
        cout << "\nRecord Deleted Successfully!";
    else
        cout << "\nCustomer not found!";
}

void Hotel::checkOut(int r) {
    deleteCustomer(r);
    cout << "\nCustomer Checked Out!";
}

int main() {
    Hotel h;
    h.mainMenu();
    return 0;
}
