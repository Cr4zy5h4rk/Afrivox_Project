# AfriVox

**La plateforme africaine de données vocales, pour les devs, par les devs.**

Projet présenté au concours **CADEV 2026** (Systalink). Les contributeurs enregistrent et valident des phrases dans des langues africaines. Les développeurs y trouvent des datasets versionnés, un Model Hub et une API. Les organisations peuvent demander un dataset sur mesure.

🌐 **Démo en ligne** : https://afrivox.duckdns.org
🚀 **Démarrage rapide** : https://afrivox.duckdns.org/quickstart


## 🔐 Comptes de démonstration – Jury

Les comptes suivants permettent au jury de tester les différentes fonctionnalités de la plateforme.

| Rôle | Login | Mot de passe |
|---|---|---|
| Contributeur | `jury_contrib` | `ZXJqHVYr4ugpaj` |
| Développeur | `jury_dev` | `dKD1FpNJZt220i` |
| Administrateur | `jury_admin` | `P2kx2N9HZTSK51` |

### 👤 Fonctionnalités par rôle

#### 1. Contributeur (`jury_contrib`)
- Enregistrer des phrases.
- Valider des enregistrements.
- Consulter ses crédits.
- Aucune limite de 10 envois (compte hors période d'essai).
- Compléter le questionnaire de profil obligatoire avant tout enregistrement : âge, genre, pays et région.
- La validation des enregistrements nécessite une qualification préalable, qui doit être approuvée par l'administrateur.

#### 2. Développeur (`jury_dev`)
- Parcourir le catalogue de datasets.
- Acheter un dataset (paiement simulé).
- Publier un modèle.
- Soumettre des prédictions au classement.
- Créer des clés API.
- L'accès à l'espace contributeur est volontairement interdit (HTTP 403).

#### 3. Administrateur (`jury_admin`)
- Administrer les utilisateurs et les campagnes.
- Gérer les datasets, les qualifications et les crédits.
- Effectuer le contrôle qualité.
- Désactiver des comptes.
- Publier ou retirer des versions.
- Accéder à l'ensemble des fonctionnalités d'administration.

> **Note :** Ces identifiants sont fournis exclusivement pour les tests et la démonstration par le jury. 


---

## Dépôts du projet

Ce dépôt est le point d'entrée. Le code est réparti en deux dépôts.

| Dépôt | Contenu | Technologies |
|---|---|---|
| **[AfriVox (backend)](https://github.com/Cr4zy5h4rk/AfriVox)** | API REST, base de données, déploiement (`deploy/`), scripts de test, client Python | Spring Boot (JHipster), Java 21, PostgreSQL, Liquibase, Maven |
| **[Afrivox_Frontend](https://github.com/Cr4zy5h4rk/Afrivox_Frontend)** | Application web (PWA) | Angular, Angular Material |

Les deux dépôts se clonent côte à côte :

```bash
git clone https://github.com/Cr4zy5h4rk/AfriVox  : Backend
git clone https://github.com/Cr4zy5h4rk/Afrivox_Frontend : Frontend
```

---

## Sommaire

1. [Le problème et l'idée](#le-problème-et-lidée)
2. [Rôles](#rôles)
3. [Fonctionnalités](#fonctionnalités)
4. [Fonctionnement d'une donnée, de l'enregistrement au téléchargement](#fonctionnement-dune-donnée)
5. [Idées innovantes](#idées-innovantes)
6. [Architecture](#architecture)
7. [Stack technique](#stack-technique)
8. [Sécurité](#sécurité)
9. [Qualité et tests](#qualité-et-tests)
10. [Licences des dépendances](#licences-des-dépendances)
11. [Données de démonstration](#données-de-démonstration)
12. [Installer et lancer en local](#installer-et-lancer-en-local)
13. [Déploiement](#déploiement)
14. [Client Python](#client-python)
15. [Limites connues et suite](#limites-connues-et-suite)

---

## Le problème et l'idée

Les langues africaines manquent de données vocales de qualité, ouvertes et équitablement rémunérées. AfriVox met en relation trois parties :

- des **contributeurs** qui enregistrent et relisent des phrases, rémunérés en crédits ;
- des **développeurs** qui utilisent les datasets pour entraîner et publier des modèles ;
- des **organisations** (opérateurs, banques, ministères, startups, universités) qui demandent des datasets personnalisés sur devis.

Pour la santé et la banque, la plateforme n'enregistre **jamais de vraies conversations contenant des données personnelles**, seulement des phrases écrites à l'avance.

---

## Rôles

| Rôle | Ce qu'il fait |
|---|---|
| **Contributeur** | Enregistre des phrases, relit celles des autres, gagne des crédits |
| **Développeur** | Profil public, télécharge des datasets (Starter gratuit, Plus, Pro), publie des modèles et des projets, crée des clés d'API |
| **Organisation** | Soumet une demande de dataset, suit son statut (reçue, étude, devis, acceptée, collecte, livrée), peut contacter des développeurs |
| **Admin** | Étudie et chiffre les demandes, ouvre les campagnes, gère langues, datasets, utilisateurs, crédits, modération |

---

## Fonctionnalités

### Contributeurs
- Enregistrement dans le navigateur (PWA, mobile d'abord). Chaque enregistrement est converti en **WAV 16 kHz mono** avant l'envoi.
- Questionnaire de profil (âge, genre, pays, région), **déclaratif et non vérifié**, avec confirmation de majorité et langue maternelle à l'inscription.
- **Période d'essai** : plafond d'envois, puis passage automatique en compte actif si le taux d'acceptation est suffisant.
- **Crédits** : registre append-only, tarifs réglables par l'admin, surcharge possible par campagne.
- **Retrait du consentement** depuis la page profil.

### Validation par les pairs
- Chaque enregistrement est relu par 3 validateurs qualifiés au plus (jamais son auteur). Deux avis concordants tranchent.
- **Tâches pièges** : environ 1 tâche sur 5 est un enregistrement déjà tranché à l'unanimité. Elles mesurent la fiabilité du validateur, et une fiabilité trop basse révoque sa qualification.
- **Activation d'une langue** : une langue passe de la liste d'attente à active quand elle atteint le seuil de validateurs qualifiés.

### Contrôles qualité automatiques
- Mesures sur les échantillons réels des WAV PCM 16 bits : durée, niveaux, saturation, silence, estimation du rapport signal/bruit, composante continue.
- Note de 0 à 100, rejet des enregistrements inexploitables (silence, trop faible, saturé, durée incohérente).
- Les formats compressés ne sont pas analysés (limite assumée).

### Catalogue de datasets
- Datasets **versionnés et immuables**, par langue et par domaine, avec plans Starter (gratuit), Plus et Pro.
- Fiche technique style Hugging Face (Fiche, Statistiques, Aperçu, Fichiers, Modèles), **chiffres lus dans l'archive publiée**.
- Paquet livré : `audio/`, `manifest.tsv`, `splits/{train,dev,test}.tsv`, `DATASHEET.md`, `LICENSE.txt`, `checksums.sha256`.
- Achat avec choix de licence (Prototype, Recherche, Commercial). **Le paiement est simulé** dans cette version.
- Téléchargement par **lien signé et expirant**.
- **Copie personnalisée** pour chaque acheteur : noms de fichiers, ordre et marquage inaudible des WAV, pour retrouver l'origine d'une fuite.

### Model Hub
- Publication de modèles sous forme de **métadonnées et de lien**. Les poids ne sont jamais stockés.
- **Classement par WER/CER** : le développeur soumet ses prédictions (CSV), et la plateforme calcule le score contre des transcriptions de test **jamais publiées**.
- **Héritage de licence** : un modèle ne peut pas être publié avec plus de droits que les datasets qui l'ont entraîné.

### Hub des développeurs
- Projets (liens vers dépôt ou démo), commentaires, réponses, signalements et modération.
- Profils publics et annuaire, **privés par défaut**.
- Contact d'organisation vers développeur : l'adresse du développeur n'est jamais communiquée.

### API et outils
- **Clés d'API** à portées limitées (téléchargement, évaluation, publication), stockées sous forme de hash, révocables.
- Client Python et ligne de commande `afrivox`.

### Administration
- Utilisateurs (désactivation plutôt que suppression), datasets, campagnes, demandes, langues, crédits, contributeurs, qualité, modération, traçabilité des fuites.

---

## Fonctionnement d'une donnée

```
Campagne (liste de phrases)
   → Enregistrement (PWA mobile)
   → Contrôles qualité automatiques
   → Validation par 2 à 3 pairs qualifiés (+ tâches pièges)
   → Crédits versés au consensus
   → Version de dataset figée et immuable (consentement actif requis)
   → Catalogue
   → Achat / téléchargement par lien signé et expirant
```

---

## Idées innovantes

1. **Classement de modèles par langue** : jeu de test à transcriptions non publiées, le développeur soumet ses prédictions et le WER est calculé côté plateforme.
2. **Retrait de consentement qui se répercute** : les enregistrements d'un contributeur sont exclus des versions suivantes.
3. **Héritage de licence** : un modèle publié hérite des restrictions des datasets utilisés.
4. **Traçabilité des copies** : empreinte secrète par achat et détection de fuite.

---

## Architecture

```
            Navigateur (PWA Angular)
                     │  HTTPS
                     ▼
        ┌────────────────────────────┐
        │ nginx                      │  sert le frontend
        │  /      → fichiers Angular │  et redirige /api
        │  /api   → Spring Boot      │  (même domaine)
        └─────────────┬──────────────┘
                      ▼
        ┌────────────────────────────┐
        │ API Spring Boot (JHipster) │  JWT, rôles, services métier
        └───────┬──────────┬─────────┘
                ▼          ▼
        PostgreSQL     Disque : audio/ et datasets/
        (Liquibase)    (volumes Docker sauvegardés)
```

Déploiement : un seul serveur cloud Datacloud à Dakar, avec Docker Compose (API, PostgreSQL, nginx, certbot).

---

## Base de données

PostgreSQL 18, exécuté dans Docker ; le schéma est géré par Liquibase (migrations versionnées, appliquées au démarrage de l'API).

| | Dev (`src/main/docker/postgresql.yml`) | Prod (`deploy/docker-compose.yml`) |
|---|---|---|
| Accès | `127.0.0.1:5432` uniquement | aucun port publié, réseau interne Docker |
| Authentification | `trust` (sans mot de passe, local seulement) | mot de passe dans `.env`, jamais commité |
| Persistance | aucune par défaut | volume nommé `dbdata` |

- Démarrer la base seule en dev : `docker compose -f src/main/docker/postgresql.yml up -d`
- Sauvegarde : `deploy/backup.sh` (dump compressé, 7 conservés). **À copier hors du serveur.**
- Restauration : `gunzip -c db-<date>.sql.gz | docker compose exec -T db psql -U afrivox -d afrivox`
- Les audios et archives de datasets ne sont pas en base : ils sont sur les volumes `audio` et `datasets`.

---

## Stack technique

| Couche | Choix |
|---|---|
| Backend | Spring Boot (JHipster monolithe), Java 21, Maven |
| Authentification | JWT (jeton d'accès court en mémoire + cookie de rafraîchissement HttpOnly) |
| Base de données | PostgreSQL, migrations Liquibase 4.33 |
| Journalisation | Log4j2 |
| Frontend | Angular (composants autonomes), Angular Material, PWA |
| Internationalisation | FR / EN à l'exécution, prise en charge de la lecture droite à gauche |
| Déploiement | Docker Compose, nginx, Let's Encrypt |
| Client | Python (bibliothèque standard uniquement) |

Choix volontairement absents : Keycloak, cache, WebSocket, Elasticsearch, Kafka.

---

## Sécurité

- Mots de passe hachés, jetons d'accès courts, jeton de rafraîchissement en cookie HttpOnly Secure SameSite.
- Rôles vérifiés côté serveur, comptes par défaut supprimés, secret JWT et mot de passe admin en variables d'environnement.
- Clés d'API hachées (SHA-256), affichées une seule fois, révocables, sur liste blanche de routes. Une clé n'est jamais admin.
- Limitation des tentatives de connexion et des débits (API, clés, aperçu audio, nginx).
- Validation des entrées et des fichiers audio (type, taille, contenu réel).
- Liens de téléchargement signés (HMAC) et expirants.
- Consentements versionnés et journal des téléchargements.
- HTTPS obligatoire (le micro l'exige), CORS restreint, actuator limité à `health` et `info`.
- Aperçu des datasets : texte seulement, extraits audio derrière un interrupteur par dataset.
- Minimisation des données : ni date de naissance, ni téléphone, ni pièce d'identité, ni photo.

---

## Qualité et tests

- Tests unitaires Java sur la logique métier : WER/CER, consensus et tâches pièges, crédits, période d'essai, activation des langues, prix et liens signés, filigrane, traçabilité, clés d'API, qualité audio, héritage de licence, projets et contacts.
- Client Python : tests avec faux serveur local (`py -3 -m unittest discover -s tests -t .` depuis `clients/python`).
- Frontend : tests unitaires Angular (`ng test`).
- Les tests d'intégration (`*IT`) exigent Docker.
- Documentation : décisions et limites dans le `CLAUDE.md` et les `README` des dépôts, politique de licences dans `docs/LICENCES.md`.

---

## Licences des dépendances

Seules les licences **permissives** (MIT, Apache 2.0, BSD) sont acceptées, conformément au règlement.

- Inventaire généré : `LICENSES-THIRD-PARTY.md` (backend), produit par `node scripts/licenses/generate.mjs`.
- Résultat : **aucune GPL, AGPL ou LGPL** ; quelques dépendances à copyleft faible (EPL, CDDL) sont signalées dans `docs/LICENCES.md`, avec la question à poser à Systalink.
- Liquibase maintenu en 4.33.0 (la série 5.x n'est pas sous licence permissive).

---

## Données de démonstration

Les données présentes sur la démo sont des **échantillons de test**, pas des données de production.

- Les comptes `imp_*` sont créés par des scripts d'import.
- Les **avis de validation des imports sont automatiques**, donc ce n'est pas une validation humaine.
- Les **prédictions du classement de démonstration sont simulées** (modèles « [Démo] … »).
- Les sources de données de test et leurs licences sont documentées dans la section conformité.

---

## Installer et lancer en local

### Prérequis
- JDK 21, Node.js (version compatible Angular), Docker Desktop (tests d'intégration et base)
- Sous Windows, si l'erreur PKIX apparaît : `MAVEN_OPTS=-Djavax.net.ssl.trustStoreType=Windows-ROOT`

### Backend
```bash
cd AfriVox
./mvnw            # démarre l'API en profil dev
```

### Frontend
```bash
cd Afrivox_Frontend
npm install
npm start         # http://localhost:4200
```

Les détails (variables d'environnement, profils, scripts d'import locaux) sont dans le `README` de chaque dépôt.

---

## Déploiement

Le dossier `deploy/` du dépôt backend contient tout le nécessaire : `docker-compose.yml`, Dockerfiles, configuration nginx, `.env.example`, `init-letsencrypt.sh` et `backup.sh`.

```bash
cd AfriVox/deploy
cp .env.example .env     # renseigner les secrets (jamais commités)
./init-letsencrypt.sh    # certificat HTTPS
docker compose build && docker compose up -d
```

Mise à jour : sauvegarde avec `backup.sh`, `git pull` des deux dépôts, puis `docker compose build && docker compose up -d`. Les sauvegardes doivent être copiées **hors du serveur**.

---

## Client Python

Bibliothèque et ligne de commande, sans dépendance.

```bash
export AFRIVOX_URL=https://afrivox.duckdns.org
export AFRIVOX_API_KEY=afv_...

afrivox purchases
afrivox download <dataset>
afrivox testsets
afrivox submit ...
afrivox publish ...
```

Le guide pas à pas est sur la page [/quickstart](https://afrivox.duckdns.org/quickstart).

---

## Limites connues et suite

**Limites assumées**
- Paiement simulé, et crédits non convertibles en argent.
- Formats audio compressés non analysés, et filigrane non robuste aux encodages avec perte.
- Profil des contributeurs déclaratif, sans vérification.
- Le retrait de consentement ne touche que les versions suivantes, pas celles déjà publiées.
- Budget du bundle Angular dépassé, et interface à vérifier sur mobile et en lecture droite à gauche.

**Prochaines étapes**
- Paiements Mobile Money et retrait des crédits.
- Part de ventes pour les contributeurs.
- CI GitHub Actions.
- Détection de doublons et analyse des formats compressés.
- Relecture juridique des textes de licence et de consentement.
- Hébergement des modèles sur le cloud Systalink, avec l’instance GPU
- Essai de modèles en direct

Projet réalisé dans le cadre du concours **CADEV 2026 (Systalink)**.
