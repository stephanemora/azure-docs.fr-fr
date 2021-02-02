---
title: Erreur de dépassement de taille de la tâche
description: Décrit comment résoudre les erreurs lorsque la taille d’une tâche ou le modèle est trop important.
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.openlocfilehash: 1fde4918aff6e3bf494876f83c5b4313b3c5f3d2
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98610401"
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

## <a name="solution-1---simplify-template"></a>Solution 1 - Simplifier le modèle

Votre première option consiste à simplifier le modèle. Cette option fonctionne lorsque votre modèle déploie un grand nombre de types de ressources différents. Envisagez de diviser le modèle en [modèles liés](linked-templates.md). Divisez vos types de ressources en groupes logiques et ajoutez un modèle lié pour chaque groupe. Par exemple, si vous devez déployer un grand nombre de ressources réseau, vous pouvez déplacer ces ressources vers un modèle lié.

Vous pouvez définir d’autres ressources comme dépendantes du modèle lié et [récupérer des valeurs à partir de la sortie du modèle lié](linked-templates.md#get-values-from-linked-template).

## <a name="solution-2---reduce-name-size"></a>Solution 2 - Réduire la taille du nom

Essayez de raccourcir la longueur des noms que vous utilisez pour les [paramètres](template-parameters.md), [variables](template-variables.md) et [sorties](template-outputs.md). Lorsque ces valeurs sont répétées à l’aide de boucles de copie, un grand nom est multiplié plusieurs fois.

## <a name="solution-3---use-serial-copy"></a>Solution 3 - Utiliser la copie en série

Modifiez votre boucle de copie de [traitement parallèle à traitement en série](copy-resources.md#serial-or-parallel). Utilisez cette option uniquement lorsque vous pensez que l’erreur provient du déploiement d’un grand nombre de ressources à l’aide de la copie. Cette modification peut augmenter considérablement votre temps de déploiement, car les ressources ne sont pas déployées en parallèle.
