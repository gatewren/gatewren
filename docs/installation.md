# Installer Keystile

## 1. Préparer le serveur

- Linux 64 bits (amd64 ou arm64) avec Docker et le plugin Compose.
- Module noyau `wireguard` (présent par défaut depuis Linux 5.6 ; sinon Keystile bascule en espace utilisateur).
- Un nom de domaine, par exemple `vpn.exemple.fr`, avec un enregistrement DNS vers l'adresse publique du serveur.
- Ports ouverts dans le pare-feu et chez l'hébergeur : `80/tcp` et `443/tcp+udp` (portail HTTPS),
  `51820/udp` (WireGuard).

## 2. Récupérer la pile

```sh
mkdir keystile && cd keystile
docker run --rm -v "$PWD:/out" ghcr.io/keystile/keystile:0.1.0 keystile init
cp .env.example .env
```

## 3. Configurer `.env`

| Variable | Rôle |
| --- | --- |
| `DOMAIN` | Domaine du portail (certificat HTTPS automatique). |
| `WG_ENDPOINT` | Adresse et port que les appareils utilisent pour joindre le VPN. |
| `ADMIN_PASSWORD` | Mot de passe du compte `admin` créé au premier démarrage. |
| `WG_SUBNET` | Réseau interne du VPN (défaut `10.8.0.0/24`). |
| `VPN_REQUIRE_MFA` | Exige un second facteur pour ouvrir l'accès VPN (recommandé). |
| `SMTP_*` | Envoi des invitations et codes (modifiable ensuite dans l'interface). |

## 4. Démarrer

```sh
docker compose up -d
docker compose logs -f keystile    # attendre « Interface wg0 prête »
```

## 5. Premiers réglages

1. Connectez-vous à `https://<DOMAIN>` avec `admin`, changez le mot de passe et ajoutez une passkey
   ou une application d'authentification (**Sécurité du compte**). Rangez les codes de secours.
2. **Utilisateurs** : invitez vos collègues par e-mail ou créez leurs comptes.
3. **Accès** : sur la carte, créez des groupes (avec leurs membres) et des services (adresse ou réseau,
   protocole, ports). Rangez au besoin les services dans des zones.
4. Tirez la poignée d'un groupe vers un service ou une zone pour lui en donner l'accès. Une personne
   peut aussi être posée sur la carte pour un accès en propre (prestataire, exception).
5. Chaque utilisateur ajoute ses appareils depuis **Mon accès** et importe la configuration (fichier ou QR code).

## Sauvegarder

```sh
docker compose exec keystile keystile backup
```

Sauvegardez aussi le volume `keystile-data` en entier : il contient les secrets de chiffrement et la clé
WireGuard du serveur, indispensables à une restauration.

## Accès de secours

Si plus aucun administrateur ne peut se connecter :

```sh
docker compose exec keystile keystile reset-admin admin
```

Un mot de passe temporaire est affiché ; les seconds facteurs du compte sont retirés.
