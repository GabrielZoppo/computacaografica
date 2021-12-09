# Computação Gráfica

## Código do Trabalho da segunda Avaliação  - Movimentação da aranha em 2D

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
