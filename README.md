# Mon Homelab



## Préambule 


Aujourdhui plus que jamais les services tiers auquels on a recours abusent de notre confiance et éxploitent nos données a grande échelle, ma réponse a ce propbléme est Mon homelab , bien qu'on ne puisse pas échapper a l'emprise de tout les services externes on peut en héberger une grande partie et baisser notre empreinte numérique et reprendre le controle sur nos données

Ici je vous présente de façon détaillé ma configuration et la façon dont j'héberge mes propres services chez moi avec un coût matériel et énérgetique moindre . 

----------------------------

### 1. ⚙ HARDWARE


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
  <img width="400" alt="Shémas réseau" src="https://github.com/user-attachments/assets/78dca411-93b5-4265-9fb9-2e36ae04158d" />
  <br/>
  <em>Shémas réseau</em>
</div>


