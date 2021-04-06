---
title: Se connecter à GitHub
description: Utiliser GitHub pour spécifier vos références d’entité Common Data Model
author: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 5d555d7bc4d3aae9c016cacbe17b68c30859d99a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100372278"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>Utiliser GitHub pour lire les références d’entité Common Data Model

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Le connecteur GitHub dans Azure Data Factory est utilisé uniquement pour recevoir le schéma de référence d’entité pour le format [Common Data Model](format-common-data-model.md) dans le flux de données de mappage.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés suivantes sont prises en charge pour le service lié GitHub.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété de type doit être définie sur **GitHub**. | Oui
| userName | Nom d’utilisateur GitHub | Oui |
| mot de passe | Mot de passe GitHub | Oui |

## <a name="next-steps"></a>Étapes suivantes

Créez un [jeu de données source](data-flow-source.md) dans le flux de données de mappage.