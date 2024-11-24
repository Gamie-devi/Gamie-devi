import pygame
import random
import sys

# Initialize Pygame
pygame.init()

# Screen Dimensions
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Korean Traffic Runner")

# Colors
WHITE = (255, 255, 255)
GOLD = (255, 215, 0)

# Load Assets
player_image = pygame.image.load("korean_character.png")  # Trendy Korean character
car_image = pygame.image.load("modern_car.png")          # Modern car sprite
coin_image = pygame.image.load("coin.png")               # Coin sprite
background_image = pygame.image.load("korean_city.png")  # Korean city background

# Scale Assets
player_image = pygame.transform.scale(player_image, (50, 100))
car_image = pygame.transform.scale(car_image, (80, 120))
coin_image = pygame.transform.scale(coin_image, (30, 30))
background_image = pygame.transform.scale(background_image, (WIDTH, HEIGHT))

# Player Configuration
player_x = WIDTH // 2
player_y = HEIGHT - 150
player_speed = 10

# Game Variables
lane_positions = [200, 400, 600]  # Lanes for obstacles and coins
obstacles = []  # List of obstacle positions
coins = []      # List of coin positions
score = 0
clock = pygame.time.Clock()
font = pygame.font.Font(None, 36)

# Add Obstacles and Coins
def generate_obstacles():
    lane = random.choice(lane_positions)
    obstacles.append([lane, -120])  # Cars appear at the top of the screen

def generate_coins():
    lane = random.choice(lane_positions)
    coins.append([lane, -30])  # Coins appear at the top of the screen

# Collision Detection
def check_collision(rect1, rect2):
    return rect1.colliderect(rect2)

# Main Game Loop
running = True
while running:
    screen.blit(background_image, (0, 0))  # Draw background
    
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # Player Movement
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT] and player_x > 150:
        player_x -= player_speed
    if keys[pygame.K_RIGHT] and player_x < WIDTH - 200:
        player_x += player_speed

    # Add new obstacles and coins
    if random.randint(1, 100) < 3:  # 3% chance each frame
        generate_obstacles()
    if random.randint(1, 100) < 5:  # 5% chance each frame
        generate_coins()

    # Update obstacles and coins
    for obstacle in obstacles:
        obstacle[1] += 7  # Move cars down
        screen.blit(car_image, (obstacle[0], obstacle[1]))
        # Remove if out of screen
        if obstacle[1] > HEIGHT:
            obstacles.remove(obstacle)
        # Check collision
        if check_collision(pygame.Rect(obstacle[0], obstacle[1], 80, 120), pygame.Rect(player_x, player_y, 50, 100)):
            running = False  # Game over

    for coin in coins:
        coin[1] += 5  # Move coins down
        screen.blit(coin_image, (coin[0], coin[1]))
        # Remove if out of screen
        if coin[1] > HEIGHT:
            coins.remove(coin)
        # Check collision
        if check_collision(pygame.Rect(coin[0], coin[1], 30, 30), pygame.Rect(player_x, player_y, 50, 100)):
            coins.remove(coin)
            score += 10  # Increase score

    # Draw Player
    screen.blit(player_image, (player_x, player_y))

    # Display Score
    score_text = font.render(f"Score: {score}", True, GOLD)
    screen.blit(score_text, (10, 10))

    # Update Screen
    pygame.display.flip()
    clock.tick(30)  # Limit to 30 FPS

pygame.quit()
sys.exit()

