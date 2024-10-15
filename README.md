# necoarc

661201

#include <SDL.h>
#include <iostream>
using namespace std;

int main(int argc, char* argv[]) {
    // Инициализация SDL
    if (SDL_Init(SDL_INIT_VIDEO) < 0) {
        std::cout << "Ошибка инициализации SDL: " << SDL_GetError() << std::endl;
        return -1;
    }

    // Создаем окно
    SDL_Window* window = SDL_CreateWindow("Простая игра на SDL2",
        SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED,
        800, 600, SDL_WINDOW_SHOWN);
    if (!window) {
        std::cout << "Ошибка создания окна: " << SDL_GetError() << std::endl;
        SDL_Quit();
        return -1;
    }

    // Создаем рендер
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, SDL_RENDERER_ACCELERATED);
    if (!renderer) {
        std::cout << "Ошибка создания рендера: " << SDL_GetError() << std::endl;
        SDL_DestroyWindow(window);
        SDL_Quit();
        return -1;
    }

    // Загрузка изображения персонажа
    SDL_Surface* tempSurface = SDL_LoadBMP("character.bmp");
    if (!tempSurface) {
        cout << "Ошибка загрузки изображения: " << SDL_GetError() << endl;
        SDL_DestroyRenderer(renderer);
        SDL_DestroyWindow(window);
        SDL_Quit();
        return -1;
    }
    SDL_Texture* characterTexture = SDL_CreateTextureFromSurface(renderer, tempSurface);
    SDL_FreeSurface(tempSurface);

    SDL_Rect characterRect = { 100, 100, 64, 64 }; // Положение и размер персонажа
    const int speed = 5; // Скорость перемещения

    // Основной цикл игры
    bool isRunning = true;
    SDL_Event event;

    while (isRunning) {
        // Обрабатываем события
        while (SDL_PollEvent(&event)) {
            if (event.type == SDL_QUIT) {
                isRunning = false;
            }
        }

        // Получение состояния клавиш
        const Uint8* keystate = SDL_GetKeyboardState(NULL);

        // Обработка нажатий клавиш WASD
        if (keystate[SDL_SCANCODE_W]) {
            characterRect.y -= speed;
        }
        if (keystate[SDL_SCANCODE_S]) {
            characterRect.y += speed;
        }
        if (keystate[SDL_SCANCODE_A]) {
            characterRect.x -= speed;
        }
        if (keystate[SDL_SCANCODE_D]) {
            characterRect.x += speed;
        }

        // Очищаем экран
        SDL_SetRenderDrawColor(renderer, 0, 0, 0, 255); // Черный фон
        SDL_RenderClear(renderer);

        // Отрисовка персонажа
        SDL_RenderCopy(renderer, characterTexture, NULL, &characterRect);

        // Обновляем экран
        SDL_RenderPresent(renderer);
    }

    // Очищаем ресурсы
    SDL_DestroyTexture(characterTexture);
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();

    return 0;
}
