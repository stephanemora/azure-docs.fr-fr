---
title: Exclusion d’éléments d’une appartenance dynamique dans Azure Virtual Network Manager (préversion)
description: Découvrez comment exclure des éléments d’une appartenance dynamique dans Azure Virtual Network Manager.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 318a2eba29ee7641219e8c970c3dd3a51407ddf3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097073"
---
# <a name="excluding-elements-from-dynamic-membership-in-azure-virtual-network-manager-preview"></a>Exclusion d’éléments d’une appartenance dynamique dans Azure Virtual Network Manager (préversion)

Cet article explique comment utiliser des instructions conditionnelles pour exclure des réseaux virtuels d’une appartenance dynamique. Vous créez ces instructions conditionnelles à l’aide de l’éditeur de base en sélectionnant des paramètres et des opérateurs dans un menu déroulant. Vous allez également apprendre à utiliser l’éditeur avancé pour mettre à jour les instructions conditionnelles d’un groupe de réseaux existant.

> [!IMPORTANT]
> Azure Virtual Network Manager est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="parameters-and-operators"></a><a name="parameters"></a> Paramètres et opérateurs

Les réseaux virtuels avec des appartenances dynamiques sont sélectionnés à l’aide d’instructions conditionnelles. Vous pouvez définir plusieurs instructions conditionnelles à l’aide d’*opérateurs logiques* tels que **ET** et **OU** pour des scénarios où vous devez affiner davantage les réseaux virtuels sélectionnés. 

Liste des paramètres pris en charge :

| Paramètres | Champ Éditeur avancé |
| ---------- | ------------------------- |
| Nom | `Name` |
| id | `Id` |
| Étiquettes| `tag['tagName']` |
| Nom de l’abonnement | `[subscription().Name]` |
| Identifiant d’abonnement | `[subscription().Id]` |
| Étiquettes d’abonnement | `[subscription().tags['tagName']]` |
| Nom du groupe de ressources | `[resourceGroup().Name]` |
| ID de groupe de ressources | `[resourceGroup().Id]` |
| Étiquettes de groupes de ressources | `[resourceGroup().tags['tagName']]` |

Liste des opérateurs pris en charge :

| Opérateurs | Éditeur avancé |
| --------- | --------------- |
| Contient | `"contains": <>` |
| Ne contient pas | `"notcontains": <>` |
| Dans | `"in": <>` |
| Pas dans | `"notin": <>` |
| Égal à | `"equals": <>` |
| Différent de | `"notequals": <>` |
| Contient l’un des | `"contains": <>` |
| Contient tous les | `"contains": <>` |
| Ne contient aucun des | `"notcontains": <>` |
| Existe | `"exists": true` |
| N’existe pas | `"exists": false` |

> [!NOTE]
> Les opérateurs *Existe* et *N’existe pas* sont utilisés uniquement avec le paramètre **Étiquettes**.

## <a name="basic-editor"></a>Éditeur de base

Supposons que vous disposez des réseaux virtuels suivants dans votre abonnement. Une étiquette *Production* ou *Test* est associée à chaque réseau virtuel. Vous souhaitez uniquement sélectionner les réseaux virtuels avec l’étiquette Production et dont le nom contient **VNet-A**.

* VNet-A-EastUS - *Production*
* VNet-A-WestUS - *Production*
* VNet-B-WestUS - *Test*
* VNet-C-WestUS - *Test*
* VNetA - *Production*
* VNetB - *Test*

Pour commencer à utiliser l’éditeur de base afin de créer votre instruction conditionnelle, vous devez créer un groupe de réseaux.

1. Accédez à votre instance Azure Virtual Network Manager et sélectionnez **Groupes de réseaux** sous *Paramètres*. Ensuite, sélectionnez **+ Ajouter** pour créer un groupe de réseaux.

1. Entrez un **Nom** pour le groupe de réseaux. Sélectionnez ensuite l’onglet **Instructions conditionnelles**.

1. Sélectionnez **Balises** dans la liste déroulante sous *Paramètre*, puis sélectionnez **Existe** dans la liste déroulante sous *Opérateur*.

1. Entrez **prod** sous *condition*, puis sélectionnez **Évaluer**. Vous ne devriez voir dans la liste que VNet-A-EastUS, VNet-B-WestUS et VNetA.

1. Ajoutez une autre instruction conditionnelle en sélectionnant l’opérateur logique **ET**. Sélectionnez **Nom** pour le *Paramètre* et **Contient** pour l’*Opérateur*. Entrez **VNet-A** dans le champ *Condition* . Sélectionnez **Évaluer** pour voir quel réseau virtuel s’affiche dans la liste. Vous ne devriez voir s’afficher que VNet-A-EastUS et VNet-A-WestUS.

1. Sélectionnez **Vérifier + créer**, puis **Créer** une fois la validation réussie.

> [!NOTE] 
> L’**éditeur de base** est disponible uniquement lors de la création d’un groupe de réseaux. 

## <a name="advanced-editor"></a>Éditeur avancé

L’éditeur avancé peut être utilisé pour sélectionner un réseau virtuel lors de la création d’un groupe de réseaux, ou de la mise à jour d’un groupe de réseaux existant. 

1. Sélectionnez le groupe de réseaux créé dans la section précédente. Sélectionnez ensuite l’onglet **Instructions conditionnelles**.

1. Les instructions conditionnelles du groupe de réseaux apparaissent dans l’affichage de l’éditeur avancé comme suit :

    ```json
    {
       "allOf": [
          {
             "field": "tags['Environment']",
             "exists": true
          },
          {
             "field": "Name",
             "contains": "VNet-A"
          }
       ]
    }
    ```

    Le paramètre `"allOf"` contient les deux instructions conditionnelles qui sont séparées par l’opérateur logique **ET**.

1. Pour ajouter une autre instruction conditionnelle pour un champ *Nom* *ne contenant pas* **WestUS**, entrez ce qui suit dans l’éditeur avancé :

    ```json
    {
       "allOf": [
          {
             "field": "tags['Environment']",
             "exists": true
          },
          {
             "field": "Name",
             "contains": "VNet-A"
          },
          {
             "field": "Name",
             "notcontains": "WestUS"
          }
       ]
    }
    ```

1. Ensuite, sélectionnez **Évaluer**. Vous ne devriez voir dans la liste que le réseau virtuel VNet-A-EastUS.

1. Sélectionnez **Vérifier + créer**, puis **Créer** une fois la validation réussie.

Pour obtenir la liste complète des paramètres et des opérateurs que vous pouvez utiliser avec l’éditeur avancé, consultez [Paramètres et opérateurs](#parameters). Pour plus d’exemples, voir ci-dessous :

## <a name="more-examples"></a>Autres exemples

### <a name="example-1-or-operator-only"></a>Exemple 1 : opérateur OU uniquement

Cet exemple utilise l’opérateur logique **OU** pour séparer deux instructions conditionnelles.

* Éditeur de base :

    :::image type="content" source="./media/how-to-exclude-elements/or-operator.png" alt-text="Capture d’écran de l’instruction conditionnelle du groupe de réseaux utilisant l’opérateur logique OU.":::

* Opérateur avancé :

    ```json
    {
       "anyOf": [
          {
             "field": "Name",
             "contains": "VNet-A"
          },
          {
             "field": "Name",
             "contains": "VNetA"
          }
       ]
    }
    ```

Le paramètre `"anyOf"` contient les deux instructions conditionnelles séparées par l’opérateur logique **ET**.

### <a name="example-2-and-and-or-operator-at-the-same-time"></a>Exemple 2 : opérateur ET et OU en même temps

* Éditeur de base :

    :::image type="content" source="./media/how-to-exclude-elements/both-operator.png" alt-text="Capture d’écran de l’instruction conditionnelle de groupe de réseaux utilisant les opérateurs logiques Ou et ET.":::

* Éditeur avancé :

```json
{
   "allOf": [
      {
         "anyOf": [
            {
               "field": "Name",
               "contains": "VNet-A"
            },
            {
               "field": "Name",
               "contains": "VNetA"
            }
         ]
      },
      {
         "field": "Name",
         "notcontains": "West"
      }
   ]
}
```

`"allOf"` et `"anyOf"` sont utilisés dans le code. Étant donné que l’opérateur **ET** est le dernier dans la liste, il se trouve dans la partie externe du code contenant les deux instructions conditionnelles avec l’opérateur **OU**.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [Groupes de réseaux](concept-network-groups.md).
- Créez une instance [Azure Virtual Network Manager](create-virtual-network-manager-portal.md).
