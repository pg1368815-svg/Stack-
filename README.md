#include <stdio.h>
#include <string.h>

int size;
char stack[100]; 
int top = -1;

int isEmpty() {
    return (top == -1);
}

int isFull() {
    return (top == size - 1);
}

void push(char item) {
    if (!isFull()) {
        stack[++top] = item;
    } else {
        printf("Stack is Full!\n");
    }
}

char pop() {
    if (!isEmpty()) {
        return stack[top--];
    } else {
        return '\0';
    }
}

char TOP() {
    if (!isEmpty()) {
        return stack[top];
    } else {
        return '\0';
    }
}

void displayStack() {
    if (isEmpty()) {
        printf("Stack is empty!\n");
        return;
    }
    printf("Stack elements: ");
    for (int i = 0; i <= top; i++) {
        printf("%c ", stack[i]);
    }
    printf("\n");
}

int isOperand(char ch) {
    if ((ch >= 'a' && ch <= 'z') || (ch >= 'A' && ch <= 'Z') || (ch >= '0' && ch <= '9'))
        return 1;
    return 0;
}

int icp(char ch) {
    if (ch == '+' || ch == '-')
        return 1;
    if (ch == '*' || ch == '/')
        return 2;
    if (ch == '^')
        return 3;  
    if (ch == '(')
        return 5;
    return 0;
}


int isp(char ch) {
    if (ch == '+' || ch == '-')
        return 1;
    if (ch == '*' || ch == '/')
        return 2;
    if (ch == '^')
        return 4;  
    return 0;
}

void reverse(char str[]) {
    int i, j;
    char temp;
    int len = strlen(str);
    for (i = 0, j = len - 1; i < j; i++, j--) {
        temp = str[i];
        str[i] = str[j];
        str[j] = temp;
    }
}

void infixToPrefix(char in_exp[]) {
    char pre_exp[100];
    int k = 0, i = 0;
    char tkn;

    reverse(in_exp);
    for (int x = 0; in_exp[x] != '\0'; x++) {
        if (in_exp[x] == '(')
            in_exp[x] = ')';
        else if (in_exp[x] == ')')
            in_exp[x] = '(';
    }

    tkn = in_exp[i];
    while (tkn != '\0') {
        if (isOperand(tkn)) {
            pre_exp[k] = in_exp[i];
            k++;
        } else {
            if (tkn == '(') {
                push('(');
            } else if (tkn == ')') {
                while ((tkn = pop()) != '(') {
                    pre_exp[k] = tkn;
                    k++;
                }
            } else {
                while (!isEmpty() && isp(TOP()) >= icp(tkn)) {
                    pre_exp[k] = pop();
                    k++;
                }
                push(tkn);
            }
        }
        i++;
        tkn = in_exp[i];
    }

    while (!isEmpty()) {
        pre_exp[k] = pop();
        k++;
    }
    pre_exp[k] = '\0';

    reverse(pre_exp);
    printf("Prefix Expression: %s\n", pre_exp);
}


void infixToPostfix(char in_exp[]) {
    char post_exp[100];
    int i = 0, k = 0;
    char tkn;

    while ((tkn = in_exp[i++]) != '\0') {
        if (isOperand(tkn)) {
            post_exp[k++] = tkn;
        } else if (tkn == '(') {
            push(tkn);
        } else if (tkn == ')') {
            while (!isEmpty() && TOP() != '(') {
                post_exp[k++] = pop();
            }
            pop();
        } else {
            while (!isEmpty() && isp(TOP()) >= icp(tkn)) {
                post_exp[k++] = pop();
            }
            push(tkn);
        }
    }

    while (!isEmpty()) {
        post_exp[k++] = pop();
    }
    post_exp[k] = '\0';

    printf("Postfix Expression: %s\n", post_exp);
}

int main() {
    int choice;
    char value;
    char expression[100];

    printf("Enter the size of the stack (max 100): ");
    scanf("%d", &size);

    while (1) {
        printf("\n--- Menu ---\n");
        printf("1. Push\n");
        printf("2. Pop\n");
        printf("3. TOP\n");
        printf("4. Display Stack\n");
        printf("5. Infix to Postfix\n");
        printf("6. Infix to Prefix\n");
        printf("7. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1:
                printf("Enter a character to push: ");
                scanf(" %c", &value);
                push(value);
                break;
            case 2: {
                char temp = pop();
                if (temp != '\0')
                    printf("Popped: %c\n", temp);
                else
                    printf("Stack is empty!\n");
                break;
            }
            case 3: {
                char temp = TOP();
                if (temp != '\0')
                    printf("Top element: %c\n", temp);
                else
                    printf("Stack is empty!\n");
                break;
            }
            case 4:
                displayStack();
                break;
            case 5:
                printf("Enter infix expression: ");
                scanf("%s", expression);
                top = -1;
                infixToPostfix(expression);
                break;
            case 6:
                printf("Enter infix expression: ");
                scanf("%s", expression);
                top = -1;
                infixToPrefix(expression);
                break;
            case 7:
                printf("Exiting program.\n");
                return 0;
            default:
                printf("Invalid choice! Try again.\n");
        }
    }
    return 0;
}
