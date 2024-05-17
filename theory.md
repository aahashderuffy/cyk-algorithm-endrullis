---
layout: default
permalink: /theory/
---

<style>
ht2{
    font-size: 25px;
    text-decoration: underline;

}

ht3{
    font-size: 25px;
    text-decoration: underline;
}

ht4{
    font-size: 25px;
    text-decoration: underline;
}

zentriert{
    display: flex;
    justify-content: center;
    align-items: center;
}

p2{
    display: flex;
    justify-content: center;
    align-items: center;  
}

list{
    display: flex;
    justify-content: center;
    align-items: center;  
}

p{
    font-size: 20px;
}
</style>

<div style="padding: 20px;">
    <h1>Cocke-Younger-Kasami-Algorithmus - Theorie und Beispiel</h1>
    <ht2> Was ist das CYK-Algorithmus?</ht2>
    <p>
        Das Cocke-Younger-Kasami Algorithmus, in Kurzform CYK-Algorithmus, ist ein Bottom-Up Parsing Technik für die Grammatik in Chomsky Normalform.
    <br>
        Mit diesem Algorithmus kann man überprüfen, ob ein Wort von einer kontextfreien Grammatik erzeugt werden kann. 
    </p>
    <ht3> CYK-Algorithmus von Cocke, Younger und Kasami (aktueller Algorithmus)</ht3>
    <p>
        Die aktuelle Berechnung mit dem CYK-Algorithmus folgt über einem V(i,j)-Tabelle (i geht in die waagrechte Richtung nach rechts und j nach senkrechte Richtung nach unten). 
    <br>
        Nun wird ein Beispiel vorgestellt für die Berechnung (siehe auch Bild 1):
    <br>
    <br>
    <list>
        Sei w = abbb und G = ({S, A,B}, {a, b}, P, S) mit P = {S → AB, A → BB | a, B -> AB | b}
    </list>
    <br>
        Zuerst schreibt man das Wort auf, danach zählt man auf, wie viele Buchstaben das Wort hat (in dem Fall sind es 4, also kann man eine Tabelle erstellen mit i = j = 4)
    <br>
        Danach schaut man, ob "a" und "b" in der Grammatik definiert wurde. Also füllt man folgendes V(i,1)-Einträge in der Tabelle ein: 
    <br>
    <br>
    <list>
        V(1,1)= A
        <br>
        V(2,1) = B
        <br>
        V(3,1) = B
        <br>
        V(4,1) = B
    </list>
    <br>
    <br>
        Als nächstes schauen wir die zweite Zeile an. Hier muss man achten, dass für die nächsten Schritte mit der 3-fache Schleife berechnet wird:
    <br>
    <br>
    <list>
        V(1,2): j=2, i=1, k=1: V(1,2) = V(1,2) &cup; {A | A -> BC, B &isin; V(1,1), C &isin; V(2,1)}
        <br>
        V(2,2): j=2, i=2, k=1: V(2,2) = V(2,2) &cup; {A | A -> BC, B &isin; V(2,1), C &isin; V(3,1)}
        <br>
        V(3,2): j=2, i=3, k=1: V(3,2) = V(3,2) &cup; {A | A -> BC, B &isin; V(3,1), C &isin; V(4,1)}
    </list>
    <br>
    <br>
        Erklärung: Bei V(1,2) schauen wir, ob das Teilwort "AB", dass aus V(1,1) und V(2,1) kombiniert wird, in der Grammatik definiert ist. Da "AB" sowohl in "S", als auch in "B" vorkommt, schreiben wir bei V(2,1) S,B. Und bei V(2,2), in dem Beispiel auch V(3,2) schauen wir das Teilwort "BB" an. In der Grammatik ist "A -> BB" definiert, also schreiben wir bei V(2,2) und bei B(3,2) "A". 
    <br>
    <br>
        Danach schauen wir uns die dritte Zeile an. Vielleicht habt ihr die Frage gestellt, warum k beim vorherige Berechnung verwendet wurde. Der Grund ist, dass es verschiedene Möglichkeiten für die Bestimmung des Teilwortes gibt, je weiter man Zeile für Zeile berechnet:
    <br>
    <br>
    <list>
        V(1,3): j=3, i=1, k=1: V(1,3) = V(1,3) &cup; {A | A -> BC, B &isin; V(1,1), C &isin; V(2,2)}
        <br>
        V(1,3): j=3, i=1, k=2: V(1,3) = V(1,3) &cup; {A | A -> BC, B &isin; V(1,2), C &isin; V(3,1)}
    </list>
    <br>
    <list>
        V(2,3): j=3, i=2, k=1: V(2,3) = V(2,3) &cup; {A | A -> BC, B &isin; V(2,1), C &isin; V(3,2)}
        <br>
        V(2,3): j=3, i=2, k=2: V(2,3) = V(2,3) &cup; {A | A -> BC, B &isin; V(2,2), C &isin; V(4,1)}
    </list>
    <br>
    <br>
        Erklärung: In der dritte Zeile müssen wir achten, dass es für V(1,3) und V(2,3) zwei mögliche Teilwörter gibt, die in der Grammatik definiert sind. 
    <br>
        Bei V(1,3) haben wir zwei Teilwörter: "AA" (aus V(1,1) und V(2,2)) und "SB, BB" (aus V(1,2) und V(3,1)). "AA" und "SB" sind in der Grammatik nicht definiert. Dafür haben wir das Teilwort "BB", das in der Grammatik als "A" gekennzeichnet wird.
        Und bei V(2,3) haben wir ebenfalls zwei Teilwörter: "BA" (aus V(2,1) und V(3,2)) und "AB" (aus V(2,2) und V(4,1)). "BA" ist in der Grammatik nicht enthalten, aber für "AB" haben wir "S" und "B".
    <br>
    <br>
        Zum Schluss schauen wir und die letzte Zeile an. Hier gibt es drei verschiedene Teilwörter, die in der Grammatik enthalten sind.:
    <br>
    <br>
    <list>
        V(1,4): j=4, i=1, k=1: V(1,4) = V(1,4) &cup; {A | A -> BC, B &isin; V(1,1), C &isin; V(2,3)}
        <br>
        V(1,4): j=4, i=1, k=2: V(1,4) = V(1,4) &cup; {A | A -> BC, B &isin; V(1,2), C &isin; V(3,2)}
        <br>
        V(1,4): j=4, i=1, k=3: V(1,4) = V(1,4) &cup; {A | A -> BC, B &isin; V(1,3), C &isin; V(4,1)}
    </list>
    <br>
    <br>
        Erklärung: Wie bereits erwähnt, haben wir hier drei verschiedene Teilwörter: "AS, AB" (aus V(1,1) und V(2,3)), "SA, BA" (aus V(1,2) und V(3,2)) und "AB" (aus V(1,3) und V(4,1)). 
    <br>
        Wir schauen, ob wir die gefundene Teilwörter in der Grammatik liegt. Und das ist nur bei "AB" der Fall. Also schreiben wir bei V(1,4) "S,B". 
    <br>
        So sind wir mit der Berechnung fertig. Nun überprüfen wir, ob das Wort "abbb" in der Sprache liegt. Wichtig ist, dass am Ende, in dem Beispiel bei V(1,4) "S" vorkommt, was wir hier haben.
    <br>
        Also können wir die Antwort schreiben:
    <br>
    <br>
    <list>
        Da S &cup; V(1,4) gilt w &cup; L(G).
    </list>
    <br>
    <br>
    <zentriert>
        <img title="Neuer CYK-Algorithm von Dr. Jörg Endrullis" height="600" alt="Bild zur neuen Berechnung mit dem neuen CYK-Algorithmus" src="{{ site.baseurl }}/assets/images/old cyk algorithm.png">
        </zentriert>
    <p2>
        Bild 1: Berechnung mit dem alten CYK-Algorithmus
    </p2>
    </p>
    <ht4> CYK-Algorithmus von Jörg Endrullis (neuer Algorithmus)</ht4>
    <p>
        In dem vorherigen ist die Berechnung sehr einfach und schnell. Aber bei größere Wörter wird es dann sehr zeitaufwendig, da man mehrere Schritte benötigt und die Wahrscheinlichkeit ist sehr hoch, dass man sich dazwischen irgendwo verrechnet, sodass die Ergebnis beeinflusst wird. 
    <br>
        Daher wurde eine neue Berechnung des CYK-Algorithmus vorgestellt, die von Dr. Jörg Endrullis von der Vrije Universiteit Amsterdam entwickelt wurde.
    <br>
        Nehmen wir diesselbe Beispiel von oben. Nun sieht die Berechnung mit dem neuen CYK-Algorithmus so aus:
    <br>
    <br>
    <zentriert>
        <img title="Neuer CYK-Algorithm von Dr. Jörg Endrullis" height="600" alt="Bild zur neuen Berechnung mit dem neuen CYK-Algorithmus" src="{{ site.baseurl }}/assets/images/new cyk algorithm.png">
    </zentriert>
    <p2>
        Bild 2: Berechnung mit dem neuen CYK-Algorithmus von Dr. Jörg Endrullis
    <br>
    <a href="http://joerg.endrullis.de/automata/12_parsing_cyk.pdf/print_12_parsing_cyk.pdf">(Quelle: http://joerg.endrullis.de/automata/12_parsing_cyk.pdf/print_12_parsing_cyk.pdf, Seite 4) </a>
    </p2>
    <br>
        Erklärung: Zuerst schauen wir uns an, wie viele verschiedene Teilwörter in dem Wort "abbb" vorkommen. In dem Fall haben wir "a" und "b" und beide Teilwörter sind in der Grammatik definiert. 
    <br>
    <br>
        Als nächstes teilen wir das Wort "abbb" in 2er-Paare: "ab" und "bb". Wir überprüfen, ob das Ergebnis in der Grammatik definiert ist. Für "AB" haben wir "S, B" und für "BB" haben wir "A". 
    <br>
    <br>
        Danach nehmen wir die ersten drei Buchstaben ("abb") und die letzten drei Buchstaben ("bbb"). 
    <br>
    <br>
        Für "abb" haben wir zwei mögliche Kombinationen, die wir berechnen können: "a" und "bb" , sowie "ab" und "b". Da wir schon im vorherigen Schritt berechnet haben, können wir einfach die Wörter kombinieren: "a" und "bb" ergibt "AA" und "ab" und "b" haben wir "SB, BB". Wir haben dann "BB", die in der Grammatik definiert wurde (A -> BB).
    <br>
    <br>
        Bei "bbb" wird ebenfalls genauso berechnet wie "abb" 
    <br>
    <br>
        Zum Schluss haben wir das Wort "abbb". Da haben wir drei verschiedene Kombinationsmöglichkeiten: "a" und "bbb", "ab" und "bb", "abb" und "b". 
    <br>
        Für "a" und "bbb": "AS, AB"
    <br>
        Für "ab" und "bb": "SA, BA"
    <br>
        Für "abb" und "b": "AB"
    <br>
    <br>
        Da "AB" in der Grammatik schon definiert ist (S und B) sind wir mit der Berechnung fertig, weil zum Schluss ein "S" steht.  
    <br>
        Zur Überprüfung könnt ihr mal ableiten, um zu schauen, ob ihr bei der Berechnung mit dem neuen CYK-Algorithmus einen Fehler gemacht habt (siehe letzte Zeile im Bild 2).
    </p>
</div>

