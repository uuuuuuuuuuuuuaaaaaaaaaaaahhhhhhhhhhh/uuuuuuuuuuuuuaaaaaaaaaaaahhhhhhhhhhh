import pygame
import sys
import random

# Initialize Pygame
pygame.init()

# Set up screen
WIDTH, HEIGHT = 800, 600
win = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Simple Shooter Game")

# Define colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)

# Player attributes
player_width, player_height = 50, 50
player_x = WIDTH // 2 - player_width // 2
player_y = HEIGHT - player_height - 10
player_speed = 5

# Bullet attributes
bullet_width, bullet_height = 5, 15
bullet_speed = 7
bullets = []

# Enemy attributes
enemy_width, enemy_height = 50, 50
enemy_speed = 3
enemies = []

# Create player
player = pygame.Rect(player_x, player_y, player_width, player_height)

# Function to draw player
def draw_player():
    pygame.draw.rect(win, WHITE, player)

# Function to draw bullets
def draw_bullets():
    for bullet in bullets:
        pygame.draw.rect(win, WHITE, bullet)

# Function to draw enemies
def draw_enemies():
    for enemy in enemies:
        pygame.draw.rect(win, RED, enemy)

# Main game loop
def main():
    global bullets, enemies

    clock = pygame.time.Clock()

    while True:
        # Handle events
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()

        # Player movement
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT] and player.x > 0:
            player.x -= player_speed
        if keys[pygame.K_RIGHT] and player.x < WIDTH - player_width:
            player.x += player_speed

        # Bullet movement
        for bullet in bullets:
            bullet.y -= bullet_speed
            if bullet.y < 0:
                bullets.remove(bullet)

        # Enemy spawning
        if len(enemies) < 5 and random.random() < 0.01:
            enemy_x = random.randint(0, WIDTH - enemy_width)
            enemy_y = random.randint(-HEIGHT, 0)
            enemy = pygame.Rect(enemy_x, enemy_y, enemy_width, enemy_height)
            enemies.append(enemy)

        # Enemy movement
        for enemy in enemies:
            enemy.y += enemy_speed
            if enemy.y > HEIGHT:
                enemies.remove(enemy)

        # Bullet-enemy collision detection
        for bullet in bullets:
            for enemy in enemies:
                if bullet.colliderect(enemy):
                    bullets.remove(bullet)
                    enemies.remove(enemy)

        # Clear screen
        win.fill(BLACK)

        # Draw objects
        draw_player()
        draw_bullets()
        draw_enemies()

        # Update display
        pygame.display.update()

        # Cap the frame rate
        clock.tick(60)

        # Check for game over
        if any(enemy.colliderect(player) for enemy in enemies):
            print("Game Over")
            pygame.quit()
            sys.exit()

        # Handle shooting
        if keys[pygame.K_SPACE]:
            bullet_x = player.x + player_width // 2 - bullet_width // 2
            bullet_y = player.y - bullet_height
            bullet = pygame.Rect(bullet_x, bullet_y, bullet_width, bullet_height)
            bullets.append(bullet)

if __name__ == "__main__":
    main()

