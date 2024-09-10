# Les liaisons séries - Le protocole I2C

## C'est quoi, le protocole I2C ?

Le protocole I2C (Inter-Integrated Circuit) a été développé par Philips dans les années 1980 pour permettre la communication entre différents composants électroniques à l'intérieur d'un même circuit imprimé. Contrairement à la RS-232, qui est souvent utilisée pour les communications entre appareils distincts, l'I2C est principalement conçu pour interconnecter plusieurs circuits intégrés (ou puces) sur une même carte. Ce protocole est devenu un standard pour la communication série entre microcontrôleurs et périphériques comme les capteurs, les EEPROMs, les convertisseurs analogiques-numériques, et bien plus encore.

I2C se distingue par sa simplicité et son efficacité : avec seulement deux fils de communication, il permet à plusieurs composants de se connecter ensemble sur un même bus, tout en gardant le contrôle des communications.

<center>

![Schéma d'un bus I2C avec plusieurs périphériques connectés à un microcontrôleur.](assets/10000000000002BF000000D113DCFB57.jpg 'Schéma d’un bus I2C avec plusieurs périphériques connectés à un microcontrôleur.')

</center>

## Comment ça marche ?

Le protocole I2C utilise deux lignes principales pour la communication : SDA (Serial Data Line) et SCL (Serial Clock Line). Ces lignes sont partagées par tous les dispositifs connectés au bus, permettant à un maître (typiquement un microcontrôleur) de communiquer avec plusieurs esclaves (capteurs, mémoires, etc.). Voici les principes fondamentaux du fonctionnement du protocole I2C :

1. **Maître et esclave** : Le protocole I2C fonctionne selon une architecture maître-esclave. Le maître contrôle le bus I2C et initie les communications. Les esclaves attendent les instructions du maître et répondent en conséquence. Il est possible d'avoir plusieurs maîtres sur un même bus, mais cela complexifie la gestion des conflits.

2. **Adresse des dispositifs** : Chaque périphérique connecté sur le bus I2C possède une adresse unique sur 7 ou 10 bits. Lorsqu'il veut communiquer avec un esclave spécifique, le maître envoie l'adresse de l'esclave sur le bus. Si l'adresse correspond à celle d’un esclave, ce dernier répond et la communication commence.

<center>

| Adresse sur 7 bits | Fonction                                 |
| ------------------ | ---------------------------------------- |
| 0x50               | EEPROM (Mémoire)                        |
| 0x68               | Module RTC (Horloge en temps réel)       |
| 0x76               | Capteur de pression                      |
| 0x27               | Capteur de température et d'humidité     |

</center>

3. **Cadencement des données** : La ligne SCL (Serial Clock Line) est utilisée pour synchroniser le transfert des données. Le maître génère les impulsions d’horloge sur cette ligne, ce qui permet de synchroniser l’envoi des bits de données sur la ligne SDA (Serial Data Line). À chaque impulsion d’horloge, un bit de donnée est transmis.

4. **Communication bidirectionnelle** : La ligne SDA est utilisée pour envoyer et recevoir des données. Lorsqu'un périphérique envoie des données, les autres périphériques doivent libérer la ligne pour éviter les interférences. Ce mécanisme permet à la communication d'être bidirectionnelle sur une seule ligne de données.

5. **Acknowledge (Ack) et Non-Acknowledge (Nack)** : Après chaque octet envoyé, le périphérique récepteur doit envoyer un bit d'acquittement (Ack) pour indiquer que les données ont été reçues correctement. Si un Nack (non-acquittement) est envoyé, cela signifie qu’il y a eu un problème, et le maître peut décider de renvoyer les données ou d’arrêter la communication.

<center>

![Chronogramme illustrant une transmission I2C, avec les bits d'adresse, de données et les bits d'acquittement.](assets/Capture_trame_i2C.PNG 'Chronogramme illustrant une transmission I2C, avec les bits d’adresse, de données et les bits d’acquittement.')

</center>

6. **Vitesse de transmission** : Le protocole I2C prend en charge plusieurs vitesses de transmission, telles que le mode standard (jusqu'à 100 kbit/s), le mode rapide (jusqu'à 400 kbit/s), et le mode haute vitesse (jusqu'à 3,4 Mbit/s). La vitesse choisie dépend des besoins de l'application et des capacités des périphériques connectés.

7. **Arbitration** : Si plusieurs maîtres sont présents sur le même bus et tentent de prendre le contrôle simultanément, un mécanisme d'arbitrage est utilisé pour résoudre les conflits. Le maître qui émet le signal le plus fort sur la ligne SDA pendant une condition de Start continue la transmission, tandis que les autres se retirent.

## Les registres I2C

Lors de la communication avec des périphériques I2C, il est courant que chaque esclave ait plusieurs registres, chacun dédié à une fonction spécifique. Par exemple, un capteur de température peut avoir des registres séparés pour stocker les valeurs de température, l'état de fonctionnement et les commandes de configuration. 

Pour lire ou écrire des données dans un registre particulier d'un périphérique I2C :
- Le maître envoie d'abord l'adresse de l'esclave.
- Ensuite, il envoie l'adresse du registre à lire ou écrire.
- Enfin, il effectue l'opération de lecture ou d'écriture sur ce registre.

Chaque périphérique a sa propre disposition de registres, et il est important de consulter la documentation pour connaître l'organisation et les fonctions des registres spécifiques du périphérique.

## Les résistances de pull-up

Le protocole I2C repose sur une configuration **Open Drain** ou **Open Collector**, ce qui signifie que les lignes SDA et SCL doivent être tirées à un état logique haut par des résistances de pull-up externes. Ces résistances sont nécessaires pour garantir le bon fonctionnement du bus I2C.

### Pourquoi des résistances de pull-up ?

Dans un bus I2C, les lignes SDA et SCL peuvent être partagées par plusieurs périphériques. Chaque périphérique peut "tirer" la ligne vers le bas (état bas, ou 0 logique) lorsqu'il envoie des données, mais pour que la ligne revienne à l'état haut (1 logique), il est nécessaire de lier la ligne à une tension de référence (souvent 3.3V ou 5V) via une résistance. Sans cette résistance, les lignes SDA et SCL risquent de ne jamais retourner à l'état haut, ce qui perturberait la communication.

### Valeur des résistances de pull-up

Les valeurs courantes pour les résistances de pull-up sont de l'ordre de **4.7 kΩ** ou **10 kΩ**, mais cela peut varier en fonction de la vitesse de transmission, de la longueur des lignes de bus et du nombre de périphériques connectés.

---

En résumé, le protocole I2C est un outil puissant et flexible pour la communication entre circuits intégrés. Sa capacité à connecter plusieurs périphériques sur un seul bus avec seulement deux fils le rend particulièrement adapté aux systèmes embarqués complexes où l'espace et les broches de communication sont limités.

[Dans la section de cours suivante, nous verrons un exemple d'utilisation du protocole I2C avec plusieurs capteurs.](Exemple%I2C.md)