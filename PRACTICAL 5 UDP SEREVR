#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <netinet/in.h>

#define PORT 9146
#define BUF_SIZE 1024

int main() {
    int sockfd;
    struct sockaddr_in server, client;
    char buffer[BUF_SIZE];
    socklen_t len = sizeof(client);

    sockfd = socket(AF_INET, SOCK_DGRAM, 0);
    if (sockfd < 0) {
        perror("Socket creation failed");
        exit(EXIT_FAILURE);
    }

    memset(&server, 0, sizeof(server));
    server.sin_family = AF_INET;
    server.sin_port = htons(PORT);
    server.sin_addr.s_addr = INADDR_ANY;

    if (bind(sockfd, (struct sockaddr *)&server, sizeof(server)) < 0) {
        perror("Bind failed");
        close(sockfd);
        exit(EXIT_FAILURE);
    }

    printf("UDP File Server listening on port %d...\n", PORT);

    // 1. Receive filename
    memset(buffer, 0, BUF_SIZE);
    recvfrom(sockfd, buffer, BUF_SIZE, 0, (struct sockaddr *)&client, &len);
    char filename[BUF_SIZE];
    strncpy(filename, buffer, BUF_SIZE);
    printf("Receiving file: '%s'\n", filename);

    // 2. Receive filesize
    memset(buffer, 0, BUF_SIZE);
    recvfrom(sockfd, buffer, BUF_SIZE, 0, (struct sockaddr *)&client, &len);
    int filesize = atoi(buffer);
    printf("Expected file size: %d bytes\n", filesize);

    // 3. Open file
    FILE *fp = fopen(filename, "wb");
    if (!fp) {
        perror("File open failed");
        close(sockfd);
        return 1;
    }

    // 4. Receive file contents
    int received = 0;
    while (received < filesize) {
        memset(buffer, 0, BUF_SIZE);
        int n = recvfrom(sockfd, buffer, BUF_SIZE, 0, (struct sockaddr *)&client, &len);
        fwrite(buffer, 1, n, fp);
        received += n;
    }

    printf("File received successfully (%d bytes).\n", received);
    fclose(fp);
    close(sockfd);
    return 0;
}
