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
    char name[50];  //char array max upto 50 char
    char regNumber[15];
    char courses[maxCourses][50];
    int grades[maxCourses];
};

bool authenticateAdmin();   // Takes Input From User and compare it with the correct login id and password
void studentLogin(Student students[], int numStudents);       // Takes student reg number as input and compare with the reg number already enrolled
void addStudent(Student students[], int &numStudents);      // Fucntion for adding student data
void displayStudents(Student students[], int numStudents);    // Function to disply studen data
void editStudent(Student students[], int numStudents);       // Function to edit student data
void deleteStudent(Student students[], int &numStudents);    // Function to delete student data
void displayStudentDetails(Student& student);     // Function to dislpay student data
void adminFunctionalities(Student students[], int &numStudents);    // Admin choice Menu and other functions called in here
void readStudentsFromFile(Student students[], int &numStudents);    // Load data from the file named "student.txt"

int main() {
    Student students[maxStudents];  // student structure datatype with variable name student array and size of maxstudent
                                    // fix memory of 100 student for the array masstudnets
    int numStudents = 0; // intial students are zero

    readStudentsFromFile(students, numStudents); // Load student data from file and numStudents = number of student in the file

    bool isAdmin = false;   // initailly no log in as admin

    int choice;
    do {
        cout << "\nSchool Management System Menu:\n";   // initiall menu
        cout << "1. Login as Admin\n";
        cout << "2. Login as Student\n";
        cout << "0. Exit\n";
        cout << "Enter your choice: ";
        cin >> choice;

        switch (choice) {
            case 1:
                isAdmin = authenticateAdmin(); // Check for admin login
                if (isAdmin) {  // if true
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

void addStudent(Student students[], int &numStudents) {     // array of student structure   // numStudent = total students
                                                            // int &numStudent by reference to make actual changes
    if (numStudents < maxStudents) {    // if students is less than 100
        cin.ignore(); // Clear the input buffer
        cout << "Enter student name: ";
        cin.getline(students[numStudents].name, 50);    // getline to ignore spaces and max 50 character and save it to name
                                                        // save students to the index at numStudent 
        cout << "Enter registration number: ";
        cin >> students[numStudents].regNumber;         // save reg no to regNumber at index numStudent

        cin.ignore(); // Clear the input buffer
        for (int i = 0; i < maxCourses; ++i) {          // if courses less than 3
            cout << "Enter course " << i + 1 << " name: ";
            cin.getline(students[numStudents].courses[i], 50);      // save courses to courses [i] at index i
            cout << "Enter grade for course " << students[numStudents].courses[i] << ": ";
            cin >> students[numStudents].grades[i];     //save grades at grades[i]
            cin.ignore(); // Clear the input buffer
        }

        numStudents++;  // student added so increment student by one

        ofstream outFile("students.txt", ios::app);     // ofstream class to write to files // save student to student.txt
        if (outFile.is_open()) {                        // open file
            outFile << students[numStudents - 1].name << ","    //numStudent - 1 to get the data at that index (array index start at zero)
                    << students[numStudents - 1].regNumber << ",";
            for (int i = 0; i < maxCourses; ++i) {
                outFile << students[numStudents - 1].courses[i] << ","
                        << students[numStudents - 1].grades[i] << ",";
            }
            outFile << "\n";    // enter a newline to add next students
            outFile.close();
            cout << "Student added successfully!\n";
        } else {
            cout << "Error Opening File!\n";
        }
    } else {
        cout << "Maximum number of students reached!\n";
    }
}

void displayStudents(Student students[], int numStudents) {     //Struct Student array student, student num and index
    cout << "\nList of Students:\n";
    for (int i = 0; i < numStudents; ++i) {     // for loop iterates through students and print for i lrss than numStudent
        cout << "Name: " << students[i].name << "\n";
        cout << "Registration Number: " << students[i].regNumber << "\n";
        for (int j = 0; j < maxCourses; ++j) {
            cout << "Course " << j + 1 << ": " << students[i].courses[j] << ", Grade: " << students[i].grades[j] << "\n";
        }
        cout << "-----------------\n";
    }
}

void editStudent(Student students[], int numStudents) {
    char regNum[15];    // Max 15
    cout << "Enter registration number to edit: ";
    cin >> regNum;

    for (int i = 0; i < numStudents; ++i) {     // for loop to iterate through all the students reg
        if (strcmp(students[i].regNumber, regNum) == 0) {   // compare the reg number proivded through the loop, if match is found
            cin.ignore(); // Clear the input buffer         // open that line for editing

            for (int j = 0; j < maxCourses; ++j) {
                cout << "Enter new grade for course " << students[i].courses[j] << ": ";
                cin >> students[i].grades[j];
                cin.ignore(); // Clear the input buffer
            }

            ofstream outFile("students.txt");       // write the changes to the file
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
                cout << "Error Opening File!\n";
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
        if (strcmp(students[i].regNumber, regNum) == 0) {       // strcmp compare reg number and delete the student afterwards
            for (int j = i; j < numStudents - 1; ++j) {         // 0 means a matching is found and if reg == 0
                students[j] = students[j + 1];                  // i is index of found student
            // j = i and it write the data of the current student by the next student j + 1
            }                                                   
            numStudents--;      // student removed so decrement numstudent by 1

            ofstream outFile("students.txt");   //make an object of the ofstream class named outFile to write data to the file named "students.txt".
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

void displayStudentDetails(Student& student) {  // pass value by reference to display the actual changes
    cout << "Name: " << student.name << "\n";
    cout << "Registration Number: " << student.regNumber << "\n";
    for (int j = 0; j < maxCourses; ++j) {
        cout << "Course " << j + 1 << ": " << student.courses[j] << ", Grade: " << student.grades[j] << "\n";
    }
}

void studentLogin(Student students[], int numStudents) {
    char regNum[15];
    cout << "Enter your registration number: ";
    cin >> regNum;

    for (int i = 0; i < numStudents; ++i) {
        if (strcmp(students[i].regNumber, regNum) == 0) {   //compare the regnumber with all students
            displayStudentDetails(students[i]); // Function to display student details
            return;
        }
    }
    cout << "Student with registration number " << regNum << " not found!\n";
}

void adminFunctionalities(Student students[], int &numStudents) {      // Struct student, numStudent for student count(total number of student and students at index)
    int adminChoice;
    do {
        cout << "\nAdmin Menu:\n";
        cout << "1. Add Student\n";
        cout << "2. Display Students\n";
        cout << "3. Edit Student Record\n";
        cout << "4. Delete Student\n";
        cout << "0. Back to Main Menu\n";
        cout << "Enter your choice: ";
        cin >> adminChoice;

        switch (adminChoice) {      // Access  different functions
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

void readStudentsFromFile(Student students[], int &numStudents) {   //array of student, reference to integer variable
    ifstream inFile("students.txt");    // Input filestream with object named inFile
    if (inFile.is_open()) {
        while (numStudents < maxStudents && 
        inFile.getline(students[numStudents].name, 50, ',') && // fetch data from student file /name upto 50 char or comma
        inFile.getline(students[numStudents].regNumber, 15, ',')) {     // fetch reg no

            for (int i = 0; i < maxCourses; ++i) {
                inFile.getline(students[numStudents].courses[i], 50, ',');      // fetch student courses
                inFile >> students[numStudents].grades[i];      // fetch student grades
                inFile.ignore(1000, ',');   // ignore the line until the comma or 1000 char are reached
            }

            ++numStudents;  // increment numstudent to read from next line
            inFile.ignore(1000, '\n'); // Ignore until 1000 char or new line
        }

        inFile.close();
        cout << "Data loaded successfully from file.\n";
    } else {
        cout << "Error Opening The File!\n";
    }
}
