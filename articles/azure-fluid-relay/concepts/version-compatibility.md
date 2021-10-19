---
title: Compatibilité des versions avec les versions de l’infrastructure Fluid
description: >
  Comment déterminer les versions de l’infrastructure Fluid compatibles avec le service Relais Azure Fluid.
services: azure-fluid
author: tylerbutler
ms.author: tylerbu
ms.date: 09/28/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: 62493eeb270a171fe874a877dfd51ac404eaab27
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661887"
---
# <a name="version-compatibility-with-fluid-framework-releases"></a>Compatibilité des versions avec les versions de l’infrastructure Fluid

> [!NOTE]
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.

Pour connecter votre application au service Relais Azure Fluid, vous allez utiliser la bibliothèque **@fluidframework/azure-client** . Vous allez également utiliser la bibliothèque **fluid-framework** pour utiliser les structures de données de base et celles fournies par l’infrastructure Fluid.

Étant donné que vous utilisez le service Relais Azure Fluid, vous devez commencer par installer la dernière version disponible de @fluidframework/azure-client, puis utiliser cette version pour déterminer la version de la bibliothèque de l’infrastructure Fluid à utiliser. La bibliothèque exprime une *dépendance d’homologue* sur la version du package d’infrastructure Fluid dont elle dépend.

Vous pouvez utiliser l’outil [install-peerdeps](https://www.npmjs.com/package/install-peerdeps) pour installer @fluidframework/azure-client et la version compatible de l’infrastructure Fluid à l’aide de la commande suivante :

```bash
npx install-peerdeps @fluidframework/azure-client
```

> [!TIP]
> Pendant la période de préversion publique, les versions de **@fluidframework/azure-client** et de l’**infrastructure Fluid** correspondent. Autrement dit, si la version actuelle de **@fluidframework/azure-client** est 0.48, elle est compatible avec l’**infrastructure Fluid** 0.48. L’inverse est également vrai.

## <a name="compatibility-table"></a>Table de compatibilité

| Package npm                         | Version minimale | API                                                              |
| ----------------------------------  | :-------------- | :--------------------------------------------------------------- |
| @fluidframework/azure-client        | [0.48.4][]      | [API](https://fluidframework.com/docs/apis/azure-client/)        |
| Infrastructure Fluid                     | [0.48.4][]      | [API](https://fluidframework.com/docs/apis/fluid-framework/)     |
| @fluidframework/azure-service-utils | [0.48.4][]      | [API](https://fluidframework.com/docs/apis/azure-service-utils/) |
| @fluidframework/test-client-utils   | [0.48.4][]      | [API](https://fluidframework.com/docs/apis/test-client-utils/)   |

[0.48.4]: https://fluidframework.com/docs/updates/v0.48/

## <a name="next-steps"></a>Étapes suivantes

- [Provisionner un service Relais Azure Fluid](../how-tos/connect-fluid-azure-service.md)
- [Se connecter à un service Relais Azure Fluid](../how-tos/connect-fluid-azure-service.md)
- [Utiliser AzureClient pour les tests locaux](../how-tos/local-mode-with-azure-client.md)
