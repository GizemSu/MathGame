# Math Adventure

**Math Adventure** is a game where players answer simple mathematical questions to earn points while racing against time. The game is developed using the Pygame library in Python.

## Features
- Basic arithmetic questions (addition, subtraction, multiplication).
- Time-limited gameplay (30 seconds).
- +10 points for correct answers, -5 points for wrong answers.
- Game over screen with final score.
- Options to restart or quit the game.


## Getting Started

### Requirements
- Python 3.x
- Pygame library

### Installation

#### 1. Ensure Python and Pygame are Installed

Install the Pygame library by running the following command in your terminal or command prompt:

```bash

import pygame
import random
import sys

# Initialize Pygame
pygame.init()

# Screen dimensions
SCREEN_WIDTH, SCREEN_HEIGHT = 800, 600
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Math Adventure")

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
BLUE = (70, 130, 180)
RED = (220, 20, 60)
GREEN = (34, 139, 34)
GRAY = (200, 200, 200)
YELLOW = (255, 215, 0)

# Fonts
font = pygame.font.Font(None, 40)
large_font = pygame.font.Font(None, 60)

# Timer
clock = pygame.time.Clock()

# Game variables
score = 0
time_left = 30
question = ""
correct_answer = 0
options = []
game_over = False
in_menu = True  # Menu state

# Generate questions
def generate_question():
    global question, correct_answer, options
    num1 = random.randint(1, 10)
    num2 = random.randint(1, 10)
    operators = ['+', '-', '*']
    operator = random.choice(operators)
    question = f"{num1} {operator} {num2}"
    correct_answer = eval(question)  # Calculate the correct answer
    options = [correct_answer]
    while len(options) < 4:  # Add wrong answers
        wrong_answer = random.randint(1, 100)
        if wrong_answer not in options:
            options.append(wrong_answer)
    random.shuffle(options)  # Shuffle the options

# Reset the game
def reset_game():
    global score, time_left, game_over, in_menu
    score = 0
    time_left = 30
    game_over = False
    in_menu = False
    generate_question()

# Draw the main menu
def draw_menu():
    screen.fill(GRAY)
    draw_text("Math Adventure", large_font, YELLOW, 200, 100)
    draw_text("Click to start", font, BLACK, 250, 300)

# Draw text on the screen
def draw_text(text, font, color, x, y):
    rendered_text = font.render(text, True, color)
    screen.blit(rendered_text, (x, y))

# Start generating questions
generate_question()

# Main game loop
while True:
    screen.fill(WHITE)

    # Handle events
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()
        
        if event.type == pygame.MOUSEBUTTONDOWN:
            x, y = pygame.mouse.get_pos()
            if in_menu:
                reset_game()
            elif game_over:
                # Check if Restart or Quit buttons are clicked
                if restart_button.collidepoint(x, y):
                    reset_game()
                if quit_button.collidepoint(x, y):
                    pygame.quit()
                    sys.exit()
            else:
                # Check which option is clicked
                for idx, rect in enumerate(option_rects):
                    if rect.collidepoint(x, y):
                        if options[idx] == correct_answer:
                            score += 10
                        else:
                            score -= 5
                        generate_question()

    if in_menu:
        draw_menu()
    else:
        # Time control
        if not game_over:
            time_left -= clock.get_time() / 1000  # Convert milliseconds to seconds
            if time_left <= 0:
                game_over = True
                time_left = 0

        # Draw question and answers
        draw_text(f"Question: {question}", large_font, BLACK, 50, 50)
        draw_text(f"Score: {score}", font, BLUE, 50, 150)
        draw_text(f"Time Left: {int(time_left)}", font, RED, 50, 200)

        # Draw options
        option_rects = []
        for idx, option in enumerate(options):
            rect = pygame.Rect(50, 300 + idx * 60, 700, 50)
            option_rects.append(rect)
            pygame.draw.rect(screen, GREEN if not game_over else GRAY, rect)
            draw_text(str(option), font, WHITE, rect.x + 20, rect.y + 10)

        # Game Over screen
        if game_over:
            draw_text("Game Over!", large_font, BLACK, 300, 200)
            draw_text(f"Your Final Score: {score}", large_font, BLUE, 250, 300)

            # Restart and Quit buttons
            restart_button = pygame.Rect(200, 400, 150, 50)
            quit_button = pygame.Rect(450, 400, 150, 50)

            pygame.draw.rect(screen, BLUE, restart_button)
            pygame.draw.rect(screen, RED, quit_button)

            draw_text("Restart", font, WHITE, restart_button.x + 10, restart_button.y + 10)
            draw_text("Quit", font, WHITE, quit_button.x + 40, quit_button.y + 10)

    # Update the screen
    pygame.display.flip()
    clock.tick(30)  # FPS

