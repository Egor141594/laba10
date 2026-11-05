# Домашнее задание к работе 10

## Условие задачи

Создать модульную программу с пользовательскими функциями: вычисление составных функций $f_a(x)$ и $f_b(x)$ с интерактивным меню операций, вычисление натурального логарифма $\ln(x)$ с помощью бесконечного ряда (по числу членов $N$ и по заданной точности $\epsilon$) с выводом таблицы сходимости, а также геометрические задачи с параллелограммом (расчет площади, вывод определения, отрисовка фигурного параллелограмма).

## Алгоритм и блок-схема

### Алгоритм

1. Начало
2. Объявление прототипов функций `fa`, `fb`, `ln_n`, `ln_eps`, `area_parallelogram`, `print_definition`, `draw_parallelogram`.
3. Функция `task_one`: ввод $x$, вычисление значений функций и выбор операции через меню (вычисление каждой, произведения, разности квадратов, удвоенной суммы).
4. Функция `task_two`:
   - Ввод $x > 0$, числа членов $N$ и точности $\epsilon$.
   - Расчет $\ln(x)$ через библиотечную функцию `log(x)` и с помощью разработанных функций рядов.
   - Вывод результатов и таблицы сходимости с погрешностями.
5. Функция `task_three`: меню работы с параллелограммом (расчет площади, вывод определения, отрисовка фигуры со смещением и символом).
6. Функция `main`: бесконечный цикл работы с возможностью выхода по клавише `n`.
7. Конец

### Блок-схема

<img width="1024" height="733" alt="image" src="https://github.com/user-attachments/assets/b6305cc9-31a9-4479-9b54-b65405c3fe9a" />


## Реализация программы

```c
#include <stdio.h>
#include <stdlib.h>
#include <locale.h>
#include <math.h>

double fa(double x);
double fb(double x);
int task_one();
double ln_n(double x, int N);
double ln_eps(double x, double eps);
int task_two();
float area_parallelogram(float a, float h);
void print_definition();
void draw_parallelogram(int a, int h, int shift, char symbol);
int task_three();

void main() {
    while (1) {
        char a;
        setlocale(LC_ALL, "RUS");
        task_one();
        task_two();
        task_three();
        printf("Continue? (y/n): ");
        scanf_s(" %c", &a, 1);
        if (a == 'n') break;
    }
}

double fa(double x) {
    double y;
    if (x <= 3.0) {
        y = x - x * 3.0 * x + 9.0;
    }
    else {
        y = 1.0 / (pow(x, 3.0) + 3.0);
    }
    return y;
}

double fb(double x) {
    double y;
    y = x * exp(sin(x * x));
    return y;
}

int task_one() {
    puts("TASK ONE----------------------------------------------------------------------------------------------------------------");
    double x;
    int choice;
    printf("Enter the value of x: ");
    scanf_s("%lf", &x);
    printf("\nFunction values at x = %lf:\n", x);
    printf("fa(x) = %lf, fb(x) = %lf\n\n", fa(x), fb(x));
    printf("Select an operation:\n");
    printf("1 - Calculate only fa(x)\n");
    printf("2 - Calculate only fb(x)\n");
    printf("3 - Product fa(x) * fb(x)\n");
    printf("4 - Difference of squares fa(x)^2 - fb(x)^2\n");
    printf("5 - Doubled sum 2 * (fa(x) + fb(x))\n");
    printf("6 - Output everything at once\n");
    printf("Your choice: ");
    scanf_s("%d", &choice);
    double val_a = fa(x);
    double val_b = fb(x);
    if (choice == 1) printf("fa(x) = %lf\n", val_a);
    else if (choice == 2) printf("fb(x) = %lf\n", val_b);
    else if (choice == 3) printf("Product: %lf\n", val_a * val_b);
    else if (choice == 4) printf("Difference of squares: %lf\n", (val_a * val_a) - (val_b * val_b));
    else if (choice == 5) printf("Doubled sum: %lf\n", 2.0 * (val_a + val_b));
    else if (choice == 6) printf("Product: %lf, difference of squares: %lf, doubled sum: %lf\n", val_a * val_b, (val_a * val_a) - (val_b * val_b), 2.0 * (val_a + val_b));
    else printf("Invalid menu item!\n");
    return 0;
}

double ln_n(double x, int N) {
    double t = (x - 1.0) / (x + 1.0);
    double t_pow = t;
    double sum = 0.0;
    for (int n = 0; n < N; n++) {
        sum += (1.0 / (2 * n + 1)) * t_pow;
        t_pow *= t * t;
    }
    return 2.0 * sum;
}

double ln_eps(double x, double eps) {
    double t = (x - 1.0) / (x + 1.0);
    double t_pow = t;
    double sum = 0.0;
    int n = 0;
    double term = 2.0 * (1.0 / (2 * n + 1)) * t_pow;
    while (fabs(term) > eps) {
        sum += term;
        n++;
        t_pow *= t * t;
        term = 2.0 * (1.0 / (2 * n + 1)) * t_pow;
    }
    return sum;
}

int task_two() {
    puts("TASK TWO----------------------------------------------------------------------------------------------------------------");
    double x, eps;
    int N;
    printf("Enter x to calculate ln(x) (x > 0): ");
    scanf_s("%lf", &x);
    if (x <= 0) { printf("Error! x must be greater than 0.\n"); return 0; }
    printf("Enter the number of terms N: ");
    scanf_s("%d", &N);
    printf("Enter accuracy eps (e.g., 0.0001): ");
    scanf_s("%lf", &eps);
    double math_res = log(x);
    double n_res = ln_n(x, N);
    double eps_res = ln_eps(x, eps);
    puts("\nCalculation results:");
    printf("1) Via log(x) from math.h:       %.10lf\n", math_res);
    printf("2) Via sum of %d series terms:   %.10lf (deviation: %e)\n", N, n_res, fabs(math_res - n_res));
    printf("3) By series with accuracy %lf: %.10lf (deviation: %e)\n", eps, eps_res, fabs(math_res - eps_res));
    puts("\nConvergence table (first N terms):");
    printf("  N |    ln_n    | deviation\n");
    printf("----+------------+------------\n");
    for (int i = 1; i <= N; i++) {
        double current_val = ln_n(x, i);
        printf("%3d | %10.6lf | %+11.4e\n", i, current_val, current_val - math_res);
    }
    return 0;
}

float area_parallelogram(float a, float h) { return a * h; }
void print_definition() { printf("\nDefinition: A parallelogram is a quadrilateral with opposite sides parallel and equal in pairs.\n"); }
void draw_parallelogram(int a, int h, int shift, char symbol) {
    int spaces;
    printf("\n");
    for (int row = 0; row < h; row++) {
        spaces = row * shift;
        for (int i = 0; i < spaces; i++) printf(" ");
        for (int col = 0; col < a; col++) {
            if (row == 0 || row == h - 1 || col == 0 || col == a - 1) printf("%c ", symbol);
            else printf("  ");
        }
        printf("\n");
    }
}

int task_three() {
    puts("TASK THREE----------------------------------------------------------------------------------------------------------------");
    int choice, shift, side_a, side_h;
    float base, height;
    char symbol;
    printf("Figure Work Menu (Parallelogram):\n");
    printf("1 - Calculate area\n");
    printf("2 - Print figure definition\n");
    printf("3 - Draw figure\n");
    printf("Your choice: ");
    scanf_s("%d", &choice);
    if (choice == 1) {
        printf("Enter the base of the figure: ");
        scanf_s("%f", &base);
        printf("Enter the height of the figure: ");
        scanf_s("%f", &height);
        printf("Figure area: %.2f\n", area_parallelogram(base, height));
    }
    else if (choice == 2) print_definition();
    else if (choice == 3) {
        printf("Enter drawing symbol: ");
        scanf_s(" %c", &symbol, 1);
        printf("Enter base length (integer): ");
        scanf_s("%d", &side_a);
        printf("Enter height (integer): ");
        scanf_s("%d", &side_h);
        printf("Enter shift amount (1, 2, 3): ");
        scanf_s("%d", &shift);
        draw_parallelogram(side_a, side_h, shift, symbol);
    }
    else printf("Invalid menu item!\n");
    return 0;
}
```

## Результаты работы программы

```text
TASK ONE----------------------------------------------------------------------------------------------------------------
Enter the value of x: 2
Function values at x = 2.000000:
fa(x) = 5.000000, fb(x) = 2.072528
...
```

## Информация о разработчике

Панарин Егор БИД-261
