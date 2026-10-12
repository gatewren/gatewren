<p align="center"><img src="docs/logo.svg" width="72" alt=""></p>

# Keystile

**Votre accès au réseau, en toute sécurité.** Keystile est une passerelle VPN WireGuard auto-hébergée :
chaque personne n'atteint que les réseaux, serveurs et ports qui lui sont attribués, et seulement après
s'être authentifiée.

- **Refus par défaut** : filtrage par destination (réseau, protocole, ports), appliqué côté serveur.
- **Accès conditionné à l'identité** : passkeys, SSO (Entra ID, Google Workspace, Keycloak…), LDAP / Active
  Directory, double authentification, sessions à durée limitée et révocables instantanément.
- **Administration claire** : utilisateurs, groupes, appareils (configuration et QR code), journal d'audit,
  état des connexions en temps réel.
- **Exploitation simple** : une image Docker, HTTPS automatique, sauvegarde en une commande.

Ce dépôt contient la documentation et les fichiers de déploiement. Keystile est un logiciel propriétaire
distribué sous forme d'image Docker (`ghcr.io/keratos/keystile`), gratuite jusqu'à 5 utilisateurs.

## Installation

Prérequis : un serveur Linux avec Docker, un nom de domaine qui pointe vers lui, les ports **80/tcp**,
**443/tcp+udp** et **51820/udp** ouverts.

```sh
mkdir keystile && cd keystile
docker run --rm -v "$PWD:/out" ghcr.io/keratos/keystile:0.1.0 keystile init
cp .env.example .env
nano .env                 # DOMAIN, WG_ENDPOINT, ADMIN_PASSWORD
docker compose up -d
```

Ouvrez ensuite `https://<votre domaine>` et connectez-vous avec le compte `admin`. Le certificat HTTPS est
obtenu automatiquement. Activez sans attendre un second facteur dans **Sécurité du compte**.

Guide complet : [docs/installation.md](docs/installation.md).

## Éditions

| | Gratuite | Commerciale |
| --- | --- | --- |
| Utilisateurs actifs | 5 | selon la licence |
| Appareils | 10 | selon la licence |
| Filtrage, portail, passkeys, MFA, e-mails | oui | oui |
| SSO OpenID Connect, LDAP / Active Directory | — | oui |
| Support | communautaire (issues) | prioritaire |

Pour une licence commerciale : dans Keystile, page **Licence**, cliquez sur « Générer l'identifiant » et
envoyez-le avec votre nom de domaine à licences@keystile.example. La licence reçue se colle sur la même page.

## Mettre à jour

Changez `KEYSTILE_VERSION` dans `.env`, puis :

```sh
docker compose pull && docker compose up -d
```

La base est migrée automatiquement. Les changements de chaque version sont décrits dans le
[journal des versions](CHANGELOG.md).

## Support et sécurité

- Questions et anomalies : [issues](../../issues) (sans donnée sensible : ni clé, ni licence, ni journal complet).
- Faille de sécurité : ne publiez rien, suivez la [politique de sécurité](SECURITY.md).

## Licence

La documentation et les fichiers de déploiement de ce dépôt sont sous licence MIT : adaptez-les librement.
Le logiciel Keystile (image Docker) est propriétaire et régi par son contrat de licence.
