# Kadence-s-SnakeGame
This is my snake game that i talked with chatgpt to help me make.


#include "raylib.h"
#include <vector>

const int screenWidth = 800;
const int screenHeight = 600;
const int cellSize = 20;

struct SnakeSegment {
    int x, y;
};

enum Direction { UP, DOWN, LEFT, RIGHT };

bool PlaySnakeGame() {
    std::vector<SnakeSegment> snake = { {10, 10} };
    Direction dir = RIGHT;

    SnakeSegment food = {
        GetRandomValue(0, screenWidth / cellSize - 1),
        GetRandomValue(0, screenHeight / cellSize - 1)
    };

    int frames = 0;
    bool gameOver = false;

    while (!WindowShouldClose() && !gameOver) {
        // Input
        if (IsKeyPressed(KEY_UP) && dir != DOWN) dir = UP;
        if (IsKeyPressed(KEY_DOWN) && dir != UP) dir = DOWN;
        if (IsKeyPressed(KEY_LEFT) && dir != RIGHT) dir = LEFT;
        if (IsKeyPressed(KEY_RIGHT) && dir != LEFT) dir = RIGHT;

        // Movement every few frames (for speed control)
        if (++frames >= 10) {
            frames = 0;

            // Move snake
            SnakeSegment head = snake[0];
            switch (dir) {
            case UP: head.y--; break;
            case DOWN: head.y++; break;
            case LEFT: head.x--; break;
            case RIGHT: head.x++; break;
            }

            // Insert new head
            snake.insert(snake.begin(), head);

            // Eat food or move normally
            if (head.x == food.x && head.y == food.y) {
                food = {
                    GetRandomValue(0, screenWidth / cellSize - 1),
                    GetRandomValue(0, screenHeight / cellSize - 1)
                };
            }
            else {
                snake.pop_back();
            }

            // Check collision with wall
            if (head.x < 0 || head.x >= screenWidth / cellSize ||
                head.y < 0 || head.y >= screenHeight / cellSize) {
                gameOver = true;
            }

            // Check collision with self
            for (size_t i = 1; i < snake.size(); i++) {
                if (head.x == snake[i].x && head.y == snake[i].y) {
                    gameOver = true;
                }
            }
        }

        // Draw
        BeginDrawing();
        ClearBackground(DARKGREEN);

        // Draw food
        DrawRectangle(food.x * cellSize, food.y * cellSize, cellSize, cellSize, RED);

        // Draw snake
        for (const auto& segment : snake) {
            DrawRectangle(segment.x * cellSize, segment.y * cellSize, cellSize, cellSize, WHITE);
        }

        EndDrawing();
    }

    return !WindowShouldClose(); // Return true if user didn't close the window
}

int main() {
    InitWindow(screenWidth, screenHeight, "Snake Game");
    SetTargetFPS(60);

    while (true) {
        bool keepPlaying = PlaySnakeGame();

        if (!keepPlaying) break;

        // Show Game Over screen
        while (!WindowShouldClose()) {
            BeginDrawing();
            ClearBackground(BLACK);

            DrawText("Game Over!", screenWidth / 2 - 100, screenHeight / 2 - 60, 40, RED);
            DrawText("Press ENTER to play again", screenWidth / 2 - 150, screenHeight / 2, 20, LIGHTGRAY);
            DrawText("Press ESC to quit", screenWidth / 2 - 100, screenHeight / 2 + 40, 20, LIGHTGRAY);

            EndDrawing();

            if (IsKeyPressed(KEY_ENTER)) break;      // Restart game
            if (IsKeyPressed(KEY_ESCAPE)) goto exit; // Exit game
        }
    }

exit:
    CloseWindow();
    return 0;
}
