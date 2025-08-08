#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
#include <iterator>
#include <ctime>
#include <cstdlib>
#include <chrono>

using namespace std;
using namespace chrono;

// 単語リスト作成
vector<string> createWordList() {
    vector<string> words = {
        "computer", "algorithm", "keyboard", "performance",
        "iteration", "compile", "function", "developer",
        "programming", "difficulty", "innovation", "accuracy"
    };
    return words;
}

// ゲーム開始関数
void startGame() {
    vector<string> words = createWordList();
    int score = 0;

    cout << "=== シンプルタイピングゲーム ===\n";
    cout << "制限時間内に正しく入力してください！\n\n";

    srand((unsigned)time(0));
    random_shuffle(words.begin(), words.end());

    for (auto it = words.begin(); it != words.end(); ++it) {
        string target = *it;

        cout << "単語: " << target << endl;
        cout << "入力: ";

        auto startTime = steady_clock::now();
        string input;
        cin >> input;
        auto endTime = steady_clock::now();

        double elapsed = duration_cast<seconds>(endTime - startTime).count();

        if (elapsed > 5) { // 制限時間5秒
            cout << "時間切れ！ (" << elapsed << "秒)\n";
            score -= 5;
            continue;
        }

        if (input == target) {
            cout << "正解！ (" << elapsed << "秒)\n";
            score += 10 - elapsed;
        } else {
            cout << "不正解！ 正しい答えは: " << target << endl;
            score -= 3;
        }
    }

    cout << "\n=== ゲーム終了 ===\n";
    cout << "最終スコア: " << score << endl;
}

int main() {
    startGame();
    return 0;
}
