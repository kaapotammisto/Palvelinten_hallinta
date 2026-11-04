1A) Lataa Debian.
  Kopioitu tehtävästä h0 missä latasin debianin -
 Linux debianin asennun virtualboxiin
- Koneena Mac Pro M2 silikonilla. Latasin arm64 version koska M2 on Apple silicon.
- Mukautin debian vb asetukset alunperin väärin, tajusin että Terokarvinen.com löytyy tarkat ohjeet, seurasin niitä.
-Asennuksessa kesti noin 10min
- Terminal emulaattorissa sudo apt-get -y disc-upgrade kaikkien toimintojen päivittämiseen ja palomuurin asennus
-Viimeisenä VirtualBox Guest additions

1B) SSH - publickey - sisäänkirjautuminen ilman salasanaa
   - Lataa SSH komennolla sudo apt-get update ja sudo apt-get -y install ssh
   - kokeile SSH yhteys
   - tee avainpari komennolla ssh-keygen
   - Kopioi avainpari ssh-copy-id localhost. Boom nyt Pitäisi toimia ilman salasanaa.
Varmistin vielä kokeilemalla ottaa yhteyden itseeni komennolla ssh kaapo@127.0.0.1
  
  1C) PUBKEY
Avainparin olin jo luonut komennolla ssh-keygen -t id....
  Senjälkeen tuli vuoro automatisoida se ansiblella johon tein uuden micron eli tekstitiedoston nimellä pubkey.yml
  Pubkeyn sisälle tuli hosts, connection joka oli local, tasks, komento nimi ja julkinen avaimeni, ansible.posix.authorized_key, käyttäjäni, avain ja state presenttiin.
  Sitten kokeilin ajaa tämän komennolla ansible-playbook pubkey.yml. Aluksi minulle tuli ongelma ettei koemnto toimi, menin katsomaan tiedoston uudelleen ja huomasin että userin sijasta olin kirjoittanut usr, vaihdoin tämän, ajoin komennon uudelleen ja se toimi.
  Testasin myös ottaa yhteyden komennolla ssh kaapo@127.0.0.1 ja pääsin sisään ilman salasanaa.

  1D) Hei Ansible 
Tein hei maailma tehtävän Ansiblella ja ajoin sen SSH:n yli. Ohjeena tässä toimi mainiosti Tero Karvisen sivu Hello Ansible.
  Koska SSH piti toimia ensin ilman Ansiblea, tämän olin kokeillut tehtävän b ja c kohdissa, ja localhost pääsi sisään ilman salasanaa.
  Ansiblen asensin komennolla sudo apt install ansible.

Tein kansion ~/ansible ja sen sisälle neljä tiedostoa jotka olivat ansible.cfg = sinne meni yleiset asetukset, ja kertoo mistä inventory löytyy, hosts.ini: = lista hallittavista koneista mutta tässä tehtävässä se oli vain localhost, site.yml: = kertoi, mitkä koneet saa mitkä roolit ja roles/hello/tasks/main.yml: = roolin koodi eli sen varsinainen tehtävä.
  Tiedoston sisällöistä laitan kuvat erikseen, sillä en ole saanut niitä toimivaan tässä tiedostossa. Ne löytyvät kuitenkin liitettynä.
Rooli käyttää kopiointia joka luo palvelimelle tiedoston /tmp/hello-ansible
  Ajoin playbookin ansible-playbook site.yml joka ei aluksi toiminut, sillä tiedostossa oli kirjoitusvirheitä, korjasin ne ja ne toimivat. Sitten tarkistin SSH:lla komellonna ssh localhost 'cat /tmp/hello-ansible'. Tiedosto toimi joten Ansible toimi SSH:n yli.
  Ajoin vielä impotenssin toisen kerran ja changed oli tällä kertaa 0. koska tiedosto oli paikassa mihin halusin sen eikä Ansible tehnyt mitään. Se oli tarkoitus. Ansible muuttaa vain sen mikä ei vastaa sitä vielä.

  Ongelmia: Ekana No inventory was parsed viesti. Syy siihen oli että ansible.cfg:ssä luki väärin ja kävin korjaamassa kirjoitusvirheen.
  'the role of hello colum7 was not found' = Olin jättänyt site.yml riviin ylimääräisen tekstinm, jonka takia Ansible etsi roolia väärältä riviltä.l

Lähteet -
 Karvinen, T. S.a. Luettavissa:https://terokarvinen.com/passwordless-sudo-with-ansible/. Luettu 2.11.2026
 Karvinen, T. S.a. Luettavissa:https://terokarvinen.com/hello-ansible/. Luetti 4.11.2026
Karvinen, T. S.a. Luettavissa:https://terokarvinen.com/palvelinten-hallinta/#laksyt. Luettu 3.11.2026
Claude - prompt - miksi site.yml ei toimi? 
