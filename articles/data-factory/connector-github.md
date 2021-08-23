---
title: Se connecter à GitHub
titleSuffix: Azure Data Factory & Azure Synapse
description: Utiliser GitHub pour spécifier vos références d’entité Common Data Model
author: linda33wj
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: jingwang
ms.openlocfilehash: 659b0883e1b4085c9a8ad61f3512909a9a738a66
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642045"
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