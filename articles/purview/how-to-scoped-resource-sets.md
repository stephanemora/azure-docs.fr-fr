---
title: Comment créer une configuration de jeu de ressources déterminé
description: Découvrez comment créer une règle de configuration de jeu de ressources déterminé pour changer la manière dont les ressources sont regroupées dans les jeux de ressources
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/17/2021
ms.openlocfilehash: 10e925a84dbe187ccdf5e444cb8b3dd4b7bb4676
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608000"
---
# <a name="create-scoped-resource-set-configuration-rules"></a>Créer des règles de configuration de jeu de ressources déterminé

Les systèmes de traitement de données à l’échelle stockent généralement une table unique sur un disque en tant que fichiers multiples. Ce concept est représenté dans Azure Purview à l’aide de jeux de ressources. Dans le catalogue de données, un jeu de ressources est un objet unique qui représente un grand nombre de ressources dans le stockage. Pour plus d’informations, consultez [Présentation des jeux de ressources](concept-resource-sets.md).

Lors de l’analyse d’un compte de stockage, Azure Purview se sert d’un ensemble de modèles définis pour déterminer si un groupe d’éléments constitue un jeu de ressources. Dans certains cas, le regroupement en jeu de ressources d’Azure Purview peut ne pas refléter précisément votre patrimoine de données. Les règles de jeu de ressources déterminé vous permettent de personnaliser ou de remplacer la manière dont Azure Purview détecte les ressources qui sont regroupées en jeux de ressources, et la façon dont elles sont affichées dans le catalogue.

## <a name="how-to-create-a-scoped-resource-set-configuration"></a>Comment créer une configuration de jeu de ressources déterminé

Suivez les étapes ci-dessous pour créer une configuration de jeu de ressources déterminé :

1. Accédez au centre de gestion. Sélectionnez **Jeux de ressources déterminés** dans le menu. Cliquez sur **+ Nouveau** pour créer un nouvel ensemble de règles de configuration.

   :::image type="content" source="media/how-to-scoped-resource-sets/create-new-scoped-resource-set-rule.png" alt-text="Créer une règle de jeu de ressources déterminé" border="true":::

1. Entrez l’étendue de votre configuration de jeu de ressources déterminé. Sélectionnez le type de compte de stockage et le nom du compte de stockage sur lequel vous souhaitez créer un ensemble de règles. Chaque ensemble de règles est appliqué par rapport à une étendue de chemin de dossier spécifiée dans le champ **Chemin du dossier**.

   :::image type="content" source="media/how-to-scoped-resource-sets/create-new-scoped-resource-set-scope.png" alt-text="Créer des configurations de jeu de ressources déterminé" border="true":::

1. Pour entrer une règle destinée à une étendue de configuration, sélectionnez **+ Nouvelle règle**.

1. Renseignez les champs suivants pour créer une règle :

   1. **Nom de la règle.** Nom de la règle de configuration. Ce champ n’a aucun effet sur les ressources auxquelles la règle s’applique.

   1. **Nom qualifié.** Chemin qualifié qui utilise une combinaison de texte, de remplacements dynamiques et de remplaçants statiques pour faire correspondre les ressources à la règle de configuration. Ce chemin dépend de l’étendue de la règle de configuration. Consultez la section sur la [syntaxe](#syntax) ci-dessous pour obtenir des instructions détaillées sur la façon de spécifier des noms qualifiés.

   1. **Nom d’affichage.** Nom d’affichage de la ressource. Ce champ est facultatif. Utilisez du texte brut et des remplaçants statiques pour personnaliser l’affichage d’une ressource dans le catalogue. Pour obtenir des instructions plus détaillées, consultez la section sur la [syntaxe](#syntax) ci-dessous.

   1. **Ne pas regrouper en jeu de ressources.** Si cette option est activée, la ressource correspondante n’est pas regroupée dans un jeu de ressources.

      :::image type="content" source="media/how-to-scoped-resource-sets/scoped-resource-set-rule-example.png" alt-text="Créer une nouvelle règle de configuration." border="true":::

1. Enregistrez la règle en cliquant sur **Ajouter**.

## <a name="scoped-resource-set-syntax"></a><a name="syntax"></a> Syntaxe du jeu de ressources déterminé

Lorsque vous créez des règles de jeu de ressources déterminé, utilisez la syntaxe suivante pour spécifier à quelles ressources les règles s’appliquent.

### <a name="dynamic-replacers-single-brackets"></a>Remplacements dynamiques (crochets simples)

Les crochets simples sont utilisés comme **remplacements dynamiques** dans une règle de jeu de ressources déterminé. Spécifiez un remplacement dynamique dans le nom qualifié à l’aide du format `{<replacerName:<replacerType>}`. En cas de correspondance, les remplacements dynamiques sont utilisés sous forme de condition de regroupement indiquant les ressources qui doivent être représentées en tant que jeu de ressources. Si les ressources sont regroupées dans un jeu de ressources, le chemin qualifié du jeu de ressources doit contenir `{replacerName}` à l’endroit où le remplacement a été précisé.

Par exemple, si deux ressources `folder1/file-1.csv` et `folder2/file-2.csv` correspondaient à la règle `{folder:string}/file-{NUM:int}.csv`, le jeu de ressources constituerait une entité unique `{folder}/file-{NUM}.csv`.

#### <a name="special-case-dynamic-replacers-when-not-grouping-into-resource-set"></a>Cas particulier : Remplacements dynamiques en l’absence de regroupement dans un jeu de ressources

Si l’option *Ne pas regrouper en jeu de ressources* est activée pour une règle de jeu de ressources déterminé, le nom du remplaçant est un champ facultatif. `{:<replacerType>}` est une syntaxe valide. Par exemple, `file-{:int}.csv` correspondrait pour `file-1.csv` et `file-2.csv`, et créerait deux ressources différentes au lieu d’un jeu de ressources.

### <a name="static-replacers-double-brackets"></a>Remplaçants statiques (doubles crochets)

Les doubles crochets sont utilisés comme **remplaçants statiques** dans le nom qualifié d’une règle de jeu de ressources déterminé. Spécifiez un remplaçant statique dans le nom qualifié à l’aide du format `{{<replacerName>:<replacerType>}}`. S’il est mis en correspondance, chaque ensemble de valeurs uniques de remplaçant statique crée des regroupements de jeu de ressources différents.

Par exemple, si deux ressources `folder1/file-1.csv` et `folder2/file-2.csv` correspondaient à la règle `{{folder:string}}/file-{NUM:int}.csv`, deux jeux de ressources seraient créés, `folder1/file-{NUM}.csv` et `folder2/file-{NUM}.csv`.

Les remplaçants statiques peuvent servir à spécifier le nom d’affichage d’une ressource correspondant à une règle de jeu de ressources déterminé. L’utilisation de `{{<replacerName>}}` dans le nom d’affichage d’une règle utilise la valeur correspondante dans le nom de la ressource.

### <a name="available-replacement-types"></a>Types de remplacement disponibles

Voici les types disponibles qui peuvent être utilisés dans les remplacements statiques et dynamiques :

| Type | Structure |
| ---- | --------- |
| string | Série de 1 ou de plusieurs caractères Unicode, y compris des délimiteurs comme les espaces. |
| int | Série de 1 ou de plusieurs caractères ASCII allant de 0 à 9, et pouvant commencer par 0 (par exemple, 0001). |
| guid | Série d’une représentation de chaîne 32 ou 8-4-4-4-12 d’un UUID tel que défini dans [RFC 4122](https://tools.ietf.org/html/rfc4122). |
| Date | Série de 6 ou 8 caractères ASCII allant de 0 à 9, avec des séparateurs facultatifs : aaaammjj, aaaa-mm-jj, aammjj, aa-mm-jj, comme spécifié dans [RFC 3339](https://tools.ietf.org/html/rfc3339). |
| time | Série de 4 ou 6 caractères ASCII allant de 0 à 9, avec des séparateurs facultatifs : HHmm, HH:mm, HHmmss, HH:mm:ss, tel que précisé dans [RFC 3339](https://tools.ietf.org/html/rfc3339). |
| timestamp | Série de 12 ou 14 caractères ASCII allant de 0 à 9, avec des séparateurs facultatifs : aaaa-mm-jjTHH:mm, aaaammjjhhmm, aaaa-mm-jjTHH:mm:ss, aaaammjjHHmmss, tel que défini dans [RFC 3339](https://tools.ietf.org/html/rfc3339). |
| boolean | Peut contenir « true » ou « false », insensible à la casse. |
| nombre | Une série de 0 ou de plusieurs caractères ASCII allant de 0 à 9, pouvant commencer par 0 (par exemple, 0001) et être suivi éventuellement d’un point « . » et série de 1 ou de plusieurs caractères ASCII allant de 0 à 9, pouvant se terminer par 0 (par exemple, .100) |
| hex | Série de 1 ou de plusieurs caractères ASCII compris dans l’ensemble de chiffres 0 à 1 et de lettres A à F ; la valeur peut comporter le préfixe 0 |
| locale | Chaîne qui correspond à la syntaxe spécifiée dans [RFC 5646](https://tools.ietf.org/html/rfc5646). |

## <a name="order-of-scoped-resource-set-rules-getting-applied"></a>Ordre d’application des règles de jeu de ressources déterminé.

Voici l’ordre des opérations permettant d’appliquer des règles de jeu de ressources déterminé :

1. Les étendues plus sélectives sont prioritaires si une ressource est conforme à deux règles. Par exemple, les règles d’une étendue `container/folder` s’appliquent avant les règles dont la portée est `container`.

1. L’ordre des règles au sein d’une étendue particulière. Il est modifiable dans l’expérience utilisateur.

1. Si une ressource ne correspond à aucune règle spécifiée, les heuristiques du jeu de ressources par défaut s’appliquent.

## <a name="examples"></a>Exemples

### <a name="example-1"></a>Exemple 1

Extraction de données SAP en charges complètes et différentielles

#### <a name="inputs"></a>Entrées

Fichiers :

- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_02.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/delta/2020/01/15/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_02.txt`

#### <a name="scoped-resource-set-rule"></a>Règle de jeu de ressources déterminé

**Étendue :** `https://myazureblob.blob.core.windows.net/bar/`

**Nom d’affichage :** « Client externe »

**Nom qualifié :** `customer/{extract:string}/{year:int}/{month:int}/{day:int}/saptable_customer_{date_from:date}_{date_to:time}_{sequence:int}.txt`

**Nom du jeu de ressources :** true

#### <a name="output"></a>Output

Une ressource de jeu de ressources

**Nom d’affichage :** Client externe

**Nom qualifié :** `https://myazureblob.blob.core.windows.net/bar/customer/{extract}/{year}/{month}/{day}/saptable_customer_{date_from}_{date_to}_{sequence}.txt`

### <a name="example-2"></a>Exemple 2

Données IoT au format Avro

#### <a name="inputs"></a>Entrées

Fichiers :

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="scoped-resource-set-rules"></a>Règles de jeu de ressources déterminé

**Étendue :** `https://myazureblob.blob.core.windows.net/bar/`

Règle 1

**Nom d’affichage :** « machine-89 »

**Nom qualifié :** `raw/machinename-89/{date:date}/{time:time}-{id:int}.avro`

**Nom du jeu de ressources :** true

Règle 2

**Nom d’affichage :** « machine-90 »

**Nom qualifié :** `raw/machinename-90/{date:date}/{time:time}-{id:int}.avro`

#### <a name="resource-set-true"></a>*Nom du jeu de ressources : true*

#### <a name="outputs"></a>Sorties

2 jeux de ressources

Jeu de ressources 1

**Nom d’affichage :** machine-89

**Nom qualifié :** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

Jeu de ressources 2

**Nom d’affichage :** machine-90

**Nom qualifié :** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-3"></a>Exemple 3

Données IoT au format Avro

#### <a name="inputs"></a>Entrées

Fichiers :

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.netbar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="scoped-resource-set-rule"></a>Règle de jeu de ressources déterminé

**Étendue :** `https://myazureblob.blob.core.windows.net/bar/`

**Nom d’affichage :** « Machine-{{Machineid}} »

**Nom qualifié :** `raw/machinename-{{machineid:int}}/{date:date}/{time:time}-{id:int}.avro`

**Nom du jeu de ressources :** true

#### <a name="outputs"></a>Sorties

Jeu de ressources 1

**Nom d’affichage :** machine-89

**Nom qualifié :** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

Jeu de ressources 2

**Nom d’affichage :** machine-90

**Nom qualifié :** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-4"></a>Exemple 4

Ne pas regrouper dans des jeux de ressources

#### <a name="inputs"></a>Entrées

Fichiers :

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="scoped-resource-set-rule"></a>Règle de jeu de ressources déterminé

**Étendue :** `https://myazureblob.blob.core.windows.net/bar/`

**Nom d’affichage :** `Machine-{{machineid}}`

**Nom qualifié :** `raw/machinename-{{machineid:int}}/{{:date}}/{{:time}}-{{:int}}.avro`

**Jeu de ressources :** false

#### <a name="outputs"></a>Sorties

4 ressources individuelles

Ressource 1

**Nom d’affichage :** machine-89

**Nom qualifié :** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`

Ressource 2

**Nom d’affichage :** machine-89

**Nom qualifié :** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`

Ressource 3

**Nom d’affichage :** machine-89

**Nom qualifié :** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`

Ressource 4

**Nom d’affichage :** machine-90

**Nom qualifié :** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

## <a name="next-steps"></a>Étapes suivantes

Commencez par [inscrire et analyser un compte de stockage Azure Data Lake Storage Gen2](register-scan-adls-gen2.md).