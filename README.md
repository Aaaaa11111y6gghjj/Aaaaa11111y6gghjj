import pygame
from pygame.locals import *
from OpenGL.GL import *
from OpenGL.GLU import *
import numpy as np
import math

# Initialize Pygame
pygame.init()

# Screen dimensions
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT), DOUBLEBUF | OPENGL)
pygame.display.set_caption("3D Car Game")

# OpenGL Settings
glClearColor(0.5, 0.7, 1.0, 1)  # Background color
gluPerspective(45, WIDTH / HEIGHT, 0.1, 1000.0)  # Camera perspective
glTranslatef(0.0, -1.5, -10)  # Initial camera position
glEnable(GL_DEPTH_TEST)

# Car properties
car_pos = np.array([0.0, 0.0, 0.0])
car_angle = 0
car_speed = 0
acceleration = 0.02
deceleration = 0.05
max_speed = 1.0
turn_speed = 2

# Draw car (a simple wireframe cube for representation)
def draw_car():
    glPushMatrix()
    glTranslatef(*car_pos)
    glRotatef(car_angle, 0, 1, 0)
    glBegin(GL_QUADS)
    
    glColor3f(1, 0, 0)  # Red color
    # Front face
    glVertex3f(-1, -0.5, 1)
    glVertex3f(1, -0.5, 1)
    glVertex3f(1, 0.5, 1)
    glVertex3f(-1, 0.5, 1)
    
    glColor3f(0, 1, 0)  # Green color
    # Back face
    glVertex3f(-1, -0.5, -1)
    glVertex3f(1, -0.5, -1)
    glVertex3f(1, 0.5, -1)
    glVertex3f(-1, 0.5, -1)
    
    glColor3f(0, 0, 1)  # Blue color
    # Top face
    glVertex3f(-1, 0.5, -1)
    glVertex3f(1, 0.5, -1)
    glVertex3f(1, 0.5, 1)
    glVertex3f(-1, 0.5, 1)
    
    glEnd()
    glPopMatrix()

# Main loop
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # Movement controls
    keys = pygame.key.get_pressed()
    if keys[pygame.K_UP]:
        car_speed = min(car_speed + acceleration, max_speed)
    elif keys[pygame.K_DOWN]:
        car_speed = max(car_speed - acceleration, -max_speed)
    else:
        car_speed = max(0, car_speed - deceleration) if car_speed > 0 else min(0, car_speed + deceleration)

    if keys[pygame.K_LEFT]:
        car_angle += turn_speed if car_speed != 0 else 0
    if keys[pygame.K_RIGHT]:
        car_angle -= turn_speed if car_speed != 0 else 0

    # Update car position
    car_pos[0] += math.sin(math.radians(car_angle)) * car_speed
    car_pos[2] += math.cos(math.radians(car_angle)) * car_speed

    # Clear screen and render car
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT)
    draw_car()
    pygame.display.flip()
    clock.tick(FPS)

pygame.quit()
