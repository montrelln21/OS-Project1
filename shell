#include <stdbool.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/wait.h>
#include <fcntl.h>
#include <signal.h>

#define MAX_COMMAND_LINE_LEN 1024
#define MAX_COMMAND_LINE_ARGS 128
#define PATH_MAX 1024

char prompt[] = "shell > ";
char delimiters[] = " \t\r\n";
extern char **environ;
char *getcwd(char *buf, size_t size);
bool isBackground = false;
pid_t pid;      // To store the child process ID returned by fork().

void sigint_handler(int signum) {
  // kill(pid, SIGKILL);
}     //signal interrupt handler

void sigalarm_handler(int signum) {
  kill(pid, SIGKILL);
}     //signal alarm handler

int main() {
    // Stores the string typed into the command line.
    char command_line[MAX_COMMAND_LINE_LEN];
    char cmd_bak[MAX_COMMAND_LINE_LEN];
  
    // Stores the tokenized command line input.
    char *args[MAX_COMMAND_LINE_ARGS];
    

    char cwd[PATH_MAX];

    char *token, *name, *value;
    int status, position;

    signal(SIGINT, sigint_handler);
    signal(SIGALRM, sigalarm_handler);

    while (true) {
      
        do{
            // Print the shell prompt
            // 0. Modify the prompt to print the current working directory
            printf("%s> ", getcwd(cwd, sizeof(cwd)));
            fflush(stdout);         //  flush the value from buffer to display

            // Read input from stdin and store it in command_line. If there's an
            // error, exit immediately. (If you want to learn more about this line,
            // you can Google "man fgets")
        
            if ((fgets(command_line, MAX_COMMAND_LINE_LEN, stdin) == NULL) && ferror(stdin)) {
                fprintf(stderr, "fgets error");
                exit(0);
            }
 
        }while(command_line[0] == 0x0A);  // while just ENTER pressed

      
        // If the user input was EOF (ctrl+d), exit the shell.
        if (feof(stdin)) {
            printf("\n");
            fflush(stdout);
            fflush(stderr);
            return 0;
        }

        // TODO:
         
        // 1. Tokenize the command line input (split it on whitespace)
        token = strtok(command_line, delimiters);
        position = 0;
        while (token != NULL){
          args[position] = token;
          position++;
          token = strtok(NULL, delimiters);
        }
      
        // 2. Implement Built-In Commands
        int NoOfOwnCmds = 6, i, switchOwnArg = 0;
        char* ListOfOwnCmds[NoOfOwnCmds];
  
        ListOfOwnCmds[0] = "cd";
        ListOfOwnCmds[1] = "pwd";
        ListOfOwnCmds[2] = "echo";
        ListOfOwnCmds[3] = "exit";
        ListOfOwnCmds[4] = "env";
        ListOfOwnCmds[5] = "setenv";

        if (strcmp(args[position-1],"&") == 0) {
          args[position-1] = '\0';
          isBackground = true;
        }
          
        for (i = 0; i < NoOfOwnCmds; i++) {
            if (strcmp(args[0], ListOfOwnCmds[i]) == 0) {
                switchOwnArg = i + 1;
                break;
            }
        }

        switch (switchOwnArg) {
        case 1:
            if (args[1] == NULL) {
              chdir(getenv("HOME"));
            } else {
              chdir(args[1]);
            }
            break;
        case 2:
            printf("%s\n ", getcwd(cwd, sizeof(cwd)));
            break;
        case 3:
            for (i = 1; i < position; i++) {
              if (args[i][0] == '$')
                  printf("%s ", getenv(&args[i][1]));
              else
                  printf("%s ", args[i]);
            }
            printf("\n");
            break;
        case 4:
            printf("Goodbye\n");
            exit(0);
        case 5:
            printf("%s\n", getenv(args[1]));
            break;
        case 6:
            name = strtok(args[1], "=");
            value = strtok(NULL, "=");
            setenv(name, value, 1);
            break;
        default:
          // 3. Create a child process which will execute the command line input
          alarm(10);
          pid = fork();
          if (pid < 0) {
            perror("The child was not created\n");  // If fork() fails it does not create a child and returns -1.
            exit(1);
          } else if (pid == 0) {
            if (execvp(args[0], args) == -1)
                perror("Input not executable");
          } else {
            printf("parent process\n");
            if(!isBackground)  // if & was found on command line
              pid = wait(NULL);
               
            isBackground = false; // then we wait
          }
          break;
        }
    
        for (i = 0; i < NoOfOwnCmds; i++)
            args[i]  = '\0';
            
            
        // Extra Credit
        // man dup2
        // man open
        // man pipes
    }
    // This should never be reached.
    return -1;
}
