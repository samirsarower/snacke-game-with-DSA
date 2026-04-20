#include <bits/stdc++.h>
#include <conio.h>
#include <windows.h>
using namespace std;

struct Node
{
    int row, col;
    Node *next;

    Node(int r, int c, Node *nxt = nullptr)
    {
        row = r;
        col = c;
        next = nxt;
    }
};

class Snake
{
public:
    Node *head;
    int length;
    int lvl = 1;
    map<int, set<pair<int, int>>> levelWalls;

    Snake(int startRow, int startCol)
    {
        head = new Node(startRow, startCol);
        length = 1;
    }

    ~Snake()
    {
        Node *current = head;
        while (current)
        {
            Node *next = current->next;
            delete current;
            current = next;
        }
    }

    void setupLevels()
    {
        set<pair<int, int>> lvl1;

        for (int i = 0; i < 20; i++)
        {
            if ((i >= 5 && i <= 8) || (i >= 12 && i <= 15))
                continue;

            lvl1.insert({i, 0});
            lvl1.insert({i, 19});
            lvl1.insert({0, i});
            lvl1.insert({19, i});
        }
        levelWalls[1] = lvl1;

        set<pair<int, int>> lvl2;
        for (int i = 0; i < 20; i++)
        {
            lvl2.insert({i, 0});
            lvl2.insert({i, 19});
            lvl2.insert({0, i});
            lvl2.insert({19, i});
        }
        for (int i = 2; i < 18; i += 3)
        {
            for (int j = 2; j < 18; j += 3)
            {
                if (i > 4 && i < 15 && j > 4 && j < 15)
                {
                    lvl2.insert({i, j});
                    lvl2.insert({i + 1, j});
                    lvl2.insert({i, j + 1});
                }
            }
        }

        levelWalls[2] = lvl2;
        set<pair<int, int>> lvl3;

        for (int i = 0; i < 20; i++)
        {
            if ((i >= 5 && i <= 8) || (i >= 12 && i <= 15))
                continue;

            lvl3.insert({i, 0});
            lvl3.insert({i, 19});
            lvl3.insert({0, i});
            lvl3.insert({19, i});
        }
        set<int> num;
        num.insert(19 * 3 + 7);
        num.insert(19 * 4 + 6);
        num.insert(19 * 5 + 5);

        num.insert(19 * 3 + 12);
        num.insert(19 * 4 + 13);
        num.insert(19 * 5 + 14);

        num.insert(19 * 15 + 5);
        num.insert(19 * 16 + 6);
        num.insert(19 * 17 + 7);

        num.insert(19 * 15 + 14);
        num.insert(19 * 16 + 13);
        num.insert(19 * 17 + 12);
        for (int i = 0; i < 20; i++)
        {
            for (int j = 0; j < 20; j++)
            {
                if (i == 10)
                {

                    if (j <= 4 || j >= 16)
                        lvl3.insert({i, j});
                }
                if (i >= 5 && i <= 8 || i >= 12 && i <= 15)
                {
                    if (j == 3 || j == 16)
                        lvl3.insert({i, j});
                }
                if (j >= 7 && j <= 13)
                {
                    if (i == 8 || i == 12)
                        lvl3.insert({i, j});
                }
                if (num.count(i * 19 + j))
                    lvl3.insert({i, j});
            }
        }

        levelWalls[3] = lvl3;

        set<pair<int, int>> lvl4;
        for (int i = 0; i < 20; i++)
        {
            for (int j = 0; j < 20; j++)
            {
                if (i == 8 || i == 12)
                {
                    if (j >= 6 && j <= 14)
                        lvl4.insert({i, j});
                }
                if (j == 10)
                {
                    if ((i >= 4 && i <= 8) || (i >= 12 && i <= 16))
                        lvl4.insert({i, j});
                }
            }
        }
        for (int i = 0; i < 20; i++)
        {
            if ((i >= 5 && i <= 8) || (i >= 12 && i <= 15))
                continue;

            lvl4.insert({i, 0});
            lvl4.insert({i, 19});
            lvl4.insert({0, i});
            lvl4.insert({19, i});
        }

        for (int i = 0; i < 20; i++)
        {
            for (int j = 0; j < 20; j++)
            {
                if (i == 8 || i == 12)
                {
                    if (j >= 6 && j <= 14)
                        lvl4.insert({i, j});
                }
                if (j == 10)
                {
                    if ((i >= 4 && i <= 8) || (i >= 12 && i <= 16))
                        lvl4.insert({i, j});
                }
            }
        }
        levelWalls[4] = lvl4;
    }

    bool isWall(int level, int row, int col)
    {
        return levelWalls[level].count({row, col});
    }

    void move(int dRow, int dCol, bool grow)
    {
        Node *newHead = new Node(head->row + dRow, head->col + dCol, head);
        head = newHead;

        if (head->col < 0)
            head->col = 19;
        if (head->row < 0)
            head->row = 19;
        if (head->col >= 20)
            head->col = 0;
        if (head->row >= 20)
            head->row = 0;

        if (!grow)
        {
            Node *temp = head;
            while (temp->next && temp->next->next)
                temp = temp->next;

            delete temp->next;
            temp->next = nullptr;
        }
        else
        {
            length++;
        }
    }

    bool checkSelfCollision()
    {
        int r = head->row, c = head->col;
        Node *temp = head->next;

        while (temp)
        {
            if (temp->row == r && temp->col == c)
                return true;
            temp = temp->next;
        }
        return false;
    }

    bool isPositionOnSnake(int row, int col)
    {
        Node *temp = head;
        while (temp)
        {
            if (temp->row == row && temp->col == col)
                return true;
            temp = temp->next;
        }
        return false;
    }

    void viewmaps()
    {
        for (auto it : levelWalls)
        {
            cout << "Level " << it.first << " Map:" << endl;
            for (int i = 0; i < 20; i++)
            {
                for (int j = 0; j < 20; j++)
                {
                    if (it.second.count({i, j}))
                        cout << "# ";
                    else
                        cout << "  ";
                }
                cout << endl;
            }
            cout << endl;
        }
        cout << "......................................................................................" << endl
             << endl;
    }
};

void gotoxy(int x, int y)
{
    COORD coord = {(SHORT)x, (SHORT)y};
    SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), coord);
}

void hideCursor()
{
    CONSOLE_CURSOR_INFO info;
    GetConsoleCursorInfo(GetStdHandle(STD_OUTPUT_HANDLE), &info);
    info.bVisible = false;
    SetConsoleCursorInfo(GetStdHandle(STD_OUTPUT_HANDLE), &info);
}

void showCursor()
{
    CONSOLE_CURSOR_INFO info;
    GetConsoleCursorInfo(GetStdHandle(STD_OUTPUT_HANDLE), &info);
    info.bVisible = true;
    SetConsoleCursorInfo(GetStdHandle(STD_OUTPUT_HANDLE), &info);
}

void drawGame(Snake &snake, int foodRow, int foodCol, int rows, int cols, int score)
{
    for (int i = 0; i < rows; i++)
    {
        gotoxy(1, i + 1);
        for (int j = 0; j < cols; j++)
        {
            if (i == foodRow && j == foodCol)
                cout << "F ";
            else if (snake.isPositionOnSnake(i, j))
                cout << "O ";
            else if (snake.isWall(snake.lvl, i, j))
                cout << "# ";
            else
                cout << "  ";
        }
    }

    gotoxy(0, rows + 2);
    cout << "Score: " << score;
    gotoxy(0, rows + 3);
    cout << "Length: " << snake.length;
    gotoxy(0, rows + 4);
    cout << "Level: " << snake.lvl;
}

void generateFood(int &r, int &c, Snake &snake, int rows, int cols)
{
    do
    {
        r = rand() % rows;
        c = rand() % cols;
    } while (snake.isPositionOnSnake(r, c) || snake.isWall(snake.lvl, r, c));
}

bool valid(char n, char c)
{
    return !((c == 'w' && n == 's') ||
             (c == 's' && n == 'w') ||
             (c == 'a' && n == 'd') ||
             (c == 'd' && n == 'a'));
}

void play(Snake &snake, int speed)
{
    const int rows = 20, cols = 20;

    snake.head->next = new Node(rows / 2, cols / 2 - 1);
    snake.head->next->next = new Node(rows / 2, cols / 2 - 2);
    snake.length = 3;

    srand(time(0));

    int foodRow, foodCol;
    generateFood(foodRow, foodCol, snake, rows, cols);

    char dir = 'd', nextDir = 'd';
    int score = 0;
    bool run = true;

    system("cls");
    hideCursor();

    while (run)
    {
        if (_kbhit())
        {
            char ch = tolower(_getch());
            if ((ch == 'w' || ch == 'a' || ch == 's' || ch == 'd') && valid(ch, dir))
                nextDir = ch;
            else if (ch == 27)
                break;
        }

        dir = nextDir;

        int dr = 0, dc = 0;
        if (dir == 'w')
            dr = -1;
        if (dir == 's')
            dr = 1;
        if (dir == 'a')
            dc = -1;
        if (dir == 'd')
            dc = 1;

        int nr = snake.head->row + dr;
        int nc = snake.head->col + dc;

        if (nr < 0)
            nr = 19;
        if (nr >= rows)
            nr = 0;
        if (nc < 0)
            nc = 19;
        if (nc >= cols)
            nc = 0;

        bool grow = (nr == foodRow && nc == foodCol);

        snake.move(dr, dc, grow);

        if (snake.isWall(snake.lvl, snake.head->row, snake.head->col) ||
            snake.checkSelfCollision())
        {
            run = false;
            break;
        }

        if (grow)
        {
            score += 10;
            speed = max(60, speed - 2);
            generateFood(foodRow, foodCol, snake, rows, cols);
        }

        drawGame(snake, foodRow, foodCol, rows, cols, score);

        Sleep(speed);
    }

    showCursor();
    system("cls");

    cout << "\n\nGAME OVER\n";
    cout << "Score: " << score << endl;
    cout << "Length: " << snake.length << endl;
    cout << "Level: " << snake.lvl << endl;

    _getch();
}

int main()
{
    const int rows = 20, cols = 20;
    int speed;

    Snake snake(rows / 2, cols / 2);
    snake.setupLevels();

    int x, diff;

    cout << "=================================\n";
    cout << "          SNAKE GAME            \n";
    cout << "=================================\n\n";

    cout << "  Select Map Level\n";
    cout << "-----------------------------\n";
    snake.viewmaps();

    cout << "\nEnter Level (1 / 2 / 3 / 4): ";
    cin >> x;
    snake.lvl = x;

    cout << "\n  Difficulty Settings\n";
    cout << "-----------------------------\n";
    cout << "1. Easy   (Slow)\n";
    cout << "2. Medium (Normal)\n";
    cout << "3. Hard   (Fast)\n";

    cout << "\nEnter Choice: ";
    cin >> diff;

    if (diff == 1)
        speed = 200;
    else if (diff == 2)
        speed = 100;
    else
        speed = 50;

    cout << "\n  Starting Game...\n";
    cout << "=================================\n";

    Sleep(1000);
    system("cls");

    play(snake, speed);

    return 0;
}
