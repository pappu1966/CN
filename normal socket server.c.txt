#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>

#define PORT 8000

int main()
{
    char servermessage[256];
    struct sockaddr_in serveraddress, clientaddress;
    int client_address_len = sizeof(clientaddress);

    int serversocket;
    /* int socket(int domain, int type, int protocol)
                    DOMAIN: represents address family over which communication will be performed
                    AF_LOCAL OR AF_UNIX (client and server on same node)
                    AF_INET (represents the IPv4 address of client)
                    AF_BLUETOOTH (for low lvl bluetooth connection) 
                    
                    TYPE: type of connection
                    SOCK_STREAM (for TCP)
                    SOCK_DGRAM (for UDP)
                    
                    PROTOCOL: protocol used in the family. When there is only one protocol in the family, the protocol number will be 0,
                    else specific number of protocols should be specified   
    */
    serversocket = socket(AF_INET, SOCK_STREAM, 0); 
    if(serversocket < 0)
    {
        printf("socket failed");
        return -1;
    }

    serveraddress.sin_family = AF_INET;
    serveraddress.sin_port = htons(PORT);
    serveraddress.sin_addr.s_addr = INADDR_ANY;

    int bindstatus;
    // int bind(int socket_descriptor, const struct sockaddr *address, sizeof(address))
    bindstatus = bind(serversocket, (const struct sockaddr*)&serveraddress, sizeof(serveraddress));
    if (bindstatus < 0)
    {
        printf("binding failed\n");
        return -1;
    }
    else
    {
        printf("binding is successful\n");
    }

    // int listen(int socket_descriptor, int back_log)
    // backlog - max num of connection requests that can be made to server by client nodes at a time 
    listen(serversocket, 3);
    printf("Waiting for client connection...\n");

    int clientsocket;
    // 
    clientsocket = accept(serversocket, (struct sockaddr *)&clientaddress, (socklen_t *)&client_address_len);
    if(clientsocket<0)
    {
        printf("connection is rejected by server\n");
        return -1;
    } 
    else
        printf("connection is accepted\n");


    char a[50];
    recv(clientsocket, a, sizeof(a), 0);
    printf("Received from client: %s\n", a);

    char response[50];
    printf("enter your response:\n");
    scanf("%s", response);
    send(clientsocket, response, sizeof(response), 0);

    close(serversocket);

    return 0;
}