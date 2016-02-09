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
Aber vorher noch etwas `inline code` zum Anschauen.

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

  //Funktion: MuModellVergleich()
  // Was soll die Funktion machen:
  //   Verschiedene Wachstumsmodelle in der Form µ=f(SM) vergleichen.
  //   SM soll von -10 bis 450 g mit einem Abstand von 5 g laufen.
  // Welche Parameter benötigt sie:
  //   keine
  // Welchen Datentyp gibt sie zurück: 
  //   nichts, also void
  // Beispiel für Funktionsaufruf:
  //   MuModellVergleich();
  // Funktionsdefinition (Schnittstelle und Rumpf):
  static void MuModellVergleich() {
    double SM, Anf, End, Abst; // alle in g
    int Anz, i;

    XLabel(0, "SM in g");
    YLabel(0, "µ in 1/min");

    // Datenpunkte aller PlotIDs ausblenden
    for(i=0; i<4; i++) {
      Style(i, PlotStyle.Line);
    }

    Anf  = -10; // g
    End  = 450; // g
    Abst =   5; // g
    // Anzahl der Punkte berechnen
    Anz = 1 + (int)(Math.Round(Math.Abs((End-Anf)/Abst)));

    XScaleLeft(0, Anf);

    for(i=0; i<Anz; i++) {
      SM = Anf + i*Abst;
      Plot(0, SM, MuIdeal(SM, 0.035));
      Plot(1, SM, MuMonod(SM, 0.035, 25));
      Plot(2, SM, MuMonodInhibiert(SM, 0.035, 25, 150));
      Plot(3, SM, MuPwl(SM, 0.035));
    }
  } // Ende von MuModellVergleich()


  //Funktion: ReaktorSimVergleich()
  // Was soll die Funktion machen:
  //   Bioreaktoren mit verschiedenen Wachstumsmodellen simulieren.
  // Welche Parameter benötigt sie:
  //   keine
  // Welchen Datentyp gibt sie zurück: 
  //   nichts, also void
  // Beispiel für Funktionsaufruf:
  //   ReaktorSimVergleich();
  // Funktionsdefinition (Schnittstelle und Rumpf):
  static void ReaktorSimVergleich() {
    double SM, BM, mu; // in g, g, 1/min
    double Ybs, t, Dt; // in 1, min, min
    int i;

    XLabel(1, "t in min"); YLabel(1, "BM in g");

    // Schleife über 4 Wachstumsmodelle
    for(i=0; i<4; i++) {
      SM=300; BM=2.5;     // in g, g, 1/min
      Ybs=0.5; t=0; Dt=2; // in 1, min, min
      mu=0;

      Plot(i+4, t, BM);
      // Simulationsschleife
      while(t<340) {
        if(i==0) { mu = MuIdeal(SM, 0.035); }
        if(i==1) { mu = MuMonod(SM, 0.035, 25); }
        if(i==2) { mu = MuMonodInhibiert(SM, 0.035, 25, 150); }
        if(i==3) { mu = MuPwl(SM, 0.035); }
        t  = t + Dt;
        SM = SM - BM * (Math.Exp(mu*Dt)-1)/Ybs;
        BM = BM * Math.Exp(mu*Dt);
        Plot(i+4, t, BM);
      } // Ende der Simulationsschleife

    } // Ende der Schleife über 4 Wachstumsmodelle

  } // Ende von ReaktorSimVergleich()


  //Funktion: MuIdeal()
  // Was soll die Funktion machen:
  //   Ideales Wachstum berechnen, vorausgesetzt es ist Substratmasse vorhanden.
  // Welche Parameter benötigt sie:
  //   SM:    Substratmasse SM in g als Kommazahl,
  //   mumax: Max. Wachstumsrate in 1/min als Kommazahl
  // Welchen Datentyp gibt sie zurück: 
  //   Die Wachstumsrate in 1/min als Kommazahl
  // Beispiel für Funktionsaufruf:
  //   mu = MuIdeal(SM, 0.035);
  // Funktionsdefinition (Schnittstelle und Rumpf):
  static double MuIdeal(double SM, double mumax) {
    double mu; // Tatsächliche Wachstumsrate in 1/min

    // Wachstum nur mit Nahrung
    if(SM>0) {
      mu = mumax;
    } else {
      mu = 0;
    }

    return mu;
  } // Ende von MuIdeal()


  //Funktion: MuMonod()
  // Was soll die Funktion machen:
  //   Wachstum nach Monod bzw. Michaelis-Menten berechnen, 
  //   vorausgesetzt es ist Substratmasse vorhanden.
  // Welche Parameter benötigt sie:
  //   SM:    Substratmasse SM in g als Kommazahl,
  //   mumax: Max. Wachstumsrate in 1/min als Kommazahl
  //   Ksm:   Substratsättigungsmasse in g als Kommazahl
  // Welchen Datentyp gibt sie zurück: 
  //   Die Wachstumsrate in 1/min als Kommazahl
  // Beispiel für Funktionsaufruf:
  //   mu = MuMonod(SM, 0.035, 25);
  // Funktionsdefinition (Schnittstelle und Rumpf):
  static double MuMonod(double SM, double mumax, double Ksm) {
    double mu; // Tatsächliche Wachstumsrate in 1/min

    // Wachstum nur mit Nahrung
    if(SM>0) {
      mu = mumax*(SM/(Ksm+SM));
    } else {
      mu = 0;
    }

    return mu;
  } // Ende von MuMonod()


  //Funktion: MuMonodInhibiert()
  // Was soll die Funktion machen:
  //   Inhibiertes Wachstum berechnen, 
  //   vorausgesetzt es ist Substratmasse vorhanden.
  // Welche Parameter benötigt sie:
  //   SM:    Substratmasse SM in g als Kommazahl,
  //   mumax: Max. Wachstumsrate in 1/min als Kommazahl
  //   Ksm:   Substratsättigungsmasse in g als Kommazahl
  //   Kim:   Inhibierungsmasse in g
  // Welchen Datentyp gibt sie zurück: 
  //   Die Wachstumsrate in 1/min als Kommazahl
  // Beispiel für Funktionsaufruf:
  //   mu = MuMonodInhibiert(SM, 0.035, 25, 150);
  // Funktionsdefinition (Schnittstelle und Rumpf):
  static double MuMonodInhibiert(double SM, double mumax, double Ksm, double Kim ) {
    double mu; // Tatsächliche Wachstumsrate in 1/min

    // Wachstum nur mit Nahrung
    if(SM>0) {
      mu = mumax*(SM/(Ksm+SM+SM*SM/Kim));
    } else {
      mu = 0;
    }

    return mu;
  } // Ende von MuMonodInhibiert()


  //Funktion: MuPwl()
  // Was soll die Funktion machen:
  //   Wachstum über Geraden-Näherung berechnen
  //   (Pwl: Piece wise linear), 
  //   vorausgesetzt es ist Substratmasse vorhanden.
  // Welche Parameter benötigt sie:
  //   SM:    Substratmasse SM in g als Kommazahl,
  //   mumax: Max. Wachstumsrate in 1/min als Kommazahl
  //   Die Eckpunkte der Geradenstücke werden in der Funktion definiert.
  // Welchen Datentyp gibt sie zurück: 
  //   Die Wachstumsrate in 1/min als Kommazahl
  // Beispiel für Funktionsaufruf:
  //   mu = MuPwl(SM, 0.035);
  // Funktionsdefinition (Schnittstelle und Rumpf):
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


  //Funktion: GGlgStg()
  // Was soll die Funktion machen:
  //   Steigung einer Gerade durch zwei Punkte berechnen
  // Welche Parameter benötigt sie:
  //   x- und y-Koordinaten (jeweils Kommazahlen) der beiden Punkte
  // Welchen Datentyp gibt sie zurück: 
  //   Die Steigung als Kommazahl
  // Beispiel für Funktionsaufruf:
  //   m = GGlgStg(x1, y1, x2, y2);
  // Funktionsdefinition (Schnittstelle und Rumpf):
  static double GGlgStg(double x1, double y1, double x2, double y2) {
    return (y2-y1)/(x2-x1);
  }

  //Funktion: GGlgOff()
  // Was soll die Funktion machen:
  //   Offset (Y-Achsenabschnitt) einer Gerade durch zwei Punkte berechnen
  // Welche Parameter benötigt sie:
  //   x- und y-Koordinaten (jeweils Kommazahlen) der beiden Punkte
  // Welchen Datentyp gibt sie zurück: 
  //   Den Offset (Y-Achsenabschnitt) als Kommazahl
  // Beispiel für Funktionsaufruf:
  //   t = GGlgOff(x1, y1, x2, y2);
  // Funktionsdefinition (Schnittstelle und Rumpf):
  static double GGlgOff(double x1, double y1, double x2, double y2) {
    return y1 - (y2-y1)/(x2-x1)*x1;
  }

  //Funktion: GGlgYvonX()
  // Was soll die Funktion machen:
  //   y(x) einer Gerade berechnen
  // Welche Parameter benötigt sie:
  //   Steigung u. Offset der Geradengleichung, x-Wert (alles Kommazahlen)
  // Welchen Datentyp gibt sie zurück: 
  //   y-Wert als Kommazahl
  // Beispiel für Funktionsaufruf:
  //   y = GGlgYvonX(m, t, x);
  // Funktionsdefinition (Schnittstelle und Rumpf):
  static double GGlgYvonX(double m, double t, double x) {
    return m*x + t;
  }

  //Funktion: GGlgXvonY()
  // Was soll die Funktion machen:
  //   x(y) einer Gerade berechnen (sozusagen Umkehrfunktion)
  // Welche Parameter benötigt sie:
  //   Steigung u. Offset der Geradengleichung, y-Wert (alles Kommazahlen)
  // Welchen Datentyp gibt sie zurück: 
  //   x-Wert als Kommazahl
  // Beispiel für Funktionsaufruf:
  //   x = GGlgYvonX(m, t, y);
  // Funktionsdefinition (Schnittstelle und Rumpf):
  static double GGlgXvonY(double m, double t, double y) {
    return (y-t)/m;
  }

}
```

Das sollte erstmal reichen.
Ende.

Fertig
