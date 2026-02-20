# Jubarte Plongée Zélande

Application web d'aide à la planification de plongées en Zélande (Pays-Bas), développée par **Louis TINANT** (plongeur 3⭐) pour les membres du club **JUBARTE C.A.S. HABAY**.

---

## Contexte : pourquoi la Zélande est un cas particulier

La Zélande est un delta formé par l'embouchure du Rhin, de la Meuse et de l'Escaut dans la Mer du Nord. Cette géographie crée des conditions sous-marines uniques en Europe : eaux riches en nutriments, biodiversité exceptionnelle (anémones, éponges, homards, seiches, congres), mais aussi des **courants de marée parmi les plus puissants du continent**.

La mer monte et descend deux fois par jour selon un cycle d'environ 12h25 (cycle lunaire). Entre la pleine mer et la basse mer, d'immenses volumes d'eau s'engouffrent dans les estuaires puis repartent, générant des courants pouvant dépasser 3 à 5 nœuds sur certains sites. **Plonger dans ces courants est dangereux**, voire fatal : un plongeur ne peut pas nager contre un courant de marée soutenu.

---

## Le concept central : l'étale de courant

L'**étale** (ou *kentering* en néerlandais) est le court moment de renversement de courant entre deux marées, quand la mer est à son point le plus haut (pleine mer) ou le plus bas (basse mer). Pendant une fenêtre de **±30 minutes** autour de ce moment, le courant est suffisamment faible pour permettre une plongée en sécurité.

```
Courant →→→→→→  [ ÉTALE ≈ 30-60 min ]  ←←←←←← Courant
            ↑                        ↑
         Pleine mer              Basse mer
```

Chaque journée offre donc **2 fenêtres de plongée** (une autour de la pleine mer, une autour de la basse mer), séparées d'environ 6h. En dehors de ces fenêtres, la plongée est contre-indiquée sur les sites soumis aux marées.

### Les corrections locales par site

L'heure de l'étale **n'est pas la même partout**. L'onde de marée progresse à travers les estuaires et atteint certains sites avec un décalage de plusieurs dizaines de minutes par rapport à la station de référence. Ces corrections (dites corrections NOB, issues de la *Nederlandse Onderwatersport Bond*) sont documentées site par site et croisées avec les données de duikspotter.nl, sovscaldis.nl et duikersgids.nl.

Exemple : sur l'Oosterschelde, la pleine mer à Sint Annaland arrive **+40 minutes** après la pleine mer à la station de référence de Stavenisse (STAVNS).

---

## Les 4 zones de plongée couvertes

### Oosterschelde - Bras de mer à marées
Le plus grand estuaire de Zélande, séparé de la Mer du Nord par le barrage anti-tempête de la Delta Works. **Soumis aux marées** (marnage 2–3m), courants significatifs, mais biodiversité exceptionnelle. La majorité des 31 sites de l'application s'y trouvent. Quelques zones de calme existent (Zoetersbout, Bergse Diepsluis) où le courant est négligeable.

### Grevelingenmeer - Lac fermé, sans marée
Ancien bras de mer transformé en lac d'eau salée après les Delta Works (1971). **Aucun courant de marée.** Eau très claire (visibilité 5–15m), faune dense. Sites accessibles à tout moment, idéaux pour les plongeurs moins expérimentés ou souhaitant une plongée longue sans contrainte horaire.

### Veerse Meer - Lac saumâtre, sans marée
Lac intérieur entre Walcheren et Noord-Beveland. **Sans courant**, peu profond (max 15m), eaux calmes. Bon terrain pour débutants et formations. Seiches en saison printanière.

### Westerschelde - Voie maritime internationale, experts uniquement
Estuaire actif menant au port d'Anvers. **Courants extrêmement violents** (4–5 nœuds), trafic maritime dense (porte-conteneurs, ferries), visibilité très faible (1–4m). Tous les sites de cette zone sont classés **experts uniquement** et nécessitent une organisation spécifique (boat-diving, parachutes, guide local).

---

## Fonctionnalités de l'application

### 1. Sélection du site et de la date

- **31 sites** répartis sur les 4 zones, groupés par zone dans le sélecteur
- Planification sur **7 jours** (aujourd'hui + 6 jours)
- Pour chaque site : affichage des métadonnées clés (zone, profondeur max, fourchette de visibilité typique, statut marée/sans-marée, niveau requis)

### 2. Filtre profil plongeur - matrice LIFRAS

L'application utilise les **brevets LIFRAS** (Ligue Francophone de Recherches et d'Activités Subaquatiques). Deux sélecteurs sont proposés : le niveau du plongeur et le niveau du binôme. La combinaison détermine la profondeur maximale autorisée selon la règlementation LIFRAS.

#### Niveaux disponibles

| Code | Désignation |
|---|---|
| NB | Non Breveté |
| 1* | Plongeur Encadré |
| 2* | Plongeur Autonome |
| 3* | Plongeur Confirmé |
| 4* | Plongeur Expert |
| AM / MC / MF / MN | Assistant Moniteur / Moniteur Club, Fédéral ou National |

#### Matrice des profondeurs autorisées (binôme)

| Plongeur \ Binôme | NB | 1* | 2* | 3* | 4* | AM/Moniteur |
|---|---|---|---|---|---|---|
| **NB** | - | - | - | - | - | 15 m |
| **1*** | - | - | - | 20 m | 20 m | 20 m |
| **2*** | - | - | 20 m (\*) | 30 m | 40 m | 40 m |
| **3*** | - | 20 m | 30 m | 40 m | 40 m | 40 m |
| **4*** | - | 20 m | 40 m | 40 m | 40 m | >40 m |
| **AM/Moniteur** | 15 m | 20 m | 40 m | 40 m | 40 m | >40 m |

(\*) 2* + 2* : uniquement si les deux plongeurs ont **18 ans révolus**. Jamais au-delà de 20 m.

L'application détecte les combinaisons non autorisées (cellules "-") et affiche une alerte bloquante. Elle indique également combien de sites du catalogue sont accessibles pour la combinaison sélectionnée.

### 3. Bannière de statut - décision immédiate

C'est l'élément central de l'interface : une bannière colorée indique en un coup d'œil si la plongée est possible **maintenant**.

| Couleur | Signification |
|---|---|
| 🟢 Vert | Fenêtre d'étale en cours - plongez maintenant |
| 🟡 Jaune | Étale dans moins de 45 minutes - préparez-vous |
| 🔴 Rouge | Courant trop fort - attendez la prochaine fenêtre |
| 🔵 Bleu | Site sans marée ou planification future |

Pour les sites sans courant (lacs), la bannière indique simplement que le site est plongeable à tout moment.

### 4. Marées et fenêtres de plongée

Pour les sites soumis aux marées :
- Récupération des données de hauteur d'eau en temps réel via l'API Rijkswaterstaat
- Détection automatique des extrêmes (pleine mer / basse mer) par analyse de la série temporelle
- Calcul des fenêtres de plongée = ±30 minutes autour de l'heure d'étale corrigée par site
- Affichage de chaque fenêtre avec : heure de début, heure d'étale, heure de fin, correction appliquée
- Les fenêtres passées sont grisées ; la fenêtre active pulse en vert
- **Mécanisme de repli** : si l'API RWS est indisponible, des données de marée synthétiques sont générées à partir du cycle lunaire théorique (6h21)

### 5. Graphique de hauteur d'eau - 24h

Un graphique canvas affiche la courbe de hauteur d'eau sur 24h avec :
- Zones vertes semi-transparentes pour les fenêtres de plongée
- Ligne verticale orange indiquant l'heure actuelle (pour une plongée aujourd'hui)
- Valeurs normalisées en mètres NAP (référence altimétrique néerlandaise)

### 6. Visibilité sous-marine estimée

L'application calcule une estimation de la visibilité sous-marine en combinant plusieurs facteurs :

- **Zone** : le Grevelingenmeer offre structurellement une meilleure visibilité (5–15m) que l'Oosterschelde (3–9m) ou la Westerschelde (1–4m)
- **Saison** : visibilité améliorée en été (eaux plus stables), réduite en hiver
- **État de la mer** : des vagues significatives (>0.8m) turbulent les sédiments et réduisent la visibilité
- **Données du site** : chaque site a une fourchette de visibilité typique issue de l'expérience terrain

L'estimation est affichée sous forme de barre de progression avec code couleur (vert/jaune/rouge).

### 7. Météo et vent

- Vitesse, direction et rafales du vent en m/s
- Conversion et affichage en **échelle Beaufort** avec label descriptif
- Rose des vents (compas animé)
- Alertes automatiques : Beaufort 5+ = mise en garde, Beaufort 7+ = plongée déconseillée
- Température de l'air (Open-Meteo)
- Température de l'eau (saisonnière par table mensuelle)

### 8. État de la mer et vagues

- Hauteur des vagues significatives (Hm0)
- Période et direction des vagues
- Hauteur et période de la houle (swell)
- Hauteur des vagues générées par le vent (wind wave)
- Pour les sites en eaux intérieures (lacs), cette section indique explicitement que les données marines ne sont pas applicables

### 9. Carte météo interactive Windy

Une carte Windy.com intégrée, centrée automatiquement sur le site sélectionné, affichant en temps réel les couches vent, vagues, température, précipitations (modèle ECMWF).

### 10. Sources et statut des données

Un panneau de transparence liste toutes les sources de données avec :
- Indicateur vert/rouge de disponibilité en temps réel
- Heure de dernière mise à jour
- Lien direct vers la source originale

---

## Base de données des 31 sites

Chaque site est décrit par :

```
Nom (FR + NL)        Langue et nom néerlandais d'origine
Zone                 Oosterschelde / Grevelingenmeer / Veerse Meer / Westerschelde
Station RWS          Code de la station de mesure associée
Correction HW        Décalage en minutes vs station de référence, pleine mer
Correction LW        Décalage en minutes vs station de référence, basse mer
Soumis marées        Oui / Non
Sans courant         Oui / Non (lacs, bras morts)
Niveau requis        Débutant / Intermédiaire / Expérimenté
Profondeur max       En mètres
Visibilité typique   Fourchette min–max en mètres
Coordonnées GPS      Lat/Lon pour centrage carte et API
Description          Caractéristiques, vie marine, conditions particulières
```

---

## Sécurité et alertes

L'application intègre plusieurs mécanismes de sécurité :

- **Sites experts** : signalement visuel (tag rouge, alerte) pour les sites à courants violents ou trafic maritime (Westerschelde, Roompot, Neeltje Jans)
- **Incompatibilité niveau** : alerte si le site dépasse le niveau du plongeur
- **Incompatibilité profondeur** : avertissement si le site est plus profond que la limite choisie
- **Vent dangereux** : alerte Beaufort 5+, interdiction Beaufort 7+
- **Mer agitée** : alerte si la hauteur des vagues dépasse 0.8m
- **Saison seiches** (printemps, Oosterschelde) : notification informative de la période de reproduction

---

## Sources de données

| Source | Données | Fréquence |
|---|---|---|
| [Rijkswaterstaat Waterwebservices](https://waterwebservices.rijkswaterstaat.nl) | Hauteur d'eau temps réel (stations STAVNS, BRKDMDN, VLISSGN, TERNZN) | 10 minutes |
| [Rijkswaterstaat Waterinfo](https://waterinfo.rws.nl/thema/waterhoogte) | Marées astronomiques officielles - référence nationale NL | Référence |
| [Getijdentabel.nl](https://www.getijdentabel.nl) | Tables de marées et calcul kentering pour plongeurs | Référence |
| [Open-Meteo Standard](https://open-meteo.com) | Météo, vent, température, visibilité air (modèle ECMWF) | Horaire |
| [Open-Meteo Marine](https://marine-api.open-meteo.com) | Vagues, houle, période, direction (modèle ERA5) | Horaire |
| [Windy.com](https://www.windy.com) | Carte météo interactive temps réel | Continue |
| NOB / duikspotter.nl / sovscaldis.nl | Corrections kentering par site - données empiriques vérifiées 2025, tolérance ±15 min | Statique |

---

## Stack technique

- **HTML / CSS / JavaScript vanilla** - un seul fichier, aucun framework, aucune dépendance de build
- Appels API parallèles via `Promise.allSettled` (RWS, Open-Meteo Standard, Open-Meteo Marine)
- Graphique rendu sur `<canvas>` avec gestion du device pixel ratio (affichage net sur écrans Retina)
- Rafraîchissement automatique toutes les 10 minutes pour les plongées du jour
- Design responsive mobile-first, inspiré Apple, avec effets glassmorphism
- Interface entièrement en français

---

## Auteur

**Louis TINANT** - Plongeur 3⭐, Club **JUBARTE C.A.S. HABAY**

Application créée pour faciliter la préparation des sorties plongée du club en Zélande.
