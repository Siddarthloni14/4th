# 4thDevelop a Program in C for converting an Infix Expression to Postfix Expression. Program 
should support for both parenthesized and free parenthesized expressions with the operators: +, -, 
*, /, % (Remainder), ^ (Power) and alphanumeric operands.

#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>
#include <ctype.h>

#define MAX_SIZE 100

// Stack structure
struct Stack {
    char items[MAX_SIZE];
    int top;
};

// Function prototypes
void initializeStack(struct Stack *s);
bool isEmpty(struct Stack *s);
bool isFull(struct Stack *s);
void push(struct Stack *s, char value);
char pop(struct Stack *s);
int precedence(char op);
bool isOperator(char op);
bool isOperand(char op);
void infixToPostfix(char *infix, char *postfix);

int main() {
    char infix[MAX_SIZE], postfix[MAX_SIZE];

    // Input infix expression
    printf("Enter infix expression: ");
    scanf("%s", infix);

    // Convert infix to postfix
    infixToPostfix(infix, postfix);

    // Output postfix expression
    printf("Postfix expression: %s\n", postfix);

    return 0;
}

// Function to initialize stack
void initializeStack(struct Stack *s) {
    s->top = -1;
}

// Function to check if the stack is empty
bool isEmpty(struct Stack *s) {
    return s->top == -1;
}

// Function to check if the stack is full
bool isFull(struct Stack *s) {
    return s->top == MAX_SIZE - 1;
}

// Function to push an element onto the stack
void push(struct Stack *s, char value) {
    s->items[++s->top] = value;
}

// Function to pop an element from the stack
char pop(struct Stack *s) {
    return s->items[s->top--];
}

// Function to determine precedence of operators
int precedence(char op) {
    switch (op) {
        case '^':
            return 3;
        case '*':
        case '/':
        case '%':
            return 2;
        case '+':
        case '-':
            return 1;
        default:
            return 0;
    }
}

// Function to check if a character is an operator
bool isOperator(char op) {
    return op == '+' || op == '-' || op == '*' || op == '/' || op == '%' || op == '^';
}

// Function to check if a character is an operand
bool isOperand(char op) {
    return isalnum(op);
}

// Function to convert infix expression to postfix expression
void infixToPostfix(char *infix, char *postfix) {
    struct Stack stack;
    initializeStack(&stack);
    int i = 0, j = 0;

    while (infix[i] != '\0') {
        char token = infix[i];
        if (isOperand(token)) {
            postfix[j++] = token;
        } else if (token == '(') {
            push(&stack, token);
        } else if (token == ')') {
            while (!isEmpty(&stack) && stack.items[stack.top] != '(') {
                postfix[j++] = pop(&stack);
            }
            if (!isEmpty(&stack) && stack.items[stack.top] == '(')
                pop(&stack); // Discard '('
        } else if (isOperator(token)) {
            while (!isEmpty(&stack) && precedence(stack.items[stack.top]) >= precedence(token)) {
                postfix[j++] = pop(&stack);
            }
            push(&stack, token);
        }
        i++;
    }

    // Pop remaining operators from stack to output
    while (!isEmpty(&stack)) {
        postfix[j++] = pop(&stack);
    }

    postfix[j] = '\0'; // Null terminate the postfix expression
}
