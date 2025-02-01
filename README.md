# Student-Management-System.c

Code:

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// Structure to store student information
struct Student {
    int rollNo;
    char name[100];
    int age;
    float marks;
};

// Function to add a student to the file
void addStudent() {
    FILE *file = fopen("students.dat", "ab");
    if (file == NULL) {
        printf("Error opening file.\n");
        return;
    }
    
    struct Student student;
    printf("Enter Roll Number: ");
    scanf("%d", &student.rollNo);
    getchar();  // Consume the newline character left by scanf
    printf("Enter Name: ");
    fgets(student.name, sizeof(student.name), stdin);
    student.name[strcspn(student.name, "\n")] = '\0';  // Remove the newline character at the end
    printf("Enter Age: ");
    scanf("%d", &student.age);
    printf("Enter Marks: ");
    scanf("%f", &student.marks);

    fwrite(&student, sizeof(struct Student), 1, file);
    fclose(file);
    printf("Student record added successfully!\n");
}

// Function to view all students
void viewStudents() {
    FILE *file = fopen("students.dat", "rb");
    if (file == NULL) {
        printf("Error opening file.\n");
        return;
    }
    
    struct Student student;
    printf("\n--- List of Students ---\n");
    while (fread(&student, sizeof(struct Student), 1, file)) {
        printf("Roll No: %d\n", student.rollNo);
        printf("Name: %s\n", student.name);
        printf("Age: %d\n", student.age);
        printf("Marks: %.2f\n\n", student.marks);
    }

    fclose(file);
}

// Function to search a student by roll number
void searchStudent() {
    int rollNo;
    printf("Enter the Roll Number to search: ");
    scanf("%d", &rollNo);

    FILE *file = fopen("students.dat", "rb");
    if (file == NULL) {
        printf("Error opening file.\n");
        return;
    }
    
    struct Student student;
    int found = 0;
    while (fread(&student, sizeof(struct Student), 1, file)) {
        if (student.rollNo == rollNo) {
            printf("\nStudent Found!\n");
            printf("Roll No: %d\n", student.rollNo);
            printf("Name: %s\n", student.name);
            printf("Age: %d\n", student.age);
            printf("Marks: %.2f\n", student.marks);
            found = 1;
            break;
        }
    }
    if (!found) {
        printf("Student with Roll No %d not found.\n", rollNo);
    }

    fclose(file);
}

// Function to delete a student by roll number
void deleteStudent() {
    int rollNo;
    printf("Enter the Roll Number to delete: ");
    scanf("%d", &rollNo);

    FILE *file = fopen("students.dat", "rb");
    FILE *tempFile = fopen("temp.dat", "wb");
    if (file == NULL || tempFile == NULL) {
        printf("Error opening file.\n");
        return;
    }
    
    struct Student student;
    int found = 0;
    while (fread(&student, sizeof(struct Student), 1, file)) {
        if (student.rollNo != rollNo) {
            fwrite(&student, sizeof(struct Student), 1, tempFile);
        } else {
            found = 1;
        }
    }

    fclose(file);
    fclose(tempFile);
    
    // Replace original file with temp file
    remove("students.dat");
    rename("temp.dat", "students.dat");

    if (found) {
        printf("Student record with Roll No %d deleted successfully.\n", rollNo);
    } else {
        printf("Student with Roll No %d not found.\n", rollNo);
    }
}

// Function to initialize some sample data if the file doesn't exist
void initializeSampleData() {
    FILE *file = fopen("students.dat", "rb");
    if (file != NULL) {
        fclose(file);  // File exists, no need to initialize
        return;
    }

    file = fopen("students.dat", "wb");
    if (file == NULL) {
        printf("Error opening file.\n");
        return;
    }

    // Pre-load some sample students into the file
    struct Student sampleStudents[] = {
        {101, "John Doe", 20, 85.5},
        {102, "Jane Smith", 22, 92.3},
        {103, "Alice Brown", 19, 78.9},
        {104, "Bob White", 21, 88.0},
        {105, "Charlie Green", 20, 90.5}
    };

    fwrite(sampleStudents, sizeof(struct Student), 5, file);
    fclose(file);
    printf("Sample student records have been added.\n");
}

// Main menu
void menu() {
    int choice;
    do {
        printf("\n--- Student Management System ---\n");
        printf("1. Add Student\n");
        printf("2. View All Students\n");
        printf("3. Search Student\n");
        printf("4. Delete Student\n");
        printf("5. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1:
                addStudent();
                break;
            case 2:
                viewStudents();
                break;
            case 3:
                searchStudent();
                break;
            case 4:
                deleteStudent();
                break;
            case 5:
                printf("Exiting the system. Goodbye!\n");
                break;
            default:
                printf("Invalid choice! Please try again.\n");
        }
    } while (choice != 5);
}

// Main function
int main() {
    // Initialize sample data if the file does not exist
    initializeSampleData();
    menu();  // Run the main menu
    return 0;
}



