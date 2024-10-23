# Introducere

### TODO

# Code Style

Pe viitor veți vedea că este important modul în care scrieți codul (în engleză, [code style](https://en.wikipedia.org/wiki/Programming_style)), pe de o parte, pentru a vă ajuta pe voi înșivă în a citi cu ușurință un cod scris cu câteva luni în urmă, pe de altă parte pentru a fi ușor de înțeles și de alte persoane. Astfel, au apărut numeroase convenții utilizate în scrierea codului adoptate de fiecare companie sau proiect (ex.: [Google](https://google.github.io/styleguide/cppguide.html), [kernelul Linux](https://www.kernel.org/doc/html/v4.10/process/coding-style.html), etc).

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

Există și programe care verifică sursele pentru a găsi greșeli de code style. De exemplu, la UPB pentru cursul de C este folosit programul [acesta](https://cutt.ly/pc20-cs) pentru a verifica dacă sursa respectă code style-ul folosit de [kernelul Linux](https://www.kernel.org/doc/html/v4.10/process/coding-style.html). Alt checker des folosit, care funcționează și pentru C++, este [cpplint](https://github.com/cpplint/cpplint), care verifică respectarea code style-ului folosit de [Google](https://google.github.io/styleguide/cppguide.html).

# Comentarii

Unele linii de cod sunt greu de înțeles indiferent de cât de "curat" le scriem. Cel mai ușor mod de a rezolva acest lucru este prin comentarii. Acestea pot fi folosite atât pentru explicarea propriu-zisă a unei bucăți de cod, dar și pentru a specifica niște presupuneri legate de variabilele folosite (vom vedea mai tărziu [programarea prin contract](#programare-prin-contract)).

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

Continuând exemplul cu căutarea binară:
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

Ce presupuneri fac funcțiile următoare?

 1.
```cpp
int factorial(int n)
{
  int r = 1;
  for(int i = 2; i <= n; i++)
    r *= i;
  return r;
}
```

 2.
```cpp
void quicksort(int* v, int n)
{
  if(n <= 1) return;
  
  int pivot = v[0], l = 0, r = n;
  while(l < r)
  {
    if(v[l] <= pivot)
      l++;
    else
      swap(v[l], v[--r]);
  }

  if(l > 0) quicksort(v, l);
  if(l < n) quicksort(v + l, n - l);
}
```

 3.
```cpp
#define MOD 1000000007

int add(int x, int y)
{
  return (x + y) % MOD;
}
```

 4.
```cpp
#define MOD 1000000007

int add(int x, int y)
{
  x += y;
  if(x >= MOD)
    x -= MOD;
  return x;
}
```

# Invarianți

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

Să ne gândim cum funcționează: variabilele l și r reprezintă intervalul $[l, r)$ în care se află elementul căutat. Bucla continuă cât timp în acest interval sunt cel puțin două elemente. În cod, acest lucru se scrie $r - l > 1$, adică lungimea intervalului $[l, r)$ este mai mare ca $1$. Acesta este primul invariant de buclă recunoscut în algoritm, fiind chiar condiția de rulare a buclei (de aceea se numește și invariant trivial). Un alt invariant, netrivial, este $v[l] \le x$. De ce? Deoarece $x$ există în $v$, $v[0] \le x$, deci inițial, condiția are loc. Pe parcursul buclei, $l$ se modifică doar atunci când $v[m] \le x$, devenind $m$, deci condiția are loc și după modificare. Astfel, în fiecare moment aceasta se menține. La fel, putem spune că $v[r] > x$ (presupunând că $v[n]$ este infinit).

Am identificat câțiva dintre invarianții de buclă, dar de ce sunt utili? Am pus mai sus condiția că $x$ există în $v$, dar dacă nu știm asta? Să luăm cazurile:

 - $v[0] \le x$ - în acest caz, deoarece condiția $v[l] \le x < v[r]$ este un invariant, este ușor să ne dăm seama că, la finalul buclei, $v[l]$ va fi cel mai mare număr din $v$, care este $\le x$;
 - $v[0] > x$ - în acest caz, condiția $v[m] \le x$ va fi mereu falsă, deci $l$ va rămâne $0$.

Astfel, cu ajutorul invarianților, am identificat comportamentul algoritmului și cazurile particulare. Astfel, putem face ajustările necesare ca acesta să meargă și pentru situația când $x$ nu se află în $v$:

```cpp
// v is a sorted array of length n
int search(int* v, int n, int x)
{
  int l = 0, r = nș
  while(r - l > 1)
  {
    int m = (l + r) / 2;
    if(v[m] <= x)
      l = m;
    else
      r = m;
  }
  return v[l] <= x ? l : -1;
}
```

Pentru această implementare, în situația când $v[0] > x$ returnăm indicele $-1$ semnificând inexistența unui număr $\le x$.

Probabil că, pentru algoritmul de căutare binară, aceste lucruri par destul de evidente, dar în alte situații este mult mai ușor de înțeles și de adaptat o bucată de cod prin identificarea invarianților.

## Exerciții

Identificați invarianții de buclă din următoarele funcții:

 1.
```cpp
void merge(int* a, int n, int* b, int m, int* c, int& k)
{
  int i = 0, j = 0;
  k = 0;

  while(i < n && j < m)
  {
    if(a[i] < b[j])
      c[k++] = a[i++];
    else
      c[k++] = b[j++];
  }

  while(i < n)
    c[k++] = a[i++];

  while(j < m)
    c[k++] = b[j++];
}
```

 2.
```cpp
void selection_sort(int* v, int n)
{
  for(int i = 0; i < n; i++)
    for(int j = i; j < n; j++)
      if(v[i] > v[j])
        swap(v[i], v[j]);
}
```

 3.
```cpp
void gcd(int a, int b)
{
  while(b != 0)
  {
    int t = a % b;
    a = b;
    b = t;
  }
  return a;
}
```

# Complexitate de timp

Toate problemele la info au restricții legate de timpul de execuție și de memoria folosită. De asemenea, și la job este important să știm câtă memorie consumă codul scris și cât durează execuția. Aceste lucruri se pot măsura, dacă avem codul deja scris, sau se pot estima. Este util să le putem estima înainte de a scrie codul, pentru a ne asigura că se încadrează în restricțiile impuse.

În cazul estimării timpului de execuție, acest lucru este destul de complicat și intervin mulți factori pe care nu îi putem prezice (unele operații mai scumpe ca altele, [cache miss](https://en.wikipedia.org/wiki/CPU_cache#Cache_miss), [branch prediction failure](https://en.wikipedia.org/wiki/Branch_predictor), alte aplicații rulează în fundal și ocupă din resursele procesorului etc.). De aceea, ce este studiat, în realitate, este cum se scalează timpul de execuție față de intrările programului. Aici apare conceptul de complexitate de timp.

Să luăm ca exemplu o simplă buclă care citește un vector de la tastatură:
```cpp
cin >> n;
  for(int i = 0; i < n; i++)
    cin >> v[i];
```

Această bucată de cod execută un număr de pași (instrucțiuni) proporțional cu valoarea lui $n$. De aceea și timpul de execuție va fi aproximativ proporțional cu $n$. Astfel, putem spune că programul are o complexitate de timp de $O(n)$ ([big O notation](https://en.wikipedia.org/wiki/Big_O_notation)).

Alt exemplu este cel al algoritmului de cătuare binară:
```cpp
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

Care este complexitatea de timp a acestuia? La început, $r-l=n$, iar la fiecare iterație a buclei, $r - l$ se înjumătățește, și bucla se oprește atunci când $r-l\le 1$. Astfel, dacă $k$ este numărul total de iterații ale buclei, avem că $\lfloor\frac{n}{2^k}\rfloor=1\Rightarrow2^k\le n<2^{k+1}\Rightarrow k\le \log(n)<k+1$. Deci, complexitatea căutării binare este $O(\log n)$.

Continuăm cu următorul exemplu:
```cpp
for(int i = 0; i < n - 10; i++)
{
  for(int j = 1; j < 10; j++)
    a[i] += a[i + j];
  a[i] /= 10;
}
```

Chiar dacă în bucla principală executăm câte 10 operații pentru fiecare iterație, complexitatea de timp este tot $O(n)$! În general, dacă codul nostru execută $c \cdot f(n)$ operații, unde $c$ este o constantă care nu depinde de intrări, complexitatea va fi $O(f(n))$, adică notația O mare ignoră constanta. Totuși, este adevărat și faptul că acest cod este de 10 ori mai lent decât dacă am fi eliminat for-ul interior.

Astfel, este important să luăm în calcul și constanta, nu numai complexitatea de timp. Pot apărea cazuri în care un algoritm $O(n^3)$ cu constantă mică să se comporte mai bine decât un algoritm $O(n^2)$ cu constantă mare! Totul depinde de datele de intrare.

## Exerciții

 1. Care este complexitatea de timp a fiecăreia dintre funcțiile următoare?
```cpp
void selection_sort(int* v, int n)
{
  for(int i = 0; i < n; i++)
    for(int j = i; j < n; j++)
      if(v[i] > v[j])
        swap(v[i], v[j]);
}
```

```cpp
void quicksort(int* v, int n)
{
  if(n <= 1) return;
  
  int pivot = v[0], l = 0, r = n;
  while(l < r)
  {
    if(v[l] <= pivot)
      l++;
    else
      swap(v[l], v[--r]);
  }

  if(l > 0) quicksort(v, l);
  if(l < n) quicksort(v + l, n - l);
}
```

```cpp
struct event_t
{
  int i;
  int x;
};

void go_through_events(int* sums, int n, event_t* events, int m)
{
  sums[0] = 0;
  for(int i = 1, j = 0; i <= n; i++)
  {
    sums[i] = sums[i - 1];
    while(j < m && events[j].i <= i)
    {
      sums[i] += events[j].x;
      j++;
    }
  }
}
```

```cpp
int slow_fibonacci(int n)
{
  if(n <= 1) 
    return n;
  
  return slow_fibonacci(n - 1) + slow_fibonacci(n - 2);
}
```

```cpp
void multiply(int n, int m, int p, int a[100][100], int b[100][100], int c[100][100])
{
  for(int i = 0; i < n; i++)
    for(int j = 0; j < p; j++)
    {
      c[i][j] = 0;
      for(int k = 0; k < m; k++)
        c[i][j] += a[i][k] * b[k][j];
    }
}
```

 2. Care dintre următoarele funcții este mai rapidă? De ce?
```cpp
int factorial_iterativ(int n)
{
  int r = 1;
  for(int i = 1; i <= n; i++)
    r *= i;
  return r;
}

int factorial_recursiv(int n)
{
  if(n <= 1) return 1;
  else return n * factorial_recursiv(n - 1);
}
```

 3. Cum putem reduce complexitatea de timp a următorului program la $O(\sqrt n)$?
```cpp
bool is_prime(int n)
{
  for(int i = 2; i < n; i++)
    if(n % i == 0)
      return false;
  return true;
}
```

 4. Cum putem reduce și mai mult timpul de execuție al programului de mai sus (nu neapărat complexitatea)?
 5. Scrieți un program care citește de la tastatură un număr $n$ între $1$ și $1.000.000$ și un șir de $n$ numere naturale cuprinse între $1$ și $1.000.000$. Pentru fiecare număr, să se afișeze $1$ dacă este prim și $0$ altfel. Care este complexitatea de timp a programului?

## Memorie

La prima vedere, memoria utilizată nu influențează timpul de execuție, dar nu este așa! Pe de o parte, dacă folosim $S$ octeți, complexitatea de timp va fi de minimum $O(S)$, căci accesăm $S$ octeți. Pe de altă parte, cu cât folosim memorie mai puțină, cu atât o să avem mai puține [cache miss-uri](https://en.wikipedia.org/wiki/CPU_cache#Cache_miss). Să presupunem că procesorul pe care rulează codul are $8 MB$ (exemplu: Ryzen 3 5100). Dacă programul nostru folosește $16 MB$, atunci $50\%$ din accesări vor nimeri cache-ul. În schimb, dacă reducem utilizarea până și cu $4 MB$, la $12 MB$, $75\%$ din accesări vor nimeri cache-ul (Atenție! Această descriere este una simplificată, pentru a ilustra ideea. În realitate, sunt mult mai mulți factori).

# Tema

 1. Care este complexitatea de timp a următoarei secvențe de cod?
```cpp
int power(int a, int n)
{
  if(n == 0) return 1;
  if(n == 1) return a;

  if(n % 2 == 0) return power(a * a, n / 2);
  else return a * power(a * a, n / 2);
}
```

 2. Care este complexitatea de timp a următoarei funcții?
```cpp
void normalize(int* v, int n, int* xn)
{
  std::sort(v, v + n);

  int x = 0;
  xn[0] = 0;
  for(int i = 1; i < n; i++)
  {
    if(v[i] != v[i - 1])
      x++;
      
    xn[i] = x;
  }
}
```

 3. Se dă un număr natural $n$ și un șir de $n$ numere întregi $a_i$ $(1 \le i \le n)$. Să se calculeze suma maximă a unei subsecvențe a șirului $a$. Rezolvați cu o complexitate de timp cât mai mică.
 
Exemplu:
| Intrare           | Ieșire  | Explicație
| ------------------|---------|-----------
| 5 <br> 1 4 -7 3 1 | 5       | Suma maximă este obținută pentru subsecvența <br> $[1, 2]$ și este egală cu $a_1 + a_2 = 1 + 4 = 5$
| 5 <br> 1 4 -2 3 1 | 7       | Suma maximă este obținută pentru subsecvența <br> $[1, 5]$ și este egală cu $a_1 + a_2 + a_3 + a_4 + a_5 =$ <br> $= 1 + 4 + (-2) + 3 + 1 = 7$

 4. Scrieți o funcție care primește ca parametru un număr întreg $n$ și returnează $F_n$ - al $n$-lea număr Fibonacci. Ce complexitate are programul scris și câtă memorie folosește? Există o soluție cu complexitatea de timp mai bună de $O(n)$?
