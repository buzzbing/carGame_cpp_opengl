
#include<windows.h>
#include<GL/glut.h>
#include<math.h>
#include<time.h>
#include<iostream>

using namespace std;

GLint windowWidth = 800, windowHeight = 600;
GLint radius ,xcent,ycent;

float x_car = 0.0, x_background = 0.0;

const double TWO_PI = 6.2831853;
//GLuint regHex;


void initialize()
{
    glClearColor(0.1,0.5,0.7,0.0);
    glMatrixMode(GL_PROJECTION);
    gluOrtho2D(0.0,1600.0,0.0,1200.0);
    glClear(GL_COLOR_BUFFER_BIT);
}



class point
{
    GLint x,y;
public:
    point(){x=0;y=0;}

    void setCoord(GLint xCoord, GLint yCoord)
    {
        x = xCoord; y= yCoord;
    }
    GLint getx() const{return x;}
    GLint gety() const{return y;}

    void increment_x(){x++;}
    void decrement_y(){y--;}
};

void setPixel(GLint xCoord, GLint yCoord)
{
    glBegin(GL_POINTS);
        glVertex2i(xCoord, yCoord);
    glEnd();

}

void circlePlotPoints(GLint xc, GLint yc, point circlePt)
{
    setPixel (xc + circlePt.getx(), yc + circlePt.gety());
    setPixel (xc - circlePt.getx(), yc + circlePt.gety());
    setPixel (xc + circlePt.getx(), yc - circlePt.gety());
    setPixel (xc - circlePt.getx(), yc - circlePt.gety());
    setPixel (xc + circlePt.gety(), yc + circlePt.getx());
    setPixel (xc - circlePt.gety(), yc + circlePt.getx());
    setPixel (xc + circlePt.gety(), yc - circlePt.getx());
    setPixel (xc - circlePt.gety(), yc - circlePt.getx());
}


void midCircle(GLint xc, GLint yc, GLint radius)
{
    point circlePt;
    GLint p = 1-radius;

    circlePt.setCoord(0,radius);

   // void circlePlotPoints(GLint GLint, point);
    circlePlotPoints(xc, yc, circlePt);
    while(circlePt.getx()< circlePt.gety())
    {
        circlePt.increment_x();
        if(p<0)
            p = p + (2* circlePt.getx()) + 1;
        else
        {circlePt.decrement_y();
        p+= 2*(circlePt.getx() - circlePt.gety())+1;
            }

            circlePlotPoints(xc,yc,circlePt);
    }

}

void regHex(GLint x_cent, GLint y_cent)
{

    point hexVertex, circCenter;
    circCenter.setCoord(x_cent,y_cent);
     glBegin(GL_POLYGON);
            for(int k = 0;k<6;k++)
            {
                GLdouble theta = TWO_PI * k/6.0;//theta = (n-2)*pi/n interior angle
                hexVertex.setCoord(circCenter.getx()+ 10 * cos(theta),
                                            circCenter.gety() + 10 *sin(theta));
                glVertex2i(hexVertex.getx(),hexVertex.gety());
            }
        glEnd();
}

void trafficLights(float x_backgrnd, long _begin)
{
    GLint i = 0;

    float redlight[] = {0.1,0.1,0.1},yellowlight[] = {0.1,0.1,0.1},greenlight[] = {0.1,0.1,0.1};
        GLint timer = _begin/1000 ;
    GLint time = timer;
      if(time>11)
      {time = timer%10;}

     if((time>=7)&&(time<=11))
        {greenlight[1] = 1;}
    else if((time>=5)&&(time<7))
        {yellowlight[1] = 1;yellowlight[0] = 1;}
    else if((time>=0)&&(time<5))
        {redlight[0] = 1.0;}
    while(i!=5)
     {

    glColor3f(0.5,0.2,0);

    GLint pole_left_x = 20, pole_left_y = 180, pole_right_x = 25, pole_right_y = 30;

    glRectf(x_backgrnd + pole_left_x, pole_left_y, x_backgrnd + pole_right_x, pole_right_y);

    glColor3f(0.0,0.0,0.0);
    glRectf(x_backgrnd + pole_left_x - 9,120, x_backgrnd+pole_right_x + 9,190);

    glColor3fv(greenlight);
    regHex(x_backgrnd + pole_left_x + 2,pole_left_y - 45);

    glColor3fv(yellowlight);
    regHex(x_backgrnd + pole_left_x + 2,pole_left_y - 25);

    glColor3fv(redlight);
    regHex(x_backgrnd + pole_left_x + 2,pole_left_y - 5);
    x_backgrnd = 1500 + x_backgrnd;
    i++;
    }

}

void home(float x_background)
{
//left roof
float house_left_x=-70.0, roof_topleft_x=-45.0, house_right_x= -20.0, roof_topright_x=-20.0;
//glClear(GL_COLOR_BUFFER_BIT);
glColor3f(0.5,0.2,0);
glBegin(GL_TRIANGLES);
glVertex2f(house_left_x+x_background,house_left_x+250);
glColor3f(0,0,0);
glVertex2f(house_right_x+x_background,house_right_x+200);

glVertex2f(roof_topleft_x+x_background,roof_topleft_x+325);
glEnd();
//side wall
glColor3f(1,0.9,0.5);

glBegin(GL_POLYGON);
glVertex2f(house_left_x+x_background,house_left_x+250);

glVertex2f(house_right_x+x_background,house_right_x+200);
glVertex2f(house_right_x+x_background,house_right_x+50);
glColor3f(0.6,0.3,0);
glVertex2f(house_left_x+x_background,house_left_x+100);
glEnd();
//right roof
glColor3f(0.5,0.2,0);
glBegin(GL_QUADS);
glVertex2f(roof_topleft_x+x_background,roof_topleft_x+325);
glVertex2f(roof_topright_x+110+x_background,roof_topright_x+300);
glColor3f(0,0,0);
glVertex2f(house_right_x+150+x_background,house_right_x+200);
glVertex2f(house_left_x+50+x_background,house_left_x+250);
glEnd();
//front wall
glColor3f(1,1,0.7);
glBegin(GL_POLYGON);
glVertex2f(house_left_x+50+x_background,house_left_x+250);
glVertex2f(house_right_x+150+x_background,house_right_x+200);
glVertex2f(house_right_x+150+x_background,house_right_x+50);
glVertex2f(house_left_x+50+x_background,house_left_x+100);
glEnd();
//window 1
glColor3f(0.5,0.1,0.0);
glBegin(GL_POLYGON);
glVertex2f(house_left_x+60+x_background,house_left_x+230);
glVertex2f(house_left_x+90+x_background,house_left_x+230);
glVertex2f(house_left_x+90+x_background,house_left_x+200);
glVertex2f(house_left_x+60+x_background,house_left_x+200);
glEnd();
//window 1 door
glColor3f(0.9, 0.8, 0.5);
glPointSize(20);
glBegin(GL_POINTS);
glVertex2f(house_left_x+75+x_background,house_left_x+215);
glEnd();
//window 2
glColor3f(0.5,0.1,0.0);
glBegin(GL_POLYGON);
glVertex2f(house_right_x+110+x_background,house_right_x+180);
glVertex2f(house_right_x+140+x_background,house_right_x+180);
glVertex2f(house_right_x+140+x_background,house_right_x+150);
glVertex2f(house_right_x+110+x_background,house_right_x+150);
glEnd();
//window 2 door
glColor3f(0.9, 0.8, 0.5);
glPointSize(20);
glBegin(GL_POINTS);
glVertex2f(house_right_x+125+x_background,house_right_x+165);
glEnd();
//door
glColor3f(0.5,0.1,0.0);
glBegin(GL_POLYGON);
glVertex2f(house_left_x+105+x_background,house_left_x+215);
glVertex2f(house_right_x+95+x_background,house_right_x+165);
glVertex2f(house_right_x+95+x_background,house_right_x+50);
glVertex2f(house_left_x+105+x_background,house_left_x+100);
glEnd();
//door knob
glColor3f(0.7, 0.4, 0.2);
glPointSize(7);
glBegin(GL_POINTS);
glVertex2f(house_right_x+90+x_background,house_right_x+107);
glEnd();
}
void road(float x_background)
{
float zebra_x=x_background+windowWidth+650.0, zebra_y=30.0;
//road
glColor3f(0.5,0.5,0.5);
glRecti(0,5,zebra_x,zebra_y);
//glEnd();
GLint zebx= 5, zeby = 10;
//zebracrossing
for(GLint i=0;i<6;i++)
{
    glColor3f(1.0,1.0,1.0);
glBegin(GL_POLYGON);
glVertex2f(zebra_x-200-zebx+x_background,zebra_y+zeby-5);
glVertex2f(zebra_x+zebx+x_background,zebra_y+zeby-5);

glVertex2f(zebra_x+zebx+2.5+x_background,zebra_y+zeby-10);
glVertex2f(zebra_x-200-(zebx+2.5)+x_background,zebra_y+zeby-10);
glEnd();

zebx+=2.5;zeby-=10;

}

}
void tree(float x_background)
{
float tree_x=680.0, tree_y=150.0;
//tree
glColor3f(0.3,0.0,0.0);
glBegin(GL_POLYGON);
glVertex2f(tree_x+x_background,tree_y+150);
glVertex2f(tree_x+30+x_background,tree_y+150);

glVertex2f(tree_x+30+x_background,tree_y);
glColor3f(0,0,0);
glVertex2f(tree_x+x_background,tree_y);
glEnd();

glColor3f(0.5,0.7,0.1);
glBegin(GL_TRIANGLES);
glVertex2f(tree_x+15+x_background,tree_y+200);
glColor3f(0,0.5,0.3);
glVertex2f(tree_x-40+x_background,tree_y+100);
glVertex2f(tree_x+70+x_background,tree_y+100);
glEnd();

glColor3f(0.5,0.7,0.1);
glBegin(GL_TRIANGLES);
glVertex2f(tree_x+15+x_background,tree_y+200);
glColor3f(0,0.5,0.3);
glVertex2f(tree_x-25+x_background,tree_y+90);
glVertex2f(tree_x+60+x_background,tree_y+90);
glEnd();

glColor3f(0.5,0.7,0.1);
glBegin(GL_TRIANGLES);
glVertex2f(tree_x+15+x_background,tree_y+200);
glColor3f(0,0.5,0.3);
glVertex2f(tree_x-20+x_background,tree_y+80);
glVertex2f(tree_x+50+x_background,tree_y+80);
glEnd();
}

void car(float x_car)
{

    glColor3f(0.8,0.0,0.0);
    //body
    GLint low_back_end[2] = {x_car+50,20}, low_front_end[2] = {x_car+150,20},
            up_front_end[2] = {x_car+150,40}, front_window_corner[2] = {x_car+130,40},
            front_roof_end[2] = {x_car+120,60}, back_roof_end[2] = {x_car+80,60},
            back_window_corner[2] = {x_car+70,40}, up_back_end[2] = {x_car+50,40};


    glBegin(GL_POLYGON);
        glVertex2iv(low_back_end);
        glVertex2iv(low_front_end);
        glVertex2iv(up_front_end);
        glVertex2iv(front_window_corner);
        glVertex2iv(front_roof_end);
        glVertex2iv(back_roof_end);
        glVertex2iv(back_window_corner);
        glVertex2iv(up_back_end);
    glEnd();

    //window
    glColor3f(0.0,0.0,0.0);

    GLint back_windowsill_low[2], front_windowsill_low[2] , front_windowsill_up[2], back_windowsill_up[2];

    back_windowsill_low[0] = back_window_corner[0] + 5;
    back_windowsill_low[1] = back_window_corner[1];
    front_windowsill_low[0] = front_window_corner[0] - 5;
    front_windowsill_low[1] = front_window_corner[1] ;
    front_windowsill_up[0] = front_roof_end[0] - 5;
    front_windowsill_up[1] = front_roof_end[1] - 5;
    back_windowsill_up[0] = back_roof_end[0] + 5;
    back_windowsill_up[1] = back_roof_end[1] - 5;




    glBegin(GL_LINES);
    glVertex2iv(low_back_end);
        glVertex2iv(low_front_end);
        glVertex2iv(up_front_end);
        glVertex2iv(front_window_corner);
        glVertex2iv(front_roof_end);
        glVertex2iv(back_roof_end);
        glVertex2iv(back_window_corner);
        glVertex2iv(up_back_end);
    glEnd();

        //wheels
    glPointSize(10);
    midCircle(back_window_corner[0],back_window_corner[1]-20,10);
    midCircle(front_window_corner[0],front_window_corner[1]-20,10);
    glColor3f(1.0,1.0,1.0);
    midCircle(back_window_corner[0],back_window_corner[1]-20,5);
    midCircle(front_window_corner[0],front_window_corner[1]-20,5);
    glColor3f(0.0,0.0,0.0);
    midCircle(back_window_corner[0],back_window_corner[1]-20,1);
    midCircle(front_window_corner[0],front_window_corner[1]-20,1);

    glBegin(GL_POLYGON);
        glVertex2iv(back_windowsill_low);
        glVertex2iv(front_windowsill_low);
        glVertex2iv(front_windowsill_up);
        glVertex2iv(back_windowsill_up);
    glEnd();
}
void streetlight(float x_background)
{
    GLint i=0;
   // x_background+=500;
    while(i!=10)
   {

   //glClear(GL_COLOR_BUFFER_BIT);

    GLfloat polefoot_left_x =100.0,polefoot_left_y = 30.0, poleHead_right_x =105, poleHead_right_y=150.0;
    glColor3f(0.3,0,0);
    glRectf(polefoot_left_x+x_background,polefoot_left_y,poleHead_right_x+x_background,poleHead_right_y);

    glColor3f(0.5,0,0);

  glBegin(GL_POLYGON);

    glVertex2f(poleHead_right_x+x_background,poleHead_right_y);
    glVertex2f(poleHead_right_x+15+x_background,poleHead_right_y+15);
    glVertex2f(poleHead_right_x+15+x_background,poleHead_right_y+20);
    glVertex2f(poleHead_right_x-5+x_background,poleHead_right_y);
    glEnd();
    glRectf(poleHead_right_x+15+x_background, poleHead_right_y+15, poleHead_right_x+30+x_background, poleHead_right_y+20);

      glBegin(GL_POLYGON);
    glVertex2f(poleHead_right_x+30+x_background, poleHead_right_y+15);
    glVertex2f(poleHead_right_x+35+x_background, poleHead_right_y+10);
    glVertex2f(poleHead_right_x+40+x_background, poleHead_right_y+10);
    glVertex2f(poleHead_right_x+30+x_background, poleHead_right_y+20);
    glEnd();

    glRectf(poleHead_right_x+30+x_background, poleHead_right_y+10, poleHead_right_x+45+x_background, poleHead_right_y+5);

    glBegin(GL_POLYGON);
    glColor3f(0.1,0.5,0.7);
    glVertex2f(polefoot_left_x-100+x_background, polefoot_left_y);
    glVertex2f(poleHead_right_x+200+x_background, polefoot_left_y);
    glColor3f(1,1,0.1);
    glVertex2f(poleHead_right_x+45+x_background,poleHead_right_y+5);
    glVertex2f(poleHead_right_x+30+x_background,poleHead_right_y+5);
    glEnd();

    glColor3f(0.3,0,0);
    glRectf(polefoot_left_x+x_background,polefoot_left_y,poleHead_right_x+x_background,poleHead_right_y);
    x_background += 500;
    i++;
   }
}
/* void road(float x_car)
{
    point low_road_left, low_road_right, high_road_left,high_road_right;
    low_road_left.setCoord(0.0,5.0);
    low_road_right.setCoord(windowWidth,5.0);
    high_road_right.setCoord(windowWidth,30.0);
    high_road_left.setCoord(0.0,30.0);

    glColor3f(0.5,0.5,0.5);
    glBegin(GL_POLYGON);
    glVertex2f(x_car + low_road_left.getx(),low_road_left.gety());
    glVertex2f(x_car + low_road_right.getx(),low_road_right.gety());
    glVertex2f(x_car + high_road_right.getx(),high_road_right.gety());
    glVertex2f(x_car + high_road_left.getx(),high_road_left.gety());
    glEnd();
}*/
void move_car_forward()
{
   // x_car+=0.2;
    x_background-=0.5;
    //windowWidth+=0.5;
    glutPostRedisplay();
}
void move_car_backward()
{
    //x_car-=0.2;
    x_background+=0.5;
    glutPostRedisplay();
}
void stop()
{glutPostRedisplay();}
void display()
{

    glClear(GL_COLOR_BUFFER_BIT);
    clock_t begin_time = clock();
    glPushMatrix();
    road(x_background);
    trafficLights(x_background,begin_time);
    streetlight(x_background);
    tree(2000+x_background);
    home(2500+x_background);
    car(x_car);
    glPopMatrix();
    glutSwapBuffers();
    glFlush();

}

void moveFcn(GLint movKey, GLint xMouse, GLint yMouse )
{
    switch(movKey)
    {
    case GLUT_KEY_RIGHT:  glutIdleFunc(move_car_forward);break;
    case GLUT_KEY_LEFT:     glutIdleFunc(move_car_backward);break;
    default:glutIdleFunc(stop);break;
    }
}

void winReshapeFcn(int newWidth, int newHeight)
{
    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();
    gluOrtho2D(0.0,(GLdouble) newWidth/2.5,0.0,(GLdouble) newHeight/2.5);
    glClear(GL_COLOR_BUFFER_BIT);
}
int main(int argc,char** argv)
{
    glutInit(&argc, argv);
    glutInitDisplayMode(GLUT_SINGLE | GLUT_RGB);
    glutInitWindowPosition(100,100);
    glutInitWindowSize(windowWidth, windowHeight);
    glutCreateWindow("Let's Check");

    initialize();
    glutDisplayFunc(display);
    glutReshapeFunc(winReshapeFcn);
    glutSpecialFunc(moveFcn);
    glutMainLoop();
    return 0;
}
