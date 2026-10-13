# Journal des versions

Format : [Keep a Changelog](https://keepachangelog.com/fr/1.1.0/) ; numérotation [SemVer](https://semver.org/lang/fr/).

## [Non publié]

### Ajouté
- Interface et e-mails en français et en anglais. Langue choisie par chaque utilisateur (Sécurité du
  compte), sinon celle du navigateur, sinon la langue par défaut de l'instance (Réglages, ou
  `DEFAULT_LOCALE`). Sélecteur de langue sur les pages de connexion ; langue de chaque compte
  modifiable par un administrateur. La ligne de commande `keystile` suit la langue du terminal.
- Carte des accès (page Accès) : groupes, personnes, services et zones sur une même carte ; on accorde
  un accès en tirant un lien. Un service regroupe une ou plusieurs règles (réseau, protocole, ports) ;
  une zone rassemble des services, et un accès à la zone couvre aussi ceux ajoutés plus tard.
  Suppressions et sorties de zone annoncées avec leur impact (qui perd quel accès), modifications
  simultanées visibles en direct, conflits signalés sans rien écraser.

### Modifié
- Les pages Groupes et Destinations sont remplacées par la carte des accès ; les destinations
  existantes sont reprises automatiquement en services, avec les mêmes accès.

### Sécurité
- Le trafic d'un appareil VPN vers un autre est toujours bloqué, même si une règle large
  (`10.0.0.0/8`, Internet) l'inclut.

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
