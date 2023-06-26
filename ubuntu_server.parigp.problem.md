historical page for this pari-users mailing list question:  
https://pari.math.u-bordeaux.fr/archives/pari-users-2306/msg00095.html


## PARI/GP

I compiled PARI/GP from source "Stable version: pari-2.15.3.tar.gz (4.9 MB), Mar 14 2023" for Ubuntu and RHEL.
I installed "Stable 64-bit version: Pari64-2-15-3.exe (97.9 MB), Mar 14 2023" on Win11.

I used https://github.com/Hermann-SW/RSA_numbers_factored/blob/main/pari/sqrtm1.gp for computing ```sqrt(-1) (mod p)```
for 10,000-/36,401-/100,355-digit primes. Win11 7600X runtimes [s] are slightly better than RHEL i7-11850H.
What is scary is how bad 7600X Ubuntu runtimes are, resaon seems the be the many page faults:  

|#digits of prime|Win11|Ubuntu 22.04 |RHEL 8.7||
|-:|-:|-:|-:|-|
||7600X|7600X|i7-11850H| CPU|
|10000|3.0|7.7|3.0||
|36401|70.5|303.3|74.3|94.9%|
|100355|660.8|1183.1|708.9|93.2%|


I had this in ```/etc/gprc``` for all OSes:  
```
...
parisizemax = 2G
parisize = 2G
...
```

perf stat output for 100355-digit prime run on 7600X Ubuntu:
```
      1,183,241.38 msec task-clock                #    1.000 CPUs utilized
             3,693      context-switches          #    3.121 /sec
                 0      cpu-migrations            #    0.000 /sec
           244,894      page-faults               #  206.969 /sec
 5,546,523,281,783      cycles                    #    4.688 GHz                      (83.33%)
     5,676,680,988      stalled-cycles-frontend   #    0.10% frontend cycles idle     (83.33%)
    47,098,027,588      stalled-cycles-backend    #    0.85% backend cycles idle      (83.33%)
21,602,536,408,297      instructions              #    3.89  insn per cycle
                                                  #    0.00  stalled cycles per insn  (83.33%)
 1,630,759,947,914      branches                  #    1.378 G/sec                    (83.33%)
    12,131,127,915      branch-misses             #    0.74% of all branches          (83.33%)

    1183.424912459 seconds time elapsed

    1183.096741000 seconds user
       0.142245000 seconds sys
```

perf stat output for 100355-digit prime run on i7-11850H RHEL:
```
        709,429.88 msec task-clock:u              #    1.000 CPUs utilized
                 0      context-switches:u        #    0.000 /sec
                 0      cpu-migrations:u          #    0.000 /sec
             1,320      page-faults:u             #    1.861 /sec
 3,268,129,133,813      cycles:u                  #    4.607 GHz
10,354,296,769,999      instructions:u            #    3.17  insn per cycle
   854,517,377,744      branches:u                #    1.205 G/sec
     9,269,843,275      branch-misses:u           #    1.08% of all branches
16,340,645,669,065      slots:u                   #   23.033 G/sec
11,023,022,135,648      topdown-retiring:u        #     65.0% retiring
 3,139,967,206,996      topdown-bad-spec:u        #     18.5% bad speculation
 1,012,026,816,375      topdown-fe-bound:u        #      6.0% frontend bound
 1,791,447,225,675      topdown-be-bound:u        #     10.6% backend bound

     709.490256363 seconds time elapsed

     708.992250000 seconds user
       0.051910000 seconds sys
```
