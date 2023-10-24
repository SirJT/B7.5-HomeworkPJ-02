from random import randint

#***Собственный тип данных "точка"***
class Dot:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    # Метод __eq__ отвечает за сравнение двух объектов.
    # Делается для установление\неравенства равенства между self x y и other x y.
    # Чтобы каждый раз не писать print(a.x == c.x and a.y == c.y)
    def __eq__(self, other):
        return self.x == other.x and self.y == other.y

    # __repr__ отвечающий за вывод точек в консоль.
    def __repr__(self):
        return f"({self.x}, {self.y})"

#***Собственные классы исключений.***
class BoardException(Exception):
    pass

# Исключение в случае если игрок пытается выстрелить за пределы установленных координат.
class BoardOutException(BoardException):
    def __str__(self):
        return "Вы пытаетесь выстрелить за доску!"

# Исключение в случае если игрок ранее стрелял по данным координатам.
class BoardUsedException(BoardException):
    def __str__(self):
        return "Вы уже стреляли в эту клетку"

# Исключение для того, чтобы размещать нормально корабли.
class BoardWrongShipException(BoardException):
    pass

#***Класс "Корабль"***
class Ship:
    def __init__(self, bow, l, o):
        self.bow = bow
        self.l = l
        self.o = o
        self.lives = l

    @property
    def dots(self):
        ship_dots = []
        for i in range(self.l):
            cur_x = self.bow.x
            cur_y = self.bow.y

            if self.o == 0:
                cur_x += i

            elif self.o == 1:
                cur_y += i

            ship_dots.append(Dot(cur_x, cur_y))

        return ship_dots
    # Проверка на наличе попадания в корабль.
    def shooten(self, shot):
        return shot in self.dots

#***Класс "Игровое поле"***
class Board:
    def __init__(self, hid=False, size=6):
        self.size = size # Размер поля.
        self.hid = hid # Сокрытие поля.

        self.count = 0 # Колличество пораженных кораблей.

        self.field = [["O"] * size for _ in range(size)] # Сетка в которой будет храниться состояние клеток.

        self.busy = [] # Занятые точки.
        self.ships = [] # Список кораблей.

    # ***Контур корабля и добавление его на доску***
    def add_ship(self, ship):  # Метод "add_ship" для размещения коробля.

        for d in ship.dots:
            if self.out(d) or d in self.busy: # Проверка того, что каждая точка коробля
            # не выходит за границы "self.out" и не занята "self.busy".
                raise BoardWrongShipException() # Если не соблюдается предыдущее условие, то выбрасывается исключение.
        for d in ship.dots:
            self.field[d.x][d.y] = "■" # Установка квадратика в точку занятую кораблём.
            self.busy.append(d) # Записывает точку в список занятых.

        self.ships.append(ship)
        self.contour(ship)

    # Метод "contour" -Устанавливает отступы от занятой точки.
    def contour(self, ship, verb=False):
        # Список near содержит сдвиги в каждую сторону от (0, 0)
        near = [
            (-1, -1), (-1, 0), (-1, 1),
            (0, -1), (0, 0), (0, 1),
            (1, -1), (1, 0), (1, 1)
        ]
        for d in ship.dots:
            for dx, dy in near:
                cur = Dot(d.x + dx, d.y + dy)
                if not (self.out(cur)) and cur not in self.busy:
                    if verb:
                        self.field[cur.x][cur.y] = "." # Поставить точку.
                    self.busy.append(cur) # добавить в список занятых точек.

    # Вывод коробля на доску.
    def __str__(self):
        res = ""
        res += "  | 1 | 2 | 3 | 4 | 5 | 6 |"
        for i, row in enumerate(self.field):
            res += f"\n{i + 1} | " + " | ".join(row) + " |"

        if self.hid: # Если True, то заменяет все символы коробля на пустые символы.
            res = res.replace("■", "O")
        return res

    # Метод Out проверяет, находится ли точка в пределах координат игрового поля.
    def out(self, d):
        return not ((0 <= d.x < self.size) and (0 <= d.y < self.size))

    # ***Стрельба по доске***

    # Метод "shot" делает выстрел
    def shot(self, d):
        if self.out(d): # Проверяе выходит ли выстрел за границы, если выходит то делает исключение "BoardOutException"
            raise BoardOutException()

        if d in self.busy: # Проверяет занята ли точка, если занята то выюрасывает искллючение "BoardUsedException"
            raise BoardUsedException()

        self.busy.append(d) # делает точку занятой.

        for ship in self.ships:
            if d in ship.dots:
                ship.lives -= 1 # Уменьшает количесто жизней у корабля в случае попадания.
                self.field[d.x][d.y] = "X" # Поставить "Х" если корабль поражен.
                if ship.lives == 0: # Если колличество жизней корабля равно "0"
                    self.count += 1 # Добавить к счетчику уничтоженных кораблей +1
                    self.contour(ship, verb=True) # Обозначить по контуру точками клетки вокруг уничтоженного корабля.
                    print("Корабль уничтожен!")
                    return False # Заканчивает ход
                else:
                    print("Корабль ранен!")
                    return True # Продолжает ход, после попадания.

        #Выполняется в случае промаха.
        self.field[d.x][d.y] = "." #Поставить точку.
        print("Мимо!")
        return False # Закончить ход.

    #Обнулить колличество точек перед стартом игры.
    def begin(self):
        self.busy = []

#Класс "Игрок"
class Player:
    def __init__(self, board, enemy):
        self.board = board # Доска игрока
        self.enemy = enemy # Доска противника

    def ask(self):
        raise NotImplementedError()

    def move(self):
        while True:
            try:
                target = self.ask()
                repeat = self.enemy.shot(target)
                return repeat
            except BoardException as e:
                print(e)

#Класс "игрок-компьютер"
class AI(Player):
    def ask(self):
        d = Dot(randint(0, 5), randint(0, 5))
        print(f"Ход компьютера: {d.x + 1} {d.y + 1}")
        return d

#Класс "игрок-пользователь"
class User(Player):
    def ask(self):
        while True:
            cords = input("Ваш ход: ").split()

            if len(cords) != 2:
                print(" Введите 2 координаты! ")
                continue

            x, y = cords

            if not (x.isdigit()) or not (y.isdigit()):
                print(" Введите числа! ")
                continue

            x, y = int(x), int(y)

            return Dot(x - 1, y - 1)

#Конструктор
class Game:
    def __init__(self, size=6): # Задаем размер поля в системе координат 6х6
        self.size = size
        pl = self.random_board() # Сгенерировать случайную доску для игрока.
        co = self.random_board() # Сгенерировать случайную доску для ИИ
        co.hid = True # Сделать скрытой доску ИИ

        self.ai = AI(co, pl)
        self.us = User(pl, co)

    # Метод "random_board" гарантированно создает доску
    def random_board(self):
        board = None
        while board is None:
            board = self.random_place()
        return board

    # Пытаемся на доску установить корабли.
    def random_place(self):
        lens = [3, 2, 2, 1, 1, 1, 1] # Список кораблей которые будем устанавливать.
        board = Board(size=self.size)
        attempts = 0 # Количество попыток поставить корабли.
        for l in lens:
            while True:
                attempts += 1 # Увеличивает количество попыток +1
                if attempts > 2000: # Если количество попыток достигает свыше 2000, то вернуть "None"
                    return None
                ship = Ship(Dot(randint(0, self.size), randint(0, self.size)), l, randint(0, 1))
                try:
                    board.add_ship(ship)
                    break
                except BoardWrongShipException:
                    pass
        board.begin()
        return board

    #Приветствие
    def greet(self):
        print("-------------------")
        print("  Приветсвуем вас  ")
        print("      в игре       ")
        print("    морской бой    ")
        print("-------------------")
        print(" формат ввода: x y ")
        print(" x - номер строки  ")
        print(" y - номер столбца ")

    def loop(self):
        num = 0 # Номер хода
        while True:
            print("-" * 20)
            print("Доска пользователя:")
            print(self.us.board) # Вывести доску пользователя
            print("-" * 20)
            print("Доска компьютера:")
            print(self.ai.board) # Вывести доску ИИ
            if num % 2 == 0: # Если номер хода четный, то ходит пользователь
                print("-" * 20)
                print("Ходит пользователь!")
                repeat = self.us.move()
            else: # Если номер хода нечетный, то ходит ИИ
                print("-" * 20)
                print("Ходит компьютер!")
                repeat = self.ai.move()
            if repeat: # В случае повтора хода, num уменьшить на -1, чтобы ход игрока не увеличился и случился переход.
                num -= 1

            if self.ai.board.count == 7:
                print("-" * 20)
                print("Пользователь выиграл!")
                break

            if self.us.board.count == 7:
                print("-" * 20)
                print("Компьютер выиграл!")
                break
            num += 1

    def start(self):
        self.greet()
        self.loop()


g = Game()
g.start()