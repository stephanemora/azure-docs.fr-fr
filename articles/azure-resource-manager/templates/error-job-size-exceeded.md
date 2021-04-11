---
title: Erreur de dépassement de taille de la tâche
description: Décrit comment résoudre les erreurs lorsque la taille d’une tâche ou le modèle est trop important.
ms.topic: troubleshooting
ms.date: 03/23/2021
ms.openlocfilehash: b39a0bba15e73bab1a85cbd9e36efebf82d6cf42
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889363"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>Résoudre les erreurs de dépassement de taille de la tâche

Cet article explique comment résoudre les erreurs **JobSizeExceededException** et **DeploymentJobSizeExceededException**.

## <a name="symptom"></a>Symptôme

Lors du déploiement d’un modèle, vous recevez une erreur indiquant que le déploiement a dépassé les limites.

## <a name="cause"></a>Cause

Vous recevez cette erreur lorsque le déploiement dépasse l’une des limites autorisées. En règle générale, cette erreur s’affiche lorsque la taille de votre modèle ou de la tâche qui exécute le déploiement est trop importante.

La tâche de déploiement ne peut pas dépasser 1 Mo. La tâche inclut les métadonnées relatives à la demande. Pour les modèles volumineux, les métadonnées associées au modèle peuvent dépasser la taille autorisée pour une tâche.

Le modèle ne peut pas dépasser 4 Mo. La limite de 4 Mo s’applique à l’état final du modèle une fois développé avec les définitions des ressources qui utilisent la [copie](copy-resources.md) pour créer de nombreuses instances. L’état final comprend également les valeurs résolues pour les variables et les paramètres.

Les autres limites du modèle sont les suivantes :

* 256 paramètres
* 256 variables
* 800 ressources (incluant le nombre de copies)
* 64 valeurs de sortie
* 24 576 caractères dans une expression de modèle

Lorsque vous utilisez des boucles de copie pour déployer une ressource, n’utilisez pas le nom de boucle en tant que dépendance :

```json
dependsOn: [ "nicLoop" ]
```

Au lieu de cela, utilisez l’instance de la ressource de la boucle dont vous devez dépendre. Par exemple :

```json
dependsOn: [
    "[resourceId('Microsoft.Network/networkInterfaces', concat('nic-', copyIndex()))]"
]
```

## <a name="solution-1---simplify-template"></a>Solution 1 - Simplifier le modèle

Votre première option consiste à simplifier le modèle. Cette option fonctionne lorsque votre modèle déploie un grand nombre de types de ressources différents. Envisagez de diviser le modèle en [modèles liés](linked-templates.md). Divisez vos types de ressources en groupes logiques et ajoutez un modèle lié pour chaque groupe. Par exemple, si vous devez déployer un grand nombre de ressources réseau, vous pouvez déplacer ces ressources vers un modèle lié.

Vous pouvez définir d’autres ressources comme dépendantes du modèle lié et [récupérer des valeurs à partir de la sortie du modèle lié](linked-templates.md#get-values-from-linked-template).

## <a name="solution-2---reduce-name-size"></a>Solution 2 - Réduire la taille du nom

Essayez de raccourcir la longueur des noms que vous utilisez pour les [paramètres](template-parameters.md), [variables](template-variables.md) et [sorties](template-outputs.md). Lorsque ces valeurs sont répétées à l’aide de boucles de copie, un grand nom est multiplié plusieurs fois.