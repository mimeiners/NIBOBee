.. NIBOBeeRSP_ZeroW documentation master file, created by
   sphinx-quickstart on Wed Feb 26 10:35:56 2020.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Raspberry Pi Zero W 
============================================

Das Ziel
--------

Das Ziel dieses Prokjekts ist es herauszufinden, ob sich der Raspberry Pi 3B+ auf der NIBOBee durch einen Raspberry Pi Zero ersetzen lässt. Für den Vergleich haben wir einen Raspberry Pi Zero W verwendet. Das „W“ bezieht sich dabei auf das verbaute Wlan-Modul. Ansonsten unterscheidet sich der Raspberry Pi Zero nicht vom Pi Zero W. Bei unseren Tests haben wir uns auf die Bereiche der Temperatur- und Feuchtigkeitsmessung, die Übertragung eines Livestreams, so wie auf das Verwenden des Bluetoothmoduls beschränkt.

+------------------------+--------------------------------------------------+-----------------------------------------+
|                        |                 Raspberry Pi 3B+                 |           Raspberry Pi Zero W           |
+========================+==================================================+=========================================+
| Prozessor              | 1.4GHz 64-bit Quadcore-Prozessor                 | 1GHz Singlecore-Prozessor               |
+------------------------+--------------------------------------------------+-----------------------------------------+
| RAM                    | 1GB LPDDR2 SDRAM                                 | 512MB SRAM                              |
+------------------------+--------------------------------------------------+-----------------------------------------+
| Ethernet               | Bis zu 300Mb/s                                   | Nein                                    |
+------------------------+--------------------------------------------------+-----------------------------------------+
| Wlan                   | 2,4GHz u. 5GHz, b/g/n/ac                         | 2,4GHz, b/g/n                           |
+------------------------+--------------------------------------------------+-----------------------------------------+
| Bluetooth              | 4.2 LE                                           | 4.1 LE                                  |
+------------------------+--------------------------------------------------+-----------------------------------------+
| USB-Schnittstellen     | 4xUSB 2.0                                        | 1xMicro-USB                             |
+------------------------+--------------------------------------------------+-----------------------------------------+
| HDMI                   | Full-size HDMI                                   | Mini HDMI                               |
+------------------------+--------------------------------------------------+-----------------------------------------+
| Power input            | 5V/2.5A DC                                       | 5V/100-140mA DC via Micro-USB           |
+------------------------+--------------------------------------------------+-----------------------------------------+
| Kameraschnittstelle    | CSI Kamera port für Raspberry Pi Kamera          | CSI Kamera port für Raspberry Pi Kamera |
+------------------------+--------------------------------------------------+-----------------------------------------+
| Displayschittstelle    | DSI Bildschirm port für Raspberry Pi touchscreen | Nein                                    |
+------------------------+--------------------------------------------------+-----------------------------------------+
| Serielle Schnittstelle | Extended 40-pin GPIO header                      | 40-pin GPIO header                      |
+------------------------+--------------------------------------------------+-----------------------------------------+
| USB-Schnittstellen     | 4xUSB 2.0                                        | 1xMicro-USB                             |
+------------------------+--------------------------------------------------+-----------------------------------------+
| HDMI                   | Full-size HDMI                                   | Mini HDMI                               |
+------------------------+--------------------------------------------------+-----------------------------------------+
| Power input            | 5V/2.5A DC                                       | 5V/100-140mA DC via Micro-USB           |
+------------------------+--------------------------------------------------+-----------------------------------------+
| Kameraschnittstelle    | CSI Kamera port für Raspberry Pi Kamera          | CSI Kamera port für Raspberry Pi Kamera |
+------------------------+--------------------------------------------------+-----------------------------------------+
| Displayschittstelle    | DSI Bildschirm port für Raspberry Pi touchscreen | Nein                                    |
+------------------------+--------------------------------------------------+-----------------------------------------+
| Serielle Schnittstelle | Extended 40-pin GPIO header                      | 40-pin GPIO header                      |
+------------------------+--------------------------------------------------+-----------------------------------------+

Der Temperatur- und Feuchtigkeitssensor
---------------------------------------
Dank identischer Pinbelegung lässt sich der Temperatur- und Feuchtigkeitssensor an den Raspberry Pi Zero genau wie an den Raspberry Pi 3B+ anschließen. Auch `das Programm zur Abfrage der Messdaten <https://nibobee.readthedocs.io/de/latest/Kapitel_05_RaspberryPi3.html#die-fertigen-pythonscripte>`_ bleibt das Gleiche. Die Abfrage dauert hier um die 10 Sekunden.

.. image:: img/luftfeuchtigkeit_DHT11_Steckplatine.png
*Anschluss des Temperatur- und Feuchtigkeitssensors an den Raspberry Pi*

Installation der Kamera
-----------------------
Die Kamera wird über die auf der Platine verbaute Kameraschnittstelle mithilfe eines Flachbandkabels verbunden und nimmt somit keinen USB-Anschluss in Anspruch. Da die Kameraschnittstelle des Raspberry Pi 3B+ sich von der des Pi Zero in der Breite unterscheidet, mussten wir zuerst ein passendes Verbindungskabel kaufen. Danach haben nach `dieser Anleitung <https://www.datenreise.de/raspberry-pi-ueberwachungskamera-livestream/>`_ die Funktionalität der Kamera getestet. Auch hier konnten keine Unterschiede zum Raspberry Pi 3B+ festgestellt werden.

Livestream mit dem Raspberry Pi Zero W
--------------------------------------
Wie eingangs erwähnt, haben wir uns aufgrund des Wlan-Moduls für den Raspberry Pi Zero W entschieden, ansonsten hätten wir uns noch einen Wlan-Adapter kaufen müssen. 
Zuerst haben wir versucht, den Livestream per VLC zu übertragen. Dazu haben wir nach `dieser Anleitung <https://tutorials-raspberrypi.de/raspberry-pi-ueberwachungskamera-livestream-einrichten/>`_ gearbeitet. Leider stellte sich heraus, dass der Singlecore-Prozessor des Raspberry Pi Zero W nicht leistungsfähig genug ist, um die Übertragung per VLC zu bewältigen. Es kam zu sehr hohen Latenzen und einer starken Erwärmung des Prozessors. Deshalb haben wir uns nach einem anderen Programm umgesehen und sind letztendlich auf GStreamer gestoßen. Dieses Programm ermöglicht eine einfachere Übertragung mithilfe der Windows-Konsole. Um Gstreamer nutzen zu können mussten wir das Programm mit dem Befehl 

.. code-block:: python

 sudo apt-get install gstreamer1.0-tools
 

installieren. Danach mussten wir eine Datei auf dem Raspberry Pi Zero W erstellen, die GStreamer starten konnte. Dazu haben wir mit 

.. code-block:: python
 
 sudo nano livestream.sh
 

eine Shell-Datei direkt auf dem Raspberry Pi Zero W erstellt. In die nun geöffnete Datei haben wir folgenden Code eingetragen:

.. code-block:: python

 #!/bin/bash
 raspivid –n –t 0 –w 640 –h 480 –fps 30 –b 500000 –o  - |gst-launch-1.0 –e –vvv fdsrc ! h264parse ! rtph264pay pt=96 config-interval=5 ! udpsink host={IP-ADRESSE-DES-EMPFÄNGERS} port=5000
 

Dieser Code sorgt bei Ausführung dafür, dass der bereits vorinstallierte Videodienst des Raspberry Pi Zero W gestartet und die für die Übetragung nötigen Parameter ausgeführt werden.

|

+------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Erklärung der einzelnen Parameter:                                                                                                                                                                           	 |
+====================================+===========================================================================================================================================================================+
| raspivid                           | startet Programm für Videos mit dem Raspberry Pi                                                                                                                          |
+------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| -n                                 | sorgt dafür, dass kein Video über den Raspberry Pi ausgegeben wird                                                                                                        |
+------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| -t 0                               | Timer für die Dauer des übertragenen Videos, 0=Stream/Video bis zur manuellen Beendung                                                                                    |
+------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| -w                                 | Weite des übertragenen Videos, Angabe in Pixeln                                                                                                                           |
+------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| -h                                 | Höhe des übertragenen Videos, Angabe in Pixeln                                                                                                                            |
+------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| -fps                               | Anzahl der übertragenen Bilder pro Sekunde                                                                                                                                |
+------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| -b                                 | Bitrate der Übertragung, zwischen 150‘000 und 700‘000 sorgen für ein gutes Bild ohne hohe Latenz (<1 Sekunde)                                                             |
+------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| -o                                 | bei jeder Übertragung wird gleichzeitig eine Videodatei erstellt, die man sich im nachhinein anschauen kann, mit diesem Parameter kann der Speicherort angegeben werden   |
+------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| -rot                               | dreht das Video um 0 bis 360 Grad, falls die Kamera nicht in Originalposition installiert werden kann                                                                     |
+------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| host                               | hier muss die IP-Adresse des Empfägergeräts angegeben werden. Dies kann ein einzelner PC oder ein Server sein                                                             |
+------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

|

Um den Stream jetzt auch empfangen zu können, mussten wir GStreamer auch auf dem PC installieren. Dazu sind wir auf die `Homepage <https://gstreamer.freedesktop.org/download/>`_ des Programms gegangen und haben die Version „1.16.2 runtime installer“ heruntergeladen. Danach musste das gesamte Programm mit allen Features über die benutzerdefinierte Installation installiert werden. Wichtig ist hierbei, dass man sich den genauen Speicherort des Programms merkt, der Speicherort ist allerdings frei wählbar.
Danach haben wir eine Datei mit der Endung .bat erstellt. Auch hier ist der Speicherort frei wählbar, allerdings muss man diese Datei ausführen um den Livestream zu empfangen, weshalb sich der Desktop als Speicherort anbietet. In diese Datei muss nun der Code

.. code-block:: 

 @ECHO OFF
 cd gstreamer\1.0\x86_64\bin
 start gst-launch-1.0 –e –v udpsrc port=5000 ! application/x-rtp, payload=96 ! rtpjitterbuffer ! rtph264depay ! avdec_h264 ! fpsdisplaysink sync=false text-overlay=false
  
eingetragen warden. Bei Ausführung der Datei wird ein Fenster geöffnet, in dem der Livestream der Kamera angezeigt wird. 

ACHTUNG: sollte GStreamer auf einer anderen Festplatte als der Systemfestplatte installiert werden, muss der Code zu

.. code-block::
 
 @ECHO OFF
 A:						#wechselt zu der Festplatte auf der Gstreamer installiert ist
 cd gstreamer\1.0\x86_64\bin			#wechselt zum Installationsort von Gstreamer	
 
 start gst-launch-1.0 –e –v udpsrc port=5000 ! application/x-rtp, payload=96 ! rtpjitterbuffer ! rtph264depay ! avdec_h264 ! fpsdisplaysink sync=false text-overlay=false
  
geändert warden, ansonsten kann GStreamer nicht gestartet werden und der Stream empfangen werden.

Fernsteuerung der NIBOBee
-------------------------
Um die Belastbarkeit des Raspberry Pi Zero weiter zu testen haben wir uns entschieden, die NIBOBee per Bluetooth fernzusteuern. Dazu haben wir uns einen JOYCON von einer Nintendo Switch genommen, der über ein Bluetoothmodul verfügt. Als erstes muss der JOYCON mit dem schon voreingebauten Bluetoothmodul des Respberry PI Zero verbunden werden. Dazu muss das Programm Pulseaudio dem Raspberry Pi zur Verfügung stehen.

.. code-block:: python
 	
 sudo apt-get install pulseaudion 						#Installation von pulseaudio, auf Raspbian full schon vorhanden
 
 sudo apt-get install pulseaudio pulseaudio-module-bluetooth pavucontrol	#bluez-error fix:						 
 
 --Raspberry Pi neustarten--		
 
 sudo bluetoothctl								#greift auf das Bluetoothmodul des Raspberry Pi zu
 
 power on									#schaltet die Bluetoothfunktion ein
 
 scan on / scan off								#startet oder stoppt die Suche nach Geräten
 
 pair {Bluetoothadresse des zu verbindenden Geräts}				#mit gewünschtem Gerät paaren			 
 
 connect {Bluetoothadresse des zu verbindenden Geräts}				#mit gepaartem Gerät verbinden
 

Um zu überprüfen ob die Verbindung richtig funktioniert kann man diese Befehle benutzen:

.. code-block:: python
 
 ls/dev/input  		#Zeigt alle verfügbaren Eingabegeräte an
 
 cat /dev/input/event  	#als Event wird das Eingabegerät deiner Wahl angegeben (siehe ls/dev/input)
 

Wenn alles geklappt hat müsste nun bei der Betätigung des Eingabegerätes Reihen von erst einmal sinnlosen Zeichen erscheinen. Um daraus etwas Verwendbares zu machen mussten wir nach einem Programm zum Kovertieren suchen.
Bei unserer Suche sind wir dann auf die Pythonbibliothek `evdev <https://python-evdev.readthedocs.io/en/latest/usage.html>`_ gestoßen. Diese wandelt die Eingaben des JOYCON in Werte um, die wir dann an die NIBOBee übertragen konnten.

.. code-block:: python
 
 sudo apt-get install python-dev		#Installation von Python
 
 sudo apt-get install python-pip		#Installation von py pip
 
 sudo apt-get install python-idle		#Installation von Python ide
 
 sudo pip install evdev				#Installation der Bibliothek evdev
 
 sudo reboot
 

Um jetzt sinvolle Eingaben aus dem Gerät zu gewinnen, benötigen wir ein Pythoncode 

 
.. code-block:: python

 import evdev
 from evdev import categorize, ecodes

 name = evdev.InputDevice(evdev.list_devices()[0])      # Weist der Variablen "name", das Event "[0]" zu 
 print(name)                                            # Gibt "name" als ein Gerät aus

 for event in name.read_loop
     if event.type == ecodes.EV_KEY:
         print(event)                                	# Gibt bei Benutzung des Gerätes Informationen über Eingaben wieder, z.B gibt beim Drücken der "A"-Taste auf dem JOYCON eine Reihe von Informationen über die gedrückte Taste aus. 

Die Variable Null in den eckigen Klammern ist das Gerät, das wir verwenden möchten. Wir haben Zahlen von 0 an ausprobiert, bis wir unser Eingabegerät gefunden haben. (Meistens ist es 0).	
	 
In der Python shell wird nun der Name des Gerätes angezeigt.

Wenn jetzt eine Taste gedrückt wird, erscheint der Zustand 1 oder 0 und eine Nummer (Eventausgabe) die zu der Taste gehört. Diese Nummer können wir nun in Python weiterverwenden.

Als Nächstes müssen wir uns über die Pinbelegung vom ATMEGA 16 und Raspberry PI informieren, um HIGH- und LOW-Signale auszutauschen. Dafür wird die RaspberryPi-Erweiterungsplatine NICHT benötigt.
Aus dem `Schaltplan <http://download.nicai-systems.com/nibo/nibobee_berry_schematic_1_04.pdf>`_ lässt sich entnehmen, dass die Pins RXD und TXD des ATMEGA 16 nicht belegt sind und wir diese daher nutzen können.

.. image:: img/atmegapins.png

Die Nibobee haben wir wie folgt programmiert.

.. code-block:: C++

 #include <NIBObee.h>			// Benutze die Nibobee Bibliothek
 #include <avr/io.h>


 void setup() {
 DDRD &= ~(1 << PD0);			// setze PD0 als Eingang 
 DDRD &= ~(1 << PD1);			// setze PD1 als Eingang

 NIBObee.begin();
 }

 void loop() {
 if((PIND & (1 << PD0)))		// Wenn PD0 nicht 0 dann
 {	
 if((PIND & (1 << PD1)))		// Wenn PD1 nicht 0 dann
 {
 Engine.setPWM(500, 500);
 }
 }

 if((PIND & (1 << PD0)))		// Wenn PD0 nicht 0 dann
 {
 if(!(PIND & (1 << PD1)))		// Wenn PD1 nicht 1 dann
 {
 Engine.setPWM(500, -500);
 }
 }

 if(!(PIND & (1 << PD0)))		// Wenn PD0 nicht 1 dann
 {
 if((PIND & (1 << PD1)))		// Wenn PD1 nicht 0 dann
 {
 Engine.setPWM(-500, 500);
 }
 }

 if(!(PIND & (1 << PD0)))		// Wenn PD0 nicht 1 dann
 {
 if(!(PIND & (1 << PD1)))		// Wenn PD1 nicht 1 dann
 {	
 Engine.setPWM(0, 0);
 }
 }

 }
 
Wenn PD1 = 1 und PD0 = 1 fährt die Nibobee geradeaus. Wenn PD1 = 0 PD0 = 1 fährt sie nach links. Wenn PD0 = 1 und PD1 = 0 fährt sie nach Rechts. Wenn PD0 = 0 und PD1 = 0 hält sie an.

.. image:: img/rasppins.png

Mit dem Nibobee-Programm und den Eventzuständen im Hinterkopf, konnten wir nun ein Pythonscript zur Steuerung der Nibobee schreiben.
			
.. code-block:: python

 import evdev
 import time
 import RPi.GPIO as GPIO 
 from evdev import categorize, ecodes
 
 GPIO.setmode(GPIO.BCM)								#Raspberry Pi benutzt "Broadcom SOC channel" (siehe Pinlayout, grüne Rechtecke)
 
 GPIO.setup(22, GPIO.OUT, initial = GPIO.LOW)		#Setze GPIO 22 als Ausgang und auf LOW
 GPIO.setup(4,  GPIO.OUT, initial = GPIO.LOW)			
 
 name = evdev.InputDevice(evdev.list_devices()[0])	#weist der Variablen "name", das Event "[0]" zu 
 print(name)
 
 bx = 305											#weist den Variablen die Eventausgaben zu
 by = 307
 bb = 306
 ba = 304
 r  = 318
 
 for event in name.read_loop(): 					#Loop für dauerhafte Überprufung der Eventzustände
	 if event.type == ecodes.EV_KEY:
	 
		 if event.value == 1:						#wenn Eventzustand 1 (Gedrückt)
				
			 if event.code == r:					#wennenn Taste "r" gedrückt
				GPIO.output(4, GPIO.HIGH)			#setzt GPIO 4 HIGH
				GPIO.output(22, GPIO.HIGH)
				print("vorwärts")
				 
			 if event.code == ba:
				GPIO.output(4, GPIO.HIGH)
				GPIO.output(22, GPIO.LOW)
				print("links")
				 
			 if event.code == by:
				GPIO.output(4, GPIO.LOW)
				GPIO.output(22, GPIO.HIGH)
				print("rechts")
				 
		 elif event.value == 0:						# Wenn keine Tasten gedrückt, setze alle auf 0
			  GPIO.output(4, GPIO.LOW)					
			  GPIO.output(22, GPIO.LOW)				
			  print("stop")
			  
 GPIO.cleanup()
	
Und siehe da, es Funktiniert, die NIBOBee reagiert auf Tastendruck. Allerdings ist die Reichweite mit nur ungefähr 50cm sehr eingeschränkt. Daraus können wir schließen, dass die Rechweite des Bluetoothmodules zu gering ist für unser Vorhaben ist.

Da uns da nicht gereicht hat, mussten wir uns eine andere Lösung einfallen lassen. Also haben wir uns eine Bluetoothtastatur besorgt, die über einen eigenen Bluetoothadapter vefügt. Das Pythonscript für die umwandlung der Eingaben blieb fast unverändert. Nur die Eventzustände mussten mit bekannter Methodik neu erfasst werden.
Nun konnten wir die NIBOBee mit W,A,D steuern und hatten eine Reichweite von 10m, auch durch Wände hindurch.
	
.. code-block:: python

 import evdev
 import time
 import RPi.GPIO as GPIO 
 from evdev import categorize, ecodes
 
 GPIO.setmode(GPIO.BCM)				  					
 
 GPIO.setup(22, GPIO.OUT, initial = GPIO.LOW)
 GPIO.setup(4,  GPIO.OUT, initial = GPIO.LOW)
 
 name = evdev.InputDevice(evdev.list_devices()[0])
 print(name)
 
 w = 17
 a = 30
 s = 31
 d = 32
 
 for event in name.read_loop():
	 if event.type == ecodes.EV_KEY:
	 
		 if event.value == 1:
		 
			 if event.code == w:
				GPIO.output(4, GPIO.HIGH)
				GPIO.output(22, GPIO.HIGH)
				print("vorwärts")
				 
			 if event.code == a:
				GPIO.output(4, GPIO.HIGH)
				GPIO.output(22, GPIO.LOW)
				print("links")
				 
			 if event.code == d:
				GPIO.output(4, GPIO.LOW)
				GPIO.output(22, GPIO.HIGH)
				print("rechts")
				 
		 elif event.value == 0:
			  GPIO.output(4, GPIO.LOW)
			  GPIO.output(22, GPIO.LOW)
			  print("stop")
			  
 GPIO.cleanup()
  
 
Um das Pythonskript bei Start des Raspberry PI auszuführen mussten wir das Programm, das für den Autostart zuständig ist, um unser Programm erweitern. Diese Datei wird mit 

.. code-block:: python
 
 sudo nano /etc/profile
 
aufgerufen. Unter die letzte Zeile (fi) mussten wir nun 

.. code-block:: python
 
 sudo python {Pfad des Pythonprogramms} &
 
einfügen. DAS "&" IST HIER SEHR WICHTIG. Es verhindert, dass das Pythonscript beim starten mit anderen Programmen interferiert.

z.B. 

.. code-block:: python

 unset id
 fi
 sudo python /home/pi/{Name des Pythonprogramms.py &
 

Die Scripte
-----------

Da sich die Art und Weise, in der der Temperatur- und Feuchtigkeitssensor an den Raspberry Pi Zero W angeschlossen wird,
nicht von der des Raspberry Pi 3B+ unterscheidet konnten wir diese `Scripte für die Messungen <https://nibobee.readthedocs.io/de/latest/Kapitel_05_RaspberryPi3.html#die-fertigen-pythonscripte>`_ einfach übernehmen.
Für die automatische Installation von GStreamer mussten wir uns allerdings etwas anderes einfallen lassen. Letztendlich sind auf folgende Lösung gekommen.

.. code-block:: python
 
 #!/usr/bin/python
 # -*- coding: utf-8 -*-
 
 import os
 
 #sucht nach neuen Updates für den Piund diese
 print os.system('sudo apt-get --assume-yes update');			
 print os.system('sudo apt-get --assume-yes upgrade');			
 print os.system('sudo apt-get --assume-yes autoremove');		
 
 #erstellt ein neues Verzeichnis und wechselt in dieses
 print os.system('mkdir /home/pi/Livestream');
 print os.chdir('/home/pi/Livestream');				
 
 #installiert die nötige Version von GStreamer
 print os.system('sudo apt-get install gstreamer1.0-tools');
 
 #erstellt eine neue Shell-Datei in dem eben erstellten Verzeichnis 
 print os.system('sudo nano livestream.sh');
 
 #fragt nacht der IP Adresse des Empfängers (wird leider bei jedem Start des Programms gemacht, was einen Bildschirm und eine Tastatur voraussetzt
 ip_address = raw_input("IP Adresse des Empfängers eingeben: "); 
 
 #öffnet die eben erstellte Shelldatei, schreibt die nötigen Parameter hinein und schließt die Datei wieder
 f= open("livestream.sh","w+");
 f.write("#!/bin/bash \n")
 f.write("Raspivid –n –t 0 –w 640 –h 480 –fps 30 –b 500000 –o  -rot 0 - |gst-launch-1.0 –e –vvv fdsrc ! h264parse ! rtph264pay pt=96 config-interval=5 ! udpsink host=ip_address port=5000");
 f.close();
 
 #startet das Programm für den Livestream
 print os.system('sh livestream.sh');
 
Da nun aber nicht bei jedem Livestream auch die Updates mit installiert werden müssen, haben wir ein seperates Programm erstellt, das nur den Stream startet.

.. code-block:: python

 #!/usr/bin/python
 # -*- coding: utf-8 -*-
 
 import os
 	
 #wechselt in das Verzeichnis der Shell-Datei
 print os.chdir('/home/pi/Livestream');				
 
 f= open("livestream.sh","w+");
 ip_address = raw_input("IP Adresse des Empfängers eingeben: ");
 f.write("#!/bin/bash \n") 
 f.write("Raspivid –n –t 0 –w 640 –h 480 –fps 30 –b 500000 –o  -rot 0 - |gst-launch-1.0 –e –vvv fdsrc ! h264parse ! rtph264pay pt=96 config-interval=5 ! udpsink host=ip_address port=5000");
 f.close();
 
 print os.system('sh livestream.sh');
 
Wie schon erwähnt, gibt es bei diesen Programmen kleine Fehler, die wie nicht behoben konnten. Im Endeffekt brauchten wir immer manuelle Eingaben, um den Stream zu starten.