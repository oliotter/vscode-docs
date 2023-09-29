import pygame
import random

# Initialize Pygame
pygame.init()

# Constants
WIDTH, HEIGHT = 800, 600
BALL_SIZE = 20
PADDLE_WIDTH, PADDLE_HEIGHT = 10, 100
POWER_UP_SIZE = 30
WHITE = (255, 255, 255)
BALL_SPEED_X = 7
BALL_SPEED_Y = 7
PADDLE_SPEED = 10
POWER_UP_SPAWN_RATE = 0.02

# Create the game window
window = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Pong Game")

# Create the paddles, balls, and power-up
left_paddle = pygame.Rect(50, HEIGHT // 2 - PADDLE_HEIGHT // 2, PADDLE_WIDTH, PADDLE_HEIGHT)
right_paddle = pygame.Rect(WIDTH - 50 - PADDLE_WIDTH, HEIGHT // 2 - PADDLE_HEIGHT // 2, PADDLE_WIDTH, PADDLE_HEIGHT)
balls = [pygame.Rect(WIDTH // 2 - BALL_SIZE // 2, HEIGHT // 2 - BALL_SIZE // 2, BALL_SIZE, BALL_SIZE)]
power_up = None

# Initialize ball directions
ball_speeds = [(BALL_SPEED_X * random.choice((1, -1)), BALL_SPEED_Y * random.choice((1, -1))) for _ in range(len(balls))]

# Initialize scores
left_score = 0
right_score = 0

# Create fonts for score display
font = pygame.font.Font(None, 36)

# Create a font for the power-up display
power_up_font = pygame.font.Font(None, 48)
power_up_text = power_up_font.render("✖️", True, WHITE)

# Game loop
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # Move right paddle (controlled by player)
    keys = pygame.key.get_pressed()
    if keys[pygame.K_UP] and right_paddle.top > 0:
        right_paddle.y -= PADDLE_SPEED
    if keys[pygame.K_DOWN] and right_paddle.bottom < HEIGHT:
        right_paddle.y += PADDLE_SPEED

    # Move the left paddle (computer-controlled)
    if balls[0].centery < left_paddle.centery:
        left_paddle.y -= PADDLE_SPEED
    elif balls[0].centery > left_paddle.centery:
        left_paddle.y += PADDLE_SPEED

    # Move the balls
    for i in range(len(balls)):
        balls[i].x += ball_speeds[i][0]
        balls[i].y += ball_speeds[i][1]

    # Ball collisions with top and bottom walls
    for i in range(len(balls)):
        if balls[i].top <= 0 or balls[i].bottom >= HEIGHT:
            ball_speeds[i] = (ball_speeds[i][0], -ball_speeds[i][1])

    # Ball collisions with paddles
    for i in range(len(balls)):
        if balls[i].colliderect(left_paddle) or balls[i].colliderect(right_paddle):
            ball_speeds[i] = (-ball_speeds[i][0], ball_speeds[i][1])

    # Check for power-up collision
    if power_up:
        for i in range(len(balls)):
            if balls[i].colliderect(power_up):
                # Double the number of balls
                new_ball = pygame.Rect(random.randint(0, WIDTH - BALL_SIZE),
                                       random.randint(0, HEIGHT - BALL_SIZE),
                                       BALL_SIZE, BALL_SIZE)
                balls.append(new_ball)
                ball_speeds.append((random.choice((1, -1)) * BALL_SPEED_X, random.choice((1, -1)) * BALL_SPEED_Y))
                power_up = None
                break

    # Scoring
    for ball in balls:
        if ball.left <= 0:
            right_score += 1
            balls.remove(ball)
            ball_speeds.remove(ball_speeds[balls.index(ball)])
            # Randomly spawn a power-up
            if random.random() < POWER_UP_SPAWN_RATE and not power_up:
                power_up_x = random.randint(0, WIDTH - POWER_UP_SIZE)
                power_up_y = random.randint(0, HEIGHT - POWER_UP_SIZE)
                power_up = pygame.Rect(power_up_x, power_up_y, POWER_UP_SIZE, POWER_UP_SIZE)

        elif ball.right >= WIDTH:
            left_score += 1
            balls.remove(ball)
            ball_speeds.remove(ball_speeds[balls.index(ball)])
            # Randomly spawn a power-up
            if random.random() < POWER_UP_SPAWN_RATE and not power_up:
                power_up_x = random.randint(0, WIDTH - POWER_UP_SIZE)
                power_up_y = random.randint(0, HEIGHT - POWER_UP_SIZE)
                power_up = pygame.Rect(power_up_x, power_up_y, POWER_UP_SIZE, POWER_UP_SIZE)

    # Clear the screen
    window.fill((0, 0, 0))

    # Draw paddles, balls, and power-up
    pygame.draw.rect(window, WHITE, left_paddle)
    pygame.draw.rect(window, WHITE, right_paddle)
    for ball in balls:
        pygame.draw.ellipse(window, WHITE, ball)
    if power_up:
        pygame.draw.rect(window, WHITE, power_up)
        window.blit(power_up_text, (power_up.x + POWER_UP_SIZE // 2 - power_up_text.get_width() // 2,
                                    power_up.y + POWER_UP_SIZE // 2 - power_up_text.get_height() // 2))

    # Draw scores
    left_score_text = font.render(str(left_score), True, WHITE)
    right_score_text = font.render(str(right_score), True, WHITE)
    window.blit(left_score_text, (WIDTH // 4, 50))
    window.blit(right_score_text, (3 * WIDTH // 4 - right_score_text.get_width(), 50))

    # Update the display
    pygame.display.flip()

# Quit Pygame
pygame.quit()