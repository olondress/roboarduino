# Documentação do Robô Arduino

Este repositório contém o código para um robô baseado em Arduino com três funcionalidades principais: sensor ultrassônico para direção, seguidor de linha e controle por Bluetooth.

## Índice
- [Requisitos de Hardware](#requisitos-de-hardware)
- [Requisitos de Software](#requisitos-de-software)
- [Configuração](#configuração)
- [Sensor Ultrassônico](#sensor-ultrassônico)
- [Seguidor de Linha](#seguidor-de-linha)
- [Controle por Bluetooth](#controle-por-bluetooth)
- [Uso](#uso)

## Requisitos de Hardware
1. Arduino Uno
2. Sensor Ultrassônico (e.g., HC-SR04)
3. Sensores de Linha
4. Módulo Bluetooth (e.g., HC-05 ou HC-06)
5. Servo Motor
6. Driver de Motor (e.g., L298N)
7. Rodas e Chassi
8. Jumpers
9. Fonte de Alimentação (Pacote de Baterias)

## Requisitos de Software
1. Arduino IDE

## Configuração
1. Conecte o sensor ultrassônico ao Arduino:
   - Pino Trigger no A0
   - Pino Echo no A1
2. Conecte os sensores de linha ao Arduino:
   - Sensor 1 no pino 3
   - Sensor 2 no pino 12
3. Conecte o módulo Bluetooth ao Arduino:
   - TX no pino 9
   - RX no pino 8
4. Conecte os motores e o driver de motor ao Arduino:
   - Pinos do motor nos pinos 4, 5, 6 e 7
5. Conecte o servo motor ao pino 11.

## Sensor Ultrassônico

### Código
```cpp
#include <Servo.h>

Servo servo_11;

long readUltrasonicDistance(int triggerPin, int echoPin)
{
  pinMode(triggerPin, OUTPUT);
  digitalWrite(triggerPin, LOW);
  delayMicroseconds(2);
  digitalWrite(triggerPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(triggerPin, LOW);
  pinMode(echoPin, INPUT);
  return pulseIn(echoPin, HIGH);
}

void setup()
{
  servo_11.attach(11, 500, 2500);
  pinMode(4, OUTPUT);
  pinMode(5, OUTPUT);
  pinMode(6, OUTPUT);
  pinMode(7, OUTPUT);
  servo_11.write(90);
}

void loop(){
  int frente =  0.01723 * readUltrasonicDistance(A0, A1);
  int direita;
  int esquerda;
  digitalWrite(4, LOW);
  digitalWrite(5, HIGH);
  digitalWrite(6, HIGH);
  digitalWrite(7, LOW);

  if (frente < 30) {
    digitalWrite(4, LOW);
    analogWrite(5, 0);
    analogWrite(6, 0);
    digitalWrite(7, LOW);

    servo_11.write(0);
    delay(1000); 
    direita = 0.01723 * readUltrasonicDistance(A0, A1);
    servo_11.write(180);
    delay(2000); 
    esquerda = 0.01723 * readUltrasonicDistance(A0, A1);

    if (direita > esquerda) {
      digitalWrite(4, LOW);
      digitalWrite(5, HIGH);
      digitalWrite(6, LOW);
      digitalWrite(7, LOW);
      delay(400);
      digitalWrite(4, LOW);
      digitalWrite(5, HIGH);
      digitalWrite(6, HIGH);
      digitalWrite(7, LOW);
    }
    if (direita < esquerda) {
      digitalWrite(4, LOW);
      digitalWrite(5, LOW);
      digitalWrite(6, HIGH);
      digitalWrite(7, LOW);
      delay(400);
      digitalWrite(4, LOW);
      digitalWrite(5, HIGH);
      digitalWrite(6, HIGH);
      digitalWrite(7, LOW);
    }
  }
  servo_11.write(90);
  delay(200);
}
```

## Seguidor de Linha

### Código
```cpp
int velocidadeD = 255; 
int velocidadeE = 255;
int direita = 0;
int esquerda = 0;

void setup()
{
  pinMode(12, INPUT);
  pinMode(3, INPUT);
  pinMode(7, OUTPUT);
  pinMode(6, OUTPUT);
  pinMode(5, OUTPUT);
  pinMode(4, OUTPUT);
}

void loop()
{
  if (digitalRead(3) == HIGH && digitalRead(12) == HIGH) {
    digitalWrite(7, LOW);
    digitalWrite(6, LOW);
    digitalWrite(5, LOW);
    digitalWrite(4, LOW);
  }
  if(digitalRead(3) == LOW && digitalRead(12) == LOW){
    analogWrite(6, 200);
    digitalWrite(7, LOW);
    digitalWrite(4, LOW);
    analogWrite(5, 200);

  }else if (digitalRead(3) == HIGH && digitalRead(12) == LOW) {
    analogWrite(6, 200);
    digitalWrite(7, LOW);
    digitalWrite(4, HIGH);
    analogWrite(5, 55);
  } else if(digitalRead(3) == LOW && digitalRead(12) == HIGH) {
    analogWrite(6, 55);
    digitalWrite(7, HIGH);
    digitalWrite(4, LOW);
    analogWrite(5, 200);
  }
  delay(10);
}
```

## Controle por Bluetooth

### Código
```cpp
#include <SoftwareSerial.h>

// Pinos do Bluetooth
int bluetoothTx = 9;
int bluetoothRx = 8;

SoftwareSerial bluetooth(bluetoothRx, bluetoothTx);

void setup() 
{
  Serial.begin(9600);
  bluetooth.begin(9600);

  pinMode(4, OUTPUT);
  pinMode(5, OUTPUT);
  pinMode(6, OUTPUT);
  pinMode(7, OUTPUT);
  pinMode(13, OUTPUT);
}

void loop() 
{
   if(bluetooth.available() > 0)
   {
    char direcao = bluetooth.read();
    // Andar para frente
    if (direcao == 'F') {
      digitalWrite(4,LOW);
      digitalWrite(5,HIGH);
      digitalWrite(6,HIGH);
      digitalWrite(7,LOW);
      digitalWrite(13,HIGH);
    }
    // Andar para trás
    if(direcao == 'B') {
      digitalWrite(4, HIGH);
      digitalWrite(5, LOW);
      digitalWrite(6, LOW);
      digitalWrite(7, HIGH);
    }
    // Andar para a direita
    if(direcao == 'R') {
      digitalWrite(4, LOW);
      digitalWrite(5, HIGH);
      digitalWrite(6, LOW);
      digitalWrite(7, LOW);
    }
    // Andar para a esquerda
    if(direcao == 'L'){
      digitalWrite(4, LOW);
      digitalWrite(5, LOW);
      digitalWrite(6, HIGH);
      digitalWrite(7, LOW);
    }
    // Andar para trás-direita
    if(direcao == 'P') {
      digitalWrite(4, HIGH);
      digitalWrite(5, LOW);
      digitalWrite(6, LOW);
      digitalWrite(7, LOW);
    }    
    // Andar para trás-esquerda
    if(direcao == 'S') {
      digitalWrite(4, LOW);
      digitalWrite(5, LOW);
      digitalWrite(6, LOW);
      digitalWrite(7, HIGH);
    }
   }
}
```

## Uso
1. Faça o upload do código respectivo para a funcionalidade que você deseja testar no Arduino usando o Arduino IDE.
2. Para o sensor ultrassônico, certifique-se de que o servo motor está corretamente conectado e que o sensor está devidamente ligado.
3. Para o seguidor de linha, certifique-se de que os sensores estão posicionados corretamente para detectar a linha.
4. Para o controle por Bluetooth, emparelhe seu módulo Bluetooth com um dispositivo móvel e use um aplicativo de terminal Bluetooth para enviar comandos ('F' para frente, 'B' para trás, 'L' para esquerda, 'R' para direita, 'P' para trás-direita, 'S' para trás-esquerda).
