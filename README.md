#include <iostream>
#include <vector>
#include <cstdlib>
#include <ctime>

using namespace std;

const int WIDTH = 9;
const int HEIGHT = 9;
const int NUM_BOMBS = 10;

struct Cell {
    bool isBomb = false;
    bool isRevealed = false;
    int surroundingBombs = 0;
};

vector<vector<Cell>> board(HEIGHT, vector<Cell>(WIDTH));
bool gameOver = false;

// 方向ベクトル（8方向）
const int dx[8] = { -1, -1, -1,  0, 1, 1, 1,  0 };
const int dy[8] = { -1,  0,  1,  1, 1, 0, -1, -1 };

void placeBombs(int firstY, int firstX) {
    int bombsPlaced = 0;
    while (bombsPlaced < NUM_BOMBS) {
        int x = rand() % WIDTH;
        int y = rand() % HEIGHT;

        // 最初に選んだマスとその周囲には爆弾を置かない
        bool nearFirstClick = (abs(x - firstX) <= 1 && abs(y - firstY) <= 1);
        if (!board[y][x].isBomb && !nearFirstClick) {
            board[y][x].isBomb = true;
            bombsPlaced++;
        }
    }

    // 爆弾の周囲の数を設定
    for (int y = 0; y < HEIGHT; ++y) {
        for (int x = 0; x < WIDTH; ++x) {
            if (board[y][x].isBomb) continue;
            int count = 0;
            for (int d = 0; d < 8; ++d) {
                int ny = y + dy[d], nx = x + dx[d];
                if (ny >= 0 && ny < HEIGHT && nx >= 0 && nx < WIDTH) {
                    if (board[ny][nx].isBomb) count++;
                }
            }
            board[y][x].surroundingBombs = count;
        }
    }
}

void reveal(int y, int x) {
    if (y < 0 || y >= HEIGHT || x < 0 || x >= WIDTH) return;
    if (board[y][x].isRevealed) return;

    board[y][x].isRevealed = true;

    if (board[y][x].surroundingBombs == 0 && !board[y][x].isBomb) {
        for (int d = 0; d < 8; ++d) {
            reveal(y + dy[d], x + dx[d]);
        }
    }
}

void printBoard(bool revealAll = false) {
    cout << "\n   ";
    for (int x = 0; x < WIDTH; ++x) cout << x << " ";
    cout << endl;

    for (int y = 0; y < HEIGHT; ++y) {
        cout << y << " |";
        for (int x = 0; x < WIDTH; ++x) {
            if (revealAll) {
                if (board[y][x].isBomb) cout << "B ";
                else cout << board[y][x].surroundingBombs << " ";
            } else {
                if (!board[y][x].isRevealed) cout << "* ";
                else if (board[y][x].isBomb) cout << "B ";
                else cout << board[y][x].surroundingBombs << " ";
            }
        }
        cout << endl;
    }
}

bool checkWin() {
    for (int y = 0; y < HEIGHT; ++y) {
        for (int x = 0; x < WIDTH; ++x) {
            if (!board[y][x].isBomb && !board[y][x].isRevealed) {
                return false;
            }
        }
    }
    return true;
}

int main() {
    srand(static_cast<unsigned int>(time(0)));
    cout << "=== マインスイーパー（難易度：中）===\n";

    int firstY, firstX;
    printBoard();

    // 初手
    cout << "最初のマスを選んでください (y x): ";
    cin >> firstY >> firstX;

    placeBombs(firstY, firstX);
    reveal(firstY, firstX);

    while (!gameOver) {
        printBoard();
        int y, x;
        cout << "開くマスを入力 (y x): ";
        cin >> y >> x;

        if (y < 0 || y >= HEIGHT || x < 0 || x >= WIDTH) {
            cout << "範囲外です。\n";
            continue;
        }

        if (board[y][x].isBomb) {
            cout << "爆弾でした！ゲームオーバー！\n";
            gameOver = true;
            printBoard(true); // 全表示
            break;
        }

        reveal(y, x);

        if (checkWin()) {
            cout << "おめでとうございます！すべての安全マスを開きました！\n";
            printBoard(true);
            break;
        }
    }

    return 0;
}
