// # readme.txt
// Repository containing codes and details about our Project: School Management Program

#include <iostream>
#include <fstream>
#include <cstring>
#include <limits>

using namespace std;

const int maxStudents = 100;
const int maxCourses = 3;

// Admin credentials (hardcoded for simplicity)
const string adminUsername = "admin";
const string adminPassword = "password";

struct Student {
    char name[50];
    char regNumber[15];
    char courses[maxCourses][50];
    int grades[maxCourses];
};

bool authenticateAdmin();
void adminLogin();
void addStudent(Student students[], int &numStudents);
void displayStudents(const Student students[], int numStudents);
void editStudent(Student students[], int numStudents);       
void deleteStudent(Student students[], int &numStudents);
void displayStudentDetails(const Student& student);
void studentLogin(const Student students[], int numStudents);
void adminFunctionalities(Student students[], int &numStudents);
void readStudentsFromFile(Student students[], int &numStudents);

int main() {
    Student students[maxStudents];
    int numStudents = 0; // Initially, there are no students

    readStudentsFromFile(students, numStudents); // Load student data from file

    bool isAdmin = false;

    int choice;
    do {
        cout << "\nSchool Management System Menu:\n";
        cout << "1. Login as Admin\n";
        cout << "2. Login as Student\n";
        cout << "0. Exit\n";
        cout << "Enter your choice: ";
        cin >> choice;

        switch (choice) {
            case 1:
                isAdmin = authenticateAdmin();
                if (isAdmin) {
                    adminFunctionalities(students, numStudents);
                } else {
                    cout << "Admin login failed.\n";
                }
                break;
            case 2:
                studentLogin(students, numStudents);
                break;
            case 0:
                cout << "Exiting the program. Goodbye!\n";
                break;
            default:
                cout << "Invalid choice. Please try again.\n";
        }
        
    } while (choice != 0);

    return 0;
}


bool authenticateAdmin() {
    string username, password;
    cout << "Enter admin username: ";
    cin >> username;
    cout << "Enter admin password: ";
    cin >> password;

    return (username == adminUsername && password == adminPassword);
}

void adminLogin() {
    bool loggedIn = false;
    do {
        loggedIn = authenticateAdmin();
        if (!loggedIn) {
            cout << "Invalid credentials. Try again.\n";
        }
    } while (!loggedIn);
    cout << "Admin login successful.\n";
}

void addStudent(Student students[], int &numStudents) {
    if (numStudents < maxStudents) {
        cin.ignore(); // Clear the input buffer
        cout << "Enter student name: ";
        cin.getline(students[numStudents].name, 50);
        cout << "Enter registration number: ";
        cin >> students[numStudents].regNumber;

        cin.ignore(); // Clear the input buffer
        for (int i = 0; i < maxCourses; ++i) {
            cout << "Enter course " << i + 1 << " name: ";
            cin.getline(students[numStudents].courses[i], 50);
            cout << "Enter grade for course " << students[numStudents].courses[i] << ": ";
            cin >> students[numStudents].grades[i];
            cin.ignore(); // Clear the input buffer
        }

        numStudents++;

        ofstream outFile("students.txt", ios::app);
        if (outFile.is_open()) {
            outFile << students[numStudents - 1].name << ","
                    << students[numStudents - 1].regNumber << ",";
            for (int i = 0; i < maxCourses; ++i) {
                outFile << students[numStudents - 1].courses[i] << ","
                        << students[numStudents - 1].grades[i] << ",";
            }
            outFile << "\n";
            outFile.close();
            cout << "Student added successfully!\n";
        } else {
            cout << "Unable to open the file for writing!\n";
        }
    } else {
        cout << "Maximum number of students reached!\n";
    }
}

void displayStudents(const Student students[], int numStudents) {
    cout << "\nList of Students:\n";
    for (int i = 0; i < numStudents; ++i) {
        cout << "Name: " << students[i].name << "\n";
        cout << "Registration Number: " << students[i].regNumber << "\n";
        for (int j = 0; j < maxCourses; ++j) {
            cout << "Course " << j + 1 << ": " << students[i].courses[j] << ", Grade: " << students[i].grades[j] << "\n";
        }
        cout << "-----------------\n";
    }
}

void editStudent(Student students[], int numStudents) {
    char regNum[15];
    cout << "Enter registration number to edit: ";
    cin >> regNum;

    for (int i = 0; i < numStudents; ++i) {
        if (strcmp(students[i].regNumber, regNum) == 0) {
            cin.ignore(); // Clear the input buffer

            for (int j = 0; j < maxCourses; ++j) {
                cout << "Enter new grade for course " << students[i].courses[j] << ": ";
                cin >> students[i].grades[j];
                cin.ignore(); // Clear the input buffer
            }

            ofstream outFile("students.txt");
            if (outFile.is_open()) {
                for (int k = 0; k < numStudents; ++k) {
                    outFile << students[k].name << ","
                            << students[k].regNumber << ",";
                    for (int l = 0; l < maxCourses; ++l) {
                        outFile << students[k].courses[l] << ","
                                << students[k].grades[l] << ",";
                    }
                    outFile << "\n";
                }
                outFile.close();
                cout << "Record updated successfully!\n";
                return;
            } else {
                cout << "Unable to open the file for writing!\n";
                return;
            }
        }
    }
    cout << "Student with registration number " << regNum << " not found!\n";
}

void deleteStudent(Student students[], int &numStudents) {
    char regNum[15];
    cout << "Enter registration number to delete: ";
    cin >> regNum;

    for (int i = 0; i < numStudents; ++i) {
        if (strcmp(students[i].regNumber, regNum) == 0) {
            for (int j = i; j < numStudents - 1; ++j) {
                students[j] = students[j + 1];
            }
            numStudents--;

            ofstream outFile("students.txt");
            if (outFile.is_open()) {
                for (int k = 0; k < numStudents; ++k) {
                    outFile << students[k].name << ","
                            << students[k].regNumber << ",";
                    for (int l = 0; l < maxCourses; ++l) {
                        outFile << students[k].courses[l] << ","
                                << students[k].grades[l] << ",";
                    }
                    outFile << "\n";
                }
                outFile.close();
                cout << "Record deleted successfully!\n";
                return;
            } else {
                cout << "Unable to open the file for writing!\n";
                return;
            }
        }
    }
    cout << "Student with registration number " << regNum << " not found!\n";
}

void displayStudentDetails(const Student& student) {
    cout << "Name: " << student.name << "\n";
    cout << "Registration Number: " << student.regNumber << "\n";
    for (int j = 0; j < maxCourses; ++j) {
        cout << "Course " << j + 1 << ": " << student.courses[j] << ", Grade: " << student.grades[j] << "\n";
    }
}

void studentLogin(const Student students[], int numStudents) {
    char regNum[15];
    cout << "Enter your registration number: ";
    cin >> regNum;

    for (int i = 0; i < numStudents; ++i) {
        if (strcmp(students[i].regNumber, regNum) == 0) {
            displayStudentDetails(students[i]); // Function to display student details
            return;
        }
    }
    cout << "Student with registration number " << regNum << " not found!\n";
}

void adminFunctionalities(Student students[], int &numStudents) {
    int adminChoice;
    do {
        cout << "\nAdmin Functionalities Menu:\n";
        cout << "1. Add Student\n";
        cout << "2. Display Students\n";
        cout << "3. Edit Student Record\n";
        cout << "4. Delete Student\n";
        cout << "0. Back to Main Menu\n";
        cout << "Enter your choice: ";
        cin >> adminChoice;

        switch (adminChoice) {
            case 1:
                addStudent(students, numStudents);
                break;
            case 2:
                displayStudents(students, numStudents);
                break;
            case 3:
                editStudent(students, numStudents);
                break;
            case 4:
                deleteStudent(students, numStudents);
                break;
            case 0:
                cout << "Returning to the main menu.\n";
                break;
            default:
                cout << "Invalid choice. Please try again.\n";
        }
    } while (adminChoice != 0);
}

void readStudentsFromFile(Student students[], int &numStudents) {
    ifstream inFile("students.txt");
    if (inFile.is_open()) {
        while (numStudents < maxStudents &&
               inFile.getline(students[numStudents].name, 50, ',') &&
               inFile.getline(students[numStudents].regNumber, 15, ',')) {

            for (int i = 0; i < maxCourses; ++i) {
                inFile.getline(students[numStudents].courses[i], 50, ',');
                inFile >> students[numStudents].grades[i];

                if (i != maxCourses - 1) {
                    inFile.ignore(numeric_limits<streamsize>::max(), ',');
                }
            }

            ++numStudents;
            inFile.ignore(numeric_limits<streamsize>::max(), '\n'); // Ignore end-of-line
        }

        inFile.close();
        cout << "Data loaded successfully from file.\n";
    } else {
        cout << "Unable to open the file for reading!\n";
    }
}
