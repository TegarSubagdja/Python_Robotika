// Motor A connections
int enA = 9;
int in1 = 4;
int in2 = 5;
// Motor B connections
int enB = 10;
int in3 = 6;
int in4 = 7;

// Ultrasonic sensor connections
const int trigPin = 11;
const int echoPin = 12;

void setup() {
	// Set all the motor control pins to outputs
	pinMode(enA, OUTPUT);
	pinMode(enB, OUTPUT);
	pinMode(in1, OUTPUT);
	pinMode(in2, OUTPUT);
	pinMode(in3, OUTPUT);
	pinMode(in4, OUTPUT);

	// Set ultrasonic sensor pins
	pinMode(trigPin, OUTPUT);
	pinMode(echoPin, INPUT);
	
	// Turn off motors - Initial state
	digitalWrite(in1, LOW);
	digitalWrite(in2, LOW);
	digitalWrite(in3, LOW);
	digitalWrite(in4, LOW);
	
	// Initialize Serial communication for debugging
	Serial.begin(9600);
}

void loop() {
	// Read distance from ultrasonic sensor
	long duration, distance;
	digitalWrite(trigPin, LOW);
	delayMicroseconds(2);
	digitalWrite(trigPin, HIGH);
	delayMicroseconds(10);
	digitalWrite(trigPin, LOW);
	duration = pulseIn(echoPin, HIGH);
	distance = (duration * 0.034) / 2; // Convert time to distance in cm
	
	Serial.print("Distance: ");
	Serial.println(distance);

	if (distance < 20) {
		moveClockwise();
	} else {
		stopMotors();
	}
	delay(500);
}

// Function to move motors clockwise
void moveClockwise() {
	// Set motors to maximum speed
	analogWrite(enA, 100);
	analogWrite(enB, 100);

	// Turn on motor A & B clockwise
	digitalWrite(in1, HIGH);
	digitalWrite(in2, LOW);
	digitalWrite(in3, LOW);
	digitalWrite(in4, HIGH);
}

// Function to stop motors
void stopMotors() {
	// Turn off motors
	digitalWrite(in1, LOW);
	digitalWrite(in2, LOW);
	digitalWrite(in3, LOW);
	digitalWrite(in4, LOW);
}
