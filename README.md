# TEST
TEST

# Розроби свою гру в цьому файлі!
from pygame import *

back = (100, 19, 255)
RED = (255, 0, 0)
BLUE = (0, 0, 255)
GREEN = (62, 206, 0)
window_width = 700
window_heigth = 500
window = display.set_mode((700, 500))
window.fill(back)
picture = transform.scale(image.load("fantasticlabirint.jpg"), (700, 500))
display.set_caption("Фантастичний Лабіринт")

class PlayShowSprite(sprite.Sprite): 
#конструкт класа
    def __init__(self, user_image, user_x, user_y, size_x, size_y):
# виклик конструктора класа
        sprite.Sprite.__init__(self)
# кожен спрайт має зберігти image - зоображення
        self.image = transform.scale(image.load(user_image), (size_x, size_y))
# кожен спрайт має зберігти rect - прямокут, в який він вписан
        self.rect = self.image.get_rect()
        self.rect.x = user_x
        self.rect.y = user_y
# метод який відображає(малює) персоонажа на вікні(сцені)
    def reset(self):
        window.blit(self.image, (self.rect.x, self.rect.y))

class User(PlayShowSprite):
# спосіб(метод) за допомогою якого реалізовано керування тим, чи - іншим спрайтом за допомогою клавіатури кнопками (W, A, S, D) або стрілочками
    def __init__(self, user_image, user_x, user_y, size_x, size_y, user_x_runSpeed, user_y_runSpeed):
# виклик конструктора класа (Sprite):
        PlayShowSprite.__init__(self, user_image, user_x, user_y, size_x, size_y)
        self.x_runSpeed = user_x_runSpeed
        self.y_runSpeed = user_y_runSpeed
# переміщення персоонажа використоввуючи поточну вертикальну чи гаризонтальну
    def update(self):
# спочатку рух по гаризонталі
        if self.rect.x< window_width-80 and self.x_runSpeed>0 or self.rect.x >0 and self.x_runSpeed<0:
            self.rect.x += self.x_runSpeed
    # якщо був вихід за текстури стіни, тоді встати впрнитул до стіни        
        platforms_touched = sprite.spritecollide(self, barriers, False)
        if self.x_runSpeed > 0: # рух праворуч, правий край персонажа впритул до лівого краю стіни
            for p in platforms_touched:
                self.rect.right = p.rect.left  # якщо торкнулися одразу кількох стін
        elif self.x_runSpeed < 0: # аналогічно з лівою
            for p in platforms_touched:
                self.rect.left = p.rect.right
        if self.rect.y < window_height-80 and self.y_runSpeed>0 or self.rect.y > 10  and self.y_runSpeed<0:
            self.rect.y += self.y_runSpeed
        platforms_touched = sprite.spritecollide(self, barriers, False)
        if self.y_runSpeed > 0: # йдемо вниз
            for p in platforms_touched:
                # перевірка яка з платформ знизу найвища, та вирівнювання по ній, запам"ятання
                self.rect.bottom = p.rect.top
        elif self.y_runSpeed < 0: # йдемо вгору
            for p in platforms_touched:
                self.rect.top = p.rect.bottom # вирівнювання нижнього краю по лівих карях стінки

# постріл метод (використання місця гравця, щоб створити там пулю)
    def fire(self):
        bullet = Bullet("Laser_Bullet_2_rebg_alph.png", self.rect.right, self.rect.centery, 15, 20, 14)
        bullets.add(bullet)

# класс спрайту для ворога
class Enemy(PlayShowSprite):
    def __init__(self, user_image, user_x, user_y, size_x, size_y, user_speed):
        # виклик конструктора класу (Sprite):
        PlayShowSprite.__init__(self, user_image, user_x, user_y, size_x, size_y)
        self.speed = user_speed
        self.side = "left"

# рух ворога
    def update(self):
        if self.rect.x <= 150: #wall1 - x + wall 1 - width
            self.side = "right"
        if self.rect.x >= 613:
            self.side = "left"

        if self.side == "left":
            self.rect.x -= self.speed
        else:
            self.rect.x += self.speed

# класс для спарайту кулі
class Bullet(PlayShowSprite):
    def __init__(self, user_image, user_x, user_y, size_x, size_y, user_speed):
        PlayShowSprite.__init__(self, user_image, user_x, user_y, size_x, size_y)
        self.speed = user_speed
    # рух ворога
    def update(self):
        self.rect.x += self.speed 
        # зникне, якщо дійде до краю єкрана
        if self.rect.x > window_width+10:
            self.kill()

# створення вікна
window_width = 700
window_height = 500

# задаваненя кольору відповідно до схеми кольорів(RGB)
back = (119, 210, 223)
# створення групи для стін
barriers = sprite.Group()
bullets = sprite.Group()
# створення стін картинок
wall1 = PlayShowSprite("wall_T2.jpg", 100, 180, 500, 100)# window_width/2 - window_width/3, window_height/2
wall2 = PlayShowSprite("wall_T2.jpg", 90, 100, 66, 450)
wall3 = PlayShowSprite("spacewall.jpg", 360, 100, 20, 80)
wall4 = PlayShowSprite("spacewall.jpg", 390, 386, 480, 17)
wall5 = PlayShowSprite("spacewall.jpg", 250, 280, 160, 26)
# додавання стін до группи
barriers.add(wall1)
barriers.add(wall2)
barriers.add(wall3)
barriers.add(wall4)
barriers.add(wall5)
#створення групи для монстрів
monsters = sprite.Group()

# створення Спрайтів
user_personage = User("Shuttle_alph_2_rebg_alph.png", 5, window_height - 80, 80, 80, 0, 0)
monster = Enemy("ufo_1.png", window_height - 80, 180, 80, 80, 4)
final_sprite = PlayShowSprite("winbF_alph.jpg", window_width - 85, window_height - 100, 80, 80)

monster1 = Enemy("ufo_1.png", window_width - 80, 20, 80, 80, 6)
monster2 = Enemy("ufo_2.png", window_width - 90, 300, 80, 80, 4)
monster3 = Enemy("ufo_3_rebg.png", window_width - 90, 420, 80, 80, 3)

monsters.add(monster1)
monsters.add(monster2)
monsters.add(monster3)

# змінна п відповідає за те, як само скінчилась ігра
finish = False

# цикл ігри

picture = transform.scale(image.load("fantasticlabirint.jpg"), (700, 500))

Start = True
while Start:
# кожні 0.05 сек. цикл спрацьовує(відпрацюоввує)
    time.delay(50)
    
# керування
    for obj in event.get():
        if obj.type == QUIT:
            Start = False
# клавіша натиснута
        elif obj.type == KEYDOWN:
            if obj.key == K_LEFT:
                user_personage.x_runSpeed = -5
            elif obj.key == K_RIGHT:
                user_personage.x_runSpeed = 5
            elif obj.key == K_UP:
                user_personage.y_runSpeed = -5
            elif obj.key == K_DOWN:
                user_personage.y_runSpeed = 5
            elif obj.key == K_SPACE:
                user_personage.fire()

# клавіша відпущена(віджата)
        elif obj.type == KEYUP:
            if obj.key == K_LEFT:
                user_personage.x_runSpeed = 0
            elif obj.key == K_RIGHT:
                user_personage.x_runSpeed = 0
            elif obj.key == K_UP:
                user_personage.y_runSpeed = 0
            elif obj.key == K_DOWN:
                user_personage.y_runSpeed = 0

    if finish == False: #if not finish if finish != True
# зафарбовування вікна кольором
        window.blit(picture, (0, 0))
    # малювання об"єкт
        user_personage.reset()
    # ввімкнення руху
        user_personage.update()
        bullets.update()
        
    # малювання двох стін
    # wall1.reset()
    # wall2.reset()
        barriers.draw(window)
        
        bullets.draw(window)
        final_sprite.reset()

        sprite.groupcollide(monsters, bullets, True, True)
        monsters.update()
        monsters.draw(window)
        #monster.reset()
        sprite.groupcollide(bullets, barriers, True, False)

    # перевірка зіткнення героя з ворогами та стінами
        if sprite.spritecollide(user_personage, monsters, False):
            finish = True
    # обчислення відношення
            picture = image.load("finisch-no-win.jpg")
          #  d = picture.get_width() // picture.get_height()
            window.fill((255, 255, 255))
            window.blit(transform.scale(picture, (window_width, window_height)), (0, 0))

        if sprite.collide_rect(user_personage, final_sprite):
            finish = True
            picture = image.load("winb_alph.jpg")
            d = picture.get_width() // picture.get_height()
            window.fill((255, 255, 255))
            window.blit(transform.scale(picture, (window_width, window_height)), (0, 0))            

    display.update()# Розроби свою гру в цьому файлі!
from pygame import *

back = (100, 19, 255)
RED = (255, 0, 0)
BLUE = (0, 0, 255)
GREEN = (62, 206, 0)
window_width = 700
window_heigth = 500
window = display.set_mode((700, 500))
window.fill(back)
picture = transform.scale(image.load("fantasticlabirint.jpg"), (700, 500))
display.set_caption("Фантастичний Лабіринт")

class PlayShowSprite(sprite.Sprite): 
#конструкт класа
    def __init__(self, user_image, user_x, user_y, size_x, size_y):
# виклик конструктора класа
        sprite.Sprite.__init__(self)
# кожен спрайт має зберігти image - зоображення
        self.image = transform.scale(image.load(user_image), (size_x, size_y))
# кожен спрайт має зберігти rect - прямокут, в який він вписан
        self.rect = self.image.get_rect()
        self.rect.x = user_x
        self.rect.y = user_y
# метод який відображає(малює) персоонажа на вікні(сцені)
    def reset(self):
        window.blit(self.image, (self.rect.x, self.rect.y))

class User(PlayShowSprite):
# спосіб(метод) за допомогою якого реалізовано керування тим, чи - іншим спрайтом за допомогою клавіатури кнопками (W, A, S, D) або стрілочками
    def __init__(self, user_image, user_x, user_y, size_x, size_y, user_x_runSpeed, user_y_runSpeed):
# виклик конструктора класа (Sprite):
        PlayShowSprite.__init__(self, user_image, user_x, user_y, size_x, size_y)
        self.x_runSpeed = user_x_runSpeed
        self.y_runSpeed = user_y_runSpeed
# переміщення персоонажа використоввуючи поточну вертикальну чи гаризонтальну
    def update(self):
# спочатку рух по гаризонталі
        if self.rect.x< window_width-80 and self.x_runSpeed>0 or self.rect.x >0 and self.x_runSpeed<0:
            self.rect.x += self.x_runSpeed
    # якщо був вихід за текстури стіни, тоді встати впрнитул до стіни        
        platforms_touched = sprite.spritecollide(self, barriers, False)
        if self.x_runSpeed > 0: # рух праворуч, правий край персонажа впритул до лівого краю стіни
            for p in platforms_touched:
                self.rect.right = p.rect.left  # якщо торкнулися одразу кількох стін
        elif self.x_runSpeed < 0: # аналогічно з лівою
            for p in platforms_touched:
                self.rect.left = p.rect.right
        if self.rect.y < window_height-80 and self.y_runSpeed>0 or self.rect.y > 10  and self.y_runSpeed<0:
            self.rect.y += self.y_runSpeed
        platforms_touched = sprite.spritecollide(self, barriers, False)
        if self.y_runSpeed > 0: # йдемо вниз
            for p in platforms_touched:
                # перевірка яка з платформ знизу найвища, та вирівнювання по ній, запам"ятання
                self.rect.bottom = p.rect.top
        elif self.y_runSpeed < 0: # йдемо вгору
            for p in platforms_touched:
                self.rect.top = p.rect.bottom # вирівнювання нижнього краю по лівих карях стінки

# постріл метод (використання місця гравця, щоб створити там пулю)
    def fire(self):
        bullet = Bullet("Laser_Bullet_2_rebg_alph.png", self.rect.right, self.rect.centery, 15, 20, 14)
        bullets.add(bullet)

# класс спрайту для ворога
class Enemy(PlayShowSprite):
    def __init__(self, user_image, user_x, user_y, size_x, size_y, user_speed):
        # виклик конструктора класу (Sprite):
        PlayShowSprite.__init__(self, user_image, user_x, user_y, size_x, size_y)
        self.speed = user_speed
        self.side = "left"

# рух ворога
    def update(self):
        if self.rect.x <= 150: #wall1 - x + wall 1 - width
            self.side = "right"
        if self.rect.x >= 613:
            self.side = "left"

        if self.side == "left":
            self.rect.x -= self.speed
        else:
            self.rect.x += self.speed

# класс для спарайту кулі
class Bullet(PlayShowSprite):
    def __init__(self, user_image, user_x, user_y, size_x, size_y, user_speed):
        PlayShowSprite.__init__(self, user_image, user_x, user_y, size_x, size_y)
        self.speed = user_speed
    # рух ворога
    def update(self):
        self.rect.x += self.speed 
        # зникне, якщо дійде до краю єкрана
        if self.rect.x > window_width+10:
            self.kill()

# створення вікна
window_width = 700
window_height = 500

# задаваненя кольору відповідно до схеми кольорів(RGB)
back = (119, 210, 223)
# створення групи для стін
barriers = sprite.Group()
bullets = sprite.Group()
# створення стін картинок
wall1 = PlayShowSprite("wall_T2.jpg", 100, 180, 500, 100)# window_width/2 - window_width/3, window_height/2
wall2 = PlayShowSprite("wall_T2.jpg", 90, 100, 66, 450)
wall3 = PlayShowSprite("spacewall.jpg", 360, 100, 20, 80)
wall4 = PlayShowSprite("spacewall.jpg", 390, 386, 480, 17)
wall5 = PlayShowSprite("spacewall.jpg", 250, 280, 160, 26)
# додавання стін до группи
barriers.add(wall1)
barriers.add(wall2)
barriers.add(wall3)
barriers.add(wall4)
barriers.add(wall5)
#створення групи для монстрів
monsters = sprite.Group()

# створення Спрайтів
user_personage = User("Shuttle_alph_2_rebg_alph.png", 5, window_height - 80, 80, 80, 0, 0)
monster = Enemy("ufo_1.png", window_height - 80, 180, 80, 80, 4)
final_sprite = PlayShowSprite("winbF_alph.jpg", window_width - 85, window_height - 100, 80, 80)

monster1 = Enemy("ufo_1.png", window_width - 80, 20, 80, 80, 6)
monster2 = Enemy("ufo_2.png", window_width - 90, 300, 80, 80, 4)
monster3 = Enemy("ufo_3_rebg.png", window_width - 90, 420, 80, 80, 3)

monsters.add(monster1)
monsters.add(monster2)
monsters.add(monster3)

# змінна п відповідає за те, як само скінчилась ігра
finish = False

# цикл ігри

picture = transform.scale(image.load("fantasticlabirint.jpg"), (700, 500))

Start = True
while Start:
# кожні 0.05 сек. цикл спрацьовує(відпрацюоввує)
    time.delay(50)
    
# керування
    for obj in event.get():
        if obj.type == QUIT:
            Start = False
# клавіша натиснута
        elif obj.type == KEYDOWN:
            if obj.key == K_LEFT:
                user_personage.x_runSpeed = -5
            elif obj.key == K_RIGHT:
                user_personage.x_runSpeed = 5
            elif obj.key == K_UP:
                user_personage.y_runSpeed = -5
            elif obj.key == K_DOWN:
                user_personage.y_runSpeed = 5
            elif obj.key == K_SPACE:
                user_personage.fire()

# клавіша відпущена(віджата)
        elif obj.type == KEYUP:
            if obj.key == K_LEFT:
                user_personage.x_runSpeed = 0
            elif obj.key == K_RIGHT:
                user_personage.x_runSpeed = 0
            elif obj.key == K_UP:
                user_personage.y_runSpeed = 0
            elif obj.key == K_DOWN:
                user_personage.y_runSpeed = 0

    if finish == False: #if not finish if finish != True
# зафарбовування вікна кольором
        window.blit(picture, (0, 0))
    # малювання об"єкт
        user_personage.reset()
    # ввімкнення руху
        user_personage.update()
        bullets.update()
        
    # малювання двох стін
    # wall1.reset()
    # wall2.reset()
        barriers.draw(window)
        
        bullets.draw(window)
        final_sprite.reset()

        sprite.groupcollide(monsters, bullets, True, True)
        monsters.update()
        monsters.draw(window)
        #monster.reset()
        sprite.groupcollide(bullets, barriers, True, False)

    # перевірка зіткнення героя з ворогами та стінами
        if sprite.spritecollide(user_personage, monsters, False):
            finish = True
    # обчислення відношення
            picture = image.load("finisch-no-win.jpg")
          #  d = picture.get_width() // picture.get_height()
            window.fill((255, 255, 255))
            window.blit(transform.scale(picture, (window_width, window_height)), (0, 0))

        if sprite.collide_rect(user_personage, final_sprite):
            finish = True
            picture = image.load("winb_alph.jpg")
            d = picture.get_width() // picture.get_height()
            window.fill((255, 255, 255))
            window.blit(transform.scale(picture, (window_width, window_height)), (0, 0))            

    display.update()








