# Flappy-puppy.-py

import pygame
import random

# Initialize pygame
pygame.init()

# Screen dimensions
WIDTH, HEIGHT = 400, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Flappy Puppy")

# Colors
WHITE = (255, 255, 255)
GREEN = (0, 255, 0)
BLUE = (0, 0, 255)

# Game variables
gravity = 0.5
jump_strength = -10
velocity = 0
x_pos = 50  # Initial X position for the puppy
y_pos = HEIGHT // 2  # Initial Y position for the puppy
bird_width, bird_height = 50, 50

# Load puppy image
puppy_image = pygame.image.load('puppy.png')
puppy_image = pygame.transform.scale(puppy_image, (bird_width, bird_height))  # Resize puppy image

# Pipes
pipe_width = 60
pipe_gap = 150
pipe_velocity = 5

# Font
font = pygame.font.SysFont('Arial', 32)

# Game loop
clock = pygame.time.Clock()
score = 0
pipe_list = []
running = True
while running:
    screen.fill(WHITE)

    # Event handling
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE:
                velocity = jump_strength  # Make the puppy jump

    # Update puppy's position
    velocity += gravity
    y_pos += velocity

    # Check for collision with the ground or ceiling
    if y_pos > HEIGHT - bird_height:
        y_pos = HEIGHT - bird_height
        velocity = 0
    if y_pos < 0:
        y_pos = 0
        velocity = 0

    # Create pipes
    if len(pipe_list) == 0 or pipe_list[-1]['x'] < WIDTH - 300:
        pipe_height = random.randint(150, HEIGHT - pipe_gap - 150)
        pipe_list.append({'x': WIDTH, 'height': pipe_height})

    # Move pipes
    for pipe in pipe_list:
        pipe['x'] -= pipe_velocity
        if pipe['x'] < -pipe_width:
            pipe_list.remove(pipe)
            score += 1

    # Draw pipes
    for pipe in pipe_list:
        pygame.draw.rect(screen, GREEN, (pipe['x'], 0, pipe_width, pipe['height']))
        pygame.draw.rect(screen, GREEN, (pipe['x'], pipe['height'] + pipe_gap, pipe_width, HEIGHT))

    # Draw puppy
    screen.blit(puppy_image, (x_pos, y_pos))

    # Draw score
    score_text = font.render(f"Score: {score}", True, BLUE)
    screen.blit(score_text, (10, 10))

    # Check for collisions with pipes
    for pipe in pipe_list:
        if pipe['x'] < x_pos + bird_width and pipe['x'] + pipe_width > x_pos:
            if y_pos < pipe['height'] or y_pos + bird_height > pipe['height'] + pipe_gap:
                running = False

    # Update the screen
    pygame.display.update()

    # Frame rate
    clock.tick(60)

# Game over
game_over_text = font.render("Game Over!", True, (255, 0, 0))
screen.blit(game_over_text, (WIDTH // 2 - game_over_text.get_width() // 2, HEIGHT // 2))
pygame.display.update()
pygame.time.wait(2000)  # Wait for 2 seconds before closing

pygame.quit()
