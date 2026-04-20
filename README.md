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

<img width="474" height="474" alt="tailscale logo" src="https://github.com/user-attachments/assets/769726de-e5f5-4f69-a5c5-00b827c968ec" />



<div align="center">
  <img width="474" height="474" alt="tailscale logo" src="https://github.com/user-attachments/assets/769726de-e5f5-4f69-a5c5-00b827c968ec" />
  <em>Raspberry pi 4B avec un dissipateur</em>
</div>

<br/>

Maintenant que tout est prêt on peut attaquer le projet et commencer a installer les logiciels essentiels a l'hébergement de me services


--------------------------------------------------------

# 🛠 Services
