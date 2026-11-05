# Dear-Data-100-Assignment
IN1007 Creative Coding sketches and Dear Data 100 data
// Dear Data 100 | Week 4.5 sketch: purple light, diamond frames and sparks
// Data: minutes spent writing a book, Monday to Friday (placeholder numbers).
// Inspiration: Generative Design by Bohnacker, Gross, Laub and Lazzeroni
// (http://www.generative-gestaltung.de/2/), especially the glowing line work of P_2_2_4_01.

// MEDIA (sounds): loads the Sound library so the sketch can make noise.
import processing.sound.*;

// DATA: two arrays hold the weekday names and the minutes written on each of the 25 days.
String[] dayNames = {"Mon", "Tue", "Wed", "Thu", "Fri"};
float[] minutes = {
  15, 52, 138, 64, 58,
  10, 71, 126, 47, 63,
  22, 58, 152, 69, 41,
  14, 66, 119, 55, 72,
  18, 49, 144, 61, 67
};

// VARIABLES: these store the settings and the things the sketch needs to remember.
int itemCount = 25;
float boxSize = 56;
float minGap = 115;
float[] xs;
float[] ys;
DayBox[] boxes;
int activeIndex = -1;
float glow = 0;

// MEDIA (sounds): these are the sound makers and their envelopes (how each sound starts and fades).
WhiteNoise crack;
BrownNoise rumble;
Env crackEnv;
Env rumbleEnv;

// SKETCHES: setup() runs once at the start to prepare everything.
void setup()
{
  size(1200, 760);
  randomSeed(42);
  xs = new float[itemCount];
  ys = new float[itemCount];
  boxes = new DayBox[itemCount];

  // LOOPS: this loop gives every box a random position that is not too close to the others.
  for (int i = 0; i < itemCount; i = i + 1)
  {
    float x = width / 2;
    float y = height / 2;
    boolean placed = false;
    int tries = 0;

    while (!placed && tries < 5000)
    {
      x = random(90, width - 90);
      y = random(110, height - 90);
      placed = isFarEnough(x, y, i);
      tries = tries + 1;
    }

    xs[i] = x;
    ys[i] = y;
  }

  // CLASSES AND OBJECTS: this loop makes 25 DayBox objects, one for each day.
  for (int i = 0; i < itemCount; i = i + 1)
  {
    boxes[i] = new DayBox(xs[i], ys[i], boxSize, minutes[i], i);
  }

  // MEDIA (sounds): this creates the two sounds and their envelopes ready to be played.
  crack = new WhiteNoise(this);
  rumble = new BrownNoise(this);
  crackEnv = new Env(this);
  rumbleEnv = new Env(this);
}

// SKETCHES: draw() runs about 60 times a second and redraws the whole picture.
void draw()
{
  background(12, 10, 22);

  String info = "Tap a box to hear thunder";

  // DECISIONS: if the mouse is over a box it gets brighter and its details show at the bottom.
  for (int i = 0; i < boxes.length; i = i + 1)
  {
    boolean hovered = boxes[i].contains(mouseX, mouseY);
    float extra = 0;
    if (hovered)
    {
      extra = 0.3;
      info = boxes[i].label();
    }
    if (i == activeIndex)
    {
      extra = max(extra, glow);
    }
    boxes[i].display(extra);
  }

  // INTERACTION: the glow from a tap fades away a little every frame.
  glow = glow * 0.93;

  // DATA VISUALISATION: this writes the title, the info line and the credit on the screen.
  fill(210, 200, 240);
  textAlign(LEFT, CENTER);
  textSize(18);
  text("Time spent writing a book: weeks 1 to 5", 30, 30);
  textAlign(CENTER, CENTER);
  textSize(16);
  text(info, width / 2, height - 55);
  textAlign(LEFT, CENTER);
  textSize(12);
  text("Brighter purple means a longer writing day. Inspired by Generative Design (generative-gestaltung.de).", 30, height - 25);
}

// INTERACTION: this runs once per mouse click, finds the box that was clicked and plays the thunder.
void mousePressed()
{
  for (int i = 0; i < boxes.length; i = i + 1)
  {
    if (boxes[i].contains(mouseX, mouseY))
    {
      activeIndex = i;
      glow = 1;
      playThunder(boxes[i].intensity);
    }
  }
}

// MEDIA (sounds): this method plays a short crackle and a longer rumble, louder for longer writing days.
void playThunder(float intensity)
{
  crackEnv.play(crack, 0.001, 0.04, 0.5, 0.25);
  rumbleEnv.play(rumble, 0.02, 0.2, 0.3 + 0.6 * intensity, 1.2);
}

// METHODS: this method answers true if a spot is far enough away from all the boxes placed so far.
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

// METHODS: this method turns minutes into text, for example 138 becomes "2h 18m".
String formatTime(float m)
{
  int totalMins = int(m);
  int hours = totalMins / 60;
  int mins = totalMins % 60;
  if (hours > 0)
  {
    return hours + "h " + mins + "m";
  }
  return mins + "m";
}

// CLASSES AND OBJECTS: DayBox is the blueprint for one glowing box, and each object stores one day of data.
class DayBox
{
  float x, y, side;
  float minutes;
  float intensity;
  int index;
  float phase;
  float[] sparkLength;

  // CLASSES AND OBJECTS: the constructor runs when a box is created and fills in its values.
  DayBox(float x, float y, float side, float minutes, int index)
  {
    this.x = x;
    this.y = y;
    this.side = side;
    this.minutes = minutes;
    this.index = index;
    this.intensity = constrain(minutes / 150, 0.05, 1);
    this.phase = random(TWO_PI);
    this.sparkLength = new float[14];
    for (int k = 0; k < sparkLength.length; k = k + 1)
    {
      sparkLength[k] = random(10, 42);
    }
  }

  // INTERACTION: this method answers true if the mouse is on this box.
  boolean contains(float px, float py)
  {
    return abs(px - x) < side * 0.9 && abs(py - y) < side * 0.9;
  }

  // DATA: this method gives the day, week and time of this box, for example "Wed, week 3: 2h 32m".
  String label()
  {
    return dayNames[index % 5] + ", week " + (index / 5 + 1) + ": " + formatTime(minutes);
  }

  // ART AND CREATIVITY: this method draws the glowing box, its diamond and its sparks, brighter for longer days.
  void display(float extra)
  {
    float pulse = 0.85 + 0.15 * sin(frameCount * 0.04 + phase);
    float level = min(1, intensity * pulse + extra);
    color tint = lerpColor(color(70, 20, 140), color(235, 150, 255), level);

    blendMode(ADD);
    rectMode(CENTER);
    noStroke();
    int layers = 16;
    fill(tint, 4 + level * 26);
    for (int k = 0; k < layers; k = k + 1)
    {
      float s = side * (1 - k * 1.0 / layers);
      rect(x, y, s, s);
    }
    blendMode(BLEND);

    noFill();
    stroke(255, 230, 255, 90 + level * 120);
    strokeWeight(1.5);
    rect(x, y, side, side, 6);

    stroke(tint, 120 + level * 135);
    float d = side * 0.95;
    quad(x, y - d, x + d, y, x, y + d, x - d, y);

    strokeWeight(1);
    stroke(tint, 60 + level * 150);
    for (int k = 0; k < sparkLength.length; k = k + 1)
    {
      float angle = TWO_PI * k / sparkLength.length;
      float x1 = x + cos(angle) * d;
      float y1 = y + sin(angle) * d;
      float x2 = x + cos(angle) * (d + sparkLength[k] * level);
      float y2 = y + sin(angle) * (d + sparkLength[k] * level);
      line(x1, y1, x2, y2);
    }
  }
}