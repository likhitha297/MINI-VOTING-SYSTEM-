#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_CANDIDATES 10
#define MAX_VOTERS 100
#define NAME_LEN 50
#define PASSWORD "admin123"

typedef struct Candidate {
    int id;
    char name[NAME_LEN];
    int votes;
} Candidate;

typedef struct Voter {
    int id;
    char name[NAME_LEN];
    int hasVoted;
} Voter;

Candidate candidates[MAX_CANDIDATES];
Voter voters[MAX_VOTERS];

int candidateCount = 0;
int voterCount = 0;

// Function declarations
void adminMenu();
void userMenu();
void addCandidate();
void addVoter();
void castVote();
void displayResults();
void listCandidates();
int findCandidateById(int id);
int findVoterById(int id);
void loginAdmin();
void listVoters();

void pause() {
    printf("\nPress Enter to continue...");
    getchar();
    getchar();
}

void addCandidate() {
    if (candidateCount >= MAX_CANDIDATES) {
        printf("Candidate limit reached.\n");
        return;
    }
    Candidate c;
    c.id = candidateCount + 1;
    printf("Enter candidate name: ");
    getchar();
    fgets(c.name, NAME_LEN, stdin);
    c.name[strcspn(c.name, "\n")] = 0;
    c.votes = 0;
    candidates[candidateCount++] = c;
    printf("Candidate added with ID: %d\n", c.id);
}

void addVoter() {
    if (voterCount >= MAX_VOTERS) {
        printf("Voter limit reached.\n");
        return;
    }
    Voter v;
    v.id = voterCount + 1;
    printf("Enter voter name: ");
    getchar();
    fgets(v.name, NAME_LEN, stdin);
    v.name[strcspn(v.name, "\n")] = 0;
    v.hasVoted = 0;
    voters[voterCount++] = v;
    printf("Voter registered with ID: %d\n", v.id);
}

int findVoterById(int id) {
    for (int i = 0; i < voterCount; i++) {
        if (voters[i].id == id)
            return i;
    }
    return -1;
}

int findCandidateById(int id) {
    for (int i = 0; i < candidateCount; i++) {
        if (candidates[i].id == id)
            return i;
    }
    return -1;
}

void castVote() {
    int voterId;
    printf("Enter your voter ID: ");
    scanf("%d", &voterId);
    int voterIndex = findVoterById(voterId);
    if (voterIndex == -1) {
        printf("Invalid Voter ID.\n");
        return;
    }
    if (voters[voterIndex].hasVoted) {
        printf("You have already voted!\n");
        return;
    }

    listCandidates();
    int choice;
    printf("Enter candidate ID to vote: ");
    scanf("%d", &choice);
    int candidateIndex = findCandidateById(choice);
    if (candidateIndex == -1) {
        printf("Invalid candidate ID.\n");
        return;
    }

    candidates[candidateIndex].votes++;
    voters[voterIndex].hasVoted = 1;
    printf("Vote cast successfully!\n");
}

void listCandidates() {
    printf("\n--- Candidates ---\n");
    for (int i = 0; i < candidateCount; i++) {
        printf("ID: %d, Name: %s, Votes: %d\n", 
                candidates[i].id, candidates[i].name, candidates[i].votes);
    }
}

void listVoters() {
    printf("\n--- Voters ---\n");
    for (int i = 0; i < voterCount; i++) {
        printf("ID: %d, Name: %s, Voted: %s\n",
                voters[i].id, voters[i].name, 
                voters[i].hasVoted ? "Yes" : "No");
    }
}

void displayResults() {
    printf("\n--- Voting Results ---\n");
    int maxVotes = -1;
    for (int i = 0; i < candidateCount; i++) {
        printf("%s: %d votes\n", candidates[i].name, candidates[i].votes);
        if (candidates[i].votes > maxVotes)
            maxVotes = candidates[i].votes;
    }

    printf("\n--- Winner(s) ---\n");
    for (int i = 0; i < candidateCount; i++) {
        if (candidates[i].votes == maxVotes && maxVotes > 0) {
            printf("%s\n", candidates[i].name);
        }
    }
    if (maxVotes == 0) {
        printf("No votes cast yet.\n");
    }
}

void adminMenu() {
    int choice;
    do {
        printf("\n--- Admin Menu ---\n");
        printf("1. Add Candidate\n");
        printf("2. Add Voter\n");
        printf("3. View Candidates\n");
        printf("4. View Voters\n");
        printf("5. View Results\n");
        printf("6. Logout\n");
        printf("Enter choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1: addCandidate(); break;
            case 2: addVoter(); break;
            case 3: listCandidates(); break;
            case 4: listVoters(); break;
            case 5: displayResults(); break;
            case 6: return;
            default: printf("Invalid choice.\n");
        }
        pause();
    } while (choice != 6);
}

void loginAdmin() {
    char pass[20];
    printf("Enter admin password: ");
    scanf("%s", pass);
    if (strcmp(pass, PASSWORD) == 0) {
        adminMenu();
    } else {
        printf("Incorrect password.\n");
    }
}

void userMenu() {
    int choice;
    do {
        printf("\n--- Voter Menu ---\n");
        printf("1. Vote\n");
        printf("2. View Candidates\n");
        printf("3. Back to Main Menu\n");
        printf("Enter choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1: castVote(); break;
            case 2: listCandidates(); break;
            case 3: return;
            default: printf("Invalid choice.\n");
        }
        pause();
    } while (choice != 3);
}

int main() {
    int choice;
    do {
        printf("\n=== Mini Voting System ===\n");
        printf("1. Admin Login\n");
        printf("2. Voter Menu\n");
        printf("3. Exit\n");
        printf("Enter choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1: loginAdmin(); break;
            case 2: userMenu(); break;
            case 3: printf("Exiting...\n"); break;
            default: printf("Invalid choice.\n");
        }
    } while (choice != 3);

    return 0;
}
