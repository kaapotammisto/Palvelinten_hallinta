X) Tiivistä.
Karvinen 2026: https://terokarvinen.com/passwordless-sudo/.
Sudo ilman salasanaa.
- Ensiksi käyttäjän ja ryhmän luonti
- Uusi sääntö ryhmälle sudoless lupa käyttää sudoa ilman salasanaa.
- Testi SSH yhteydellä
- sudo K komento unohtaa tietosi
- Sudo ei kysynyt salasanaa? Tehtävä onnistui.

  Munroe 2006: xkcd 149: Sandwitch
  - Sarkastinen kuva sudon toiminnasta.
  - Henkilö kysyy kaveriltaan voisiko hän saada voileivän. Kaveri sanoo ei, jolloin henkilö käyttää sudoa kaveriinsa, suostuu tekemään leivän.
 
  Karvinen 2026: Passwordless Sudo with Ansible
  -Salasanaton sudokäyttö Ansiblella.
  - Tee uusi hakemisto roles/nimi/tasks.
  - muokkaa main.yml
  - sisällytä microon nimellä roles/nimi/tasks/main.yml groups, user, authorized key, ja kopiointi. Kirjoita sisältö
  - avaa site.yml -> lisää become true
  - aja Ansible-playbook site.yml. Jos valittaa sudo tarvitsee salasanan. Tehtävä tehty oikein.
 

1A) Tee ansiblea varten tunnus jota voi käyttää ilman salasanaa. 
- Tein ensiksi ryhmän ja käyttäjän komennoilla sudo adduser ja sudo addgroup. Nimeksi annoin kaapo, ja ryhmälle sudoless. Lisäsin kaapon sudoless ryhmään komennolla adduser kaapo sudoless.
- avasin root-shellin ja seuraavaksi oli säännön teko.
- Sitten oli uuden säännönteon aika. Tein visudolla tiedoston sudo visudo /etc/sudoers.d/sudoless
- visudon sisään komento %sudoless ALL = (ALL) NOPASSWD: ALL
- Tuon jälkeen kokeilin SSH yhteydellä. Otin yhteden komennolla SSH kaapo@localhost
- Sisällä ollessa komento sudo -k ja sudo echo "See you at TeroKarvinen.com"
- Kun minulta ei kysytty salasanaa, ymmärsin että passwordless sudo toimii.
- <img width="737" height="65" alt="Screenshot 2026-11-09 at 16 09 09" src="https://github.com/user-attachments/assets/4e02748b-5dba-42a4-ab65-8afbc43aa5e1" />


- 1B) Antero. Tee salasanaton, automaattisesti ssh:lla kirjautuva tunnus Ansiblella.
- Tein saman Ansiblella jotta se toimii automaattisesti
- Loin roolin /roles/kaapo/tasks/main.yml joilla on 4 tehtävää
- group luo ryhmän sudoless, user lisää käyttäjän kaapo ryhmiin sudoless ja sudo, authorized_key lisää julkisen avaimen, jolloin SSH-kirjautuminen onnistuu ilman salasanaa.
- Copy loi tiedoston /etc/sudoers.d/sudoless säännöllä %sudoless ALL = (ALL) NOPASSWD: ALL
- site.yml'ääm lisäsin become: true ja roolin kaapo
- kokeilin ensimmäistä ajoa komennolla ansible-playbook site.yml --ask-become pass koska NOPASSWD sääntöä ei vielä ollut
- testasin SSH yhteydellä ja sitten ohjeiden mukaisesti sudo -k ja sudo whoami että tuleeko root ilman salasanaa.
- Ajoin ansible-playbookin uudestaan ja tuli changed =0.
<img width="737" height="214" alt="Screenshot 2026-11-09 at 16 08 37" src="https://github.com/user-attachments/assets/3e42f759-5b96-4d5f-8472-02ef59768139" />

  Ongelmat - site.ymlissä oli vahingossa väärä rooli minkä korjasin, kaksoispiste puuttui groupista jonka takia aluksi se ei toiminut.

  1C) Package. asenna kaksi pakettia ansblella.
  
- tein roolin roles/paketit/tasks/main.yml
- rooli käyttää apt moduulia ja asentaa 2 pakettia jotka oli tree ja htop
- muokkasin asetukset. Laitoin state: present ja update_cache: true joka päivitti paketin ennen asetusta.
- Lisäsin roolin site.ymlään jossa become: true pakosti koska asennus vaati root oikeuden
- Ajoin Ansible-playbook site.yml.
- Tarkistin asennuksen komennolla dpkg -l
- Ajoin playbook in uudestaan ja changed näytti nollaa.
  

- 1D) File Kirjoita orjalle useamman rivin mittainen tiedosto Ansiblella.
  
  
- Tein roolin roles/tiedosto/tasks/main.yml
- rooli käytti copy moduulia joka loi tiedoston /tmp/ansible-tiedosto.txt ja kirjoittaa kolmirivisen tekstin
- teksti on kirjoitettu content: | muodossa
- omistajana kaapo, ryhmänä sudoless
- oikeudet 0640 eli tiedosto, read + write, read ja ei oikeuksia.
- katsoin ls -l /tmp/ansible-tiedosto.txt sisällön ja se näytti-rw-r----- 1 kaapo sudoless 91
- cat /tmp/ansible-tiedosto.txt näytti sisällön
- viimeiseksi sudo -u nobody cat /tmp/ansible-tiedosto.txt ja toisella ajolla changed oli 0.
- <img width="748" height="462" alt="Screenshot 2026-11-10 at 18 13 25" src="https://github.com/user-attachments/assets/af8580c6-dc59-44c5-8dbb-28db0fe86585" />
- <img width="727" height="742" alt="Screenshot 2026-11-10 at 17 59 51" src="https://github.com/user-attachments/assets/3a2f54c2-752e-4434-9341-4706bc0d197e" />
- <img width="727" height="460" alt="Screenshot 2026-11-10 at 18 02 44" src="https://github.com/user-attachments/assets/4d976e4a-66a6-4921-bea5-614b526ccac4" />

Lähteet:
https://terokarvinen.com/passwordless-sudo-with-ansible/
https://terokarvinen.com/passwordless-sudo/
Claude - prompt. Miten aloitan Package tiedoston kirjoittamisen?
https://www.cyberciti.biz/faq/how-to-install-htop-on-debian-linux-using-apt-get/
