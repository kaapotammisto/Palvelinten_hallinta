X) Karvinen 2026: https://terokarvinen.com/apache-ansible/.
-Apache2 asennus Ansiblella.
- Asenna paketti, muokkaa asetustiedot ja käynnistä uudestaan eli package-file-service
- selkeytettynä -> asentaa apache2, kopioi oman asetustiedoston ja ottaa sivuston käyttöön linkillä.
- asetukset tulevat voimaan vasta uudelleen käynnistymisen jälkeen.

  X) Ansible Community Documentation: https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_handlers.html
  - Ansible käyttää 'käsittelijöitä' jotka tekevät tehtävän vain, jos siitä ilmoitetaan
  
  *Handlers - Käsittelijät eivät suorita tehtävää jos esim muutoksia ei konfiguroida jossakin tiedostossa. Tehtävä suoritetaan jos tiedostoa muokataan.
  * Notifying handlers - Yhdellä komennolla voi ottaa yhteyden moneen käsittelijään, ei tarvitse ilmoittaa kaikille erikseen tehtävästä.
  
  * Ansible-doc-test - Ansiblen mukana tuleva dokumentaatiotyökalu
  * enabled - avautuuko palvelu heti käynnistyessä
  * name - Palvelun nimi
  * state - kontrolloi palvelun operatiivista statusta remote hostissa
  * EXAMPLES - esimerkkejä handlereista. Kopioitu ensimmäinen handler " name: Start service httpd, if not started
  ansible.builtin.service:
    name: httpd
    state: started"

  #1Y) Etsi hacker newsista uutinen palvelimen hallinnasta. https://hayzam.com/blog/03-lying-to-zeds-remote-editor/
  - Postaaja sai Zed editorin-etäkehyksen toimimaan FreeBSD palvelimella vaikka Zed ei virallisesti tue sitä.
  - Samalla FreeBSD:n ytimestä löytyi virhe, joka koski estoa hakemistojen listauksesta
  - Postaaja korjasi virheen
 
  #1A) Asenna Apache2 käsin
  - Ensiksi ohjelmien päivitys sudo apt update
  - seuraavaksi sudo install -y apache2
  - tein kansion komennolla mkdir ~/publicsite/index.html ilman sudoa, se toimi
  - Kirjoitin asetustiedoston /etc/apache2/site-available/publicsite.conf.
  - Sen sisällä virtual host jonka DocumentRoot osoittaa kansiooni.
  - Otin sivuston käyttöön komennolla a2ensite publicsite ja oletussivu poistettu käytöstä.
  - Varmistin asetukset komennolla apache2ctl configtest ennen uusiksi käynnistystä.
  - Ensimmäinen curlaus antoi 403 virheen. Korjattu komennolla chmod o+x ~(sallii kansion läpikulun muille mutta ei listausta)
  - Seuraavaksi muokkasin sivua tavallisena käyttäjänä, sekin onnistui
  - Uutta mitä opin oli että asetustiedoston muutokset tulevat voimaan vasta kun palvelu käynnistetään uudelleen, sekä configtest kannattaa ajaa ennen sitä.
    

   - <img width="628" height="484" alt="Screenshot 2026-11-14 at 17 27 22" src="https://github.com/user-attachments/assets/c1c3cfe7-5b7f-4a67-9362-8fd23e5f65ef" />
   - <img width="1256" height="968" alt="image" src="https://github.com/user-attachments/assets/c0ac2536-fd4d-44a5-a436-52a5ec1c58e9" />
   - <img width="1120" height="484" alt="Screenshot 2026-11-14 at 17 41 25" src="https://github.com/user-attachments/assets/f99c008d-041e-4077-b62e-963bae8ec111" />

#1B) Moottorix. Asenna Nginx käsin, 

-Ensimmäisenä tuli pysäyttää Apache. Sen ajoin alas komennoilla sudo systemctl stop apache2, sudo systemctl disable apache2. systemctl status näytti inactive ja disabled eli pysäyttäminen toimi.
- Nginx asennettu sudo apt update ja sudo apt install y- Nginx komennoilla
- Ennenkuin tein mitään, katsoin curlilla että Nginxin oletussivut toimii
- Käytin samaa kansiota kuin Apachessa (~/publicsite/index.html) ja kirjoitin sinne uuden tekstin ilman sudoa
- tein asetustiedoston /etc/nginx/sites-available/publicsite.
- Sivusto tuli käyttöön kun tein linkin sites-enabled kansioon ja poistin oletussivun linkin.
- Tein tuossa aluksi virheen kun kirjoitin linkin kohteeksi sites-available ja tuli ilmoitus file exists. Korjasin komennon kansioon sites-enabled
- tarkistin asetukseni komennolla sudo nginx -t Alla kuvakaappaus. Käynnistin Nginxin uudelleen.
- Testasin sivua, ja minulla näkyi vieläkin oletussivu. Korjasin sen uudelleenkäynnistämisellä taas
- Seuraavaksi muokkasin sivua ilman sudo käyttäjää ja uudelleenkäynnistin. Curlasin sen ja huomasin että se päivittyi. Kuva alla
  

-<img width="670" height="138" alt="Screenshot 2026-11-15 at 15 46 48" src="https://github.com/user-attachments/assets/e2898370-af8e-4b37-9db5-2c863f8b39bb" />
- <img width="670" height="750" alt="Screenshot 2026-11-15 at 15 32 21" src="https://github.com/user-attachments/assets/bc62a703-4226-41ba-9089-887f5473deb0" />
-<img width="670" height="118" alt="Screenshot 2026-11-15 at 15 54 08" src="https://github.com/user-attachments/assets/ced88b0a-7635-46af-b926-fd0bd71bf18c" />

#1C) Automoottorix. Automatisoi Nginx.

-Automatisoin asennuksen ja asetukset Ansible roolilla roles/nginx. Tehtävä oli samantyylinen kuin 1B.
- Roolin tehtävät teki saman minkä tein 1B:ssä käsin. Ensin Apachen pysäytys jotta Nginx pääsee porttiin 80
- kopioin tiedoston kohteeseen /etc/nginx/sites-available/publicsite ja lopuksi linkin teko sites-enabled kansioon, poistin oletussivun linkin ja varmistin palvelun käynnissäolon
- Ensimmäisellä ajolla minulle tuli YAML virhe. Olin laittanut väärän määrän välilyöntejä joten korjasin sen.
- Sitten kun ajoin uudestaan se kaatui handleriin kun Ansible yritti käynnistää sitä uudestaan.
- Tässä kohtaa kysyin apua Claudella promptilla mikä meni vikaan?. Claude löysi syyksi että olin unohtanut kirjata [::] jälkeen portin 80.
- Korjauksen jälkeen ajoin ansiblen ja nyt se toimi. Curlauksesta näkyi oma sivuni, ajoin Ansiblen uudestaan ja nyt tuli changed 0. Alta löytyy liitteitä.

-<img width="740" height="345" alt="Screenshot 2026-11-16 at 18 17 17" src="https://github.com/user-attachments/assets/7065f4cd-2b9f-4728-8b78-b97650e52245" />
-<img width="688" height="570" alt="Screenshot 2026-11-16 at 18 21 38" src="https://github.com/user-attachments/assets/6d14df0c-4c34-4efd-a422-291112de2255" />
-<img width="688" height="450" alt="Screenshot 2026-11-16 at 18 23 13" src="https://github.com/user-attachments/assets/6ef3cad8-ffa0-43ff-9260-5b5c9dd42a7c" />
-<img width="670" height="199" alt="Screenshot 2026-11-16 at 18 08 50" src="https://github.com/user-attachments/assets/0cabc2c8-dc33-41d9-86db-17afca824b7f" />
<img width="670" height="223" alt="Screenshot 2026-11-16 at 18 07 36" src="https://github.com/user-attachments/assets/a6b6e825-0b9b-49a4-8ceb-18914f5d3bb1" />

Lähteet: 
https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_handlers.html
https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_handlers.html#notifying-handlers
https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-debian-11
https://medium.com/@khushim648/automating-nginx-server-setup-with-ansible-on-rhel-205ba19b9e80
https://builder.aws.com/content/3De3wpktkg1mTQQBg6esmnL0VX4/ansible-playbooks-automating-nginx-web-server-configuration
https://docs.ansible.com/projects/ansible/latest/collections/ansible/builtin/service_module.html
Claude promp - Mitä laitan files publicsiten sisälle?
Claude promp - Miksi ajo ei onnistunut? Löydätkö sinä vian?
https://docs.vultr.com/how-to-install-nginx-webserver-on-debian-12



  
