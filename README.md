# black neon Biercounter V3.0

#
#
#

# Raspberry Pi GPIO Pinout und Verdrahtungsplan

## 1. Gesamte Hardwareübersicht

| **Komponente**            | **GPIO-Pin**       | **Pin-Nummer (BCM)** | **Beschreibung**                  |
|---------------------------|--------------------|----------------------|----------------------------------|
| **NeoPixel LED Ring**      | GPIO12             | Pin 32               | Datenleitung für NeoPixel LEDs   |
| **RFID-Leser (MFRC522)**   | SPI GPIO-Pins      |                      | Kommunikation über SPI           |
| - SDA                     | GPIO8 (CE0)        | Pin 24               | Chip Select für SPI              |
| - SCK                     | GPIO11 (SCLK)      | Pin 23               | Serial Clock                     |
| - MOSI                    | GPIO10 (MOSI)      | Pin 19               | Master Out Slave In              |
| - MISO                    | GPIO9 (MISO)       | Pin 21               | Master In Slave Out              |
| - RST                     | GPIO25             | Pin 22               | Reset-Pin des RFID-Readers       |
| **LCD Display (I2C)**      | I2C GPIO-Pins      |                      | I2C-Verbindung                   |
| - SDA                     | GPIO2              | Pin 3                | Datenleitung I2C                 |
| - SCL                     | GPIO3              | Pin 5                | Taktleitung I2C                  |
| - VCC                     | 5V                 | Pin 2                | Stromversorgung                  |
| - GND                     | GND                | Pin 6                | Masseverbindung                  |
| **Eingabetaster**          | GPIO-Pins          |                      | Pull-Down konfigurierte Taster   |
| - Taster 1 (K1)           | GPIO4              | Pin 7                | Taster für Eingabe               |
| - Taster 2 (K2)           | GPIO17             | Pin 11               | Taster für Eingabe               |
| - Taster 3 (K3)           | GPIO27             | Pin 13               | Taster für Eingabe               |
| - Taster 4 (K4)           | GPIO22             | Pin 15               | Taster für Eingabe               |
| **Stromversorgung**        | 5V & GND           | Pin 2, 4, 6, etc.    | Versorgung aller Komponenten     |

---

## 2. GPIO-Pinout Übersicht

Die folgende Tabelle zeigt, welche Pins am Raspberry Pi belegt sind:

| **Pin-Nummer (Physisch)** | **GPIO-Nummer (BCM)** | **Verwendung**             |
|---------------------------|-----------------------|----------------------------|
| Pin 3                    | GPIO2                 | SDA für I2C LCD Display    |
| Pin 5                    | GPIO3                 | SCL für I2C LCD Display    |
| Pin 7                    | GPIO4                 | Eingabetaster 1 (K1)       |
| Pin 11                   | GPIO17                | Eingabetaster 2 (K2)       |
| Pin 13                   | GPIO27                | Eingabetaster 3 (K3)       |
| Pin 15                   | GPIO22                | Eingabetaster 4 (K4)       |
| Pin 19                   | GPIO10                | SPI MOSI (RFID)            |
| Pin 21                   | GPIO9                 | SPI MISO (RFID)            |
| Pin 22                   | GPIO25                | RFID-Reset-Pin             |
| Pin 23                   | GPIO11                | SPI Clock (SCK, RFID)      |
| Pin 24                   | GPIO8                 | SPI Chip Select (RFID)     |
| Pin 32                   | GPIO12                | NeoPixel LED Datenleitung  |
| Pin 2/4                  | 5V                    | Stromversorgung            |
| Pin 6/14/20/25/etc.      | GND                   | Masseverbindung            |

---

## 3. Verdrahtungsdetails

### NeoPixel LED Ring
- **GPIO12** (Pin 32): Datenleitung  
- **5V** (Pin 2 oder 4): Stromversorgung  
- **GND** (Pin 6): Masse  

### RFID-Leser (MFRC522)
- **SDA (CE0):** GPIO8 (Pin 24)  
- **SCK:** GPIO11 (Pin 23)  
- **MOSI:** GPIO10 (Pin 19)  
- **MISO:** GPIO9 (Pin 21)  
- **RST:** GPIO25 (Pin 22)  
- **VCC:** 3.3V oder 5V  
- **GND:** Pin 6  

### I2C LCD Display
- **SDA:** GPIO2 (Pin 3)  
- **SCL:** GPIO3 (Pin 5)  
- **VCC:** 5V (Pin 2)  
- **GND:** Pin 6  

### Eingabetaster (K1-K4)
- **K1:** GPIO4 (Pin 7)  
- **K2:** GPIO17 (Pin 11)  
- **K3:** GPIO27 (Pin 13)  
- **K4:** GPIO22 (Pin 15)  
- **Pull-Down-Widerstände**: (Standardmäßig die vom Pi - Konfiguration im Skript:)
   ```python
   GPIO.setup(pin, GPIO.IN, pull_up_down=GPIO.PUD_DOWN)

#

# Software

#

## 1. Übersicht was so los ist

| **Softwarekomponente**      | **Beschreibung**                              | **Verwendung im Skript**         |
|------------------------------|----------------------------------------------|----------------------------------|
| **Python 3**                | Programmiersprache zur Steuerung der Hardware | Hauptsprache des Projekts        |
| **RPi.GPIO**                | GPIO-Bibliothek für Raspberry Pi             | Steuerung von GPIO-Pins          |
| **NeoPixel (Adafruit)**     | Steuerung von WS2812 LEDs (NeoPixel)          | Ansteuerung des LED-Rings        |
| **MFRC522**                 | Bibliothek für RFID-Leser                    | Kommunikation mit RFID-Modul     |
| **RPLCD**                   | I2C-Bibliothek für LCD-Ansteuerung           | Textausgabe auf dem 20x4 LCD     |
| **MySQL Connector**         | Verbindung zu MySQL-Datenbank                | Speicherung und Abfrage von Daten|
| **Flask**                   | Web-Framework für Python                     | Bereitstellung von Web-Interfaces|
| **Flask-Login**             | Benutzerverwaltung für Flask                 | Benutzer-Authentifizierung       |
| **Logging**                 | Python Logging-Modul                         | Loggen von Systemereignissen     |
| **Threading**               | Mehrfachausführung von Tasks                 | Parallele Aufgabensteuerung      |
| **time**                    | Zeitsteuerung                                 | Verzögerungen und Timing         |

---

## 2. Flask-Webserver und Benutzerverwaltung

### **Flask Webserver**
- **Konfiguration**:
   ```python
   app = Flask(__name__)
   app.config['SECRET_KEY'] = 'IhrSicheresSecretKey'

### Flask-Login
- Flask-Login wird für die Benutzeranmeldung und -verwaltung eingesetzt.
   ```python
   from flask_login import LoginManager

  login_manager = LoginManager()
  login_manager.init_app(app)
  login_manager.login_view = 'login'

## 3. Datenbankverbindung (MySQL)

### MySQL Config:

   ```python
  db = mysql.connector.connect(
      host="localhost",
      user="hierusereinfügen",
      password="hiersqlpassworteinfügen",
      database="biercounter_db"
  )

