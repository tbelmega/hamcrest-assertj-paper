# Hamcrest API und Testframework AssertJ

Hamcrest und AssertJ sind Klassenbibliotheken, die zur Verbesserung der Lesbarkeit und Ausdrucksstärke von Testklassen verwendet werden können. Bei beiden handelt es sich nicht um eigenständige Testing Frameworks, sondern sie stellen Ergänzungen zu JUnit und TestNG dar. Die Bibliotheken sind mit allen JVM-Sprachen kompatibel, die JUnit unterstützen, darunter Java, Kotlin, Scala und Groovy.

### Assertions

Das wesentliche Element von Softwaretests ist der Vergleich des erwarteten Verhaltens mit dem beobachteten Verhalten des Programms. In automatisierten Tests erfolgt dieser Vergleich über sogenannte Assertions (dt. Zusicherungen): Der Assertion wird ein Wert übergeben, der aus der Programmausführung resultiert; stimmt der Wert mit der Erwartung überein, so tut das Assertion-Statement nichts, andernfalls löst es eine Exception (dt. Ausnahme) aus, die dem Programmierer als Fehlschlag des Tests angezeigt wird.

JUnit und TestNG enthalten von sich aus eine kleine Anzahl grundlegender Assertions: assertEquals vergleicht den eingegebenen zu prüfenden Wert mit dem ebenfalls eingegebenen erwarteten Wert. assertTrue, assertFalse und assertNull vergleichen den eingegebenen Wert mit True, False oder Null. assertSame überprüft die beiden Eingabewerte auf referentielle Identität, stellt also fest ob es sich um zwei Referenzen auf dasselbe Objekt handelt, und nicht nur um ein gleiches Objekt im Sinne der jeweiligen equals-Implementierung.

Um komplexere Bedingungen prüfen zu können, z.B. ob der beobachtete Wert einen erwarteten Substring enthält, muss der benötigte Vergleich programmatisch durchgeführt und das Ergebnis des Vergleichs über assertTrue bzw. assertFalse zugesichert werden. Auf diese Weise lässt sich jede erwünschte Bedingung prüfen; daraus folgt, dass Hamcrest und AssertJ nicht die Mächtigkeit der JUnit-Assertions erhöhen, sondern nur die Usability, wie im Verlauf der vorliegenden Arbeit gezeigt werden soll.

### Hamcrest

Die Hamcrest-Bibliothek ist seit 2007 im öffentlichen Maven Repository [https://mvnrepository.com/artifact/org.hamcrest/hamcrest-core] verfügbar. Sie wurde 2012 als Bestandteil in JUnit 4.4 aufgenommen; JUnit 5 enthält Hamcrest nicht mehr, weist aber auf die Möglichkeit der Verwendung von "Third-party Assertion Libraries such as AssertJ, Hamcrest, Truth, etc." [https://junit.org/junit5/docs/5.0.0/user-guide/#writing-tests-assertions-third-party] ausdrücklich hin.
Der Quellcode vom Harmcrest wurde 2012 auf GitHub veröffentlicht [https://github.com/hamcrest/JavaHamcrest] und wurde seitdem lange nicht mehr gewartet. Erst 2018 wurde die Wartung des Projekts wiederaufgenommen und mit einigen Bugfixes und technischen Neuerungen als Version 2.0 released.

Hamcrest enthält eine einzige zusätzliche Assertion-Methode: assertThat. Diese Methode nimmt neben dem zu prüfenden Wert einen Matcher entgegen (to match dt. entsprechen, passen, übereinstimmer). Matcher machen den größten Teil der Hamcrest-Bibliothek aus; es handelt sich um Methoden, mit denen der Programmierer die beschriebenen komplexeren Erwartungen formulieren kann. Bei der Testausführung überprüft assertThat also ob der übergebene Wert den durch den Matcher formulierten Kriterien genügt und wirft andernfalls eine Exception.


### AssertJ

AssertJ wurde 2013 auf GitHub und Maven Repository veröffentlicht und wurde seitdem durchgehend gewartet und monatlich released [https://mvnrepository.com/artifact/org.assertj/assertj-core, https://github.com/joel-costigliola/assertj-core/releases]. AssertJ war niemals Bestandteil von JUnit, konnte aber aufgrund der mittlerweile weit verbreiteten Dependency-Management-Lösungen wie Maven, Ivy und Gradle leicht in Java-Projekte eingebunden werden. 
Laut Google Trends wird nach "AssertJ" in Deutschland inzwischen häufiger gesucht als nach "Hamcrest", weltweit liegt Hamcrest mit schwindendem Abstand vorne [https://trends.google.de/trends/explore?date=all&q=hamcrest,assertj]. (An dieser Stelle sei darauf hingewiesen, dass die Häufigkeit als Suchbegriff nicht mit der Verwendung in Softwareprojekten korrelieren muss. So kann eine Bibliothek bei gleicher Nutzung dennoch häufiger Suchbegriff sein, weil sie schwieriger zu verstehen ist; oder die ältere Bibliothek wird wegen ihrer Verwendung in Legacy-Projekten gesucht, während neue Projekte häufiger die jüngere Bibliothek verwenden.) 

Aus Nutzersicht bringt auch AssertJ nur eine einzelne Assertion-Methode namens assertThat mit; technisch handelt es sich um eine Reihe überladener Methoden mit unterschiedlicher Signatur. Der Typ des Eingabewerts bestimmt also, welche der gleichnamigen assertThat-Methoden tatsächlich aufgerufen wird. Dieses assertThat nimmt nur den zu prüfenden Wert entgegen, keinen Vergleichswert. Dafür gibt der Aufruf von assertThat ein Assertion-Object zurück, auf dem dann eine weitere Methode zum Spezifizieren der Erwartung aufgerufen werden kann, die dann wiederum ein Assertion-Objekt zurückgibt. Auf diese Weise lassen sich mehrere Erwartungen aneinanderhängen, die man flüssig hintereinanderweg lesen kann. (Dieser Programmierstil "Fluent Interface" [https://www.martinfowler.com/bliki/FluentInterface.html] ist in moderneren Frameworks weit verbreitet und soll eine Domain Specific Language bilden, die intuitiv zu lesen und zu schreiben ist. Die Vor- und Nachteile dieses Stils werden in einem folgenden Kapitel bewertet.)
Wie Hamcrest dient AssertJ dem nutzerfreundlichen Formulieren komplexerer Erwartungen für Assertions. In diesem Fall werden keine Matcher-Objekte erzeugt, sondern Methoden auf den typspezifischen Assertion-Objekten aufgerufen.



## Motivation: Software Testing

Um die beiden vorgestellten Bibliotheken im wissenschaftlichen Sinne vergleichen zu können, ist es erforderlich zunächst Vergleichskriterien zu entwickeln. Zu diesem Zweck beginnt das folgende Kapitel mit der Motivation für Software Testing, um daraus abzuleiten unter welchen Gesichtspunkten Hamcrest und AssertJ zu vergleichen sind.

Eine vielzitierte Aussage zu Software Testing stammt von Edsger W. Dijkstra: "Testing shows the presence of bugs, not the absence." [http://homepages.cs.ncl.ac.uk/brian.randell/NATO/nato1969.PDF] (dt. sinngemäß: Testen kann nur das Vorhandensein von Fehlern zeigen, aber nicht die Abwesenheit von Fehlern beweisen.) 
Diese Feststellung ist zwar wahr und im Kontext der zitierten Diskussion über formale Programmkorrekheit von 1969 durchaus angebracht; Schlussfolgerungen für die Praxis, insbesondere unter Berücksichtigung heutige verbreiteter Methoden und Technologien, dürfen aber nur äußerst vorsichtig gezogen werden. Die Aussage unterstellt, die Intention von Software Testing wäre der Beweis der Fehlerlosigkeit eines Programms, und weist darauf hin, dass dieses Ziel nicht erreicht werden kann. Tatsächlich sind automatisierte Tests aber ein wesentlicher Bestandteil jedes modernen Softwareentwicklungsprojekts (und formaler Verifikation nicht, aber das soll im Rahmen einer gesonderten Arbeit erläutert werden), weil Testing eben nicht auf formale Korrektheit abzielt, sondern andere Aufgaben erfüllt.
Eine der Aufgaben, die Testing heute erfüllt, nennt Charles. A. R. Hoare bereits in derselben Diskussion: "You should convince yourself, or other people, [...] it will always work on any data." Die Beobachtung, dass ein Programm für eine Reihe sorgfältig ausgewählte Eingaben (Testfälle) reproduzierbar die erwarteten Ausgaben erzeugt, soll dem Entwickler das Zutrauen geben, dass das Programm immer und mit beliebigen Eingaben funktioniert. 





Testen kann nur vorhandene Fehler finden, aber nicht die Abwesenheit von Fehlern beweisen

Testen ist eine Aktivität mit Kosten und Nutzen. Testen lohnt sich, wann immer der Nutzen die Kosten übersteigt.


=> Vergleich von Harmcrest und AssertJ muss auf Kosten/Nutzenanalyse aufbauen

Kosten/Nutzen im Lebenszyklus automatischer Tests ermitteln

* Beim Schreiben der Tests (Test first/Test after)
* Beim Refactoring des Produktivcodes
* Beim Ändern des Produktivcodes
* Beim Fehlschlagen eines zuvor erfolgreichen Tests (Regression)
* Beim Release

Schreiben der Tests: Content-Assist, Lernaufwand, intuitive Benutzung, Routine. Nutzen: Fehler im Produktivcode entdecken und beheben.
Refactoring: Nutzen, da Tests zeigen dass Verhalten nicht geändert wurde
Ändern: Höherer Aufwand, je mehr Tests und mehr detaillierte Tests. Lesbarkeit/Wartbarkeit erwünscht
Regression: Nutzen, Absicherung gegen versehentliches einfügen von Fehlern. Aufwand, Verständlichkeit der Fehlermeldung und des Tests
Release: Confidence 
