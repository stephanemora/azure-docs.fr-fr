---
title: Guide pratique pour mettre à jour une affectation Azure Blueprint existante
description: Découvrez le mécanisme de mise à jour d’une affectation existante dans les blueprints Azure.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: ecac0fb21a6691874d5e8db49eadd7114d41845f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956198"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Comment mettre à jour une affectation de blueprint existante

Quand un blueprint est affecté, l’affectation peut être mis à jour. Plusieurs raisons peuvent motiver la mise à jour d’une affectation existante, notamment quand il s’agit de :

- Ajouter ou supprimer un [verrouillage de ressource](../concepts/resource-locking.md)
- Modifier la valeur de [paramètres dynamiques](../concepts/parameters.md#dynamic-parameters)
- Mettre à niveau l’affectation vers une version **publiée** plus récente du blueprint

## <a name="updating-assignments"></a>Mise à jour des affectations

1. Lancez le service Azure Blueprint dans le portail Azure. Pour cela, cliquez sur **Tous les services**, puis recherchez et sélectionnez **Stratégie** dans le volet gauche. Dans la page **Stratégie**, cliquez sur **Blueprints**.

1. Sélectionnez **Blueprints affectés** dans la page de gauche.

1. Dans la liste des blueprints, cliquez sur l’affectation de blueprint, puis sur le bouton **Mettre à jour l’affectation** OU cliquez avec le bouton droit sur l’affectation de blueprint, puis sélectionnez **Mettre à jour l’affectation**.

   ![Mettre à jour l’affectation](../media/update-existing-assignments/update-assignment.png)

1. La page **Affecter le blueprint** se charge préremplie avec toutes les valeurs de l’affectation d’origine. Vous pouvez modifier la **version de définition du blueprint**, l’état **Verrouiller l’affectation**, ainsi que tous les paramètres dynamiques qui existent dans la définition du blueprint. Cliquez sur **Affecter** une fois que vous terminé les modifications.

1. Dans la page de détails de l’affectation mise à jour, examinez le nouvel état. Dans cet exemple, nous avons ajouté **Verrouillage** à l’affectation.

   ![Affectation mise à jour - verrouillée](../media/update-existing-assignments/updated-assignment.png)

1. Examinez les détails des autres **Opérations d’affectation** via la liste déroulante. Le tableau **Ressources gérées** est mis à jour en fonction de l’opération d’affectation sélectionnée.

   ![Opérations d’affectation](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Règles de mise à jour des affectations

Le déploiement des affectations mises à jour obéit à quelques règles importantes. Ces règles déterminent ce qu’il arrive à une ressource existante en fonction de la modification demandée et du type de la ressource d’artefact déployée ou mise à jour.

- Affectations de rôles
  - Si le rôle ou son destinataire (utilisateur, groupe ou application) change, une nouvelle affectation de rôle est créée. L’affectation de rôle déployée précédemment est maintenue en place.
- Affectations de stratégies
  - Si les paramètres de l’affectation de stratégie sont modifiés, l’affectation existante est mise à jour.
  - Si la définition de l’affectation de stratégie est modifiée, une nouvelle affectation de stratégie est créée. L’affectation de stratégie déployée précédemment est maintenue en place.
  - Si l’artefact d’affectation de stratégie est supprimé du blueprint, l’affectation de stratégie déployée précédemment est maintenue en place.
- Modèles Microsoft Azure Resource Manager
  - Le modèle est traité via Resource Manager en tant que **PUT**. Sachant que chaque type de ressource gère cela différemment, consultez la documentation de chaque ressource incluse pour déterminer l’impact de cette action quand elle est exécutée par des blueprints.

## <a name="possible-errors-on-updating-assignments"></a>Erreurs possibles pendant la mise à jour d’affectations

Pendant la mise à jour d’affectations, il est possible d’apporter des modifications qui sont rompues au moment de l’exécution. Cela peut se produire par exemple quand l’emplacement d’un groupe de ressources est modifié après avoir déjà été déployé. Il est possible d’apporter n’importe quelle modification prise en charge par [Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md), mais celles susceptibles d’entraîner une erreur via Azure Resource Manager entraîneront aussi l’échec de l’affectation.

Il n’existe aucune limite quant au nombre de fois qu’une affectation peut être mise à jour. Par conséquent, si une erreur se produit, que ce soit en raison d’un paramètre incorrect, d’un objet déjà existant ou d’une modification non autorisés par Azure Resource Manager, déterminez la cause de l’erreur et apportez une autre mise à jour à l’affectation.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [cycle de vie des blueprints](../concepts/lifecycle.md)
- Apprendre à utiliser les [paramètres statiques et dynamiques](../concepts/parameters.md)
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../concepts/sequencing-order.md)
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../concepts/resource-locking.md)
- Résoudre les problèmes qui se produisent durant l’affectation d’un blueprint avec la [résolution des problèmes d’ordre général](../troubleshoot/general.md)
