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

## Aloitus 
Tehtävän tarkoituksena oli löytää opettajan pyytämät lokitiedostot ja navigoida sinne käyttäen komentokehotetta.
Aloitin tehtävät käynnistämällä virtuaalisen koneen perjantaina 27.1.2023 klo 21.00. Koneelle kirjautumisen jälkeen avasin komentokehotteen ja navigoin siellä kohteeseen ```/var/log/```, jonka alta löytyi oletuksena kaikki tehtävän lokitiedostot, paitsi Apache2:n lokit, tästä lisää myöhemmässä vaiheessa.


### Syslog
Aloitin ensin syslogista avaamalla sen komennolla ```less syslog``` ja huomasin heti törmääväni ongelmaan: </br>
![Kuva1](https://user-images.githubusercontent.com/122887740/215199535-335cca99-4e25-40c9-8dfe-4f1bac60fcd1.png)</br>

Tästä viisastuneena päätin ottaa juurioikeudet käyttööni aktivoimalla ne komennolla ```sudo su```, komentokehote pyysi järjestelmänvalvojan tunnuksia, jotka syötin onnistuneesti. Tämän jälkeen siirryin avaamaan jälleen syslogia ```less syslog``` komennolla ja sain kuin sainkin lokitiedoston auki:</br>
![Kuva2](https://user-images.githubusercontent.com/122887740/215200206-d84968ff-b3ab-4657-ab1c-135bcee14492.png)</br>


```/var/log/syslog``` <- löytyy käynnistyksestä, virta-asetuksiin, sisältää myös onnistumiset ja virheet. Toisin sanoen läjä, johon menee kategorisoimattomat järjestelmän tapahtumat.</br>

Esim. Jan 27 21:02:23 matti-virtualmachine systemd[881]: gpg-agent-ssh.socket: Succeeded.
- Kello: oikea, aikavyöhyke EET +2
- Jan 27 21:02:23 <- Tapahtumanaika
- matti-virtualmachine <- laitteen nimi
- systemd <- tapahtuman pääluokka
- 881 <- systemd luokkaan kuuluvan tapahtuman koodi
- gpg-agent-ssh.socket: Succeeded. <- koodia ihmiselle selventävä teksti </br>


Kyseessä on daemon, jonka tehtävänä käsitellä ja puskuroida salasanoja avainnipussa. Eli tässä tapauksessa loki ilmoittaa puskuroinnin ja käsittelyn onnistuneen hyvin. Lokia oli tosi paljon, kaiken selvittämiseen menisi hyvin paljon aikaa. Ymmärsin pääsääntöisesti kaiken, mitä lokissa on, koska olen joutunut työssäni jonkin verran käymään läpi erilaisia lokitapahtumia.


### Auth.log
Seuraavana oli vuorossa Auth.log, joka saatiin auki käyttämällä samaa vanhaa less komentoa: </br>
![Kuva3](https://user-images.githubusercontent.com/122887740/215200357-20e05fdc-1ed5-42d6-b222-345e9bda90d6.png)</br>

```/var/log/auth.log``` <- sisältää kirjautumisiin liittyvän lokituksen.</br>
Esim. Jan 27 21:02:07 matti-virtualmachine lightmd pam-unix(lightdm-greeter:session): session opened for user lightdm (uid=117) by (uid=0)
- Kello: oikea, aikavyöhyke EET +2
- Jan 27 21:02:07 <- Tapahtumanaika
- matti-virtualmachine <- laitteen nimi
- lightdm<- tapahtuman pääluokka
- pam_unix(lightdm-greeter:session) <- lightdm luokkaan kuuluva aliluokka
- session opened for user lightdm (uid=117) by (uid=0) <- koodia ihmiselle selventävä teksti</br>
Lokia ei ollut paljon, koska virtuaalikoneella ei ole kauheasti vielä historiaa.


Loki sisältää tietoa siitä, että lightdm käyttäjällä on käynnistetty juurikäyttäjän uid=0 toimesta lightdm-greeter:session palvelu, joka tuo näkyviin käyttäjälle kirjautumisikkunan koneen käynnistyessä.

Kuvasta näkee Sudo logit: </br>
![Kuva4](https://user-images.githubusercontent.com/122887740/215260043-55e91b5e-bf26-4600-a3b4-de03e6be4864.png)</br>


### Apache2 Access.log & Error.log

Generoidakseni lokia kansioon ```/var/log/apache2/```, tuli minun ensin asentaa Apache2 rooli koneelle käyttämällä komentoa ```sudo apt get install apache2```.
Generoin lokia lokiin ```/var/log/apache2/access.log``` käyttämällä Mozilla Firefox selainta ja kirjoittamalla osoiteriviin ```localhost:80```, sain tulokseksi seuraavan: </br>
![Kuva5 5](https://user-images.githubusercontent.com/122887740/215259449-779b0cc4-58f5-44e9-ba6c-04b3951c45e7.png)</br>

Vierailuni omalla sivullani generoi Access.log tiedostoon dataa:</br>
![Kuva5](https://user-images.githubusercontent.com/122887740/215201088-faf90857-c75e-4056-979a-d66c5987a8cc.png)</br>

```/var/log/apache2\access.log``` <- sisältää sivustolla tapahtuvien yhteyksien lokituksen.</br>
- 127.0.0.1 - - [Jan 27 21:29:25 +0200] "GET / HTTP/1.1" 200 3380 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0"
- Kello: oikea, aikavyöhyke (+0200) EET +2
- Jan 27 21:29:25 <- Tapahtumanaika
- "GET / HTTP/1.1" <- sivuston tarjoama HTTP säännöstö
- "200 3380" <- Success code, eli sivusto latautui onnistuneesti
- "Mozilla/5.0 (X11; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0" <- tämä kertoo selaimen version ja millä alustalla selainta ajetaan.</br>
Lokia ei ollut paljon, koska virtuaalikoneella ei ole aiemmin vielä hostattu Apache2 webpalvelinta
Loki pitää sisällään tietoa siitä mitä kaikkea kirjautuneessa sessiossa on tapahtunut ja miten kävi.

Access.log tiedoston jälkeen päätin vielä kurkata Error.log tiedostoa: </br>
![Kuva6](https://user-images.githubusercontent.com/122887740/215201523-a7e058ea-e045-479f-90fa-dc3c31570430.png)</br>

```/var/log/apache2\error.log``` <- sisältää Apache2 web-palvelimen toimintaan liittyvien virheiden lokituksen</br>
[Fri Jan 27 21:29:15.667234 2023] [mpm_event:notice] [pid 2509:tid 139778747977024] AH00489: Apache/2.4.54 (Debian) Configured -- resuming normal operations
- Kello: oikea, aikavyöhyke (+0200) EET +2
- Jan 27 21:29:15 <- Tapahtumanaika
- [mpm_event:notice]: tapahtuman tyyppi
- [pid 2509:tid 139778747977024]: pid = process identifier & tid = thread identifier
- AH00489: Apache/2.4.54 (Debian) Configured -- resuming normal operations = Apache2:n lokitukseen liittyvä koodi, tässä tapauksessa kyseessä on perus operointiin liittyvä koodi. Samassa rimpsussa näkee myös Apachen version 2.4.54.</br>

Tässä lokissa tulee selville Apache2-palvelun operoinnin jatkumisesta normaalitilassa.


Lokia ei ollut paljon, koska virtuaalikoneella ei ole aiemmin vielä hostattu Apache2 webpalvelinta.
Loki pitää sisällään tietoa siitä mitä kaikkea kirjautuneessa sessiossa on tapahtunut ja miten sen kävi.

Lopetin hommat perjantaina 27.1.2023 klo 21:55.

## Itse aiheutettujen lokimerkintöjen luonti
Hommat jatkuivat seuraavana aamuna lauantaina 28.1.2023 klo 11:06.</br>
Tässä tehtävässä oli tarkoitus generoida lokitiedostoon tahallisesti lisälokia.
Ensiksi päätin kokeilla, miltä näyttää tahallisesti väärin kirjoitettu salasana kirjautumisruudussa: </br>
![Kuva7](https://user-images.githubusercontent.com/122887740/215258286-ff3242c9-13c6-4da4-b2f0-9f04b388ae20.png) </br>
Kuten kuvasta näkee, syslogiin on kirjattu parikin merkintää. Ensimmäinen generoitui kirjautumisruudussa ja toinen tuli, kun ```sudo su``` komentoa käyttäessä kirjoitin tahallisesti väärän salasanan ensiksi. </br>

Kohde: Jan 28 11:06:59 matti-virtualmachine sudo: pam unix(sudo:auth): authentication failure; logname= uid=1000 euid=0 tty=/dev/pts/0 ruser=mattis rhost= user=mattis</br>
- Kello: oikea, aikavyöhyke EET +2
- Jan 28 11:06:59 <- Tapahtumanaika
- matti-virtualmachine <- laitteen nimi
- sudo <- tapahtuman pääluokka
- pam unix(sudo:auth) <- sudo luokkaan kuuluva alaluokka
- authentication failure; logname= uid=1000 euid=0 tty=/dev/pts/0 ruser=mattis rhost= user=mattis <- itse tapahtuma, tässä näkee autentikoinnin epäonnistumisen, lokinimen, uid (unique identifier):n, euid=1000: Effective UID (prosessia ajavan käyttäjän ID), ruser=mattis: koneelle kirjautunut käyttäjä, rhost=: näyttää liittyvän etähallintaan (remote host), user=mattis: itse käyttäjä.

Lokitieto pitää sisällään epäonnistuneen autentikoinnin siitä, kun käyttäjä mattis on yrittänyt ajaa komennon ```sudo su``` ja syöttänyt väärän salasanan.


Seuraavaksi yritin generoida virheilmoitusta sammuttamalla Apache2:n palvelun (```/etc/init.d/apache2 stop ```), mutta se ei tuottanut tulosta:</br>
![Kuva8](https://user-images.githubusercontent.com/122887740/215258288-b2ac3674-0544-4831-b863-8e42b71a1971.png)</br>

Koitin myös generoida lokia koittamalla sammuttaa Apache2 palvelun ilman juurioikeuksia:</br>
![Kuva9](https://user-images.githubusercontent.com/122887740/215258292-33bd1282-b807-4468-b8c0-e6235df3c863.png)</br>
Ensimmäisestä juurioikeuksilla tehdystä palvelun sammutuksesta / uudelleenkäynnistyksestä tuli vain onnistunut merkintä lokiin. Toisesta yrityksestä ei tullut lokiin mitään merkintää, tutkin asiaa netistä ja siellä mainittiin, että lokin ilmestymistä varten tulisi ottaa käyttöön laajempi audit esim. kolmannen osapuolen lisäosalla.


## Lopetus
Kyseinen tehtävä avasi lokituksen maailmaa toisesta näkökulmasta, koska olen tutkinut lokeja graafisella käyttöliittymällä esim. Windowsissa ja siellä ne näyttävät erilaiselta. Toki pitää muistaa, että kaikki lokit maailmassa onneksi käyttävät samanlaista runkoa, eli jos osaat jotain lokia lukea, niin osaat lukea myös toista lokia. Hommiin meni tällä erää n. 1,5h.

## Lähteet:
Carl Tashian, 03.12.2021, How to Handle Secrets on the Command Line: 
https://smallstep.com/blog/command-line-secrets/

Archlinux Wiki, 14.12023, GnuPG: https://wiki.archlinux.org/title/GnuPG

Archlinux Wiki, 27.1.2023, LightDM: https://wiki.archlinux.org/title/LightDM

How to set up a localhost server with http protocol on apache [closed]:
https://unix.stackexchange.com/questions/347532/how-to-set-up-a-localhost-server-with-http-protocol-on-apache

Wesley Chai & Kevin Ferguson, March 2021, HTTP (Hypertext Transfer Protocol):
https://www.techtarget.com/whatis/definition/HTTP-Hypertext-Transfer-Protocol

Difference between PID and TID:
https://stackoverflow.com/questions/4517301/difference-between-pid-and-tid

Difference between EUID and UID?:
https://stackoverflow.com/questions/27669950/difference-between-euid-and-uid
