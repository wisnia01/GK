# GK

#include <X11/Xlib.h>
#include <X11/Xutil.h>
#include <stdlib.h>
#include <string.h>
#include <stdio.h>
#include <math.h>

#define TRUE 1
#define FALSE 0

int red, green, blue, yellow;
unsigned long foreground, background;

int offsetX = 300;
int offsetY = 300;
int circle1X = 200;
int circle1Y = 200;

int circle2X = 500;
int circle2Y = 200;
XPoint mousePosition;

void move(XEvent event, XPoint* points)
{
  int movementX = event.xbutton.x - mousePosition.x;
  int movementY = event.xbutton.y - mousePosition.y;
  for(int i =0; i<12; i++)
  {
    points[i].x += movementX;
    points[i].y += movementY;

  }

  circle1X += movementX;
  circle1Y += movementY;

  mousePosition.x = event.xbutton.x;
  mousePosition.y = event.xbutton.y;
}

void initialize(XPoint points1[], XPoint points2[])
{
  //W
  points1[0].x = -60 + offsetX;
  points1[0].y = -40 + offsetY;
  points1[1].x = -40 + offsetX;
  points1[1].y =  40 + offsetY;
  points1[2].x = -20 + offsetX;
  points1[2].y =  40 + offsetY;
  points1[3].x =   0 + offsetX;
  points1[3].y =   0 + offsetY;
  points1[4].x =  20 + offsetX;
  points1[4].y =  40 + offsetY;
  points1[5].x =  40 + offsetX;
  points1[5].y =  40 + offsetY;
  points1[6].x =  60 + offsetX;
  points1[6].y = -40 + offsetY;
  points1[7].x =  40 + offsetX;
  points1[7].y = -40 + offsetY;
  points1[8].x =  30 + offsetX;
  points1[8].y =  20 + offsetY;
  points1[9].x =   0 + offsetX;
  points1[9].y = -20 + offsetY;
  points1[10].x = -30 + offsetX;
  points1[10].y =  20 + offsetY;
  points1[11].x = -40 + offsetX;
  points1[11].y = -40 + offsetY;
  //J
  points2[0].x = -20 + 2*offsetX;
  points2[0].y = -60 + offsetY;
  points2[1].x = -20 + 2*offsetX;
  points2[1].y = -40 + offsetY;
  points2[2].x =  20 + 2*offsetX;
  points2[2].y = -40 + offsetY;
  points2[3].x =  20 + 2*offsetX;
  points2[3].y =  20 + offsetY;
  points2[4].x =   0 + 2*offsetX;
  points2[4].y =  40 + offsetY;
  points2[5].x = -20 + 2*offsetX;
  points2[5].y =  20 + offsetY;
  points2[6].x = -40 + 2*offsetX;
  points2[6].y =  20 + offsetY;
  points2[7].x = -10 + 2*offsetX;
  points2[7].y =  60 + offsetY;
  points2[8].x =  10 + 2*offsetX;
  points2[8].y =  60 + offsetY;
  points2[9].x =  40 + 2*offsetX;
  points2[9].y =  20 + offsetY;
  points2[10].x =  40 + 2*offsetX;
  points2[10].y = -60 + offsetY;


}
//*************************************************************************************************************************
//funkcja przydzielania kolorow

int AllocNamedColor(char *name, Display* display, Colormap colormap)
  {
    XColor col;
    XParseColor(display, colormap, name, &col);
    XAllocColor(display, colormap, &col);
    return col.pixel;
  } 

//*************************************************************************************************************************
// inicjalizacja zmiennych globalnych okreslajacych kolory

int init_colors(Display* display, int screen_no, Colormap colormap)
{
  background = WhitePixel(display, screen_no);  //niech tlo bedzie biale
  foreground = BlackPixel(display, screen_no);  //niech ekran bedzie czarny
  red=AllocNamedColor("red", display, colormap);
  green=AllocNamedColor("green", display, colormap);
  blue=AllocNamedColor("blue", display, colormap);
  yellow=AllocNamedColor("yellow", display, colormap);
}

//*************************************************************************************************************************
// Glowna funkcja zawierajaca petle obslugujaca zdarzenia */

int main(int argc, char *argv[])
{
  char            icon_name[] = "Grafika";
  char            title[]     = "Grafika komputerowa";
  Display*        display;    //gdzie bedziemy wysylac dane (do jakiego X servera)
  Window          window;     //nasze okno, gdzie bedziemy dokonywac roznych operacji
  GC              gc;         //tu znajduja sie informacje o parametrach graficznych
  XEvent          event;      //gdzie bedziemy zapisywac pojawiajace sie zdarzenia
  KeySym          key;        //informacja o stanie klawiatury 
  Colormap        colormap;
  int             screen_no;
  XSizeHints      info;       //informacje typu rozmiar i polozenie ok
  
  char            buffer[8];  //gdzie bedziemy zapamietywac znaki z klawiatury
  int             hm_keys;    //licznik klawiszy
  int             to_end;

  display    = XOpenDisplay("");                //otworz polaczenie z X serverem pobierz dane od zmiennej srodowiskowej DISPLAY ("")
  screen_no  = DefaultScreen(display);          //pobierz domyslny ekran dla tego wyswietlacza (0)
  colormap = XDefaultColormap(display, screen_no);
  init_colors(display, screen_no, colormap);

  //okresl rozmiar i polozenie okna
  info.x = 100;
  info.y = 150;
  info.width = 1000;
  info.height = 600;
  info.flags = PPosition | PSize;
  //moje dane
  XPoint pointsW[12];
  XPoint pointsJ[11];
  initialize(pointsW, pointsJ);




  //majac wyswietlacz, stworz okno - domyslny uchwyt okna
  window = XCreateSimpleWindow(display, DefaultRootWindow(display),info.x, info.y, info.width, info.height, 7/* grubosc ramki */, foreground, background);
  XSetStandardProperties(display, window, title, icon_name, None, argv, argc, &info);
  //utworz kontekst graficzny do zarzadzania parametrami graficznymi (0,0) domyslne wartosci
  gc = XCreateGC(display, window, 0, 0);
  XSetBackground(display, gc, background);
  XSetForeground(display, gc, foreground);

  //okresl zdarzenia jakie nas interesuja, np. nacisniecie klawisza
  XSelectInput(display, window, (KeyPressMask | ExposureMask | ButtonPressMask| ButtonReleaseMask | Button1MotionMask));
  XMapRaised(display, window);  //wyswietl nasze okno na samym wierzchu wszystkich okien
      
  to_end = FALSE;

 /* petla najpierw sprawdza, czy warunek jest spelniony
     i jesli tak, to nastepuje przetwarzanie petli
     a jesli nie, to wyjscie z petli, bez jej przetwarzania */
  while (to_end == FALSE)
  {
    XNextEvent(display, &event);  // czekaj na zdarzenia okreslone wczesniej przez funkcje XSelectInput
    XClearWindow(display, window);
    XSetForeground(display, gc, red);
    XFillPolygon(display, window, gc, pointsW, 12,0,0);
    XDrawArc(display, window, gc, circle1X, circle1Y, 200,200, 0,360*64);
    XSetForeground(display, gc, green);
    XFillPolygon(display, window, gc, pointsJ, 11,0,0);
     XDrawArc(display, window, gc, circle2X, circle2Y, 200,200, 0,360*64);
   


    switch(event.type)
    {
      case Expose:
        if (event.xexpose.count == 0)
        {

         
        }
        break;

      case MappingNotify:
        XRefreshKeyboardMapping(&event.xmapping); // zmiana ukladu klawiatury - w celu zabezpieczenia sie przed taka zmiana trzeba to wykonac
        break;

      case ButtonPress:
        if (event.xbutton.button == Button1)  // sprawdzenie czy wcisnieto lewy przycisk		
        {
   		      mousePosition.x = event.xbutton.x;
            mousePosition.y = event.xbutton.y;
            printf("%d %d\n", mousePosition.x, mousePosition.y);     
    
        }
        break;


      case KeyPress:
        hm_keys = XLookupString(&event.xkey, buffer, 8, &key, 0);
        if (hm_keys == 1)
        {
          if (buffer[0] == 'q') to_end = TRUE;        // koniec programu
          if (buffer[0] == 's')
          {
            
          }
          
        }
        break;
      case MotionNotify:
      	printf("Motion: %d %d\n", event.xbutton.x, event.xbutton.y);
        move(event, pointsW);
       break;


      default:
        break;
    }
  }

  XFreeGC(display, gc);
  XDestroyWindow(display, window);
  XCloseDisplay(display);

  return 0;
}
