## Edistymisraportti
###### Päivitetty 18.4.2016
#### Linux-projekti (ICT4TN018-6)

####Tärkeät ilmoitukset
	18.4.2016: Virtuaalipalvelimen käyttöönotossa on ollut ongelmia. Vuokraamani virtuaalipalvelimeni käyttäjän salasana on unohtunut, jolloin jouduin asentamaan palvelimeen Ubuntun uudelleen. Tällöin projektityön valitavoite jää vajaaksi suunnitelmasta. Palvelin kovettaminen on tällöin toteuttamatta. Muutoin projekti on edennyt odotuksien mukaisesti. Palvelimen kovettaminen toteutetaan viikkoon 17 mennessä dokumentaation kera.

###1 Virtuaaliympäristön käyttöönotto

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


###2. LAMP-sovelluspinon asennus Vagrantille

Aloitetaan asentamalla kaikki LAMP-pinon vaativat paketit.

	$ sudo apt-get update
	$ sudo apt-get install apache2 php5 php5-mysql mysql-server

Saamme MySQL:n asennusikkunan, joka pyytää MySQL:n pääkäyttäjälle salasanaa. Luodaan uusi vahva salasana ja painetaan enter, jolloin paketin asennus suoriutuu loppuun.

####2.1 Apachen konfigurointi

Ensiksi varmistetaan, että Apache asentui koneelle onnistuneesti.

	$ dpkg -s apache2|grep installed
	
	$ Status: install ok installed

Aloitetaan poistamalla ensiksi /var/www/html -hakemisto, jossa Apache pyörittää sivuja tällä hetkellä. Haluamme kehittää web-sivuja käyttäjän kotihakemistossa, jolloin emme tarvitse pääkäyttäjäoikeuksia, joita nykyinen hakemisto vaatisi.

	$ sudo rm -R /var/www/html
	
Otetaan käyttäjien kotihakemistot käyttöön.

	$ sudo a2enmod userdir
	$ sudo service apache2 restart

Mennään kotihakemistoomme (käyttäjänimemme on vagrant) ja luodaan sinne alihakemisto "public_html".

	$ mkdir ~/public_html

####MySQL:n konfigurointi ja tietokannan luonti

Olemme asentaneet mysql-server -nimisen paketin ja annoimme pääkäyttäjälle salasanan. Asennetaan uusi tietokanta ja ajetaan turvallinen asennus (Secure installation).

	$ sudo mysql_install_db && sudo mysql_secure_installation


