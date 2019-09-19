---
title: Modèles Microsoft Azure Resource Manager
description: Décrit comment utiliser des modèles Azure Resource Manager pour le déploiement de ressources.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/07/2019
ms.author: tomfitz
ms.openlocfilehash: 61e9bbabee969280c07521edb05d67ba68c0c58e
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801980"
---
# <a name="azure-resource-manager-templates"></a>Modèles Microsoft Azure Resource Manager

Avec la migration vers le cloud, de nombreuses équipes ont adopté des méthodes de développement agile. Ces équipes effectuent des itérations rapides. Elles doivent déployer à plusieurs reprises leurs solutions dans le cloud et savoir que leur infrastructure est dans un état fiable. Comme l’infrastructure fait partie intégrante du processus itératif, la séparation entre les opérations et le développement a disparu. Les équipes doivent gérer le code d’infrastructure et le code d’application à l’aide d’un processus unifié.

Pour relever ces défis, vous pouvez automatiser les déploiements et utiliser la pratique de l’infrastructure en tant que code. Dans le code, vous définissez l’infrastructure qui doit être déployée. Le code d’infrastructure devient partie intégrante de votre projet. Tout comme le code d’application, vous stockez le code d’infrastructure dans un dépôt source et vous gérez ses versions. Toute personne de votre équipe peut exécuter le code et déployer des environnements similaires.

Pour implémenter une infrastructure en tant que code pour vos solutions Azure, utilisez des modèles Azure Resource Manager. Le modèle est un fichier JSON (JavaScript Object Notation) qui définit l’infrastructure et la configuration de votre projet. Le modèle utilise la syntaxe déclarative, qui vous permet d’indiquer ce que vous envisagez de déployer sans avoir à écrire la séquence de commandes de programmation pour le créer. Dans le modèle, vous spécifiez les ressources à déployer et les propriétés de ces ressources.

## <a name="benefits-of-resource-manager-templates"></a>Avantages des modèles Resource Manager

Les modèles Resource Manager offrent les avantages suivants :

* Déployer, gérer et superviser toutes les ressources de votre solution sous forme de groupe, plutôt qu’individuellement

* Déployez votre solution à plusieurs reprises tout au long du cycle de vie de développement. Vous avez ainsi l’assurance que vos ressources présentent un état cohérent lors de leur déploiement.

* Gérer votre infrastructure à l’aide de modèles déclaratifs plutôt que de scripts

Si vous essayez de choisir entre utiliser des modèles Resource Manager ou l’un des autres services d’infrastructure en tant que code, tenez compte des avantages suivants que présentent les modèles par rapport à ces services :

* Les nouveaux services et fonctionnalités Azure sont immédiatement disponibles dans les modèles. Dès qu’un fournisseur de ressources introduit de nouvelles ressources, vous pouvez les déployer par le biais de modèles. Avec les autres services d’infrastructures en tant que code, vous devez attendre que des tiers implémentent des interfaces pour les nouvelles ressources.

* Les déploiements de modèle sont gérés par le biais d’un seul envoi du modèle, plutôt qu’avec plusieurs commandes impératives. Resource Manager orchestre le déploiement de ressources interdépendantes afin qu’elles soient créées dans l’ordre approprié. Il analyse le modèle et détermine l’ordre correct pour le déploiement en fonction des références entre les ressources.

   ![Comparaison de déploiements de modèles](./media/template-deployment-overview/template-processing.png)

* Les déploiements de modèles font l’objet d’un suivi dans le portail Azure. Vous pouvez consulter l’historique des déploiements et obtenir des informations sur le déploiement des modèles. Vous pouvez voir le modèle qui a été déployé, les valeurs de paramètre passées et toutes les valeurs de sortie. Les autres services d’infrastructure en tant que code ne font pas l’objet d’un suivi par le biais du portail.

   ![Historique de déploiement](./media/template-deployment-overview/deployment-history.png)

* Les déploiements de modèles sont soumis à une validation en amont. Resource Manager vérifie le modèle avant de commencer le déploiement pour être sûr que celui-ci va réussir. Votre déploiement est moins susceptible de s’arrêter dans un état à moitié terminé.

* Si vous utilisez des [stratégies Azure](../governance/policy/overview.md), la correction des stratégies est effectuée sur des ressources non conformes quand elles sont déployées par le biais de modèles.

* Microsoft fournit des [blueprints](../governance/blueprints/overview.md) de déploiement pour respecter les standards de réglementation et de conformité. Ces blueprints incluent des modèles prédéfinis pour différentes architectures.

## <a name="idempotent"></a>Idempotent

Idempotent signifie simplement que vous pouvez exécuter les mêmes opérations plusieurs fois et obtenir le même résultat. Le déploiement d’un modèle Resource Manager est idempotent. Vous pouvez déployer le même modèle plusieurs fois et obtenir les mêmes types de ressources dans le même état. Ce concept est important car cela signifie que vous obtenez des résultats cohérents, que vous redéployiez un modèle dans un groupe de ressources existant ou que vous déployiez un modèle dans un nouveau groupe de ressources.

Supposons qu’après avoir déployé trois ressources dans un groupe de ressources, vous décidez que vous devez ajouter une quatrième ressource. Au lieu de créer un modèle qui contient uniquement la nouvelle ressource, vous pouvez ajouter la quatrième ressource à votre modèle existant. Quand vous déployez le nouveau modèle dans le groupe de ressources qui contient déjà trois ressources, Resource Manager détermine les actions à entreprendre.

Si la ressource existe dans le groupe de ressources et que la demande ne contient aucune mise à jour des propriétés, aucune action n’est effectuée. Si la ressource existe mais que des propriétés ont changé, la ressource existante est mise à jour. Si la ressource n’existe pas, la nouvelle ressource est créée.

Vous avez l’assurance qu’une fois le déploiement terminé, les ressources sont toujours dans l’état attendu.

## <a name="template-file"></a>Fichier de modèle

Dans votre modèle, vous pouvez écrire des [expressions de modèle](template-expressions.md) qui étendent les fonctionnalités de JSON. Ces expressions utilisent les [fonctions](resource-group-template-functions.md) fournies par Resource Manager.

Le modèle comprend les sections suivantes :

* [Paramètres](template-parameters.md) - Pendant le déploiement, fournissez des valeurs qui permettent d’utiliser le même modèle avec différents environnements.

* [Variables](template-variables.md) - Définissez des valeurs qui sont réutilisées dans vos modèles. Elles peuvent être construites à partir de valeurs de paramètres.

* [Fonctions définies par l’utilisateur](template-user-defined-functions.md) - Créez des fonctions personnalisées qui simplifient votre modèle.

* [Ressources](resource-group-authoring-templates.md#resources) - Spécifiez les ressources à déployer.

* [Sorties](template-outputs.md) - Retournez des valeurs à partir des ressources déployées.

## <a name="template-features"></a>Fonctionnalités de modèle

Resource Manager analyse les dépendances pour garantir que les ressources sont créées dans l’ordre approprié. La plupart des dépendances sont déterminées implicitement. Toutefois, vous pouvez définir explicitement une dépendance pour garantir qu’une ressource est déployée avant une autre ressource. Pour plus d’informations, consultez [Définition de dépendances dans des modèles Azure Resource Manager](resource-group-define-dependencies.md).

Vous pouvez ajouter une ressource à votre modèle et éventuellement la déployer. En général, vous passez une valeur de paramètre qui indique si la ressource doit être déployée. Pour plus d’informations, consultez [Déploiement conditionnel dans des modèles Resource Manager](conditional-resource-deployment.md).

Au lieu de répéter des blocs de code JSON de nombreuses fois dans votre modèle, vous pouvez utiliser un élément copy pour spécifier plusieurs instances d’une variable, d’une propriété ou d’une ressource. Pour plus d’informations, consultez [Itération de variable, de propriété ou de ressource dans les modèles Azure Resource Manager](resource-group-create-multiple.md).

## <a name="export-templates"></a>Exporter des modèles

Vous pouvez obtenir un modèle pour un groupe de ressources existant en exportant l’état actuel du groupe de ressources ou en consultant le modèle utilisé pour un déploiement particulier. L’affichage du [modèle exporté](export-template-portal.md) est un moyen utile pour en découvrir plus sur sa syntaxe.

Lorsque vous créez une solution à partir du portail, cette solution inclut automatiquement un modèle de déploiement. Vous n’êtes pas contraint de créer votre modèle à partir de zéro, car vous pouvez partir du modèle de votre solution et le personnaliser en fonction de vos besoins spécifiques. Pour obtenir un exemple, consultez [Démarrage rapide : Créer et déployer des modèles Azure Resource Manager à l’aide du portail Azure](./resource-manager-quickstart-create-templates-use-the-portal.md).

## <a name="template-deployment-process"></a>Processus de déploiement de modèle

Quand vous déployez un modèle, Resource Manager le convertit en opérations d’API REST. Par exemple, lorsque Resource Manager reçoit un modèle avec la définition de ressource suivante :

```json
"resources": [
  {
    "apiVersion": "2016-01-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {
    }
  }
]
```

Il convertit la définition en opération API REST suivante, qui est envoyée au fournisseur de ressources Microsoft.Storage :

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
REQUEST BODY
{
  "location": "westus",
  "properties": {
  }
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage"
}
```

## <a name="template-design"></a>Conception de modèle

La manière dont vous définissez les modèles et les groupes de ressources dépend entièrement de vous et de la façon dont vous voulez gérer votre solution. Par exemple, vous pouvez déployer votre application à trois niveaux via un modèle unique pour un groupe de ressources unique.

![modèle à trois niveaux](./media/template-deployment-overview/3-tier-template.png)

Cependant, il est inutile de définir toute votre infrastructure dans un seul modèle. Il peut être judicieux de diviser les exigences de votre déploiement dans un ensemble de modèles ciblés destinés à un usage particulier. Vous pouvez facilement réutiliser ces modèles pour différentes solutions. Pour déployer une solution particulière, créez un modèle de référence qui relie tous les modèles requis. L’illustration suivante montre comment déployer une solution à trois niveaux via un modèle parent qui inclut trois modèles imbriqués.

![modèle à niveaux imbriqués](./media/template-deployment-overview/nested-tiers-template.png)

Si vos niveaux vont avoir des cycles de vie distincts, vous pouvez déployer les trois niveaux dans des groupes de ressources séparés. Notez que les ressources peuvent toujours être liées aux ressources dans d’autres groupes.

![modèle niveau](./media/template-deployment-overview/tier-templates.png)

Pour plus d’informations sur les modèles imbriqués, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les propriétés de fichiers de modèle, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour bien démarrer avec le développement d’un modèle, consultez [Utiliser Visual Studio Code pour créer des modèles Azure Resource Manager](resource-manager-tools-vs-code.md).
* Pour une introduction au service Resource Manager, notamment de ses fonctionnalités de gestion, consultez [Vue d’ensemble d’Azure Resource Manager](resource-group-overview.md).

