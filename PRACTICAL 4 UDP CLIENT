#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <netinet/in.h>
#include <sys/stat.h>

#define PORT 9146
#define BUF_SIZE 1024

int main() {
    int sockfd;
    struct sockaddr_in server;
    char buffer[BUF_SIZE];
    socklen_t len = sizeof(server);

    sockfd = socket(AF_INET, SOCK_DGRAM, 0);
    if (sockfd < 0) {
        perror("Socket creation failed");
        exit(EXIT_FAILURE);
    }

    memset(&server, 0, sizeof(server));
    server.sin_family = AF_INET;
    server.sin_port = htons(PORT);
    server.sin_addr.s_addr = INADDR_ANY;

    // 1. Ask for file name
    char filename[BUF_SIZE];
    printf("Enter filename to send: ");
    fgets(filename, BUF_SIZE, stdin);
    filename[strcspn(filename, "\n")] = 0; // remove newline

    FILE *fp = fopen(filename, "rb");
    if (!fp) {
        perror("File open failed");
        close(sockfd);
        return 1;
    }

    // 2. Get file size
    fseek(fp, 0, SEEK_END);
    int filesize = ftell(fp);
    fseek(fp, 0, SEEK_SET);

    // 3. Send filename
    sendto(sockfd, filename, strlen(filename) + 1, 0, (struct sockaddr *)&server, len); // include null terminator

    // 4. Send filesize
    sprintf(buffer, "%d", filesize);
    sendto(sockfd, buffer, strlen(buffer) + 1, 0, (struct sockaddr *)&server, len);

    // 5. Send file contents
    int sent = 0;
    while (!feof(fp)) {
        int n = fread(buffer, 1, BUF_SIZE, fp);
        sendto(sockfd, buffer, n, 0, (struct sockaddr *)&server, len);
        sent += n;
    }

    printf("File sent successfully (%d bytes).\n", sent);
    fclose(fp);
    close(sockfd);
    return 0;
}
