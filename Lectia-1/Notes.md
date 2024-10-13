# Introducere

### TODO

# Code Style

Pe viitor veți vedea că este important modul în care scrieți codul (în engleză, [code style](https://en.wikipedia.org/wiki/Programming_style)). Pe de o parte, pentru a vă ajuta pe voi înșivă în a citi cu ușurință un cod scris cu câteva luni în urmă, pe de altă parte pentru a fi ușor de înțeles și de alte persoane. Astfel, au apărut numeroase convenții utilizate în scrierea codului adoptate de fiecare companie sau proiect (ex.: [Google](https://google.github.io/styleguide/cppguide.html), [kernelul Linux](https://www.kernel.org/doc/html/v4.10/process/coding-style.html), etc).

Nu voi insista pe respectarea unui stil anume, dar vreau ca cel pe care îl folosiți într-o sursă să fie constant. Acest lucru vă va fi de folos și pe viitor, când veți ajunge să lucrați într-o echipă și va trebui respectat un stil anume de întreaga echipă.

De exemplu, nu scrieți codul așa:
```cpp
if (x < 10) {
  cout << "Este cifra" << endl;
}
else
{
  cout << "Nu este cifra" << endl;
}
```

ci așa:
```cpp
if(x < 10) {
  cout << "Este cifra" << endl;
} else {
  cout << "Nu este cifra" << endl;
}
```
sau așa:
```cpp
if(x < 10)
{
  cout << "Este cifra" << endl;
}
else
{
  cout << "Nu este cifra" << endl;
}
```

Nu scrieți codul așa:
```cpp
int suma(int a, int b)
{
  int c = a + b;
   c %= 1000000007;
 return c;
}
```

ci așa:
```cpp
int suma(int a, int b)
{
  int c = a + b;
  c %= 100000007;
  return c;
}
```

# Comentarii

Unele linii de cod sunt greu de înțeles indiferent de cât de "curat" le scriem. Cel mai ușor mod de a rezolva acest lucru este prin comentarii. Acestea pot fi folosite atât pentru explicarea propriu-zisă a unei bucăți de cod, dar și pentru a specifica niște presupuneri legate de variabilele folosite (vom vedea mai tărziu [programare prin contract](#programare-prin-contract)).

Să încercăm să înțelegem ce face următoarea funcție:
```cpp
void solve()
{
  // TODO
}
```

```cpp
// TODO
```

# Programarea prin contract

În general, când lucrăm cu funcții, facem niște presupuneri despre parametrii primiți. De exemplu, la căutarea binară, presupunem că vectorul în care căutam este sortat. Ce s-ar întâmpla dacă acesta nu ar fi sortat? Rezultatul ar fi greșit. Pentru o funcție oarecare, utilizatorul ar trebui să înțeleagă pe deplin ce face aceasta pentru a găsi motivul pentru care rezultatul primit este greșit. Pentru a rezolva această problemă, o metodă des întâlnită este [programarea prin contract](https://en.wikipedia.org/wiki/Design_by_contract), ceea ce nu spune mai mult decât să scrii în comentarii deasupra funcției presupunerile pe care aceasta le face despre parametrii.

De exemplu, continuând cu căutarea binară:
```cpp
// v is an array of length n
// v is increasing
// x exists in v
// returns the rightmost position of x in v
int search(int* v, int n, int x)
{
  int l = 0, r = n;
  while(r - l > 1)
  {
    int m = (l + r) / 2;
    if(v[m] <= x)
      l = m;
    else
      r = m;
  }
  return l;
}
```

Astfel, un programator care nu știe neapărat tot ce face funcția respectivă (posibil chiar și voi după câteva luni de la scrierea codului sau după o oră de disecat altă problemă!), poate citi cu ușurință comentariile pentru a înțelege cum să o folosească.

## Exerciții

### TODO adaugă câteva exerciții 

# Invarianți

### TODO reformulare
În afară de a scrie cod care este ușor de înțeles, trebuie să ne asigurăm că este și corect, inclusiv pentru cazurile particulare. Aici intervine ideea de [invariant](https://dexonline.ro/definitie/invariant). Este important ca pentru un algoritm să identificăm invarianții pentru a avea o anumită garanție asupra funcționării acestuia. De exemplu, să luăm următorul cod de căutare binară:

```cpp
// v is a sorted array of length n
// x exists in v
int search(int* v, int n, int x)
{
  int l = 0, r = n;
  while(r - l > 1)
  {
    int m = (l + r) / 2;
    if(v[m] <= x)
      l = m;
    else
      r = m;
  }
  return l;
}
```

Să ne gândim cum funcționează: variabilele l și r reprezintă intervalul ```[l, r)``` în care se află elementul căutat. Algoritmul continuă cât timp în acest interval sunt cel puțin două elemente. În cod, acest lucru se scrie ```r - l > 1```, adică lungimea intervalului ```[l, r)``` este mai mare ca ```1```. Acesta este primul invariant de buclă recunoscut în algoritm, fiind chiar condiția de rulare a buclei (de aceea se numește și invariant trivial). Un alt invariant, netrivial, este ```v[l] <= x```. De ce? Deoarece ```x``` există în ```v```, ```v[0] <= x```, deci inițial, condiția are loc. Pe parcursul buclei, ```l``` se modifică doar atunci când ```v[m] <= x```, devenind ```m```, deci condiția are loc și după modificare. Astfel, în fiecare moment aceasta se menține. La fel, putem spune că ```v[r] > x``` (ignorând situația ```r == n```).

Am identificat câțiva dintre invarianții de buclă, dar de ce sunt utili? Am pus mai sus condiția că ```x``` există în ```v```, dar dacă nu știm asta? Să luăm cazurile:

 - ```v[0] <= x``` - în acest caz, deoarece condiția ```v[l] <= x < v[r]``` este un invariant, este ușor să ne dăm seama că, la finalul buclei, ```v[l]``` va fi cel mai mare număr din ```v```, care este ```<= x```;
 - ```v[0] > x``` - în acest caz, condiția ```v[m] <= x``` va fi mereu falsă, deci ```l``` va rămâne ```0```.

Astfel, cu ajutorul invarianților, am identificat comportamentul algoritmului și cazurile particulare. Astfel, putem face ajustările necesare ca acesta să meargă și pentru situația când ```x``` nu se află în ```v```:

```cpp
// v is a sorted array of length n
int search(int* v, int n, int x)
{
  int l = 0, r = nș
  while(r - l > 1)
  {
    int m = (l + r) / 2;
    if(v[m] <= l)
      l = m;
    else
      r = m;
  }
  return v[l] <= x ? l : -1;
}
```

În noul algoritm, în situația când ```v[0] > x``` returnăm indicele ```-1``` semnificând inexistența unui număr ```<= x```.

Probabil că, pentru algoritmul de căutare binară, aceste lucruri par destul de evidente, dar în alte situații este mult mai ușor de înțeles și de adaptat o bucată de cod prin identificarea invarianților.

### TODO încă un exemplu

## Exerciții

### TODO adaugă câteva exerciții de identificare a invarianților
