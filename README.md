# Mon Homelab



## Préambule 


Aujourdhui plus que jamais les services tiers auquels on a recours abusent de notre confiance et éxploitent nos données a grande échelle, ma réponse a ce propbléme est Mon homelab , bien qu'on ne puisse pas échapper a l'emprise de tout les services externes on peut en héberger une grande partie, baisser notre empreinte numérique et reprendre le controle sur nos données

Ici je vous présente de façon détaillé ma configuration et la façon dont j'héberge mes propres services chez moi avec un coût matériel et énérgetique moindre . 

----------------------------

## 1. ⚙ HARDWARE


Pour héberger des services il en faut peu on peut facilement recycler des vielles machines et leurs donner une seconde vie, les 3 grands critéres pour moi sonts Le coût ( a la consomation et en matériel), le bruit ( je veut une machine qui soit le plus silencieuse possible ) et la puissance (plus on a une machine puissante plus on peut héberger des services demandant et dans certains cas méme heberger des llms locaux) .

| Critère       | Exigence                                                                |
|---------------|-------------------------------------------------------------------------|
| **Coût**      | Consommation électrique + investissement matériel minimal               |
| **Bruit**     | Machine silencieuse (le homelab est dans ma chambre)                    |
| **Puissance** | Suffisante pour faire tourner plusieurs services, voire des LLMs légers |

Pour ma part j'ai choisi un Raspberry pi 4B avec 8gb de ram c'est un sbc assez puissant pour sa taille et sa consomation maximale de 15W ( une ampoule électrique a leds consome autant), avec ça j'ai fait un boitié en lego pour le style,j'ai mais un dissipateur sur le processeur puis j'ai branché un ventilateur en 5V sur les pins gpio et j'ai aussi branché un hdd en 2.5 pouce de 500 gb en usb avec un adaptateur sata -> usb pour avoir de l'espace de stockage en plus 

- **Raspberry Pi 4B** : 8 gb de ram processeur 4 coeur overclocké a 1.9 Ghz
- **Disque dur HDD***: disque en 2.5 pouce connecté avec un adaptateur sata vers usb pour 500 gb de stockage additionels
- **Ventilateur 5V générique**: connécté sur les pins gpio du Raspberry pour refroidir le tout avec un dissipateur sur le processeur
- **Boitier**: boitié fait en lego pour le style et la durabilité (je compte en imprimer un en 3D a l'avenir)
- **Switch** : TP-Link 5 ports 



### Galerie du matériel:

<div align="center">
  <img width="450" alt="Raspberry pi 4B avec un dissipateur" src="https://github.com/user-attachments/assets/9a0f9193-f69d-456d-8197-4bf9a9c7cb8a" />
  <br/>
  <em>Raspberry pi 4B avec un dissipateur</em>
</div>

<br/>

<div align="center">
  <img width="450" alt="Raspberry dans son boitié" src="https://github.com/user-attachments/assets/3ebc6061-be03-42d6-aa94-9b46c136076f" />
  <br/>
  <em>Raspberry dans son boitié</em>
</div>

<br/>

<div align="center">
  <img width="450" alt="Disque dur HDD 500GB" src="https://github.com/user-attachments/assets/7289e5e9-7f24-48fb-98de-50341dd81254" />
  <br/>
  <em>Disque dur HDD 500GB</em>
</div>

<br/>

<div align="center">
  <img width="450" alt="Ventilateur" src="https://github.com/user-attachments/assets/937519a2-8c0b-4c09-8528-8b1becd06205" />
  <br/>
  <em>Ventilateur</em>
</div>

<br/>

<div align="center">
  <img width="450" alt="montage complet" src="https://github.com/user-attachments/assets/db9dda38-f8cd-4892-8c0b-9a212cff51c3" />
  <br/>
  <em>montage complet</em>
</div>




Maintenant que notre machine est prête on va choisir un systeme d'éxploitation.


--------------------------------------------

## 💻 Spécification Software


Nous allons désormais chosir un systeme d'éxploitation selon nos besoins 

il nous faut une distribution linux légere, stable et sécurisé ce qui est essentiel pour héberger des services en continu

| Critère       | Exigence                                                                |
|---------------|-------------------------------------------------------------------------|
| **Stabilité** | il faut que sa tourne sans problemes sur ma machine sans avoir a toujours faire de la maintenance et sans planter quand on ne s'y attend pas  |
| **légereté**  | on a besoin d'un systeme conçu pour l'architécture ARM de mon prcesseur et qu'il puisse tourner sans avoir une grosse empreinte sur mes ressources                   |
| **Sécurité** | Mises a jours réguliéres  |

Si je veux de la stabilité et de la sécurité je dois m'orienter vers debian qui est reconnue et une trés grandes partie des distributions linux se basent dessus, J'avais devant moi 3 choix 

### Ubuntu Server

Distribution classique basée sur Debian, très connue dans le milieu du self-host.

**Le problème** : Ubuntu appartient à Canonical. Mon homelab est une démarche de **reprise de contrôle** sur mes données et mes outils. Remplacer la dépendance aux GAFAM par une dépendance à Canonical n'a pas de sens pour moi.

De plus, Ubuntu Server embarque par défaut des services comme `snapd` (gestion de paquets propriétaire) et `cloud-init` dont je n'ai pas besoin. Ce n'est pas "bloatware" dans le sens lourd, mais c'est une **complexité inutile** pour un petit Raspberry Pi.

> ❌ **Verdict : trop éloigné de ma philosophie "minimalisme et contrôle"**

### Raspberry Pi OS

Distribution officielle de la fondation Raspberry Pi, basée sur Debian.

**Le problème** : Même en version Lite (sans interface graphique), Raspberry Pi OS est construit sur une base qui reste orientée "bureau". Cela se traduit par :

- Des bibliothèques graphiques (Wayland, X11) installées par défaut alors que mon serveur n'a pas d'écran
- Des services comme `lightdm` (gestionnaire de connexion) ou `pulseaudio` (audio) qui tournent inutilement
- Un système de configuration (`raspi-config`) qui est pratique mais m'éloigne des standards Linux (Debian pur)

**Conséquence** : Chaque service inutile est une ressource gaspillée. Sur un petit Raspberry Pi, je veux que **chaque octet de RAM serve à quelque chose**.

> ❌ **Verdict : trop de services "bureau" résiduels pour un serveur headless**


### DietPi

Distribution basée sur Debian, conçue pour être la plus légère possible.

**L'avantage** : DietPi part d'une base ultra-minimaliste. On n'a que le strict minimum, ce qui est parfait dans mon cas. On a aussi une bibliothèque variée avec mes outils et logiciels habituels.

L'énorme avantage ici, c'est qu'on idle à environ **1-2% de CPU et 80-120 Mo de RAM** — ce qui est excellent et recherché dans mon cas pour un serveur dédié à l'hébergement de services.

> ✅ **Verdict : le plus minimaliste, adapté à mon usage serveur**

### Overclocking

CPU overclocké de **1.5 GHz** → **1.9 GHz**. Température stable sous 50°C grâce au ventilateur. L'overclocking, combiné à la légèreté de DietPi, me laisse une marge confortable pour héberger une multitude de services.


<img width="412" height="247" alt="image" src="https://github.com/user-attachments/assets/c3a60753-64c2-4500-9fa3-77bd27173e6f" />


--------------------------------------
# 📡 Réseau 


Pour ma configuration réseau j'ai fait quelque chose de simple 


j'avais déja un switch non géré qui connectais une partie de mes apareils a mon routeur donc j'y ai juste branché ma machine 


<br/>

<div align="center">
  <img width="450" alt="Shémas réseau" src="https://github.com/user-attachments/assets/78dca411-93b5-4265-9fb9-2e36ae04158d" />
  <br/>
  <em>Shémas réseau</em>
</div>

<br/>


J'utilise aussi un outil appelé **Tailscale** qui crée un tunnel réseau privé entre mes appareils clients et servers, j'utilise cet outil car il me permet d'acceder a mes services partout dans le monde grace a une connection internet la ou autrement j'aurais du rediriger mes ports ce qui peut s'averer dangereux, Tailscale est basé sur wireguard et il assigne une ip a chaque appareil connecté sur le vpn cette configuration marche bien pour mon cas car personne n'utilise mes services au dela des membres de ma famille et mes amis



<div align="center">
  <img width="1347" height="503" alt="image" src="https://github.com/user-attachments/assets/f14c90fe-3bb8-41c0-9832-c05032fdbb72" />
  <em>Shémas Tailscale</em>
</div>

<br/>

Maintenant que tout est prêt on peut attaquer le projet et commencer a installer les logiciels essentiels a l'hébergement de me services


--------------------------------------------------------

## 🛠 Services

### 🐳 Docker 

Je n'héberge aucun service "en bare metal" (installé directement sur le système). Tout tourne dans des **conteneurs Docker**.

### Avantages de la contenarisation dans mon cas:

| Avantage | Explication |
|----------|-------------|
| **Isolation** | Chaque service est isolé des autres. Si un service plante ou est compromis, les autres ne sont pas affectés. |
| **Portabilité** | Un conteneur tourne identiquement sur ma machine ou un autre serveur. Je peux migrer facilement sans avoir des problemes de compatibilité. |
| **Maintenance** | Les mises à jour sont simples : `docker compose pull && docker compose up -d` |
| **Reproductibilité** | Toute ma configuration est dans des fichiers `docker-compose.yml`. Je peux tout recréer en 10 minutes. |
| **Légèreté** | Docker n'émule pas un OS complet (contrairement à une VM). Les conteneurs partagent le noyau Linux. |

### Comment j'organise mes conteneurs

Chaque service a son propre dossier avec un `docker-compose.yml` 



### Commandes que j'utilise au quotidien

```bash
# Voir tous les conteneurs qui tournent
docker ps

# Voir les ressources utilisées par conteneur
docker stats

# Démarrer un service
docker compose up -d

# Arrêter un service
docker compose down

# Mettre à jour un service
docker compose pull
docker compose up -d

# Voir les logs d'un conteneur
docker logs <nom_conteneur> --tail 50

# Entrer dans un conteneur (debug)
docker exec -it <nom_conteneur> sh
```
voiçi les containers de mes services sur docker :
<img width="1781" height="367" alt="image" src="https://github.com/user-attachments/assets/62868692-6542-471b-85aa-0bd5bb8d8288" />

vous pourrez retrouver mes fichiers compose dans leurs répertoire respectif dans cette repo

### 📸 Immich
Alternative à Google Photos. Sauvegarde automatique de mes photos, partage d'albums.


<img width="1916" height="1042" alt="image" src="https://github.com/user-attachments/assets/4414f4ef-a529-4623-b5d2-3d664fc482cc" />


### 🏠 Home Assistant
Domotique locale. Gestion des lumières, capteurs de température, automatisations sans passer par le cloud.


<img width="1920" height="1034" alt="image" src="https://github.com/user-attachments/assets/12bc492c-1e6a-459b-b308-a4c54db41522" />


### 🎬 Jellyfin
Serveur multimédia maison. Streaming de mes films et musique sur téléphone, tablette ou TV.

<img width="1920" height="1039" alt="image" src="https://github.com/user-attachments/assets/b206dc74-efc8-4992-845f-7fdac3b7a203" />


### 🤖 Open WebUI + Ollama
Interface web pour interagir avec des LLM (Qwen,Exaone,Llama,...) en local. Pas d'envoi de données à OpenAI ou autre.


<img width="1920" height="1042" alt="image" src="https://github.com/user-attachments/assets/4f82372a-fd40-495e-9086-b3d8d0ba0d9c" />


### ⚙️ Windmill
Plateforme d'automatisation. Alternative à n8n ou Zapier, mais auto-hébergée et open-source.



<img width="1919" height="1033" alt="image" src="https://github.com/user-attachments/assets/96a2511b-feb2-4bcd-bc26-8097cf5e63df" />



### 📁 Samba
Partage de fichiers sur le réseau local NAS. Accès a mon HDD 500 Go depuis n'importe quel appareil.



<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/d7c73f53-c390-4f61-beca-60c6c90211aa" />



### 📊 Homer
Dashboard central. Page d'accueil perso avec tous mes services en un clic.

### 🔌 ESPHome
Gestion de mes capteurs maison sur les microcontrolleurs (ESP8266/ESP32). Température, humidité, présence.


<img width="1920" height="1027" alt="image" src="https://github.com/user-attachments/assets/e3cde197-3755-4a55-8b05-42ec2da3f204" />


### 🐳 What's Up Docker (WUD)
Surveillance automatique des mises à jour de mes conteneurs.



<img width="1920" height="1035" alt="image" src="https://github.com/user-attachments/assets/858e6363-7287-44da-acc2-aad8fa41e4c5" />







### Ressources

Avec 8 Go de RAM, la configuration tient confortablement :

| Métrique | Valeur |
|----------|--------|
| RAM utilisée | ~2.5-4 Go |
| RAM libre | ~3.5-4 Go |
| CPU moyenne | < 5% |
| CPU pic | ~25% (base de données) |

consomation de ressources par service : 

<img width="818" height="326" alt="image" src="https://github.com/user-attachments/assets/dcd178d7-4233-4af5-befd-2d141bbd9243" />


La légèreté de DietPi et l'overclocking m'aident à garder de la marge, le travail d'optimissation a été respecté .



## 🛡 Sécurité

La sécurité est aussi un aspect important de mon projet 

### Tailscale 

Comme évoqué précedamment le fait d'avoir tailscale me permet d'acceder a mes services depuis n'importe ou dans le monde ce qui fait que je n'ai pas besoin de rediriger mes ports ce qui vien éliminer une potentielle voie pour des attaques

### Honeypot

J'ai déployé un honeypot sur le port 22 de ma machine qui est le port ssh pour mieu comprendre les menaces qui touchent mon server notament sur le port 22 du protocole ssh 

<img width="1500" height="815" alt="image" src="https://github.com/user-attachments/assets/9723d6f0-b765-4550-a9f9-8514af238730" />


On peut voir sur cette capture d'écran qu'on a les logs a gauche et un potentiel attaquant qui tenterais de se connecter au serveur via le protocole ssh, on vois sur les logs l'ip de l'attaquant quand es ce qu'il s'est connécté avec quel utilisateur/mot de passe et ensuite quand il est connecté sur le honeypot et qu'il tente par exemple d'installer un logiciel malveillant sur la machine on vois les commandes qu'il rentre et tout ses faits et gestes en général


grace a ça non seulement on deviens plus concient des dangers d'ouvrir des ports de façon non sécurisée et en plus on apprend a adopter une maniére de dévélopper, deployer, et utiliser nos programmes/services/logiciels de maniére plus sécurisée et résponsable  


bien que les honeypots servent a sécuriser mon systeme il peut aussi devenir une passerelle au vrai systeme ce qui est trés dangereux pour ce fait il est important d'isoler le honeypot en employant la contenarisation, la virtualisation ou même encore le séparer completement et le deployer sur une machine dédié qui ne contient que le honeypot et ou d'autres services dédiés a la sécurité des autres machines de l'écosysteme 


### Parefeu



Pour combler mon arsenal de sécurité sur ma machine je vais configurer un parefeu sur les ports que j'utilise pour garder un controle total de ce qui sort et entre 


<img width="1554" height="840" alt="image" src="https://github.com/user-attachments/assets/b8b3f9d2-975d-4716-81cf-3165469f7f33" />


voiçi les régles de plus près :


<img width="598" height="122" alt="image" src="https://github.com/user-attachments/assets/68d654d7-568b-4eaa-a9b6-662c463bc64c" />


Donc maintenant que le firewall fonctionne correctement j'ai fait en sorte que  :


- mes appareils autorisés qui ont un accés complet au server 
- mon honeypot reste ouvert pour continuer de recevoir des donées et évaluer les risques des attaques sur le port 22
- Toute personne non autorisée est est refusée catégoriquement par le parfeu

avec tout ça on a donc un stack de sécurité fonctionnel qui a une vraie utilité et qui me permet de faire de sécuriser mon tout, (on peut aller pousser la chose encore plus loin en faisant un vrai soc avec des automations par rapport au honeypot par exemple,automatiser les mises a jours systeme, ou encore créer un vrai soc maison , ...)


--------------------------

## Conclusions

### Ce que j'ai appris durant ce projet

#### Compétences techniques

**Systèmes**
- Maintenir une infrastructure complète (hardware → OS → réseau → services)
- Choisir une distribution Linux adaptée (DietPi) pour des raisons précises (minimalisme, ARM, performances)
- Overclocker et refroidir un Raspberry Pi (température stable sous 50°C)
- Gérer les ressources système (RAM, CPU) pour optimiser les performances

**Réseau**
- Configurer un VPN (Tailscale) pour accéder à mes services à distance sans ouvrir de ports
- Comprendre le NAT, le DHCP et les redirections de ports
- Isoler mes services derrière un pare-feu (UFW) avec des règles par IP

**Conteneurisation (Docker)**
- Utiliser Docker Compose pour déployer et gérer des services
- Isoler chaque service dans son propre conteneur
- Persister les données avec des volumes
- Mettre à jour proprement : `docker compose pull && docker compose up -d`

**Sécurité**
- Mettre en place un honeypot (Cowrie) pour analyser des attaques réelles
- Configurer un pare-feu (UFW) avec des règles strictes (accès par IP uniquement)
- Isoler le honeypot dans Docker pour qu'il ne compromette pas le vrai système
- Comprendre les limites : un honeypot mal isolé peut devenir une passerelle

**Services**
- Héberger mes propres alternatives aux GAFAM :
  - **Immich** → Google Photos
  - **Jellyfin** → Netflix
  - **Home Assistant** → Domotique en local
  - **Open WebUI + Ollama** → ChatGPT local
  - **Windmill** → Zapier / n8n
  - **ntfy** → Notifications push
  - **WUD** → Mises à jour automatiques Docker

---

#### Compétences transversales (soft skills)

**Autonomie**
- Apprendre par moi-même sans cadre scolaire
- Chercher des solutions sur la documentation, les forums, GitHub
- Tester, casser, réparer, recommencer


**Résolution de problèmes**
- Diagnostiquer des erreurs (logs, `docker logs`, `journalctl`)
- Comprendre pourquoi un service ne démarre pas (port occupé, config erronée, permission)
- Isoler un problème et le résoudre étape par étape


**Patience et persévérance**
- Réinstaller DietPi plusieurs fois avant d'avoir une config stable
- Passer des heures à comprendre pourquoi un conteneur ne répond pas
- Accepter que ça ne marche pas du premier coup


**Curiosité et veille technologique**
- Découvrir de nouveaux outils (Immich, ntfy, WUD, Tailscale)
- Lire des blogs, des docs, des issues GitHub
- Tester des alternatives avant de choisir (Ubuntu vs Raspberry Pi OS vs DietPi)


**Organisation et rigueur**
- Structurer ma documentation (README clair, dossiers par service)
- Sauvegarder mes configurations (`docker-compose.yml`)
- Anticiper les problèmes (backups, monitoring)


**Communication**
- Documenter mon travail pour qu'il soit compréhensible par d'autres
- Expliquer mes choix techniques (pourquoi j'ai pris DietPi plutôt qu'Ubuntu)
- Partager mon projet sur GitHub



---

### Ce que je retiens

> *"Mon homelab n'est pas juste un serveur. C'est la preuve qu'on peut reprendre le contrôle de ses données et de ses outils — et que le chemin pour y arriver est aussi instructif et enrichissant que le résultat."*

---


### Prochaines étapes

**Matériel**
- [ ] me procurer une machine sur une architecture **x86** (mini PC type N100 ou NUC) pour plus de puissance et la capaciter a faire de la virtualisation
- [ ] Installer **Proxmox** pour faire de la virtualisation (plusieurs VMs, snapshots, backups)

**Virtualisation**
- [ ] Migrer mes services Docker dans des **LXC** sous Proxmox
- [ ] Apprendre et Tester **Kubernetes** (k3s) sur plusieurs nœuds

**IA locale**
- [ ] me procurer une machine dédiée au **LLM** (GPU ou accélérateur Intel/AMD)
- [ ] éxpérimenter avec des llms plus puissants et faire de l'automatisation avec un llm et windmill

**Sécurité (SOC maison)**
- [ ] Centraliser les logs (**ELK Stack** ou **Grafana Loki**)
- [ ] Ajouter un dashboard de sécurité (alertes en temps réel)
- [ ] Automatiser la réponse aux attaques (fail2ban + webhook ntfy)

**Infrastructure**
- [ ] Mettre en place un **reverse proxy** (Nginx) avec HTTPS (Let's Encrypt)
- [ ] Mettre en place des backups sur une autre machine
- [ ] Automatiser les backups


**Domotique / Automatisation**
- [ ] Ajouter **Node-RED** pour des automatisations plus avancées
- [ ] Remplacer Windmill par **n8n** ou **ActivePieces** si besoin
- [ ] intégrer plus d'objets connéctés

---------------------------

Merci d'avoir pris le temps de lire ma documentation.

Ce projet est en constante évolution. N'hésitez pas à jeter un œil aux prochaines étapes ou à me contacter si vous avez des questions.

— Sa-r00t
