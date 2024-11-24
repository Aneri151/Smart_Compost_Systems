# Smart Compost System

Introduction
There is a drastic increase in waste generation which has become a big problem in managing it. Managing biodegradable waste is critical for reducing greenhouse gasses. Composting manually is a very tedious job, whereas there should be a mechanism that should measure components such as temperature which would be much easier. According to conditions, at regular intervals, the system should automatically rotate the fan and mix the compost. The system should also display the temperature, pH, and moisture levels on display and the buzzer should switch on when levels are not in optimum conditions.

# Arduino code

#include <LiquidCrystal.h>
#include <Servo.h>

// Initialize the LCD (RS, E, DB4, DB5, DB6, DB7)
LiquidCrystal lcd(7, 8, 9, 10, 11, 12);

// Define pins for components
#define MOISTURE_SENSOR A0
#define TEMP_SENSOR A1       // Simulated analog pin for temperature sensor
#define PH_SENSOR A2         // Simulated analog pin for pH sensor
#define BUZZER_PIN 13
#define TEMP_LED_PIN 2
#define MOIST_LED_PIN 3
#define MOTOR_EN 5           // Motor Driver Enable Pin
#define MOTOR_IN1 4          // Motor Driver Input 1
#define MOTOR_IN2 6          // Motor Driver Input 2
#define SERVO_PIN 9          // Servo Motor Pin

Servo servoMotor;            // Servo motor object

bool compostReady = false;   // Flag to track compost readiness

void setup() {
  // Initialize LCD
  lcd.begin(16, 2); // 16x2 LCD
  lcd.print("System Initializing");
  delay(2000); // Display initialization message for 2 seconds
  lcd.clear();

  // Setup pins
  pinMode(MOISTURE_SENSOR, INPUT);
  pinMode(TEMP_SENSOR, INPUT);
  pinMode(PH_SENSOR, INPUT);
  pinMode(BUZZER_PIN, OUTPUT);
  pinMode(TEMP_LED_PIN, OUTPUT);
  pinMode(MOIST_LED_PIN, OUTPUT);
  pinMode(MOTOR_EN, OUTPUT);
  pinMode(MOTOR_IN1, OUTPUT);
  pinMode(MOTOR_IN2, OUTPUT);

  // Initialize Servo
  servoMotor.attach(SERVO_PIN);
  servoMotor.write(0); // Move servo to initial position

  // Initialize LCD display
  lcd.print("System Reading");
  delay(2000);
  lcd.clear();

  Serial.begin(9600); // For debugging
}

void loop() {
  // Read sensors
  digitalWrite(BUZZER_PIN, HIGH);
  int moistureLevel = analogRead(MOISTURE_SENSOR);
  int tempLevel =analogRead(TEMP_SENSOR); // Simulated temperature sensor
  int pHLevel = analogRead(PH_SENSOR);

  // Simulated temperature conversion (adjust as needed)
  int temperature = map(tempLevel, 0, 1023, -10, 50); // Example: -10°C to 50°C
  float pH = (pHLevel / 1023.0) * 14.0; // Example: Map analog value to 0-14 pH scale
  digitalWrite(BUZZER_PIN, HIGH);
  // Determine compost readiness condition
  if (moistureLevel > 700 && temperature > 25 && temperature < 40 && pH > 11 && pH < 14) {
    compostReady = true; // Compost is ready
    digitalWrite(TEMP_LED_PIN, HIGH); // Turn on LED to indicate readiness
    digitalWrite(MOIST_LED_PIN, HIGH);
  } else {
    compostReady = false; // Compost is not ready
    digitalWrite(TEMP_LED_PIN, LOW);
    digitalWrite(MOIST_LED_PIN, LOW);
  }

  // Display sensor values on LCD
  lcd.setCursor(0, 0);
  lcd.print("Temp: ");
  lcd.print(temperature);
  lcd.print("C");

  lcd.setCursor(0, 1);
  lcd.print("Moist: ");
  lcd.print(moistureLevel);

  delay(5000);
  lcd.clear();

  lcd.setCursor(0, 0);
  lcd.print("pH: ");
  lcd.print(pH);

  lcd.setCursor(0, 1);
  lcd.print("Moist: ");
  lcd.print(moistureLevel);

  delay(2000);
  lcd.clear();

  // Control Buzzer
  if (compostReady) {
    lcd.print("Compost Ready");
    digitalWrite(BUZZER_PIN, HIGH); // Turn on buzzer
    delay(10000); // Keep buzzer on for 30 seconds
    digitalWrite(BUZZER_PIN, LOW); // Turn off buzzer
    compostReady = false; // Reset compostReady flag to avoid repeated buzzing
  }

  // Control Servo Motor (Simulate blade movement)
  if (moistureLevel < 300) { // Example: Blade activation condition
    servoMotor.write(90); // Rotate servo to 90 degrees
    delay(2000);
    servoMotor.write(0); // Return to initial position
  }

  // Control Motor (Simulate a motor-driven component, e.g., compost mixing)
  if (pH < 6 || pH > 8) { // Example: Motor activation condition
    digitalWrite(MOTOR_EN, HIGH); // Enable motor
    digitalWrite(MOTOR_IN1, HIGH); // Rotate motor in one direction
    digitalWrite(MOTOR_IN2, LOW);
    delay(10000); // Run motor for 5 seconds
    digitalWrite(MOTOR_EN, LOW); // Turn off motor
  }
}
