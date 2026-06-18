# Docker compose advanced media
Guide d'installation d'outils d'automatisation et de gestion de téléchargements touts médias, stable et safe, pour Plex (ou autre).

<h2>Caractéristiques</h2>

Stack utilisé sur un Synology DS918+, DSM 7.1.1-42962 Update 6 mais cela devrait fonctionner avec n'importe quel serveur sur lequel on peut installer Docker.<br>
<br>
<b>Toutes les app sensibles passent à travers un client VPN, donc avoir un compte chez un fournisseur VPN, de préférence avec redirection de port (Proton VPN ou autre) est requis pour utiliser tout cela.</b>

<h2>Guide</h2>

<ol>
    <li>Créer un dossier partagé "docker", puis, dedans, le dossier "portainer"</li>
    <li>Installer Docker (ou Container Manager dans DSM 7.2) [<a href="https://linuxhint.com/run-docker-containers-synology-nas/" target="_blank">tuto</a>]</li>
    <li>Installer "portainer-ce" dans Docker [<a href="https://mariushosting.com/how-to-install-portainer-on-your-synology-nas/" target="_blank">tuto</a>]</li>
    <li>Créer les dossiers suivant* dans le dossier partagé "docker" :<br>
    bazarr<br>
    cross-seed<br>
    flaresolverr<br>
    <b>gluetun</b><br>
    jackett<br>
    seerr<br>
    qbittorrent<br>
    radarr<br>
    sonarr<br>
    watchtower</li>
    <li>Créer une stack sur Portainer du nom que vous voulez, avec le Web editor [<a href="https://docs.portainer.io/user/docker/stacks/add#option-1-web-editor" target="_blank">tuto</a>]</li>
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
    <li><b><a href="https://github.com/linuxserver/docker-qbittorrent" target="_blank">qBittorrent</a></b> <i style="font-size: 12px;">(passe par gluetun)</i><br>
    Client torrent de qualité</li>
    <li><b><a href="https://github.com/containrrr/watchtower/" target="_blank">Watchtower</a></b><br>
    MAJ automatique de toutes les images de vos containers ne contenant pas le label 'com.centurylinklabs.watchtower.enable: "false"'</li>
</ul>
