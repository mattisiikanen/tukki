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

Aloitus 27.1.2023 klo 21.00


/var/log/syslog <- löytyy käynnistyksestä, virta-asetuksiin, sisältää myös onnistumiset ja virheet. Toisin sanoen läjä, johon menee kategorisoimattomat järjestelmän tapahtumat.
Esim. Jan 27 21:02:23 matti-virtualmachine systemd[881]: gpt-agent-ssh.socket: Succeeded.
Kello: oikea, aikavyöhyke EET +2
Jan 27 21:02:23 <- Tapahtumanaika
matti-virtualmachine <- laitteen nimi
systemd <- tapahtuman pääluokka
881 <- systemd luokkaan kuuluvan tapahtuman koodi
gpt-agent-ssh.socket: Succeeded. <- koodia ihmiselle selventävä teksti
Lokia oli tosi paljon, kaiken selvittämiseen menisi hyvin paljon aikaa.
Ymmärsin pääsääntöisesti kaiken, mitä lokissa on, koska olen joutunut työssäni jonkin verran käymään läpi erilaisia lokitapahtumia.

/var/log/auth.log <- sisältää kirjautumisiin liittyvän lokituksen.
sim. Jan 27 21:02:07 matti-virtualmachine lightmd pam-unix(lightdm-greeter:session): session opened for user lightdm (uid=117) by (uid=0)
Kello: oikea, aikavyöhyke EET +2
Jan 27 21:02:07 <- Tapahtumanaika
matti-virtualmachine <- laitteen nimi
lightdm<- tapahtuman pääluokka
pam_unix(lightdm-greeter:session) <- lightdm luokkaan kuuluva aliluokka
session opened for user lightdm (uid=117) by (uid=0) <- koodia ihmiselle selventävä teksti
Lokia ei ollut paljon, koska virtuaalikoneella ei ole kauheasti vielä historiaa.
Loki sisältää tietoa siitä, että lightdm käyttäjällä on käynnistetty juurikäyttäjän uid=0 toimesta lightdm-greeter:session palvelu, joka tuo näkyviin käyttäjälle kirjautumisikkunan koneen käynnistyessä.

Kuvasta näkee Sudo logit: Kuva 4

Generoidakseni lokia kansioon /var/log/apache2/, tuli minun asentaa Apache2 rooli koneelle käyttämällä koodia sudo apt get install apache2.
Generoin lokia lokiin /var/log/apache2/access.log käyttämällä Mozilla Firefox selainta ja kirjoittamalla osoiteriviin localhost:80, sain tulokseksi seuraavan: 
Kuva 5.5

Lähteet:
Archlinux Wiki, 27.1.2023, LightDM (https://wiki.archlinux.org/title/LightDM)






## Lopetus
Kyseinen kotitehtävä avasi paljon komentokehotteen maailmaa Linuxissa. Sen avulla voi selvästi tehdä kaikki toimenpiteet, toki harjoitukset olivat vain pintaraapaisu, mutta kyllä niistä oli itselle ainakin hyötyä. Tehtäviin meni kokonaisuudessaan noin 1,5h.


## Lähteet:
Carl Tashian, 03.12.2021, How to Handle Secrets on the Command Line: 
https://smallstep.com/blog/command-line-secrets/
