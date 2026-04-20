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

<br/>

<div align="center">
  <img width="450" alt="Shémas réseau" src="https://github.com/user-attachments/assets/78dca411-93b5-4265-9fb9-2e36ae04158d" />
  <br/>
  <em>Shémas réseau</em>
</div>


Maintenant que notre machine est prête on va choisir un systeme d'éxploitation.

## 💻 Spécification Software


Nous allons désormais chosir un systeme d'éxploitation selon nos besoins 

il nous faut une distribution linux légere, stable et sécurisé ce qui est essentiel pour héberger des services en continu

| Critère       | Exigence                                                                |
|---------------|-------------------------------------------------------------------------|
| **Stabilité** | il faut que sa tourne sans problemes sur ma machine sans avoir a toujours faire de la maintenance et sans planter quand on ne s'y attend pas  |
| **légereté**  | on a besoin d'un systeme conçu pour l'architécture ARM de mon prcesseur et qu'il puisse tourner sans avoir une grosse empreinte sur mes ressources                   |
| **Sécurité** | Mises a jours réguliéres  |

Si je veux de la stabilité et de la sécurité je dois m'orienter vers debian qui est reconnue et une trés grandes partie des distributions linux se basent dessus, J'avais devant moi 3 choix 

#### *Ubuntu server*:

distribution classique basée sur debian est trés connue dans le millieu du self-host le probléme est le fait qu'ubuntu appartient a cannonical ce qui me retiens déja , sans parler des bloatwares additionels qui font que le systéme d'éxploitation idle a 25% d'utilisation cpu et 3 gb de ram ce qui est éliminatoire pour moi 

#### *Raspberry pi OS*:


c'est la distribution la plus populaire sur les Raspberry pi en général basée sur debian, on a une vérsion headless qui permet d'enlever l'interface utilisateur et tout ce qui est nécessaire pour une utilisation érgonomique du systéme d'exploitation mon souçis ici est qu'on idle a 10% d'utilisation cpu et 1 gb de ram ce qui est déja bien meilleur qu'ubuntu server mais le vrai probléme pour moi ici c la taille de la librairie de base qui est limitée (je pourrais trés bien ajouter des librairies a ma guise mais c'est pour moi une autre faille en plus a surveiller 


#### *Diet pi*:

Diet pi est une distribution basée sur débian qui a pour but d'étre la plus légere possible on n'a que le strict minimum ce qui est parfait dans mon cas, on a aussi une librairie variée avec mes outils et logiciels habituels, mais l'énorme avantage ici c'est qu'on idle a 5% de cpu et 500 mb de ram ce qui est complétement lunaire et recherché dans mon cas 

