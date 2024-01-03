    import pygame
    import random
    import math

    pygame.init()

    WIDTH, HEIGHT = 400, 300

    WHITE = (255, 255, 255)
    BLACK = (0, 0, 0)

    BALL_SPEED = 4
    PADDLE_SPEED = 10

    win = pygame.display.set_mode((WIDTH, HEIGHT))
    pygame.display.set_caption("Ping Pong")
    clock = pygame.time.Clock()

    PADDLE_WIDTH, PADDLE_HEIGHT = 10, 100
    left_paddle_x, right_paddle_x = 50, WIDTH - 50 - PADDLE_WIDTH
    left_paddle_y, right_paddle_y = HEIGHT // 2, HEIGHT // 2

    ball_radius = 10
    ball_x, ball_y = WIDTH // 2, HEIGHT // 2
    ball_speed = BALL_SPEED
    ball_angle = random.uniform(-math.pi / 4, math.pi / 4)  # Ángulo inicial aleatorio

    left_paddle_up = False
    left_paddle_down = False
    right_paddle_up = False
    right_paddle_down = False

    left_score = 0
    right_score = 0
    max_score = 10  # Puntuación máxima para ganar

    font = pygame.font.Font(None, 36)

    def update_score():
    text = font.render(f"{left_score} - {right_score}", True, WHITE)
    win.blit(text, ((WIDTH - text.get_width()) // 2, 10))

    def show_winner(winner):
    text = font.render(f"¡Jugador {winner} gana!", True, WHITE)
    win.blit(text, ((WIDTH - text.get_width()) // 2, HEIGHT // 2))
    return pygame.time.get_ticks()

    running = True
    winner_shown_time = 0
    winner_display_time = 3000  # Tiempo en milisegundos (3 segundos)

    while running:
    win.fill(BLACK)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_w:
                left_paddle_up = True
            elif event.key == pygame.K_s:
                left_paddle_down = True
            elif event.key == pygame.K_UP:
                right_paddle_up = True
            elif event.key == pygame.K_DOWN:
                right_paddle_down = True
        elif event.type == pygame.KEYUP:
            if event.key == pygame.K_w:
                left_paddle_up = False
            elif event.key == pygame.K_s:
                left_paddle_down = False
            elif event.key == pygame.K_UP:
                right_paddle_up = False
            elif event.key == pygame.K_DOWN:
                right_paddle_down = False

    if left_paddle_up and left_paddle_y > 0:
        left_paddle_y -= PADDLE_SPEED
    if left_paddle_down and left_paddle_y < HEIGHT - PADDLE_HEIGHT:
        left_paddle_y += PADDLE_SPEED
    if right_paddle_up and right_paddle_y > 0:
        right_paddle_y -= PADDLE_SPEED
    if right_paddle_down and right_paddle_y < HEIGHT - PADDLE_HEIGHT:
        right_paddle_y += PADDLE_SPEED

    ball_x += int(ball_speed * math.cos(ball_angle))
    ball_y += int(ball_speed * math.sin(ball_angle))

    if ball_y - ball_radius <= 0 or ball_y + ball_radius >= HEIGHT:
        ball_angle = -ball_angle

    if left_paddle_x + PADDLE_WIDTH >= ball_x - ball_radius >= left_paddle_x and left_paddle_y <= ball_y <= left_paddle_y + PADDLE_HEIGHT:
        ball_speed += 0.5
        relative_intersect_y = (left_paddle_y + PADDLE_HEIGHT / 2) - ball_y
        normalized_relative_intersect_y = relative_intersect_y / (PADDLE_HEIGHT / 2)
        bounce_angle = normalized_relative_intersect_y * (math.pi / 4)
        ball_angle = math.pi - ball_angle + bounce_angle

    elif right_paddle_x <= ball_x + ball_radius <= right_paddle_x + PADDLE_WIDTH and right_paddle_y <= ball_y <= right_paddle_y + PADDLE_HEIGHT:
        ball_speed += 0.5
        relative_intersect_y = (right_paddle_y + PADDLE_HEIGHT / 2) - ball_y
        normalized_relative_intersect_y = relative_intersect_y / (PADDLE_HEIGHT / 2)
        bounce_angle = normalized_relative_intersect_y * (math.pi / 4)
        ball_angle = math.pi - ball_angle + bounce_angle

    if ball_x - ball_radius <= 0:
        right_score += 1
        ball_x, ball_y = WIDTH // 2, HEIGHT // 2
        ball_speed = BALL_SPEED
        ball_angle = random.uniform(-math.pi / 4, math.pi / 4)
    elif ball_x + ball_radius >= WIDTH:
        left_score += 1
        ball_x, ball_y = WIDTH // 2, HEIGHT // 2
        ball_speed = BALL_SPEED
        ball_angle = random.uniform(-math.pi / 4, math.pi / 4)

    if left_score >= max_score or right_score >= max_score:
        winner_shown_time = show_winner("Izquierdo" if left_score >= max_score else "Derecho")
        pygame.time.wait(1000)  # Esperar 3 segundos después de mostrar al ganador
        running = False

    pygame.draw.rect(win, WHITE, (left_paddle_x, left_paddle_y, PADDLE_WIDTH, PADDLE_HEIGHT))
    pygame.draw.rect(win, WHITE, (right_paddle_x, right_paddle_y, PADDLE_WIDTH, PADDLE_HEIGHT))
    pygame.draw.circle(win, WHITE, (ball_x, ball_y), ball_radius)
    update_score()

    pygame.display.flip()
    clock.tick(60)

    pygame.quit()
