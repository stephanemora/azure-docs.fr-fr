---
title: Comprendre le verrouillage de ressources dans les blueprints Azure
description: Découvrez les options de verrouillage permettant de protéger les ressources au moment d’affecter un blueprint.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 718c23b806da5058c042b961077e51ba0d4b6245
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973250"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Comprendre le verrouillage de ressources dans les blueprints Azure

La création d’environnements cohérents à l’échelle n’est vraiment utile que s’il existe un mécanisme pour garantir la cohérence dans le temps. Cet article explique le fonctionnement du verrouillage de ressources dans les blueprints Azure.

## <a name="locking-modes-and-states"></a>Modes et états de verrouillage

Le mode de verrouillage s’applique à l’affectation de blueprint et propose seulement deux options : **Aucun** ou **Toutes les ressources**. Configuré pendant l’affectation d’un blueprint, ce paramètre ne peut plus être modifié une fois que l’affectation a été appliquée avec succès à l’abonnement.

Les ressources créées par des définitions d’artefacts dans le blueprint affecté à l’abonnement présentent trois états possibles : **Pas verrouillé**, **Lecture seule** ou **Modification/suppression impossible**. Les artefacts peuvent présenter l’état **Pas verrouillé**, quel que soit leur type. En revanche, les artefacts de groupes sans ressources sont à l’état **Lecture seule** et les groupes de ressources sont à l’état **Modification/suppression impossible**. Il s’agit d’une distinction importante dans la façon dont ces ressources sont gérées.

L’état **Lecture seule** est exactement tel qu’il est défini : la ressource ne peut pas être modifiée de quelque manière que ce soit (aucune modification possible et suppression impossible). L’état **Modification/suppression impossible** est plus nuancé compte tenu de la nature de « conteneur » des groupes de ressources. L’objet groupe de ressources est en lecture seule, mais il est possible de créer, mettre à jour et supprimer des ressources dans le groupe de ressources (du moment qu’elles ne font pas partie d’une affectation de blueprint avec l’état de verrouillage **Lecture seule**).

## <a name="overriding-locking-states"></a>Neutralisation des états de verrouillage

Même si une personne disposant d’un [contrôle d’accès en fonction du rôle](../../../role-based-access-control/overview.md) (RBAC) approprié sur l’abonnement, tel que le rôle « Propriétaire », peut généralement modifier ou supprimer n’importe quelle ressource, cela n’est pas le cas lorsque les blueprints appliquent un verrouillage dans une affectation déployée. Si l’affectation a été définie avec l’option **Verrouiller**, même le propriétaire de l’abonnement ne peut pas modifier les ressources incluses.

La cohérence du blueprint défini est ainsi préservée et l’environnement à partir duquel il a été créé est protégé contre toute modification ou suppression accidentelle ou par programmation.

## <a name="removing-locking-states"></a>Suppression des états de verrouillage

S’il devient nécessaire de supprimer les ressources créées par une affectation, le seul moyen de les supprimer est de supprimer d’abord l’affectation. Une fois l’affectation supprimée, les verrous créés par les blueprints sont supprimés. Cependant, la ressource est conservée et doit être supprimée selon des procédés normaux par une personne disposant des autorisations appropriées.

## <a name="how-blueprint-locks-work"></a>Fonctionnement des verrous de blueprint

Un rôle RBAC `denyAssignments` est appliqué aux ressources d’artefacts pendant l’affectation d’un blueprint si l’option **Verrouiller** a été sélectionnée pour l’affectation. Le rôle est ajouté par l’identité managée de l’affectation du blueprint et il ne peut être supprimé des ressources d’artefacts que par cette même identité managée. Cela a pour effet d’appliquer le mécanisme de verrouillage et d’empêcher les tentatives de suppression du verrou du blueprint en dehors de Blueprints. Le rôle et le verrou ne peuvent être supprimés qu’en supprimant l’affectation du blueprint, ce que seules les personnes disposant des droits appropriés peuvent effectuer.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [cycle de vie des blueprints](lifecycle.md)
- Apprendre à utiliser les [paramètres statiques et dynamiques](parameters.md)
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](sequencing-order.md)
- Découvrir comment [mettre à jour des affectations existantes](../how-to/update-existing-assignments.md)
- Résoudre les problèmes qui se produisent durant l’affectation d’un blueprint avec la [résolution des problèmes d’ordre général](../troubleshoot/general.md)