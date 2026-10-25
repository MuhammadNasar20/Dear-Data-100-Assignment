# Dear-Data-100-Assignment
IN1007 Creative Coding sketches and Dear Data 100 data

String[] dayNames = {"Mon","Tue","Wed","Thu","Fri"};   // weekday names, used with i % 5
float[] minutes = {
  15, 52, 138, 64, 58,     // week 1
  10, 71, 126, 47, 63,     // week 2
  22, 58, 152, 69, 41,     // week 3
                           // week 4
                           // week 5

  };

float spineWidth = 22;     // thin spines so 25 fit on screen
float gap = 6;             // space between spines
float weekGap = 16;        // extra space between weeks
float baseY;               // y position of the shelf line
color[] dayColours;        // one colour per weekday

void setup()
{
  size(860, 460);
  textAlign(CENTER, CENTER);
  textSize(13);
  baseY = height - 90;
