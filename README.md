#include <stdio.h>
#include <stdlib.h>

#define MAX 50

/* Structure for sparse matrix */
struct sparse
{
    int row;
    int col;
    int value;
};

struct sparse A[MAX], B[MAX], SUM[MAX], TRANS[MAX];

int countA = 0, countB = 0;
int countSUM = 0, countTRANS = 0;

int rowsA, colsA, rowsB, colsB;


void readMatrix(struct sparse M[], int *count, int *rows, int *cols)
{
    int i, j, value;

    printf("Enter number of rows and columns: ");
    scanf("%d%d", rows, cols);

    printf("Enter the matrix elements:\n");

    *count = 0;

    for(i = 0; i < *rows; i++)
    {
        for(j = 0; j < *cols; j++)
        {
            scanf("%d", &value);

            if(value != 0)
            {
                (*count)++;

                M[*count].row = i;
                M[*count].col = j;
                M[*count].value = value;
            }
        }
    }

    /* First element stores matrix information */
    M[0].row = *rows;
    M[0].col = *cols;
    M[0].value = *count;
}


void display(struct sparse M[])
{
    int i;
    int count = M[0].value;

    printf("\nRow\tColumn\tValue\n");

    for(i = 0; i <= count; i++)
    {
        printf("%d\t%d\t%d\n",
               M[i].row,
               M[i].col,
               M[i].value);
    }
}


/* Function to display normal matrix */
void displayNormal(struct sparse M[])
{
    int i, j, k = 1;

    printf("\nMatrix:\n");

    for(i = 0; i < M[0].row; i++)
    {
        for(j = 0; j < M[0].col; j++)
        {
            if(k <= M[0].value &&
               M[k].row == i &&
               M[k].col == j)
            {
                printf("%d\t", M[k].value);
                k++;
            }
            else
            {
                printf("0\t");
            }
        }

        printf("\n");
    }
}


/* Function for transpose */
void transpose(struct sparse M[], struct sparse T[])
{
    int i, j, k = 1;

    T[0].row = M[0].col;
    T[0].col = M[0].row;
    T[0].value = M[0].value;

    for(i = 0; i < M[0].col; i++)
    {
        for(j = 1; j <= M[0].value; j++)
        {
            if(M[j].col == i)
            {
                T[k].row = M[j].col;
                T[k].col = M[j].row;
                T[k].value = M[j].value;
                k++;
            }
        }
    }
}


/* Function for addition */
int addSparse(struct sparse A[],
              struct sparse B[],
              struct sparse S[])
{
    int i = 1, j = 1, k = 1;

    if(A[0].row != B[0].row ||
       A[0].col != B[0].col)
    {
        return 0;
    }

    S[0].row = A[0].row;
    S[0].col = A[0].col;

    while(i <= A[0].value && j <= B[0].value)
    {
        if(A[i].row == B[j].row &&
           A[i].col == B[j].col)
        {
            if(A[i].value + B[j].value != 0)
            {
                S[k].row = A[i].row;
                S[k].col = A[i].col;
                S[k].value = A[i].value + B[j].value;
                k++;
            }

            i++;
            j++;
        }

        else if(A[i].row < B[j].row ||
               (A[i].row == B[j].row &&
                A[i].col < B[j].col))
        {
            S[k] = A[i];
            i++;
            k++;
        }

        else
        {
            S[k] = B[j];
            j++;
            k++;
        }
    }

    while(i <= A[0].value)
    {
        S[k] = A[i];
        i++;
        k++;
    }

    while(j <= B[0].value)
    {
        S[k] = B[j];
        j++;
        k++;
    }

    S[0].value = k - 1;

    return 1;
}


/* Main function */
int main()
{
    int choice;

    while(1)
    {
        printf("\n\n===== SPARSE MATRIX PROCESSING =====\n");
        printf("1. Read Matrix A\n");
        printf("2. Read Matrix B\n");
        printf("3. Display Matrix A\n");
        printf("4. Display Matrix B\n");
        printf("5. Add A + B\n");
        printf("6. Transpose Matrix A\n");
        printf("7. Transpose Matrix B\n");
        printf("8. Exit\n");

        printf("\nEnter your choice: ");
        scanf("%d", &choice);

        switch(choice)
        {
            case 1:
                readMatrix(A, &countA, &rowsA, &colsA);

                printf("\nSparse Matrix A in Triplet Form:");
                display(A);
                break;


            case 2:
                readMatrix(B, &countB, &rowsB, &colsB);

                printf("\nSparse Matrix B in Triplet Form:");
                display(B);
                break;


            case 3:
                if(countA == 0)
                    printf("\nMatrix A not entered!");
                else
                {
                    printf("\nMatrix A:");
                    displayNormal(A);

                    printf("\nTriplet Representation:");
                    display(A);
                }
                break;


            case 4:
                if(countB == 0)
                    printf("\nMatrix B not entered!");
                else
                {
                    printf("\nMatrix B:");
                    displayNormal(B);

                    printf("\nTriplet Representation:");
                    display(B);
                }
                break;


            case 5:
                if(countA == 0 || countB == 0)
                {
                    printf("\nEnter both matrices first!");
                }
                else if(addSparse(A, B, SUM) == 0)
                {
                    printf("\nAddition not possible!");
                    printf("\nBoth matrices must have the same dimensions.");
                }
                else
                {
                    countSUM = SUM[0].value;

                    printf("\nA + B in Triplet Form:");
                    display(SUM);

                    printf("\nA + B:");
                    displayNormal(SUM);
                }
                break;


            case 6:
                if(countA == 0)
                {
                    printf("\nEnter Matrix A first!");
                }
                else
                {
                    transpose(A, TRANS);

                    countTRANS = TRANS[0].value;

                    printf("\nTranspose of Matrix A:");
                    displayNormal(TRANS);

                    printf("\nTranspose in Triplet Form:");
                    display(TRANS);
                }
                break;


            case 7:
                if(countB == 0)
                {
                    printf("\nEnter Matrix B first!");
                }
                else
                {
                    transpose(B, TRANS);

                    printf("\nTranspose of Matrix B:");
                    displayNormal(TRANS);

                    printf("\nTranspose in Triplet Form:");
                    display(TRANS);
                }
                break;


            case 8:
                printf("\nProgram terminated.\n");
                exit(0);


            default:
                printf("\nInvalid choice!");
        }
    }

    return 0;
}
# data-structure-2nd-program
