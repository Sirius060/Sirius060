%{
#include <stdio.h>
#include <stdlib.h>
int float_count = 0;
int int_count = 0;
int id_count = 0;
int error_count = 0;
void print_summary() {
    printf("Total Float: %d\n", float_count);
    printf("Total Integer: %d\n", int_count);
    printf("Total Identifier: %d\n", id_count);
    printf("Total Error: %d\n", error_count);
}
%}

%option noyywrap

%%

[+-]?([0-9]+\.[0-9]+) { 
    printf("Float: %s\n", yytext); 
    float_count++; 
}

[+-]?[0-9]+ { 
    printf("Integer: %s\n", yytext); 
    int_count++; 
}

[a-zA-Z][a-zA-Z0-9_]* { 
    printf("Identifier: %s\n", yytext); 
    id_count++; 
}

\.([0-9]+) { 
    printf("Error: %s\n", yytext); 
    error_count++; 
}

_[a-zA-Z][a-zA-Z0-9_]* { 
    printf("Error: %s\n", yytext); 
    error_count++; 
}

[^\n;]+; { 
    printf("Error: %s\n", yytext); 
    error_count++; 
}


; { 
    printf("Error: ;\n"); 
    error_count++; 
}

[ \t]+ { /* do nothing */ }

\n { /* do nothing */ }

%%

int main(int argc, char **argv) {
    if (argc > 1) {
        FILE *file = fopen(argv[1], "r");
        if (!file) {
            perror("Error opening file");
            return EXIT_FAILURE;
        }
        yyin = file; 
    } else {
        return EXIT_FAILURE;
   }
   
   yylex(); 
   print_summary();

   return EXIT_SUCCESS;
}
