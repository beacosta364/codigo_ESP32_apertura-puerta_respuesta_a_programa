# codigo_ESP32_apertura-puerta_respuesta_a_programa
Este código se puede cargar en un microcontrolador esp32 lo cual es la configuración servidor embebido para el control de acceso electrónico para una bodega, dicho control es implementado en pretesis 



#include <WiFi.h>
#include <WebServer.h>

const char* ssid = "ANKERRUY";         
const char* password = "Monaco04"; 

WebServer server(80); 

const int electroiman = 13;  // Ajusta el pin según tu ESP32

void handleRoot() {
  String html = "<html><head><title>Control Electroimán</title></head>";
  html += "<script>";
  html += "function activarElectroiman() {";
  html += "var xhr = new XMLHttpRequest();";
  html += "xhr.open('GET', '/activar', true);";
  html += "xhr.send();";
  html += "}";
  html += "</script>";
  html += "</head><body>";
  html += "<h1>Control de Electroimán</h1>";
  html += "<p><button onclick=\"activarElectroiman()\">Abrir</button></p>";
  html += "</body></html>";

  server.send(200, "text/html", html);
}

void handleActivar() {
  digitalWrite(electroiman, HIGH);
  delay(1000);  // Mantiene el electroimán activo por 1 segundo
  server.send(200, "text/plain", "Electroimán activado");
  digitalWrite(electroiman, LOW); 
}

void setup() {
  Serial.begin(115200);
  delay(10);
  
  pinMode(electroiman, OUTPUT); 
  digitalWrite(electroiman, LOW);

  WiFi.begin(ssid, password);
  Serial.print("Conectando a ");
  Serial.println(ssid);

  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.print(".");
  }
  Serial.println();
  Serial.print("Conectado! IP: ");
  Serial.println(WiFi.localIP());

  server.on("/", handleRoot);
  server.on("/activar", handleActivar);

  server.begin(); 
  Serial.println("Servidor HTTP iniciado");
}

void loop() {
  server.handleClient(); 
}
