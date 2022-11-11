# platformer-game
import pygame
pygame.init()  
pygame.display.set_caption("easy platformer")  # sets the window title
screen = pygame.display.set_mode((800, 800))  # creates game screen
screen.fill((0,0,0))
clock = pygame.time.Clock() #set up clock
gameover = False #variable to run our game loop

#CONSTANTS
LEFT=0
RIGHT=1
UP = 2
DOWN = 3

#load images
background2 = pygame.image.load('space.png')
background2 = pygame.transform.scale(background2,(800,800))


#player variables
xpos = 500 #xpos of player
ypos = 200 #ypos of player
vx = 0 #x velocity of player
vy = 0 #y velocity of player
keys = [False, False, False, False] #this list holds whether each key has been pressed
isOnGround = False #this variable stops gravity from pulling you down more when on a platform
health = 100 
#enemy variables
#expos = 200
# eypos = 630
#timer = 0
enemy1 = [200 ,630 ,0] #xpos, ypos, ticker
enemy2 = [400,780,0]

#---------------------------------------function definition--------------
def enemyMove(enemyInfo):
    #print(enemyInfo)
    enemyInfo[2]+=1 #increment timer
    if enemyInfo[2] <= 60:#move right 60 ticks
        enemyInfo[0] +=1 #move right
    elif enemyInfo[2] <= 120:#move left 60 ticks
        enemyInfo[0] -=1 #move left
    else:
        enemyInfo[2] = 0 #reset timer
        
def enemyCollide(enemyInfo, playerX, playerY):
    if playerX+20>enemyInfo[0]:#right side of player, left side of enemy
        if playerX < enemyInfo[0]+20: #left side of player, right side of enemy
            if playerY < enemyInfo[1]+20: #top of the player, bottom of the enemy
                if playerY+20 > enemyInfo[1]:
                    return True
    else:
        return False
#end function deinition---------------------------------------------------

#SOUND--------------------------------------------------------------------
jump = pygame.mixer.Sound('jump.ogg')#load in sound effect
music = pygame.mixer.music.load('music.ogg')#load in background music
pygame.mixer.music.play(-1)#start background music
#-------------------------------------------------------------------------

while not gameover and health > 0: #GAME LOOP############################################################
    clock.tick(60) #FPS
    
 #Input Section------------------------------------------------------------
    for event in pygame.event.get(): #quit game if x is pressed in top corner
        if event.type == pygame.QUIT:
            gameover = True
     
        if event.type == pygame.KEYDOWN: #keyboard input
            if event.key == pygame.K_LEFT:
                keys[LEFT]=True
               
            elif event.key == pygame.K_RIGHT:
                keys[RIGHT]=True

            elif event.key == pygame.K_UP:
                keys[UP]=True
        elif event.type == pygame.KEYUP:
            if event.key == pygame.K_LEFT:
                keys[LEFT]=False
               
            elif event.key == pygame.K_RIGHT:
                keys[RIGHT]=False

            elif event.key == pygame.K_UP:
                keys[UP]=False
         
    #physics section--------------------------------------------------------------------
    #LEFT MOVEMENT
    if keys[LEFT]==True:
        vx=-3
        direction = LEFT
       
    elif keys[RIGHT]==True:
        vx=3
        direction = RIGHT

    #turn off velocity
    else:
        vx = 0
        #JUMPING
    if keys[UP] == True and isOnGround == True: #only jump when on the ground
        vy = -8
        isOnGround = False
        direction = UP
   
   
    #function call for enemy movement
    enemyMove(enemy1)
    enemyMove(enemy2) 
       
    #check collision withe enemies 
    if enemyCollide(enemy1, xpos, ypos):
        print("hit!")
        health -= 10
    if enemyCollide(enemy2, xpos, ypos):
        print("hit!")
        health -= 10
    
    #COLLISION with platforms
    if xpos+20>100 and xpos<200 and ypos+40 >750 and ypos+40 <770:
        ypos = 750-40
        isOnGround = True
        vy = 0
    elif xpos+20>200 and xpos<300 and ypos+40 >650 and ypos+40 <670:
        ypos = 650-40
        isOnGround = True
        vy = 0
    #third platform (400, 570)
    elif xpos+20>400 and xpos<550 and ypos+40 >570 and ypos+40 <590:
        ypos = 570-40
        isOnGround = True
        vy = 0
        
    else:
        isOnGround = False


   
    #stop falling if on bottom of game screen
    if ypos > 760:
        isOnGround = True
        vy = 0
        ypos = 760
   
    #gravity
    if isOnGround == False:
        vy+=.2 #notice this grows over time, aka ACCELERATION
   

    #update player position
    xpos+=vx
    ypos+=vy
  
    # RENDER Section--------------------------------------------------------------------------------
            
    screen.blit(background2,(0,0)) #clear screen between loops

    #draw player
    pygame.draw.rect(screen, (100, 200, 100), (xpos, ypos, 20, 40))
    
    #draw enemy
    pygame.draw.rect(screen, (255, 255, 255), (enemy1[0], enemy1[1], 20, 20))
    pygame.draw.rect(screen, (255, 255, 255), (enemy2[0], enemy2[1], 20, 20))
    
    #first platform
    pygame.draw.rect(screen, (200, 0, 100), (100, 750, 100, 20))
    
    #second platform
    pygame.draw.rect(screen, (100, 0, 200), (200, 650, 100, 20))
    
    #third platform
    pygame.draw.rect(screen, (200, 0, 200), (400, 570, 150, 20))    
    pygame.display.flip()#this actually puts the pixel on the screen
    
#end game loop------------------------------------------------------------------------------
if health <=0:
    print("game over, you died")
pygame.quit()
