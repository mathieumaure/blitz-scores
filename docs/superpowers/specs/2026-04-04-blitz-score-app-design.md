# Blitz Score - App de comptage de points Dutch Blitz

## Contexte

Le Dutch Blitz est un jeu de cartes rapide où les joueurs accumulent des points sur plusieurs manches. Le calcul des scores en cours de partie est fastidieux : il faut additionner les points de chaque manche pour chaque joueur. Cette app remplace le papier et le crayon par une interface simple, persistante et mobile-friendly.

## Règles de scoring

- **+1 point** par carte posée dans les piles centrales (Dutch Piles)
- **-2 points** par carte restante dans la pile Blitz du joueur
- Score d'une manche = (cartes au centre x 1) + (cartes restantes x -2)
- **Fin de partie** : quand un joueur atteint **100 points cumulés**, il est le **perdant**
- Le joueur avec le **score le plus bas** est le meilleur
- 2 à 8 joueurs supportés

## Stack technique

- **HTML/CSS/JS vanilla** : un fichier `index.html` unique, zéro dépendance
- **localStorage** pour la persistance des données
- **CSS** : variables CSS, Flexbox/Grid, media queries
- Pas de framework, pas de bundler, pas de build step

## Architecture de l'app

### Single Page App

Tout sur un seul écran avec deux états :

1. **État "config"** : écran d'accueil pour configurer les joueurs (affiché si aucune partie en cours)
2. **État "game"** : tableau des scores avec bouton d'ajout de manche
3. **État "results"** : écran de fin de partie avec podium et classement
4. **État "history"** : historique des parties passées et classement global

### Structure des données (localStorage)

```json
{
  "blitzGame": {
    "players": ["Alice", "Bob", "Charlie"],
    "rounds": [
      [2, -3, 5],
      [10, 4, -1]
    ],
    "inputMode": "direct"
  },
  "blitzHistory": [
    {
      "date": "2026-04-04T18:30:00Z",
      "players": ["Alice", "Bob", "Charlie"],
      "rounds": [[2, -3, 5], [10, 4, -1]],
      "totals": [12, 1, 4],
      "loser": "Alice",
      "ranking": ["Bob", "Charlie", "Alice"]
    }
  ]
}
```

- `blitzGame` : partie en cours
  - `players` : tableau de noms (2-8 éléments)
  - `rounds` : tableau de manches, chaque manche est un tableau de scores (même ordre que players)
  - `inputMode` : dernier mode de saisie utilisé ("direct" ou "detailed")
- `blitzHistory` : tableau de parties terminées
  - `date` : date ISO de fin de partie
  - `players`, `rounds`, `totals` : données de la partie
  - `loser` : nom du joueur ayant atteint 100
  - `ranking` : classement du meilleur au moins bon (score le plus bas = meilleur)

## Écrans et composants

### 1. Écran de configuration

- Titre "Blitz Score" avec visuel ludique (icône cartes)
- Sélecteur nombre de joueurs (2-8) : boutons ou stepper
- Champs de saisie pour les noms des joueurs (placeholder : "Joueur 1", "Joueur 2"...)
- Bouton "Commencer la partie"
- Validation : au moins 2 joueurs avec des noms non vides

### 2. Écran de jeu (tableau des scores)

- **Header** : titre "Blitz Score" + bouton menu (nouvelle partie)
- **Tableau des scores** :
  - Colonnes = joueurs (noms dans le header du tableau)
  - Lignes = manches numérotées (Manche 1, 2, 3...)
  - Cellules cliquables pour éditer un score passé
  - Dernière ligne fixe sticky = **Totaux** en gras
  - Le leader (score le plus bas) est mis en évidence visuellement (couleur/badge)
  - Le joueur en danger (proche de 100) est marqué en rouge
  - Scroll horizontal si plus de 4 joueurs sur mobile
- **Bouton flottant "+"** en bas à droite pour ajouter une manche
- **Bouton historique** : accès à l'historique des parties et classement global

### 3. Modal de saisie de manche

- **Toggle** en haut : "Score direct" / "Détaillé"
- **Mode direct** :
  - Un champ numérique par joueur (accepte les négatifs)
  - Label = nom du joueur
- **Mode détaillé** :
  - Deux champs par joueur : "Cartes au centre" et "Cartes restantes"
  - Score calculé affiché en temps réel : `centre × 1 + restantes × (-2) = X`
- Bouton "Valider la manche"
- Bouton "Annuler"

### 4. Édition d'un score

- Tap sur une cellule du tableau ouvre un modal d'édition (cohérent avec le modal de saisie)
- Permet de corriger le score de cette manche pour ce joueur
- Recalcule les totaux automatiquement

### 5. Écran de fin de partie (Results)

Affiché automatiquement quand un joueur atteint 100 points :

- **Annonce du perdant** : nom du joueur ayant atteint 100 pts, mise en évidence
- **Podium top 3** : affichage visuel avec médailles or/argent/bronze (positions 1-2-3 par score le plus bas)
- **Classement complet** : tous les joueurs classés du meilleur au moins bon avec leurs scores
- **Actions** :
  - "Nouvelle partie (mêmes joueurs)" : relance avec les mêmes noms
  - "Nouvelle partie" : retour à la config
  - "Voir l'historique" : accès à l'historique
- La partie terminée est automatiquement sauvegardée dans `blitzHistory`

### 6. Écran historique

Accessible depuis l'écran de jeu ou l'écran de config :

- **Classement global** : tableau des joueurs tous temps confondus
  - Colonnes : Nom, Parties jouées, Victoires (1ère place), Score moyen, Podiums (top 3)
  - Trié par nombre de victoires, puis par score moyen (le plus bas = meilleur)
- **Liste des parties passées** : date, joueurs, vainqueur, scores finaux
  - Chaque partie est expandable pour voir le détail des manches
- **Bouton retour** vers l'écran en cours (config ou jeu)

### 7. Actions

- **Supprimer la dernière manche** : bouton avec confirmation
- **Nouvelle partie** : confirmation, option de garder les mêmes joueurs ou de reconfigurer
- **Reset complet** : retour à l'écran de configuration
- **Supprimer l'historique** : bouton avec double confirmation dans l'écran historique

## Design visuel

### Palette de couleurs

- **Fond principal** : vert foncé rappelant un tapis de jeu (#2d5016 -> #1a3409)
- **Cartes/conteneurs** : blanc cassé (#f5f0e8) avec coins arrondis et ombres
- **Accents** : rouge (#e74c3c), bleu (#3498db), jaune (#f1c40f), orange (#e67e22)
- **Texte** : blanc sur fond vert, noir sur cartes blanches
- **Leader** : badge doré / surbrillance jaune

### Typographie

- Font system stack pour la performance
- Titres bold, scores en grande taille pour lisibilité
- Taille minimum 16px pour éviter le zoom auto sur iOS

### Éléments visuels

- Conteneurs avec style "carte à jouer" (coins arrondis 12px, ombre portée)
- Animations subtiles : apparition des scores, highlight du nouveau tour
- Bouton flottant avec ombre et effet press
- Icônes simples en SVG inline (pas de dépendance)

## Responsive / Mobile

- **Mobile-first** : design optimisé pour 375px de large
- Tableau avec scroll horizontal et header joueurs sticky
- Modal plein écran sur mobile (<768px), centré sur desktop
- Zones tactiles minimum 44x44px
- Pas de hover-only interactions
- viewport meta tag pour empêcher le zoom non désiré

## Persistance

- Sauvegarde dans `localStorage` à chaque modification (ajout manche, édition, suppression)
- Clé unique : `blitzGame`
- Au chargement : lecture du localStorage, si données présentes -> afficher l'écran de jeu, sinon -> config
- Pas de sauvegarde côté serveur

## Vérification

1. Ouvrir `index.html` dans un navigateur
2. Configurer 3-4 joueurs, commencer une partie
3. Ajouter quelques manches en mode direct et détaillé
4. Vérifier que les totaux sont corrects
5. Fermer et rouvrir le navigateur -> les scores doivent être conservés
6. Tester l'édition d'un score passé
7. Tester la suppression de la dernière manche
8. Tester la fin de partie : amener un joueur à 100+ pts
9. Vérifier le podium : top 3 correct, perdant identifié, classement complet
10. Vérifier que la partie terminée apparaît dans l'historique
11. Jouer 2-3 parties et vérifier le classement global (victoires, score moyen)
12. Tester sur mobile (ou responsive mode du navigateur) : scroll horizontal, modal plein écran
13. Tester "Nouvelle partie" avec et sans garder les joueurs
