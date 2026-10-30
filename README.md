# Dear-Data-100-Assignment
IN1007 Creative Coding sketches and Dear Data 100 data
// Dear Data 100 | Week 4 sketch: boxes with light inside
// Data: minutes spent writing a book, Monday to Friday (placeholder numbers).
// Inspiration: Generative Design by Bohnacker, Gross, Laub and Lazzeroni
// (http://www.generative-gestaltung.de/2/), especially the glowing line work of P_2_2_4_01.

String[] dayNames = {"Mon", "Tue", "Wed", "Thu", "Fri"};
float[] minutes = {
  15, 52, 138, 64, 58,     // week 1
  10, 71, 126, 47, 63,     // week 2
  22, 58, 152, 69, 41,     // week 3
  14, 66, 119, 55, 72,     // week 4
  18, 49, 144, 61, 67      // week 5
};

int itemCount = 20;        // weeks 1 to 4 only: 4 weeks x 5 weekdays
float boxSize = 56;        // width and height of each box
float minGap = 115;        // minimum distance between two box centres
float[] xs;                // x position of each box
float[] ys;                // y position of each box
DayBox[] boxes;            // one object per day of data

void setup()
{
  size(1200, 760);
  randomSeed(42);          // the same "random" layout on every run
  xs = new float[itemCount];
  ys = new float[itemCount];
  boxes = new DayBox[itemCount];

  for (int i = 0; i < itemCount; i = i + 1)
  {
    float x = width / 2;
    float y = height / 2;
    boolean placed = false;
    int tries = 0;

    while (!placed && tries < 5000)       // keep trying random spots until one is far enough from the others
    {
      x = random(90, width - 90);
      y = random(110, height - 90);
      placed = isFarEnough(x, y, i);
      tries = tries + 1;
    }

    xs[i] = x;
    ys[i] = y;
  }

  for (int i = 0; i < itemCount; i = i + 1)       // now create one object per box
  {
    boxes[i] = new DayBox(xs[i], ys[i], boxSize, minutes[i], i);
  }
}

void draw()
{
  background(12, 10, 22);

  for (DayBox b : boxes)
  {
    b.display();
  }

  fill(210, 200, 240);
  textAlign(LEFT, CENTER);
  textSize(18);
  text("Time spent writing a book: weeks 1 to 4", 30, 30);
  textSize(12);
  text("Brighter light means a longer writing day. Inspired by Generative Design (generative-gestaltung.de).", 30, height - 25);
}

// Returns true if (x, y) is at least minGap away from the first 'count' boxes already placed.
boolean isFarEnough(float x, float y, int count)
{
  for (int j = 0; j < count; j = j + 1)
  {
    if (dist(x, y, xs[j], ys[j]) < minGap)
    {
      return false;
    }
  }
  return true;
}

class DayBox
{
  float x, y, side;        // centre position and width of the box
  float minutes;           // writing time for this day
  float intensity;         // 0 to 1: how bright the light is
  int index;               // position in the data, 0 to 24

  DayBox(float x, float y, float side, float minutes, int index)
  {
    this.x = x;
    this.y = y;
    this.side = side;
    this.minutes = minutes;
    this.index = index;
    this.intensity = constrain(minutes / 150, 0.05, 1);   // 150 minutes or more is full brightness
  }

  void display()
  {
    rectMode(CENTER);
    noStroke();

    int layers = 14;
    fill(255, 240, 200, 3 + intensity * 30);    // longer days get a stronger light
    for (int k = 0; k < layers; k = k + 1)
    {
      float s = side * (1 - k * 1.0 / layers);  // each layer is smaller, so the middle glows most
      rect(x, y, s, s);
    }

    noFill();
    stroke(255, 255, 255, 110);
    strokeWeight(1.5);
    rect(x, y, side, side, 6);                  // the box outline with slightly rounded corners
  }
}