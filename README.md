# H4 - tukki

## Lukuläksy
Lukuläksyä varten valitsin Carl Tashianin 3.12.2021 kirjoittaman artikkelin, jossa käsitellään salaisuuksien käsittelyä komentokehotteessa otsikolla: How to Handle Secrets on the Command Line (https://smallstep.com/blog/command-line-secrets/)
- Komentokehotteen tietoturvassa tulee olla tarkkana
- Pienikin virhe voi altistaa koodinpätkässä olevan tiedon
- Putkilla voidaan toimittaa tehokkaasti salattua tietoa
- Tunnustiedosto (Credential file), voidaan käyttää salatun tiedoston avaamiseen turvallisesti
- Ympäristömuuttujat (Environment Variables), eivät ole kovin tietoturvallisia paikkoja ylläpitää salaista tietoa
- Esim. salasanojen käytössä tulee olla tarkkana, jotta ne eivät jää leikepöydälle / puskuriin odottamaan noutajaa
- Linux kernelissä on mahdollista käyttää avainnippua (keyring), joka ei vuoda koskaan levylle
- Salasanoja ei kannata yleisesti kirjoittaa suoraan komentoon / skriptiin, koska ne on helppo kaapata

## Ympäristö

Hyper-V kotikoneella (Host):

- CPU: i5-9600K
- RAM: 16Gb
- HDD: 120Gb
- Win 11 Pro x64

Virtuaalikoneen speksit:

- 2 x CPU
- 4 Gb RAM
- 50 Gb HDD
- Generation 2 (Hyper-V pyytää määrittelemään)


## Lopetus
Kyseinen kotitehtävä avasi paljon komentokehotteen maailmaa Linuxissa. Sen avulla voi selvästi tehdä kaikki toimenpiteet, toki harjoitukset olivat vain pintaraapaisu, mutta kyllä niistä oli itselle ainakin hyötyä. Tehtäviin meni kokonaisuudessaan noin 1,5h.

## Lähteet:
Carl Tashian, 03.12.2021, How to Handle Secrets on the Command Line
https://smallstep.com/blog/command-line-secrets/
