# Really-
#define _CRT_SECURE_NO_WARNINGS
#include <iostream>
#include <fstream>
#include <string>
#include <ctime>
#include <winsock2.h>

#pragma comment(lib, "ws2_32.lib") 

using namespace std;

// دالة قراءة ملف التصميم الخاص بك
string read_html_design() {
    ifstream file("inyear.html");
    if (!file.is_open()) return "<h1>Error: inyear.html not found!</h1>";
    string str, content;
    while (getline(file, str)) content += str + "\n";
    return content;
}

// الدالة القوية والشاملة لاستبدال النصوص في كل مكان بالملف
void replace_all(string& str, const string& from, const string& to) {
    size_t start_pos = 0;
    while ((start_pos = str.find(from, start_pos)) != string::npos) {
        str.replace(start_pos, from.length(), to);
        start_pos += to.length(); 
    }
}

// دالة استخراج الأرقام من المتصفح
string get_value(const string& request, const string& key) {
    size_t pos = request.find(key + "=");
    if (pos == string::npos) return "0";
    pos += key.length() + 1;
    size_t end_pos = request.find("&", pos);
    if (end_pos == string::npos) end_pos = request.find(" ", pos);
    return request.substr(pos, end_pos - pos);
}


<p align="center">
  <img class="ss" src="https://capsule-render.vercel.app/api?type=waving&color=0:3a1c71,50:d76d77,100:ffaf7b&height=300&section=header&text=Know%20your%20Age&fontSize=80&fontColor=ffffff" style="width: 100vw; height: 100vh; object-fit: cover;" />
</p>


int main() {
    WSADATA wsaData;
    if (WSAStartup(MAKEWORD(2, 2), &wsaData) != 0) return 1;

    SOCKET server = socket(AF_INET, SOCK_STREAM, 0);
    sockaddr_in addr;
    addr.sin_family = AF_INET;
    addr.sin_addr.s_addr = INADDR_ANY;
    addr.sin_port = htons(8080);

    bind(server, (struct sockaddr*)&addr, sizeof(addr));
    listen(server, 5);

    cout << "==========================================" << endl;
    cout << "[+] Server is RUNNING! Open: http://localhost:8080" << endl;
    cout << "==========================================" << endl;

    while (true) {
        SOCKET client = accept(server, nullptr, nullptr);
        if (client == INVALID_SOCKET) continue;

        char buffer[2048] = {0};
        recv(client, buffer, 2048, 0);
        string request(buffer);
        string response = "";

        // 1. عند فتح الصفحة لأول مرة (حالة الانتظار)
        if (request.find("GET / HTTP") != string::npos || request.find("GET /index.html") != string::npos) {
            string page = read_html_design();
            replace_all(page, "{{YEARS}}", "-");
            replace_all(page, "{{MONTHS}}", "-");
            replace_all(page, "{{DAYS}}", "-");
            response = "HTTP/1.1 200 OK\r\nContent-Type: text/html; charset=utf-8\r\n\r\n" + page;
        } 
        // 2. عند الضغط على الزر: الحساب الدقيق والفرش بالإنجليزية داخل تصميمك
        else if (request.find("GET /calculate") != string::npos) {
            int day = stoi(get_value(request, "day"));
            int month = stoi(get_value(request, "month"));
            int year = stoi(get_value(request, "year"));

            // حساب الوقت الحالي
            time_t t = time(0);
            struct tm* now = localtime(&t);
            int cur_year = now->tm_year + 1900;
            int cur_month = now->tm_mon + 1;
            int cur_day = now->tm_mday;

            int res_year = cur_year - year;
            int res_month = cur_month - month;
            int res_day = cur_day - day;

            if (res_day < 0) { res_month--; res_day += 30; }
            if (res_month < 0) { res_year--; res_month += 12; }

            // قراءة التصميم الأصلي النظيف
            string my_styled_page = read_html_design();
            
            // استبدال شامل لكل القيم بدون أي نقص
            replace_all(my_styled_page, "{{YEARS}}", to_string(res_year));
            replace_all(my_styled_page, "{{MONTHS}}", to_string(res_month));
            replace_all(my_styled_page, "{{DAYS}}", to_string(res_day));

            response = "HTTP/1.1 200 OK\r\nContent-Type: text/html; charset=utf-8\r\n\r\n" + my_styled_page;
        }

        send(client, response.c_str(), response.length(), 0);
        closesocket(client);
    }

    closesocket(server);
    WSACleanup();
    return 0;
}


replace_all(my_styled_page, "{{MONTHES}}", to_string(res_month));
