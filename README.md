# space_game_project
    Игра под названием космическое путешествие включает в себя пушку, из которой можно стрелять, нажимая на клавиишу пробел, по врагам, 
которые надвигаются на пушку.Новые враги генерируются автоматичекси, игрок проигрывает, когда враг касается самой пушки. 
У игрока есть 3 жизни, то есть 3 пушки. Когда одна пушка закончилась, то автоматически идет перезаупск игры. Также игрок видит счет, сколько врагов он убил, видит количество оставшихся жизней из трех, а также свой рекорд. То есть, если игрко перезайдет в игру, то он увидит свой наилучший счет.

    Функция в основной часте программы в файле Space joyrney, которая создает окно для игры, а также внутри данной функции подключаются классы каждого объекта (пушка, пули и прищельцы) и статистика игры.
```
def run():
    pygame.init()
    screen = pygame.display.set_mode((700, 800))
    pygame.display.set_caption('Космическое путешествие')
    fon = (0, 0, 0)
    gun = Gun(screen)
    bullets = Group()
    inos = Group()
    controls.create_army(screen, inos)
    stats = Stats()
    sc = Scores(screen, stats)
```
    Внутри главного цикла программы вызываются функции, которые отрисовывают все объекты, а также те функции,которые отвественны за события. 
```
    while True:
        controls.events(screen,gun,bullets)
        if stats.run_game == True:
            gun.update_gun()
            controls.update(fon, screen, stats, sc,  gun, inos, bullets)
            controls.update_bullets(screen,stats, sc, inos, bullets)
            controls.update_inos(stats,screen,sc,gun,inos,bullets)
```
    В файле gun описана логика работы пушки. Создан класс, внутри которого первая функция инициализирует класс пушки (получение графического изображения пушки происходит через прямоугольник багодаря методу get_rect()), вторая функция отрисовывает пушку на экран с помощью метода blit().
```
    def output(self):
        self.screen.blit(self.image, self.rect)
```
    Также создана функция update_gun(), которая обновляет позицию пушки.Функция create_gun воссоздает пушку сначала, если была потеряна жизнь. 

    Модуль, посвященный пулям, создан в отдельном файле под названием bullet. Создан класс Bullet на основе модуля sprite,в нем инициализируется пуля в текущей позиции пушки. Также прописан метод для перемещения пули по координате y и отрисовки пули (draw_bullet).
    
 ```
     def update(self):
        self.y -= self.speed
        self.rect.y = self.y
 ```
    Модуль ino ответственен за работу пришельцев-врагов. Создается класс для одного врага, в имеется метод для инициализации и отрисовки врага. Из одного пришельца создается армия.Также прописана функция для передвижения инопланетян по оси y. 
    Также создан файл stats, где прописывается статистика игры. Создан класс Stats, который отслеживает статистику текущей игры. Создан метод reset(), который отслеживает статистику, изменяющуюся во время игры. У пушки заданы две жизни. Также отслеживается текущий счет и рекордный счет. 
    Создан файл scores, в котором создан класс для вывода игровой информации. Создан метод для инициализации подсчета очков, в котором прописан тип, цвет и размер шрифта и вызван метод для отрисовки текста. Метод def image_score() непосредственно преобразоывает текст счета в графическое изображение. Счет отображается в правой верхней части экрана. Метод show_score выводит счет на экран. Метод image_high_score преобразует рекордный счет в графическое изображение. Также создана функция image_guns(), которая выводит количсетво жизней в виде изображений пушки. 

    Все игровые события записаны в файле controls. Функция events() обрабатывает игровые события, а именно событие закрытия окна, и нажатия на клавиши для перемещения пушки, событие нажатия на пробел для стрельбы, при котором создается новая пуля. Функция update() отвечает за обновление экрана. Функция update_bullets() отвечает за обновление позиций пуль, чтобы как только пуля вылетела из пушки, она бы удалялась из котейнера.Также в данной функции проверяются коллизии или столкновения пуль и пришельцнв. Если происходит стоклновение спрайтов, то и пуля и пришелец удаляются. Если все пришельцы будут убиты, то создается вновь новая армия. Также если столкновение происходит, то начисляется 10 баллов за одного убитого пришельца. 
```
def update_bullets(screen,stats,sc, inos,bullets):
    bullets.update()
    for bullet in bullets.copy():
        if bullet.rect.bottom <= 0:
            bullets.remove(bullet)
    collisions = pygame.sprite.groupcollide(bullets,inos,True,True)
    if collisions:
        for inos in collisions.values():
            stats.score += 10 * len(inos)
        sc.image_score()
        check_high_score(stats, sc)
        sc.image_guns()
    if len(inos) == 0:
        bullets.empty()
        create_army(screen, inos)

```
    Также в файле controls создана фунцкия create_army() для создания группы пришельцев. Чтобы рассчитать необходимо количсетво пришельцев, которые могут поместиться на экране, была взята ширина экрана и из нее вычтено две ширины прищельца, а затем разделена на ширину одного пришельца. Ряд создается через цикл. Также рассчитывается количество рядов. Из высоты окна вычитается размер пушки и двойная высота пришельца. 

```
def create_army(screen, inos):
    ino = Ino(screen)
    ino_width = ino.rect.width
    number_ino_x = int((700 - 2*ino_width)/ino_width)
    ino_height = ino.rect.height
    number_ino_y = int((800 - 100 - 2 * ino_height) / ino_height)

    for row_number in range(number_ino_y - 1):
        for ino_number in range(number_ino_x):
            ino = Ino(screen)
            ino.x = ino_width + ino_width * ino_number
            ino.y = ino.height + ino.height * row_number
            ino.rect.x = ino.x
            ino.rect.y = ino.rect.height + ino.rect.height * row_number
            inos.add(ino)
```
    Кроме этого создана функция, которая обновляет позицию пришельцев и проверяет коллизию пушки и пришельцев. 
```
def update_inos(stats,screen, sc, gun, inos, bullets):
    inos.update()
    if pygame.sprite.spritecollideany(gun, inos):
        gun_kill(stats,screen,sc,gun,inos,bullets)
    inos_check(stats, screen,sc, gun, inos, bullets)
```
    Функция gun_kill() проверяет столкновение пушки и пришельцев. При столкновении удаляется жизнь пушки.Пришельцы и пули удаляются, вызывается функция создания новой армии. Есди жизней больше не осталось, то окно игры закрывается. 
    Фунцкия inos_сheck() проверяет, добралась ли армия с пришельцами до края экрана. Если прищельцы достигла низ экрана, то игра перегружается. 
    Функция check_high_score() проверяет новые рекорды. Если текущий счет больше рекордного, то в рекордный записывает новый счет. Функция вызывыается потсоянноо, при попадании в прищельца.Счет записывается в отдельный файл highscore.txt.
```
def check_high_score(stats, sc):
    if stats.score > stats.high_score:
        stats.high_score = stats.score
        sc.image_high_score()
        with open('highscore.txt', 'w') as f:
            f.write(str(stats.high_score))

```
