// # readme.txt
// Repository containing codes and details about our Project: Class Management Program

#include <iostream>
#include <fstream>
#include <cstring>

using namespace std;

const int maxStudents = 100;    // Max number of students in class
const int maxCourses = 3;   // Max number of courses for student
string adminUsername = "project";     // Admin Login Id
string adminPassword = "******";  // Admin password


// Structure for student details
struct Student {
    char name[50];
    char regNumber[15];
    char courses[maxCourses][50];
    int grades[maxCourses];
};

bool authenticateAdmin();   // Takes Input From User and compare it with the correct login id and password
void adminLogin();      // Confirms the admin login
void addStudent(Student students[], int &numStudents);      // Fucntion for adding student data
void displayStudents(const Student students[], int numStudents);    // Function to disply studen data
void editStudent(Student students[], int numStudents);       // Function to edit student data
void deleteStudent(Student students[], int &numStudents);    // Function to delete student data
void displayStudentDetails(const Student& student);     // Function to dislpay student data
void studentLogin(const Student students[], int numStudents);       // Takes student reg number as input and compare with the reg number already enrolled
void adminFunctionalities(Student students[], int &numStudents);    // Admin choice Menu and other functions called in here
void readStudentsFromFile(Student students[], int &numStudents);    // Load data from the file named "student.txt"

int main() {
    Student students[maxStudents];
    int numStudents = 0; // intial students are zero

    readStudentsFromFile(students, numStudents); // Load student data from file and numStudents = number of student in the file

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
                isAdmin = authenticateAdmin(); // Check for admin login
                if (isAdmin) {
                    adminFunctionalities(students, numStudents); // if Admin Login is correct, Display Admin Menu
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
    string username, password;  // admin login variables
    cout << "Enter admin username: ";
    cin >> username;
    cout << "Enter admin password: ";
    cin >> password;

    return (username == adminUsername && password == adminPassword); // compare admin login variables
}

void adminLogin() {
    bool loggedIn = false;  // Admin is not logged in
    do {
        loggedIn = authenticateAdmin();     // check login details with ids
        if (!loggedIn) {        // if details are incorrect display error msg
            cout << "Invalid credentials. Try again.\n";
        }
    } while (!loggedIn);    // Running loop until correct details are not provided
    cout << "Admin login successful.\n";    // if login detail is correct
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

                // Ignore commas up to 1000 characters before reading the next item
                // This is a conservative approach to handle potential long lines
                inFile.ignore(1000, ',');
            }

            ++numStudents;
            inFile.ignore(1000, '\n'); // Ignore end-of-line
        }

        inFile.close();
        cout << "Data loaded successfully from file.\n";
    } else {
        cout << "Unable to open the file for reading!\n";
    }
}
