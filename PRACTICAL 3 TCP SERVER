//  TCP FILE TRANSFER CODE FOR SERVER
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <netinet/in.h>
#include <arpa/inet.h>

#define PORT 10035
#define BUF_SIZE 1024

int main() {
    int server_fd, new_socket;
    struct sockaddr_in address;
    int addrlen = sizeof(address);
    char buffer[BUF_SIZE];

    if ((server_fd = socket(AF_INET, SOCK_STREAM, 0)) < 0) {
        perror("Socket creation failed");
        exit(EXIT_FAILURE);
    }

    address.sin_family = AF_INET;
    address.sin_addr.s_addr = INADDR_ANY;
    address.sin_port = htons(PORT);

    if (bind(server_fd, (struct sockaddr *)&address, sizeof(address)) < 0) {
        perror("Bind failed");
        close(server_fd);
        exit(EXIT_FAILURE);
    }

    if (listen(server_fd, 3) < 0) {
        perror("Listen failed");
        close(server_fd);
        exit(EXIT_FAILURE);
    }
    printf("TCP File Server listening on port %d...\n", PORT);

    if ((new_socket = accept(server_fd, (struct sockaddr *)&address,
                             (socklen_t *)&addrlen)) < 0) {
        perror("Accept failed");
        close(server_fd);
        exit(EXIT_FAILURE);
    }

    memset(buffer, 0, BUF_SIZE);
    int bytes_received = recv(new_socket, buffer, BUF_SIZE, 0);
    if (bytes_received <= 0) {
        perror("Filename receive failed");
        close(new_socket);
        close(server_fd);
        exit(EXIT_FAILURE);
    }
    buffer[bytes_received] = '\0';
    printf("Receiving file: %s\n", buffer);

    FILE *fp = fopen(buffer, "wb");
    if (fp == NULL) {
        perror("File creation failed");
        close(new_socket);
        close(server_fd);
        exit(EXIT_FAILURE);
    }

    while ((bytes_received = recv(new_socket, buffer, BUF_SIZE, 0)) > 0) {
        fwrite(buffer, 1, bytes_received, fp);
    }

    printf("File received successfully!\n");

    fclose(fp);
    close(new_socket);
    close(server_fd);
    return 0;
}
