# Journal des versions

Format : [Keep a Changelog](https://keepachangelog.com/fr/1.1.0/) ; numérotation [SemVer](https://semver.org/lang/fr/).

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
