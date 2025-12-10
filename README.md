hospital management system 
// hospital_mgmt.c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_NAME 60
#define PAT_FILE "patients.dat"
#define APP_FILE "appointments.dat"

typedef struct {
    int pid;
    char name[MAX_NAME];
    int age;
    char disease[50];
} Patient;

typedef struct {
    int appid;
    int pid;
    char date[20]; // e.g., 2025-12-10
    char doctor[50];
} Appointment;

void add_patient() {
    Patient p; FILE *fp=fopen(PAT_FILE,"ab"); if(!fp){perror("File"); return;}
    printf("Patient ID: "); scanf("%d",&p.pid);
    printf("Name: "); getchar(); fgets(p.name,MAX_NAME,stdin); p.name[strcspn(p.name,"\n")]=0;
    printf("Age: "); scanf("%d",&p.age);
    printf("Disease: "); getchar(); fgets(p.disease,50,stdin); p.disease[strcspn(p.disease,"\n")]=0;
    fwrite(&p,sizeof(Patient),1,fp); fclose(fp); printf("Patient added.\n");
}

void list_patients(){
    FILE *fp=fopen(PAT_FILE,"rb"); if(!fp){printf("No patients.\n"); return;}
    Patient p; printf("--- Patients ---\n");
    while(fread(&p,sizeof(Patient),1,fp)==1) printf("ID:%d Name:%s Age:%d Disease:%s\n",p.pid,p.name,p.age,p.disease);
    fclose(fp);
}

void book_appointment() {
    Appointment a; FILE *fp=fopen(APP_FILE,"ab"); if(!fp){perror("File"); return;}
    printf("Appointment ID: "); scanf("%d",&a.appid);
    printf("Patient ID: "); scanf("%d",&a.pid);
    printf("Date (YYYY-MM-DD): "); getchar(); fgets(a.date,20,stdin); a.date[strcspn(a.date,"\n")]=0;
    printf("Doctor: "); fgets(a.doctor,50,stdin); a.doctor[strcspn(a.doctor,"\n")]=0;
    fwrite(&a,sizeof(Appointment),1,fp); fclose(fp); printf("Appointment booked.\n");
}

void list_appointments(){
    FILE *fp=fopen(APP_FILE,"rb"); if(!fp){printf("No appointments.\n"); return;}
    Appointment a; printf("--- Appointments ---\n");
    while(fread(&a,sizeof(Appointment),1,fp)==1) printf("AppID:%d PID:%d Date:%s Doctor:%s\n",a.appid,a.pid,a.date,a.doctor);
    fclose(fp);
}

void search_patient(){
    int pid; printf("Enter patient ID: "); scanf("%d",&pid);
    FILE *fp=fopen(PAT_FILE,"rb"); if(!fp){printf("No data.\n"); return;}
    Patient p; int found=0;
    while(fread(&p,sizeof(Patient),1,fp)==1){
        if(p.pid==pid){ printf("Found: ID:%d Name:%s Age:%d Disease:%s\n",p.pid,p.name,p.age,p.disease); found=1; break; }
    }
    if(!found) printf("Not found.\n");
    fclose(fp);
}

int main(){
    int ch;
    while(1){
        printf("\nHospital System\n1.Add Patient\n2.List Patients\n3.Book Appointment\n4.List Appointments\n5.Search Patient\n6.Exit\nChoose: ");
        if(scanf("%d",&ch)!=1){ getchar(); continue;}
        switch(ch){
            case 1: add_patient(); break;
            case 2: list_patients(); break;
            case 3: book_appointment(); break;
            case 4: list_appointments(); break;
            case 5: search_patient(); break;
            case 6: exit(0);
            default: printf("Invalid\n");
        }
    }
    return 0;
}
