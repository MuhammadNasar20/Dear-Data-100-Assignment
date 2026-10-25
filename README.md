# Dear-Data-100-Assignment
IN1007 Creative Coding sketches and Dear Data 100 data


String[] days = {"Mon","Tue","Wed","Thu","Fri","Sat","Sun"};   // text labels
float[] minutes = {15, 60, 30, 60, 20, 240, 120};              // one value per day

float spineWidth = 60;       // spine thickness
float gap = 30;              // space between spines
float baseY;                 // y of the "shelf" line

void setup()
{
  size(740, 460);
  textAlign(CENTER, CENTER);
  textSize(16);
  baseY = height - 70;       // shelf sits 70 px from the bottom
}

void draw()
{
  background(250, 240, 220);                           // paper colour

  for (int i = 0; i < minutes.length; i = i + 1)       // once per day
  {
    float x = 40 + i * (spineWidth + gap);             // each spine sits further right
    float spineHeight = minutesToPixels(minutes[i]);   // convert minutes to height
    float y = baseY - spineHeight;                     // top y (y grows downwards)

    boolean isHovered = mouseX > x && mouseX < x + spineWidth
                        && mouseY > y && mouseY < baseY;   // is the mouse on this spine?

    if (isHovered)      { fill(240, 180, 60); }        // gold on hover
    else if (i >= 5)    { fill(180, 70, 60); }         // Sat/Sun are indices 5 and 6
    else                { fill(70, 110, 160); }        // weekdays blue

    rect(x, y, spineWidth, spineHeight);               // draw the spine

    fill(40);
    text(days[i], x + spineWidth / 2, baseY + 20);     // day label under the shelf
    text(int(minutes[i]) + " min", x + spineWidth / 2, y - 14);  // minutes above the spine
  }

  fill(40);
  text("Total: " + int(totalMinutes()) + " min (" + nf(totalMinutes() / 60, 1, 1) + " h)", width/2, height - 25);
}

float minutesToPixels(float m)     // returns a value: your Part 1 "method that returns a value"
{
  return m * 1.5;                  // 240 min becomes 360 px, which fits the canvas
}

float totalMinutes()               // loops over the array and returns the sum
{
  float total = 0;
  for (float m : minutes)
  {
    total = total + m;
  }
  return total;
}
