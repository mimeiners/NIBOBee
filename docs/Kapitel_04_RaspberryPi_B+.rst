RaspberryPi Modell 2B
=====================

.. _Kamera: https://www.raspberrypi.org/products/camera-module-v2/
.. _RaspberryPi Modell 2B: https://www.raspberrypi.org/products/raspberry-pi-2-model-b/
.. _DHT22: https://www.adafruit.com/product/385
.. _Link: https://tutorials-raspberrypi.de/raspberry-pi-luftfeuchtigkeit-temperatur-messen-dht11-dht22/
.. _Links: https://www.datenreise.de/raspberry-pi-ueberwachungskamera-livestream/

In diesem Kapitel soll kurz die Installation einer Kamera_ und eines Temperatur-/Feutigkeitssensors
auf einem `RaspberryPi Modell 2B`_ beschrieben werden. Zu diesem Zweck wurden Internetrecherchen durchgeführt.


Temperatur-/Feuchtigkeitssensor
-------------------------------

Der DHT22_ ist ein Sensor zu Messung von Umgebungstemperatur und der Umgebungsfeuchtigkeit. Dieser soll auf die
NIBOBee montiert und mit Hilfe von RaspberryPi angesteuert werden. Im wesentlichen soll der Sensor die
Temperatur und die Feuchigkeit messen und die Werte dieser Größen auf den Bildschirm ausgeben.
Anweisungen zur Installatio befinden sich und folgendem Link_.
Zu beachten ist folgendes:

- Es soll ein Pull-Up Widerstand(:math:`4,7\,k\Omega - 10\,k\Omega`) von dem GPIO-Pin vorgeschaltet werden

- Bei der Ausgabeanforderung soll die Nummer des Sensors (22) und die **GPIO-Nummer** angegeben werden.

Aufgrund der vielseitigen Möglichkeiten des Betriebssystems des RaspberryPi's, wurde eine Installationsdatei erstellt.
Diese ist eine einfache Bash-Datei. Sie führt nach einander die einzelnen Installationsbefehle aus.
Zum Ausführen der Datei muss diese in das Home-Verzeichnis der RaspberryPi's Systems kopiert und mit dem Befehl

- bash Sensor_Installation.sh

ausgeführt werden.  
  
Später muss noch dem Autostart des Systems ein Befehl hinzugefügt werden. Damit würde die Messung automatisch in vorgegebenen
Abständen stattfinden.


Kamera
------

Die Intallation der Kamera ist, im gegensatz zum Temperatursensor, deutlich aufwändiger.
Wie oben bereits beschrieben, kommt, aufgrund der Montagemöglichkeit, eine originale RaspberryPi
Kamera zum einsatz. Des Weiteren kann die originale Kamera in an CSI-Anschluss des Pi's angeschlossen werden,
und blockiert somit nicht die wenigen USB-Anschlüsse.
Vor der Installation muss die Kameranutzung zunächst aktiviert werden. Die Aktivierung wird mit Hilfe von raspi-config Datei unter dem Menupunkt 5 durchgeführt. Die raspi-config Datei wird mit dem Befehl

- sudo raspi-config

aufgerufen. Nach der Aktivierung muss ein Neustart erfolgen.
Die Kamerafunltionalität kann mit dem Befehl

- raspisill -o test.jpg

getestet werden. Macht die Kamera ein Bild ist diese aktiviert und einsatzfähig.
Nun kann die Kamera zum Livestream(Direktübertragung) eingerichetet werden. Auch hier werden die
Installationsanweisungen des folgenden Links_ ausgeführt. Werden diese Anweisung fehlerfrei ausgeführt,
kann ein Livestream über die IP-Adresse oder die http-Adresse verfolgt werden. 

