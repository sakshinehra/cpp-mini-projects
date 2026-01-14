# cpp-mini-projects
Student Record Management System in C++

#include <iostream>
#include <fstream>
#include <cstring>
using namespace std;

class Student {
public:
    int roll;
    char name[50];
    float marks;

    void input() {
        cout << "Enter Roll Number: ";
        cin >> roll;
        cin.ignore();
        cout << "Enter Name: ";
        cin.getline(name, 50);
        cout << "Enter Marks: ";
        cin >> marks;
    }

    void display() const {
        cout << "\n----------------------------";
        cout << "\nRoll No : " << roll;
        cout << "\nName    : " << name;
        cout << "\nMarks  : " << marks;
        cout << "\n----------------------------";
    }
};

void addStudent() {
    Student s;
    ofstream fout("students.dat", ios::binary | ios::app);
    if (!fout) {
        cout << "Error opening file!\n";
        return;
    }
    s.input();
    fout.write((char*)&s, sizeof(s));
    fout.close();
    cout << "\n Student record added successfully!\n";
}

void displayAll() {
    Student s;
    ifstream fin("students.dat", ios::binary);
    if (!fin) {
        cout << "\n No records found!\n";
        return;
    }
    cout << "\n All Student Records:\n";
    while (fin.read((char*)&s, sizeof(s))) {
        s.display();
    }
    fin.close();
}

void searchStudent() {
    int r;
    bool found = false;
    Student s;
    ifstream fin("students.dat", ios::binary);

    if (!fin) {
        cout << "\n No records found!\n";
        return;
    }

    cout << "Enter roll number to search: ";
    cin >> r;

    while (fin.read((char*)&s, sizeof(s))) {
        if (s.roll == r) {
            s.display();
            found = true;
            break;
        }
    }
    fin.close();

    if (!found)
        cout << "\n Record not found!\n";
}

void updateStudent() {
    int r;
    bool found = false;
    Student s;

    fstream file("students.dat", ios::binary | ios::in | ios::out);

    if (!file) {
        cout << "\n No records found!\n";
        return;
    }

    cout << "Enter roll number to update: ";
    cin >> r;

    while (file.read((char*)&s, sizeof(s))) {
        if (s.roll == r) {
            cout << "\nExisting Record:";
            s.display();

            cout << "\n\nEnter New Details:\n";
            Student newS;
            newS.input();

            file.seekp(-sizeof(s), ios::cur);
            file.write((char*)&newS, sizeof(newS));

            cout << "\n Record updated successfully!\n";
            found = true;
            break;
        }
    }
    file.close();

    if (!found)
        cout << "\n Record not found!\n";
}

void deleteStudent() {
    int r;
    cout << "Enter roll number to delete: ";
    cin >> r;

    Student s;
    ifstream fin("students.dat", ios::binary);
    ofstream fout("temp.dat", ios::binary);

    if (!fin) {
        cout << "\n No records found!\n";
        return;
    }

    bool found = false;

    while (fin.read((char*)&s, sizeof(s))) {
        if (s.roll != r) {
            fout.write((char*)&s, sizeof(s));
        } else {
            found = true;
        }
    }

    fin.close();
    fout.close();

    remove("students.dat");
    rename("temp.dat", "students.dat");

    if (found)
        cout << "\n Record deleted successfully!\n";
    else
        cout << "\n Record not found!\n";
}

int main() {
    int choice;

    do {
        cout << "\n==============================";
        cout << "\n STUDENT RECORD MANAGEMENT ";
        cout << "\n==============================";
        cout << "\n1. Add Student Record";
        cout << "\n2. Display All Records";
        cout << "\n3. Search Student";
        cout << "\n4. Update Student Record";
        cout << "\n5. Delete Student Record";
        cout << "\n6. Exit";
        cout << "\nEnter your choice: ";
        cin >> choice;

        switch (choice) {
        case 1:
            addStudent();
            break;
        case 2:
            displayAll();
            break;
        case 3:
            searchStudent();
            break;
        case 4:
            updateStudent();
            break;
        case 5:
            deleteStudent();
            break;
        case 6:
            cout << "\nExiting program...\n";
            break;
        default:
            cout << "\n Invalid choice!\n";
        }

    } while (choice != 6);

    return 0;
}

