# Projektisuunnitelma
##### Linux-projekti (ICT4TN018-6)

## Projektiorganisaatio

Projektin vetää ja toteuttaa Juha-Matti Ohvo. Toimeksiantajana toimii Haaga-Helian tietojenkäsittelyn ainejärjestö Atkins ry.

## Palvelinympäristö

	- DigitalOceanin virtuaalipalvelin
		- 512 MB RAM / 1 cpu
		- 20 GB SSD
		- 1 TB Transfer
	- Ubuntu Server 14.04.4 LTS

## Projektisuunnitelma

Kurssiprojektin tavoitteena on asentaa Wordpress-sisällönhallintajärjestelmä Ubuntu-pohjaiselle virtuaalipalvelimelle. Sivut tulevat Atkins ry:n viralliseen käyttöön, joten kyseessä on toimeksianto.
Sivuja on tarkoitus ylläpitää pidemmän aikaa, tarkemmin sanottuna vuosia.

## Tavoitteet

Projekti on jaettu kolmeen välitavoitteeseen. Ensiksi pystytetään palvelininfrastruktuuri ja asennetaan tarvittavat ohjelmat Wordpress mukaanlukien. Kaikki toimenpiteet dokumentoidaan tarkasti. Tässä välitavoitteessa panostetaan tietoturvaan ja palvelun toimivuuteen.

Toinen välitavoite on kartoittaa palvelun ylläpitosuunnitelma, eli kuinka palvelua tullaan ylläpitää. Tässä paneudutaan prosesseihin, joihin kuuluvat muun muassa varmuuskopiointi ja Wordpress-käyttäjien roolit (ylläpitäjät, käyttöoikeudet). Prosessien tarkoituksena on tehdä ylläåidosta joustavaa ja tehokasta.

Kolmas tavoite on testata Wordpress-sovelluksen ja virtuaalipalvelimenturvallisuus. Tulen tekemään tietoturvatestaamisen valmiilla työkaluilla, esimerkiksi niktolla. Testaaminen tullaan myös dokumentoimaan kattavasti. Lisäksi mahdollisuuksien mukaan palvelimen asennukset tullaan automatisoimaan keskitetyn hallinnan työkalulla, esimerkiksi Puppetilla.

## Aikataulu

Projektilla on valmiiksi mitoitettu aikataulu. Projekti toteutetaan Haaga-Helia ammattikorkeakoulun Linux-projekti -kurssin projektityönä. Kurssi alkoi viikolla 13 ja projektin on oltava valmiina viikolla 17. Viikolla 16 on tilannekatsaus.

Ennen tilannekatsausta ensimmäinen välitavoite on valmiina ja osa toisesta välitavoitteestakin on työstettynä tuohon aikaan mennessä. Viikkoon 17, eli deadlinea, mennessä koko projekti on valmis, jolloin voidaan puhua versiosta 1.0. Jatkuvuuden takia dokumentaatio ja versionhallinta tulee olemaan suuressa roolissa.

	Viikko 14:
	- Projektityöehdotuksen esittely ja työn aloitus
	
	Viikko 15:
	- Palvelimen ja sovelluksen asennus
		- Ubuntu asennettu virtuaalipalvelimelle
		- LAMP-sovelluspino asennettu

	- Palvelimen konfiguroitu hyvien tietoturvakäytäntöjen mukaisesti
		- SSH-avaimet
		- Palomuuri konfiguroitu avaamalla vain tarvittavat sovellusportit
		- SANS Linux Security Checklist

	Viikko 16:
	- Dokumentointi asennus ja konfigurointitoimeenpisteistä valmiina esiteltäväksi
	- Ylläpitosuunnitelma valmis

	Viikko 17:
	- Suoritetaan palvelimen ja Wordpress-sovelluksen tietoturvan testaamista
		- Wordpress skannataan mm. niktolla

	Viikko 18:
	- Dokumentaation viimeistelyä

	Viikko 19:
	- Projektin esittely
	- Palvelun ottaminen tuotantokäyttöön
