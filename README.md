# Projet Prusa MK3 "MK_Twin" - X-Axis 500mm

Bienvenue sur la documentation officielle du projet **MK_Twin**. 
Ce dépôt contient le firmware modifié d'une Prusa MK3 (ou MK3S/+) pour étendre son axe X à **500 mm**, avec une motorisation doublée.

*(Basé sur le code source Original Prusa i3 - GNU GPL 3).*

---

## 1. Caractéristiques Mécaniques de la Machine
* **Volume d'impression utile (X, Y, Z) :** 500 x 250 x 210 mm
* **Motorisation Y :** Double moteur pas-à-pas NEMA 17 (câblés en parallèle sur le même driver).
* **Plateau chauffant :** 2x Heatbeds magnétiques V2 d'origine.
* **Palpage (Leveling) :** Système Prusa intelligent (G80), grille automatique étendue sur 470 mm.

---

## 2. Modifications apportées au Firmware
Pour référence future, voici les valeurs exactes qui ont été modifiées par rapport au firmware d'usine Prusa pour adapter le cerveau à la nouvelle mécanique :

### Fichier `MK3.h & MK3S.h dans variants` (Limites et Puissance)
* **Dimensions physiques :**
  * `X_MAX_POS` passé à `500`
* **Courant des moteurs (Drivers TMC2130) :**
  * Pour compenser le poids des deux lits, le courant de maintien (`_H`) et de mouvement (`_R`) de l'axe Y a été augmenté pour gérer deux moteurs en parallèle (environ 390mA par moteur).
  * `TMC2130_CURRENTS_H` : `{16, 35, 35, 30}` *(Y passe de 20 à 35)*
  * `TMC2130_CURRENTS_R` : `{16, 35, 35, 30}` *(Y passe de 20 à 35)*

### Fichier `mesh_bed_calibration.h` (Zone de Palpage Pinda)
* Les limites virtuelles dans lesquelles la sonde a le droit de descendre ont été agrandies.
* L'espacement de la grille de palpage se calcule automatiquement.
  * `BED_Xn` passé à `470.f` (500mm - 30mm de marge de sécurité)

---

##  3. Guide de Compilation (VS Code & CMake)

Le firmware Prusa utilise **CMake**.

### Prérequis
* Visual Studio Code avec l'extension **CMake Tools** installée.
* Python installé sur le PC.

### Procédure de compilation
1. **IMPORTANT SOUS WINDOWS :** Placez le dossier `MK3.2` au plus près de la racine de votre disque (ex: `C:\MK3.2`). Ne compilez **JAMAIS** depuis un dossier "OneDrive", car la limite de caractères de Windows fera planter le compilateur avec l'erreur `CreateProcess: No such file or directory`.
2. Ouvrez le dossier `MK3.2` dans Visual Studio Code.
3. Ouvrez un terminal dans VS Code et lancez l'installation des dépendances avec :
   `python .\utils\bootstrap.py`
4. Sur la barre de gauche, ouvrez l'onglet **CMake** > **Build Targets**.
5. Cherchez la cible **`ALL_ENGLISH`** (pour compiler rapidement sans les traductions européennes) et cliquez sur le bouton **Build**.
6. Le fichier compilé se trouvera dans `build/MK3S_MK3S+_..._ENGLISH.hex`. *(Flashez-le avec PrusaSlicer).*

---


## 4. Configuration PrusaSlicer

Le firmware fait tout le travail. Les modifications dans le Slicer sont mineures :

1. Prenez un profil `Original Prusa i3 MK3S & MK3S+` ou `Original Prusa i3 MK3` standard.
2. Allez dans **Réglages de l'imprimante** > **Général** > **Forme du plateau**.
3. Réglez les dimensions sur **X: 500** et **Y: 250** (selon vos plaques).
4. **G-Code de démarrage :** Conservez le `G80` d'origine. Le firmware étirera automatiquement le palpage sur toute la plaque de 500 mm. La ligne de purge se fera également au bon endroit d'origine.
5. **Sauvegarde :** Allez dans *Fichier > Exporter > Exporter le lot de configuration* pour sauvegarder vos profils personnalisés.
