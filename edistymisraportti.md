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
	
	Status: install ok installed

Aloitetaan poistamalla ensiksi /var/www/html -hakemisto, jossa Apache pyörittää sivuja tällä hetkellä. Haluamme kehittää web-sivuja käyttäjän kotihakemistossa, jolloin emme tarvitse pääkäyttäjäoikeuksia, joita nykyinen hakemisto vaatisi.

	$ sudo rm -R /var/www/html
	
Otetaan käyttäjien kotihakemistot käyttöön.

	$ sudo a2enmod userdir
	$ sudo service apache2 restart

Mennään kotihakemistoomme (käyttäjänimemme on vagrant) ja luodaan sinne alihakemisto "public_html".

	$ mkdir ~/public_html


####2.2 MySQL:n konfigurointi ja tietokannan luonti

Olemme asentaneet mysql-server -nimisen paketin ja annoimme pääkäyttäjälle salasanan. Asennetaan uusi tietokanta ja ajetaan turvallinen asennus (Secure installation).

	$ sudo mysql_install_db && sudo mysql_secure_installation

MySQL asensi tietokannan js seuraavaksi on vuorossa konfigurointi. Annetaan MySQL:n pääkäyttäjän salasana ja teemme seuraavanlaiset valinnat.

	NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MySQL
	SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!


	In order to log into MySQL to secure it, we'll need the current
	password for the root user.  If you've just installed MySQL, and
	you haven't set the root password yet, the password will be blank,
	so you should just press enter here.

	Enter current password for root (enter for none): 
	OK, successfully used password, moving on...

	Setting the root password ensures that nobody can log into the MySQL
	root user without the proper authorisation.

	You already have a root password set, so you can safely answer 'n'.

	Change the root password? [Y/n] n
	 ... skipping.

	By default, a MySQL installation has an anonymous user, allowing anyone
	to log into MySQL without having to have a user account created for
	them.  This is intended only for testing, and to make the installation
	go a bit smoother.  You should remove them before moving into a
	production environment.

	Remove anonymous users? [Y/n] y
	 ... Success!

	Normally, root should only be allowed to connect from 'localhost'.  This
	ensures that someone cannot guess at the root password from the network.

	Disallow root login remotely? [Y/n] y
	 ... Success!

	By default, MySQL comes with a database named 'test' that anyone can
	access.  This is also intended only for testing, and should be removed
	before moving into a production environment.

	Remove test database and access to it? [Y/n] y
	 - Dropping test database...
	ERROR 1008 (HY000) at line 1: Can't drop database 'test'; database doesn't exist
	 ... Failed!  Not critical, keep moving...
	 - Removing privileges on test database...
	 ... Success!

	Reloading the privilege tables will ensure that all changes made so far
	will take effect immediately.

	Reload privilege tables now? [Y/n] y
	 ... Success!

	Cleaning up...



	All done!  If you've completed all of the above steps, your MySQL
	installation should now be secure.

	Thanks for using MySQL!

Kirjaudutaan MySQL:ään pääkäyttäjällä.

	$ mysql -u root -p

Luodaan Wordpressiä varten uusi tietokanta ja sitä varten käyttäjä, jolle annamme täydet oikeudet luomaamme tietokantaan.

HUOM! Antamamme salasana ei ole virallinen salasana, vaan esimerkki tarpeeksi hyvästä salasanasta.

	mysql> CREATE DATABASE ATKINS_SIVU;
	Query OK, 1 row affected (0.00 sec)

	mysql> CREATE USER 'atkins'@'localhost' identified by 'Rt#2Perunapiirakka7!hyva';
	Query OK, 1 row affected (0.00 sec)

	mysql> GRANT ALL ON ATKINS_SIVU.* TO 'atkins'@'localhost' IDENTIFIED BY 'Rt#2Perunapiirakka7!hyva';
	Query OK, 1 row affected (0.00 sec)

Poistutaan MySQL:stä "exit"-komennolla.


####2.3 PHP5:n konfigurointi

Aloitetaan ottamalla PHP5 käyttöön Apachen puolella muokkaamalla Apachen PHP-konfiguraatiotiedostoa.

	$ sudoedit /etc/apache2/mods-enabled/php5.conf

Kommentoidaan tiedoston viimeisimmät rivit seuraavanlaisesti:

	# Running PHP scripts in user directories is disabled by default
	#
	# To re-enable PHP in user directories comment the following lines
	# (from <IfModule ...> to </IfModule>.) Do NOT set it to On as it
	# prevents .htaccess files from disabling it.
	#<IfModule mod_userdir.c>
	#    <Directory /home/*/public_html>
	#        php_admin_flag engine Off
	#    </Directory>
	#</IfModule>

Käynnistetään Apache uudelleen.

	$ sudo service apache2 restart

Testataan php:n toimivuus palvelin puolella tekemällä "public_html" -hakemistoon pieni php-skripti, joka suorittaa kertolaskun.

	$ nano ~/public_html/kertolasku.php

Kirjoitetaan tiedostoon php-skripti:

	<?php
		print(20 * 5);
	?>

Mennään verkkosivuille ja tarkastetaan seuraavan php-skriptin toimivuus työasemalta.

	http://10.0.0.20/~vagrant/kertolasku.php

Sivuilla pitäisi olla tulostettuna "100" ja näin myös onkin. Täten php toimii palvelimen puolella.

LAMP-sovelluspino on asennettu onnistuneesti virtuaalipalvelimelle. Käytämme myöhemmin samoja käytäntöjä, kun teemme asennuksen varsinaiselle palvelimelle viikkoon 17 mennessä.


###3 Wordpressin asentaminen

Aloitetaan lataamalla Wordpressin asennuspaketti käyttäjämme kotihakemistoon "public_html" -hakemiston alle ja puretaan se sinne.

	$ wget http://wordpress.org/latest.tar.gz
	$ tar xf latest.tar.gz

Hakemistoon ilmestyy "wordpress"-hakemisto. Tämän jälkeen suoritetaan Wordpressin asennus työasemalta.

	http://10.0.0.20/~vagrant/wordpress 

Sivusto pyytää käyttäjätunnuksemme MySQL-tietokantaan ja tietokannan nimen, jonne Wordpressin tiedot tallennetaan. Käytämme luomamme "ATKINS_SIVU"-kantaa ja käyttäjäksi "atkins". Taulukoiden etuliite muutetaan tietoturvasyistä (automatisoidut hyökkäykset) wp_:stä df_:ksi.

Asennus etenee ja nyt se ilmoittaa ettei "wp-config.php" -tiedostoa löydy. Tällaista ei ole luotu Wordpressin asennuksen yhteydessä, joten luomme sen itse "wordpress"-hakemiston alle. Ilmoituksessa oleva koodi kopioidaan tekemäämme tiedostoon.

	$ nano wordpress/wp-config.php

Tämän jälkeen jatketaan asennusta. Seuraavaksi ongelmia ei ilmennyt ja annamme sivustollemme otsikon ja pääkäyttäjän nimen ja salasanan.

	Title: Atkins ry
	Käyttäjänimi (ei virallinen): atkinsadmin
	Salasana (ei virallinen): 20k_pr4/latintorTTU39#!

Suoritetaan asennus loppuun ja pääsemme kirjautumaan Wordpress-sivuillemme.


###4 Ylläpitosuunnitelma

Sivuston ylläpitämistä varten on luotu ylläpitosuunnitelma, jota tullaan täydentämään projektin edetessä.

####4.1 Ylläpitokäytännöt

Palvelimen ja Wordpress-sivun ylläpitokäytäntöihin kuuluu muun muassa päivitykset, jatkuva monitorointi, lokitiedostojen analysoiminen ja hyökkäysyritysten tunnistaminen ja estäminen. Ylläpidon helpottamiseksi projektin loppupuolella toteutetaan BPMN-prosessikaaviot, mitä tehdää missäkin ylläpitotehtävässä. Tällöin varmistetaan parhaiden käytäntöjen käyttäminen ja vältetään samalla virhetilanteita, esimerkiksi varmuuskopioiden unohtaminen.

Ajankäyttö on myös huomioitava ylläpidossa, koska koko projektista huolehtii ainoastaa yksi henkilö. Tällöin on varmistettava, että ylläpito on tarpeeksi tehokasta. Ylläpitokäytäntöjen on täten oltava mahdollisimman yksikertaisia ja nopeita, mutta samalla vaalia hyviä käytäntöjä ja huomioida samalla tietoturvanäkökulma. Ylläpitoprosessit on tällöin kuvatta tarkasti ja dokumentaation on oltava ajan tasalla, jotta kaikki tehdyt ylläpitotoimet ovat tallessa. Tällöin vikatilanteiden selvittäminenkin helpottuu, kun tiedetään tarkasti mitä muutoksia järjestelmään on tehty.

Ylläpidon jatkuvuus on huomioitava, mikäli projekti siirtyy toisen ihmisen tai ryhmän hoidettavaksi. Tällöin ensisijaisesti dokumentaation tulee olla kunnossa, joten dokumentaatio on huomioitava yhtenä ydinprosessina yläpidon näkökulmasta. Sen on oltava selkeää ja helposti tulkitavaa. Prosessikaavioiden selkeys on tärkeää, jotta työtä jatkava taho hahmottaa, miten palvelimesta ja Wordpressista on pidetty huolta. 

Ylläpitoprosessitkaan eivät ole koskaan valmiita, vaan osana ylläpitosuunnitelmaa on kehittää prosesseja tarpeen mukaan. Tällöin mittareina ovat lähinnä prosessien tehokkuus ja hyöty. Prosesseihin tapatuvat muutoksetkin tullaan dokumentoimaan selkeästi.


Lähteet:

http://cs.joensuu.fi/tSoft/yllapito.htm
