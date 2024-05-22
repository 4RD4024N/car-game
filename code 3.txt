import pygame
import random

pygame.init()

# Ekran boyutları
dis_width = 800
dis_height = 600

# Renkler
black = (0, 0, 0)
white = (255, 255, 255)
red = (255, 0, 0)
blue = (0, 0, 255)

# Araba boyutları
car_width = 50
car_height = 60

# Ekran oluşturma
dis = pygame.display.set_mode((dis_width, dis_height))
pygame.display.set_caption('Araba Yarışı')

clock = pygame.time.Clock()
car_speed = 5

# Araba görseli
car_img = pygame.Surface((car_width, car_height))
car_img.fill(blue)

# Engelleri oluştur
def create_obstacle():
    obstacle_width = 50
    obstacle_height = 50
    obstacle_x = random.randint(0, dis_width - obstacle_width)
    obstacle_y = -obstacle_height
    return pygame.Rect(obstacle_x, obstacle_y, obstacle_width, obstacle_height)

# Oyun döngüsü
def game_loop():
    game_over = False
    car_x = dis_width // 2
    car_y = dis_height - car_height - 10
    car_x_change = 0

    obstacles = []
    obstacle_speed = 5
    score = 0

    while not game_over:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                game_over = True
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT:
                    car_x_change = -car_speed
                if event.key == pygame.K_RIGHT:
                    car_x_change = car_speed
            if event.type == pygame.KEYUP:
                if event.key == pygame.K_LEFT or event.key == pygame.K_RIGHT:
                    car_x_change = 0

        car_x += car_x_change

        if car_x < 0:
            car_x = 0
        elif car_x > dis_width - car_width:
            car_x = dis_width - car_width

        if random.randint(0, 100) < 10:  # Rastgele engeller oluştur
            obstacles.append(create_obstacle())

        dis.fill(white)

        # Engelleri güncelle ve çiz
        for obstacle in obstacles[:]:
            obstacle.y += obstacle_speed
            if obstacle.y > dis_height:
                obstacles.remove(obstacle)
                score += 1
            if obstacle.colliderect(pygame.Rect(car_x, car_y, car_width, car_height)):
                game_over = True
            pygame.draw.rect(dis, red, obstacle)

        dis.blit(car_img, (car_x, car_y))

        score_text = pygame.font.SysFont(None, 35).render(f'Skor: {score}', True, black)
        dis.blit(score_text, (10, 10))

        pygame.display.update()
        clock.tick(30)

    pygame.quit()

game_loop()
