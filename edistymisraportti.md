## Edistymisraportti
###### Päivitetty 18.4.2016
#### Linux-projekti (ICT4TN018-6)

### Virtuaaliympäristön käyttöönotto

Aloitetaan ottamalla Vagrant-provisiointi käyttöön, johon teemme sovelluksen valmiiksi. Valmis sovellus lopuksi siirretään virtuaalipalvelimelle. Tällöin testiympäristössä työskentely on turvallisempaa, jottei keskeneräisen projektin aikana Wordpress-sovellukseen jää mahdollisia tietoturva-aukkoja.

Aloitetaan projekti asentamalla Vagrant työasemalle. Työasemana toimii HP Elitebook 2560p -kannettava tietokone.

	$ sudo apt-get install vagrant virtualbox

Luodaan uusi Vagrant laatikko, eli määritellään minkä käyttöjärjestelmän haluamme ladata virtuaalitietokonettamme varten. Lataamme 64-bittisen Ubuntu palvelinkäyttöjärjestelmän, koska asennamme kyseisen käyttöjärjestelmän varsinaiselle virtuaalipalvelimelle myöhemmin työn edetessä.

	$ vagrant box add ubuntu/trusty64

Latauksen jälkeen annetaan init-komento, jolloin Vagrant tekee määritykset valmiiksi.

	$ vagrant init ubuntu/trusty64

Käyttäjän kotihakemistoon on luotu boxin yhteydessä Vagrantfile-tiedosto, jossa on kaikki konfiguroinnit. Muokataan virtuaalikoneemme verkko-osoitetta.

	$ nano Vagrantfile

Muokataan tiedoston verkko-osoite asetus seuraavanlaiseksi (tiedoston rivi 29).

	 config.vm.network "private_network", ip: "10.0.0.20"

Nyt meillä on valmis Vagrant-virtuaalikone ja sille on määritelty haluamamme verkko-osoite. Tietokoneemme pitää olla tällöin käyttövalmiina. Käynnistetään virtuaalikone ja otetaan siihen ssh-yhteys.

	$ vagrant up && vagrant ssh
	
	# Hetken ajan päästä saamme ssh-yhteyden koneeseen.
	
	$ vagrant@vagrant-ubuntu-trusty-64:~$

Meillä on täten valmis virtuaalikone valmiina, jota käytämme Wordpress-sovelluksemme asennusta ja konfigurointia varten.
