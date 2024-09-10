# Comment interfacer plusieurs capteurs avec un Arduino ?

Le protocole I2C est idéal pour connecter plusieurs capteurs à un seul microcontrôleur, comme l'Arduino, tout en utilisant seulement deux fils de communication : SDA (données) et SCL (horloge). Grâce au système d'adressage unique pour chaque périphérique, il est possible de brancher plusieurs capteurs sur le même bus I2C sans avoir besoin de lignes dédiées pour chaque capteur.

## Étapes pour interfacer plusieurs capteurs

1. **Connexions physiques** :
   - **SDA** : Connectez la broche SDA de l'Arduino aux broches SDA de tous les capteurs.
   - **SCL** : Connectez la broche SCL de l'Arduino aux broches SCL de tous les capteurs.
   - **Alimentation** : Connectez les broches VCC et GND de chaque capteur à l'alimentation (5V ou 3.3V) et à la masse (GND) de l'Arduino.

   Assurez-vous également d'ajouter des **résistances de pull-up** (typiquement 4.7kΩ) sur les lignes SDA et SCL pour garantir un bon fonctionnement du bus.

2. **Configuration des adresses I2C** :
   - Chaque capteur connecté sur le bus I2C doit avoir une adresse unique. De nombreux capteurs offrent la possibilité de modifier leur adresse par des ponts (jumpers) ou des broches d'adressage. Par exemple, un capteur de température pourrait avoir plusieurs adresses possibles, selon la configuration matérielle.

   Voici un exemple de configuration avec trois capteurs connectés à un Arduino :

   | Adresse sur 7 bits | Périphérique               |
   | ------------------ | -------------------------- |
   | 0x68               | Capteur de température      |
   | 0x76               | Capteur de pression         |
   | 0x27               | Capteur d'humidité          |

3. **Gestion des données avec l'Arduino** :
   - Grâce à la bibliothèque `Wire` d'Arduino, vous pouvez communiquer avec chaque capteur en spécifiant son adresse unique. Le processus consiste à envoyer l'adresse du périphérique, puis à lire ou écrire des données sur le capteur via le bus I2C.

### Exemple de code Arduino pour lire plusieurs capteurs I2C

Voici un exemple de code qui montre comment un Arduino peut interroger trois capteurs I2C sur le même bus :

```cpp
#include <Wire.h>

void setup() {
  Wire.begin(); // Initialise le bus I2C
  Serial.begin(9600); // Initialise la communication série pour afficher les résultats

  // Optionnel : Initialisation spécifique des capteurs
  // ...
}

void loop() {
  // Lecture du capteur de température (adresse 0x68)
  Wire.beginTransmission(0x68);
  Wire.write(0x00); // Commande de lecture (dépend du capteur)
  Wire.endTransmission();
  Wire.requestFrom(0x68, 2); // Demande 2 octets (exemple)
  if (Wire.available()) {
    int tempData = Wire.read() << 8 | Wire.read(); // Lecture des données
    Serial.print("Température: ");
    Serial.println(tempData);
  }

  // Lecture du capteur de pression (adresse 0x76)
  Wire.beginTransmission(0x76);
  Wire.write(0x00); // Commande de lecture (dépend du capteur)
  Wire.endTransmission();
  Wire.requestFrom(0x76, 2); // Demande 2 octets
  if (Wire.available()) {
    int pressureData = Wire.read() << 8 | Wire.read(); // Lecture des données
    Serial.print("Pression: ");
    Serial.println(pressureData);
  }

  // Lecture du capteur d'humidité (adresse 0x27)
  Wire.beginTransmission(0x27);
  Wire.write(0x00); // Commande de lecture (dépend du capteur)
  Wire.endTransmission();
  Wire.requestFrom(0x27, 2); // Demande 2 octets
  if (Wire.available()) {
    int humidityData = Wire.read() << 8 | Wire.read(); // Lecture des données
    Serial.print("Humidité: ");
    Serial.println(humidityData);
  }

  delay(1000); // Attendre avant la prochaine lecture
}
```

Dans cet exemple :
- L'Arduino interroge chaque capteur en envoyant une commande de lecture spécifique, puis en lisant les données renvoyées par le capteur.
- Les adresses I2C (comme `0x68`, `0x76`, et `0x27`) sont des exemples d'adresses pour différents capteurs ; vous devez les ajuster en fonction des périphériques que vous utilisez.

### Résistances de pull-up
Il est important de noter que le protocole I2C nécessite l'ajout de **résistances de pull-up** sur les lignes SDA et SCL. Sans ces résistances, le bus risque de ne pas fonctionner correctement. Généralement, des résistances de 4.7 kΩ sont suffisantes pour la plupart des applications.

---

En résumé, le protocole I2C permet de connecter facilement plusieurs capteurs à un seul Arduino avec seulement deux fils, tout en s'assurant que chaque périphérique a une adresse unique sur le bus. La bibliothèque `Wire` simplifie grandement l'implémentation et la gestion des communications avec ces périphériques.

[Passons maintenant au TP d'application.](./TP%20Ecran%20LCD%20calculatrice.md)