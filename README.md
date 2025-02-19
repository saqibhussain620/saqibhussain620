#include <iostream>
#include <cstring>
#include <iomanip>
#include <ctime> // For Due Date

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
    int available;
    int borrowerId;
    time_t dueDate;
};

User users[100];
int userCount = 0;

Book books[100];
int bookCount = 0;

const int MAX_BORROW_DAYS = 7;
const float FINE_PER_DAY = 5.0;

void initializeBooks() {
    const char* titles[] = {"Calculus", "Data Structures", "C++ Programming", "Physics", "ICT", "Quran", "Machine Learning"};
    const char* authors[] = {"James Stewart", "Robert Lafore", "Bjarne Stroustrup", "Resnick & Halliday", "ICT Team", "Allah", "Andrew Ng"};
    
    for (int i = 0; i < 7; i++) {
        strcpy(books[i].title, titles[i]);
        strcpy(books[i].author, authors[i]);
        books[i].id = i + 1;
        books[i].available = 1;
        books[i].borrowerId = 0;
        books[i].dueDate = 0;
    }
    bookCount = 7;
}

void registerUser() {
    if (userCount >= 100) {
        cout << "User limit reached.\n";
        return;
    }
    cout << "\nEnter user name: ";
    cin.ignore();
    cin.getline(users[userCount].name, 50);
    users[userCount].id = userCount + 1;
    users[userCount].dues = 0.0f;
    cout << "? User registered successfully! User ID: " << users[userCount].id << "\n";
    userCount++;
}

void viewBooks() {
    cout << "\n?? Available Books in Library:\n";
    cout << left << setw(8) << "ID" << setw(30) << "Title" << setw(20) << "Author" << setw(15) << "Status\n";
    cout << "----------------------------------------------------------\n";
    for (int i = 0; i < bookCount; i++) {
        cout << left << setw(8) << books[i].id
             << setw(30) << books[i].title
             << setw(20) << books[i].author
             << setw(15) << (books[i].available ? "Available" : "Checked out") << "\n";
    }
}

void checkOutBook() {
    int bid, uid;
    cout << "\nEnter Book ID to checkout: ";
    cin >> bid;
    cout << "Enter User ID: ";
    cin >> uid;

    if (bid < 1 || bid > bookCount || uid < 1 || uid > userCount) {
        cout << "Invalid Book ID or User ID.\n";
        return;
    }
    if (books[bid - 1].available == 0) {
        cout << "Sorry, the book is already checked out.\n";
        return;
    }
    
    books[bid - 1].available = 0;
    books[bid - 1].borrowerId = uid;
    books[bid - 1].dueDate = time(0) + (MAX_BORROW_DAYS * 86400);

    cout << "? Book '" << books[bid - 1].title << "' has been checked out to User ID " << uid << ".\n";
    cout << "?? Due Date: " << ctime(&books[bid - 1].dueDate);
}

void checkInBook() {
    int bid;
    cout << "\nEnter Book ID to check-in: ";
    cin >> bid;

    if (bid < 1 || bid > bookCount) {
        cout << "Invalid Book ID.\n";
        return;
    }
    if (books[bid - 1].available == 1) {
        cout << "This book is already in the library.\n";
        return;
    }

    time_t now = time(0);
    if (now > books[bid - 1].dueDate) {
        int daysLate = (now - books[bid - 1].dueDate) / 86400;
        float fine = daysLate * FINE_PER_DAY;
        int userId = books[bid - 1].borrowerId;
        users[userId - 1].dues += fine;
        cout << "? You returned the book " << daysLate << " days late! Fine: $" << fine << "\n";
    }

    books[bid - 1].available = 1;
    books[bid - 1].borrowerId = 0;
    books[bid - 1].dueDate = 0;
    cout << "? Book '" << books[bid - 1].title << "' has been checked in.\n";
}

void viewDues() {
    cout << "\n?? User Dues Report:\n";
    cout << left << setw(10) << "User ID" << setw(25) << "Name" << setw(10) << "Dues ($)\n";
    cout << "------------------------------------------\n";
    for (int i = 0; i < userCount; i++) {
        cout << left << setw(10) << users[i].id
             << setw(25) << users[i].name
             << setw(10) << fixed << setprecision(2) << users[i].dues << "\n";
    }
}

// ?? *????? ?????* (Library ?? ???? ???? ?????? ??)
void generateReport() {
    cout << "\n?? Library Report\n";
    cout << "------------------------------\n";
    cout << "?? Total Users: " << userCount << "\n";
    cout << "?? Total Books: " << bookCount << "\n\n";

    cout << "?? Book Information:\n";
    cout << left << setw(8) << "ID" << setw(30) << "Title" << setw(20) << "Author" << setw(15) << "Status\n";
    cout << "----------------------------------------------------------\n";
    for (int i = 0; i < bookCount; i++) {
        cout << left << setw(8) << books[i].id
             << setw(30) << books[i].title
             << setw(20) << books[i].author
             << setw(15) << (books[i].available ? "Available" : "Checked out") << "\n";
    }

    cout << "\n?? User Information:\n";
    viewDues();
}

int main() {
    initializeBooks();
    int choice;
    do {
        cout << "\n========= Library Management System =========\n";
        cout << "1. Register New User\n";
        cout << "2. View Books\n";
        cout << "3. Check-out Book\n";
        cout << "4. Check-in Book\n";
        cout << "5. View User Dues\n";
        cout << "6. Generate Report\n";
        cout << "7. Exit\n";
        cout << "Enter your choice: ";
        cin >> choice;

        switch(choice) {
            case 1: registerUser(); break;
            case 2: viewBooks(); break;
            case 3: checkOutBook(); break;
            case 4: checkInBook(); break;
            case 5: viewDues(); break;
            case 6: generateReport(); break;
            case 7: cout << "Exiting the system...\n"; break;
            default: cout << "Invalid choice. Please try again.\n";
        }
    } while(choice != 7);

return 0;
}
