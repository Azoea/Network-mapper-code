# Network-mapper-code
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <netdb.h>

int main(int argc, char **argv) {
    struct hostent *host;
    int err, i, sock, start, end;
    char hostname[100];
    struct sockaddr_in sa;

    printf("Enter hostname or IP: ");
    fgets(hostname, sizeof(hostname), stdin);
    hostname[strcspn(hostname, "\n")] = '\0'; // Remove trailing newline if present

    printf("\nEnter start port number: ");
    scanf("%d", &start);

    printf("\nEnter end port number: ");
    scanf("%d", &end);

    memset(&sa, 0, sizeof sa); // Clear sockaddr_in struct
    sa.sin_family = AF_INET;

    if (isdigit(hostname[0])) {
        sa.sin_addr.s_addr = inet_addr(hostname);  
    } else if ((host = gethostbyname(hostname)) != NULL) {
        memcpy((char*) &sa.sin_addr, (char*) host->h_addr, host->h_length);
    } else {
        herror(hostname);
        exit(2);
    }

    for (i = start; i <= end; i++) {
        printf("Scanning port %i\n", i);

        sa.sin_port = htons(i);
        sock = socket(AF_INET, SOCK_STREAM, 0);

        if (sock < 0) {
            perror("socket");
            exit(1);    
        }

        err = connect(sock, (struct sockaddr*)&sa, sizeof(sa));

        if (err < 0) {
            // Connection failed or port is closed
            // perror("connect"); // Optionally, you can print an error message here
        } else {
            printf("Port %i is open\n", i);
        }

        pclose;
    }

    return 0;
}
