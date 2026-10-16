# Journal des versions

Format : [Keep a Changelog](https://keepachangelog.com/fr/1.1.0/) ; numérotation [SemVer](https://semver.org/lang/fr/).

## [Non publié]

## [0.3.0] - 2026-10-16

### Mise à jour
- Aucune action requise : la nouvelle table des jetons d'API est créée au démarrage, les données existantes
  sont conservées. Mettre à jour l'image (`KEYSTILE_VERSION=0.3.0` ou `latest`) puis `docker compose up -d`.
- Correctif de sécurité (voir « Sécurité ») : mise à jour recommandée pour toutes les installations 0.2.x.
- Nouvelles règles : un identifiant doit contenir au moins une lettre ou un chiffre ; le dernier
  administrateur actif ne peut plus être désactivé ni rétrogradé ; un nom d'appareil ne peut plus contenir
  de caractère de contrôle. Les comptes et appareils existants ne sont pas modifiés.

### Ajouté
- API publique v1 (Pro et Business) : jetons créés dans Réglages > API (lecture ou lecture et écriture,
  expiration, affichés une seule fois), limite de 120 requêtes par minute. Comptes, groupes et membres,
  plages horaires, appareils (configuration comprise), sessions VPN (liste et révocation), état de la
  passerelle, trafic, tentatives bloquées, accès inutilisés et journal d'audit (reprise par `since_id`).
  Erreurs au format commun, schéma OpenAPI sur `/api/v1/openapi.json`, documentation dans le README.
- Plages horaires de connexion (Pro et Business) : par groupe, avec exception par personne ; hors plage,
  appareils coupés et ouverture de l'accès refusée (avec la prochaine ouverture) ; session arrêtée à la fin
  de la plage ; administrateurs jamais bloqués ; fuseau horaire dans les réglages.
- Page Analyse (Pro et Business) : tentatives bloquées par appareil (adresse, protocole, port, essais),
  rapprochées des services avec « Accorder » (personne ou groupe, impact affiché), « Créer un service » et
  « Masquer » ; accès inutilisés depuis 14 à 90 jours, à retirer en un clic. Compteur sur 24 h dans la vue
  d'ensemble, tentatives récentes sur la fiche d'un appareil.
- Historique du trafic par appareil et par service, dans les deux sens : courbes 24 h, 7 jours et 30 jours
  sur les fiches appareil, utilisateur, service et sur le tableau de bord, avec répartitions. Mesure sans
  agent (compteurs du pare-feu et de WireGuard), jamais le contenu ni les sites visités. Conservation :
  7 jours en édition gratuite, jusqu'à 2 ans en Pro et Business ; onglet Réglages « Trafic » (arrêt de la
  mesure, durée, effacement).
  Courbes mises à jour toutes les 10 s avec le débit du moment ; un clic sur un service ou un appareil des
  répartitions affiche sa propre courbe.
- Client Keystile pour Windows : bouton « Associer le client Keystile » et lien de téléchargement dans Mon
  accès, page d'ouverture de l'accès en un clic ; dans l'administration, pastille « Client Keystile » et
  dernière activité de l'appareil.
- API du futur client Keystile pour Windows : association d'un poste validée dans le navigateur (la clé
  privée reste sur le poste), jeton d'appareil, état, configuration et coupure de l'accès ; l'accès ne
  s'ouvre jamais avec le seul jeton.
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

- Licence gratuite enregistrée : une installation fonctionne 30 jours sans licence, puis demande une
  licence gratuite (12 mois renouvelables) obtenue sur le portail client. Elle inclut 30 jours d'essai des
  fonctions commerciales (SSO, LDAP ; 25 utilisateurs, 75 appareils). Alertes d'échéance à 30, 14 et 7 jours
  (bandeau et e-mail aux administrateurs). Outil éditeur : `issue --edition free --trial-days 30`, et
  `inspect` vérifie aussi les licences émises par le portail.

### Modifié
- Interface reprise de l'IPBX : formulaires en sections (réglages, fiche utilisateur, sécurité du compte,
  licence), interrupteurs, listes à chevron, nombres à flèches ; sur téléphone, barre compacte et menu
  dépliable. Carte des accès : chaque élément s'ouvre dans une fenêtre (plein écran sur téléphone) au lieu
  du panneau de droite ; les règles d'un service se modifient dans une sous-fenêtre ; fermer avec une
  saisie en cours demande confirmation.
- Listes (utilisateurs, appareils, sessions VPN, journal, analyse) en tableaux paginés repris de l'IPBX :
  recherche, puces de filtre avec leur nombre (administrateurs, sans MFA, désactivés…), tri par colonne,
  10 à 100 lignes par page. Nouvel utilisateur et modification du profil dans une fenêtre (erreurs affichées
  sans perdre la saisie) ; la fiche d'un utilisateur montre un résumé du profil.
- Édition gratuite : 3 utilisateurs, 6 appareils, sessions VPN de 12 h au plus. Sans licence après le délai,
  ou licence expirée après sa grâce, les ajouts (comptes, appareils, services, accès) sont bloqués ; rien
  n'est coupé (VPN, connexions, retraits et modifications fonctionnent).
- Image plus légère : 45 Mo compressée au lieu de 53 Mo.
- `PUBLIC_URL` (ou `DOMAIN`) et `WG_ENDPOINT` priment sur les réglages enregistrés : une installation
  existante les prend en compte au redémarrage ; les champs correspondants passent en lecture seule.

### Corrigé
- Noms d'hôte : une réponse DNS partielle (IPv4 trouvée, IPv6 en échec, ou l'inverse) ne vide plus la
  famille en échec. Les adresses ne battent plus d'une résolution à l'autre, et les appareils ne rechargent
  plus leur configuration (avec la notification « Accès mis à jour » du client) sans changement réel.
- Un recul de l'horloge du serveur (recalage NTP, machine virtuelle) ne déconnecte plus les utilisateurs :
  la session tolère 30 s d'écart vers le futur, sa durée maximale reste vérifiée.

### Sécurité
- Un retour à la ligne dans le nom d'un appareil permettait d'injecter des lignes dans la configuration
  WireGuard du serveur : les noms contenant un caractère de contrôle sont refusés et le commentaire de la
  configuration est neutralisé.
- Plus de fuite IPv6 : les adresses IPv6 d'un nom accordé sont envoyées dans le tunnel (et refusées), au
  lieu d'être jointes en direct hors VPN ; le navigateur se replie sur l'IPv4, qui passe par le tunnel. En
  tunnel complet, tout l'IPv6 entre aussi dans le tunnel. Les appareils concernés passent « À mettre à jour ».
- Retirer un accès coupe aussitôt les connexions déjà ouvertes vers ce service (y compris un flux poussé
  par le service), et plus seulement les nouvelles.
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
