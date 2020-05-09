---
title: 'Démarrage rapide : Créer une requête partagée avec des modèles'
description: Dans ce guide de démarrage rapide, vous utilisez un modèle Resource Manager pour créer une requête partagée Resource Graph qui compte les machines virtuelles par système d’exploitation.
ms.date: 04/28/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 050cf26da2054883fceaa08b11f94c6af4c85a16
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254240"
---
# <a name="quickstart-create-a-shared-query-by-using-a-resource-manager-template"></a>Démarrage rapide : Créer une requête partagée en utilisant un modèle Resource Manager

Les requêtes Resource Graph peuvent être enregistrées comme _requête privée_ ou _requête partagée_. Une requête privée est enregistrée dans le profil du portail d’une personne et n’est visible par personne d’autre. Une requête partagée est un objet Resource Manager qui peut être partagé avec d’autres utilisateurs via des autorisations et un accès en fonction du rôle. Une requête partagée fournit une exécution courante et cohérente de la découverte des ressources. Ce guide de démarrage rapide utilise un modèle Resource Manager pour créer une requête partagée.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="create-a-shared-query"></a>Créer une requête partagée

Dans ce guide de démarrage rapide, vous créez une requête partagée appelée _Compter les VM par OS_. Pour tester cette requête dans le kit SDK ou dans le portail avec l’Explorateur Resource Graph, consultez [Exemples - Compter les VM par OS](./samples/starter.md#count-os).

### <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce guide de démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/resourcegraph-sharedquery-countos/).

:::code language="json" source="~/quickstart-templates/resourcegraph-sharedquery-countos/azuredeploy.json" highlight="28-37":::

La ressource définie dans le modèle est :

- [Microsoft.ResourceGraph/queries](/azure/templates/microsoft.resourcegraph/queries)

### <a name="deploy-the-template"></a>Déployer le modèle

> [!NOTE]
> Le service Azure Resource Graph est gratuit. Pour plus d’informations, consultez [Vue d’ensemble d’Azure Resource Graph](./overview.md).

1. Sélectionnez l’image suivante pour vous connecter au portail Azure et ouvrir le modèle :

   [![Déployer le modèle de stratégie sur Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json)

1. Sélectionnez ou entrez les valeurs suivantes :

   | Nom | Valeur |
   |------|-------|
   | Abonnement | Sélectionnez votre abonnement Azure. |
   | Resource group | Sélectionnez **Créer**, spécifiez un nom, puis sélectionnez **OK**. |
   | Emplacement | Sélectionnez une région. Par exemple, **USA Centre**. |
   | Nom de la requête | Laissez la valeur par défaut **Compter les VM par OS**. |
   | Code de la requête | Laissez la valeur par défaut `Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)` |
   | Description de la requête | Laissez la valeur par défaut **Cette requête partagée compte toutes les ressources de machine virtuelle et les classe par type de système d’exploitation.** |
   | J’accepte les termes et conditions mentionnés ci-dessus | (Sélectionner) |

1. Sélectionnez **Achat**.

Ressources supplémentaires :

- Pour trouver des exemples de modèles supplémentaires, consultez [Modèle de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Pour obtenir des informations de référence sur les modèles, accédez au [document de référence des modèles Azure](/azure/templates/microsoft.resourcegraph/allversions).
- Pour savoir comment développer des modèles Resource Manager, consultez la [documentation Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- Pour découvrir le déploiement au niveau de l’abonnement, consultez [Créer des groupes de ressources et des ressources au niveau de l’abonnement](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Valider le déploiement

Pour exécuter la nouvelle requête partagée, suivez ces étapes :

1. Dans la barre de recherche du portail, recherchez et sélectionnez **Requêtes Resource Graph**.

1. Sélectionnez la requête partagée appelée **Compter les VM par OS**, puis sélectionnez l’onglet **Résultats** dans la page **Vue d’ensemble**.

Vous pouvez aussi ouvrir la requête partagée depuis l’Explorateur Resource Graph :

1. Dans la barre de recherche du portail, recherchez et sélectionnez **Explorateur Resource Graph**.

1. Sélectionnez le bouton **Ouvrir une requête**.

1. Remplacez le **Type** par _Requêtes partagées_. Si vous ne voyez pas **Compter les VM par OS** dans la liste, utilisez la zone de filtre pour limiter les résultats. Une fois que la requête partagée **Compter les VM par OS** est visible, sélectionnez son nom.

1. Une fois que la requête est chargée, sélectionnez le bouton **Exécuter la requête**. Les résultats s’affichent sous l’onglet **Résultats** ci-dessous.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour supprimer la requête partagée créée, suivez ces étapes :

1. Dans la barre de recherche du portail, recherchez et sélectionnez **Requêtes Resource Graph**.

1. Cochez la case à côté de la requête partagée appelée **Compter les VM par OS**.

1. Sélectionnez le bouton **Supprimer** en haut de la page.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé une requête partagée Resource Graph.

Pour en savoir plus sur les requêtes partagées, continuez avec le tutoriel pour :

> [!div class="nextstepaction"]
> [Gérer les requêtes dans le portail Azure](./tutorials/create-share-query.md)