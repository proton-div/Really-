# Really-
#define _CRT_SECURE_NO_WARNINGS
#include <iostream>
#include <fstream>
#include <string>
#include <ctime>
#include <winsock2.h>

// ربط تلقائي للمكتبة لمنع الكلام الأحمر والأخطاء نهائياً
#pragma comment(lib, "ws2_32.lib") 

using namespace std;

// دالة قراءة ملف التصميم الخاص بك
string read_html_design() {
    ifstream file("inyear.html");
    if (!file.is_open()) return "<h1>Error: inyear.html not found! Put it in the same folder.</h1>";
    string str, content;
    while (getline(file, str)) content += str + "\n";
    return content;
}

// دالة استخراج الأرقام المرسلة من متصفح كروم
string get_value(const string& request, const string& key) {
    size_t pos = request.find(key + "=");
    if (pos == string::npos) return "0";
    pos += key.length() + 1;
    size_t end_pos = request.find("&", pos);
    if (end_pos == string::npos) end_pos = request.find(" ", pos);
    return request.substr(pos, end_pos - pos);
}

int main() {
    // تشغيل نظام الشبكات بويندوز
    WSADATA wsaData;
    if (WSAStartup(MAKEWORD(2, 2), &wsaData) != 0) return 1;

    SOCKET server = socket(AF_INET, SOCK_STREAM, 0);
    sockaddr_in addr;
    addr.sin_family = AF_INET;
    addr.sin_addr.s_addr = INADDR_ANY;
    addr.sin_port = htons(8080); // المنفذ 8080

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

        // إذا فتحت الرابط أول مرة: يعرض لك تصميمك المظهري
        if (request.find("GET / HTTP") != string::npos || request.find("GET /index.html") != string::npos) {
            response = "HTTP/1.1 200 OK\r\nContent-Type: text/html; charset=utf-8\r\n\r\n" + read_html_design();
        } 
        // إذا ضغطت على الزر داخل الـ HTML: يأخذ الأرقام ويحسبها بالـ C++
        else if (request.find("GET /calculate") != string::npos) {
            int day = stoi(get_value(request, "day"));
            int month = stoi(get_value(request, "month"));
            int year = stoi(get_value(request, "year"));

            // حساب الوقت الحالي بدقة
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

            // صفحة النتيجة المتناسقة مع مظهرك المظلم
            string res_page = "<html><body style='background:#0d1117; color:white; text-align:center; font-family:sans-serif; padding-top:100px;'>";
            res_page += "<h1 style='color:#39d353;'>🎉 عمرك الدقيق المحسوب بواسطة C++ هو:</h1>";
            res_page += "<h2>" + to_string(res_year) + " سنة، و " + to_string(res_month) + " شهر، و " + to_string(res_day) + " يوم!</h2>";
            res_page += "<br><br><a href='/' style='color:#39d353; font-size:18px; text-decoration:none; border:1px solid #39d353; padding:10px 20px; border-radius:5px;'>عودة للتصميم</a>";
            res_page += "</body></html>";

            response = "HTTP/1.1 200 OK\r\nContent-Type: text/html; charset=utf-8\r\n\r\n" + res_page;
        }

        send(client, response.c_str(), response.length(), 0);
        closesocket(client);
    }

    closesocket(server);
    WSACleanup();
    return 0;
}





g++ in_yerr.cpp -o in_yerr.exe -lws2_32

./in_yerr.exe
http://localhost:8080


