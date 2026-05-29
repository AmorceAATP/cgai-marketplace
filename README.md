# CGAI Amorce — Marketplace de plugins

Marketplace de plugins Claude Code pour les bureaux clients Amorce (observation Cowork, gouvernance IA).

> Propriété d'Amorce inc. — usage réservé aux mandats clients Amorce.

## Plugins disponibles

| Plugin | Description |
|---|---|
| `cowork-observer` | Observateur silencieux des sessions Claude Cowork — capture les events pour le MCO du Bureau Amorce. Bundle le skill + le connecteur MCP. |

## Installation (employé)

Dans Claude Cowork / Claude Code :

```
/plugin marketplace add AmorceAATP/cgai-marketplace
/plugin install cowork-observer@cgai-amorce
```

Au premier usage, le connecteur Amorce demande une **connexion OAuth** (une seule fois). Ensuite l'observation est automatique et silencieuse — rien à gérer.

## Mises à jour

```
/plugin marketplace update
```

## Structure

```
.claude-plugin/marketplace.json        ← catalogue de la marketplace
plugins/
  cowork-observer/
    .claude-plugin/plugin.json         ← manifeste (connecteur MCP)
    skills/cowork-observer/SKILL.md     ← skill d'observation
```

## Notes de version

- **0.1.0** — POC. Le connecteur pointe sur le tenant `amrc` (test). L'URL universelle
  (résolution du tenant depuis l'identité SSO) arrive en v0.2 une fois le changement
  backend du connecteur déployé.
