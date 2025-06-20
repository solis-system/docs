# Format des Dates - Standard ISO 8601

## Vue d'ensemble
Le standard ISO 8601 définit le format international pour représenter les dates et heures de manière non ambiguë.

## Format de base
- **Format recommandé** : `YYYY-MM-DDTHH:mm:ss`
- **Avec fuseau horaire** : `YYYY-MM-DDTHH:mm:ss±HH:mm` ou `YYYY-MM-DDTHH:mm:ssZ`

## Exemples

### ✅ Formats corrects (ISO 8601)
- `2025-06-20T08:05:05`
- `2025-06-20T08:05:05Z` (UTC)
- `2025-06-20T08:05:05+02:00` (avec fuseau horaire)
- `2025-06-20T08:05:05.123Z` (avec millisecondes)
- `2025-06-20` (date seule)

### ❌ Formats incorrects
- `20250620080505` (pas de séparateurs)
- `20/06/2025 08:05:05` (format local)
- `06-20-2025T08:05:05` (ordre américain)
- `2025/06/20 08:05:05` (mauvais séparateurs)

## Remarques importantes
- Le `T` sépare la date de l'heure
- Le `Z` indique UTC (Zulu time)
