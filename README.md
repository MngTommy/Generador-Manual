
const int pinCapacitor = 34;


const float VREF = 3.3;             
const int ADC_RESOLUTION = 4095;    
const float VOLTAJE_OBJETIVO = 2.7; 
const float OFFSET_CALIBRACION = 0.14; 

void setup() {
  Serial.begin(115200);
  

  analogReadResolution(12);
  analogSetAttenuation(ADC_11db); 
  
  delay(2000);
  Serial.println("=========================================");
  Serial.println("   PRUEBA DE TIEMPO DE CARGA (HASTA 2.7V)");
  Serial.println("=========================================");
  Serial.println("¡EMPIEZA A GIRAR LA MANIVELA AHORA!");
  Serial.println("Tiempo (s) | Voltaje (V)");
  Serial.println("-----------------------------------------");
}

void loop() {
  static int tiempoTranscurrido = 0;
  static bool objetivoAlcanzado = false;

  if (!objetivoAlcanzado) {

    long sumaADC = 0;
    int muestras = 50;
    for (int i = 0; i < muestras; i++) {
      sumaADC += analogRead(pinCapacitor);
      delay(2); 
    }
    float promedioADC = (float)sumaADC / muestras;

    float voltaje = 0.0;
    if (promedioADC > 10) { 
      voltaje = ((promedioADC / ADC_RESOLUTION) * VREF) + OFFSET_CALIBRACION;
    }

    Serial.print("   ");
    if (tiempoTranscurrido < 10) Serial.print("0"); 
    Serial.print(tiempoTranscurrido);
    Serial.print(" s     |   ");
    Serial.print(voltaje, 2);
    Serial.println(" V");

    if (voltaje >= VOLTAJE_OBJETIVO) {
      Serial.println("-----------------------------------------");
      Serial.println("¡META ALCANZADA! VOLTAJE DE 2.7V LOGRADO.");
      Serial.print("El dinamo tardó ");
      Serial.print(tiempoTranscurrido);
      Serial.println(" segundos en cargar el capacitor a 2.7V.");
      Serial.println("=========================================");
      
      objetivoAlcanzado = true;
    } else {
      tiempoTranscurrido++;
      delay(900); 
    }
  }
}
