# Datenvisualisierung

## Abgrenzung zwischen Infografik und Visualisierung

Informative Grafiken können in zwei Dimensionen eingeordnet werden.

1. **Erklärend – Explorativ**
   
   Erklärende Grafiken erklären dem Leser bekannte Fakten. Typische Beispiele hierfür sind z. B. in naturwissenschaftlichen Schulbüchern zu finden. Explorative Grafiken sind ein Werkzeug, um interessante Fakten in einem Datensatz zu finden. Während erklärende Grafiken eine Geschichte erzählen, erlauben es explorative Grafiken, eigene Geschichten in den Daten zu finden.
   
2. **Konzeptuell – Messbar**
   
   Diese Dimension ordnet Grafiken danach, ob die dargestellten Informationen messbar sind bzw. ob der Grafik ein Datensatz zu grundeliegt.
   
Erklärende Grafiken sind häufig auch konzeptueller Natur, explorative Grafiken stellen tendenziell eher messbare Informationen dar.

Die Begriffe **Infografik**  und **Visualisierung** werden nicht einheitlich verwendet. Häufig werden mit dem Begriff **Infografik** jedoch erklärende Grafiken und mit **Visualisierung** Grafiken mit messbaren Informationen bezeichnet.

* Koponen and Hildén, Data Visualization Handbook, pp. 25ff

## Visuelle Wahrnehmung des Menschen

Der Prozess des visuellen Wahrnehmung kann in drei Stufen beschrieben werden:

1. In der ersten Stufe werden einfache Merkmale wahrgenommen. Zu diesen Merkmalen gehören die Form (Position, Größe, Länge, Ausrichtung und Textur), Farbe (Farbton, Sättigung, Helligkeit) sowie Bewegung. Die Verarbeitung in dieser Stufe erfolgt *präattentiv* im Unterbewusstsein; die Informationen werden besonders schnell wahrgenommen.

2. In der zweiten Stufe teilt das Gehirn das Sichtfeld in mehrere Regionen auf und erkennt einfache Muster.

3. In der dritten Stufe werden die zuvor identifizierten Merkmale zu komplexeren Objekten kombiniert und im Arbeitsgedächtnis gespeichert. Diese Objekte werden dann mit im Gedächtnis gespeicherten Informationen verglichen. Die Anzahl an Informationen, die im Arbeitsgedächtnis gespeichert werden können, ist begrenzt. Dadurch ist die Verarbeitung in der dritten Stufe langsamer und anstregender.

**Bei der Gestaltung von Grafiken sollte die Funktionsweise der visuellen Wahrnehmung berücksichtigt werden. Relevante Informationen sollten so dargestellt werden, dass sie in den ersten beiden Wahrnehmungsstufen wahrgehommen werden können.** Einzelne Objekte können am einfachsten wahrgenommen werden, wenn sie durch eines der Merkmale (Form, Farbe, Bewegung) unterscheiden. Ein rotes Rechteck kann leicht unter roten (oder noch einfacher, unter blauen) Kreisen identifiziert werden. Aufwändiger ist es allerdings, wenn sich das gesuchte Objekt nur in der Kombination aus mehreren Merkmalen vom Rest unterscheidet: Ein rotes Rechteck unter blauen Rechtecken und roten Kreisen ist schwieriger zu finden. Es reicht nicht mehr, nur nach Form oder Farbe zu suchen.

* Koponen und Hildén, Data Visualization Handbook, pp. 49ff

## Visuelle Variablen

Der Begriff wurde durch den französischen Kartographen Jacques Bertin geprägt. Er beschreibt visuelle Merkmale (wie z. B. Position, Fläche, Helligkeit, …), durch die Daten repräsentiert werden können. Je nach Art der Daten eignen sich bestimmte visuelle Variablen besser oder schlechter. In der Regel wird die selbe Information redundant, also durch mehrere Merkmal kodiert. In Balkendiagrammen wird ein Wert zwar primär durch die Position des Balkenendes, aber sekundär auch durch Fläche und Länge des Balkens kodiert.

Unabhängig von der Art der Daten eignet sich grundsätzlich die Position am besten zur visuellen Kodierung von Daten, da der Mensch in der Lage ist, selbst sehr kleine Positionsunterschiede präzise zu unterscheiden. Durch die Abbildung einer Skala können Positionen selbst über mehrere Grafiken hinweg leicht verglichen werden.

### Numerische Werte
können auf einer Intervall- oder Verhältnisskala dargestellt werden. Werte auf einer Intervall-Skala sind geordnet und der Abstand zwischen den einzelnen Werten ist bekannt. Ein typisches Beispiel hierfür ist z. B. die Temperatur. Werte auf eine Verhältnisskala haben zusätzlich einen absoluten Nullpunkt, z. B. in Meter gemessene Längen (oder aber Temperatur in Kelvin).

Geeignete visuelle Variablen: Position, Länge, (Fläche)

### Ordinalwerte
sind ebenfalls geordnet. Die Abstände der Werte zueinander sind aber nicht bekannt. Ein Beispiel hierfür ist z. B. Zustimmung auf einer Likert-Skala (stimme voll zu, stimme zu, unentschieden, …).

Geeignete visuelle Variablen: Position, Helligkeit

### Kategorien
können auf einer Nominalskala dargestellt werden. Die einzelnen Werte können zwar unterschieden werden, sie haben aber keine natürliche Rangfolge.

Geeignete visuelle Variablen: Position, Form, Farbton

**Eine effektive Visualisierung sollte die wichtigsten Daten mithilfe der am besten geeigneten visuellen Variable kodieren. Weniger gut geeignete Variablen sollten lediglich für die redundaten Kodierung verwendet werden.**

* Koponen und Hildén, Data Visualization Handbook, pp. 58ff

## Grundsätzliches

1. Visualisierungen erlauben den Vergleich zwischen verschiedenen Werten. In der Folge werden Unterschiede in der Darstellung immer als Unterschiede in den zugrundeliegenden Daten interpretiert. Unterschiede in der Darstellung aus rein ästhetischen Gründen sollten deshalb vermieden werden, da sie in der Regel irreführend sind. So sollten beispielsweise Datenpunkte eines Diagramms nur dann mit Linien verbunden werden, wenn es sich auch tatsächlich um kontinuierliche Daten handelt.

2. Mehrere Grafiken, die die gleiche Art von Daten darstellen, sollten die gleich gestaltet sein und die gleiche Skala verwenden. Wird z. B. mithilfe von zwei Balkendiagrammen das Wachstum des BIP zweier Länder dargestellt, so sollten beide Diagramme die gleiche Achsenskalierung verwenden.

3. Sofern kontinuierliche Daten klassifiziert werden müssen, bieten sich hierfür Quantile an. Gleichmäßige Intervalle sind meist schlecht geeignet, da in den extremen Intervallen häufig nur sehr wenige Werte liegen.

4. Anstatt mehrere Datensätze in einer einzelnen Grafik darzustellen, kann es häufig sinnvoll sein, die Datensätze in mehreren einzelnen, vergleichbaren Grafiken darzustellen. 

5. Mehrere Grafiken, die die gleiche Art von Daten darstellen, sollten die gleich gestaltet sein und die gleiche Skala verwenden. Wird z. B. mithilfe von zwei Balkendiagrammen das Wachstum des BIP zweier Länder dargestellt, so sollten beide Diagramme die gleiche Achsenskalierung verwenden. Skalen etc. sollten dabei so gewählt werden, dass die einzelnen Grafiken konsistent sind.

6. Dekorative Elemente und ästhetisch ansprechende Visualisierungen können dem Verständnis zusträglich sein. Im Vergleich zu einfachen, minimalistischen Visualisierungen ist die Gefahr, eine irreführende Darstellung zu wählen, jedoch deutlich höher. Deshalb ist es im Regelfall ratsam, auf dekorative Elemente zu verzichten.

7. Da die dreidimensionale Wahrnehmung des Menschen eingeschränkt ist, sollten dreidimensionale Visualisierungen grundsätzlich vermieden werden – insbesondere dann wenn die 3D-Darstellung keinen Mehrwert gegenüber einer anderen Darstellung bietet.

## Barrierefreiheit von Farbpaletten

Mit dem [Coblis Simulator](https://www.color-blindness.com/coblis-color-blindness-simulator/) lassen sich die meisten Farbwahrnehmungsstörungen simulieren, um so zu validieren, ob gewählte Farbpaletten zugänglich sind.
