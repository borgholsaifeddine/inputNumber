# Documentation des options de configuration pour `NumberInput`

Le composant `NumberInput` est un contrôle personnalisé pour JSON Forms permettant de gérer des champs numériques avec une personnalisation avancée. Il prend en charge trois formats : `"currency"`, `"percentage"`, et `"default"`. Les options sont définies dans `uischema.options`. Cette documentation explique chaque option, son effet selon le format, et la gestion des grands nombres.

## Exemple de configuration dans le `uischema`

```json
{
  "type": "Control",
  "scope": "#/properties/amount",
  "label": "Montant",
  "options": {
    "format": "currency",
    "prefix": "€",
    "suffix": "",
    "min": -1000000,
    "max": 1000000000000,
    "step": 1,
    "thousandSeparator": " ",
    "decimalSeparator": ",",
    "decimalPlaces": 2,
    "local": "fr-FR",
    "component": "NumberInput"
  }
}
```

## Options communes à tous les formats

Ces options s’appliquent à tous les formats (`"currency"`, `"percentage"`, `"default"`).

| Option            | Description                                                  | Valeur par défaut         | Effet général                                                                 |
|-------------------|--------------------------------------------------------------|---------------------------|------------------------------------------------------------------------------|
| `prefix`          | Chaîne affichée avant la valeur dans l’input.                 | `""` (vide)               | Ajoute un symbole ou texte avant le nombre (ex. `€123`).                     |
| `suffix`          | Chaîne affichée après la valeur dans l’input.                 | `""` (vide)               | Ajoute un symbole ou texte après le nombre (ex. `123%`).                     |
| `min`             | Valeur minimale autorisée.                                   | Non défini (aucune limite) | Limite inférieure pour la valeur, appliquée si `component` n’est pas défini. |
| `max`             | Valeur maximale autorisée.                                   | Non défini (aucune limite) | Limite supérieure pour la valeur, appliquée si `component` n’est pas défini. |
| `thousandSeparator` | Séparateur des milliers (ex. `,` ou `" "`).                 | Déduit de `local`, sinon `,` | Sépare les milliers dans l’affichage (ex. `1 234 567`).                     |
| `decimalSeparator` | Séparateur des décimales (ex. `.` ou `,`).                  | Déduit de `local`, sinon `.` | Sépare la partie entière et décimale (ex. `123,456`).                       |
| `local`           | Locale pour déterminer les séparateurs (ex. `"fr-FR"`).      | `"en-US"`                 | Définit les séparateurs par défaut si non spécifiés (ex. `"fr-FR"`: `" "` et `","`). |
| `grid`            | Configuration de mise en page pour la grille (Material-UI).   | Non défini                | Contrôle la largeur dans une grille (ex. `xs: 12`, `sm: 6`).                 |

## Options spécifiques par format

### Format : `"currency"`

Conçu pour les valeurs monétaires avec séparateurs et décimales.

| Option            | Description                                   | Valeur par défaut | Effet pour `"currency"`                                   | Exemple           |
|-------------------|-----------------------------------------------|-------------------|----------------------------------------------------------|-------------------|
| `format`          | Définit le style comme `"currency"`.          | `"default"`       | Active les séparateurs et les décimales selon `decimalPlaces`. | `€1 234,56`       |
| `decimalPlaces`   | Nombre de décimales affichées.                | `1`               | Limite les décimales dans l’affichage (ex. `2` → `1 234,56`). | `€1 234,56`       |

**Comportement spécifique** :
- Accepte les nombres négatifs (`allowNegative: true`).
- Si `component: "NumberInput"` est défini, les valeurs sont enregistrées comme chaînes (utile pour les grands nombres).

**Exemple** : `€-1 234,56` (2 décimales, ` ` pour milliers, `,` pour décimales).

### Format : `"percentage"`

Conçu pour les pourcentages, limité aux entiers.

| Option            | Description                                   | Valeur par défaut | Effet pour `"percentage"`                                | Exemple           |
|-------------------|-----------------------------------------------|-------------------|--------------------------------------------------------|-------------------|
| `format`          | Définit le style comme `"percentage"`.        | `"default"`       | Force `decimalScale: 0` (pas de décimales), bloque `-, +, ., ^, ,`. | `123%`            |
| `step`            | Incrément pour les flèches de l’input.        | `1`               | Ajuste la valeur par incréments (ex. `5` → `120` à `125`). | `125%` (après flèche) |
| `decimalPlaces`   | Nombre de décimales affichées.                | `1`               | Ignoré, forcé à `0` pour interdire les décimales.       | `123%` (pas de décimales) |

**Comportement spécifique** :
- Bloque les caractères non numériques via `handleKeyDown`.
- Si `component: "NumberInput"` est défini, les valeurs sont enregistrées comme chaînes.

**Exemple** : `-5000000$` (suffixe `$`, pas de décimales, nombres négatifs acceptés).

### Format : `"default"`

Style numérique simple sans formatage avancé.

| Option            | Description                                   | Valeur par défaut | Effet pour `"default"`                                   | Exemple           |
|-------------------|-----------------------------------------------|-------------------|---------------------------------------------------------|-------------------|
| `format`          | Définit le style comme `"default"`.           | `"default"`       | Désactive les séparateurs et force `decimalScale: 0`.    | `-1234`           |

**Comportement spécifique** :
- Accepte les nombres négatifs.
- Si `component: "NumberInput"` est défini, les valeurs sont enregistrées comme chaînes.

**Exemple** : `5000000` (pas de séparateurs, pas de décimales).

## Gestion des grands nombres

Pour gérer des nombres dépassant `Number.MAX_SAFE_INTEGER` (9007199254740991), suivez ces instructions :

- **Pour utiliser des grands nombres (supérieurs à 9007199254740991)** :
  - Ajoutez `"component": "NumberInput"` dans les `options` du `uischema`.
  - Définissez le type de l’input dans le schéma JSON comme `"type": "string"`.
  - **Effet** : Les valeurs sont enregistrées sous forme de chaînes pour préserver la précision.

  **Exemple** :
  ```json
  // uischema
  {
    "type": "Control",
    "scope": "#/properties/largeAmount",
    "label": "Grand Montant",
    "options": {
      "format": "currency",
      "prefix": "€",
      "component": "NumberInput"
    }
  }
  // schema
  {
    "type": "object",
    "properties": {
      "largeAmount": {
        "type": "string"
      }
    }
  }
  ```

- **Pour utiliser des nombres inférieurs à 9007199254740991** :
  - Ne définissez pas `"component": "NumberInput"` dans les `options`.
  - Définissez le type de l’input dans le schéma JSON comme `"type": "number"`.
  - **Effet** : Les valeurs sont enregistrées sous forme de nombres et limitées par `min` et `max` si spécifiés.

  **Exemple** :
  ```json
  // uischema
  {
    "type": "Control",
    "scope": "#/properties/smallAmount",
    "label": "Petit Montant",
    "options": {
      "format": "currency",
      "prefix": "€",
      "min": 0,
      "max": 1000000
    }
  }
  // schema
  {
    "type": "object",
    "properties": {
      "smallAmount": {
        "type": "number"
      }
    }
  }
  ```

**Note** : Les grands nombres dépassant `Number.MAX_SAFE_INTEGER` seront toujours enregistrés sous forme de chaînes si `component: "NumberInput"` est défini, même si le schéma spécifie `"type": "number"`.

## Notes importantes

- **Séparateurs dynamiques** :
  - Si `thousandSeparator` ou `decimalSeparator` ne sont pas définis, ils sont déduits de `local` (ex. `"fr-FR"` → `" "` et `","`).
  - Les valeurs explicites dans la configuration ont la priorité.
- **Personnalisation de `step`** :
  - Appliqué uniquement pour `"percentage"` via `type: "number"`. Pour d’autres formats, modifiez `inputProps` si nécessaire.
- **Compatibilité** :
  - Compatible avec le schéma `"type": ["number", "string"]` pour gérer à la fois les nombres et les chaînes.
- **Dépendance** :
  - Utilise `react-number-format` pour le formatage. Consultez la [documentation de react-number-format](https://s-yadav.github.io/react-number-format/docs/numeric_format) pour plus de détails.
