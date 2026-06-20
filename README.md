# ArrHub
Guide d'installation d'outils d'automatisation et de gestion de téléchargements touts médias, stable et safe, pour Plex/Jellyfin (ou autre).

<h2>Caractéristiques</h2>

Stack utilisé sur un Synology DS918+, DSM 7.1.1-42962 Update 6 mais cela devrait fonctionner avec n'importe quel serveur sur lequel on peut installer Docker.<br>
<br>
<b>Toutes les app sensibles passent à travers un client VPN, donc avoir un compte chez un fournisseur VPN, de préférence avec redirection de port (Proton VPN ou autre) est requis pour utiliser tout cela.</b>

### Prérequis

- Un NAS ou serveur capable d'exécuter Docker
- Docker (ou Container Manager sous DSM 7.2+)
- Portainer CE
- Un fournisseur VPN compatible OpenVPN ou WireGuard (Proton VPN, Mullvad, AirVPN, etc.)
- Un compte Plex (ou équivalent)

<h3><a href="https://docs.portainer.io/start/install-ce" target="_blank">Pourquoi utiliser Portainer ?</a></h3>

Bien qu'il soit tout à fait possible de gérer cette stack directement avec Docker Compose en ligne de commande, ce guide s'appuie sur **Portainer CE** afin de simplifier l'administration quotidienne.

Portainer fournit une interface web permettant notamment de :

- Déployer des stacks Docker à partir d'un fichier Compose
- Démarrer, arrêter ou redémarrer des conteneurs
- Consulter les logs des applications
- Modifier facilement la configuration d'une stack
- Accéder à un terminal dans les conteneurs
- Gérer les volumes, réseaux et images Docker
- Mettre à jour ou recréer des services sans utiliser le terminal

L'utilisation de Portainer n'est pas obligatoire mais elle facilite grandement la maintenance de l'environnement, en particulier pour les utilisateurs peu familiers avec Docker en ligne de commande.

Dans ce guide, toutes les opérations de déploiement et de gestion des conteneurs seront réalisées depuis l'interface web de Portainer.

## Architecture des dossiers requise

Pour que les **hardlinks** fonctionnent correctement avec Radarr et Sonarr, le dossier contenant les téléchargements doit impérativement être situé sur le **même volume** que les bibliothèques de médias.

Un hardlink est simplement une seconde référence vers un même fichier sur le disque. Lors de l'import d'un film ou d'une série, Radarr/Sonarr créent alors un lien vers le fichier téléchargé au lieu de le copier.

### Avantages

- Conservation du seeding dans qBittorrent
- Aucune duplication des données
- Import quasi instantané
- Économie d'espace disque
- Réduction des écritures sur les disques

### Exemple d'arborescence

```text
/volume1
│
├── docker
│   ├── gluetun
│   ├── qbittorrent
│   ├── radarr
│   ├── sonarr
│   ├── prowlarr
│   └── ...
│
└── media
    ├── Films
    ├── Series
    └── Torrents
        ├── InProgress
        ├── Movies
        ├── TV Shows
        ├── Music
        ├── Books
        └── _torrent-files
```

⚠️ Si les dossiers `Torrents`, `Films` et `Series` sont situés sur des volumes différents, les hardlinks seront impossibles et Radarr/Sonarr effectueront une copie complète des fichiers.

⚠️ Pour maximiser les chances de fonctionnement des hardlinks sous Docker, il est recommandé de monter le dossier parent commun (`/volume1/media`) dans les conteneurs plutôt que de monter séparément les dossiers `Films`, `Series` et `Torrents`.

<br>

> [!TIP]
> **Exemple de montage recommandé dans Radarr, Sonarr et qBittorrent :**
> - Host : `/volume1/media`
> - Container : `/media`

<br>


<h2>Guide</h2>

<ol>
    <li>Créer un dossier partagé "docker", puis, dedans, le dossier "portainer"</li>
    <li>Installer Docker (ou Container Manager dans DSM 7.2) [<a href="https://linuxhint.com/run-docker-containers-synology-nas/" target="_blank">tuto</a>]</li>
    <li>Installer "portainer-ce" dans Docker [<a href="https://mariushosting.com/how-to-install-portainer-on-your-synology-nas/" target="_blank">tuto</a>]</li>
    <li>Créer les dossiers suivant* dans le dossier partagé "docker" :<br>
    bazarr<br>
    cleanuparr<br>
    cross-seed<br>
    flaresolverr<br>
    <b>gluetun</b><br>
    jackett<br>
    prowlarr<br>
    seerr<br>
    qbittorrent<br>
    radarr<br>
    sonarr<br>
    watchtower</li>
    <li>Ouvrir l'interface Portainer puis créer une nouvelle stack via `Stacks > Add Stack` en utilisant le Web Editor (<a href=https://docs.portainer.io/user/docker/stacks/add#option-1-web-editor target="_blank">tuto</a>))</li>
    <li>Copier/coller le code docker-compose que j'ai rédigé ici pour installer l'ensemble des containers que j'ai sélectionné* : <a href="https://github.com/Pandaarr/docker-compose-advanced-media/blob/main/stack-portainer-ce" target="_blank">stack-portainer-ce</a>
    <li>Editer les 'XXXX' ainsi que les PUID, PGID et volumes pour que cela fonctionne avec votre systeme</li>
    PS : Pour connaitre le PUID et PGID d'un utilisateur que vous avez créé via l'interface de votre Synology : <a href="https://mariushosting.com/synology-how-to-find-uid-userid-and-gid-groupid/" target="_blank">tuto</a>
    <li>Cliquer sur "Deploy the stack" tout en bas (vous inquiétez pas, c'est éditable à volonté si jamais vous zappez un truc !)</li>
    <li>Configurer l'ensemble des images installées avec leurs doc officielles (liens en # commentaire # dans le code du github)</li>
</ol>

<h4>*Liste des containers présents dans la stack suggérée et leurs fonctionnalités :</h4>

<ul>
    <li><b><a href="https://radarr.video/" target="_blank">Radarr</a></b><br>
    Automatisation et gestion de téléchargement de films</li>
    <li><b><a href="https://sonarr.tv/" target="_blank">Sonarr</a></b><br>
    Automatisation et gestion de téléchargement de séries</li>
    <li><b><a href="https://www.bazarr.media/" target="_blank">Bazarr</a></b><br>
    Automatisation et gestion de téléchargement de sous-titres pour films et séries</li>
    <li><b><a href="https://seerr.dev/" target="_blank">Seerr</a></b><br>
    Outil de gestion des requêtes (les vôtres et celles des utilisateurs de votre Plex) et de découverte de médias conçu pour fonctionner avec Plex, Radarr et Sonarr</li>
    <li><b><a href="https://github.com/qdm12/gluetun#features" target="_blank">Gluetun</a></b><br>
    Client VPN pour plusieurs fournisseurs VPN, écrit en Go et utilisant OpenVPN ou Wireguard, DNS sur TLS, avec quelques serveurs proxy intégrés</li>
    <li><b><a href="https://github.com/FlareSolverr/FlareSolverr" target="_blank">Flaresolverr</a></b> <i style="font-size: 12px;">(passe par gluetun)</i><br>
    Serveur proxy pour contourner la protection Cloudflare et DDoS-GUARD, à renseigner dans Jackett</li>
    <li><b><a href="https://github.com/Jackett/Jackett" target="_blank">Jackett</a></b> <i style="font-size: 12px;">(passe par gluetun)</i><br>
    Passerelle entre vos trackers torrent et Radarr, Sonarr...</li>
	<li><b><a href="https://github.com/prowlarr/prowlarr" target="_blank">Prowlarr</a></b> <i style="font-size: 12px;">(passe par gluetun)</i><br>
    Alternative à Jackett, Prowlarr est un gestionnaire d'indexeurs/proxy développé à partir de la pile de base populaire *arr .net/reactjs, conçu pour s'intégrer à vos différentes applications PVR. Il prend en charge la gestion aussi bien des trackers Torrent que des indexeurs Usenet. </li>
	<li><b><a href="https://github.com/cross-seed/cross-seed" target="_blank">Cross-seed</a></b> <i style="font-size: 12px;">(passe par gluetun)</i><br>
    Le « cross-seeding » consiste à télécharger un torrent à partir d'un tracker et à utiliser ces données pour partager des fichiers sur d'autres trackers. Cela permet de limiter au maximum les téléchargements tout en partageant instantanément des fichiers, ce qui en fait un excellent moyen d'améliorer son ratio et de contribuer à la communauté.</li>
	<li><b><a href="https://github.com/Cleanuparr/Cleanuparr" target="_blank">Cleanuparr</a></b><br>
Outil de maintenance pour l'écosystème *Arr permettant notamment de supprimer automatiquement les torrents ayant atteint certains critères (ratio, temps de seed, état dans Radarr/Sonarr, etc.), d'éviter l'accumulation de téléchargements inutiles et de conserver une bibliothèque propre. En gros, si vous supprimez un média dans Radarr/Sonarr, cela va créer un "orphelin" qui n'a pas de hardlink dans votre dossier de téléchargement torrent. Lors du scan de Cleanuparr, ce dernier sera déplacé dans un dossier "unlinked" dans Qbittorrent et sera supprimé physiquement et définitivement à partir du moment où le temps de seed minimum paramétré est atteint.</li>
    <li><b><a href="https://github.com/linuxserver/docker-qbittorrent" target="_blank">qBittorrent</a></b> <i style="font-size: 12px;">(passe par gluetun)</i><br>
    Client torrent de qualité</li>
    <li><b><a href="https://github.com/containrrr/watchtower/" target="_blank">Watchtower</a></b><br>
    MAJ automatique de toutes les images de vos containers ne contenant pas le label 'com.centurylinklabs.watchtower.enable: "false"'</li>
</ul>
