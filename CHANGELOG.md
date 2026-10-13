# Journal des versions

Format : [Keep a Changelog](https://keepachangelog.com/fr/1.1.0/) ; numérotation [SemVer](https://semver.org/lang/fr/).

## [Non publié]

### Ajouté
- Page de connexion : illustration animée (appareils, passerelle, services ; un accès non accordé s'arrête
  à la passerelle), fixe si le système demande de réduire les animations.
- Une action sensible interrompue par la confirmation d'identité est relancée automatiquement une fois
  le mot de passe (ou la passkey) confirmé, au lieu d'être à refaire ; jamais avec un champ secret.
- Recherche instantanée dans les listes (utilisateurs, appareils, sessions VPN) et dans tout le journal
  (y compris par libellé d'action) ; recherche dans les choix de compte et de service de la carte.
- Suppression depuis les listes : utilisateurs (un par un ou par sélection), appareils ; annulation d'une
  invitation en attente ; purge du journal au-delà d'une durée et des sessions VPN terminées.
- Déploiement derrière un proxy HTTPS existant (nginx, Caddy, Traefik…) : `compose.proxy.yaml`, livré par
  `keystile init`, désactive le Caddy de Keystile ; exemples Caddy et nginx dans la documentation.

### Modifié
- `PUBLIC_URL` (ou `DOMAIN`) et `WG_ENDPOINT` priment sur les réglages enregistrés : une installation
  existante les prend en compte au redémarrage ; les champs correspondants passent en lecture seule.

### Sécurité
- Après la suppression d'un appareil, le retour se fait toujours vers une page de ce site, jamais vers
  l'adresse d'un en-tête Referer extérieur.
- Une règle ne peut plus couvrir l'adresse du serveur VPN lui-même : elle coupait l'accès au portail, voire
  le tunnel sur certains systèmes. Le message propose l'adresse interne à utiliser ; un nom d'hôte qui
  s'y résout ne la retient pas.

## [0.2.0] - 2026-10-13

### Mise à jour
- Sauvegardez avant de mettre à jour (`keystile backup`). Au premier démarrage, la base est migrée
  automatiquement : les destinations et les groupes deviennent des services et des liens sur la
  carte des accès, avec exactement les mêmes droits.
- Les appareils dont la configuration change (serveurs DNS désormais inclus dans le tunnel partiel)
  sont signalés « À mettre à jour » : téléchargez ou scannez de nouveau leur configuration.

### Ajouté
- Carte des accès (page Accès) : groupes, personnes, services et zones sur une même carte ; on accorde
  un accès en tirant un lien. Un service regroupe une ou plusieurs règles (réseau, protocole, ports) ;
  une zone rassemble des services, et un accès à la zone couvre aussi ceux ajoutés plus tard.
  Suppressions et sorties de zone annoncées avec leur impact (qui perd quel accès), modifications
  simultanées visibles en direct, conflits signalés sans rien écraser. Clavier et écrans tactiles
  (pincer pour zoomer).
- Règles par nom d'hôte (`erp.entreprise.local`) : le serveur le résout avec les serveurs DNS des
  réglages, à l'enregistrement puis toutes les 5 minutes (`RESOLVE_INTERVAL`), et ouvre les adresses
  obtenues. DNS injoignable : dernières adresses connues conservées ; nom jamais résolu : rien n'est
  ouvert. Pas de joker.
- Appareils : badge « À mettre à jour » quand leur configuration a changé (accès, DNS, point d'accès…)
  depuis le dernier import ; la page de l'appareil permet de confirmer un import par QR code.
- Interface et e-mails en français et en anglais. Langue choisie par chaque utilisateur (Sécurité du
  compte), sinon celle du navigateur, sinon la langue par défaut de l'instance (Réglages, ou
  `DEFAULT_LOCALE`). Sélecteur de langue sur les pages de connexion ; langue de chaque compte
  modifiable par un administrateur. La ligne de commande `keystile` suit la langue du terminal.
- Journal d'audit : membres ajoutés et retirés lors d'une modification de groupe.

### Modifié
- Les pages Groupes et Destinations sont remplacées par la carte des accès.
- Les serveurs DNS des réglages sont joignables par tous les appareils (port 53 uniquement) et ajoutés
  à leur tunnel partiel : plus besoin de les déclarer comme service.

### Sécurité
- Le trafic d'un appareil VPN vers un autre est toujours bloqué, même si une règle large
  (`10.0.0.0/8`, Internet) l'inclut.
- Une session expirée n'est plus jamais prise pour un succès par l'interface (réponse 401 explicite).

## [0.1.0] - 2026-10-12

Première version.

### Accès réseau
- Passerelle WireGuard (noyau, repli wireguard-go) en refus par défaut, filtrage nftables par destination
  (réseau, protocole, ports), groupes d'accès, tunnel partiel ou complet.
- Connexion VPN conditionnée à une session sur le portail, durée globale ou par utilisateur, révocation immédiate.

### Authentification
- Mot de passe, passkeys (WebAuthn), SSO OpenID Connect, LDAP / Active Directory, code par e-mail.
- Second facteur : application TOTP, passkey ou codes de secours ; revérification avant les actions sensibles.
- Invitations et mot de passe oublié par e-mail.

### Administration
- Utilisateurs, groupes, destinations, appareils (configuration et QR code), sessions, journal d'audit,
  aperçu du pare-feu, état en temps réel. Thèmes clair et sombre.

### Exploitation
- Image durcie, déploiement avec Caddy (HTTPS automatique), `keystile init`, `backup`, `reset-admin`.
- Édition gratuite (5 utilisateurs, 10 appareils) et licences commerciales liées à l'installation.
