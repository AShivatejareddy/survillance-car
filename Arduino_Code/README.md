# Arduino Code

This folder contains the ESP32-CAM firmware for the IoT Surveillance Car.

#include "esp_camera.h"
#include <WiFi.h>
#include <WebServer.h>

/* ================= CAMERA PINS (AI THINKER) ================= */
#define PWDN_GPIO_NUM 32
#define RESET_GPIO_NUM -1
#define XCLK_GPIO_NUM 0
#define SIOD_GPIO_NUM 26
#define SIOC_GPIO_NUM 27
#define Y9_GPIO_NUM 35
#define Y8_GPIO_NUM 34
#define Y7_GPIO_NUM 39
#define Y6_GPIO_NUM 36
#define Y5_GPIO_NUM 21
#define Y4_GPIO_NUM 19
#define Y3_GPIO_NUM 18
#define Y2_GPIO_NUM 5
#define VSYNC_GPIO_NUM 25
#define HREF_GPIO_NUM 23
#define PCLK_GPIO_NUM 22

/* ================= MOTOR PINS ================= */
#define ENA 12
#define IN1 13
#define IN2 15

#define ENB 2
#define IN3 14
#define IN4 4 // DO NOT use 4 here (flash LED uses 4)

/* ================= BUILT IN FLASH LED ================= */
#define FLASH_LED 4

/* ================= WIFI ================= */
const char* ssid = "ESP32-CAR";
const char* password = "12345678";

WebServer server(80);

/* ================= PWM ================= */
#define PWM_FREQ 5000
#define PWM_RES 8

int speedValue = 150;

/* ================= HTML ================= */
String webpage = R"====(
<!DOCTYPE html>
<html>
<head>
<meta name="viewport" content="width=device-width, initial-scale=1">
<style>
button {width:70px;height:70px;font-size:30px;margin:5px;}
</style>
</head>
<body align=center>
<h2>ESP32 CAM CAR</h2>
<img src="/stream" width="320"><br><br>

<button onclick="fetch('/forward')">⬆</button><br>
<button onclick="fetch('/left')">⬅</button>
<button onclick="fetch('/stop')"> </button>
<button onclick="fetch('/right')">➡</button><br>
<button onclick="fetch('/backward')">⬇</button>

<br><br>
Speed:<br>
<input type="range" min="0" max="255" value="150"
oninput="fetch('/speed?value='+this.value)">

<br><br>
LED Brightness:<br>
<input type="range" min="0" max="255" value="0"
oninput="fetch('/light?value='+this.value)">

</body>
</html>
)====";

/* ================= MOTOR CONTROL ================= */
void stopCar(){
  digitalWrite(IN1,LOW);
  digitalWrite(IN2,LOW);
  digitalWrite(IN3,LOW);
  digitalWrite(IN4,LOW);
}

void forward(){
  digitalWrite(IN1,HIGH);
  digitalWrite(IN2,LOW);
  digitalWrite(IN3,HIGH);
  digitalWrite(IN4,LOW);
}

void backward(){
  digitalWrite(IN1,LOW);
  digitalWrite(IN2,HIGH);
  digitalWrite(IN3,LOW);
  digitalWrite(IN4,HIGH);
}

void left(){
  digitalWrite(IN1,LOW);
  digitalWrite(IN2,HIGH);
  digitalWrite(IN3,HIGH);
  digitalWrite(IN4,LOW);
}

void right(){
  digitalWrite(IN1,HIGH);
  digitalWrite(IN2,LOW);
  digitalWrite(IN3,LOW);
  digitalWrite(IN4,HIGH);
}

/* ================= CAMERA STREAM ================= */
void startCamera(){
  camera_config_t config;
  config.ledc_channel = LEDC_CHANNEL_0;
  config.ledc_timer = LEDC_TIMER_0;
  config.pin_d0 = Y2_GPIO_NUM;
  config.pin_d1 = Y3_GPIO_NUM;
  config.pin_d2 = Y4_GPIO_NUM;
  config.pin_d3 = Y5_GPIO_NUM;
  config.pin_d4 = Y6_GPIO_NUM;
  config.pin_d5 = Y7_GPIO_NUM;
  config.pin_d6 = Y8_GPIO_NUM;
  config.pin_d7 = Y9_GPIO_NUM;
  config.pin_xclk = XCLK_GPIO_NUM;
  config.pin_pclk = PCLK_GPIO_NUM;
  config.pin_vsync = VSYNC_GPIO_NUM;
  config.pin_href = HREF_GPIO_NUM;
  config.pin_sscb_sda = SIOD_GPIO_NUM;
  config.pin_sscb_scl = SIOC_GPIO_NUM;
  config.pin_pwdn = PWDN_GPIO_NUM;
  config.pin_reset = RESET_GPIO_NUM;
  config.xclk_freq_hz = 20000000;
  config.pixel_format = PIXFORMAT_JPEG;

  config.frame_size = FRAMESIZE_QVGA;
  config.jpeg_quality = 12;
  config.fb_count = 1;

  esp_camera_init(&config);
}

void handleStream(){
  WiFiClient client = server.client();
  String response = "HTTP/1.1 200 OK\r\n";
  response += "Content-Type: multipart/x-mixed-replace; boundary=frame\r\n\r\n";
  server.sendContent(response);

  while(client.connected()){
    camera_fb_t * fb = esp_camera_fb_get();
    if(!fb) return;

    server.sendContent("--frame\r\n");
    server.sendContent("Content-Type: image/jpeg\r\n\r\n");
    server.sendContent((char*)fb->buf, fb->len);
    server.sendContent("\r\n");

    esp_camera_fb_return(fb);
  }
}

/* ================= SETUP ================= */
void setup(){
  Serial.begin(115200);

  pinMode(IN1,OUTPUT);
  pinMode(IN2,OUTPUT);
  pinMode(IN3,OUTPUT);
  pinMode(IN4,OUTPUT);

  // PWM for motors
  ledcAttach(ENA, PWM_FREQ, PWM_RES);
  ledcAttach(ENB, PWM_FREQ, PWM_RES);
  ledcWrite(ENA, speedValue);
  ledcWrite(ENB, speedValue);

  // PWM for Flash LED
  ledcAttach(FLASH_LED, PWM_FREQ, PWM_RES);
  ledcWrite(FLASH_LED, 0);

  startCamera();

  WiFi.softAP(ssid,password);
  Serial.println(WiFi.softAPIP());

  server.on("/", [](){ server.send(200,"text/html",webpage); });
  server.on("/stream", handleStream);

  server.on("/forward", forward);
  server.on("/backward", backward);
  server.on("/left", left);
  server.on("/right", right);
  server.on("/stop", stopCar);

  server.on("/speed", [](){
    speedValue = server.arg("value").toInt();
    ledcWrite(ENA, speedValue);
    ledcWrite(ENB, speedValue);
    server.send(200,"text/plain","OK");
  });

  server.on("/light", [](){
    int brightness = server.arg("value").toInt();
    ledcWrite(FLASH_LED, brightness);
    server.send(200,"text/plain","OK");
  });

  server.begin();
}

/* ================= LOOP ================= */
void loop(){
  server.handleClient();
}

