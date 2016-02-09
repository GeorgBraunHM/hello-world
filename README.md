# hello-world
Here goes description, which is optional.

We start with a list
* item 1
* item 2
* item 3

And a table

header 1| header 2
---|---
Zelle 1 | Zelle 2
Zelle 3 | Zelle 4
Das ist etwas mehr Text |fertig



Maybe not working ...

and some c-code with backticks:

```c
for(i=0; i<10; i++) printf("%d", i);
```

Und wie schaut C# aus?
Aber vorher noch etwas `inline code` zum Anschauen. Die Frage ist, wie sich der inline code mit neuen Zeilen verträgt. Dafür muss der Text aber lang genug sein, damit er mehr als eine Zeile beansprucht. Ich hoffe, das reicht.

Den C#-Code machen wir jetzt noch etwas kürzer:

```cs
using System;
class Program:HMGuiApp {
  static void Main() {
    ProgTitle("k01_01_MuVergleich");
    // Definition aller Variablen:
    int i;

    // Datenpunkte aller PlotIDs ausblenden
    for(i=0; i<8; i++) {
      Style(i, PlotStyle.Line);
    }

    Put("Vergleich verschiedener Wachstumsmodelle:");
    Put("Links: Modell Wachstumsrate µ=f(SM)");
    Put("Rechts: Bioreaktorsimulation BM=f(t)");
    Put(" p0, p4: Ideales Wachstum");
    Put(" p1, p5: Modell nach Monod bzw. Michaelis-Menten");
    Put(" p2, p6: Modell mit Substratüberschusshemmung (inhibiert)");
    Put(" p3, p7: Modell mit Geradennäherung");

    MuModellVergleich();
    MBox("Weiter mit Reaktorsimulation.");
    ReaktorSimVergleich();
  } // Ende von Main()

  // Platz für Ihre eigenen Funktionen:

  //Funktion: MuPwl()
  static double MuPwl(double SM, double mumax) {
               // SM: Substratmasse in g
               // mumax: max. Wachstumsrate in 1/min
    double mu; // Tatsächliche Wachstumsrate in 1/min
    double m_mu, t_mu;    // Steigung und Offset der Geradennäherung
    // Eckpunkte der Geradennäherung
    double SM0=0, mu0=0, SM1=100, mu1=mumax;
    double SM2=200, mu2=mumax, SM3=400, mu3=mumax/10;
    // mu berechnen:
    if(SM<=SM0) {
      m_mu = 0;
      t_mu = mu0;
    } else if(SM<=SM1) {
      m_mu = GGlgStg(SM0, mu0, SM1, mu1);
      t_mu = GGlgOff(SM0, mu0, SM1, mu1);
    } else if(SM<=SM2) {
      m_mu = GGlgStg(SM1, mu1, SM2, mu2);
      t_mu = GGlgOff(SM1, mu1, SM2, mu2);
    } else if(SM<=SM3) {
      m_mu = GGlgStg(SM2, mu2, SM3, mu3);
      t_mu = GGlgOff(SM2, mu2, SM3, mu3);
    } else {
      m_mu = 0;
      t_mu = mu3;
    }
    mu = GGlgYvonX(m_mu, t_mu, SM);

    return mu;
  } // Ende von MuPwl()



}
```

Das sollte erstmal reichen.
Ende.

Fertig
