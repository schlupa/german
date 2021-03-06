# Speicherklassen

D besitzt eine statische Typisierung. Wenn eine Variable deklariert wurde, kann der
Typ nicht mehr verändert werden. Dies erlaubt dem Kompiler viele Fehler früh
zu erkennen und Restriktionen können schon während der Kompilierung erkannt werden.
Ein gutes Typsystem leistet die benötigte Unterstützung für die Sicherheit
und bessere Wartbarkeit großer Programme.

### `immutable`

Zusätzlich zu der statischen Typisierung, stellt D Speicherklassen bereit
welche weitere Beschränkungen erlauben. Zum Beispiel kann ein `immutable` (unveränderbares)
Objekt nur einmal initialisiert werden und kann danach nicht mehr verändert werden.

    immutable int err = 5;
    // oder: immutable arr = 5 (der Typ int wird automatisch inferriert)
    err = 5; // FEHLER

Wegen ihrer Unveränderbarkeit, können `immutable` Objekte sicher zwischen
Threads geteilt werden. Dies impliziert auch, dass `immutable` Objekte perfekt
gecacht werden können.

### `const`

`const` Objekte können ebenfalls nicht verändert werden. Diese Restriktion ist
jedoch nur für den aktuellen Geltungsbereich gültig. Ein `const` Zeiger
kann sowohl von einem `immutable` Objekt als auch von einem
_veränderbaren_ Objekt erzeugt werden.
Das bedeutet, dass die Restriktion nur für den aktuellen Bereich gilt. In der Zukunft
könnte die Variable jedoch verändert werden. Nur mit `immutable` ist garantiert,
dass der Wert sich niemals ändern kann. Es ist typisch für APIs `const` Objekte
zu akzeptieren, da so garantiert wird, dass keine Modifikation stattfindet.

    immutable a = 10;
    int b = 5;
    const int* pa = &a;
    const int* pb = &b;
    *pa = 7; // FEHLER

Sowohl `immutable` als auch `const` sind _transitiv_. Dies gewährleistet, dass
sobald ein Typ `const` ist alle Subkomponenten dieses Types ebenfalls `const` sind.

### In der Tiefe

#### Grundlegende Referenzen

- [Immutable in _Programmieren in D_](http://ddili.org/ders/d.en/const_and_immutable.html)
- [Bereiche in _Programmieren in D_](http://ddili.org/ders/d.en/name_space.html)

#### Weiterführende Referenzen

- [const(FAQ)](https://dlang.org/const-faq.html)
- [Typqualifizierer in D](https://dlang.org/spec/const3.html)

## {SourceCode}

```d
import std.stdio;

void main()
{
    immutable forever = 100;
    // FEHLER:
    // forever = 5;
    writeln("forever: ",
        typeof(forever).stringof);

    const int* cForever = &forever;
    // FEHLER:
    // *cForever = 10;
    writeln("const* forever: ",
        typeof(cForever).stringof);

    int veraenderbar = 100;
    writeln("veraenderbar: ",
        typeof(veraenderbar).stringof);
    veraenderbar = 10; // OK
    const int* constRef = &veraenderbar; // OK
    // FEHLER:
    // *constRef = 100;
    writeln("constRef: ",
        typeof(constRef).stringof);

    // FEHLER:
    // immutable int* imRef = &veraenderbar;
}
```
