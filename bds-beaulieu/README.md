# App Beaulieu — BDS du Lycée Lyautey

Application du Bureau des Sports pour les événements du complexe sportif Beaulieu (Casablanca) :
accueil des invités par QR code, carte interactive du site, live des matchs, annonces,
espace des quatre guildes (Ambassadeurs, Logistique, Arbitres, Com) et tableaux de bord admin.

**Statut : maquette.** Il n'y a pas encore de code fonctionnel ; la version fonctionnelle se construit à partir de `CLAUDE.md`.

## Contenu

| Chemin | Contenu |
|---|---|
| `CLAUDE.md` | Brief complet : fonctions, choix techniques, modèle de données, carte, confidentialité, étapes. Claude Code le lit automatiquement. |
| `assets/plan-beaulieu.svg` / `.png` | Plan du site avec légende, échelle et nord. |
| `assets/plan-beaulieu-fond.svg` | Fond de plan sans étiquettes, pour la carte de l'app. |
| `assets/points-beaulieu.json` | Coordonnées des terrains, vestiaires, services et bâtiments, périmètre du site et zone hachurée. |
| `maquette/` | Captures des 14 écrans (invités, guildes, admin). |
| `maquette/source/` | Sources de la maquette (format du canevas Claude Design), à lire comme référence. |

## Maquette interactive

Canevas Claude (privé, à partager depuis son bouton « Partager ») :
https://claude.ai/artifact/A8sc6i9xu8R728nxuZn27X

## Démarrer la version fonctionnelle

Ouvrir Claude Code à la racine du dépôt, puis :

```
Lis CLAUDE.md, regarde les captures dans maquette/ et les fichiers dans assets/.
On construit la version fonctionnelle de l'app Beaulieu (web app installable, téléphone d'abord).
Avant de coder : propose l'architecture, le schéma Supabase avec les règles d'accès par rôle
(invité, guildes, admins) et un découpage en étapes. Pose-moi tes questions.
Commence ensuite par la carte et le parcours invité QR → vestiaire.
```

## À confirmer par le BDS

Emplacements réels des vestiaires et des services, noms des terrains, formats de compétition,
signification de la zone hachurée, charte graphique de la Com.
Les équipes, scores et personnes visibles dans la maquette sont des exemples fictifs.
