# Really-
#include <iostream>
#include <fstream>
#include <string>
#include <ctime>
#include <winsock2.h>

#pragma comment(lib, "ws2_32.lib")

using namespace std;

// دالة قراءة ملف الـ HTML
string read_html_file() {
    ifstream file("inyear.html");
    if (!file.is_open()) return "<h1>Error: inyear.html not found! Place it in the same folder as C++ file.</h1>";
    string str, file_contents;
    while (getline(file, str)) { file_contents += str + "\n"; }
    return file_contents;
}

// دالة استخراج الأرقام من المتصفح
string get_param(const string& query, const string& key) {
    size_t pos = query.find(key + "=");
    if (pos == string::npos) return "0";
    pos += key.length() + 1;
    size_t end_pos = query.find("&", pos);
    return query.substr(pos, end_pos - pos);
}

int main() {
    WSADATA wsaData;
    if (WSAStartup(MAKEWORD(2, 2), &wsaData) != 0) return 1;

    SOCKET server_fd = socket(AF_INET, SOCK_STREAM, 0);
    sockaddr_in address;
    address.sin_family = AF_INET;
    address.sin_addr.s_addr = INADDR_ANY;
    address.sin_port = htons(8080);

    bind(server_fd, (struct sockaddr*)&address, sizeof(address));
    listen(server_fd, 3);

    cout << "=======================================" << endl;
    cout << "Server started! Open: http://localhost:8080" << endl;
    cout << "=======================================" << endl;

    while (true) {
        SOCKET new_socket = accept(server_fd, nullptr, nullptr);
        if (new_socket == INVALID_SOCKET) continue;

        char buffer[2048] = {0};
        recv(new_socket, buffer, 2048, 0);

        string request(buffer);
        string response = "";

        if (request.find("GET / HTTP") != string::npos) {
            response = "HTTP/1.1 200 OK\r\nContent-Type: text/html; charset=utf-8\r\n\r\n" + read_html_file();
        } 
        else if (request.find("GET /calculate") != string::npos) {
            int day = stoi(get_param(request, "day"));
            int month = stoi(get_param(request, "month"));
            int year = stoi(get_param(request, "year"));

            // الطريقة الآمنة لويندوز لمنع الأسطر الغريبة بالـ Terminal
            time_t t = time(0);
            tm now;
            localtime_s(&now, &t);
            int current_year = now.tm_year + 1900;
            int current_month = now.tm_mon + 1;
            int current_day = now.tm_mday;

            int date1 = current_year - year;
            int date2 = current_month - month;
            int date3 = current_day - day;

            if (date3 < 0) { date2--; date3 += 30; }
            if (date2 < 0) { date1--; date2 += 12; }

            string res_page = "<html><body style='background:#0d1117; color:white; text-align:center; font-family:sans-serif; padding-top:100px;'>";
            res_page += "<h1 style='color:#39d353;'>🎉 Your Exact Age is:</h1>";
            res_page += "<h2>" + to_string(date1) + " Years, " + to_string(date2) + " Months, and " + to_string(date3) + " Days!</h2>";
            res_page += "<br><br><a href='/' style='color:#39d353; font-size:18px; text-decoration:none; border:1px solid #39d353; padding:10px 20px; border-radius:5px;'>Calculate Again</a>";
            res_page += "</body></html>";

            response = "HTTP/1.1 200 OK\r\nContent-Type: text/html; charset=utf-8\r\n\r\n" + res_page;
        }

        send(new_socket, response.c_str(), response.length(), 0);
        closesocket(new_socket);
    }

    closesocket(server_fd);
    WSACleanup();
    return 0;
}

