#include <iostream>
#include <fstream>
#include <vector>
#include <cstdlib>
#include <cstring>
#include <windows.h>

using namespace std;

// Задать вопросы пользователю и сохранить ответы в файл
void askQuestions(vector<bool>& answers) {
    cout << "Ответьте на следующие вопросы (1 - Да, 0 - Нет):" << endl;
    for (int i = 0; i < 8; i++) {
        bool answer;
        cout << "Вопрос " << i + 1 << ": ";
        string input;
        cin >> input;
        while (input.length() != 1 || !isdigit(input[0]) || (input[0] != '0' && input[0] != '1')) {
            cout << "Неправильный ввод. Введите 1 для 'Да' или 0 для 'Нет': ";
            cin >> input;
        }
        answer = input[0] == '1';
        answers.push_back(answer);
    }

    ofstream file("answers.bin", ios::binary);
    for (bool answer : answers) {
        file.write(reinterpret_cast<const char*>(&answer), sizeof(answer));
    }
    file.close();
}

// Изменить ответы на вопросы
void changeAnswers(vector<bool>& answers) {
    cout << "Измените ответы на вопросы (1 - Да, 0 - Нет):" << endl;
    for (int i = 0; i < answers.size(); i++) {
        bool answer;
        cout << "Вопрос " << i + 1 << ": ";
        string input;
        cin >> input;
        while (input.length() != 1 || !isdigit(input[0]) || (input[0] != '0' && input[0] != '1')) {
            cout << "Неправильный ввод. Введите 1 для 'Да' или 0 для 'Нет': ";
            cin >> input;
        }
        answer = input[0] == '1';
        answers[i] = answer;
    }

    ofstream file("answers.bin", ios::binary);
    for (bool answer : answers) {
        file.write(reinterpret_cast<const char*>(&answer), sizeof(answer));
    }
    file.close();
}

// Читать ответы на вопросы из файла и вывести на экран
void readAnswers() {
    ifstream file("answers.bin", ios::binary);
    if (!file) {
        cout << "Файл с ответами не найден." << endl;
        return;
    }

    vector<bool> answers;
    bool answer;
    while (file.read(reinterpret_cast<char*>(&answer), sizeof(answer))) {
        answers.push_back(answer);
    }

    cout << "Ответы на вопросы: " << endl;
    for (int i = 0; i < answers.size(); i++) {
        cout << "Вопрос " << i + 1 << ": " << answers[i] << endl;
    }

    file.close();
}

int main() {
    SetConsoleCP(1251);
    SetConsoleOutputCP(1251);
    vector<bool> answers;
    askQuestions(answers);
    readAnswers();

    char choice;
    cout << "Хотите изменить ответы? (y/n): ";
    cin >> choice;
    if (choice == 'y' || choice == 'Y') {
        changeAnswers(answers);
        readAnswers();
    }

    return 0;
}
