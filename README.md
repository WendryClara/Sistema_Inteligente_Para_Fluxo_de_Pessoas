# Sistema_Inteligente_Para_Fluxo_de_Pessoas

Código 

#define TRIG_ENTRADA 8
#define ECHO_ENTRADA 7
#define TRIG_SAIDA 10
#define ECHO_SAIDA 9
#define LED_VERDE 2
#define LED_VERMELHO 3

int totalPessoas = 0;

bool detectouEntrada = false;
bool detectouSaida = false;

void setup() {

  Serial.begin(9600);

  pinMode(TRIG_ENTRADA, OUTPUT);
  pinMode(ECHO_ENTRADA, INPUT);

  pinMode(TRIG_SAIDA, OUTPUT);
  pinMode(ECHO_SAIDA, INPUT);

  pinMode(LED_VERDE, OUTPUT);
  pinMode(LED_VERMELHO, OUTPUT);

  digitalWrite(LED_VERDE, LOW);
  digitalWrite(LED_VERMELHO, LOW);

  Serial.println("SISTEMA INICIADO");
}

void loop() {

  float distanciaEntrada = medirDistancia(TRIG_ENTRADA, ECHO_ENTRADA);
  float distanciaSaida = medirDistancia(TRIG_SAIDA, ECHO_SAIDA);

  Serial.print("Entrada: ");
  Serial.print(distanciaEntrada);
  Serial.print(" cm");

  Serial.print(" | Saida: ");
  Serial.print(distanciaSaida);
  Serial.println(" cm");

  // =========================
  // PESSOA ENTROU
  // =========================

  if (
    distanciaEntrada <= 10 &&
    distanciaEntrada > 0 &&
    !detectouEntrada
  ) {

    totalPessoas++;

    Serial.println("PESSOA ENTROU");
    Serial.print("TOTAL NA SALA: ");
    Serial.println(totalPessoas);

    digitalWrite(LED_VERDE, HIGH);
    delay(500);
    digitalWrite(LED_VERDE, LOW);

    detectouEntrada = true;
  }

  // =========================
  // LIBERAR ENTRADA
  // =========================

  if (distanciaEntrada > 10) {
    detectouEntrada = false;
  }

  // =========================
  // PESSOA SAIU
  // =========================

  if (
    distanciaSaida <= 10 &&
    distanciaSaida > 0 &&
    !detectouSaida
  ) {

    if (totalPessoas > 0) {

      totalPessoas--;

      Serial.println("PESSOA SAIU");
      Serial.print("TOTAL NA SALA: ");
      Serial.println(totalPessoas);

      digitalWrite(LED_VERMELHO, HIGH);
      delay(500);
      digitalWrite(LED_VERMELHO, LOW);
    }

    detectouSaida = true;
  }

  // =========================
  // LIBERAR SAIDA
  // =========================

  if (distanciaSaida > 10) {

    detectouSaida = false;
  }

  delay(500);
}

// =========================
// FUNÇÃO MEDIR DISTÂNCIA
// =========================

float medirDistancia(int trig, int echo) {

  long duracao;
  float distancia;

  digitalWrite(trig, LOW);
  delayMicroseconds(2);

  digitalWrite(trig, HIGH);
  delayMicroseconds(10);

  digitalWrite(trig, LOW);

  duracao = pulseIn(echo, HIGH);

  distancia = duracao * 0.034 / 2;

  return distancia;
}
