# Really-
#include <iostream>
#include <fstream>
#include <string>
#include <ctime>

using namespace std;

// دالة الحساب الدقيقة والموجبة التي أصلحناها معاً
void calculate_age(int day, int month, int year) {
    // الوقت الحالي تلقائياً
    time_t t = time(0);
    struct tm now;
    #if defined(_WIN32)
        localtime_s(&now, &t);
    #else
        now = *localtime(&t);
    #endif

    int current_year = now.tm_year + 1900;
    int current_month = now.tm_mon + 1;
    int current_day = now.tm_mday;

    int date1 = current_year - year;
    int date2 = current_month - month;
    int date3 = current_day - day;

    if (date3 < 0) { 
        date2 = date2 - 1; 
        date3 = date3 + 30; 
    }
    if (date2 < 0) { 
        date1 = date1 - 1; 
        date2 = date2 + 12; 
    }

    // كتابة النتيجة مباشرة داخل ملف الـ HTML ليقرأها المتصفح فوراً!
    ofstream file("inyear.html");
    if (file.is_open()) {
        file << "<html><body style='background:#0d1117; color:white; text-align:center; font-family:sans-serif; padding-top:100px;'>\n";
        file << "<h1 style='color:#39d353;'>🎉 Your Exact Age Calculated by C++:</h1>\n";
        file << "<h2>" << date1 << " Years, " << date2 << " Months, and " << date3 << " Days!</h2>\n";
        file << "</body></html>\n";
        file.close();
    }
}

int main() {
    int day, month, year;
    
    // يطلب منك الأرقام في الشاشة السوداء أولاً
    cout << "Enter your Birth Day: ";
    cin >> day;
    cout << "Enter your Birth Month: ";
    cin >> month;
    cout << "Enter your Birth Year: ";
    cin >> year;

    // تشغيل الحسبة وتعديل ملف الـ HTML
    calculate_age(day, month, year);

    cout << "\n[+] Success! C++ updated the HTML file." << endl;
    cout << "[+] Now just open or refresh 'inyear.html' in your browser!" << endl;

    return 0;
}
