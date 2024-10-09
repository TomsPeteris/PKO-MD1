## TSP - Tabu Search

### Uzdevuma formulējums

Kurjeram nepieciešama palīdzība optimālākā maršruta sastādīšanā. Dots saraksts ar m piegādes punktiem, kas sastāv no x un y koordinātām, kā arī piegādājamā sūtījuma svara n. Zināms, ka kurjeram jāuzsāk maršruts no noliktavas, katrs piegādes punkts jāapmeklē vienu reizi un beigās jāatgriežas noliktavā. Papildus jācenšas piegādāt smagie sūtījumi pēc iespējas ātrāk.

### Algoritma apraksts

Uzdevuma risināšanai ir izvēlēts tabu search algoritms. Tabu search iteratīvi izpēta risinājumu telpu, pārvietojoties no viena potenciālā risinājuma uz citu. Tas izmanto tabu sarakstu, lai novērstu atgriešanos pie nesen apmeklētiem risinājumiem, tādējādi uzlabojot izpēti un novēršot algoritma iestāšanos lokālajos optimumos.

Implementētais algoritms sastāv no sekojošajiem soļiem:

1. Inicializācija - Tiek ģenerēti testa dati un patvaļīgi ģenerēts sākotnējais risinājums. Izmantojot random.seed() metodi tiek nodrošināts, ka radītie dati sakrītīs atkārtojot izpildi.

2. Iteratīvs cikls, kas darbojas līdz tiek izsmelta iespējamo risinājumu kopa, vai sasniegta max_interations inicializācijas parametra vērtība.

3. Apkārtnes funkcija, kas balstās uz 2-opt swap algoritmu, lai ģenerētu kaimiņus.

4. Izmaksu funkcija uz katru kaimiņu, lai aprēķinātu maršruta izmaksu. Funkcija pielāgota lai ņemtu vērā arī nosacījumu pēc iespējas ātrāk piegādāt smagos sūtījumus. Tā seko šādai formulai:

> Kopējās izmaksas = kopējais attālums + (alpha _ (sūtījuma svars _ piegādes secības indekss / 2))

Šajā gadījumā sūtījuma svara un piegādes secības indeksa reizinājums tiek dalīts ar 2, lai mazinātu svara ietekmi uz rezultātu. Papildus svara nozīmīgumu var ietekmēt izmantojot alpha parametru, kas ir viens no inicializācijas parametriem. Norādot alpha = 0 svars netiks ņemts vērā.

5. Tabu saraksta parvaldība, kas sevī ietver gājienu reģistrēšanu, kas rezultējušies jauna labākā maršruta atklāšanā. Tabu saraksta garumu iespējams kontrolēt pamainot tabu_size inicializācijas parametra vērtību.

### Testēšanas apraksts

Uz maziem piegādes punktu skaitiem testēšana un ar to saistītie aprēķini var tikt veikti manuāli, kas arī tika sākotnēji veikts, lai pārliecinātos par algoritma pareizu darbību. Programmas generate_test_data metode satur nepieciešamo kodu, lai izveidotu testa datus. Mainīgais num_deliveries nosaka cik piegādes punktu jāuzģenerē. Manuāli testējot ar pašģenerētiem datiem tika izmantota datu kopa ar 10 piegādes punktiem alpha = 0.5, tabu_size = 100, max_iterations = 10000:

```
ID: 0, Koordinātas: (50, 50)
ID: 1, Koordinātas: (47.12, 46.54), Svars: 36 kg
ID: 2, Koordinātas: (50.35, 60.96), Svars: 14 kg
ID: 3, Koordinātas: (31.24, 12.19), Svars: 12 kg
ID: 4, Koordinātas: (45.72, 47.26), Svars: 12 kg
ID: 5, Koordinātas: (54.65, 73.27), Svars: 4 kg
ID: 6, Koordinātas: (20.59, 38.09), Svars: 90 kg
ID: 7, Koordinātas: (36.44, 46.07), Svars: 76 kg
ID: 8, Koordinātas: (42.89, 89.72), Svars: 98 kg
ID: 9, Koordinātas: (60.64, 20.93), Svars: 44 kg
ID: 10, Koordinātas: (47.57, 63.71), Svars: 98 kg
```

![Datu Kopa. Ja nedarbojas, skat. assets mapi - manual10-data.png](/assets/manual10-data.png)

Algoritma izpildes rezultātā tika iegūts:

-   Optimālais maršruts: 0 -> 10 -> 8 -> 6 -> 7 -> 1 -> 9 -> 3 -> 4 -> 2 -> 5 -> 0
-   Maršruta izmaksa: 506.50, kas tika sasniegta 14 iterācijās
-   Izpildes laiks: 0.01s

![Rezultāts. Ja nedarbojas, skat. assets mapi - manual10-result.png](/assets/manual10-result.png)

Lai papildus pārliecinātos par algoritma pareizu darbību, tika piemeklēta testēšanas kopa ar zināmu rezultātu. Datu kopas iegūšanai tika izvēlēts [TSPLIB](http://comopt.ifi.uni-heidelberg.de/software/TSPLIB95/), kas satur salīdzinoši daudz dažādu kvalitatīvu datu un rezultātu TSP tipa problēmām. Konkrēti šī uzdevuma testēšanas nolūkam tika izvēlētas att48.tsp un att532.tsp datu kopas, kas satur ASV pilsētu koordinātas. Datu kopas trūkums ir, ka tā nesatur papildus informāciju par svaru, kas pēc uzdevuma formulējuma ietekmē rezultātu prioritizējot punktus, kuros jāpiegādā sūtījumi ar lielāku svaru, bet svara ignorēšana fundamentāli neizmaina risinājuma pareizību, jo svars tiek ņemts vērā tikai izmaksu funkcijā, aprēķinot soda punktus, kuru apjomu ietekmē alpha inicializācijas parametrs.

TSPLIB testēšanas procesā tika izmantots alpha = 0, lai svars netiktu ņemts vērā un tabu_size = 100, max_iterations = 10000:

![Datu Kopa. Ja nedarbojas, skat. assets mapi - att48-data.png](/assets/att48-data.png)

Algoritma izpildes rezultātā tika iegūts:

-   Optimālais maršruts: 1 -> 9 -> 40 -> 15 -> 12 -> 11 -> 13 -> 25 -> 14 -> 23 -> 3 -> 22 -> 16 -> 41 -> 34 -> 29 -> 2 -> 26 -> 4 -> 35 -> 45 -> 10 -> 24 -> 42 -> 5 -> 48 -> 39 -> 32 -> 21 -> 47 -> 20 -> 33 -> 46 -> 36 -> 30 -> 43 -> 17 -> 27 -> 19 -> 37 -> 6 -> 28 -> 7 -> 18 -> 44 -> 31 -> 38 -> 8 -> 1
-   Maršruta izmaksa: 10628.00, kas sakrīt ar TSPLIB sniegto atbildi un tika sasniegta 5882 iterācijās
-   Izpildes laiks: 213.21s

![Rezultāts. Ja nedarbojas, skat. assets mapi - att48-result.png](/assets/att48-result.png)

Papildus tika mēģināts veikt arī testēšanu uz att532.tsp datu kopu, taču tās piegādes punktu skaita dēļ, nepieciešamo iterāciju apjoms un izpildes laiks bija pārāk liels, lai iegūtu rezultātu (Algoritms tika darbināts tuvu pie 60 minūtēm).
