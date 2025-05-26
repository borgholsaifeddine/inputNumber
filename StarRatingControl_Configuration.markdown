# Configuration de l'Input `StarRatingControlRenderer`

Cette section décrit les options de configuration pour le composant `StarRatingControlRenderer` utilisé dans JSON Forms avec Material-UI. Les options sont définies dans l'objet `uischema.options`.

## Options de Configuration

| Option       | Type     | Valeur par défaut | Description                                                                 |
|--------------|----------|-------------------|-----------------------------------------------------------------------------|
| `maxStars`   | `number` | `5`               | Nombre maximum d'étoiles affichées dans le contrôle.                         |
| `precision`  | `number` | `1`               | Précision des notes (ex. `0.5` pour demi-étoiles).                          |
| `labels`     | `object` | `{}`              | Objet associant des valeurs de note à des libellés personnalisés.           |
| `component`  | `string` | `"StarRating"`    | Identifiant requis pour associer le contrôle au testeur.                     |

### Détails des Options

- **`maxStars`** : Nombre total d'étoiles (entier positif, ex. `3`, `5`, `10`).
- **`precision`** : Granularité des notes (ex. `1` pour étoiles entières, `0.5` pour demi-étoiles).
- **`labels`** : Objet `{ [key: number]: string }` pour libellés personnalisés (ex. `{ 1: "Mauvais", 5: "Excellent" }`). Par défaut : `X étoile(s)`.
- **`component`** : Doit être `"StarRating"` pour l'intégration avec JSON Forms.

## Exemple

```json
{
  "uischema": {
    "type": "Control",
    "scope": "#/properties/rating",
    "options": {
      "component": "StarRating",
      "maxStars": 5,
      "precision": 0.5,
      "labels": {
        "1": "Très insatisfait",
        "3": "Neutre",
        "5": "Très satisfait"
      }
    }
  }
}
```