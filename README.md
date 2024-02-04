# Tic-Tac-Toe-Game
This is a very simple Tic Tac Toe game. Here you have to match 3 X's, or else the opponent will win. Whoever reaches the goal first, wins.


#include <windows.h>
#include <GL/glut.h>
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include <string.h>

// We use this part for lighting
GLfloat LightAmbient[] = {1.0f, 0.89, 0.76f}; //It's used to store the ambient light color information
GLfloat LightDiffuse[] = {1.0f, 1.0f, 1.0f, 1.0f}; //It's used to store the diffuse light color information
GLfloat LightPosition[] = {0.0, 1.0, 0.0, 1.0};
GLfloat LightSpecular[] = {1.0, 1.0, 1.0, 1.0};
GLfloat mat_specular[] = {1.0, 1.0, 1.0, 1.0};
GLfloat mat_ambient[] = {1.0, 1.0, 1.0, 1.0};
GLfloat mat_diffuse[] = {1.0, 1.0, 1.0, 1.0};
GLfloat mat_shininess = 5.0;

int game = 0;
// We can call our mouse variables: Win = windows size, mouse = mouse position
int mouse_x, mouse_y, Win_x, Win_y, object_select;

int sign, gameboxes;

int player, computer, win, start_game;

static int box[8][3] = {{0, 1, 2}, {3, 4, 5}, {6, 7, 8}, {0, 3, 6}, {1, 4, 7}, {2, 5, 8}, {0, 4, 8}, {2, 4, 6}};


int box_map[9];
int object_map[9][2] = {{-6, 6}, {0, 6}, {6, 6}, {-6, 0}, {0, 0}, {6, 0}, {-6, -6}, {0, -6}, {6, -6}}; // quadric pointer for build our X
GLUquadricObj *Cylinder;

//We now begin our game routine
void init_game(void)
{
    int i;
    for (i = 0; i < 9; i++) //This loop is used to initialize elements of an array called box_map.
    {
        box_map[i] = 0;
    }

    win = 0; //the game has not been won at the start.
    start_game = 1; //the game has started.
}

// Check for three in a row/column/diagonal

int check_move(void)
{
    int i, t = 0;
    //Check for three in a row
    for (i = 0; i < 8; i++)
    {
        t = box_map[box[i][0]] + box_map[box[i][1]] + box_map[box[i][2]];
        if ((t == 3) || (t == -3))
        {
            gameboxes = i;
            return (1);
        }
    }
    t = 0; //to reset it for the next round of checking.
    // We now check it for draw game
    for (i = 0; i < 8; i++)
    {
        t = t + abs(box_map[box[i][0]]) + abs(box_map[box[i][1]]) + abs(box_map[box[i][2]]);
    }
    if (t == 24) //it likely indicates that the game has reached a certain condition, possibly a draw or a specific state (win or lose) of the game.
        return (2); //the function returns 2, which may signify a particular result or state of the game (win, draw or lose).
    return (0);
}
// We have to block other player
int blocking_win(void)
{
    int i, t;
    for (i = 0; i < 8; i++)
    {
        t = box_map[box[i][0]] + box_map[box[i][1]] + box_map[box[i][2]];
        if ((t == 2) || (t == -2))
        {
            // Find empty
            if (box_map[box[i][0]] == 0) //If this condition is true, it means that the box represented by box[i][0] is empty and available for a move.
                box_map[box[i][0]] = computer; //This likely means that the computer is making a move by occupying the box represented by box[i][0].
            if (box_map[box[i][1]] == 0) // If any of these elements are empty (i.e., their corresponding box_map value is 0), the computer sets them to its value, which signifies its move.
                box_map[box[i][1]] = computer;
            if (box_map[box[i][2]] == 0)
                box_map[box[i][2]] = computer;
            return (1);
        }
    }
    return (0);
}

// Here we check it for a free space corner
int check_column(void)
{
    int i;
    if (box_map[0] == 0)
    {
        box_map[0] = computer;
        i = 1;
        return (1);
    }
    if (box_map[2] == 0)
    {
        box_map[2] = computer;
        i = 1;
        return (1);
    }
    if (box_map[6] == 0)
    {
        box_map[6] = computer;
        i = 1;
        return (1);
    }
    if (box_map[8] == 0)
    {
        box_map[8] = computer;
        i = 1;
        return (1);
    }
    return (0);
}

 // Here we check it for a free space row
int check_row(void)
{
    if (box_map[4] == 0)
    {
        box_map[4] = computer;
        return (1);
    }
    if (box_map[1] == 0)
    {
        box_map[1] = computer;
        return (1);
    }
    if (box_map[3] == 0)
    {
        box_map[3] = computer;
        return (1);
    }
    if (box_map[5] == 0)
    {
        box_map[5] = computer;
        return (1);
    }
    if (box_map[7] == 0)
    {
        box_map[7] = computer;
        return (1);
    }
    return (0);
}

// Logic for computer's turn
int computer_move()
{
    if (blocking_win() == 1)
        return (1);
    if (check_row() == 1)
        return (1);
    if (check_column() == 1)
        return (1);
    return (0);
}
// We use this to put text on the screen
void Sprint (int x, int y, char *st)
{
    int l, i;
    l = strlen(st);         // see how many characters are in text string.
    glRasterPos2i( x, y); // location to start printing text
    for (i = 0; i < l; i++) // loop until i is greater then l
    {
        glutBitmapCharacter(GLUT_BITMAP_TIMES_ROMAN_24, st[i]); // Print a character on the screen
    }
}

static void TimeEvent(int te)
{
    sign++;
    if (sign > 360)
        sign = 180;
    glutPostRedisplay();
    glutTimerFunc(8, TimeEvent, 1);
}

void init(void)
{
    glClearColor(0.0, 0.0, 0.0, 0.0);
    glShadeModel(GL_SMOOTH);
    glEnable(GL_DEPTH_TEST);

    // Here we use lighting added to scene
    glLightfv(GL_LIGHT1, GL_AMBIENT, LightAmbient);
    glLightfv(GL_LIGHT1, GL_DIFFUSE, LightDiffuse);
    glLightfv(GL_LIGHT1, GL_SPECULAR, LightSpecular);
    glLightfv(GL_LIGHT1, GL_POSITION, LightPosition);
    glMaterialfv(GL_FRONT, GL_SPECULAR, mat_specular);
    glMaterialfv(GL_FRONT, GL_AMBIENT, mat_ambient);
    glMaterialfv(GL_FRONT, GL_DIFFUSE, mat_diffuse);
    glMaterialf(GL_FRONT, GL_SHININESS, mat_shininess);
    glShadeModel(GL_SMOOTH);
    glEnable(GL_NORMALIZE);
    glDisable(GL_LIGHTING); // Turn on lighting
    glEnable(GL_LIGHT1);   // Turn on light 1
    start_game = 0;
    win = 0;

    // We create a new quadric
    Cylinder = gluNewQuadric();
    gluQuadricDrawStyle(Cylinder, GLU_FILL);
    gluQuadricNormals(Cylinder, GLU_SMOOTH);
    gluQuadricOrientation(Cylinder, GLU_OUTSIDE);
}
void Draw_O(int x, int y, int z, int a)
{
    glPushMatrix();
    glColor3f(0.54, 0.16, 0.88);
    glTranslatef(x, y, z);
    glutSolidTorus(0.3, 1.0, 10, 300);
    glPopMatrix();
}
void Draw_X(int x, int y, int z, int a)
{
    glPushMatrix();
    glColor3f(0, 0.98, 0.60);
    glTranslatef(x, y, z);
    glPushMatrix();
    glRotatef(90, 0, 1, 0);
    glRotatef(45, 1, 0, 0);
    glTranslatef(0, 0, -2);
    gluCylinder(Cylinder, 0.3, 0.3, 4, 10, 16);
    glPopMatrix();
    glPushMatrix();
    glRotatef(90, 0, 1, 0);
    glRotatef(315, 1, 0, 0);
    glTranslatef(0, 0, -2);
    gluCylinder(Cylinder, 0.3, 0.3, 4, 10, 16);
    gluCylinder(Cylinder, 0.3, 0.3, 4, 10, 16);
    glPopMatrix();
    glPopMatrix();
}
// We design our project outer layer
void display(void)
{
    if (game == 3) //displays the quit page
    {
        glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT); //Clear the screen
        glColor3f (0.0, 0.0, 0.0);
        glMatrixMode(GL_PROJECTION);              // Tell opengl that we are doing project matrix work
        glLoadIdentity();                         // Clear the matrix
        glOrtho(-9.0, 9.0, -9.0, 9.0, 0.0, 30.0); // Setup an Ortho view
        glMatrixMode(GL_MODELVIEW);               // Tell opengl that we are doing model matrix work
        glLoadIdentity();                         // Clear the model matrix
        glDisable(GL_COLOR_MATERIAL);
        glDisable(GL_LIGHTING);
        glColor3f(1, 1, 0);
        Sprint(-2, 8.2, "Thank you for playing our game");

        glutSwapBuffers();
    }

    else if (game == 0) //display the front page
    {
        glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT); //Clear the screen
        glMatrixMode(GL_PROJECTION);                        // Tell opengl that we are doing project matrix work
        glLoadIdentity();                                   // Clear the matrix
        glOrtho(-9.0, 9.0, -9.0, 9.0, 0.0, 30.0);           // Setup an Ortho view
        glMatrixMode(GL_MODELVIEW);                         // Tell opengl that we are doing model matrix work. (drawing)
        glLoadIdentity();                                   // Clear the model matrix
        glDisable(GL_COLOR_MATERIAL);
        glDisable(GL_LIGHTING);
        glColor3f(0.0, 1.0, 0.49);
        Sprint(-2, 8.2, "CG Project: Tic Tac Toe Game ");
        glColor3f(1, 1, 0);
        Sprint(-8, 7.25, "Project Done by:");
        glColor3f(0.49, 1, 0.83);
        Sprint(-8, 6, "1) Md Nadim");
        Sprint(-8, 5, "2) Sadman Mahboob Alvi");
        glColor3f(1, 1, 0);
        Sprint(-8, 3, "Project Overview:");
        glColor3f(1, 0, 0);
        Sprint(-8, 2, "This is a very simple Tic Tac Toe game. Here you have to match 3 X's, or else the opponent will win.");
        Sprint(-8, 1, "Whoever reaches the goal first, wins.");

        glutSwapBuffers();
    }
    else
    {
        int ix, iy;
        int i;
        int j;
        glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT); //Clear the screen
        glMatrixMode(GL_PROJECTION);                        // Tell opengl that we are doing project matrix work
        glLoadIdentity();                                   // Clear the matrix
        glOrtho(-9.0, 9.0, -9.0, 9.0, 0.0, 30.0);           // Setup an Ortho view
        glMatrixMode(GL_MODELVIEW);                         // Tell opengl that we are doing model matrix work. (drawing)
        glLoadIdentity();                                   // Clear the model matrix
        glEnable(GL_COLOR_MATERIAL);
        glEnable(GL_LIGHTING);
        glColor3f(0.9, 0.8, 0.7);
        if (win == 1)
            Sprint(-2, 8, "Congratulations! You have won!!");
        if (win == -1)
            Sprint(-2, 8, "Sorry! You have lost!!");
        if (win == 2)
            Sprint(-2, 8, "It's a draw game, please try again");
        // Setup view, and print view state on screen
        glColor3f (0.0, 0.0, 1.0);
        glMatrixMode(GL_PROJECTION);
        glLoadIdentity();
        gluPerspective(60, 1, 1, 30);
        glMatrixMode(GL_MODELVIEW);
        glLoadIdentity();



        glEnable (GL_LIGHTING);

        gluLookAt (0, 0, 20, 0, 0, 0, 0, 1, 0);

        // Here we draw the grid line
        for (ix = 1; ix < 3; ix++)
        {
            glPushMatrix();
            glColor3f (1, 1, 1);
            glBegin (GL_LINES);
            glVertex2i (-9, -9 + ix * 6);
            glVertex2i (9, -9 + ix * 6);
            glEnd();
            glPopMatrix();
        }

        for (iy = 1; iy < 3; iy++)
{
    glPushMatrix();
    glColor3f(1, 1, 1);
    glBegin(GL_LINES);
    glVertex2i(-9 + iy * 6, 9);
    glVertex2i(-9 + iy * 6, -9);
    glEnd();
    glPopMatrix();
}

        for (i = 0; i < 9; i++)
        {
            j = 0;
            if (abs(win) == 1)
            {
                if ((i == box[gameboxes][0]) || (i == box[gameboxes][1]) || (i == box[gameboxes][2]))
                {
                    j = sign;
                }
                else
                    j = 0;
            }
            if (box_map[i] == 1) //If it's equal to 1, it calls a function Draw_O with certain parameters, likely for drawing an "O" on the screen.
            {
                Draw_O(object_map[i][0], object_map[i][1], -1, j);
            }
            if (box_map[i] == -1) //If it's is equal to -1, it calls a function Draw_X with certain parameters, likely for drawing an "X" on the screen.
            {
                Draw_X(object_map[i][0], object_map[i][1], -1, j);
            }
        }
        glutSwapBuffers();
    }
}

// Here we want to resize the window
void reshape(int w, int h)
{
    Win_x = w;
    Win_y = h;
    glViewport (0, 0, (GLsizei)w, (GLsizei)h);
    glMatrixMode (GL_PROJECTION);
    glLoadIdentity();

}

void keyboard (unsigned char key, int x, int y)
{
    switch (key)
    {
    case 27: //ESC = 5+19+3
        exit (0); // exit our program when ESC key is pressed
        break;
    default:
        break;
    }
}


void mouse (int button, int state, int x, int y)
{
    mouse_x = (18 * (float)((float)x / (float)Win_x)) / 6;
    mouse_y = (18 * (float)((float)y / (float)Win_y)) / 6;

    // In which square have they clicked in?
    object_select = mouse_x + mouse_y * 3;
    if (start_game == 0)
    {
        if ((button == GLUT_RIGHT_BUTTON) && (state == GLUT_DOWN))
        {
            player = 1;
            computer = -1;
            init_game();
            computer_move();
            return;
        }

        if ((button == GLUT_LEFT_BUTTON) && (state == GLUT_DOWN))
        {
            player = -1;
            computer = 1;
            init_game();
            return;
        }
    }

    if (start_game == 1)
    {
        if ((button == GLUT_LEFT_BUTTON) && (state == GLUT_DOWN))
        {
            if (win == 0) //if win is equal to 0, this condition means that the game is not over yet and it means there's no winner yet.
            {
                if (box_map[object_select] == 0)
                {
                    box_map[object_select] = player;
                    win = check_move();
                    if (win == 1)  //If win is equal to 1, it means that the player has won the game. In this case, it sets start_game to 0, indicating that the game has ended.
                    {
                        start_game = 0;
                        return;
                    }

                    computer_move();
                    win = check_move();
                    if (win == 1)
                    {
                        win = -1;
                        start_game = 0;
                    }
                }
            }
        }
    }
    if (win == 2)
        start_game = 0;
}
void menu (int choice)
{
    switch (choice)
    {
    case 1:
        game = 1;
        glutMouseFunc (mouse);
        break;
    case 2:
        game = 3;
        glutMouseFunc (mouse);
        break;
    case 3:
        exit(0);
        break;
    }
}

int main (int argc, char **argv)
{
    glutInit(&argc, argv);
    glutInitDisplayMode (GLUT_DOUBLE | GLUT_RGB | GLUT_DEPTH);
    glutInitWindowSize (1500, 1200);
    glutInitWindowPosition (10, 10);
    glutCreateWindow (argv[0]);
    glutSetWindowTitle ("CG Project: Tic Tac Toe game");
    init();
    glutCreateMenu (menu);
    glutAddMenuEntry ("Start game", 1);
    glutAddMenuEntry ("Quit", 2);
    printf ("Md. Nadim; ID: CSE 07208209\n");
    printf ("Sadman Mahboob Alvi; ID: CSE 07208230\n");

    glutAttachMenu (GLUT_RIGHT_BUTTON);
    glutDisplayFunc (display);
    glutReshapeFunc (reshape);
    glutKeyboardFunc (keyboard);
    glutMouseFunc (mouse);
    glutTimerFunc (10, TimeEvent, 1);
    glutMainLoop();
    return 0;
}

// Project submitted by: Sadman Mahboob Alvi & MD. Nadim (ID: 209 & 230)
// Project submitted to: Lomat Haider Chowdhury, Lecturer, Dept. of Computer Science & Engineering
// Course: Computer Graphics sessional (CSI 414)

