---
title: Comment mettre à jour d’une attribution existante à partir du portail
description: En savoir plus sur le mécanisme de mise à jour d’une attribution existante à partir du portail dans les plans d’Azure.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 78b2f98b61306c22eaba59b9be7259b3a24d7bbe
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58805752"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Comment mettre à jour une affectation de blueprint existante

Quand un blueprint est affecté, l’affectation peut être mis à jour. Plusieurs raisons peuvent motiver la mise à jour d’une affectation existante, notamment quand il s’agit de :

- Ajouter ou supprimer un [verrouillage de ressource](../concepts/resource-locking.md)
- Modifier la valeur de [paramètres dynamiques](../concepts/parameters.md#dynamic-parameters)
- Mettre à niveau l’affectation vers une version **publiée** plus récente du blueprint

## <a name="updating-assignments"></a>Mise à jour des affectations

1. Sélectionnez **Tous les services** dans le volet gauche. Recherchez et sélectionnez **Blueprints**.

1. Sélectionnez **Blueprints affectés** dans la page de gauche.

1. Dans la liste des blueprints, cliquez sur l’affectation de blueprint. Puis cliquez sur le **mettre à jour attribution** bouton ou l’affectation de plan d’avec le bouton droit et sélectionnez **mettre à jour attribution**.

   ![Mettre à jour d’une affectation de plan existant](../media/update-existing-assignments/update-assignment.png)

1. Le **Assign blueprint** page chargera automatiquement préremplie avec toutes les valeurs à partir de l’attribution d’origine. Vous pouvez modifier la **version de définition du blueprint**, l’état **Verrouiller l’affectation**, ainsi que tous les paramètres dynamiques qui existent dans la définition du blueprint. Cliquez sur **Affecter** une fois que vous terminé les modifications.

1. Dans la page de détails de l’affectation mise à jour, examinez le nouvel état. Dans cet exemple, nous avons ajouté **Verrouillage** à l’affectation.

   ![Mise à jour d’une affectation de plan existant - mode de verrou a été remplacée](../media/update-existing-assignments/updated-assignment.png)

1. Explorer les détails sur les autres **d’opérations d’assignation** à l’aide de la liste déroulante. La table de **les ressources managées** mises à jour par l’opération d’assignation sélectionné.

   ![Opérations d’assignation d’une affectation de plan](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Règles de mise à jour des affectations

Le déploiement des affectations mises à jour obéit à quelques règles importantes. Ces règles déterminent les effets sur les ressources déjà déployées. La modification demandée et le type de la ressource d’artefact déployée ou mise à jour déterminent les actions entreprises.

- Affectations de rôles
  - Si le rôle ou son destinataire (utilisateur, groupe ou application) change, une nouvelle affectation de rôle est créée. Les affectations de rôles déployées précédemment sont maintenues en place.
- Affectations de stratégies
  - Si les paramètres de l’affectation de stratégie sont modifiés, l’affectation existante est mise à jour.
  - Si la définition de l’affectation de stratégie est modifiée, une autre affectation de stratégie est créée. Les affectations de stratégies déployées précédemment sont maintenues en place.
  - Si l’artefact d’affectation de stratégie est supprimé du blueprint, les affectations de stratégies déployées sont maintenues en place.
- Modèles Microsoft Azure Resource Manager
  - Le modèle est traité via Resource Manager en tant que **PUT**. Sachant que chaque type de ressource gère cette action différemment, consultez la documentation de chaque ressource incluse pour déterminer l’impact de cette action quand elle est exécutée par des blueprints.

## <a name="possible-errors-on-updating-assignments"></a>Erreurs possibles pendant la mise à jour d’affectations

Pendant la mise à jour d’affectations, il est possible d’apporter des modifications qui sont rompues au moment de l’exécution. Cela peut se produire par exemple quand l’emplacement d’un groupe de ressources est modifié après avoir déjà été déployé. Il est possible d’apporter n’importe quelle modification prise en charge par [Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md), mais celles susceptibles d’entraîner une erreur via Azure Resource Manager entraîneront aussi l’échec de l’affectation.

Il n’existe aucune limite quant au nombre de fois qu’une affectation peut être mise à jour. Si une erreur se produit, déterminez la cause et apportez une autre mise à jour à l’affectation.  Exemples de scénarios d’erreur :

- Paramètre incorrect
- Objet déjà existant
- Modification non prise en charge par Azure Resource Manager

## <a name="next-steps"></a>Étapes suivantes

- Découvrir le [cycle de vie d’un blueprint](../concepts/lifecycle.md).
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../concepts/resource-locking.md).
- Résoudre les problèmes durant l’affectation d’un blueprint en suivant les étapes de [dépannage général](../troubleshoot/general.md).