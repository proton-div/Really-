# Really-
#define _CRT_SECURE_NO_WARNINGS // هذا السطر يمنع ظهور أي أسطر غريبة أو تحذيرات
#include <iostream>
#include <ctime>

using namespace std;

int main() {
    int year, month, day;

    cout << "Enter your birth year: ";
    cin >> year;
    cout << "Enter your birth month: ";
    cin >> month;
    cout << "Enter your birth day: ";
    cin >> day;

    // الطريقة العادية والمضمونة 100% مع g++
    time_t t = time(0);
    tm* now = localtime(&t);

    int current_year = now->tm_year + 1900;
    int current_month = now->tm_mon + 1;
    int current_day = now->tm_mday;

    // الحسبة الصحيحة والموجبة اللي أصلحناها معاً
    int date1 = current_year - year;
    int date2 = current_month - month;
    int date3 = current_day - day;

    if (date3 < 0) {
        date2--;
        date3 += 30;
    }
    if (date2 < 0) {
        date1--;
        date2 += 12;
    }

    cout << "\n================================" << endl;
    cout << "Your exact age is: " << endl;
    cout << "YEARS : " << date1 << " | MONTHS : " << date2 << " | DAYS : " << date3 << endl;
    cout << "================================" << endl;

    return 0;
}


