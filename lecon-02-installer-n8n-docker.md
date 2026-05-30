# Leçon 2 — Installer n8n en local avec Docker Desktop

> [!TIP]
> **Objectif de la Leçon 2 — Avoir n8n qui tourne _chez toi_, en 5 minutes, sans rien casser.**
>
> Cette leçon est un **mode d'emploi pas à pas**. Tu peux la suivre du début à la fin sans rien deviner. On n'installe **aucun** logiciel compliqué : juste **Docker Desktop**, puis un petit fichier `docker-compose.yml` qui démarre n8n pour toi.
>
> À la fin de cette leçon, tu auras :
> 1. **Docker Desktop** installé et en marche.
> 2. n8n démarré avec **une seule commande**.
> 3. L'interface n8n ouverte sur **`http://localhost:5678`**.
> 4. Compris comment **arrêter**, **redémarrer** et **mettre à jour** n8n.
>
> Phrase clé de cette leçon : **avec Docker, on ne « bricole » pas son ordinateur — on lance une boîte propre et jetable.**

## 2.1 Pourquoi Docker (et pas une installation classique)

Installer un logiciel « à l'ancienne » oblige souvent à installer aussi tout ce dont il a besoin pour fonctionner (les bonnes versions de Node.js, des bibliothèques, etc.). C'est une source d'erreurs sans fin, surtout pour un débutant : « ça marche sur la machine du prof mais pas sur la mienne ».

**Docker** résout ce problème. Il permet de lancer un logiciel à l'intérieur d'un **conteneur** : une sorte de mini-ordinateur isolé, qui contient déjà **tout** ce dont n8n a besoin pour marcher. Tu n'installes rien d'autre sur ta vraie machine, et tu peux tout supprimer proprement en une commande.

> [!NOTE]
> **Analogie.** Docker, c'est comme une **boîte-repas déjà préparée**. Au lieu d'acheter les ingrédients un par un, de vérifier les quantités et de risquer d'en oublier, tu reçois une boîte complète où tout est déjà dedans, dans les bonnes proportions. Tu la réchauffes, tu manges, et quand tu as fini, tu jettes la boîte sans avoir sali toute la cuisine.

Deux mots de vocabulaire à connaître :

- Une **image** est le modèle figé du logiciel (ici, l'image officielle `n8nio/n8n`). C'est la « recette + ingrédients » en conserve.
- Un **conteneur** est une **instance** lancée à partir d'une image. C'est le plat qui tourne réellement.

## 2.2 Installer Docker Desktop

Docker Desktop est l'application qui gère Docker sur ton ordinateur, avec une interface visuelle.

1. Va sur le site officiel : **`https://www.docker.com/products/docker-desktop`**.
2. Télécharge la version qui correspond à ton système (**Windows**, **macOS** ou **Linux**).
3. Lance l'installateur et suis les étapes (accepte les réglages par défaut).
4. **Redémarre ton ordinateur** si l'installateur te le demande.
5. Ouvre **Docker Desktop** et attends que l'icône (la petite baleine 🐳) indique **« Engine running »** (moteur en marche).

> [!NOTE]
> **Sur Windows.** Docker Desktop a besoin de **WSL 2** (un sous-système Linux intégré à Windows). L'installateur le propose automatiquement ; accepte. Si un message demande d'activer la « virtualisation », c'est un réglage du BIOS qui est presque toujours déjà activé sur les ordinateurs récents.

Pour vérifier que Docker est bien installé, ouvre un terminal (sur Windows : **PowerShell** ; sur macOS : **Terminal**) et tape :

```bash
docker --version
docker compose version
```

Si les deux commandes affichent un numéro de version, **tu es prêt**. Sinon, assure-toi que Docker Desktop est bien **ouvert et démarré**.

## 2.3 Créer le dossier du projet

On va ranger tout le cours dans un seul dossier. Ouvre ton terminal et place-toi là où tu veux créer ce dossier (par exemple ton bureau ou tes documents), puis :

```bash
# 1. Crée le dossier du projet et entre dedans
mkdir facebooks-n8n-simple
cd facebooks-n8n-simple
```

> [!NOTE]
> **Tu as déjà ce dossier.** Si tu lis cette leçon, le dossier `facebooks-n8n-simple` existe sans doute déjà avec le fichier `docker-compose.yml` à l'intérieur. Dans ce cas, ouvre simplement un terminal **dans ce dossier** et saute à l'étape 2.5.

## 2.4 Le fichier docker-compose.yml expliqué

Le fichier **`docker-compose.yml`** est la « fiche de commande » que tu donnes à Docker. Il décrit **quel** logiciel lancer et **comment**. Le voici, déjà prêt dans ton dossier :

```yaml
services:
  n8n:
    image: docker.n8n.io/n8nio/n8n:latest
    container_name: n8n-local
    restart: unless-stopped
    ports:
      - "5678:5678"
    environment:
      - N8N_HOST=localhost
      - N8N_PORT=5678
      - N8N_PROTOCOL=http
      - WEBHOOK_URL=http://localhost:5678/
      - GENERIC_TIMEZONE=America/Toronto
      - TZ=America/Toronto
    volumes:
      - ./n8n_data:/home/node/.n8n
      - ./fichiers:/data
```

Décortiquons les lignes importantes, car comprendre ce fichier t'évitera 90 % des problèmes :

| Ligne | Ce qu'elle fait |
|-------|-----------------|
| `image:` | Le logiciel à lancer : l'image **officielle** de n8n, version la plus récente (`latest`). |
| `container_name:` | Le petit nom de notre conteneur (`n8n-local`), pour le repérer facilement. |
| `restart: unless-stopped` | n8n redémarre tout seul si ton ordinateur redémarre (sauf si tu l'as arrêté exprès). |
| `ports: "5678:5678"` | Relie le port **5678 de ta machine** au port 5678 **du conteneur**. C'est ce qui rend `http://localhost:5678` accessible. |
| `environment:` | Des **réglages** : le fuseau horaire (essentiel pour les déclencheurs horaires de la Leçon 4) et l'adresse web. |
| `volumes:` | Le point **le plus important** : voir l'encadré ci-dessous. |

> [!NOTE]
> **Les volumes = la mémoire de n8n.** Un conteneur est « jetable » : si tu le supprimes, tout ce qu'il contenait disparaît. Les **volumes** sont des dossiers de **ta** machine reliés à l'intérieur du conteneur, pour que tes données **survivent**. Ici, `./n8n_data` garde tes workflows et tes identifiants (chiffrés), et `./fichiers` est un dossier partagé pour lire/écrire des fichiers (on s'en servira en Leçon 6). Sans volume, tu perdrais tout ton travail à chaque redémarrage.

## 2.5 Démarrer n8n (la commande magique)

Place-toi dans le dossier qui contient `docker-compose.yml` (vérifie avec la commande qui liste les fichiers), puis lance :

```bash
# Démarre n8n en arrière-plan (-d = "detached", il tourne en fond)
docker compose up -d
```

La **première fois**, Docker va **télécharger** l'image de n8n (quelques centaines de Mo). C'est normal que ça prenne une ou deux minutes. Les fois suivantes, le démarrage sera quasi instantané.

Quand la commande se termine, vérifie que le conteneur tourne :

```bash
docker compose ps
```

Tu dois voir une ligne avec `n8n-local` et le statut **`running`** (ou `Up`). Tu peux aussi le voir directement dans l'application **Docker Desktop**, onglet **Containers**.

## 2.6 Ouvrir n8n dans ton navigateur

Ouvre ton navigateur et va sur :

```
http://localhost:5678
```

La **première fois**, n8n te demande de créer un **compte propriétaire local** (un email et un mot de passe). C'est un compte **local**, qui reste sur ta machine : mets une adresse et un mot de passe que tu retiendras, ce n'est pas relié à Internet.

> [!NOTE]
> **« Le site ne s'ouvre pas. »** Patiente 20-30 secondes après le démarrage : n8n met un petit moment à être prêt. Si ça ne marche toujours pas, regarde les logs (section 2.8) pour voir si le conteneur a bien démarré, et vérifie qu'aucun autre programme n'utilise déjà le port 5678.

Une fois connecté, tu arrives sur l'écran d'accueil de n8n, avec un bouton pour créer ton premier workflow. **Bravo — ton n8n tourne en local !** On s'arrête là pour l'installation : la Leçon 3 te fera créer ton tout premier workflow.

## 2.7 Arrêter, redémarrer et mettre à jour n8n

Voici les seules commandes dont tu auras besoin au quotidien. Lance-les toujours **depuis le dossier** qui contient `docker-compose.yml`.

```bash
# ARRÊTER n8n (les données sont conservées grâce aux volumes)
docker compose stop

# REDÉMARRER n8n après un stop
docker compose start

# ARRÊTER ET SUPPRIMER le conteneur (les volumes/données restent)
docker compose down

# METTRE À JOUR n8n vers la dernière version
docker compose pull        # télécharge la nouvelle image
docker compose up -d        # relance avec la nouvelle version
```

> [!NOTE]
> **`stop` vs `down`.** `stop` met n8n « en pause » : il s'arrête mais reste prêt à repartir avec `start`. `down` supprime le conteneur (mais **pas** tes volumes `./n8n_data` et `./fichiers`, donc tes workflows sont saufs). Pour un usage normal, `stop`/`start` suffisent largement.

## 2.8 Voir ce qui se passe (les logs)

Si quelque chose ne marche pas, les **logs** (le journal de bord du conteneur) te disent pourquoi. Pour les afficher :

```bash
# Affiche les derniers messages de n8n et suit en direct (Ctrl+C pour quitter)
docker compose logs -f n8n
```

Au démarrage normal, tu verras une ligne qui ressemble à :

```
Editor is now accessible via: http://localhost:5678
```

C'est le signe que tout va bien. Si tu vois une erreur en rouge, copie-la : c'est ton meilleur point de départ pour comprendre un problème.

## 2.9 Tout supprimer proprement (remise à zéro)

Si un jour tu veux repartir **complètement** de zéro (effacer n8n ET tous tes workflows), tu peux tout nettoyer. **Attention : cette opération est irréversible.**

```bash
# 1. Arrête et supprime le conteneur
docker compose down

# 2. Supprime les dossiers de données (tes workflows seront PERDUS)
#    Sur macOS / Linux :
rm -rf n8n_data fichiers
#    Sur Windows (PowerShell) :
#    Remove-Item -Recurse -Force n8n_data, fichiers
```

C'est là toute la beauté de Docker : ton ordinateur reste propre, et tu repars d'une page blanche quand tu veux.

## Recap

> [!TIP]
> **Avant la Leçon 3, assure-toi de savoir :**
>
> 1. Pourquoi on utilise **Docker** (un conteneur isolé, rien à bricoler sur ta machine).
> 2. La différence entre une **image** (le modèle) et un **conteneur** (l'instance qui tourne).
> 3. À quoi sert chaque ligne du **`docker-compose.yml`**, surtout les **ports** et les **volumes**.
> 4. Lancer n8n avec **`docker compose up -d`** et l'ouvrir sur **`http://localhost:5678`**.
> 5. **Arrêter / redémarrer** (`stop` / `start`) et **mettre à jour** (`pull` puis `up -d`).
> 6. Lire les **logs** avec `docker compose logs -f n8n`.
>
> **Retiens : avec Docker, on lance une boîte propre et jetable — tes données vivent dans les volumes, pas dans le conteneur.**

Dans la **Leçon 3**, on entre enfin dans n8n : tu vas construire ton **tout premier workflow**, le plus simple possible (« Hello n8n »), pour comprendre concrètement le trigger, les nœuds et la circulation des items vue en Leçon 1.
