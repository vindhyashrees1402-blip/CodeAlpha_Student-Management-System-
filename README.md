
# CodeAlpha_Student-Management-System-



#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct Student {
    int id;
    char name[50];
    int age;
    float marks;
};

void addStudent();
void displayStudents();
void searchStudent();
void updateStudent();
void deleteStudent();

int main() {
    int choice;
    while (1) {
        printf("\n--- Student Management System ---\n");
        printf("1. Add Student\n");
        printf("2. Display All Students\n");
        printf("3. Search Student by ID\n");
        printf("4. Update Student Record\n");
        printf("5. Delete Student Record\n");
        printf("6. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1: addStudent(); break;
            case 2: displayStudents(); break;
            case 3: searchStudent(); break;
            case 4: updateStudent(); break;
            case 5: deleteStudent(); break;
            case 6: exit(0);
            default: printf("Invalid choice! Please try again.\n");
        }
    }
    return 0;
}

void addStudent() {
    FILE *fp = fopen("students.dat", "ab");
    if (!fp) {
        printf("Error opening file!\n");
        return;
    }
    struct Student s;
    printf("Enter ID: ");
    scanf("%d", &s.id);
    printf("Enter Name: ");
    scanf(" %[^\n]%*c", s.name);
    printf("Enter Age: ");
    scanf("%d", &s.age);
    printf("Enter Marks: ");
    scanf("%f", &s.marks);

    fwrite(&s, sizeof(struct Student), 1, fp);
    fclose(fp);
    printf("Student added successfully!\n");
}

void displayStudents() {
    FILE *fp = fopen("students.dat", "rb");
    if (!fp) {
        printf("No records found!\n");
        return;
    }
    struct Student s;
    printf("\nID\tName\t\tAge\tMarks\n");
    printf("------------------------------------\n");
    while (fread(&s, sizeof(struct Student), 1, fp)) {
        printf("%d\t%-15s\t%d\t%.2f\n", s.id, s.name, s.age, s.marks);
    }
    fclose(fp);
}

void searchStudent() {
    FILE *fp = fopen("students.dat", "rb");
    if (!fp) {
        printf("No records found!\n");
        return;
    }
    int id, found = 0;
    printf("Enter Student ID to search: ");
    scanf("%d", &id);

    struct Student s;
    while (fread(&s, sizeof(struct Student), 1, fp)) {
        if (s.id == id) {
            printf("Record Found:\nID: %d\nName: %s\nAge: %d\nMarks: %.2f\n", s.id, s.name, s.age, s.marks);
            found = 1;
            break;
        }
    }
    if (!found) printf("Student with ID %d not found.\n", id);
    fclose(fp);
}

void updateStudent() {
    FILE *fp = fopen("students.dat", "rb");
    FILE *temp = fopen("temp.dat", "wb");
    if (!fp || !temp) {
        printf("Error processing file!\n");
        return;
    }
    int id, found = 0;
    printf("Enter Student ID to update: ");
    scanf("%d", &id);

    struct Student s;
    while (fread(&s, sizeof(struct Student), 1, fp)) {
        if (s.id == id) {
            printf("Enter New Name: ");
            scanf(" %[^\n]%*c", s.name);
            printf("Enter New Age: ");
            scanf("%d", &s.age);
            printf("Enter New Marks: ");
            scanf("%f", &s.marks);
            found = 1;
        }
        fwrite(&s, sizeof(struct Student), 1, temp);
    }
    fclose(fp);
    fclose(temp);
    remove("students.dat");
    rename("temp.dat", "students.dat");

    if (found) printf("Student record updated successfully!\n");
    else printf("Student with ID %d not found.\n", id);
}

void deleteStudent() {
    FILE *fp = fopen("students.dat", "rb");
    FILE *temp = fopen("temp.dat", "wb");
    if (!fp || !temp) {
        printf("Error processing file!\n");
        return;
    }
    int id, found = 0;
    printf("Enter Student ID to delete: ");
    scanf("%d", &id);

    struct Student s;
    while (fread(&s, sizeof(struct Student), 1, fp)) {
        if (s.id == id) {
            found = 1;
            continue; // Skip writing this record to delete it
        }
        fwrite(&s, sizeof(struct Student), 1, temp);
    }
    fclose(fp);
    fclose(temp);
    remove("students.dat");
    rename("temp.dat", "students.dat");

    if (found) printf("Student record deleted successfully!\n");
    else printf("Student with ID %d not found.\n", id);
}
