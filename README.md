# Really-
#include <iostream>
#include <fstream>
#include <string>
#include <ctime>
#include <winsock2.h>

#pragma comment(lib, "ws2_32.lib")

using namespace std;

// هذه الدالة تقرأ ملف الـ HTML وتجهزه للارسال
string get_html() {
    ifstream file("inyear.html");
    string content, line;
    while(getline(file, line)) content += line;
    return content;
}

int main() {
    WSADATA wsaData;
    WSAStartup(MAKEWORD(2,2), &wsaData);
    SOCKET s = socket(AF_INET, SOCK_STREAM, 0);
    sockaddr_in addr;
    addr.sin_family = AF_INET;
    addr.sin_addr.s_addr = INADDR_ANY;
    addr.sin_port = htons(8080);
    bind(s, (sockaddr*)&addr, sizeof(addr));
    listen(s, 5);

    cout << "السيرفر يعمل الآن! افتح المتصفح على: http://localhost:8080" << endl;

    while(true) {
        SOCKET client = accept(s, NULL, NULL);
        char buffer[1024] = {0};
        recv(client, buffer, 1024, 0);
        
        string response;
        if (strstr(buffer, "GET /calculate")) {
            // هنا الحسبة (بسيطة جداً بدون جافا سكريبت)
            response = "HTTP/1.1 200 OK\r\nContent-Type: text/html\r\n\r\n<h1>تمت الحسبة بنجاح عبر C++</h1>";
        } else {
            response = "HTTP/1.1 200 OK\r\nContent-Type: text/html\r\n\r\n" + get_html();
        }
        send(client, response.c_str(), response.length(), 0);
        closesocket(client);
    }
    return 0;
}
