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
    <ht2>Was ist der CYK-Algorithmus?</ht2>
    <p>
        Der Cocke-Younger-Kasami-Algorithmus, kurz CYK-Algorithmus, ist eine Bottom-up-Parsing-Technik für Grammatiken in Chomsky-Normalform.
    <br>
        Dieser Algorithmus kann verwendet werden, um zu prüfen, ob ein Wort durch eine kontextfreie Grammatik erzeugt werden kann.       
    </p>
    <br>
    <ht3>Kurze Zusammenfassung zu CFG und CNF</ht3>
    <p>
        Für die kontextfreie Grammatik (CFG) gilt Folgendes: 
    <br>
        <b>G = (V, &Sigma;, P, S)</b>, wobei
    <br> 
        <b>V</b> ist eine endliche Menge von Variablen (alternativ Nichtterminale, Nichtterminalsymbole)
    <br> 
        <b>&Sigma;</b> (mit V &cap; &Sigma;= &empty;) ist ein Alphabet von Zeichen (alternativ Terminale, Terminalsymbole)
    <br> 
        <b>P</b> ist eine endliche Menge von Produktionen von der Form l -> r, 
         wobei l &isin; (V &cap; &Sigma;= &empty;)<sup>+</sup> und r &isin; (V &cap; &Sigma;= &empty;)*
    <br> 
        <b>S</b> &isin; V ist das Startsymbol (alternativ Startvariable)
    <br>
    <br>
        Die folgende Regel gilt für die Chomsky-Normalform (CNF) mit der Grammatik G = (V, &Sigma;, P, S):
    <br>
        <b>A -> BC</b>
    <br>
        <b>A -> a</b>
    <br>
        Eine CFG G = (V, &Sigma;, P, S) mit &epsilon; &notin; L(G) ist in Chomsky-Normalform, 
        wenn für jede Produktion A -> w &isin; P gilt: w = a &isin; &Sigma; oder w = BC mit B, C &isin; V. 
    <br>
        a ist ein Terminalsymbol und A,B und C sind Nichtterminale.
    </p>
    <br>
    <ht3>CYK-Algorithmus von Endrullis</ht3>
    <p>
        Es gibt zwei verschiedene Verfahren zum Rechnen mit dem CYK-Algorithmus: Den CYK-Algorithmus von Endrullis und den Standard-CYK-Algorithmus. 
    <br>
        Werfen wir zunächst einen Blick auf den CYK-Algorithmus von Endrullis. 
    <br>
    <br>
        Sei w = abbb und G = ({S, A,B}, {a, b}, P, S) mit P = {S → AB, A → BB | a, B -&gt; AB | b}
    <br>
    <br>
    <zentriert>
        <img title="Neuer CYK-Algorithm von Dr. Jörg Endrullis" height="600" alt="Bild zur neuen Berechnung mit dem neuen CYK-Algorithmus" src="/bachelorarbeit-cyk/assets/images/new cyk algorithm.png" />
    </zentriert>
    <br>
    <p2>
        Bild 1: Berechnung mit dem neuen CYK-Algorithmus von Dr. Jörg Endrullis
    <br />
    <a href="http://joerg.endrullis.de/automata/12_parsing_cyk.pdf/print_12_parsing_cyk.pdf">(Quelle: http://joerg.endrullis.de/automata/12_parsing_cyk.pdf/print_12_parsing_cyk.pdf, Seite 4) </a>
    </p2>
    <br />
        Erklärung: Zuerst schauen wir uns an, wie viele verschiedene Teilwörter in dem Wort "abbb" vorkommen. In dem Fall haben wir "a" und "b" und beide Teilwörter sind in der Grammatik definiert. 
    <br />
    <br />
        Als nächstes teilen wir das Wort "abbb" in 2er-Paare: "ab" und "bb". Wir überprüfen, ob das Ergebnis in der Grammatik definiert ist. Für "AB" haben wir "S, B" und für "BB" haben wir "A". 
    <br />
    <br />
        Danach nehmen wir die ersten drei Buchstaben ("abb") und die letzten drei Buchstaben ("bbb"). 
    <br />
    <br />
        Für "abb" haben wir zwei mögliche Kombinationen, die wir berechnen können: "a" und "bb" , sowie "ab" und "b". Da wir schon im vorherigen Schritt berechnet haben, können wir einfach die Wörter kombinieren: "a" und "bb" ergibt "AA" und "ab" und "b" haben wir "SB, BB". Wir haben dann "BB", die in der Grammatik definiert wurde (A -&gt; BB).
    <br />
    <br />
        Bei "bbb" wird ebenfalls genauso berechnet wie "abb" 
    <br />
    <br />
        Zum Schluss haben wir das Wort "abbb". Da haben wir drei verschiedene Kombinationsmöglichkeiten: "a" und "bbb", "ab" und "bb", "abb" und "b". 
    <br />
        Für "a" und "bbb": "AS, AB"
    <br />
        Für "ab" und "bb": "SA, BA"
    <br />
        Für "abb" und "b": "AB"
    <br />
    <br />
        Da "AB" in der Grammatik schon definiert ist (S und B) sind wir mit der Berechnung fertig, weil zum Schluss ein "S" steht.  
    <br />
        Zur Überprüfung könnt ihr mal ableiten, um zu schauen, ob ihr bei der Berechnung mit dem neuen CYK-Algorithmus einen Fehler gemacht habt (siehe letzte Zeile im Bild 1).
    </p>
    <br>
    <ht4>Unterschied zwischen dem Standard-CYK-Algorithmus und dem CYK-Algorithmus von Endrullis</ht4>
    <p>
        Der CYK-Algorithmus von Endrullis ist nicht der Standardalgorithmus. Der Unterschied zwischen den beiden besteht in der Darstellung und dem Berechnungsweg beim CYK-Algorithmus. 
    <br>
    <br>
        Beim <b>CYK-Algorithmus von Endrullis</b> wird das Wort in Teilwörter zerlegt und diese werden dann in aufsteigenden Wortpaaren berechnet. Zuerst wird ein Teilwort berechnet, dann in Paaren von 2 usw. 
    <br>
    <br>
        Beim <b>Standard-CYK-Algorithmus</b> wird die Berechnung anhand einer V(i,j)-Tabelle durchgeführt. Nehmen wir das gleiche Beispiel von oben.
    <br>
    <br>
        Zuerst schreibt man das Wort auf. Danach zählt man, wie viele Buchstaben das Wort hat (in diesem Fall sind es 4, also kann man eine Tabelle mit i = j = 4 erstellen)
    <br />
        Überprüfen wir dann, ob "a" und "b" in der Grammatik definiert wurden. Also trägt man folgendes V(i,1)-Einträge in die Tabelle ein: 
    <br />
    <br />
    <list>
        V(1,1)= A
        <br />
        V(2,1) = B
        <br />
        V(3,1) = B
        <br />
        V(4,1) = B
    </list>
    <br />
    <br />
        Als nächstes sehen wir uns die zweite Zeile an. Hier muss man darauf achten, dass die nächsten Schritte mit der 3-fach-Schleife berechnet werden:
    <br />
    <br />
    <list>
        V(1,2): j=2, i=1, k=1: V(1,2) = V(1,2) &cup; {A | A -&gt; BC, B &isin; V(1,1), C &isin; V(2,1)}
        <br />
        V(2,2): j=2, i=2, k=1: V(2,2) = V(2,2) &cup; {A | A -&gt; BC, B &isin; V(2,1), C &isin; V(3,1)}
        <br />
        V(3,2): j=2, i=3, k=1: V(3,2) = V(3,2) &cup; {A | A -&gt; BC, B &isin; V(3,1), C &isin; V(4,1)}
    </list>
    <br />
    <br />
        Erläuterung: Für V(1,2) prüfen wir, ob das Teilwort „AB“, das aus V(1,1) und V(2,1) zusammengesetzt ist, in der Grammatik definiert ist. Da „AB“ sowohl in „S“ als auch in „B“ vorkommt, schreiben wir S,B für V(2,1). Und für V(2,2), im Beispiel auch V(3,2), betrachten wir das Teilwort „BB“. In der Grammatik ist „A -&gt; BB“ definiert, also schreiben wir „A“ für V(2,2) und für V(3,2). 
    <br />
    <br />
        Dann schauen wir uns die dritte Zeile an. Sie haben sich vielleicht gefragt, warum in der vorherigen Berechnung k verwendet wurde. Der Grund ist, dass es verschiedene Möglichkeiten gibt, das Teilwort zu bestimmen, je weiter man Zeile für Zeile rechnet:
    <br />
    <br />
    <list>
        V(1,3): j=3, i=1, k=1: V(1,3) = V(1,3) &cup; {A | A -&gt; BC, B &isin; V(1,1), C &isin; V(2,2)}
        <br />
        V(1,3): j=3, i=1, k=2: V(1,3) = V(1,3) &cup; {A | A -&gt; BC, B &isin; V(1,2), C &isin; V(3,1)}
    </list>
    <br />
    <list>
        V(2,3): j=3, i=2, k=1: V(2,3) = V(2,3) &cup; {A | A -&gt; BC, B &isin; V(2,1), C &isin; V(3,2)}
        <br />
        V(2,3): j=3, i=2, k=2: V(2,3) = V(2,3) &cup; {A | A -&gt; BC, B &isin; V(2,2), C &isin; V(4,1)}
    </list>
    <br />
    <br />
        Erläuterung: In der dritten Zeile ist zu beachten, dass es zwei mögliche Teilwörter für V(1,3) und V(2,3) gibt, die in der Grammatik definiert sind. 
    <br />
        In V(1,3) haben wir zwei Teilwörter: „AA“ (aus V(1,1) und V(2,2)) und ‚SB, BB‘ (aus V(1,2) und V(3,1)). „AA“ und ‚SB‘ sind in der Grammatik nicht definiert. Stattdessen haben wir das Teilwort „BB“, das in der Grammatik als „A“ markiert ist.
        Und in V(2,3) haben wir auch zwei Teilwörter: „BA“ (aus V(2,1) und V(3,2)) und ‚AB‘ (aus V(2,2) und V(4,1)). BA“ ist nicht in der Grammatik enthalten, aber für ‚AB‘ haben wir ‚S‘ und ‚B‘.
    <br />
    <br />
        Betrachten wir schließlich die letzte Zeile. Hier gibt es drei verschiedene Unterwörter, die in der Grammatik enthalten sind:
    <br />
    <br />
    <list>
        V(1,4): j=4, i=1, k=1: V(1,4) = V(1,4) &cup; {A | A -&gt; BC, B &isin; V(1,1), C &isin; V(2,3)}
        <br />
        V(1,4): j=4, i=1, k=2: V(1,4) = V(1,4) &cup; {A | A -&gt; BC, B &isin; V(1,2), C &isin; V(3,2)}
        <br />
        V(1,4): j=4, i=1, k=3: V(1,4) = V(1,4) &cup; {A | A -&gt; BC, B &isin; V(1,3), C &isin; V(4,1)}
    </list>
    <br />
    <br />
        Erläuterung: Wie bereits erwähnt, haben wir hier drei verschiedene Teilwörter: „AS, AB“ (aus V(1,1) und V(2,3)), ‚SA, BA‘ (aus V(1,2) und V(3,2)) und ‚AB‘ (aus V(1,3) und V(4,1)). 
    <br />
        Wir prüfen, ob wir die Teilwörter in der Grammatik gefunden haben. Und das ist nur bei „AB“ der Fall. Also schreiben wir „S,B“ für V(1,4). 
    <br />
        Damit ist die Berechnung abgeschlossen. Nun prüfen wir, ob das Wort „abbb“ in der Sprache vorkommt. Wichtig ist, dass „S“ am Ende steht, im Beispiel bei V(1,4), was hier der Fall ist.
    <br />
        Wir können also die Antwort schreiben:
    <br />
    <br />
    <list>
        Da S &cup; V(1,4) gilt w &cup; L(G).
    </list>
    <br />
    <br />
    <zentriert>
        <img title="Standard-CYK-Algorithmus" height="600" alt="Bild zur Berechnung mit dem Standard-CYK-Algorithmus" src="/bachelorarbeit-cyk/assets/images/old cyk algorithm.png" />
        </zentriert>
    <p2>
        Bild 2: Berechnung mit dem alten CYK-Algorithmus
    </p2>
    <br>
    <br>
        Wie wir sehen können, hat der Standard-CYK-Algorithmus eine einfache und klare Darstellung. 
    <br>
        Das Problem ist, dass die Berechnung längerer Wörter sehr zeitaufwändig ist.
    <br>
        Daher ist der CYK-Algorithmus eine Alternative, da das Endergebnis in nur wenigen Schritten erzielt werden kann.
    </p>
</div>

