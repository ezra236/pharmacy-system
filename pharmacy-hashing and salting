#include <iostream>
#include <fstream>
#include <string>
#include <sstream>
#include <vector>
#include <cstring> // for string hashing
#include <conio.h> // for getch
#include <random>  // for generating random salt
#include <ctime>   // for seeding random number generator

using namespace std;

// Function declaration
string getHiddenPassword();

// Hashing function for password hashing with salt
string hashPassword(const string& password, const string& salt) {
    // Combine password and salt
    string saltedPassword = password + salt;

    // For simplicity, let's assume a basic hashing function (not secure)
    // Here, we'll use the C++ standard hash function
    hash<string> hashFunction;
    size_t hashValue = hashFunction(saltedPassword);
    return to_string(hashValue);
}

string generateSalt() {
    // Generate salt
    const string charset = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz";
    const int saltLength = 16; // Adjust the length of the salt as needed
    string salt;
    salt.reserve(saltLength);
    srand(time(nullptr)); // Seed the random number generator

    for (int i = 0; i < saltLength; ++i) {
        salt += charset[rand() % charset.length()];
    }
    return salt;
}

template <typename T>
string to_string_custom(const T& value) {
    stringstream ss;
    ss << value;
    return ss.str();
}

class Pharmacy {
private:
    double total = 0.0;
    string productDetails = "";
    vector<int> productQuantities;
    string adminPasswordHash; // Store hashed password instead of plain text
    string adminSalt;

public:
    Pharmacy() : productQuantities(8, 10) {
        // Initialize the admin password hash and salt
        adminSalt = generateSalt();
        adminPasswordHash = hashPassword("123", adminSalt); // Hash the default password with salt
    }

    // Function to verify the password
    bool verifyPassword(const string& password) {
        // Hash the provided password with the stored salt and compare it with the stored hash
        return hashPassword(password, adminSalt) == adminPasswordHash;
    }

    // Function to set a new password
    void setPassword(const string& newPassword) {
        // Generate a new salt and hash the new password with the new salt
        adminSalt = generateSalt();
        adminPasswordHash = hashPassword(newPassword, adminSalt);
    }

    void processOrder(const string& productName, double productPrice, int quantity) {
        int productIndex = getProductIndex(productName);
        if (productIndex >= 0) {
            if (!productQuantities.empty() && productQuantities[productIndex] >= quantity) {
                total += productPrice * quantity;
                productDetails += productName + "\t\t$" + to_string_custom(productPrice) + "\tQuantity: " + to_string_custom(quantity) + "\n";
                productQuantities[productIndex] -= quantity;
                cout << "\n\tAdding " << quantity << " packet(s) of " << productName << " to the order. Price: $" << productPrice * quantity << endl<<endl;
            } else {
                cout << "Not enough stock for " << productName << ". Available quantity: " << productQuantities[productIndex] << endl;
            }
        } else {
            cout << "Invalid product name. Please try again." << endl;
        }
    }

    void addProductQuantity(const string& productName, int quantity, const string& password) {
        if (verifyPassword(password)) {
            int productIndex = getProductIndex(productName);
            if (productIndex >= 0) {
                productQuantities[productIndex] += quantity;
                cout << "\n\tAdded " << quantity << " packets of " << productName << " to the stock." << endl;
                cout << "\n\tNEW QUANTITY OF " << productName << ": " << productQuantities[productIndex] << " packets" << endl;
            } else {
                cout << "Invalid product name. Please try again." << endl;
            }
        } else {
            cout << "Incorrect password. Only the administrator can add product quantity." << endl;
        }
    }

    void printReceipt(const string& fname, const string& sname) {
        ofstream file("receipt.txt");
        if (file.is_open()) {
            file << "\t------ Receipt ------" << endl;
            file << fname << "\t" << sname << endl;
            file << "\Product\t\tPrice\tQuantity" << endl;
            file << productDetails;
            file << "-----------------------" << endl;
            file << "\Total:\t\t$" << total << endl;
            file.close();

            cout << "\tReceipt has been stored in receipt.txt" << endl;
        } else {
            cout << "Unable to create the receipt file." << endl;
        }
    }

    void printtheReceipt(const string& fname, const string& sname) {
        cout << "\n------------------- RECEIPT ------------------" << endl;
        cout << "Customer Name: " << fname << "\t" << sname << endl;
        cout << "Product\t\tPrice\tQuantity" << endl;
        cout << productDetails;
        cout << "------------------------------------------------" << endl;
        cout << "\Total:\t\t$" << total << endl;
    }

    void addNewProduct(const string& productName, double productPrice, const string& password) {
        if (verifyPassword(password)) {
            int productIndex = getProductIndex(productName);
            if (productIndex == -1) {
                productDetails += productName + "\t\t$" + to_string_custom(productPrice) + "\n";
                productQuantities.push_back(10);
                cout << "\n\tAdded new product: " << productName << " Price: $" << productPrice << endl;
            } else {
                cout << "Product with the same name already exists. Use 'addProductQuantity' to update quantity." << endl;
            }
        } else {
            cout << "Incorrect password. Only the administrator can add a new product." << endl;
        }
    }

private:
    int getProductIndex(const string& productName) {
        if (productName == "ventrolin") {
            return 0;
        } else if (productName == "lucozade") {
            return 1;
        } else if (productName == "p-Alaxin") {
            return 2;
        } else if (productName == "paracetamol") {
            return 3;
        } else if (productName == "penicilin") {
            return 4;
        } else if (productName == "aspirin") {
            return 5;
        } else if (productName == "mara moja") {
            return 6;
        } else if (productName == "panadol") {
            return 7;
        } else {
            return -1;
        }
    }
};

int main() {
    Pharmacy pharmacy;
    string role, password;
    string productName;
    string fname, sname;

    cout << "-------------------------------------WELCOME TO THE PHARMACY---------------------------------------------" << endl;
    cout << "\t--------------------------------------------------------------------------------\n";
    cout << "\n\t\tENTER AS:\n\n";
    cout << "1. 'admin' for administrator:\n2. 'customer' for customer:\n3. 'view' to see available products: ";
    cin >> role;

    if (role == "admin") {
        cout << "\nEnter the password ****: ";
        password = getHiddenPassword();
        // Verify the password using the class method
        if (pharmacy.verifyPassword(password)) {
            string choice;
            cout << "\nAdministrator access granted.\n" << endl;
            cout << "\tADD a new product('new'):\n\tADD product Quantity('quantity'):\n\tCHANGE password('password'): \t";
            cin >> choice;

            if (choice == "new") {
                cout << "\n\t-Enter the name of the new product: ";
                cin >> productName;
                cout << "\t-Enter the price of the new product: ";
                double productPrice;
                cin >> productPrice;

                pharmacy.addNewProduct(productName, productPrice, password);
            } else if (choice == "quantity") {
                cout << "\n\t-Enter the name of the product to add quantity (or 'done' to finish): ";
                cin >> productName;

                while (productName != "done") {
                    int quantity;
                    cout << "\t-Enter the quantity to add for " << productName << ": ";
                    cin >> quantity;

                    pharmacy.addProductQuantity(productName, quantity, password);

                    cout << "\t-Enter the name of the product to add quantity (or 'done' to finish): ";
                    cin >> productName;
                }
            } else if (choice == "password") {
            // Change password functionality
            string newPassword;
            cout << "\t-Enter the new password: ";
            newPassword = getHiddenPassword();
            pharmacy.setPassword(newPassword);
            cout << "\n\tPassword changed successfully!" << endl;
        }
        } else {
            cout << "\n\tIncorrect password. Exiting." << endl;
            return 0;
        }
    } else if (role == "customer") {
        cout << "\n\tNAME: ";
        cin >> fname;
        cout << "\tTHE OTHER NAME: ";
        cin >> sname;
        cout << "\n\t-Enter the name of the product (or 'done' to finish): ";
        cin >> productName;

        while (productName != "done") {
            double productPrice = 0;
            int quantity = 1; // Default quantity is 1

            cout << "\t-Enter the quantity of " << productName << " you need: ";
            cin >> quantity;

            if (quantity < 1) {
                cout << "Invalid quantity. Please enter a positive number." << endl;
            } else {
                if (productName == "ventrolin") {
                    productPrice = 2.0;
                } else if (productName == "lucozade") {
                    productPrice = 3.0;
                } else if (productName == "p-Alaxin") {
                    productPrice = 1.5;
                } else if (productName == "paracetamol") {
                    productPrice = 1.0;
                } else if (productName == "penicilin") {
                    productPrice = 1.0;
                } else if (productName == "aspirin") {
                    productPrice = 1.0;
                } else if (productName == "maramoja") {
                    productPrice = 1.0;
                } else if (productName == "panadol") {
                    productPrice = 1.0;
                } else {
                    cout << "Invalid product name. Please try again." << endl;
                }

                if (productPrice > 0) {
                    pharmacy.processOrder(productName, productPrice, quantity);
                }
            }

            cout << "\t-Enter the name of the product (or 'done' to finish): ";
            cin >> productName;
        }

        pharmacy.printReceipt(fname, sname);
        pharmacy.printtheReceipt(fname, sname);
    } else if (role == "view") {
        cout << "\tAvailable products: " << endl;
        cout << "\tventrolin\t\t$2.0" << endl;
        cout << "\tlucozade\t\t$3.0" << endl;
        cout << "\tp-Alaxin\t\t$1.5" << endl;
        cout << "\tparacetamol\t\t$1.0" << endl;
        cout << "\tpenicilin\t\t$1.0" << endl;
        cout << "\taspirin\t\t\t$1.0" << endl;
        cout << "\tmara moja\t\t$1.0" << endl;
        cout << "\tpanadol\t\t\t$1.0" << endl;
    } else {
        cout << "Invalid role. Exiting." << endl;
    }

    return 0;
}  

string getHiddenPassword() {
    string password;
    char ch;
    while ((ch = _getch()) != '\r') { // Read until Enter is pressed
        if (ch == '\b') { // Handle backspace
            if (!password.empty()) {
                cout << "\b \b"; // Move cursor back, erase character, move cursor back again
                password.pop_back(); // Remove last character from the password
            }
        } else {
            cout << '*'; // Print * instead of the actual character
            password += ch; // Add character to the password
        }
    }
    cout << endl; // New line after password entry
    return password;
}
