#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>

#define PORT 8080

int main()
{
    int clientsocket;
    struct sockaddr_in serveraddress;
    int connection_status;

    clientsocket = socket(AF_INET, SOCK_STREAM, 0);
    if (clientsocket < 0)
    {
        printf("socket creation failed\n");
        return -1;
    }

    serveraddress.sin_family = AF_INET;
    serveraddress.sin_port = htons(PORT);
    serveraddress.sin_addr.s_addr = INADDR_ANY;

    connection_status = connect(clientsocket, (struct sockaddr *)&serveraddress, sizeof(serveraddress));
    if (connection_status == -1)
    {
        printf("connection failed\n");
        return -1;
    }

    int num1, num2, result;
    char operator;

    printf("Enter first number: ");
    scanf("%d", &num1);
    printf("Enter second number: ");
    scanf("%d", &num2);
    printf("Enter operator (+, -, *, /): ");
    scanf(" %c", &operator);

    send(clientsocket, &num1, sizeof(num1), 0);
    send(clientsocket, &num2, sizeof(num2), 0);
    send(clientsocket, &operator, sizeof(operator), 0);

    recv(clientsocket, &result, sizeof(result), 0);

    printf("Result received from server: %d\n", result);

    close(clientsocket);
    return 0;
}

