#define TRIGGER_PIN  12  // Arduino pin tied to trigger pin on the ultrasonic sensor.
#define ECHO_PIN     11  // Arduino pin tied to echo pin on the ultrasonic sensor.
#define MAX_DISTANCE 200 // Maximum distance we want to ping for (in centimeters). Maximum sensor distance is rated at 400-500cm. [this is an arbitrary number]
#define buzzer1  2
#define RELAY_LINE1_PIN 8

// defines variables
long duration;
int distance;
int safetyDistance;

#include "NewPing.h"
NewPing sonar(TRIGGER_PIN, ECHO_PIN, MAX_DISTANCE); // NewPing setup of pins and maximum distance.


unsigned int critical_distance_cms = 25.5;  // Cutoff distance at which the light will switch [this is an arbitrary number]
bool state = 0;

void setup() {
  Serial.begin(9600); // Open serial monitor at 115200 baud to see ping results.
  pinMode(RELAY_LINE1_PIN, OUTPUT);
  pinMode(buzzer1, OUTPUT);
  digitalWrite(RELAY_LINE1_PIN, HIGH);  // Turn the light off
}

void loop() {
  delay(50);                     // Wait 50ms between pings (about 20 pings/sec). 29ms should be the shortest delay between pings.
  unsigned int distance = readDistance(); // Current distance of any object facing the ultrasonic sensor

  Serial.print("Ultrasonic: ");
  Serial.print(distance); // Send ping, get distance in cm and print result (0 = outside set distance range)
  Serial.println("cm"); 
  Serial.print("Ultrasonic: ");

   // Reads the echoPin, returns the sound wave travel time in microseconds
duration = pulseIn(ECHO_PIN , HIGH);

// Calculating the distance
distance= duration*0.034/2;

safetyDistance = distance;
if (safetyDistance <= 50 && safetyDistance>=60 ){
  digitalWrite(buzzer1, HIGH);
}
else{
  digitalWrite(buzzer1, LOW);
}


  // Someone is near the door
  if (distance < critical_distance_cms)
  {
    while (distance < critical_distance_cms)
    {
      // Check if they moved away
      distance = readDistance();

      delay(5); // Do nothing until the person moves away from the door
    }

    state = !state; // Change the state of the relay

    if (state)
    {
      Serial.println("Pothole");
      digitalWrite(RELAY_LINE1_PIN, LOW); // Turn the light on
      digitalWrite(buzzer1, HIGH);
    }
    
    else
    {
      Serial.println("Normal road");
      digitalWrite(RELAY_LINE1_PIN, HIGH);  // Turn the light off
      digitalWrite(buzzer1, LOW);
    }
  }
}

// Updates the value of the Ultrasonic reading
unsigned int readDistance()
{
  // Read 7 values from the ultrasonic and get the median value ( median filter )
  // Gets rid of noisy reading
  unsigned int distance = sonar.convert_cm(sonar.ping_median(7));

  // The value 0 indicates that the ultrasonic sensor is reading nothing in front of it
  // Set this distance to max distance so the light doesn't switch unnecessarily
  if (distance == 0)
  {
    distance = MAX_DISTANCE;
  }
  // Prints the distance on the Serial Monitor
Serial.print("Distance: ");
Serial.println(distance);
 
  return distance;
}
