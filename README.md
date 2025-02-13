
#include <iostream>
#include <cstring>
using namespace std;


struct User {
    int id;
    char name[50];
    float dues;
};

struct Book {
    int id;
    char title[100];
    char author[50];
    int available;    // 1 if available, 0 if checked out
    int borrowerId;   // stores the user id of the borrower when checked out
};


User users[100];
int userCount = 0;

Book books[100];
int bookCount = 0;

// Function to register a new user
void registerUser() {
    if(userCount >= 100) {
        cout << "User limit reached.\n";
        return;
    }
    cout << "\nEnter user name: ";
    // Clear input buffer before reading a string
    cin.ignore();
    cin.getline(users[userCount].name, 50);
    users[userCount].id = userCount + 1;  // Auto-generated user ID
    users[userCount].dues = 0.0f;           // Initially, dues are zero
    cout << "User registered successfully. User ID: " << users[userCount].id << "\n";
    userCount++;
}

// Function to add a new book to the library
void addBook() {
    if(bookCount >= 100) {
        cout << "Book limit reached.\n";
        return;
    }
    cout << "\nEnter book title: ";
    cin.ignore();
    cin.getline(books[bookCount].title, 100);
    cout << "Enter book author: ";
    cin.getline(books[bookCount].author, 50);
    books[bookCount].id = bookCount + 1;  // Auto-generated book ID
    books[bookCount].available = 1;       // Book is initially available
    books[bookCount].borrowerId = 0;        // No borrower yet
    cout << "Book added successfully. Book ID: " << books[bookCount].id << "\n";
    bookCount++;
}

// Function to handle book checkout
void checkOutBook() {
    int bid, uid;
    cout << "\nEnter Book ID to checkout: ";
    cin >> bid;
    cout << "Enter User ID: ";
    cin >> uid;
    
    // Validate book and user IDs
    if(bid < 1 || bid > bookCount) {
        cout << "Invalid Book ID.\n";
        return;
    }
    if(uid < 1 || uid > userCount) {
        cout << "Invalid User ID.\n";
        return;
    }
    
    // Check if the book is available
    if(books[bid - 1].available == 0) {
        cout << "Sorry, the book is already checked out.\n";
        return;
    }
    books[bid - 1].available = 0;
    books[bid - 1].borrowerId = uid;
    cout << "Book ID " << bid << " has been checked out to User ID " << uid << ".\n";
}

// Function to handle book check-in
void checkInBook() {
    int bid;
    cout << "\nEnter Book ID to check-in: ";
    cin >> bid;
    if(bid < 1 || bid > bookCount) {
        cout << "Invalid Book ID.\n";
        return;
    }
    if(books[bid - 1].available == 1) {
        cout << "This book is already in the library.\n";
        return;
    }
    books[bid - 1].available = 1;
    books[bid - 1].borrowerId = 0;
    cout << "Book ID " << bid << " has been checked in.\n";
}

// Function to generate a report of library resources and user information
void generateReport() {
    cout << "\n---------------- Library Report ----------------\n";
    cout << "Total Users: " << userCount << "\n";
    cout << "Total Books: " << bookCount << "\n\n";
    
    cout << "Books Information:\n";
    for(int i = 0; i < bookCount; i++) {
        cout << "Book ID: " << books[i].id 
             << " | Title: " << books[i].title 
             << " | Author: " << books[i].author 
             << " | Status: " << (books[i].available ? "Available" : "Checked out");
        if(books[i].available == 0) {
            cout << " (Borrowed by User ID: " << books[i].borrowerId << ")";
        }
        cout << "\n";
    }
    
    cout << "\nUser Information:\n";
    for(int i = 0; i < userCount; i++) {
        cout << "User ID: " << users[i].id 
             << " | Name: " << users[i].name 
             << " | Dues: $" << users[i].dues << "\n";
    }
    cout << "------------------------------------------------\n";
}

// Main menu to interact with the library management system
int main() {
    int choice;
    do {
        cout << "\n========= Library Management System =========\n";
        cout << "1. Register New User\n";
        cout << "2. Add New Book\n";
        cout << "3. Check-out Book\n";
        cout << "4. Check-in Book\n";
        cout << "5. Generate Report\n";
        cout << "6. Exit\n";
        cout << "Enter your choice: ";
        cin >> choice;
        
        switch(choice) {
            case 1:
                registerUser();
                break;
            case 2:
                addBook();
                break;
            case 3:
                checkOutBook();
                break;
            case 4:
                checkInBook();
                break;
            case 5:
                generateReport();
                break;
            case 6:
                cout << "Exiting the system...\n";
                break;
            default:
                cout << "Invalid choice. Please try again.\n";
        }
    } while(choice != 6);
    
    return 0;
}
