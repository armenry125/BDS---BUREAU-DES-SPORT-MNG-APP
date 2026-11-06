# App Beaulieu — BDS du Lycée Lyautey

Brief de départ pour construire la version fonctionnelle avec Claude Code.
Les captures de la maquette sont dans `maquette/`, le plan et les coordonnées dans `assets/`.
Les sources de la maquette sont dans `maquette/source/` (format du canevas Claude Design) : à lire comme référence pour la mise en page, les textes et les données d'exemple ; elles ne s'ouvrent pas seules dans un navigateur.

## Contexte

- **Organisation** : Bureau des Sports (BDS) du Lycée Lyautey, Casablanca. Quatre guildes de Jeunes Organisateurs (JO) :
  - **Ambassadeurs** : gestion de l'événement, coordination des guildes.
  - **Logistique** : distribution, placement et dénombrement des JO.
  - **Arbitres** : arbitrage de toutes les disciplines.
  - **Com** : compte social, charte graphique, logo, maillots.
- **Lieu** : complexe sportif Beaulieu (piste d'athlétisme, terrains intérieurs, plateaux, terrain de football, gymnase).
- **Trois usages** :
  1. **Invités** (sans compte, arrivent par QR code). Usage sur téléphone.
  2. **Guildes** (JO connectés par code). Usage sur téléphone.
  3. **Admins** (Armand et le fondateur du BDS). Tableaux de bord sur ordinateur, consultables aussi sur téléphone.
- **Charte graphique** : pas encore livrée par la Com. Les couleurs et polices de la maquette sont provisoires et doivent rester faciles à remplacer (tokens CSS).

## Fonctions (V1)

### Invités
- **QR code → nom + établissement → vestiaire.** Ce parcours est le cœur de l'accueil : l'écran affiche le vestiaire attribué, le trajet sur la carte depuis l'accueil et le programme de l'équipe.
- **Carte interactive de Beaulieu** : points par discipline (basket, hand, volley, foot, athlé), vestiaires et services. Filtres par discipline, vue d'ensemble par zones, fiche d'un point avec statut (en direct / à venir / libre), score, arbitre(s), JO encadrants, prochain match, itinéraire.
- **Live** : matchs en cours, à venir, résultats ; carte « ton équipe est qualifiée → terrain X à HH:MM ».
- **Annonces** : remises de prix (trophées, médailles), départs de courses et de compétitions, qualifications, infos ; alertes de son équipe.

### Guildes
- Connexion par **code personnel** ; l'écran dépend de la guilde.
- **Logistique** : poste du JO, pointage des JO présents, arrivées des délégations, placement des JO par zone (besoin / présents).
- **Arbitres** : feuille de match (score, fautes, période, chrono) ; la validation met à jour le classement et inscrit les qualifiés au match suivant.
- **Com** : story résultat générée depuis un score, proposition d'annonce envoyée aux Ambassadeurs pour validation.
- **Ambassadeurs** : alertes à traiter (arbitre non confirmé, sous-effectif JO, annonce à valider), message aux guildes.

### Admins
- **Vue d'ensemble** : indicateurs, carte en direct, annonce rapide (maintenant ou programmée), état des terrains, alertes, fil d'activité.
- **Programme & qualifications** : planning des terrains sur la journée, poules et tableau, règles de qualification automatique, désignations arbitres et JO.
- **Délégations & vestiaires** : attribution des vestiaires, effectifs, arrivées, génération des QR codes (affiche d'accueil + un QR par délégation), réglages de confidentialité.
- **Carte & points** : édition des points (nom, type, discipline, emplacement, visibilité invités / guildes), calques, tracé d'itinéraires.

## Choix techniques proposés

- **Web app installable (PWA)**, pas d'application à télécharger : un invité qui scanne le QR doit arriver directement sur la bonne page.
- **Front** : Vite + React + TypeScript, déployé en statique sur **Cloudflare Pages**.
- **Données** : **Supabase** (Postgres, authentification, Row Level Security par rôle, Realtime pour les scores et les annonces, Edge Functions pour la connexion des JO par code et la recherche invitée).
- **Carte** : le fond `assets/plan-beaulieu-fond.svg` + des points stockés en base ; zoom et déplacement au doigt (une petite lib de pan-zoom ou une transformation CSS). Pas besoin de Google Maps.
- **Notifications** : mises à jour en direct dans l'app ; Web Push en option (sur iPhone, uniquement si l'app est ajoutée à l'écran d'accueil).

Ces choix sont une proposition : à valider ou ajuster avant de coder.

## Modèle de données (première version)

- `events` : nom, date, lieu.
- `points` : type (terrain, athle, vestiaire, service), discipline, code, nom, emplacement texte, `x`, `y`, `polygon`, visible_invites, visible_guildes.
- `delegations` : établissement, ville, vestiaire (`points.id`), effectif, heure d'arrivée.
- `participants` : nom, délégation (rien d'autre).
- `matches` : terrain (`points.id`), discipline, compétition, équipe A / B, début prévu, statut, score, période.
- `assignments` : match ou poste, personne, rôle (arbitre, table, JO).
- `staff` : JO, guilde, code (haché).
- `posts` : zone, horaires, JO affectés (Logistique).
- `announcements` : type, titre, texte, public visé, envoi prévu, statut (proposée, publiée, refusée).
- `stages` / `qualification_rules` : poules, rangs, match suivant pour chaque rang.

Rôles et RLS : `anon` (invité : lecture publique limitée), `staff` (par guilde), `admin`.

## Carte : fichiers et coordonnées

- `assets/plan-beaulieu-fond.svg` : fond sans étiquettes pour l'app. `viewBox="50 66 1845 842"`.
- `assets/plan-beaulieu.svg` / `.png` : plan présenté avec légende, échelle et nord.
- `assets/points-beaulieu.json` : périmètre du site, zone hachurée, 40 points (centre `xy` et contour `polygon` des terrains), 23 bâtiments.
- **Unités** : pixels de la capture Google Maps d'origine (2047 × 915), environ 4,92 unités par mètre (approximatif, déduit de la largeur des couloirs).
- **Repère de la piste** : `translate(907.55 553.2) rotate(-10.6)`. Les terrains alignés sur la piste sont décrits dans ce repère dans le SVG.
- Le SVG contient des `id` par élément et des attributs `data-nom`, `data-type`, `data-discipline`, utilisables directement pour l'interactivité.
- **Zone hachurée** (`id="zone-hachuree"`) : conservée telle quelle ; aucun point n'y est placé.
- Les éléments extérieurs au site ne sont pas tracés ; seuls les noms des rues entourent le plan présenté, pour l'orientation.

## Confidentialité

Les participants sont souvent mineurs. Principes retenus dans la maquette :
- ne stocker que le nom et la délégation ;
- recherche par nom **et** établissement, qui renvoie une seule délégation, jamais une liste ;
- aucune liste de participants visible côté invité ;
- effacement des noms 48 h après l'événement ;
- codes JO personnels, désactivés à la fin de l'événement.

À valider avec l'établissement (cadre marocain : loi 09-08, CNDP).

## À confirmer par le BDS

- Emplacement réel des vestiaires (A à F sont **supposés** : gymnase et bâtiment est), de l'accueil, du PC organisation, du poste de secours, du podium, de la buvette et des toilettes.
- Noms officiels des bâtiments et des terrains (Basket 1 à 8, Hand 1 à 4, Volley 1 à 7 ont été numérotés d'après la vue satellite).
- Disciplines, catégories et formats de compétition (poules, tableaux, règles de qualification).
- Signification de la zone hachurée.
- Charte graphique de la Com (couleurs, polices, logo).

Toutes les équipes, scores et personnes visibles dans la maquette sont des exemples fictifs.

## Étapes conseillées

1. Projet, tokens de design provisoires (bleu nuit `#0E1726`, vert citron `#CFF04A`, Barlow Condensed + Inter), PWA.
2. Carte (fond SVG + points depuis la base) et parcours invité QR → vestiaire.
3. Live et annonces en temps réel.
4. Espace guildes : connexion par code, feuille de match, pointage Logistique.
5. Admin : programme, qualifications automatiques, délégations et QR, éditeur de carte.
6. Test sur place à Beaulieu : réseau mobile, lisibilité en plein soleil, temps d'accès depuis le QR.
