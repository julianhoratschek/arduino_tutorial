# CPP Übersicht

Dies ist noch kein Tutorial für Arduino, sondern eine allgemeine C++ Referenz mit Blick auf
die Limitierungen in einem Microcontroller.

## Beispielprogramm, Vergleich mit Python

Das Beispielprogramm erstellt eine eigene Version von strlen / len für Strings und wendet die Funktion auf alle dem Programm übergebenen Parameter an.

```C
#include <iostream>                                                                                         |import sys
                                                                                                            |
int my_strlen(char *arg) {                                                                                  |def my_strlen(arg) -> int:
   int  char_count = 0;                                                                                     |   char_count = 0
                                                                                                            |
   while(arg[char_count] != '\x00')                                                                         |   while arg[char_count] != "\x00":
       char_count++;                                                                                        |       char_count += 1
                                                                                                            |
   return char_count;                                                                                       |   return char_count
}                                                                                                           |
                                                                                                            |
int main(int argc, char* argv[]) {                                                                          |if __name__ == "__main__":
    using namespace std;                                                                                    |
                                                                                                            |    
    for(int i = 1; i < argc; i++)                                                                           |   for i in len(1, sys.argv):
        cout << "Argument Nr. " << i << " has " << my_strlen(argv[i]) << " characters." << endl;            |       println(f"Argument Nr. {i} has {my_strlen(sys.argv[i])} characters.")
                                                                                                            |
    return 0;                                                                                               |
}                                                                                                           |
```

Grundlegende syntaktische Unterschiede zwischen Python und Cpp:

| Unterschied          | C++                                     | Python                                 |
|----------------------|-----------------------------------------|----------------------------------------|
| Zeilenende           | Semikolon                               | Absatz                                 |
| Codeblöcke           | Innerhalb von {}, wenn mehr als 1 Zeile | Immer eingerückt nach ":"              |
| Variablendeklaration | Deklaration mit fester Typangabe        | "Usage is Declaration" mit fluidem Typ |
| Kontrollstrukturen   | Klammern umschließen Bedingungen        | Klammern optional                      |

## Arduino-Besonderheiten

Arduino nutzt eine eigene Version des avr-gcc-Compilers. Das bedeutet, dass viele Funktionen und Eigenschaften, die C++ eigentlich beinhaltet (z.B. einen Großteil der Standardbibliothek), nicht nutzbar sind.
Das Tutorial ist spezifisch auf die Arduino-Microcontroller ausgelegt, dementsprechend ist der verwendete C++ Code nah an C angelehnt.

Ein Arduino-Programm hat keinen klassischen "main"-Eintrittspunkt. Stattdessen ist ein Minimalprogramm so aufgebaut:

```C
void setup() {
    // Einmalig bei Start des Arduino ausgeführter Code
}

void loop() {
    // Zyklisch wiederholter Code
}
```

Input und Output erfolgen nicht über eine Konsole, sondern über die PINS des Mikrocontrollers, über welche sodann Bildschirme, LEDs oder sonstige Komponenten angesprochen werden können.
In den folgenden Beispielen wird jeweils Arduino-konformer Code verwendet. Als Output gilt zunächst der Serial-Output, welcher in den ROM des Controllers geschriebene Strings ausliest und
auf einem angeschlossenen Serial-Monitor (z.B. dem PC des Programmierers) ausgibt. Ein "Hello World" sieht damit so aus:

```C
void setup() {
    Serial.begin(9600);     // Bitrate der Serialübertragung, lässt sich am Serialmonitor in der Arduino-IDE ablesen.
    while(!Serial);         // Auf Verbindung warten
}

void loop() {
    Serial.println("Hello World");  // Gibt jeden Loop eine Zeile Hello World aus.
}
```

Die println Funktion kann Strings, Ganz- und Fließkommazahlen ausgeben. Außerdem können Zahlenwerde binär, octal, dezimal oder hexadezimal ausgegeben werden:

```C
Serial.print(17, DEC);  -> 17
Serial.print(17, BIN);  -> 0b00010001
Serial.print(17, OCT);  -> 021
Serial.print(17, HEX);  -> 0x11
```

Wenn komplexere Strings mit eingefügten Parametern ausgegeben werden sollen, so müssen diese vorher formatiert werden (siehe [Strings](#strings)):

```C
String      line = String("Hallo ");
int         zahl = 12;

line += "Welt, meine Lieblingszahl ist: ";
line += zahl;

Serial.println(line);
```

## Präprozessor

In C und C++ können Anweisungen an den Präprozessor gegeben werden: Dieser bearbeitet die Dateien, bevor sie in Binärcode kompiliert werden. Die Anweisungen starten immer mit "#".
Für Microcontroller sind die wichtigsten beiden Anweisungen:

```C
#include "header_datei.h"
```

und

```C
#define ersetze_dies "Mit dem hier"
```

Die Anweisungen enden NICHT auf einem Semikolon. Include liest Definitionen aus der gegebenen Header-Datei aus, ähnlich wie Pythons import.
Define erstellt ein Alias für alles, was danach kommt. Dies eignet sich z.B., um bestimmten Zahlen eingehende Namen zu geben oder komplexe, oft wiederholte Codeabschnitte bequem zusammenzufassen:

```C
#define OUTPUT_LED_PIN          4
#define important_flag1         0b1101101
#define important_flag2         0b1110101
#define has_important_flags(x)  ((x >> 8) & important_flag1) && (x & important_flag2)
```

Defines können zwar Parameter übergeben bekommen (siehe Beispiel), allerdings sind es keine Funktionen: Sie ersetzen stumpf einen Zeichensatz durch einen anderen.


## Zahlen und Operatoren

Zahlen können in C++ im Programmcode in verschiedenen Zahlensystemen geschrieben werden: binär, octal, dezimal und hexadezimal:

| Binär      | Octal | Dezimal | Hexadezimal |
|------------|-------|---------|-------------|
| 0b00011111 | 037   | 31      | 0x1F        |

Fließkommazahlen werden mittels eines Punktes als Komma gekennzeichnet, z.B. 3.14.

Zur Übersichtlichkeit dürfen Zahlen in neueren C++-Versionen mittels ' getrennt werden:

```C
int     big_int = 1'000'000'000'000;
float   big_float = 0.101'302'849'019;
```

### Arithmetische Operatoren

Folgende Rechenoperatoren sind unterstützt:

```C
int     a = 10, b = 3, c = 0;

c = a + b;  // Addition
a += b;

c = a - b;  // Subtraktion
a -= b;

c = a * b;  // Multiplikation
a *= b;

c = a / b;  // Division
a /= b;

c = a % b;  // Modulo
a %= b;

a++;        // Inkrement
++a;        // Präfix-Inkrement. Relevant z.B. bei Funktionsaufrufen.
a += b;

a--;        // Dekrement
--a;        // Präfix-Dekrement. Relevant z.B. bei Funktionsaufrufen.
a -= b;
```

Um die Präfix- und Postfix-Inkrement- und Dekrementoperatoren besser zu verstehen, kann dieses kleine Programm helfen (in Standard Cpp, NICHT im Arduino):

```C
#include <iostream>
using namespace std;

void f(int n) { 
    cout << "In F(): " << n << endl; 
}

int main() {
    int post = 1, pre = 1;
    
    cout << "Vor F(post): " << post << endl;    // post == 1
    f(post++);                                  // post == 1
    cout << "Nach F(post): " << post << endl;   // post == 2
    
    cout << "Vor F(pre): " << pre << endl;      // pre == 1
    f(++pre);                                   // pre == 2
    cout << "Nach F(pre): " << pre << endl;     // pre == 2
    
    return 0;
}
```

### Bit-Operatoren

Außerdem gibt es Operatoren, die auf Bitebene arbeiten:

| Typ    | Operator             | Ergebnis   |
|--------|----------------------|------------|
| AND    | 0b1010 & 0b1001      | 0b1000     |
| OR     | 0b1010 &#124; 0b1001 | 0b1011     |
| XOR    | 0b1010 ^ 0b1001      | 0b0011     |
| NOT    | ~0b1010              | 0b0101     |
| LSHIFT | 0b11110001 << 2      | 0b11000100 |
| RSHIFT | 0b10001111 >> 2      | 0b00100011 |

```C
byte        a = 0b10010110, b = 0b01011010, c = 0;

// AND
c = a & b;
a &= b;

// OR
c = a | b;
a |= b;

// XOR
c = a ^ b;
a ^= b;

// NOT
c = ~a;

// RSHIFT
c = a >> 4;
a >>= 4;

// LSHIFT
c = a << 4;
a <<= 4;
```

### Boolsche Operatoren

Viele der Bit-Operatoren sind den logischen Operatoren sehr ähnlich, sollten jedoch nicht verwechselt werden: Bit-Opteratorn geben immer Zahlenwerte zurück, während
logische Operatoren immer Wahr oder Falsch zurückgeben.

| Typ | Operator         |
|-----|------------------|
| AND | A && B           |
| OR  | A &#124;&#124; B |
| NOT | !A               |



## Variablen und Datentypen

Variablen dürfen an jeder Stelle im Programm definiert werden, jedoch hat der Ort der Definition Einfluss auf die Sichtbarkeit und den Gültigkeitsbereich der Variablen (siehe [Scope](#scope)).
Sie dürfen beliebeig benannt werden mit Buchstaben, Zahlen und Unterstrichen als gültigen Zeichen. Hierbei darf ein Variablenname nicht mit eienr Zahl beginnen und ist Case-sensitiv.
Speicherplatz für eine Variable wird belegt, sobald diese definiert wird. Die Initialisierung (Belegen mit einem Wert) der Variable kann später erfolgen. Wird jedoch versucht, eine nicht
definierte Variable zu initialisieren oder eine Variable mit einem falschen Typ zu füllen (z.B. ein Int in eine String-Variable), so kann dies zu Compiler- und/oder Laufzeitfehlern führen.

Der von einer Variablen belegte Speicherplatz kann ggf. je nach Architektur variieren. So kann ein Int standardmäßig 32 oder 64 Bit belegen.

### "Streng" typisiert

C++ ist zwar nach wie vor eine streng typisierte Sprache - also kann nicht ohne größeren Aufwand zwischen Variablentypen gewechselt werden - allerdings gibt es seit C++11 eine
deutliche Erleichterung: Das Schlüsselwort "auto" inseriert, sofern genügend Informationen vorliegen, den gewünschten Typ bei Deklaration automatisch:

```C
auto    zahl = 193;                         // -> int
auto    fliesskomma = 4.35;                 // -> float
auto    cstr = "Hallo Welt";                // -> const char*

auto    arr = {1, 2, 3, 4, 5};              // -> initializer_list<int>

for(auto &e: arr)                           // -> initializer_list<int>::forward_iterator
    Serial.println(e);

// FEHLER!!
auto    error_arr = {1, "Hallo", 4.5};      // FEHLER: initializer_list muss gleiche Typen beinhalten
```

Trotzdem macht es Sinn, grade wenn wenig Speicher zur Verfügung steht und man auf diesen ziemlich direkt zugreifen möchte, sich mit den tatsächlichen Grundtypen auseinanderzusetzen.

### Scope

Scope bezeichnet den Gültigkeitsbereich von Variablen. Generell kann in C++ ein Scope als der Bereich zwischen { und } gedacht werden. Speicher, der innerhalb einer geschweiften Klammer
reserviert wird, wird generell mit dem Schluss der zugehörigen } automatisch wieder freigegeben. Dies gilt NICHT für manuell auf dem Heap reservierten Speicherplatz (siehe [Pointer](#pointer)).

Das bedeutet auch, dass Variablen, die in Funktionen initialisiert werden, normalerweise am Ende eines Funktionsaufrufes verworfen werden.

Neben diesem "lokalen" Scope gibt es den "globalen" Scope. Variablen, die hier deklariert werden, sind von überall innerhalb der gleichen Datei sichtbar und ggf. änderbar.
Dies gilt bei Arduino für alle Variablen, die außerhalb der Funktionen loop() und setup() (und natürlich jeder anderen, benutzerdefinierten Funktion) deklariert werden.

### Ganzzahlen

Je nach Datentyp können nur positive oder positive und negative Ganzzahlen dargestellt werden. Den Datentypen, die positive sowie negative Ganzzahlen darstellen können, kann das Schlüsselwort "unsigned" beigefügt werden,
damit sie zwar keine negativen Zahlen mehr, dafür aber große positive Zahlen darstellen können.

| Typ   | Größe             | Darstellungsbereich                        | Mit unsigned   |
|-------|-------------------|--------------------------------------------|----------------|
| byte  | 1 Byte            | 0 bis 255                                  | nur positiv    |
| char  | 1 Byte            | -2^7 bis (2^7)-1                           | 0 bis 255      |
| word  | Mindestens 2 Byte | 0-(2^16)-1                                 | nur positiv    |
| short | 2 byte            | -2^15 bis (2^15)-1                         | 0 bis (2^16)-1 |
| int   | 2 oder 4 Byte     | -2^15 bis (2^15)-1 oder -2^31 bis (2^31)-1 | 0 bis (2^32)-1 |
| long  | 4 Byte            | -2^31 bis (2^31)-1                         | 0 bis (2^32)-1 |

```C
int             i = -3294;
int             e = 1e4;
unsigned int    ui = 39483;
signed int      si = -48495;    // Explizite Deklaration möglich
```

C++ unterstützt auch weitere Typen, z.B. "long long" auf größeren Architekturen. Diese sind im Arduino nicht zwingend erforderlich.

### Fließkommazahlen

| Typ    | Größe  |
|--------|--------|
| float  | 4 Byte |
| double | 8 Byte |

### Boolsche Werte

Boolsche Werte unterscheiden nur zwischen Wahr und Falsch. Es sind logische Werte, die insbesondere als Flags, zur Ergebnisüberprüfung von Funktionen und bei if-Branches verwendet werden.

```C
bool    wahr_oder_falsch = true || false;
```

C++ zählt jedoch auch Zahlenwerte oder sogar Zeiger zu logischen Werten. "Wahr" kann für C++ als "Nicht NULL" gedacht werden. Also gilt logisch gesehen:

```C
false == (!true && 0 && !1 && NULL && nullptr)
```

Jedoch gilt ebenfalls:

```C
true == (!false && !0 && -1 && "Hallo" && "")
```

Der logische Speicherbereich für ein bool erstreckt sich zwar nur auf 1 Bit, jedoch wird auf der Arduino-Architektur trotzdem ein ganzes Byte reserviert.

### Arrays

Wird mehr als eine Variable von einem Typ benötigt, bzw. wenn in einer Variable ein Datencluster zusammengefsst werden soll, können Arrays definiert werden: Diese reservieren
eine gewünschte Menge Speicherplatz, abhängig von der Größe des gewählten Typs.

#### Statische Arrays (Stack)

Das Beispiel belegt 3 * 2 Byte Speicherplatz, dieser wird nicht auf 0 gesetzt, sondern beinhaltet "zufällige" Werte. Der Speicherplatz wird - wenn möglich - auf dem Stack belegt.

```C
    short        i_arr[3];
    
    i_arr[0] = 1;
    i_arr[1] = 2;
    i_arr[2] = 3;
```

Wird der Array bei Deklaration direkt initialisiert, kann die Größenangabe weggelassen werden:

```C
    short   i_arr[] = {1, 2, 3};
```

Die Indices von Arrays starten immer mit 0 und gehen bis n-1. Wird versucht, auf ein Element größer als n-1 zuzugreifen, erfolgt ein Speicherzugriffsfehler mit ggf. Programmabsturz.
Verlässt das Programm den Deklarationsort des Array (also z.B. die Funktion, in welcher er deklariert wird), so wird der Speicherplatz automatisch wieder freigegeben (siehe: [Scope](#scope)).

#### Dynamische Arrays (Heap)

Ist die Größe eines Arrays nicht vorab bekannt, so kann dynamisch Speicher belegt und wieder freigegeben werden. Hierbei ist wichtig, dass der Programmierer selbst daran denkt, den belegten
Speicher wieder freizugeben, da dies nicht automatisch durch C++ passiert. Dynamischer Speicher wird auf dem Heap angelegt.

```C
    short   *dyn_arr;               // Leere Adresse im Speicher wird deklariert, keine Größenangabe
    int     arr_size;

    arr_size = 3;
    dyn_arr = new short[arr_size];  // Neuer Array wird angelegt mit arr_size * 2 Byte.    
    
    dyn_arr[0] = 1;
    dyn_arr[1] = 2;
    dyn_arr[2] = 3;
    
    delete[] dyn_arr;               // Array MUSS freigegeben werden mit delete[], die eckigen klammern können leer bleiben.
```

> Zu merken ist: Sobald __new__ im Spiel ist, wird etwas auf dem Heap gespeichert und muss später mit delete freigegeben werden

### Pointer

Pointer sind eins der mächtigsten, aber auch gefährlichsten Werkzeuge von C++: Hiermit lässt sich Speicher sehr genau manipulieren, was zu hoher Kontrolle, aber auch zu hoher Fehleranfälligkeit führt.
In modernem C++ werden kaum noch "reine" C-Style-Pointer verwendet, sondern eigens hierfür erstellte Klassen (z.B. unique_pointer oder shared_pointer). Da die Standardbibliothek in
Mikrocontrollern diese jedoch nicht unbedingt unterstützt, wird hier nicht darauf eingegangen.

Ein Pointer ist eine Variable, welche die Adresse einer beliebigen anderen Variablen im Speicher beinhaltet. Die Adresse - also auf welche Variable der Pointer zeigt - lässt sich zur Laufzeit
beliebig verändern, ebenso der Wert der Variablen, auf welche gezeigt wird.

```C
int     int_var = 10;
int*    int_ptr;                                        // Leerer Zeiger, zeigt auf nichts

int_ptr = &int_var;                                     // Zeiger zeigt jetzt auf die Speicheradresse von int_var

Serial.print("<int_var> liegt an Speicheradresse ");
Serial.println(int_ptr);                                // Gibt den Inhalt von int_ptr, also die Speicheradresse von int_var aus
Serial.print("<int_var> beinhaltet den Wert ");
Serial.println(*int_ptr);                               // Dereferenzierung greift auf den Wert an der Speicheradresse in int_ptr zu: Also den Wert von int_var

*int_ptr = 20;                                          // Mittels Dereferenzierung lässt sich dieser Wert auch ändern

Serial.print("<int_var> beinhaltet nun den Wert ");
Serial.println(int_var);                                // Mit Effekt auf die Variable int_var, die immer noch an der gleichen Speicheradresse int_ptr liegt.
```

Dies ermöglicht z.B., dynamisch erstellte Strukturen (z.B. den Array aus dem Vorabsatz) "herumzureichen" und weiterzuverwenden,
auch wenn das Programm den eigentlichen Geltungsbereich der Variablen verlassen hat.

```C

// FALSCH
int *generate_array_stack(int size) {
    int     arr_st[size];       // Deklariere arr_st und reserviere size * sizeof(int) Byte Speicher auf dem STACK
    
    return arr_st;              // Deklarationsort von arr_st wird verlassen, Stack wird geleert, die Adresse arr_st hat keine Rechte mehr, in den Speicher zu schreiben
}

// KORREKT
int *generate_array_heap(int size) {
    int     *arr_hp = new int[size];   // Deklarierte arr_hp und reserviere size * sizeof(int) Byte Speicher auf dem HEAP
    
    return arr_hp;                     // Deklarationsort von arr_hp wird verlassen, der allozierte Speicher auf dem Heap bleibt aber bestehen. arr_hp zeigt weiterhin auf einen validen Array von size * sizeof(int) Größe.
}
```
In dem Beispiel würde ein Zugriff auf den Rückgabewert von generate_array_stack einen Fehler ergeben, da der angeforderte Speicherbereich ggf. wieder vergeben wurde. Auf den Rückgabewert von
generate_array_heap darf ohne Probleme wie auf einen lokal definierten Array zugegriffen werden, er behält seinen zugewiesenen Speicherplatz, bis dieser mittels delete[] explizit freigegeben wird.
Dies MUSS durch den Programmierer erfolgen, sobald er den Speicher nicht mehr benötigt.

Gefahren bestehen insbesondere darin, über einen Pointer auf eine Speicherstruktur zugreifen zu wollen, die an anderer Stelle bereits gelöscht wurde.

```C
int     *ptr1, *ptr2;

ptr1 = new int[10];     // Speicher wird reserviert
ptr2 = ptr1;            // ptr1 und ptr2 zeigen beide auf die gleiche Speicheradresse

delete[] ptr2;          // Die Speicheradresse, auf die ptr2 zeigt, wird freigegeben

ptr1[5] = 10;           // Undefiniert, da die Speicheradresse, auf die ptr1 zeigt, nicht mehr reserviert ist.
```

### Strings

C++ unterscheidet zwischen verschiedenen Arten darstellbarer Zeichen: Das Zeichen selbst, welches als chars (oder, wenn sie mehr als 1 Byte benötigen - z.B. für Emojis w_char) gespeichert werden,
CStrings, welche effektiv lediglich Arrays von chars sind, sowie Strings als Klasse. Arduino nutzt eine eigene Implementierung der String-Klasse, welche sich von der Standardlibrary unterscheidet.
Hier wird nur auf die Arduino String-Klasse eingegangen.

#### Chars

Ein char ist genau ein Byte groß und beinhaltet einen Zahlenwert, welcher ASCII-konform einen Buchstaben/ein Zeichen repräsentiert. Chars werden mit einfachen Anführungszeichen gekennzeichnet:

```C
char    buchstabe = 'A';
buchstabe == 65;

buchstbe = '1';
buchstabe == 49; 
```

Chars können in arithmetischen oder bitoperationen verwendet werden.

#### CString

Ein cstring ist nichts anderes als ein Array von chars mit einem Nullbyte als letztem Element. Sie können direkt mittels doppelten Anfürungsstrichen im Quellcode initialisiert werden, sind sodann jedoch konstant (nicht änderbar).
Bei der Nutzung von doppelten Anführungszeichen wird das Nullbyte automatisch an das Ende der Zeichenkette gesetzt.

```C
const char      *cstr = "Hallo Welt";
char            also_cstr[] = {'H', 'a', 'l', 'l', 'o', ' ', 'W', 'e', 'l', 't', '\x00'};
```

Im Beispiel zeigen die beiden Zeiger cstr und also_cstr beide auf eine Speicheradresse, in der ein 'H' liegt. Was durch den Programierer in Anführungsstrichen in den Code geschrieben wird,
wird direkt in den Speicher geladen und verbleibt dort an einer festen adresse. Dementsprechend muss es nicht freigegeben werden und kann frei zwischen verschiedenen Variablen hin- und hergereicht werden.

Auf den String kann wie auf ein Array zugegriffen werden. Da cstrings in der Handhabung etwas schwierig sind, werden in der Standardbibliothek Funktionen zur Verfügung gestellt, die das Bearbeiten der Strings vereinfachen.

##### Escape-Sequences

In cstrings lassen sich einige Sonderzeichen mittels Escape-Sequenzen einfügen. Diese werden mit einem Backslash angekündigt, gefolgt von einem Buchstaben oder einem Bytewert:

```C
"Hallo mit\nAbsatz"
"Ein String mit Nullbyte\0"
"Der Bytewert 0b00000010: \2, oder auch hexadezimal: \xaf"
```

#### String-Klasse

Die String-Klasse ist ein Wrapper um cstrings, welche die Speicherverwaltung übernimmt und eine Reihe von vereinfachenden Funktionen zur Verfügung stellt. Arduino implementiert eine eigene
Interpretation der String-Klasse, welche von der stdlib von C++ abweicht (da diese sehr viel umfangreicher aber auch komplizierter ist). Hier wird nur auf die Arduino-Version eingegangen.

```C
String      greeting = String("Hello ");
    
greeting += "World";
Serial.println(greeting);
```

Eine Übersicht über die Klasse und ihre Methoden findet sich [hier](https://www.arduino.cc/reference/en/language/variables/data-types/stringobject/)

### Wie zur Hölle lese ich Variablen?

Variablendeklarationen in C++ können sehr kryptisch werden. So ist es nicht einfach, Sinn in so etwas zu finden:

```C
const char (* const(*var[5]))[10] const;
```

Am besten liest man Deklarationen:
- Beginne mit dem Variablennamen
- Gehe so weit nach rechts wie möglich (bis eine Klammer kommt)
- Gehe sodann so weit von dem Variablennamen so weit nach links wie möglich

Damit wird das Beispiel
- Eine Variable namens var ist
- ein Array mit 5 Elementen von
- Zeigern auf
- konstante Zeiger auf
- ein Array mit 10 konstanten von
- chars, die konstant sind

So komplizierte Variablen sind aber zum Glück in Hobbyprojekten nicht oft notwendig.

## Kontrollstrukturen

### If

Die meistgenutzte und wichtigste Kontrollstruktur gibt es auch in C++:

```C
bool        is_true = true;

if(is_true && Serial)
    Serial.println("Yeah");
else if(Serial)
    Serial.println("At least I can write something");
else
    Serial.println("Nooooooo");
```

Ähnlich wie mit dem Walross-Operator in Python kan in C++ eine lokale Variable in einer If-Abfrage deklariert werden:

```C
if(int i = 10 - 5)              |   if i := 10 - 5:
    Serial.println(i);          |       println(i)
```

If kann als Ternary Operator innerhalb von Deklarationen eingesetzt werden:

```C
int     cond_var = a >= 10 ? 5 : 0;     // setzt cond_var auf 5 wenn a größer oder gleich 10 ist, ansonsten wird cond_var 0.
```

### Switch

Muss eine Variable auf verschiedene Inhalte getestet werden, kann die switch-Anweisung hilfreich sein:

```C
switch(a) {
    case 10: 
        Serial.println("It is 10");
        break;
    case 20:
        Serial.println("It is 20");
        Serial.println("Who would have thought...");
        break;
    default:
        Serial.println("It is something else entirely...");
}
```

Werden die break-Statements weggelassen, "fällt" das Programm einfach weiter durch die Switch-Anweisung, wenn es ein match gefunden hat:

```C
bool    a = true;
switch(a) {
    case true:
        Serial.println("Wahr");
    case false:
        Serial.println("Falsch");
}
```

Das Beispiel wird sowohl "Wahr" als auch "Falsch" ausgeben.

### While

While-Schleifen funktionieren wie in Python. Es gibt jedoch auch die do-while-Schleife, welche erst durchlaufen wird und am Ende eine Abbruchbedingung prüft:

```C
while(condition) {
    Serial.println("Hello");
}

do {
    Serial.println("Hello");
} while(condition);
```

Alle Arten von Schleifen können jederzeit mit break oder return beendet werden oder mit continue zum nächsten Durchlauf springen.

### For

C++ unterscheidet zwei Arten von For-Schleifen: Es gibt Zählerschleifen und Iteratorschleifen.

#### Zählerschleife

Der Aufbau der Zählerschleife ist for(Initialisierung; Laufbedingung; Operation)

```C
int     arr[10];
for(int i = 0; i <= 10; i++)
    arr[i] = i;
```

Bis auf die Laufbedingung kann fast alles weggelassen werden:

```C
for(;true;)
    Serial.println("Endlosschleife");
```

In Zählschleifen müssen nicht unbedingt Zahlen durchgezählt werden. Z.B. können auch Zeiger an einem Array entlanglaufen:

```C
const char  *cstr = "Hallo Welt";

for(const char *c = cstr; *c != '\x00'; c++)
    Serial.print(*c);
```

#### Iteratorschleife

Iteratorschleifen setzen voraus, dass ein Objekt die Iteratorbibliothek von C++11 implementiert. Dies ist in Mikrocontrollern nicht immer der Fall.

```C
// Code ist nicht Arduino-Tauglich, nutzt c++stdlib
#include <vector>

std::vector<int>    arr = {1, 2, 3, 4, 5, 6, 7};

for(auto &element: arr) {
    int i = element;
    element = 2;
    for (; i > 1; i--)
        element *= 2;
}
```

Das Beispiel füllt den dynamischen Array arr mit Zweierpotenzen zu seinem jeweiligen initialen Inhalt.

## Funktionen

Eine Funktionsdefinition ist so aufgebaut:

```C
return_type func_name(param_type param_name) {
    // Function Body
}
```

Es können beliebige Typen zurückgegeben werden, Grundtypen, Objekte oder auch Pointer. In einer Funktion angelegter Speicher MUSS jedoch auf dem Heap liegen, sofern
er als Zeiger zurückgegeben werden soll, damit er nicht am Ende der Funktion verworfen wird.

Soll die Funktion keinen Wert zurückgeben, so muss der Rückgabewert void angegeben werden. Auf eine return-Anweisung kann
sodann im Funktionskörper verzichtet werden.

### Call by Reference VS Value

Die Art und Weise, wie Funktionen Parameter übergeben, kann variieren:

Call by Value kopiert die übergebenen Parameter, sodass Änderungen der Werte in der Funktion keine Auswirkung auf die aufrufenden Parameter haben:

```C
void funny_func_value(int by_value) {
    by_value = 5;
}

int     my_int = 10;
funny_func_value(my_int);

Serial.println(my_int);         // Ausgabe: 10
```

Call by Reference übergibt das Objekt selbst, sodass Änderungen an dem Objekt sich auch in den aufrufenden Kontext übertragen. Damit lassen sich z.B. mehrere Rückgabewerte generieren
oder große Objekte direkt an einen Funktionsaufruf übergeben.

```C
void funny_func_ref(int &by_reference) {
    by_reference = 5;
}

int     my_int = 10;
funny_func_ref(my_int);

Serial.println(my_int);         // Ausgabe: 5

```

Ein ähnlicher Effekt kann auch mit Pointern erzielt werden:

```C
void funny_func_ptr(int *ptr) {
    *ptr = 5;
}

int my_int = 10;
funny_func_ptr(&my_int);

Serial.println(my_int);         // Ausgabe: 5
```

### Konstante Funktionen

Wenn sich in einer Funktion keine externe Variable verändert und keinen variablen Rückgabewert hat, kann die Funktion als "const" markiert werden, wodurch der Compiler oftmals Optimierungsmöglichkeiten erhält

```C

void nothing_changes() const {
    Serial.println("Nur das hier wird ausgegeben");
}
```

### Statische Variablen

Wenn eine Variable ihren Wert zwischen zwei Funktionsaufrufen behalten soll, so kann sie als "static" markiert werden.

```C
void static_calls() {
    static int      counter = 0;        // Initialisierung erfolgt nur beim ersten Funktionsaufruf, wird anschließend übersprungen
    
    counter++;
    Serial.print("Diese Funktion wurde ");
    Serial.print(counter);
    Serial.prinln(" Mal aufgerufen");
}
```

Statische Variablen werden vor Beginn des Programmes in den Speicher geladen und nur freigegeben, wenn das Programm endet. Zu viel Nutzung von statischen Variablen
kann zu einer schnellen Speicherfüllung führen.

### Lambda-Funktionen

Lambda-Funktionen in C++ sind möglich und hochgradig flexibel (mehr, als z.B. in Python). Sie sind syntaktisch jedoch kompliziert und in der Arbeit mit Microcontrollern
voraussichtlich nicht notwendig.

