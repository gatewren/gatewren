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
docker run --rm -v "$PWD:/out" ghcr.io/keystile/keystile:0.2.0 keystile init
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

## Derrière un proxy existant

Si un serveur web occupe déjà les ports 80 et 443 de la machine (nginx, Caddy, Traefik…), désactivez le
Caddy de Keystile et faites relayer le portail par ce serveur :

```sh
cp compose.proxy.yaml compose.override.yaml   # fusionné automatiquement par docker compose
docker compose up -d
```

Le portail écoute alors sur `127.0.0.1:8080`. Ajoutez `DOMAIN` à votre proxy, avec deux exigences :
transmettre les en-têtes `X-Forwarded-*` et ne pas mettre en tampon le flux temps réel.

Caddy :

```caddyfile
vpn.exemple.fr {
	reverse_proxy 127.0.0.1:8080 {
		flush_interval -1
	}
	header Strict-Transport-Security "max-age=31536000; includeSubDomains"
}
```

nginx (certificat géré par votre outil habituel, Certbot par exemple) :

```nginx
server {
    listen 443 ssl;
    server_name vpn.exemple.fr;
    # ssl_certificate … ; ssl_certificate_key … ;
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_buffering off;   # flux temps réel
        proxy_read_timeout 1h;
    }
}
```

Pour joindre par le VPN les services de cette machine elle-même (SSH, outils d'administration),
déclarez sur la carte un service avec l'adresse `172.31.250.1` (la machine vue depuis Keystile), et non
son adresse publique : celle-ci est refusée, car elle couperait l'accès au portail, voire le tunnel.
Le pare-feu de la machine doit accepter ce trafic venant du réseau Docker de Keystile.

`PUBLIC_URL` (déduite de `DOMAIN`) et `WG_ENDPOINT` priment sur les réglages enregistrés : les modifier
dans `.env` puis redémarrer suffit, y compris sur une installation existante.

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
