# Robo-Seguidor-de-linha

// Definição dos pinos dos sensores
int PinoSensor1 = 2;
int PinoSensor2 = 4;
int PinoSensor3 = 7;
int PinoSensor4 = 8;
int PinoSensor5 = 12;


// Definição dos pinos dos motores
int Motor1F = 3;  // Motor 1 para frente
int Motor1T = 5;  // Motor 1 para trás
int Motor2F = 6;  // Motor 2 para frente
int Motor2T = 11; // Motor 2 para trás


int lastTurn = 0; // 1 = direita, -1 = esquerda, 0 = reto


void setup() {
  Serial.begin(9600);


  pinMode(PinoSensor1, INPUT);
  pinMode(PinoSensor2, INPUT);
  pinMode(PinoSensor3, INPUT);
  pinMode(PinoSensor4, INPUT);
  pinMode(PinoSensor5, INPUT);


  pinMode(Motor1F, OUTPUT);
  pinMode(Motor1T, OUTPUT);
  pinMode(Motor2F, OUTPUT);
  pinMode(Motor2T, OUTPUT);
}


void moveForward(int speed) {
  speed = constrain(speed, 0, 255);
  analogWrite(Motor1F, speed);
  analogWrite(Motor1T, 0);
  analogWrite(Motor2F, speed);
  analogWrite(Motor2T, 0);
}


void turnRight(int speed) {
  speed = constrain(speed, 0, 255);
  analogWrite(Motor1F, speed);
  analogWrite(Motor1T, 0);
  analogWrite(Motor2F, 0);
  analogWrite(Motor2T, speed);
}


void turnLeft(int speed) {
  speed = constrain(speed, 0, 255);
  analogWrite(Motor1F, 0);
  analogWrite(Motor1T, speed);
  analogWrite(Motor2F, speed);
  analogWrite(Motor2T, 0);
}


void stopMotors() {
  analogWrite(Motor1F, 0);
  analogWrite(Motor1T, 0);
  analogWrite(Motor2F, 0);
  analogWrite(Motor2T, 0);
}


void loop() {
  int S1 = digitalRead(PinoSensor1);
  int S2 = digitalRead(PinoSensor2);
  int S3 = digitalRead(PinoSensor3);
  int S4 = digitalRead(PinoSensor4);
  int S5 = digitalRead(PinoSensor5);


  Serial.print("S1: "); Serial.print(S1);
  Serial.print(" S2: "); Serial.print(S2);
  Serial.print(" S3: "); Serial.print(S3);
  Serial.print(" S4: "); Serial.print(S4);
  Serial.print(" S5: "); Serial.println(S5);


  int VEL_RETA = 70;
  int VEL_CURVA = 80;
  int VEL_CURVA_FECHADA = 100;


  // Se perder a linha, tenta buscar no lado do último movimento
  if (S1 == LOW && S2 == LOW && S3 == LOW && S4 == LOW && S5 == LOW) {
    if (lastTurn == 1) {
      turnRight(80);
    } else if (lastTurn == -1) {
      turnLeft(80);
    } else {
      turnRight(80);  // padrão se estava reto
    }
    delay(150);
    return;
  }


  // Todas as condições originais:
  if (S3 == HIGH) {
    moveForward(VEL_RETA);
    lastTurn = 0;
  } else if (S1 == LOW && S2 == HIGH && S3 == HIGH && S4 == HIGH && S5 == LOW) {
    moveForward(VEL_RETA);
    lastTurn = 0;
  } else if (S1 == HIGH && S2 == HIGH && S3 == HIGH && S4 == HIGH && S5 == LOW) {
    turnRight(VEL_CURVA_FECHADA);
    lastTurn = 1;
  } else if (S1 == LOW && S2 == HIGH && S3 == HIGH && S4 == HIGH && S5 == HIGH) {
    turnLeft(VEL_CURVA_FECHADA);
    lastTurn = -1;
  } else if (S1 == HIGH && S2 == HIGH && S3 == HIGH && S4 == LOW && S5 == LOW) {
    turnRight(VEL_CURVA_FECHADA);
    lastTurn = 1;
  } else if (S1 == LOW && S2 == LOW && S3 == HIGH && S4 == HIGH && S5 == HIGH) {
    turnLeft(VEL_CURVA_FECHADA);
    lastTurn = -1;
  } else if (S1 == HIGH && S2 == HIGH && S3 == LOW && S4 == LOW && S5 == LOW) {
    turnRight(VEL_CURVA);
    lastTurn = 1;
  } else if (S1 == LOW && S2 == LOW && S3 == LOW && S4 == HIGH && S5 == HIGH) {
    turnLeft(VEL_CURVA);
    lastTurn = -1;
  } else if (S1 == HIGH && S2 == LOW && S3 == LOW && S4 == LOW && S5 == LOW) {
    turnRight(VEL_CURVA_FECHADA);
    lastTurn = 1;
  } else if (S1 == LOW && S2 == LOW && S3 == LOW && S4 == LOW && S5 == HIGH) {
    turnLeft(VEL_CURVA_FECHADA);
    lastTurn = -1;
  } else {
    stopMotors();
  }
}



