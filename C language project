/*
=============================================================================
   PROJECT: Inventory Management System using Data Structures in C
   AUTHOR : B.Tech 1st Year Student Project
   DATE   : 2024
   COURSE : Data Structures and Programming in C

   DESCRIPTION:
   This project implements a full-featured Inventory Management System
   that demonstrates the use of:
   - Singly Linked List (for product storage)
   - Stack (for Undo Delete feature)
   - Queue (for Customer Billing Queue)
   - File Handling (Save & Load inventory)
   - Arrays and Strings (product info)
   - Dynamic Memory Allocation (malloc)
   - Structures, Functions, Switch-Case, Menus
=============================================================================
*/

/* =========================================================
   SECTION 1: HEADER FILES
   - Standard libraries used in this project
   ========================================================= */
#include <stdio.h>      // printf, scanf, fprintf, fscanf, fopen, fclose
#include <stdlib.h>     // malloc, free, exit
#include <string.h>     // strcpy, strcmp, strlen, strstr
#include <ctype.h>      // tolower (for case-insensitive search)

/* =========================================================
   SECTION 2: MACROS AND CONSTANTS
   - Fixed values used throughout the program
   ========================================================= */
#define MAX_NAME     50       // Maximum length of product name
#define MAX_CATEGORY 30       // Maximum length of category name
#define LOW_STOCK    5        // Threshold quantity for low stock alert
#define STACK_SIZE   50       // Maximum items the undo stack can hold
#define FILE_NAME    "inventory.txt"  // File to save/load data
#define ADMIN_PASS   "admin123"       // Admin password for login

/* =========================================================
   SECTION 3: STRUCTURES
   - Blueprint for product, stack node, and queue node
   ========================================================= */

/* Product Structure - stores all details of one product */
struct Product {
    int   id;                    // Unique Product ID
    char  name[MAX_NAME];        // Product Name
    int   quantity;              // Stock Quantity
    float price;                 // Price per unit
    char  category[MAX_CATEGORY];// Category (e.g., Electronics, Food)
};

/* Linked List Node - each node holds one product + pointer to next */
struct Node {
    struct Product data;         // Product stored in this node
    struct Node   *next;         // Pointer to next node in list
};

/* Stack Node - used for Undo Delete feature */
struct StackNode {
    struct Product data;         // Product that was deleted
    struct StackNode *next;      // Pointer to next stack node
};

/* Queue Node - used for Customer Billing Queue */
struct QueueNode {
    char customerName[MAX_NAME]; // Name of the customer
    int  productId;              // Product they want to buy
    int  quantity;               // How many units
    struct QueueNode *next;      // Pointer to next in queue
};

/* =========================================================
   SECTION 4: GLOBAL VARIABLES
   - Shared across all functions
   ========================================================= */
struct Node      *head       = NULL;  // Head of the linked list (product list)
struct StackNode *stackTop   = NULL;  // Top of the undo stack
struct QueueNode *queueFront = NULL;  // Front of customer queue
struct QueueNode *queueRear  = NULL;  // Rear of customer queue
int nextId = 1;                       // Auto-increment ID counter

/* =========================================================
   SECTION 5: UTILITY / DISPLAY FUNCTIONS
   ========================================================= */

/* Clears the input buffer to avoid leftover characters */
void clearBuffer() {
    int c;
    while ((c = getchar()) != '\n' && c != EOF);
}

/* Prints a horizontal separator line */
void printLine() {
    printf("============================================================\n");
}

/* Prints a thin separator */
void printThinLine() {
    printf("------------------------------------------------------------\n");
}

/* Displays the program header / banner */
void printHeader() {
    printf("\n");
    printLine();
    printf("       INVENTORY MANAGEMENT SYSTEM  v1.0                   \n");
    printf("       Powered by Data Structures in C                      \n");
    printLine();
}

/* Displays a single product in formatted table row */
void printProductRow(struct Product p) {
    printf("| %-4d | %-18s | %-6d | %-8.2f | %-12s |\n",
           p.id, p.name, p.quantity, p.price, p.category);
}

/* Displays table header for product list */
void printTableHeader() {
    printThinLine();
    printf("| %-4s | %-18s | %-6s | %-8s | %-12s |\n",
           "ID", "Name", "Qty", "Price", "Category");
    printThinLine();
}

/* Pauses until user presses Enter */
void pause() {
    printf("\nPress Enter to continue...");
    clearBuffer();
    getchar();
}

/* =========================================================
   SECTION 6: ADMIN LOGIN
   - Password-protected admin access
   ========================================================= */

/*
 * Function: adminLogin
 * --------------------
 * Asks user to enter admin password.
 * Gives 3 attempts. If all fail, exits program.
 *
 * Returns: 1 if login successful, 0 if failed
 */
int adminLogin() {
    char password[30];
    int attempts = 3;

    printHeader();
    printf("\n  ** ADMIN LOGIN REQUIRED **\n\n");

    while (attempts > 0) {
        printf("  Enter Password (%d attempts left): ", attempts);
        scanf("%s", password);
        clearBuffer();

        if (strcmp(password, ADMIN_PASS) == 0) {
            printf("\n  [OK] Login Successful! Welcome, Admin.\n\n");
            return 1;
        } else {
            attempts--;
            printf("  [X] Wrong password!\n");
        }
    }

    printf("\n  Access Denied. Program will exit.\n");
    return 0;
}

/* =========================================================
   SECTION 7: LINKED LIST FUNCTIONS
   - All product storage operations
   ========================================================= */

/*
 * Function: createNode
 * ---------------------
 * Creates a new linked list node using malloc().
 * Stores the product data inside it.
 *
 * p: The product to store
 * Returns: Pointer to the new node
 */
struct Node* createNode(struct Product p) {
    // Allocate memory for one Node on the heap
    struct Node *newNode = (struct Node*) malloc(sizeof(struct Node));

    if (newNode == NULL) {
        printf("[ERROR] Memory allocation failed!\n");
        return NULL;
    }

    newNode->data = p;    // Copy product data into the node
    newNode->next = NULL; // This is the last node (no next yet)
    return newNode;
}

/*
 * Function: addProduct
 * ---------------------
 * Adds a new product to the END of the linked list.
 * Takes input from the user.
 */
void addProduct() {
    struct Product p;

    printf("\n--- ADD NEW PRODUCT ---\n");

    // Assign auto-incremented ID
    p.id = nextId++;

    printf("  Enter Product Name    : ");
    fgets(p.name, MAX_NAME, stdin);
    p.name[strcspn(p.name, "\n")] = '\0'; // Remove newline from fgets

    printf("  Enter Quantity        : ");
    scanf("%d", &p.quantity);

    printf("  Enter Price (Rs.)     : ");
    scanf("%f", &p.price);
    clearBuffer();

    printf("  Enter Category        : ");
    fgets(p.category, MAX_CATEGORY, stdin);
    p.category[strcspn(p.category, "\n")] = '\0';

    // Create a new node and add it at the end of the list
    struct Node *newNode = createNode(p);
    if (newNode == NULL) return;

    if (head == NULL) {
        // List is empty, this becomes the first node
        head = newNode;
    } else {
        // Traverse to the last node
        struct Node *temp = head;
        while (temp->next != NULL) {
            temp = temp->next;
        }
        temp->next = newNode; // Link new node at the end
    }

    printf("\n  [OK] Product '%s' added successfully with ID: %d\n", p.name, p.id);
}

/*
 * Function: displayProducts
 * --------------------------
 * Traverses the linked list and displays all products.
 * Also shows LOW STOCK alert for products with quantity <= LOW_STOCK.
 */
void displayProducts() {
    printf("\n--- ALL PRODUCTS ---\n");

    if (head == NULL) {
        printf("  [!] No products in inventory.\n");
        return;
    }

    printTableHeader();

    struct Node *temp = head;
    while (temp != NULL) {
        printProductRow(temp->data);

        // Low stock alert check
        if (temp->data.quantity <= LOW_STOCK) {
            printf("|  *** LOW STOCK ALERT: %-32s ***  |\n", temp->data.name);
        }

        temp = temp->next; // Move to next node
    }
    printThinLine();
}

/*
 * Function: searchProduct
 * ------------------------
 * Searches for a product by ID or Name.
 * Uses case-insensitive partial name matching.
 */
void searchProduct() {
    printf("\n--- SEARCH PRODUCT ---\n");
    printf("  1. Search by ID\n");
    printf("  2. Search by Name\n");
    printf("  Enter choice: ");

    int choice;
    scanf("%d", &choice);
    clearBuffer();

    struct Node *temp = head;
    int found = 0;

    if (choice == 1) {
        int searchId;
        printf("  Enter Product ID: ");
        scanf("%d", &searchId);
        clearBuffer();

        while (temp != NULL) {
            if (temp->data.id == searchId) {
                printf("\n  [FOUND]\n");
                printTableHeader();
                printProductRow(temp->data);
                printThinLine();
                found = 1;
                break;
            }
            temp = temp->next;
        }

    } else if (choice == 2) {
        char searchName[MAX_NAME];
        printf("  Enter Product Name (or part of it): ");
        fgets(searchName, MAX_NAME, stdin);
        searchName[strcspn(searchName, "\n")] = '\0';

        // Convert search term to lowercase for case-insensitive search
        char lowerSearch[MAX_NAME], lowerName[MAX_NAME];
        for (int i = 0; searchName[i]; i++)
            lowerSearch[i] = tolower(searchName[i]);
        lowerSearch[strlen(searchName)] = '\0';

        printf("\n  Search Results:\n");
        printTableHeader();

        while (temp != NULL) {
            // Copy product name to lowercase
            for (int i = 0; temp->data.name[i]; i++)
                lowerName[i] = tolower(temp->data.name[i]);
            lowerName[strlen(temp->data.name)] = '\0';

            // Check if search term is a substring of the name
            if (strstr(lowerName, lowerSearch) != NULL) {
                printProductRow(temp->data);
                found = 1;
            }
            temp = temp->next;
        }
        printThinLine();
    }

    if (!found) {
        printf("  [!] Product not found.\n");
    }
}

/*
 * Function: updateProduct
 * ------------------------
 * Updates the details of an existing product by ID.
 */
void updateProduct() {
    printf("\n--- UPDATE PRODUCT ---\n");
    printf("  Enter Product ID to Update: ");

    int id;
    scanf("%d", &id);
    clearBuffer();

    struct Node *temp = head;

    while (temp != NULL) {
        if (temp->data.id == id) {
            printf("  Product found: %s\n", temp->data.name);
            printf("  What do you want to update?\n");
            printf("  1. Name\n  2. Quantity\n  3. Price\n  4. Category\n");
            printf("  Enter choice: ");

            int ch;
            scanf("%d", &ch);
            clearBuffer();

            switch (ch) {
                case 1:
                    printf("  New Name: ");
                    fgets(temp->data.name, MAX_NAME, stdin);
                    temp->data.name[strcspn(temp->data.name, "\n")] = '\0';
                    break;
                case 2:
                    printf("  New Quantity: ");
                    scanf("%d", &temp->data.quantity);
                    clearBuffer();
                    break;
                case 3:
                    printf("  New Price: ");
                    scanf("%f", &temp->data.price);
                    clearBuffer();
                    break;
                case 4:
                    printf("  New Category: ");
                    fgets(temp->data.category, MAX_CATEGORY, stdin);
                    temp->data.category[strcspn(temp->data.category, "\n")] = '\0';
                    break;
                default:
                    printf("  [!] Invalid choice.\n");
                    return;
            }

            printf("  [OK] Product updated successfully!\n");
            return;
        }
        temp = temp->next;
    }

    printf("  [!] Product with ID %d not found.\n", id);
}

/* =========================================================
   SECTION 8: STACK FUNCTIONS
   - Used for Undo Delete (LIFO - Last In, First Out)
   ========================================================= */

/*
 * Function: push
 * ---------------
 * Pushes a deleted product onto the stack.
 * This allows us to undo the delete later.
 *
 * p: The product being pushed (saved before deletion)
 */
void push(struct Product p) {
    // Allocate memory for a new stack node
    struct StackNode *newNode = (struct StackNode*) malloc(sizeof(struct StackNode));

    if (newNode == NULL) {
        printf("[ERROR] Stack memory allocation failed!\n");
        return;
    }

    newNode->data = p;
    newNode->next = stackTop; // Point to previous top
    stackTop = newNode;       // New node becomes the top

    printf("  [STACK] Product '%s' saved for undo.\n", p.name);
}

/*
 * Function: pop
 * --------------
 * Pops the most recently deleted product from the stack.
 * Restores it back to the linked list.
 *
 * Returns: 1 if popped successfully, 0 if stack is empty
 */
int pop() {
    if (stackTop == NULL) {
        printf("  [!] Undo stack is empty. Nothing to undo.\n");
        return 0;
    }

    struct Product p = stackTop->data; // Get the top product

    // Remove top from stack
    struct StackNode *temp = stackTop;
    stackTop = stackTop->next;
    free(temp); // Free the memory of the popped node

    // Re-add product to linked list
    struct Node *newNode = createNode(p);
    if (newNode == NULL) return 0;

    if (head == NULL) {
        head = newNode;
    } else {
        struct Node *t = head;
        while (t->next != NULL) t = t->next;
        t->next = newNode;
    }

    printf("  [OK] Undo successful! Product '%s' has been restored.\n", p.name);
    return 1;
}

/*
 * Function: deleteProduct
 * ------------------------
 * Deletes a product from the linked list by ID.
 * Before deleting, pushes the product to the undo stack.
 */
void deleteProduct() {
    printf("\n--- DELETE PRODUCT ---\n");
    printf("  Enter Product ID to Delete: ");

    int id;
    scanf("%d", &id);
    clearBuffer();

    struct Node *temp = head;
    struct Node *prev = NULL;

    while (temp != NULL) {
        if (temp->data.id == id) {
            // Save product to stack before deletion (for undo)
            push(temp->data);

            // Remove node from linked list
            if (prev == NULL) {
                head = temp->next; // Deleting head node
            } else {
                prev->next = temp->next; // Bypass this node
            }

            free(temp); // Free memory
            printf("  [OK] Product with ID %d deleted.\n", id);
            return;
        }
        prev = temp;
        temp = temp->next;
    }

    printf("  [!] Product with ID %d not found.\n", id);
}

/*
 * Function: undoDelete
 * ---------------------
 * Calls pop() to restore the most recently deleted product.
 */
void undoDelete() {
    printf("\n--- UNDO DELETE ---\n");
    pop();
}

/* =========================================================
   SECTION 9: QUEUE FUNCTIONS
   - Customer Billing Queue (FIFO - First In, First Out)
   ========================================================= */

/*
 * Function: enqueue
 * ------------------
 * Adds a customer to the billing queue.
 * Customers are served in the order they arrive.
 */
void enqueue() {
    printf("\n--- ADD CUSTOMER TO QUEUE ---\n");

    struct QueueNode *newNode = (struct QueueNode*) malloc(sizeof(struct QueueNode));
    if (newNode == NULL) {
        printf("[ERROR] Queue memory allocation failed!\n");
        return;
    }

    printf("  Customer Name     : ");
    fgets(newNode->customerName, MAX_NAME, stdin);
    newNode->customerName[strcspn(newNode->customerName, "\n")] = '\0';

    printf("  Product ID to buy : ");
    scanf("%d", &newNode->productId);

    printf("  Quantity          : ");
    scanf("%d", &newNode->quantity);
    clearBuffer();

    newNode->next = NULL;

    // Add to rear of queue
    if (queueFront == NULL) {
        queueFront = queueRear = newNode;
    } else {
        queueRear->next = newNode;
        queueRear = newNode;
    }

    printf("  [OK] Customer '%s' added to billing queue.\n", newNode->customerName);
}

/*
 * Function: dequeue
 * ------------------
 * Removes the customer at the front of the queue.
 * Generates a bill for them.
 */
void dequeue() {
    printf("\n--- SERVE NEXT CUSTOMER ---\n");

    if (queueFront == NULL) {
        printf("  [!] No customers in queue.\n");
        return;
    }

    struct QueueNode *front = queueFront;
    queueFront = queueFront->next;

    if (queueFront == NULL)
        queueRear = NULL; // Queue is now empty

    printf("\n");
    printLine();
    printf("            GENERATING BILL\n");
    printLine();
    printf("  Customer : %s\n", front->customerName);

    // Find product in linked list
    struct Node *temp = head;
    int found = 0;

    while (temp != NULL) {
        if (temp->data.id == front->productId) {
            float total = temp->data.price * front->quantity;
            printf("  Product  : %s\n", temp->data.name);
            printf("  Qty      : %d\n", front->quantity);
            printf("  Price/unit: Rs. %.2f\n", temp->data.price);
            printThinLine();
            printf("  TOTAL    : Rs. %.2f\n", total);
            printLine();

            // Deduct quantity from stock
            if (temp->data.quantity >= front->quantity) {
                temp->data.quantity -= front->quantity;
                printf("  [OK] Stock updated. Remaining: %d\n", temp->data.quantity);
            } else {
                printf("  [!] Insufficient stock! Available: %d\n", temp->data.quantity);
            }
            found = 1;
            break;
        }
        temp = temp->next;
    }

    if (!found) {
        printf("  [!] Product ID %d not found!\n", front->productId);
    }

    free(front); // Free the served customer's node
}

/*
 * Function: displayQueue
 * -----------------------
 * Shows all customers currently waiting in the queue.
 */
void displayQueue() {
    printf("\n--- CUSTOMER QUEUE ---\n");

    if (queueFront == NULL) {
        printf("  [!] Queue is empty.\n");
        return;
    }

    struct QueueNode *temp = queueFront;
    int pos = 1;

    printThinLine();
    printf("  %-4s %-20s %-10s %-8s\n", "Pos", "Customer", "ProductID", "Qty");
    printThinLine();

    while (temp != NULL) {
        printf("  %-4d %-20s %-10d %-8d\n",
               pos++, temp->customerName, temp->productId, temp->quantity);
        temp = temp->next;
    }
    printThinLine();
}

/* =========================================================
   SECTION 10: FILE HANDLING FUNCTIONS
   - Save inventory to file and load it back
   ========================================================= */

/*
 * Function: saveToFile
 * ---------------------
 * Saves all products from the linked list to a text file.
 * Called when the user selects "Save" or before exit.
 */
void saveToFile() {
    FILE *fp = fopen(FILE_NAME, "w"); // Open file for writing (overwrites)

    if (fp == NULL) {
        printf("  [ERROR] Could not open file for saving!\n");
        return;
    }

    // First line: save the next available ID
    fprintf(fp, "%d\n", nextId);

    struct Node *temp = head;
    int count = 0;

    while (temp != NULL) {
        // Write each product's fields separated by '|'
        fprintf(fp, "%d|%s|%d|%.2f|%s\n",
                temp->data.id,
                temp->data.name,
                temp->data.quantity,
                temp->data.price,
                temp->data.category);
        count++;
        temp = temp->next;
    }

    fclose(fp);
    printf("  [OK] %d product(s) saved to '%s'.\n", count, FILE_NAME);
}

/*
 * Function: loadFromFile
 * -----------------------
 * Loads products from the text file into the linked list.
 * Automatically called when the program starts.
 */
void loadFromFile() {
    FILE *fp = fopen(FILE_NAME, "r"); // Open file for reading

    if (fp == NULL) {
        // File doesn't exist yet - that's OK on first run
        printf("  [INFO] No saved data found. Starting fresh.\n");
        return;
    }

    // Read nextId from first line
    fscanf(fp, "%d\n", &nextId);

    struct Product p;
    int count = 0;

    // Read each product line
    while (fscanf(fp, "%d|%49[^|]|%d|%f|%29[^\n]\n",
                  &p.id, p.name, &p.quantity, &p.price, p.category) == 5) {

        // Create node and add to end of linked list
        struct Node *newNode = createNode(p);
        if (newNode == NULL) break;

        if (head == NULL) {
            head = newNode;
        } else {
            struct Node *temp = head;
            while (temp->next != NULL) temp = temp->next;
            temp->next = newNode;
        }
        count++;
    }

    fclose(fp);
    printf("  [OK] %d product(s) loaded from '%s'.\n", count, FILE_NAME);
}

/* =========================================================
   SECTION 11: LOW STOCK ALERT
   - Scans inventory and highlights low-stock items
   ========================================================= */

/*
 * Function: lowStockAlert
 * ------------------------
 * Shows a dedicated report for products below LOW_STOCK threshold.
 */
void lowStockAlert() {
    printf("\n--- LOW STOCK ALERT REPORT ---\n");
    printf("  (Products with quantity <= %d)\n\n", LOW_STOCK);

    struct Node *temp = head;
    int found = 0;

    printTableHeader();

    while (temp != NULL) {
        if (temp->data.quantity <= LOW_STOCK) {
            printProductRow(temp->data);
            found = 1;
        }
        temp = temp->next;
    }
    printThinLine();

    if (!found) {
        printf("  [OK] All products have sufficient stock.\n");
    } else {
        printf("  [!] Please restock the above products soon!\n");
    }
}

/* =========================================================
   SECTION 12: BILLING SYSTEM MENU
   - Sub-menu for all billing and queue features
   ========================================================= */

/*
 * Function: billingMenu
 * ----------------------
 * Shows a sub-menu for the Billing / Queue System.
 */
void billingMenu() {
    int choice;

    do {
        printf("\n");
        printLine();
        printf("         BILLING & CUSTOMER QUEUE SYSTEM\n");
        printLine();
        printf("  1. Add Customer to Queue\n");
        printf("  2. Serve Next Customer (Generate Bill)\n");
        printf("  3. View Customer Queue\n");
        printf("  0. Back to Main Menu\n");
        printThinLine();
        printf("  Enter choice: ");
        scanf("%d", &choice);
        clearBuffer();

        switch (choice) {
            case 1: enqueue();       break;
            case 2: dequeue();       break;
            case 3: displayQueue();  break;
            case 0: break;
            default: printf("  [!] Invalid choice.\n");
        }

        if (choice != 0) pause();

    } while (choice != 0);
}

/* =========================================================
   SECTION 13: MAIN MENU
   ========================================================= */

/*
 * Function: mainMenu
 * -------------------
 * Displays the main menu of the application.
 * Loops until the user chooses to exit.
 */
void mainMenu() {
    int choice;

    do {
        printHeader();
        printf("  MAIN MENU\n");
        printThinLine();
        printf("  1.  Add Product\n");
        printf("  2.  Display All Products\n");
        printf("  3.  Search Product\n");
        printf("  4.  Update Product\n");
        printf("  5.  Delete Product\n");
        printf("  6.  Undo Last Delete\n");
        printf("  7.  Billing & Customer Queue\n");
        printf("  8.  Low Stock Alert\n");
        printf("  9.  Save Data to File\n");
        printf("  10. Load Data from File\n");
        printf("  0.  Exit\n");
        printThinLine();
        printf("  Enter your choice: ");
        scanf("%d", &choice);
        clearBuffer();

        switch (choice) {
            case 1:  addProduct();    break;
            case 2:  displayProducts();break;
            case 3:  searchProduct(); break;
            case 4:  updateProduct(); break;
            case 5:  deleteProduct(); break;
            case 6:  undoDelete();    break;
            case 7:  billingMenu();   break;
            case 8:  lowStockAlert(); break;
            case 9:  saveToFile();    break;
            case 10: loadFromFile();  break;
            case 0:
                printf("\n  Saving data before exit...\n");
                saveToFile();
                printf("  Thank you for using Inventory Management System!\n");
                printf("  Goodbye!\n\n");
                break;
            default:
                printf("  [!] Invalid choice. Please enter 0-10.\n");
        }

        if (choice != 0) pause();

    } while (choice != 0);
}

/* =========================================================
   SECTION 14: MAIN FUNCTION
   - Entry point of the program
   ========================================================= */

int main() {
    printf("\n");
    printLine();
    printf("   Welcome to Inventory Management System\n");
    printf("   A 1st Year B.Tech C Project\n");
    printLine();

    // Step 1: Admin Login
    if (!adminLogin()) {
        return 1; // Exit if login fails
    }

    // Step 2: Load existing data from file (if any)
    printf("\n  Loading inventory data...\n");
    loadFromFile();

    // Step 3: Show main menu (loops until user exits)
    mainMenu();

    return 0;
}

/*
=============================================================================
  END OF PROGRAM
=============================================================================
*/
