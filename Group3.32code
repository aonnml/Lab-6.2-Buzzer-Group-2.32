#include <Arduino.h>

#define BUZZER_PIN 19
#define BUTTON_PIN 18

const int melody[] = {  
    1319, 1319, 1319, 1319, 1319, 1319, 1319, 1568, 1047, 1175, 1319,
    1397, 1397, 1397, 1397, 1397, 1319, 1319, 1319, 1319, 1568, 1568, 1397, 1175, 1047
};
const int noteDurations[] = { 
    250, 250, 500, 250, 250, 500, 250, 250, 250, 250, 500, 
    250, 250, 500, 250, 250, 250, 250, 250, 250, 250, 250, 250, 500, 500
};

volatile int speedLevel = 2;
const float speedMultiplier[] = {2.0, 1.5, 1.0, 0.75, 0.5};
volatile bool buttonPressed = false;

hw_timer_t *My_timer = NULL;
volatile int noteIndex = 0;
volatile bool isPlaying = false;

void IRAM_ATTR onTimer() {
    if (isPlaying) {
        digitalWrite(BUZZER_PIN, !digitalRead(BUZZER_PIN)); // Toggle Buzzer
    }
}

void IRAM_ATTR handleButton() {
    buttonPressed = true;
}

void playNote() {
    if (noteIndex >= sizeof(melody) / sizeof(melody[0])) {
        noteIndex = 0;
    }
    
    int frequency = melody[noteIndex];
    int duration = noteDurations[noteIndex] * speedMultiplier[speedLevel];
    int halfPeriod = (1000000 / frequency) / 2;
    
    timerAlarm(My_timer, halfPeriod, true, 0);
    isPlaying = true;
    
    delay(duration);
    isPlaying = false;
    timerAlarm(My_timer, 1000000, true, 0);
    digitalWrite(BUZZER_PIN, LOW);
    delay(50);
    
    noteIndex++;
}

void setup() {
    pinMode(BUZZER_PIN, OUTPUT);
    pinMode(BUTTON_PIN, INPUT_PULLUP);
    
    attachInterrupt(BUTTON_PIN, handleButton, FALLING);
    
    My_timer = timerBegin(1000000);
    timerAttachInterrupt(My_timer, &onTimer);
    timerAlarm(My_timer, 1000000, true, 0);
    
    Serial.begin(115200);
}

void loop() {
    if (buttonPressed) {
        speedLevel = (speedLevel + 1) % 5;
        Serial.print("Speed Level: ");
        Serial.println(speedLevel + 1);
        buttonPressed = false;
    }
    playNote();
}

