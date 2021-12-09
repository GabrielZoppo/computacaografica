# Computação Gráfica
## Primeira Avaliação: 
* **Código do primeiro trabalho - rotação de uma casa:**

~~~PYTHON
# Instale essas bibliotecas se estiver utilizando o colab
!pip install dash
!pip install flask-ngrok
!pip install jupyter-dash
!pip install Wand
!apt install imagemagick
!pip install glumpy
!pip install numpy

# importando as bibliotecas
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd
from flask import *
from wand.color import Color
from glumpy import app, gl, glm, gloo, data

# Definindo os vertices da casa
vertices0 = np.array([[ 0, 4,  4,  0,  0], #x
                     [ 2, 2,  0,  0,  2], #y
                     [ 0, 0,  0,  0,  0] #z=1
])

x, y, z =vertices0
plt.plot(x, y, color = 'blue')#original em azul
plt.axis('equal')
plt.show()

# Definindo os vertices do telhado

vertices1 = np.array([[ 0, 2,  4, 0, 0], #x
                      [ 2, 3  ,  2, 2, 2], #y
                      [ 0, 0  ,  0, 0, 0] #z=1
])

x1, y1, z1 =vertices1
plt.plot(x1, y1, color = 'blue')#original em azul
plt.axis('equal')
plt.show()

# Definindo os Vertice da porta

vertices2 = np.array([[ 3, 3.5  ,  3.5, 3, 3], #x
                      [ 1  , 1  ,  0, 0, 1], #y
                      [ 0  , 0  ,  0, 0, 0] #z=1
])

x2, y2, z2 =vertices2
plt.plot(x2, y2, color = 'blue')#original em azul
plt.axis('equal')
plt.show()

# Definindo os Vertices da Janela

vertices3 = np.array([[ 0.5, 0.5  ,  1.5, 1.5, 0.5], #x
                      [ 0.5  , 1.5  ,  1.5, 0.5, 0.5], #y
                      [ 0  , 0  ,  0, 0, 0] #z=1
])

x3, y3, z3 =vertices3
plt.plot(x3, y3, color = 'blue')#original em azul
plt.axis('equal')
plt.show()

# Rotacionar 90° e translada o mesmo 2unidades na direção de x e 0 unidades na direção de y

T = np.array([
              [np.cos(np.pi/2), -np.sin(np.pi/2), 2],
              [np.sin(np.pi/2), np.cos(np.pi/2), 0]
])

plt.plot(x, y, x1, y1, x2, y2, x3, y3, color = 'blue')
plt.axis('equal')
plt.show()

new_x,new_y = np.matmul(T,vertices0)
new_x1,new_y1 = np.matmul(T,vertices1)
new_x2,new_y2 = np.matmul(T,vertices2)
new_x3,new_y3 = np.matmul(T,vertices3)
plt.plot(new_x, new_y, new_x1, new_y1, new_x2, new_y2, new_x3, new_y3, color = 'blue')
plt.axis('equal')
plt.show()

~~~
* **Código do segundo trabalho  - Tipos de materiais:**

~~~Python
import glfw
from OpenGL.GL import *
from OpenGL.GL.shaders import compileProgram, compileShader
import numpy as np
import pyrr
from PIL import Image

vertex_src = """
# version 330
layout(location = 0) in vec3 a_position;
layout(location = 1) in vec3 a_color;
layout(location = 2) in vec2 a_texture;
uniform mat4 rotation;
out vec3 v_color;
out vec2 v_texture;
void main()
{
    gl_Position = rotation * vec4(a_position, 1.0);
    v_color = a_color;
    v_texture = a_texture;
    
    //v_texture = 1 - a_texture;                      // Flips the texture vertically and horizontally
    //v_texture = vec2(a_texture.s, 1 - a_texture.t); // Flips the texture vertically
}
"""

fragment_src = """
# version 330
in vec3 v_color;
in vec2 v_texture;
out vec4 out_color;
uniform sampler2D s_texture;
void main()
{
    out_color = texture(s_texture, v_texture); // * vec4(v_color, 1.0f);
}
"""

# glfw callback functions
def window_resize(window, width, height):
    glViewport(0, 0, width, height)

# initializing glfw library
if not glfw.init():
    raise Exception("glfw can not be initialized!")

# creating the window
window = glfw.create_window(1280, 720, "My OpenGL window", None, None)

# check if window was created
if not window:
    glfw.terminate()
    raise Exception("glfw window can not be created!")

# set window's position
glfw.set_window_pos(window, 400, 200)

# set the callback function for window resize
glfw.set_window_size_callback(window, window_resize)

# make the context current
glfw.make_context_current(window)

vertices = [-0.5, -0.5,  0.5,  1.0, 0.0, 0.0,  0.0, 0.0,
             0.5, -0.5,  0.5,  0.0, 1.0, 0.0,  1.0, 0.0,
             0.5,  0.5,  0.5,  0.0, 0.0, 1.0,  1.0, 1.0,
            -0.5,  0.5,  0.5,  1.0, 1.0, 1.0,  0.0, 1.0,

            -0.5, -0.5, -0.5,  1.0, 0.0, 0.0,  0.0, 0.0,
             0.5, -0.5, -0.5,  0.0, 1.0, 0.0,  1.0, 0.0,
             0.5,  0.5, -0.5,  0.0, 0.0, 1.0,  1.0, 1.0,
            -0.5,  0.5, -0.5,  1.0, 1.0, 1.0,  0.0, 1.0,

             0.5, -0.5, -0.5,  1.0, 0.0, 0.0,  0.0, 0.0,
             0.5,  0.5, -0.5,  0.0, 1.0, 0.0,  1.0, 0.0,
             0.5,  0.5,  0.5,  0.0, 0.0, 1.0,  1.0, 1.0,
             0.5, -0.5,  0.5,  1.0, 1.0, 1.0,  0.0, 1.0,

            -0.5,  0.5, -0.5,  1.0, 0.0, 0.0,  0.0, 0.0,
            -0.5, -0.5, -0.5,  0.0, 1.0, 0.0,  1.0, 0.0,
            -0.5, -0.5,  0.5,  0.0, 0.0, 1.0,  1.0, 1.0,
            -0.5,  0.5,  0.5,  1.0, 1.0, 1.0,  0.0, 1.0,

            -0.5, -0.5, -0.5,  1.0, 0.0, 0.0,  0.0, 0.0,
             0.5, -0.5, -0.5,  0.0, 1.0, 0.0,  1.0, 0.0,
             0.5, -0.5,  0.5,  0.0, 0.0, 1.0,  1.0, 1.0,
            -0.5, -0.5,  0.5,  1.0, 1.0, 1.0,  0.0, 1.0,

             0.5,  0.5, -0.5,  1.0, 0.0, 0.0,  0.0, 0.0,
            -0.5,  0.5, -0.5,  0.0, 1.0, 0.0,  1.0, 0.0,
            -0.5,  0.5,  0.5,  0.0, 0.0, 1.0,  1.0, 1.0,
             0.5,  0.5,  0.5,  1.0, 1.0, 1.0,  0.0, 1.0]

indices = [0,  1,  2,  2,  3,  0,
           4,  5,  6,  6,  7,  4,
           8,  9, 10, 10, 11,  8,
          12, 13, 14, 14, 15, 12,
          16, 17, 18, 18, 19, 16,
          20, 21, 22, 22, 23, 20]

vertices = np.array(vertices, dtype=np.float32)
indices = np.array(indices, dtype=np.uint32)

shader = compileProgram(compileShader(vertex_src, GL_VERTEX_SHADER), compileShader(fragment_src, GL_FRAGMENT_SHADER))

# Vertex Buffer Object
VBO = glGenBuffers(1)
glBindBuffer(GL_ARRAY_BUFFER, VBO)
glBufferData(GL_ARRAY_BUFFER, vertices.nbytes, vertices, GL_STATIC_DRAW)

# Element Buffer Object
EBO = glGenBuffers(1)
glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO)
glBufferData(GL_ELEMENT_ARRAY_BUFFER, indices.nbytes, indices, GL_STATIC_DRAW)

glEnableVertexAttribArray(0)
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, vertices.itemsize * 8, ctypes.c_void_p(0))

glEnableVertexAttribArray(1)
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, vertices.itemsize * 8, ctypes.c_void_p(12))

glEnableVertexAttribArray(2)
glVertexAttribPointer(2, 2, GL_FLOAT, GL_FALSE, vertices.itemsize * 8, ctypes.c_void_p(24))

texture = glGenTextures(1)
glBindTexture(GL_TEXTURE_2D, texture)

# Set the texture wrapping parameters
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT)
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT)
# Set texture filtering parameters
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR)
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR)

# load image
image = Image.open("d:/Users/usuario/Documents/tijolo.png")
image = image.transpose(Image.FLIP_TOP_BOTTOM)
img_data = image.convert("RGBA").tobytes()
# img_data = np.array(image.getdata(), np.uint8) # second way of getting the raw image data
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGBA, image.width, image.height, 0, GL_RGBA, GL_UNSIGNED_BYTE, img_data)

glUseProgram(shader)
glClearColor(0, 0.1, 0.1, 1)
glEnable(GL_DEPTH_TEST)
glEnable(GL_BLEND)
glBlendFunc(GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA)

rotation_loc = glGetUniformLocation(shader, "rotation")

# the main application loop
while not glfw.window_should_close(window):
    glfw.poll_events()

    glClear(GL_COLOR_BUFFER_BIT|GL_DEPTH_BUFFER_BIT)

    rot_x = pyrr.Matrix44.from_x_rotation(0.5 * glfw.get_time())
    rot_y = pyrr.Matrix44.from_y_rotation(0.8 * glfw.get_time())

    glUniformMatrix4fv(rotation_loc, 1, GL_FALSE, pyrr.matrix44.multiply(rot_x, rot_y))

    glDrawElements(GL_TRIANGLES, len(indices), GL_UNSIGNED_INT, None)

    glfw.swap_buffers(window)

# terminate glfw, free up allocated resources
glfw.terminate()
~~~
## Segunda Avaliação:
* **Código do primeiro trabalho referente à segunda avaliação  - Movimentação da aranha em 2D usando o clique do mouse:**

~~~PYTHON
# Importação da biblioteca para fazer as animações
import pygame as pg
from pygame.locals import *
import os

# Cria a janela para colocarmos a aranha
pg.init()
win = pg.display.set_mode((1420, 880))
pg.display.set_caption("Moving Spyder!")

# Setando algumas cores para usarmos na animação
black = (0, 0, 0)
white = (255, 255, 255)

# Função responsável por criar a perna da aranha
def definePerna(tamanho, cor1, cor2, angulo, pos):
    perna = pg.Surface(tamanho)
    perna.fill(cor1)
    perna.set_colorkey(cor2)
    pg.draw.ellipse(perna, cor1, (10, 0, 50, 7))
    win.blit(pg.transform.rotate(perna, angulo), pos)

# Função responsável por criar o corpo da aranha
def defineCorpo(cor, posicao, tamanho):
    corpo_rect = pg.draw.circle(win, cor, posicao, tamanho)
    corpo = pg.Surface(corpo_rect.size)
    corpo.fill(cor)
    corpo.set_colorkey(black)

# Vetor com todas as posições x e y das pernas
pos =[
    [( 25,  25), ( 49,  65), ( 25,  25)],
    [( 55,  65), ( 65,  85), ( 55,  65)],
    [( 35, 165), ( 35, 165), ( 25, 165)],
    [(  1, 165), ( 16, 165), (  7, 165)],
    [( 47, 185), ( 42, 185), ( 37, 185)],
    [( 12, 185), ( 26, 207), (  2, 185)],
    [( 55, 215), ( 55, 215), ( 45, 215)],
    [(  4, 215), ( 33, 215), ( 38, 225)],
    [(170,  25), (170,  25), (163,  65)],
    [(147,  60), (147,  60), (150,  85)],
    [(145, 165), (145, 165), (145, 135)],
    [(187, 165), (192, 165), (175, 135)],
    [(125, 185), (125, 185), (155, 185)],
    [(178, 185), (188, 185), (190, 192)],
    [(145, 215), (145, 215), (155, 215)],
    [(170, 215), (170, 230), (180, 215)],
]

# Vetor com os angulos para cada uma das partes das 8 pernas
ang = [
    [ 306, 307, 306],
    [ 105, 105, 105],
    [ 160, 140, 165],
    [  60,  40,  70],
    [ 160, 200, 165],
    [  60,  70,  60],
    [ 160, 180,  21],
    [  60,  75,  85],
    [  54,  54,  54],
    [ 255, 255, 255],
    [-160,-170,  40],
    [ -60, -60, 140],
    [-160,-160, 350],
    [ -60, -50, 290],
    [-160,-210,   0],
    [ -60, -85, 280],
]

# Vetor com o tamanho de cada um dos pedaços das pernas
tam = [
    [( 50,  5), ( 25, 5), ( 50 ,5)],
    [(100,  5), ( 50, 5), (100 ,5)],
    [( 50,  5), ( 40, 5), ( 50, 5)],
    [( 70,  5), ( 25, 5), ( 45, 5)],
    [( 62,  5), ( 70, 5), ( 70, 5)],
    [( 70,  5), ( 50, 5), ( 65, 5)],
    [( 31,  5), ( 31, 5), ( 30, 5)],
    [(101,  5), ( 70, 5), ( 90, 5)],
    [( 50,  5), ( 50, 5), ( 25 ,5)],
    [(100,  5), (100, 5), ( 50 ,5)],
    [( 50,  5), ( 50, 5), ( 40, 5)],
    [( 70,  5), ( 45, 5), ( 25, 5)],
    [( 62,  5), ( 70, 5), ( 40, 5)],
    [( 70,  5), ( 40, 5), ( 50, 5)],
    [( 30,  5), ( 30, 5), ( 31, 5)],
    [(100,  5), ( 90, 5), ( 80, 5)],
]

# Vetor de posição para o corpo da aranha
posicao = [
    (115, 145),
    (115, 195),
]

# Vetor do tamanho  do corpo da aranha
tamanho = [
    40,
    50,
]

# Faz a criação das fotos das três posições da pata da aranha
win = pg.display.set_mode((250, 300))
for j in range(3):
    win.fill((0,0,0))
    for i in range(len(tam)):
        pernas = definePerna(tam[i][j], white, black, ang[i][j] , pos[i][j])

    for i in range(len(tamanho)):
        corpos = defineCorpo(white, posicao[i], tamanho[i])

    pg.image.save(win, 'p%d.png'%j)

win = pg.display.set_mode((1700, 900))
ang = 0
j = 0
run = True

# Loop de eventos do pygame

while run:
    pg.time.delay(100)

    for event in pg.event.get():
        # If responsável por rotacionar a imagem da aranha
        if event.type == pg.KEYDOWN:
            win.fill((0,0,0))
            if event.key == pg.K_LEFT:
                ang = ang +45
            elif event.key == pg.K_RIGHT:
                ang = ang -45
                
            # Fecha a janela apertando o ESC    
            elif event.key == pg.K_ESCAPE:
                run = False
            imagem = pg.transform.rotate(image, ang)
            win.blit(imagem, rect)

        # If responsável por movimentar a imagem da aranha na direção do clique do mouse
        if event.type == pg.MOUSEBUTTONDOWN:
            npos = pg.mouse.get_pos()

            if j < 2:
                j+=1
            else:
                j = 0

            win.fill((0,0,0))

            image = pg.image.load('p%d.png'%j).convert_alpha()

            rect = image.get_rect()
            
            rect.move_ip(npos[0]-rect.size[0]/2, npos[1]-rect.size[1]/2)

            imagem = pg.transform.rotate(image, ang)
            win.blit(imagem, rect)

        # faz o refresh da janela
        pg.display.update()

# Finaliza o precesso pygame
pg.quit()
~~~

* **Código do segundo trabalho referente à segunda avaliação  - Movimentação da aranha em 3D usando o teclado*
~~~PYTHON


~~~
