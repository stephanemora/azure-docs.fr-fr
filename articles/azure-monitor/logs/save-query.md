---
title: Enregistrer une requête dans Azure Monitor Log Analytics (préversion)
description: Décrit comment enregistrer une requête dans Log Analytics.
ms.subservice: logs
ms.topic: article
author: bwren
ms.author: bwren
ms.date: 05/20/2021
ms.openlocfilehash: 0334fa67849acdf628ec77a55fcf7d7f5435cbf6
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110482114"
---
# <a name="save-a-query-in-azure-monitor-log-analytics-preview"></a>Enregistrer une requête dans Azure Monitor Log Analytics (préversion)
Les [requêtes de journal](log-query-overview.md) sont des requêtes dans Azure Monitor qui vous permettent de traiter et de récupérer des données dans un espace de travail Log Analytics. L’enregistrement d’une requête de journal vous permet :

- d’utiliser la requête dans tous les contextes Log Analytics, y compris l’espace de travail et la ressource centrée sur les ressources.
- d’autoriser les autres utilisateurs à exécuter la même requête.
- de créer une bibliothèque de requêtes courantes pour votre organisation.

## <a name="save-options"></a>Enregistrer des options
Lorsque vous enregistrez une requête, elle est stockée dans un pack de requêtes qui présente les avantages suivants par rapport à la méthode précédente de stockage de la requête dans un espace de travail. L’enregistrement dans un pack de requêtes est la méthode recommandée qui offre les avantages suivants :

- Détectabilité plus facile grâce à la possibilité de filtrer et de regrouper les requêtes en fonction de différentes propriétés.
- Requêtes disponibles lors de l’utilisation d’une étendue de ressource dans Log Analytics.
- Rendre les requêtes disponibles entre les abonnements.
- Davantage de données disponibles pour décrire et classer la requête.


## <a name="save-a-query"></a>Enregistrement d’une requête
Pour enregistrer une requête dans un pack de requêtes, sélectionnez **Enregistrer en tant que requête Log Analytics** dans la liste déroulante **Enregistrer** dans Log Analytics.

[![Menu d’enregistrement de la requête](media/save-query/save-query.png)](media/save-query/save-query.png#lightbox)

Lorsque vous enregistrez une requête dans un pack de requêtes, la boîte de dialogue suivante s’affiche, dans laquelle vous pouvez fournir des valeurs pour les propriétés de la requête. Les propriétés de requête sont utilisées pour le filtrage et le regroupement de requêtes similaires pour vous aider à trouver la requête que vous recherchez. Pour obtenir une description détaillée de chaque propriété, consultez [Propriétés de la requête](queries.md#query-properties).

La plupart des utilisateurs doivent laisser l’option **Enregistrer dans le pack de requêtes par défaut**, ce qui permet d’enregistrer la requête dans le [pack de requêtes par défaut](query-packs.md#default-query-pack). Désactivez cette case s’il existe d’autres packs de requêtes dans votre abonnement. Consultez [Packes de requêtes dans Azure Monitor Logs (préversion)](query-packs.md) pour avoir plus de détails sur la création d’un nouveau pack de requêtes.

[![Boîte de dialogue d’enregistrement de la requête](media/save-query/save-query-dialog.png)](media/save-query/save-query-dialog.png#lightbox)

## <a name="edit-a-query"></a>Modifier une requête
Vous souhaiterez peut-être modifier une requête que vous avez déjà enregistrée. Cela peut consister à modifier la requête elle-même ou à modifier l’une de ses propriétés. Après avoir ouvert une requête existante dans Log Analytics, vous pouvez la modifier en sélectionnant **Modifier les détails** de la requête dans la liste déroulante **Enregistrer**. Cela vous permet d’enregistrer la requête modifiée avec les mêmes propriétés, ou de modifier les propriétés avant de les enregistrer.

Si vous souhaitez enregistrer la requête sous un autre nom, sélectionnez **Enregistrer en tant que requête Log Analytics** de la même façon que si vous étiez en train de créer une nouvelle requête. 


## <a name="save-as-a-legacy-query"></a>Enregistrer en tant que requête héritée
L’enregistrement en tant que requête héritée n’est pas recommandé en raison des avantages des packs de requêtes listés ci-dessus. Vous pouvez cependant enregistrer une requête dans l’espace de travail pour l’associer à d’autres requêtes qui ont été enregistrées dans l’espace de travail avant la publication des packs de requêtes. 

Pour enregistrer une requête héritée, sélectionnez **Enregistrer en tant que requête Log Analytics** dans la liste déroulante **Enregistrer** dans Log Analytics. Choisissez l’option **Enregistrer en tant que requête héritée** . La seule option disponible est la catégorie héritée.


## <a name="next-steps"></a>Étapes suivantes

[Prise en main des requêtes KQL](get-started-queries.md)
