---
title: Se connecter à GitHub
titleSuffix: Azure Data Factory & Azure Synapse
description: Utiliser GitHub pour spécifier vos références d’entité Common Data Model
author: linda33wj
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: jingwang
ms.openlocfilehash: 8d765a5150180303f55cc669da3ddb84deea68da
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123312836"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>Utiliser GitHub pour lire les références d’entité Common Data Model

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Le connecteur GitHub dans Azure Data Factory est utilisé uniquement pour recevoir le schéma de référence d’entité pour le format [Common Data Model](format-common-data-model.md) dans le flux de données de mappage.

## <a name="create-a-linked-service-to-github-using-ui"></a>Créer un service lié à GitHub à l’aide de l’interface utilisateur

Utilisez les étapes suivantes pour créer un service lié à GitHub dans l’interface utilisateur du portail Azure.

1. Accédez à l’onglet Gérer dans votre espace de travail Azure Data Factory ou Synapse et sélectionnez Services liés, puis cliquez sur Nouveau :

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Capture d’écran de la création d’un nouveau service lié avec l’interface utilisateur Azure Data Factory.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Capture d’écran de la création d’un nouveau service lié avec l’interface utilisateur Azure Synapse.":::

2. Recherchez GitHub et sélectionnez le connecteur GitHub.

   :::image type="content" source="media/connector-github/github-connector.png" alt-text="Capture d’écran du connecteur GitHub.":::    


1. Configurez les informations du service, testez la connexion et créez le nouveau service lié.

   :::image type="content" source="media/connector-github/configure-github-linked-service.png" alt-text="Capture d’écran de la configuration du service lié pour GitHub.":::


## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés suivantes sont prises en charge pour le service lié GitHub.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété de type doit être définie sur **GitHub**. | Oui
| userName | Nom d’utilisateur GitHub | Oui |
| mot de passe | Mot de passe GitHub | Oui |

## <a name="next-steps"></a>Étapes suivantes

Créez un [jeu de données source](data-flow-source.md) dans le flux de données de mappage.