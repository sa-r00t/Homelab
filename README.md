# Mon Homelab



## Préambule 


Aujourdhui plus que jamais les services tiers auquels on a recours abusent de notre confiance et éxploitent nos données a grande échelle, ma réponse a ce propbléme est Mon homelab , bien qu'on ne puisse pas échapper a l'emprise de tout les services externes on peut en héberger une grande partie et baisser notre empreinte numérique et reprendre le controle sur nos données

Ici je vous présente de façon détaillé ma configuration et la façon dont j'héberge mes propres services chez moi avec un coût matériel et énérgetique moindre . 

----------------------------

### 1. ⚙ HARDWARE


Pour héberger des services il en faut peu on peut facilement recycler des vielles machines et leurs donner une seconde vie, les 3 grands critéres pour moi sonts Le coût ( a la consomation et en matériel), le bruit ( je veut une machine qui soit le plus silencieuse possible ) et la puissance (plus on a une machine puissante plus on peut héberger des services demandant et dans certains cas méme heberger des llms locaux) .

Pour ma part j'ai choisi un Raspberry pi 4B avec 8gb de ram c'est un sbc assez puissant pour sa taille et sa consomation maximale de 15W ( une ampoule électrique a leds consome autant), avec ça j'ai fait un boitié en lego pour le style,j'ai mais un dissipateur sur le processeur puis j'ai branché un ventilateur en 5V sur les pins gpio et j'ai aussi branché un hdd en 2.5 pouce de 500 gb en usb avec un adaptateur sata -> usb pour avoir de l'espace de stockage en plus 

- **Raspberry Pi 4B** : 8 gb de ram processeur 4 coeur overclocké a 1.9 Ghz
- **Disque dur HDD***: disque en 2.5 pouce connecté avec un adaptateur sata vers usb pour 500 gb de stockage additionels
- **Ventilateur 5V générique**: connécté sur les pins gpio du Raspberry pour refroidir le tout avec un dissipateur sur le processuer 
- **Switch** : TP-Link 5 ports 




<img width="1000" height="700" alt="image" src="https://github.com/user-attachments/assets/f36c81a2-02f0-467a-9199-f4b5cf514ac9" />




<img width="1000" height="700" alt="image" src="https://github.com/user-attachments/assets/74cb129f-1866-400c-a0e3-0b952bf9ea16" />
