# Practica - Encender led con Node-RED

## Introducción

### Descripción

En esta práctica se ocupa un relay para encender y apagar un led mediante un botón en Node-red.

## Material Necesario

Para realizar esta práctica se ocuparon las siguientes herramientas y componentes:

- [Wokwi](https://wokwi.com/)
- [Node-red](https://nodejs.org/en)
- Relay


## Instrucciones

### Arranque de Node-red 

1. Dentro de Node-red, colocar un bloque ```mqtt out```, este hará la conexión mediante una IP.

2. Copiar la IP ```52.29.79.184```, porteriormente dirigirse al ```localhost:1880```, hacer *doble click* en **mqtt out**, en el apartado *Server*, hacer click en el ícono de lápiz y pegar en el *Server*. Cambiar el nombre del bloque en *Topic*, en este caso se utilizó el nombre de "MichelleCG1".

3. En la esquina superior derecha (debajo del ícono de propiedades), se encuentra un ícono de triángulo, hacer click en el apartado _dashboard_, seleccionar _tab_>_group_>_spacer_. Cambiar el nombre a _Botón_.

4. Añadir un bloque _switch_, hacer _doble click_, en el apartado **Group** seleccionar el grupo de _Botón_, cambiar el nombre a "Encender LED". Por último, en _On Payload_, cambiar a _string_ y el valor 1. Para _Off Payload_, igualmente cambiar a _string_ y el valor de 0. 

5. Una vez hecho, ingresar a la página de [WOKWI](https://wokwi.com/) se selecciona la tarjeta **ESP32** y un relay.

6. Una vez seleccionado la tarjeta ```Esp32``` junto a los componentes, en la parte izquierda se encuentra la pestaña de código donde se agrega lo siguiente:
```
#include <WiFi.h>
#include <PubSubClient.h>

const char* ssid = "Wokwi-GUEST";
const char* password = "";
const char* mqttServer = "52.29.79.184";
const int mqttPort = 1883;
const char* mqttUser = "MichelleCG";
const char* mqttPassword = "1234";
const char* mqttTopic = "MichelleCG1";

WiFiClient espClient;
PubSubClient client(espClient);

int ledPin = 13; // Pin del LED

void setup() {
  pinMode(ledPin, OUTPUT);
  Serial.begin(115200);
  setup_wifi();
  client.setServer(mqttServer, mqttPort);
  client.setCallback(callback);
}

void loop() {
  if (!client.connected()) {
    reconnect();
  }
  client.loop();
}

void setup_wifi() {
  delay(10);
  Serial.println();
  Serial.print("Conectando a: ");
  Serial.println(ssid);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("");
  Serial.println("Conectado a la red WiFi");
}

void reconnect() {
  while (!client.connected()) {
    Serial.print("Intentando conexión MQTT...");
    if (client.connect("ESP32Client", mqttUser, mqttPassword)) {
      Serial.println("Conectado");
      client.subscribe(mqttTopic);
    } else {
      Serial.print("Error de conexión, rc=");
      Serial.print(client.state());
      Serial.println(" Intentando de nuevo en 5 segundos");
      delay(5000);
    }
  }
}

void callback(char* topic, byte* payload, unsigned int length) {
  Serial.print("Mensaje recibido: [");
  Serial.print(topic);
  Serial.print("] ");
  for (int i = 0; i < length; i++) {
    Serial.print((char)payload[i]);
  }
  Serial.println();

  if (strcmp(topic, mqttTopic) == 0) {
    if ((char)payload[0] == '1') {
      digitalWrite(ledPin, HIGH);
    } else {
      digitalWrite(ledPin, LOW);
    }
  }
}
``` 
7. En la pestaña de *Library Manager*, instalar las librerías de **ArduinoJson** y **PubSubClient** como se muestra en la siguente imagen.

![](https://github.com/Michellecg/Node-red_con_sensor_de_distancia/blob/main/Lib_Ult.PNG)

8. Hacer la conexión del **sensor ultrasónico de distancia** y **DHT22** a la tarjeta **ESP32** como se muestra en la siguente imagen.

![](https://github.com/Michellecg/Node-red_Encender-led/blob/main/Conex_Relay.PNG)

9. En Node-red, se debe observar de la siguiente manera el diagrama:

![](https://github.com/Michellecg/Node-red_Encender-led/blob/main/Diag_Node-red.PNG)

## Resultados

El dashboard muestra el botón que maneja el LED, cuando hace la conexión se manda la señal al wokwi y se enciende o apaga el LED.

![](https://github.com/Michellecg/Node-red_Encender-led/blob/main/Resultados.PNG)

# Créditos

Desarrollado por Michelle Cuatlapantzi González

- [GitHub](https://github.com/Michellecg/)