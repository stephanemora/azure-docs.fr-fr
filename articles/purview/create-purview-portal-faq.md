---
title: Forum aux questions sur la création d’un compte Azure Purview dans le portail
description: Cet article répond aux questions fréquemment posées sur la création de comptes Purview via le portail
author: nayenama
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/11/2021
ms.openlocfilehash: bf148408d733dffad862eecf51cc06455846b19a
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122527788"
---
# <a name="faq-about-creating-purview-accounts-via-portal"></a>FAQ sur la création de comptes Purview via le portail

Cet article répond aux questions courantes que les clients et les équipes de terrain posent souvent sur la [création d’un compte Purview](create-catalog-portal.md) à l’aide du portail Azure.

## <a name="common-questions"></a>Questions courantes

Découvrez les réponses aux questions courantes suivantes.

### <a name="azure-policy-blocking-from-creating-storage-and-event-hub-namespace"></a>Une stratégie Azure empêche la création d’un espace de noms de stockage et Event Hub 

* Si **Azure Policy** empêche toutes les applications de créer un **compte de stockage** et un **espace de noms EventHub**, vous devez introduire une exception de stratégie au moyen d’une étiquette pouvant être renseignée lors du processus de création d’un compte Purview. La raison principale vient de ce que, pour chaque compte Purview créé, un groupe de ressources managé doit être créé, et dans ce groupe de ressources, un compte de stockage et un espace de noms EventHub.

   >[!IMPORTANT]
   >Vous n’êtes pas obligé de suivre cette étape si vous ne disposez pas d’Azure Policy, ou si aucune stratégie Azure existante ne bloque la création du **compte de stockage** et de l’**espace de noms EventHub**.

    1. Accédez au portail Azure et recherchez **Stratégie**.
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
