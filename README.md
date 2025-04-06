# Bird Game

Ein einfaches Flappy Bird-Spiel, das in Python mit der Bibliothek Pygame entwickelt wurde. Das Spiel enthält einen fliegenden Vogel, der durch Röhren hindurch navigieren muss, ohne mit ihnen zu kollidieren oder auf den Boden zu fallen. Es gibt einen Highscore, der nach jedem Spiel gespeichert wird.

## Features

- Vogel, der durch Drücken der Leertaste (Space) springt.
- Röhren, die sich von rechts nach links bewegen.
- Kollisionsprüfung mit Röhren und Boden.
- Highscore-System, das den höchsten Punktestand speichert.
- Game-Over-Bildschirm mit der Möglichkeit, das Spiel durch Drücken der Taste "R" neu zu starten.
- Ansprechender Hintergrund und Vogel-Bilder.

## Installation

### Voraussetzungen
- Python 3.x
- Pygame-Bibliothek

### Schritte zur Installation

1. Klone das Repository auf deinen Computer:

    ```bash
    git clone https://github.com/Kaano69/Bird-game.git
    ```

2. Navigiere in das Projektverzeichnis:

    ```bash
    cd Bird-Game
    ```

3. Installiere die benötigten Abhängigkeiten (Pygame):

    ```bash
    pip install pygame
    ```

## Spiel starten
Um das Spiel zu starten, führe einfach die Python-Datei `main.py` aus:

```bash
python main.py
```

## Source Code

### Dieser Code wurde vollständig mit KI generiert
```
import pygame
import random
import os
import sys

# Initialisierung von pygame
pygame.init()

# Bildschirmgröße
WIDTH, HEIGHT = 400, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))

# Dynamische Ermittlung des Verzeichnisses, abhängig davon, ob das Spiel als Skript oder EXE ausgeführt wird
if getattr(sys, 'frozen', False):
    current_dir = os.path.dirname(sys.executable)  # Wenn als EXE, benutze das Verzeichnis der EXE
else:
    current_dir = os.path.dirname(__file__)  # Wenn als Skript, benutze das Verzeichnis des Skripts

# Spiel-Icon und Fenstertitel
icon = pygame.image.load(os.path.join(current_dir, 'Images', 'icon.png'))  # Lade das Icon-Bild aus dem Images-Ordner
pygame.display.set_icon(icon)  # Setze das Icon
pygame.display.set_caption('Bird Game')  # Setze den Titel des Fensters

# Farben
WHITE = (255, 255, 255)
GREEN = (0, 255, 0)
BLUE = (0, 0, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)

# Variablen für das Spiel
clock = pygame.time.Clock()
gravity = 0.25
jump_strength = -6
pipe_width = 60
pipe_gap = 150
pipe_velocity = 3

# Vogel-Startwerte
bird_x = 50
bird_y = HEIGHT // 2
bird_velocity = 0

# Bilder laden
bird_image = pygame.image.load(os.path.join(current_dir, 'Images', 'Bird.PNG'))  # Vogelbild aus Images-Ordner
bird_image = pygame.transform.scale(bird_image, (40, 40))  # Bildgröße anpassen (falls nötig)
background_image = pygame.image.load(os.path.join(current_dir, 'Images', 'background.png'))  # Hintergrundbild aus Images-Ordner
background_image = pygame.transform.scale(background_image, (WIDTH, HEIGHT))  # Bildgröße anpassen

# Highscore-Datei laden oder erstellen
highscore_file = "highscore.txt"

# Wenn die Highscore-Datei nicht existiert, erstelle sie mit einem Wert von 0
if not os.path.exists(highscore_file):
    with open(highscore_file, "w") as file:
        file.write("0")

# Highscore aus der Datei lesen
def get_highscore():
    with open(highscore_file, "r") as file:
        return int(file.read())

# Highscore in die Datei schreiben
def save_highscore(new_highscore):
    with open(highscore_file, "w") as file:
        file.write(str(new_highscore))

# Funktion für das Neustarten des Spiels
def restart_game():
    global bird_x, bird_y, bird_velocity, pipes, score, passed_pipes, game_over, highscore
    bird_y = HEIGHT // 2
    bird_velocity = 0
    pipes = []  # Röhren zurücksetzen
    score = 0
    passed_pipes = []  # Bereits durchflogene Röhren zurücksetzen
    game_over = False
    highscore = get_highscore()  # Lade den Highscore neu, wenn das Spiel neu gestartet wird

# Funktion für das Zeichnen des Game-Over Menüs
def draw_game_over_menu():
    font = pygame.font.SysFont("Arial", 50)
    game_over_text = font.render("GAME OVER!", True, RED)
    screen.blit(game_over_text, (WIDTH // 4, HEIGHT // 3))

    # Button "Press R to Restart"
    button_rect = pygame.Rect(WIDTH // 4, HEIGHT // 2, WIDTH // 2, 50)  # Button rechteck
    pygame.draw.rect(screen, BLACK, button_rect)  # Button zeichnen

    # Schrift für den "Press R to Restart" Text mit kleinerer Schriftgröße
    small_font = pygame.font.SysFont("Arial", 30)  # Kleinere Schriftgröße
    restart_text = small_font.render("Press 'R' to Restart", True, WHITE)
    screen.blit(restart_text, (WIDTH // 4 + 10, HEIGHT // 2 + 10))  # Text im Button

    # Highscore anzeigen
    highscore_font = pygame.font.SysFont("Arial", 30)
    highscore_text = highscore_font.render(f"Highscore: {highscore}", True, BLACK)
    screen.blit(highscore_text, (WIDTH // 4 + 10, HEIGHT // 2 + 70))  # Highscore unter dem Button

# Spiel Schleife
running = True
score = 0
pipes = []  # Röhren initialisieren
passed_pipes = []  # Liste der bereits durchflogenen Röhren
game_over = False  # Spielstatus
highscore = get_highscore()  # Lade den Highscore

while running:
    screen.fill(WHITE)

    # Hintergrund anzeigen
    screen.blit(background_image, (0, 0))

    # Ereignisse und Tasteneingaben
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE and not game_over:
                bird_velocity = jump_strength
            elif event.key == pygame.K_r and game_over:
                restart_game()

    # Nur Bewegung und Kollision prüfen, wenn das Spiel nicht vorbei ist
    if not game_over:
        # Vogelbewegung
        bird_velocity += gravity
        bird_y += bird_velocity
        
        # Vogel darf den Bildschirm nicht verlassen (oben)
        if bird_y <= 0:
            bird_y = 0
        
        # Kollisionsprüfung mit dem Boden (Spiel endet, wenn Vogel auf dem Boden ist)
        if bird_y >= HEIGHT - 40:  # Vogelgröße von 40x40
            bird_y = HEIGHT - 40
            game_over = True  # Das Spiel endet, wenn der Vogel den Boden berührt

        bird_rect = pygame.Rect(bird_x, bird_y, 40, 40)

        # Röhren erzeugen
        if len(pipes) == 0 or pipes[-1][0].x < WIDTH - 200:
            height = random.randint(100, HEIGHT - pipe_gap - 100)
            top_pipe = pygame.Rect(WIDTH, 0, pipe_width, height)
            bottom_pipe = pygame.Rect(WIDTH, height + pipe_gap, pipe_width, HEIGHT - height - pipe_gap)
            pipes.append((top_pipe, bottom_pipe))

        # Röhren bewegen und anzeigen
        for top_pipe, bottom_pipe in pipes:
            top_pipe.x -= pipe_velocity
            bottom_pipe.x -= pipe_velocity
            pygame.draw.rect(screen, GREEN, top_pipe)  # Obere Röhre
            pygame.draw.rect(screen, GREEN, bottom_pipe)  # Untere Röhre

        # Kollision mit den Röhren
        for top_pipe, bottom_pipe in pipes:
            if bird_rect.colliderect(top_pipe) or bird_rect.colliderect(bottom_pipe):
                game_over = True  # Das Spiel endet, wenn der Vogel mit einer Röhre kollidiert

        # Punkte zählen, wenn der Vogel zwischen den Röhren durchfliegt
        for top_pipe, bottom_pipe in pipes:
            if top_pipe.x + pipe_width < bird_x and (top_pipe, bottom_pipe) not in passed_pipes:
                score += 1
                passed_pipes.append((top_pipe, bottom_pipe))

        # Entferne Röhren, die aus dem Bildschirm verschwunden sind
        pipes = [(top_pipe, bottom_pipe) for top_pipe, bottom_pipe in pipes if top_pipe.x > -pipe_width]

        # Vogel anzeigen
        screen.blit(bird_image, bird_rect)

        # Punktestand anzeigen
        font = pygame.font.SysFont("Arial", 30)
        score_text = font.render(f"Score: {score}", True, (0, 0, 0))
        screen.blit(score_text, (10, 10))

    # Wenn das Spiel vorbei ist, Game Over Menü anzeigen
    if game_over:
        if score > highscore:
            highscore = score  # Aktualisiere den Highscore, wenn der aktuelle Score höher ist
            save_highscore(highscore)  # Speichere den neuen Highscore in der Datei
        draw_game_over_menu()

    # Bildschirm aktualisieren
    pygame.display.flip()

    # Frame Rate
    clock.tick(60)

# Spiel beenden
pygame.quit()

