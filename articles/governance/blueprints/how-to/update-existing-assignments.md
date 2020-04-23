---
title: Mettre à jour une affectation à partir du portail
description: Découvrez le mécanisme de mise à jour d’une attribution de blueprint existante dans Azure Blueprints à partir du portail Azure.
ms.date: 04/15/2020
ms.topic: how-to
ms.openlocfilehash: a00a8bcc10b37af576777e3816a794225a3832f7
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381788"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Comment mettre à jour une affectation de blueprint existante

Quand un blueprint est affecté, l’affectation peut être mise à jour. Plusieurs raisons peuvent motiver la mise à jour d’une affectation existante, notamment quand il s’agit de :

- Ajouter ou supprimer un [verrouillage de ressource](../concepts/resource-locking.md)
- Modifier la valeur de [paramètres dynamiques](../concepts/parameters.md#dynamic-parameters)
- Mettre à niveau l’affectation vers une version **publiée** plus récente du blueprint

## <a name="updating-assignments"></a>Mise à jour des affectations

1. Sélectionnez **Tous les services** dans le volet gauche. Recherchez et sélectionnez **Blueprints**.

1. Sélectionnez **Blueprints affectés** dans la page de gauche.

1. Dans la liste des blueprints, cliquez sur l’affectation de blueprint. Cliquez ensuite sur le bouton **Mettre à jour l’affectation** OU cliquez avec le bouton droit sur l’affectation de blueprint, puis sélectionnez **Mettre à jour l’affectation**.

   :::image type="content" source="../media/update-existing-assignments/update-assignment.png" alt-text="Mettre à jour une affectation de blueprint existante" border="false":::

1. La page **Affecter le blueprint** se charge préremplie avec toutes les valeurs de l’affectation d’origine.
   Vous pouvez modifier la **version de définition du blueprint**, l’état **Verrouiller l’affectation**, ainsi que tous les paramètres dynamiques qui existent dans la définition du blueprint. Cliquez sur **Affecter** une fois que vous terminé les modifications.

1. Dans la page de détails de l’affectation mise à jour, examinez le nouvel état. Dans cet exemple, nous avons ajouté **Verrouillage** à l’affectation.

   :::image type="content" source="../media/update-existing-assignments/updated-assignment.png" alt-text="Affectation de blueprint Mise à jour – Mode de verrouillage modifié" border="false":::

1. Explorez les détails des autres **Opérations d’affectation** dans la liste déroulante. Le tableau **Ressources gérées** s’actualise en fonction de l’opération d’affectation sélectionnée.

   :::image type="content" source="../media/update-existing-assignments/assignment-operations.png" alt-text="Opérations d’affectation d’une affectation de blueprint" border="false":::

## <a name="rules-for-updating-assignments"></a>Règles de mise à jour des affectations

Le déploiement des affectations mises à jour obéit à quelques règles importantes. Ces règles déterminent les effets sur les ressources déjà déployées. La modification demandée et le type de la ressource d’artefact déployée ou mise à jour déterminent les actions entreprises.

- Affectations de rôles
  - Si le rôle ou son destinataire (utilisateur, groupe ou application) change, une nouvelle affectation de rôle est créée. Les affectations de rôles déployées précédemment sont maintenues en place.
- Affectations de stratégies
  - Si les paramètres de l’affectation de stratégie sont modifiés, l’affectation existante est mise à jour.
  - Si la définition de l’affectation de stratégie est modifiée, une autre affectation de stratégie est créée.
    Les affectations de stratégies déployées précédemment sont maintenues en place.
  - Si l’artefact d’affectation de stratégie est supprimé du blueprint, les affectations de stratégies déployées sont maintenues en place.
- Modèles Microsoft Azure Resource Manager
  - Le modèle est traité via Resource Manager en tant que **PUT**. Sachant que chaque type de ressource gère cette action différemment, consultez la documentation de chaque ressource incluse pour déterminer l’impact de cette action quand elle est exécutée par des blueprints.

## <a name="possible-errors-on-updating-assignments"></a>Erreurs possibles pendant la mise à jour d’affectations

Pendant la mise à jour d’affectations, il est possible d’apporter des modifications qui sont rompues au moment de l’exécution. Cela peut se produire par exemple quand l’emplacement d’un groupe de ressources est modifié après avoir déjà été déployé. Il est possible d’apporter n’importe quelle modification prise en charge par [Azure Resource Manager](../../../azure-resource-manager/management/overview.md), mais celles susceptibles d’entraîner une erreur via Azure Resource Manager entraîneront aussi l’échec de l’affectation.

Il n’existe aucune limite quant au nombre de fois qu’une affectation peut être mise à jour. Si une erreur se produit, déterminez la cause et apportez une autre mise à jour à l’affectation.  Exemples de scénarios d’erreur :

- Paramètre incorrect
- Objet déjà existant
- Modification non prise en charge par Azure Resource Manager

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [cycle de vie des blueprints](../concepts/lifecycle.md)
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../concepts/resource-locking.md).
- Résoudre les problèmes durant l’affectation d’un blueprint en suivant les étapes de [dépannage général](../troubleshoot/general.md).