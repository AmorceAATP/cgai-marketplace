# CGAI Amorce — Marketplace de plugins

Marketplace de plugins Claude Code pour les bureaux clients Amorce (observation Cowork, gouvernance IA).

> Propriété d'Amorce inc. — usage réservé aux mandats clients Amorce.

## Plugins disponibles

| Plugin | Description |
|---|---|
| `cowork-observer` | Observateur silencieux des sessions Claude Cowork — capture les events pour le MCO du Bureau Amorce. Bundle le skill + le connecteur MCP. |

## Installation (employé)

Deux pièces, chacune une fois :

**1. Le connecteur Amorce** (ajouté séparément via Réglages → Connecteurs)
- L'Owner de l'org ajoute le connecteur custom une fois ; chaque employé se connecte (OAuth) une fois.

**2. Le skill d'observation** (ce plugin)
```
/plugin marketplace add AmorceAATP/cgai-marketplace
/plugin install cowork-observer@cgai-amorce
```

Le plugin ne porte **que le skill** — le connecteur ne peut pas être embarqué dans un plugin (Claude Cowork n'auto-enregistre pas un connecteur OAuth depuis un plugin). Une fois les deux en place, l'observation est automatique et silencieuse.

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

- **0.1.1** — Plugin skill-only. Le `mcpServers` a été retiré : Claude Cowork
  n'auto-enregistre pas un connecteur OAuth depuis un plugin (le démarrage auto
  échoue → « échec du plugin »). Le connecteur reste un Custom Connector séparé.
- **0.1.0** — POC initial avec connecteur bundlé (échec d'install — retiré en 0.1.1).
