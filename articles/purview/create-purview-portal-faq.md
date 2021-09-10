---
title: Créer une exception Azure Policy pour Azure Purview
description: Cet article explique comment créer une exception Azure Policy pour Purview tout en laissant les stratégies existantes en place pour maintenir la sécurité.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.topic: how-to
ms.date: 08/26/2021
ms.openlocfilehash: 10ac52f62da8f8c20ca79ad4d5bb3073eb2bfe69
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123111908"
---
# <a name="create-an-azure-policy-exception-for-purview"></a>Créer une exception Azure Policy pour Purview

De nombreux abonnements ont des stratégies [Azure Policy](../governance/policy/overview.md) en place qui limitent la création de certaines ressources. Cela permet de maintenir la sécurité et la propreté des abonnements. Toutefois, les comptes Purview déploient deux autres ressources Azure lors de leur création : un compte Stockage Azure et un espace de noms Event Hub. Lorsque vous [créez un compte Purview](create-catalog-portal.md), ces ressources sont déployées. Elles sont gérées par Azure. Vous n’avez donc pas besoin de les maintenir, mais vous devrez les déployer.

Pour conserver vos stratégies dans votre abonnement, tout en autorisant la création de ces ressources managées, vous pouvez créer une exception de stratégie.

## <a name="create-a-policy-exception-for-purview"></a>Créer une exception de stratégie pour Purview

1. Accédez au [portail Azure](https://portal.azure.com) et recherchez **Policy**

    :::image type="content" source="media/create-purview-portal-faq/search-for-policy.png" alt-text="Capture d’écran montrant la barre de recherche Portail Azure, recherche du mot clé Policy.":::

1. Suivez [Créer une définition de stratégie personnalisée](../governance/policy/tutorials/create-custom-policy-definition.md) ou modifiez une stratégie existante pour ajouter deux exceptions avec l’opérateur `not` et l’étiquette `resourceBypass` :

    ```json
    {
    "mode": "All",
      "policyRule": {
        "if": {
          "anyOf": [
          {
            "allOf": [
            {
              "field": "type",
              "equals": "Microsoft.Storage/storageAccounts"
            },
            {
              "not": {
                "field": "tags['<resourceBypass>']",
                "exists": true
              }
            }]
          },
          {
            "allOf": [
            {
              "field": "type",
              "equals": "Microsoft.EventHub/namespaces"
            },
            {
              "not": {
                "field": "tags['<resourceBypass>']",
                "exists": true
              }
            }]
          }]
        },
        "then": {
          "effect": "deny"
        }
      },
      "parameters": {}
    }
    ```
  
    > [!Note]
    > Tant que la stratégie détecte l’étiquette, celle-ci peut être constituée de n’importe quel texte à côté de `resourceBypass` ; c’est à vous de définir sa valeur lors de la création de Purview aux étapes suivantes.

    :::image type="content" source="media/create-catalog-portal/policy-definition.png" alt-text="Capture d’écran illustrant la création d’une définition de stratégie.":::

1. [Créez une affectation de stratégie](../governance/policy/assign-policy-portal.md) à l’aide de la stratégie personnalisée créée.

    :::image type="content" source="media/create-catalog-portal/policy-assignment.png" alt-text="Capture d’écran illustrant la création d’une affectation de stratégie" lightbox="./media/create-catalog-portal/policy-assignment.png":::

> [!Note] 
> Si vous utilisez **Azure Policy** et qu’il vous faut ajouter une exception, comme dans les **Prérequis**, vous devez choisir l’étiquette appropriée. Par exemple, vous pouvez ajouter l’étiquette `resourceBypass` : :::image type="content" source="media/create-catalog-portal/add-purview-tag.png" alt-text="Ajout d’une étiquette au compte Purview.":::

## <a name="next-steps"></a>Étapes suivantes

Pour configurer Azure Purview à l’aide de Private Link, consultez [Utiliser des points de terminaison privés pour votre compte Azure Purview](./catalog-private-link.md).
