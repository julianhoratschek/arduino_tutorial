# Arduino-Tutorial V0.1

### Inhalt

<!-- TOC -->
* [Arduino-Tutorial](#arduino-tutorial)
* [Inhalt](#inhalt)
* [Referenzen](#referenzen)
* [Anfang](#anfang)
    * [Was ist Setup? Und Loop?](#was-ist-setup-und-loop)
* [Kommunikation](#kommunikation)
    * [Ein nettes Gespräch](#ein-nettes-gespräch)
* [Ratespiel](#ratespiel)
    * [One Track Mind](#one-track-mind)
    * [Zufall](#zufall)
    * [Pressure](#pressure)
    * [State-machine](#state-machine)
* [Eine Außenwelt](#eine-außenwelt)
    * [Wiggle Wiggle](#wiggle-wiggle)
    * [Ein Licht am Ende des Tunnels](#ein-licht-am-ende-des-tunnels)
    * [Farbenspiel](#farbenspiel)
    * [Extrakapitel: Zeigermagie](#extrakapitel-zeigermagie)
<!-- TOC -->

### Referenzen

Ein paar nützliche grundlegende Referenzen:

- Empfohlene IDE zur Bearbeitung von Quellcode: [IDE](https://www.arduino.cc/en/software)
  - Pro:
    - Installiert automatisch die benötigten Dependencies von avr-gcc
    - Internes Bibliotheksmanagement: Hinzufügen externen Codes wird somit sehr einfach
    - Direktes Auslesen von Serial-Monitoren
    - Unkompliziertes Laden der Binärdateien auf den Arduino
  - Contra:
    - Geringe Einstellungsmöglichkeiten des Code-Editors
    - C++ Syntaxhilfen sind unterirdisch, Du schreibst quasi in einem Txt-Editor
- Übersicht über die nutzbaren Funktionen im Arduino: [Funktionsreferenz](https://www.arduino.cc/reference/en/)
- Pinout-Blatt und Datasheet des Arduino Nano Every: [Arduino Nano Every](https://docs.arduino.cc/hardware/nano-every)

# Anfang

Dies ist kein C++ Tutorial. Es wird davon ausgegangen, dass grundlegende Strukturen der Sprache bereits bekannt sind.

Lade Dir die Arduino-IDE herunter und schau Dich ein bisschen im Programm um. Du kannst den Arduino mit deinem PC-USB-Port verbinden
und unter "Tools &rarr; Board Manager" den Arduino Nano Every auswählen. Die IDE sollte den Großteil der notwendigen Installationen selbst vornehmen.

Auch unter Tools solltest Du den Serial Monitor finden. Öffne diesen und schau rechts oben im Fenster nach der Datenübertragungsrate (nur, wenn
ein Microcontroller angeschlossen ist). Diese wird nachher wichtig. Normalerweise sollte sie bei 9600 liegen, wenn ein anderer Wert dort steht, kannst Du
diesen einfach stehen lassen.

Links befindet sich die Menüleiste, hier kannst Du unter "Bibliotheken" nach externen Programmbibliotheken suchen, z.B. Code zum Ansteuern von
externen Komponenten.

Links oben sind ein paar grüne Knöpfe. Von links nach rechts: Der Haken kompiliert Deinen Code lokal und überprüft ihn auf Fehler. Der Pfeil kompiliert den Code und lädt ihn
auf ein angeschlossenes Arduino hoch. Manchmal tritt hierbei eine Bootloader-Warnung auf (in Rot, sieht gefährlich aus), diese kann jedoch ohne
Probleme ignoriert werden. Sobald der kompilierte Code hochgeladen ist, wird der Microcontroller sofort beginnen, diesen auszuführen.
Zuletzt ist noch ein "Debugging"-Symbol dort zu finden, dies ist jedoch nur auf einigen Arduino-Plattformen verfügbar.

Solltest Du einmal bereits hochgeladenen Code nochmal ganz von vorne ausführen wollen, ohne ihn erneut auf das Gerät zu laden, so kannst Du auf den
kleinen Knopf etwa in der Mitte des Arduino drücken, dieses setzt alles zurück und beginnt wieder mit der Ausführung bei ```setup()```.

### Was ist Setup? Und Loop?

Mit der Funktion setup() sind wir schon mitten im Coding. Erstelle eine neue Codedatei in der Arduino-IDE und schau sie Dir an. Eine leere Arduino-Codedatei
(auch "Sketch" genannt, mit der Dateiendung *.ino) hat folgenden Aufbau:

```C
void setup() {
    
}

void loop() {
    
}
```

Dies sind 2 C++ Funktionen ohne Parameter und ohne Rückgabewert. Die Funktion ```setup()``` wird nur einmalig aufgerufen, 
wenn der Code ganz neu auf das Gerät geladen wird oder wenn der Reset-Knopf am Gerät ausgelöst wird (oder Spannung am 
Reset-Pin anliegt oder das Gerät neu eingeschaltet wird). Die ```loop()```-Funktion wird anschließend dauerhaft wiederholt ausgeführt.

In der Datei kannst Du in den Funktionen Code schreiben, Variablen lokal in den Funktionen oder global außerhalb der Funktionen definieren, oder auch eigene
Funktionen schreiben:

```C
int     counter = 0;

void add_2_when_divisible_by_5() {
    if(counter % 5 == 0)
        counter += 2;
}

void setup() {
    
}

void loop() {
    counter++;
    add_2_when_divisible_by_5();
}
```

Das Beispielprogramm definiert eine Globale ```counter``` und setzt sie bei Start des Programmes auf 0. Anschließend wird 
die Variable in jedem ```loop()``` um 1 herauf gezählt. Wenn sie sodann durch 5 teilbar sein sollte, wird zudem 2 addiert.

# Kommunikation

Damit wir auch mitbekommen können, was eigentlich in dem Controller passiert, können wir den Serial-Monitor der IDE mit 
dem Arduino verbinden und darüber Daten senden und empfangen. Arduino definiert hierfür eine globale Klasseninstanz 
```Serial```, welche den Software-Hintergrund der Kommunikation für uns übernimmt:

```C
void setup() {
    Serial.begin(9600);
    while(!Serial);
}

void loop() {
    Serial.println("Hello World");
    delay(500);
}
```

Wir müssen zunächst die Kommunikation mit ```Serial.begin()``` öffnen, als Parameter gibst Du die Datenübertragungsrate an, 
die Du an dem Serial-Monitor abgelesen hast. Die Zeile ```while(!Serial);``` wartet ab, bis der Monitor erfolgreich verbunden 
wurde. Meistens ist dies nicht notwendig, geht allerdings sicher, dass wir den Monitor zur Verfügung haben, wenn wir mit 
```loop()``` starten. Anschließend schreiben wir in jedem Loop "Hello World" auf den Monitor und warten mit ```delay()``` 
500ms, damit wir auch Zeit haben, die Zeilen zu lesen.

### Ein nettes Gespräch

Als Anfang bietet sich ein Programm an, das eine kleine Kommunikation mit Arduino ermöglicht. Da wir nicht nur lesen wollen, 
was Arduino uns sagt, sondern auch antworten wollen, brauchen wir aber weitere Funktionen.

Wenn wir etwas an den Arduino schicken wollen, können wir entweder mit ```Serial.read()``` ein Byte oder mit ```Serial.readString()``` 
einen String einlesen (es gibt auch noch weitere Lesefunktionen für Serial ports, diese können 
[hier](https://www.arduino.cc/reference/en/language/functions/communication/serial/) eingesehen werden). Daten lassen
sich einfach aus der Arduino IDE senden, indem etwas in die oberste Zeile des Serial-Monitor getippt und anschließend __Enter__ gedrückt wird.

Mit diesem Vorwissen können wir ein Beispielprogramm konzipieren:

```C
String get_input() {                                                        // Benutzerdefinierte Funktion
    while(!Serial.available());                                             // Blockiert das Gerät, bis ein Input im Serial-Port zu finden ist.
    
    String  str = Serial.readString();                                      // Lese einen String ein          
    str.trim();                                                             // Entferne ggf. bestehende Absätze
    
    return str;
}

void setup() {
    Serial.begin(9600);
    while(!Serial);
}

void loop() {
    String      answer;
    int         age;

    Serial.println("Hallo, wie heißt Du?");
    answer = get_input();
    Serial.println(String("Hallo ") + answer + ", wie alt bist Du?");
    answer = get_input();
    age = answer.toInt();

    Serial.print(String("Also bist Du ") + age + " Jahre alt. Das ist in meiner Sprache ");
    Serial.println(age, BIN);

    Serial.println("Was ist Deine Lieblingsfarbe?");
    answer = get_input();
    Serial.println(answer + ", huh? Meine ist Navy:)");
    Serial.println("Aus Datenschutzrechtlichen Gründen muss ich unsere Konversation leider löschen :( Caio.\r\n\r\n");
}
```

Gehen wir das Programm nacheinander durch, beginnend mit der ```setup()``` Funktion: Hier sollte alles bekannt sein. 
Wir verbinden uns mit dem Serial-Port und warten, bis die Verbindung hergestellt ist. In ```loop()``` wird es interessanter: 
Hier wird ```Serial.print()``` und ```Serial.println()``` genutzt, um Text auf dem Monitor zu generieren. Manchmal wird 
der Text in doppelten Anführungszeichen geschrieben, manchmal aber auch zusätzlich mit ```String()``` umschlossen. Warum?

C++ unterscheidet zwischen verschiedenen Arten von Strings: Die in den doppelten Anführungszeichen sind C-Strings, im Grunde 
genommen nur Arrays von bytes im Speicher. Auf diese sind nur schwierig Operatoren anwendbar, sie sind nicht besonders flexibel. 
Allerdings gibt es auch die Klasse ```String```, welche Arduino unabhängig von der C++ stdlib implementiert: Die Klasse bietet 
die Möglichkeit, mit Strings zu interagieren, sie zu verändern oder auch zu konvertieren. Damit ist die Klasse ```String```
in etwa das, was in vielen Skriptsprachen (Python, Ruby, Javascript) generell als String gehandelt wird. Dennoch benötigt 
die Klasse als Grundlage immer noch einen klassischen C-String in doppelten Anführungszeichen, um zu wissen, womit sie arbeiten soll.

Zu bedenken ist, dass alle C-Strings, die ein Programmierer in den Quellcode schreibt, genau so in die kompilierte Binärdatei 
übernommen werden. Wenn sich irgendwo Text sparen lässt, so ist das also eine gute Idee. Hierfür gibt es einzelne Ausnahmen, 
die aber aktuell noch nicht relevant sind.

In ```loop()``` werden 2 Funktionen von ```String``` benutzt: einmal ```toInt()```, welches den Inhalt des Strings so gut 
wie möglich in eine Ganzzahl umwandelt und zurückgibt. Die Funktion bricht die Konvertierung in eine Zahl ab und gibt das
bisherige Ergebnis zurück, sobald ein zeichen gefunden wird, das keine Ziffer ist. Die zweite Funktion ist ```+```, welches 
einen String mit einem anderen Datentyp verbinden kann. Dabei ist wichtig, dass ganz links ein String steht (kein C-String), rechts
können fast beliebige Grundtypen stehen, diese werden automatisch in einen String verwandelt (z.B. C-Strings, Ints, Floats).
So können auch mehrere ```+```-Anweisungen aneinandergereiht werden.

```Serial.print()``` wird hier genutzt, um eine Zahl auszugeben und diese gleichzeitig in eine Binärdarstellung zu übersetzen.
Dies ist eine Convenience-Funktion: Mit einem optionalen 2. Parameter (BIN, OCT, DEC, HEX) kann ```Serial.print()``` angewiesen 
werden, eine Zahl, die als erster Parameter übergeben wurde, in das entsprechende System zu konvertieren, bevor sie ausgegeben 
wird. Wird der 2. Parameter weggelassen ist der Default DEC.

Da ```Serial.readString()``` die Ausführung des Programmes nicht unterstützt, wir aber auf eine Antwort des Serial-Port warten
müssen, wird in dem Beispiel die Funktion ```get_input()``` geschrieben:

Wird diese aufgerufen, so blockiert sie zunächst das gesamte Gerät, bis im Serial-Port Daten stehen, die durch Arduino ausgelesen 
werden können: ```while(!Serial.available());```. Dies ist ähnlich zu der bekannten Zeile ```while(!Serial);```, wo wir ebenfalls 
auf ein Gerät gewartet haben. ```Serial.available()``` gibt eigentlich keinen boolean zurück, sondern die Anzahl der bereitgestellten 
Byte, da C++ allerdings __0__ als __false__ wertet, kann der Rückgabewert als boolean genutzt werden. Sobald Daten zur Verfügung 
stehen, liest ```Serial.readString()``` eine komplette Zeile als String-Objekt ein. Wir speichern dieses Objekt und nutzen seine Methode
```trim()```, um den mitgelesenen Absatz ("\n") zu entfernen. Anschließend geben wir den String zur weiteren Verarbeitung an unsere 
aufrufende Funktion zurück.

Weitere Funktionen der Klasse String finden sich [hier](https://www.arduino.cc/reference/en/language/variables/data-types/stringobject/)

> Aufgabe: Schreibe ein Programm, das eingegebene Dezimalzahlen als binär-, octal- und hexadezimalzahl ausgibt.
> Dafür kann die Funktion get_input() genutzt werden, indem Du sie in Deinen Quellcode kopierst.

> Extrauafgabe: Schreibe das Programm so, dass der Benutzer wählen kann, ob die eingegebene Zahl als binär, octal- oder hexadezimalzahl ausgegeben wird.

<details>
<summary>Lösung</summary>

```C
String get_input() {
    while(!Serial.available());
    
    return Serial.readString().trim();
}

void setup() {
    Serial.begin(9600);
    while(!Serial);
}

void loop() {
    String      input;
    int         nr;
    
    Serial.println("Welche Nummer soll konvertiert werden?");
    nr = get_input().toInt();
    
    Serial.println("In welches System soll die Nummer konvertiert werden? (bin, oct, hex)");
    input = get_input();
    
    Serial.print("Das Ergebnis ist: ");
    if(input == "bin")
        Serial.println(nr, BIN);
    else if(input == "oct")
        Serial.println(nr, OCT);
    else if(input == "hex")
        Serial.println(nr, HEX);
    else
        Serial.println("Abbruch, da keine gültige Konvertierung angegeben wurde.");
}
```
</details>

# Ratespiel

Jetzt können wir ein erstes Programm schreiben, das mittels des Serial-Port zwischen zwei Computern kommuniziert. Hierfür 
lassen wir den Arduino eine zufällige Zahl zwischen 0 und 100 wählen und müssen diese innerhalb von 5 Versuchen erraten. 
Nach Erraten oder nach Ablauf der 5 Versuche soll das Spiel von neuem beginnen.

> Ab hier kannst Du versuchen, die Programme selbst zu schreiben, bevor Du in die
> Beispiellösungen schaust.

### One Track Mind

> Schreibe ein Programm, das eine festgelegte Zahl "im Kopf" hat und auf eine Benutzereingabe wartet. 

Definiere hierfür eine Variable von einem Ganzzahltypen und weise ihr einen Zahlenwert zu. Nutze ```Serial.readString()```,
um in jedem Loop zu versuchen, einen String einzulesen und in einer Variablen zu speichern. Nutze __nicht__ die ```get_input()``` 
Funktion und blockiere das Gerät __nicht__ mit einer ```while()``` Schleife, bis ein Input erfolgt ist! Wenn die Funktion 
keine Daten zum Lesen gefunden hat, wird sie ```""``` zurückgeben. Du kannst dies mittels einer ``if``-Abfrage überprüfen. 
Sollte etwas eingelesen worden sein, konvertiere es mittels ```.toInt()``` (siehe Beispiel im Vorkapitel) zu einer Zahl und 
vergleiche sie mit der "ausgedachten" Zahl des Computers. Schreibe mittels ```Serial.println()```, ob die Zahl richtig 
erraten worden ist oder nicht.

> Als Nächstes soll das Programm mitteilen, ob die Zahl größer, kleiner oder genau die erwartete ist. Gib außerdem aus, was
> für eine Nummer der Benutzer eingegeben hat.

Erweitere Dein Programm mittels weiterer ```if```-Abfragen um diese Funktionalität. Kannst Du die Nutzung von C-Strings 
geschickt minimieren und trotzdem vollständige Sätze schreiben?

<details>
<summary>Lösung</summary>

```C
byte        my_number = 69;                     // Initialisierung

void setup() {
    Serial.begin(9600);                         // Verbinden mit Serial-Port
    while(!Serial);
}

void loop() {
    String      answer = Serial.readString();   // Lese einen String ein, wenn Daten vorliegen, sonst wird nach 1 Sekunde "" eingelesen
    
    if(answer != "") {                          // Falls etwas eingelesen wurde
        int     user_number = answer.toInt();   // Konvertiere zu Int
        
        Serial.print("Meine Nummer ist ");
        
        if(user_number > my_number)             // Branching
            Serial.print("kleiner als ");
        else if(user_number < my_number)
            Serial.print("größer als ");
        else
            Serial.print("genau ");
        
        Serial.println(user_number);
    }
}
```
</details>

### Zufall

Arduino verfügt über einen eingebauten Pseudo-Random-Number-Generator. Mittels ```random(max)``` kann ein zufälliger Wert 
zwischen 0 und max-1 generiert werden. Die Funktion kann auch mit einer unteren Schranke als ```random(min, max)``` aufgerufen werden.

Da ```random()``` seedabhängig ist, würden wir jedoch bei jedem neuen Durchlauf die gleiche "zufällige" Reihenfolge erhalten. 
Um dies zu verhindern, kann die Funktion ```randomSeed(n)``` aufgerufen werden, wobei n selbst ein möglichst zufälliger Wert 
sein sollte. Oftmals wird hierfür z.B. die aktuelle Zeit oder ein Wert einer Hardwarekomponente genommen. Wir können hier 
die zufällige Spannung an einem unangeschlossenen analogen Pin nehmen. Dies kann mittels ```analogRead(A0)``` erfolgen, 
um die Spannung an dem A0 Pin abzulesen. Die Funktion ```randomSeed()``` wird am besten in ```setup()``` aufgerufen.

> Lass den Arduino in der ```loop()```-Funktion eine zufällige Zahl zwischen 0 und 100 ausdenken, sobald die alte Zahl erraten wurde.
> Hierfür benötigst Du entweder eine globale Kontrollvariable (z.B. ein Boolean), die beinhaltet, ob eine neue Zahl generiert werden soll,
> oder Du denkst Dir einen geschickten Weg aus, dies mit der bereits definierten "Zufallsvariable" zu erreichen.
> Denke daran, den Arduino mit dem Serial-Port "sprechen" zu lassen, damit wir wissen, was grade passiert.

<details>
<summary>Lösung</summary>

```C
int my_number = -1;                                         // Nummer mit -1 initialisiert, dies wird das Zeichen, dass eine neue gewählt werden muss

void setup() {
    Serial.begin(9600);
    while(!Serial);
    
    randomSeed(analogRead(A0));                             // Setzt den Random-Seed auf einen zufällig von A0 abgelesenen Wert
}

void loop() {    
    if(my_number == -1) {                                   // Generiere eine zufällige Zahl, wenn my_number -1 ist
        Serial.println("Ich überlege mit eine neue Nummer zwischen 0 und 100");
        my_number = random(100);
        Serial.println("Los geht's mit dem Raten :)");
    }
    
    String      answer = Serial.readString();
    
    if(answer != "") {
        int     user_number = answer.toInt();
        
        Serial.print("Meine Nummer ist ");
        
        if(user_number > my_number)
            Serial.print("kleiner als ");
        else if(user_number < my_number)
            Serial.print("größer als ");
        else {
            Serial.print("genau ");
            my_number = -1;                                 // Setze my_number auf -1, um eine neue zufällige Zahl zu generieren
        }
        
        Serial.println(user_number);
    }
}
```
</details>

### Pressure

> Zusätzlich soll der Benutzer nicht unendlich viele Versuche haben. Führe die Variable ```tries``` ein, welche pro Versuch 
> herabgezählt wird. Wenn sie den Wert 0 erreicht, soll ausgegeben werden, dass der Benutzer verloren hat und eine neue zufällige 
> Zahl generiert werden, ```tries``` soll dann wieder auf die maximale Versuchszahl gesetzt werden. Ein Fallstrick ist, dass 
> je nach Implementierung passieren kann, dass eine "Du hast verloren"-Nachricht ausgegeben wird, obwohl mit dem letzten Versuch die
> korrekte Zahl erraten worden ist. Bedenke diesen Sonderfall.

<details>
<summary>Lösung</summary>

```C
int my_number = -1, tries = 5;                                                      // tries wird initialisiert auf 5

void setup() {
    Serial.begin(9600);
    while(!Serial);
    
    randomSeed(analogRead(A0));
}

void loop() {
    if(my_number != -1 && tries == 0) {                                             // Nur, wenn keine neue Zahl generiert werden soll, ist die Anzahl der Versuche relevant
        Serial.println("Du hast keine Versuche mehr übrig und verloren :(");
        Serial.println(String("Meine Nummer war ") + my_number);
        
        my_number = -1;                                                             // Eine neue zufällige Nummer soll generiert werden
    }
    
    if(my_number == -1) {
        Serial.println("Ich überlege mit eine neue Nummer zwischen 0 und 100");
        my_number = random(100);
        Serial.println("Los geht's mit dem Raten :)");
        
        tries = 5;                                                                  // Beim Neustart sollen wieder alle Versuche zur Verfügung stehen
    }
    
    String      answer = Serial.readString();
    
    if(answer != "") {
        int     user_number = answer.toInt();
        
        Serial.print("Meine Nummer ist ");
        
        if(user_number > my_number)
            Serial.print("kleiner als ");
        else if(user_number < my_number)
            Serial.print("größer als ");
        else {
            Serial.print("genau ");
            my_number = -1;
        }
        
        Serial.println(user_number);
        
        if(--tries > 0) {                                                           // Zähle tries herab und überprüfe, ob noch welche übrig sind
          Serial.print(String("Du hast noch ") + tries);
          Serial.println(" Versuche übrig.");
        }
    }
}
```
</details>

### State-machine

> Dies ist ein Extraabschnitt, der auf Programmiertechnik eingeht

Die aktuelle Implementierung funktioniert und macht, was sie soll. Wir haben aktuell jedoch die gesamte Programmlogik in der 
```loop()``` Funktion und durch if-Abfragen gestaffelt. Das funktioniert, wird bei komplexeren Projekten schnell unübersichtlich.

Zwei Programmiertechniken können dabei helfen, das Programm in übersichtliche, "verdauliche" Abschnitte zu gliedern: Zunächst 
können wir die Sinnabschnitte - Generieren einer zufälligen Zahl, Überprüfen der Versuche und Überprüfen der eingegebenen Nummer - in 
Funktionen verpacken, um die Lesbarkeit zu verbessern.

> Schreibe die Funktionen ```get_random_number()```, ```check_tries()``` und ```check_number()``` und rufe sie an den entsprechenden Stellen auf.

Eine weitere sinnvolle Möglichkeit, das Programm zu gliedern stellt die sog. State-machine dar: Immer, wenn ein Programm zu 
bestimmten Zeitpunkten distinkt unterschiedliches Verhalten hat, kann dies als "State" bezeichnet werden. Der State, in dem
das Programm sich befinden soll, kann in einer Variablen gespeichert werden. Jeder State kann angeben, welchen State das Programm 
als Nächstes annehmen soll. Bei uns wäre dies der ```GET_NUMBER_STATE```, der ```CHECK_TRIES_STATE``` und der ```CHECK_NUMBER_STATE```. 
Der Progress wäre:

- GET_NUMBER_STATE
  - &rarr; CHECK_TRIES_STATE (immer als nächster Schritt)
- CHECK_TRIES_STATE
  - &rarr; CHECK_NUMBER_STATE (wenn tries > 0)
  - &rarr; GET_NUMBER_STATE (wenn tries == 0)
- CHECK_NUMBER_STATE
  - &rarr; CHECK_TRIES_STATE (wenn user_number != my_number)
  - &rarr; GET_NUMBER_STATE (wenn user_number == my_number)
  - &rarr; CHECK_NUMBER_STATE (so lange keine Eingabe durch den Benutzer erfolgt ist)

Um den States sprechende Namen zu geben, kann die Präprozessor-Anweisung ```#define``` genutzt werden: Diese ersetzt ihren
ersten Parameter stumpf durch den zweiten: So kann z.B. ```#define RANDOM_NUMBER_STATE 0``` genutzt werden, um jedes
Vorkommen von RANDOM_NUMBER_STATE im Code durch den Zahlenwert "0" zu ersetzen. Defines sind nützlich, um den Code lesbarer 
und auch leichter bearbeitbar zu gestalten. Der erste Parameter von ```#define``` folgt den Konventionen von Variablenbenennung, 
es hat sich eingebürgert, diese immer in Caps zu schreiben. Eine Besonderheit von ```#define``` ist, dass es auch Parameter übernimmt:

```C
#define SWAP_INT(x, y)        {int tmp_def_var = x; x = y; y = tmp_def_var;}
```

Defines können als Makros gedacht werden, die das Vorkommen des linken Texts durch den rechten Text ersetzen.

> Versuche, die State-machine zu implementieren. Der State kann in einer Variablen vom Typen int oder byte gespeichert werden. 
> Die Werte der States können mittels ```#define STATE_NAME 0``` o.Ä. benannt werden.
> Zudem könnten die Funktionen ```get_random_number()```, ```check_tries()``` und ```check_number()``` auch States zurückgeben, 
> die in der aufrufenden Funktion in eine Globale geschrieben werden, um den Code übersichtlich zu gestalten, auch wenn hierdurch 
> weniger klar wird, von welchem State in welchen gewechselt wird.

<details>
<summary>Lösung</summary>

```C
int my_number = -1, tries = 5;

#define RANDOM_NUMBER_STATE   0
#define CHECK_TRIES_STATE     1
#define CHECK_NUMBER_STATE    2

byte program_state = RANDOM_NUMBER_STATE;                                        // State-Variable

byte check_tries() {                                                             // Überprüfe die übrigen Versuche
    if(tries != 0) {
      Serial.println(String("Versuche übrig: ") + tries);
      tries--;
      return CHECK_NUMBER_STATE;                                                 // Wenn noch Versuche übrig sind, gehe zu CHECK_NUMBER State
    }

    Serial.println("Du hast keine Versuche mehr übrig und verloren :(");
    Serial.println(String("Meine Nummer war ") + my_number);

    return RANDOM_NUMBER_STATE;                                                  // Wenn keine Versuche übrig sind, gehe zu State RANDOM_NUMBER
}


void get_random_number() {                                                       // Generiere eine zufällige Nummer
    Serial.println("Ich überlege mit eine neue Nummer zwischen 0 und 100");
    my_number = random(100);
    Serial.println("Los geht's mit dem Raten :)");
    
    tries = 5;
}


byte check_number() {                                                             // Überprüfe die angegebene Nummer
    String      answer = Serial.readString();
    
    if(answer == "")
        return CHECK_NUMBER_STATE;                                                // Wenn keine Eingabe erfolgt ist, bleibe im CHECK_NUMBER State
    
    int     user_number = answer.toInt();
    byte    ret = CHECK_TRIES_STATE;                                              // Wenn die Nummer zu groß oder zu klein ist, gehe zum CHECK_TRIES State
    
    Serial.print("Meine Nummer ist ");
    
    if(user_number > my_number)
        Serial.print("kleiner als ");
    else if(user_number < my_number)
        Serial.print("größer als ");
    else {
        Serial.print("genau ");
        ret = RANDOM_NUMBER_STATE;                                                // Wurde die Nummer erraten, gehe zum RANDOM_NUMBER State
    }
    
    Serial.println(user_number);
    return ret;
}


void setup() {
    Serial.begin(9600);
    while(!Serial);
    
    randomSeed(analogRead(A0));
}


void loop() {
    switch(program_state) {                                                     // Überprüfe den aktuellen State
        case CHECK_TRIES_STATE:
            program_state = check_tries();
            break;
        
        case RANDOM_NUMBER_STATE:
            get_random_number();
            program_state = CHECK_TRIES_STATE;
            break;
        
        case CHECK_NUMBER_STATE:
            program_state = check_number();
    }
}
```

</details>

In der Implementierung wird anstelle von verschachtelten if-Anweisungen die switch-Struktur verwendet. Sofern einzelne Werte 
in einer Variablen überprüft werden sollen, ist dies eine bequeme Möglichkeit hierfür. Wichtig sind die ```break;``` Anweisungen 
am Ende eines ```case```-Blocks: Werden diese weggelassen, führt C++ die folgende case-Anweisung weiter aus (sog. "Fallthrough"). 
Dies kann unter Umständen auch nützlich sein, z.B. wenn bei verschiedenen Werten der gleiche Codeblock ausgeführt werden soll.

Soll ein Block ausgeführt werden, wenn keine der Optionen auf die Variable zutrifft, kann am Ende der switch-Anweisung ein 
```default:```-Block erstellt werden. Auch in diesen kann "gefallen" werden, an seinem Ende benötigt er jedoch keine 
```break;``` Anweisung.

# Eine Außenwelt

Damit wir nicht nur einfach auf einem schwächeren Prozessor als unserem PC programmieren, sondern coole Komponenten bauen können, 
müssen wir den Arduino nach außen verbinden. Dies geschieht über die Pins an dem Ardunio. Hierbei ist wichtig, dass ein Pin 
mit der richtigen Eigenschaft verbunden wird.

![Arduino Nano Every Pinout](https://docs.arduino.cc/static/90c04d4cfb88446cafa299787bf06056/ABX00028-pinout.png)

Das vollständige Datasheet zum Arduino Nano Every ist [hier](https://docs.arduino.cc/static/441cdc31c2622d63f370221ae24fb7b3/ABX00028-datasheet.pdf) zu finden.

Grob gibt es:
- Stromversorgung mit 3.3V und 5V
- Analoge Pins
  - Senden oder empfangen Spannungswerte, die sie in Ints zwischen 0 und 1023 umwandeln
  - Lassen sich im Programmcode als A0-A7 direkt ansprechen
  - Fast alle analogen Pins können auch als digitale Pins verwendet werden.
- Digitale Pins
  - Unterscheiden zwischen OUTPUT und INPUT, sowie zwischen LOW und HIGH
  - Lassen sich im Programmcode mit den Zahlen 2-13 als Parameter z.B. der Funktion ```digitalWrite()``` ansprechen 
  - Einige digitale Pins können die Range von analogen Pins über PWN (Pulse Width Manipulation) simulieren
    - Hierbei wird durch die Frequenz einer Rechteckwelle ein spezifischer Wert gesendet
    - Pins, die hierzu in der Lage sind, werden auf dem Pinout-Sheet mit "~" gekennzeichnet
    - Im Nano Every sind dies die Pins D3, D5, D6, D9 und D10
  - Der Pin D13 ist etwas besonders im Auslesen, da hier eine LED angebracht ist, er wird niemals die vollen 5V Spannung erreichen
- Für spezielle Protokolle vorgesehene Pins
  - TX/RX für ein asynchrones Transmitterprotokoll
  - Pins A4 und A5 als SDA und SCL für das I2C-Protokoll (kann im Programmcode mittels der globalen Klasseninstanz Wire angesprochen werden)
  - Pins D11, D12 und D13 als SPI MOSI, SPI MISO und SPI SCK (kann im Programmcode mittels der globalen Klasseninstanz SPI angesprochen werden)
  - Diese Pins können alle außerhalb der speziellen Protokolle als normale analoge und/oder digitale Pins genutzt werden

### Wiggle Wiggle

Nutze ein Breadboard, um ein Arduino mit einem der Joysticks zu verbinden. Hierbei muss der "B"-Pin des Joysticks mit einem Digitalpin,
die "X" und "Y"-Pins mit jeweils Analogpins verbunden werden. Die Stromversorgung des Joysticks erfolgt ebenfalls über das Breadboard.

Die Analogpins lassen sich mittels ```analogRead(PIN)``` auslesen, wobei PIN für die Pin-ID A0-A7 steht. Der Rückgabewert ist ein int.
Der Digitalpin muss in ```setup()``` zunächst auf den Input-Mode gestellt werden, dies kann mittels ```pinMode(PIN_NR, MODE)``` erfolgen,
wobei PIN_NR die Nummer des Pins von 2 bis 13 (ohne "D") und MODE einer der Werte OUTPUT, INPUT oder INPUT_PULLUP ist. Danach kann der Pin
beliebig mittels ```digitalRead(PIN_NR)``` ausgelesen werden, welches entweder HIGH oder LOW zurückgibt.

> Schreibe ein Programm, das kontinuierlich die X und Y-Position des Joysticks ausgibt, sowie informiert, wenn der Joystick gedrückt worden ist.

Gib den verwendeten Pins im Programm mittels ```#define``` direkt am Anfang sprechende Namen (z.B. ```#define X_PIN A2```
oder ```#define BTN_PIN 4```). Dies vereinfacht auch die Bearbeitung des Codes, falls mal andere Pin-Belegungen genutzt werden sollten.
```analogRead()``` liest int-Werte direkt ein, welche Du über ```Serial.print()``` mit einem bisschen Extratext ausgeben kannst.
```digitalRead()``` gibt entweder ```HIGH``` oder ```LOW``` zurück, wobei beides Ganzzahlwerte sind, die ebenfalls ausgegeben werden können.
Vergiss nicht, den Digitalpin in ```setup()``` auf INPUT zu setzen.

> Als Extraaufgabe kannst Du versuchen, den "Gedrückt"-Status zu speichern und sowohl beim Drücken als auch beim Loslassen des Knopfes zu informieren.

<details>
<summary>Lösung</summary>

```C
#define X_PIN           A2                                              // Vereinfacht Zugriff auf Pins, z.B. wenn wir das Setup mal anders aufbauen
#define Y_PIN           A3
#define BTN_PIN         4

byte    button_state;                                                   // Speichert letzten Zustand des Knopfes: HIGH oder LOW

void setup() {
    Serial.begin(9600);
    while(!Serial);
    
    pinMode(BTN_PIN, INPUT);                                            // Setzt den Digitalpin in INPUT-Modus
    
    button_state = LOW;                                                 // Initialisiert den State
}

void loop() {
    Serial.print(String("X: ") + analogRead(X_PIN));                   // Ausgabe von X und Y
    Serial.print(String(" Y: ") + analogRead(Y_PIN));
    
    if(digitalRead(BTN_PIN) != button_state) {                          // Überprüfe, ob eine State-Änderung stattfand
        Serial.print("Button ");
        button_state = button_state ? LOW : HIGH;                       // "Flip" den Button State.
        Serial.println(button_state ? "released" : "pressed");          // Ausgabe je nach neuem Button State
    }
}
```

</details>

Die Variable ```button_state``` wird genutzt, um den letzten ausgelesenen Wert am BTN_PIN zu speichern. Wenn dieser sich 
ändert, so wird der Wert in ```button_state``` auch geändert und entsprechender Text ausgegeben. Statt ```if```-Anweisungen 
wird hier das ```Ternary If``` verwendet: ```BEDINGUNG ? WENN_JA_WERT : WENN_NEIN_WERT```. Dies vereinfacht das Schreiben 
von Code, wenn Parameter- oder Variablenzuweisungen bedingt erfolgen sollen. Das klappt hier sehr gut, da LOW als 0 und HIGH 
als 1 definiert ist und C++ boolsche Werte so sieht:

```C
true == !false && !0 && "Wert" && "" && !NULL && !nullptr

false == !true || 0 || NULL || nullptr
```

> Verändere das Programm so, dass nur bei einer Änderung des Joysticks seine Position ausgegeben wird. Beachte, dass im "Neutralzustand" 
> der Wert des Joysticks etwas fluktuiert.
> Hilfreich kann sein, eine "Deadzone" zu definieren, in welcher noch nichts passieren soll.

Definiere am besten eine Funktion ```float with_deadzone(float value)```, welche überprüft, ob ```value``` außerhalb eines
durch eine globale Variable (z.B. ```float dead_zone = 0.05f```) definierten Zone um den Mittelpunkt der ausgelesenen Werte
an den Pins liegt. Die Funktion soll nur ```value``` zurückgeben, wenn das zutrifft, ansonsten ```0```.

> Extraaufgabe: Versuche, die Axenbewegungen in dem Datentyp float auf Werte zwischen -1 und 1 zu mappen, wobei 0 die Neutralstellung ist.

Hilfestellung: Das Mapping kann über die Formel: ```new_value = -1.0f + (initial_value * 2.0f) / 1023.0f``` erfolgen, da 
wir Werte von 0 bis 1023 auf Werte zwischen -1 und 1 mappen wollen. Die ursprüngliche Formel ist 
```new_value = to_start + ((value - from_start) * (to_end - to_start)) / (from_end - from_start)```.
Schreibe am besten eine Funktion ```float map_axis(float value)```, die den gemappten Wert zurückgibt. Die Funktion sollte
nicht ```map()``` heißen, da Arduino diese Funktion bereits implementiert, diese jedoch nur für Ganzzahlen funktioniert.

<details>
<summary>Lösung</summary>

```C
#define X_PIN       A1
#define Y_PIN       A2
#define BTN_PIN     4

float dead_zone = 0.05f;
    
float map_axis(float value) { return -1.0f + (value * 2.0f) / 1023.0f; }                                // Konvertiert 0..1023 zu -1..1
float with_deadzone(float value) { return value > dead_zone || value < -dead_zone ? value : 0.0f; }     // Gibt 0 zurück, falls die Deadzone nicht verlassen wurde
    
float axis(byte pin) { return with_deadzone(map_axis(analogRead(x_pin))); }                             // Fasst die Funktionen zusammen

void setup() {
    Serial.begin(9600);
    while(!Serial);
    
    pinMode(BTN_PIN, INPUT);
}

void loop() {
    float   x = axis(X_PIN),
            y = axis(Y_PIN);
    if(x != 0 || y != 0)
        Serial.println(String("X: ") + x + " Y: " + y);
}
```
</details>

Jetzt können wir einen Input abfragen und darauf reagieren. Als Nächstes müssen wir auch "nach außen" mit anderen Bauteilen interagieren.

### Ein Licht am Ende des Tunnels

Baue das vorherige Projekt ab und verbinde eine LED über das Breadboard mit dem Arduino. Das Datasheet zur LED findest Du 
[hier](https://www.led-genial.de/mediafiles//Sonstiges/PL9823.pdf).
Die LED sollte schon leuchten, wenn Du sie lediglich an den Arduino anschließt. Um mit ihr kommunizieren zu können, benötigen 
wir allerdings eine externe Bibliothek. Diese kann über die Arduino IDE leicht heruntergeladen und installiert werden. Es gibt 
einige Bibliotheken zur Kommunikation mit diesem LED-Typ, hier wird die Adafruit Neopixel Bibliothek verwendet.

- Wähle in der linken Leiste in der IDE das Bibliotheks-Zeichen aus (3. von oben)
- Suche nach "Adafruit NeoPixel"
- Das erste Ergebnis sollte das korrekte sein, klicke auf "Install"

Ab jetzt kannst Du die Funktionen der Bibliothek in jedem Projekt in der IDE nutzen, sofern Du ```#include <Adafruit_NeoPixel.h>``` 
an den Anfang der Datei schreibst.

Im Beispiel laden wir die Bibliothek und setzen die LED auf ein mittelstarkes "Rot":

```C
#include <Adafruit_NeoPixel.h>                          // Einbinden des externen Headers

#define LED_PIN     4                                   // LED liegt an Pin D4 an

Adafruit_NeoPixel       led(1, LED_PIN, NEO_RGB);       // Deklariere und initialisiere die LED: die Parameter geben an: Wir haben 1 LED an Pin 4, welche die Daten in der Reihenfolge Rot -> Grün -> Blau erwartet

void setup() {
    led.begin();                                        // Starten der Bibliothek
}

void loop() {
    led.clear();                                        // LED-Speicher wird geleert, erwartet neue Daten
    
    led.setPixelColor(0, led.Color(150, 0, 0));         // Setzt die LED an Position 0 (die 1. LED) auf die Farbe Color(Red, Green, Blue)
    led.show();                                         // Setzt die Änderungen um
}
```

Die Kontrolle der LED erfolgt über die Klasse ```Adafruit_NeoPixel```, von welcher wir eine Instanz global initialisieren. 
Als Parameter erwartet der Constructor der Klasse die Anzahl der LEDs (können z.B. bei einem LED-Strip oder einer LED-Matrix 
mehrere sein), den digitalen Pin, über den die Kommunikation stattfindet und die Reihenfolge der Datenübertragung (hier RGB, 
siehe Datenblatt. Es gibt auch beliebige andere Reihenfolgen, je nach Bauart).

In ```setup()``` müssen wir ```.begin()``` aufrufen, damit die Bibliothek initialisiert. Dies muss nicht bei allen Bibliotheken 
erfolgen, allerdings hat sich die Schreibweise bei Arduino anscheinend oftmals bewährt. Im ```loop()``` setzen wir in jedem 
Durchlauf die Farbe (redundant) auf ein mittelstarkes Rot: ```clear()``` leert den internen Speicher der LED und lässt sie 
auf neue Daten warten. ```Color(R, G, B)``` setzt Werte von 0 bis 255 in übertragbare Bytewerte um, während ```setPixelColor(LED_NR, COLOR)``` 
zunächst die Position der angesteuerten LED im Array (beginnend mit 0 bis N-1) und anschließend den Rückgabewert von ```Color()``` als
zu übertragende Farbe erwartet. Zum Schluss müssen wir der LED noch sagen, dass die Änderungen umgesetzt werden sollen, dies passiert mit ```show()```.

### Farbenspiel

Arduino bietet die Funktion ```cos()```, welche als Parameter ein float als Winkel in rad. erwartet und den Cosinus als double 
zwischen -1.0 und 1.0 zurückgibt. Mit dieser Funktion ist es möglich, ein Programm zu entwerfen, das eine LED nacheinander in 
den Farben Rot, Grün und Blau ein- und ausleuchten lässt. Um die Cosinuswerte auf die Bitwerte 0 bis 255 umzusetzen, kann die 
Formel aus der Voraufgabe wieder verwendet werden, um ```byte map_color(float cos_input)``` zu schreiben. An die Cosinusfunktion 
könnte z.B. eine Counter-Variable vom Typen ```float``` verfüttert werden, die jeden Loop hochgezählt wird (ein guter Abstand 
ist 0.1f pro Loop). Die Variable sollte als Globale definiert werden, damit sie ihren Wert zwischen den loop()-Aufrufen beibehält.

Der Wechsel der Farbe kann z.B. als State-machine verwirklicht werden: Die States STATE_RED, STATE_GREEN und STATE_BLUE könnten 
mittels ```#define``` auf die Zahlen 0 - 2 definiert werden und der aktuelle State in einer Variablen vom Typen ```int``` gespeichert 
werden. Wenn nun die Farbwerte für Rot, Grün und Blau in einem Array von 3 Bytes gespeichert werden (Syntax: ```byte  colors[3] = {0, 0, 0}```), 
könnte mit der State-Variablen immer auf das aktuelle Element im Array zugegriffen werden (z.B. ```state_var = STATE_RED; colors[state_var] = 150;```). 
Anschließend muss nur noch überlegt werden, wann der Farbwechsel stattfinden soll und von welcher Farbe zu welcher gewechselt 
wird und der Statevariablen der jeweilige Wert zugewiesen werden.

Wenn der Farbwechsel zu schnell erfolgt, kann mittels ```delay(MS)``` der Programmablauf für MS Millisekunden unterbrochen werden, 
um Änderungen sichtbar zu machen.

> Versuche, das beschriebene Programm zu konzipieren. Orientiere Dich an dem Beispielprogramm aus dem Vorkapitel.

```C
#include <Adafruit_NeoPixel.h>

#define LED_PIN     4

#define RED_STATE   0                                   // Definieren der States
#define GREEN_STATE 1                                   // Werden auch genutzt, um auf entsprechende Elemente im Array 'colors' zuzugreifen
#define BLUE_STATE  2

Adafruit_NeoPixel       led(1, LED_PIN, NEO_RGB);

int                     current_color = RED_STATE;      // Hält den aktuellen State RED_STATE, GREEN_STATE oder BLUE_STATE
byte                    colors[3] = {0, 0, 0};          // Array mit unseren Farben in Reihenfolge Rot, Grün, Blau
float                   counter = 0.0f;                 // Zählervariable, die an cos() verfüttert wird

byte map_color(float value) {                           // Angeglichene Mapping-Funktion, kann von -1.0 bis 1.0 auf 0 bis 255 mappen
    return ((value + 1) * 255) / 2;
}

void setup() {
    led.begin();
}

void loop() {
    counter += 0.1;                                     // Counter wird jeden Loop hochgezählt
    colors[current_color] = map_color(-cos(counter));   // Durch -cos(counter) beginnen wir mit dem "niedrigsten" Wert und füttern ihn nach Konvertierung
                                                        // an die aktuelle Farbe

    if(counter >= 6.28f) {                              // Bei ungefähr 2PI sind wir mit einem Zyklus durch
        colors[current_color] = 0;                      // "Housekeeping", setzt verwendete Variablen wieder auf 0
        counter = 0.0f;

        switch(current_color) {                         // State-Progress
            case RED_STATE:
                current_color = GREEN_STATE;
                break;
            case GREEN_STATE:
                current_color = BLUE_STATE;
                break;
            case BLUE_STATE:
                current_color = RED_STATE;
        }
    }
    
    led.clear();
    led.setPixelColor(0, led.Color(colors[RED_STATE], colors[GREEN_STATE], colors[BLUE_STATE]));   // Füttere unser byte-Array an die LED
    led.show();

    delay(50);                                           // Lässt die LED etwas langsamer "phasen"
}
```

### Extrakapitel: Zeigermagie

Das eben geschrieben Programm funktioniert und macht exakt was es soll. Es eignet sich auch gut, um die Funktionsweise von 
Zeigerarithmetik in C++ zu verstehen. Das folgende Beispiel zeigt genau das gleiche Programm, nur wurde die State-machine durch 
einen laufenden Zeiger ersetzt:

```C
#include <Adafruit_NeoPixel.h>

#define LED_PIN     4

Adafruit_NeoPixel       led(1, LED_PIN, NEO_RGB);

byte                    *current_color;                     // Zeiger auf eine beliebige Byte-Variable. Wird auf eine der 3 Variablen im colors-Array zeigen
byte                    colors[3] = {0, 0, 0};
float                   counter = 0.0f;

byte map_color(float value) {
    return ((value + 1) * 255) / 2;
}

void setup() {
    led.begin();

    current_color = colors;                                 // Initialisiere den Zeiger: Zeigt so auf den Beginn von 'colors', ist äquivalent zu
                                                            // current_color = &colors[0];
}

void loop() {
    counter += 0.1;
    *current_color = map_color(-cos(counter));              // Durch die Dereferenzierung (das Sternchen) verändern wir durch die Zuweisung den Inhalt der
                                                            // Variablen, auf die current_color zeigt, nicht current_color selbst.

    if(counter >= 6.28f) {
        *current_color = 0;                                 // Wieder Dereferenzierung: Wir greifen auf die Variable zu, auf die current_color zeigt
        counter = 0.0f;
        
        current_color++;                                    // Zeiger wird um 1 byte herauf gezählt

        if(current_color > &colors[2])                      // Wenn current_color auf eine Adresse außerhalb des Arrays zeigt
            current_color = colors;                         // wird der Zeiger wieder auf den Anfang des Arrays zurückgesetzt
    }
    
    led.clear();
    led.setPixelColor(0, led.Color(colors[0], colors[1], colors[2]));
    led.show();

    delay(50);
}
```

Zeiger können in C++ genutzt werden, um die Werte anderer Variablen zu manipulieren: Der Inhalt einer Zeigervariablen ist 
kein Wert wie eine Zahl oder ein Buchstabe, sondern die Adresse einer anderen Variablen (was u.U. auch wiederum eine Zeigervariable 
sein kann). Um nicht auf den Inhalt einer Variablen zuzugreifen, sondern auf deren Adresse im Speicher, kann einem Variablennamen 
ein ```&``` vorangestellt werden. Da wir hier dem Zeiger ```current_color``` jedoch einen Array zuweisen, können wir darauf 
verzichten, da Arrays im Grunde genommen auch Zeiger sind (der Variablenname "zeigt" quasi auf den Beginn des Arrays im Speicher).

Wird einem Zeiger ein Asterisk vorgestellt, so wird er dereferenziert: Das bedeutet, dass wir jetzt nicht auf die Zeigervariable 
zugreifen, sondern dass wir im Grunde genommen die Zeigervariable an dieser Stelle durch die Variable ersetzen, auf die sie zeigt.

Zeiger können auch - wenn sie nicht dereferenziert werden - die Adresse, auf die sie zeigen ändern. So kann über eine Variable 
auf verschiedene Adressen im Speicher zugegriffen werden.

Im Beispiel wird sich dies zunutze gemacht, indem weiterhin der Array ```colors``` alle 3 Bytes für unsere Farben Rot, Grün 
und Blau enthält. Nun wird der Wert der Variablen, die grade "dran" ist stets durch Zugriff über ```current_color``` verändert. 
Muss der State gewechselt werden, so wird ```current_color``` einfach auf das nächste Element zeigen gelassen.

Hierfür wird im Beispiel sog. Zeigerarithmetik genutzt: Auf Zeiger lassen sich fast alle Rechenoperatoren anwenden, auch 
Inkrement und Dekrement. Hierbei addiert der Zeiger die Größe seines Typs (hier byte) hinzu und zeigt anschließend auf die 
"neue" Adresse. Da ein Array im Speicher nebeneinander liegende Adressen belegt, können wir hiermit jede Variable im Array 
```colors``` nacheinander durchlaufen.

Es muss nur darauf geachtet werden, dass über einen Zeiger nicht versehentlich ein Speicherbereich verändert wird, der nicht 
alloziert wurde oder durch einen anderen Programmteil genutzt wird: Daher überprüfen wir im Beispiel mit 
```if(current_color > &colors[2])``` ob ```current_colors``` auf eine Adresse jenseits des letzten Arrayelements zeigt.
Sollte dies der Fall sein, wird der Zeiger wieder auf den Anfang des Arrays zurückgesetzt.

Zeigerarithmetik kann sehr nützlich sein und es ist sinnvoll, sich mit dieser zentralen Funktionsweise von C++ auseinanderzusetzen 
(zudem nutzen viele andere Programmiersprachen das Prinzip "under the hood": Die Funktionsweise von Python-Objekten als 
Funktionsparameter wird sehr viel klarer, wenn man sie als Zeiger denkt). Allerdings bergen Zeiger auch viele Gefahren: 
Wenn z.B. ein Wert an einer falschen Speicheradresse verändert wird, oder versucht wird, mit einem Zeiger auf eine Variable 
zuzugreifen, deren Gültigkeitsbereich bereits verlassen wurde. Daher werden in modernem C++ oftmals eigens hierfür entworfene Objekte
(z.B. ```unique_pointer``` oder ```shared_pointer```) verwendet, welche diese Gefahren minimieren. Die meisten Standardbibliotheken 
von Microcontrollern implementieren diese Klassen jedoch nicht.