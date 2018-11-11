---
title: Comprendre le verrouillage de ressources dans les blueprints Azure
description: Découvrez les options de verrouillage permettant de protéger les ressources au moment d’affecter un blueprint.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 4e71797837927fe5f5233bcf88d35fef98f504e9
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139440"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Comprendre le verrouillage de ressources dans les blueprints Azure

La création d’environnements cohérents à l’échelle n’est vraiment utile que s’il existe un mécanisme pour gérer cette cohérence. Cet article explique le fonctionnement du verrouillage de ressources dans les blueprints Azure.

## <a name="locking-modes-and-states"></a>Modes et états de verrouillage

Le mode de verrouillage s’applique à l’affectation de blueprint et propose seulement deux options : **Aucun** ou **Toutes les ressources**. Le mode de verrouillage est configuré pendant l’affectation d’un blueprint et ne peut plus être modifié une fois que l’affectation a été appliquée avec succès à l’abonnement.

Les ressources créées par des artefacts dans une affectation de blueprint présentent trois états possibles : **Pas verrouillé**, **Lecture seule** ou **Modification/suppression impossible**. Chaque artefact peut présenter l’état **Pas verrouillé**. En revanche, les artefacts de groupes sans ressources présentent l’état **Lecture seule** et les groupes de ressources présentent l’état **Modification/suppression impossible**. Il s’agit d’une différence importante dans la façon dont ces ressources sont gérées.

L’état **Lecture seule** est exactement tel qu’il est défini : la ressource ne peut pas être modifiée de quelque manière que ce soit (aucune modification possible et suppression impossible). L’état **Modification/suppression impossible** est plus nuancé compte tenu de la nature de « conteneur » des groupes de ressources. L’objet groupe de ressources est en lecture seule, mais il est possible d’apporter des modifications à des ressources non verrouillées dans le groupe de ressources.

## <a name="overriding-locking-states"></a>Neutralisation des états de verrouillage

Il est généralement possible pour une personne disposant d’un [contrôle d’accès en fonction du rôle](../../../role-based-access-control/overview.md) (RBAC) approprié sur l’abonnement, tel que le rôle « Propriétaire », d’être autorisée à modifier ou supprimer n’importe quelle ressource. Cet accès n’est pas possible quand les blueprints appliquent un verrouillage dans le cadre d’une affectation déployée. Si l’affectation a été définie avec l’option **Verrouiller**, même le propriétaire de l’abonnement ne peut pas modifier les ressources incluses.

Cette mesure de sécurité assure la cohérence du blueprint défini et protège l’environnement pour la création duquel il a été conçu contre toute modification ou suppression accidentelle ou programmatique.

## <a name="removing-locking-states"></a>Suppression des états de verrouillage

S’il devient nécessaire de supprimer les ressources créées par une affectation, le moyen de le faire consiste à supprimer d’abord l’affectation. Une fois l’affectation supprimée, les verrous créés par les blueprints sont supprimés. Cependant, la ressource est conservée et doit être supprimée selon des procédés normaux.

## <a name="how-blueprint-locks-work"></a>Fonctionnement des verrous de blueprint

Un rôle RBAC `denyAssignments` est appliqué aux ressources d’artefacts pendant l’affectation d’un blueprint si l’option **Verrouiller** a été sélectionnée pour l’affectation. Le rôle est ajouté par l’identité managée de l’affectation du blueprint et il ne peut être supprimé des ressources d’artefacts que par cette même identité managée. Cette mesure de sécurité a pour effet d’appliquer le mécanisme de verrouillage et d’empêcher la suppression du verrou du blueprint en dehors de blueprints. Le rôle et le verrou ne peuvent être supprimés qu’en supprimant l’affectation du blueprint, ce que seules les personnes disposant des droits appropriés peuvent effectuer.

> [!IMPORTANT]
> Azure Resource Manager met en cache les détails des affectations de rôles pendant 30 minutes au maximum. Par conséquent, `denyAssignments` sur les ressources de blueprint peut ne pas être immédiatement effectif. Pendant cette période de temps, il peut être possible de supprimer une ressource destinée à être protégée par des verrous de blueprint.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [cycle de vie des blueprints](lifecycle.md)
- Comprendre comment utiliser les [paramètres statiques et dynamiques](parameters.md)
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](sequencing-order.md)
- Découvrir comment [mettre à jour des affectations existantes](../how-to/update-existing-assignments.md)
- Résoudre les problèmes durant l’affectation d’un blueprint en suivant les étapes de [dépannage général](../troubleshoot/general.md)