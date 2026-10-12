# Politique de sécurité

## Signaler une vulnérabilité

Ne la décrivez pas dans une issue publique. Utilisez le
[signalement privé de GitHub](../../security/advisories/new) ou écrivez à securite@keystile.example.

Indiquez la version concernée (`keystile version`), les étapes pour reproduire et l'impact constaté.
Nous accusons réception sous 3 jours ouvrés et vous tenons informé jusqu'à la correction.

## Versions maintenues

Seule la dernière version mineure reçoit les correctifs de sécurité. Les images publiées sont signées
(Cosign) et accompagnées d'un inventaire des composants (SBOM) :

```sh
cosign verify ghcr.io/keystile/keystile:0.1.0 \
  --certificate-identity-regexp '^https://github.com/keystile/keystile-app/\.github/workflows/release\.yml@refs/tags/v' \
  --certificate-oidc-issuer https://token.actions.githubusercontent.com
```
