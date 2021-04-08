# RFID-Kit
Anleitung zum RFID Kit mit Arduino

Der RFID („radio-frequency identification“) Reader wird verwendet, um von RFID Sendern (auch „RFID Tags“ genannt) per Funk einen bestimmten Code auszulesen. Jeder Sender hat dabei nur einen einmaligen ganz individuellen Code. Damit lassen sich mit dem Arduino Schließanlagen oder ähnliche Projekte verwirklichen, bei denen sich eine Person mit einem Sender identifizieren soll.

RFID-TAGs können verschiedene Formen haben, wie z.B. Schlüsselanhänger oder Karten im Kreditkartenformat.

Auf dem folgenden Bild sieht man Links oben und unten rechts zwei RFID-TAGs und in der Mitte den RFID-Empfänger RFID-RC522, mit bereits angelöteter 90° Stiftleiste. Es gibt auch Versionen bei denen die Stiftleiste selbst an den RFID-Empfänger gelötet werden muss.

Wie funktioniert das ganze? Ein RFID-Empfänger enthält eine kleine Kupferspule, die ein magnetisches Feld erzeugt. Ein RFID-Sender enthält ebenfalls eine Kupferspule, die das magnetische Feld aufgreift und in dem Sender eine elektrische Spannung erzeugt. Diese Spannung wird dann verwendet um einen kleinen elektronischen Chip dazu zu bringen, per Funk einen elektrischen Code auszusenden. Dieser Code wird dann direkt vom Sender empfangen und so verarbeitet, dass der Arduino-Mikrocontroller den empfangenen Code weiterverarbeiten kann.

Es ist auch möglich, RFID-TAGs zu mit einem Code zu beschreiben. Dies wird aufgrund der Komplexität in dieser Anleitung nicht behandelt. Weiterführende Tutorials finden sich genügend im Netz.




Einen RFID-TAG mit Arduino auslesen und die Daten verarbeiten
Material: Arduino UNO oder MEGA, RFID-READER, mindestens einen RFID-TAG, Breadboard, einige Breadboardkabel, eine LED, ein 200 Ohm Widerstand

Aufgabe: Mit Hilfe eines Arduino-Mikrocontrollers soll ein RFID-TAG ausgelesen werden. Sofern es sich um den richtigen TAG handelt, soll eine Leuchtdiode für 5 Sekunden leuchten.

Verkabelung des Arduino-Boards mit dem RFD-Empfänger:

Board:  Arduino Uno   Arduino Mega    MFRC522-READER

Pin:      10              53            SDA

Pin:      13              52            SCK

Pin:      11              51            MOSI

Pin:      12              50            MISO

Pin:    unbelegt        unbelegt        IRQ

Pin:      GND           GND             GND

Pin:       9             5              RST

Pin:     3,3V           3,3V            3,3V


Foto vom Aufbau. In diesem Beispiel wurden an den RFID-Empfänger die um 90° gebogenen Kontaktstifte angelötet, damit der Empfänger senkrecht in ein Breadboard gesteckt werden kann: (https://i1.wp.com/funduino.de/wp-content/uploads/2016/01/Fb7.png?resize=480%2C625&ssl=1)(https://i0.wp.com/funduino.de/wp-content/uploads/2016/01/Fb8.png?resize=480%2C549&ssl=1)




Progammierung
Beim Auslesen und verarbeiten der Daten eines RFID-Empfängers wären wie auch bei anderen komplexen Aufgaben sehr viele Zeilen Quellcode erforderlich. Daher bedienen wir uns einer vorgefertigten Library. Die „ MFRC522“ Library von GithubCommunity kann über den Bibliothekenverwalter mit dem Suchbegriff „RFID“ gefunden und installiert werden. Eine Anleitung zur Installation einer Bibliothek über den Bibliotheksverwalter findet sich unter 2.2.2 Bibliotheken zur Arduino Software hinzufügen.

Vorbereitung – der erste Sketch mit dem RFID-READER

Zunächst werden wir die UID („Unique Identification Number“), also die individuelle Bezeichnung eines RFID-TAGs auslesen. Dazu verwenden wir das folgende Programm (Achtung, das Programm funktioniert nur, wenn die library wie oben beschrieben zur Arduino-Software hinzugefügt wurde). Das Programm ist für den UNO R3 Mikrocontroller vorgesehen. Bei MEGA2560 und anderen Controllern müssen die Pins entsprechend angepasst werden.

Wenn alles funktioniert hat, sieht das Ergebnis am Serial-Monitor (Abgesehen von der eigenen UID) so aus: (https://i2.wp.com/funduino.de/wp-content/uploads/2016/01/FB9.png?w=493&ssl=1)

Mit dieser hintereinander geschrieben HEX-Zahl lässt sich nicht gut arbeiten. Also ändern wir die Zeile „Serial.print(mfrc522.uid.uidByte[i], HEX);“ um in „Serial.print(mfrc522.uid.uidByte[i], DEC);“. Dann bekommt man als Ergebnis die einzelnen Blöcke des UID-Codes als Dezimalzahl ausgegeben:




RFID Sketch 2
Jetzt wird der UID-Code zwar als Dezimalzahl ausgegeben, aber er ist immernoch in vier Blöcke aufgeteilt. Wir verändern den Code jetzt mit ein wenig Mathematik dahingehend, dass wir für die UID eine einzige zusammenhängende normale Zahl erhalten (Dezimalzahl).

Warum machen wir das? Wenn wir später den Sketch verwenden wollen um etwas in Abhängigkeit eines richtig ausgelesenen RFID-TAGs auszulösen (Z.B. eine LED soll leuchten oder eine Servomotor soll sich um 90 Grad drehen…) können wir mit einer zusammenhängenden Zahl besser einen IF-Befehl verwenden. Zum beispiel:

„Wenn der RFID-Code=1031720 ist, dann soll eine LED für 5 Sekunden leuchten“.

Schwerer wäre es dagegen mit dem Befehl „Wenn der erste Block 195 lautet und der zweite Block 765 lautet und der dritte Block 770 lautet und der vierte Block 233 lautet … Dann schalte eine LED für 5 Sekunden an.

Nachteil ist jedoch, dass der Sketch dadurch etwas unsicherer wird, weil nicht alle Zahlen der vier Blöcke (Max. 12 Ziffern) in einer Zusammenhängenden Zahl dargestellt werden können. Wenn es sicherer sein soll, müsste man jeden einzelnen Block als solchen abfragen.

Prima, jetzt können wir von einem RFID-TAG eine eindeutige Identifikationsnummer auslesen (Die Nummer wird am Serial Monitor angezeigt). In diesem Fall lautet die Identifikationsnummer dieses individuellen RFID-TAGs 1031720.

Und wie geht es weiter? Jetzt wollen wir eine LED für 5 Sekunden einschalten, falls der gewünschte RFID-TAG vor den RFID-READER gehalten wird.




RFID Sketch 3






Weitere Infos: https://funduino.de/nr-18-rfid-kit
