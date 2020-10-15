---
title: Fichier Include
description: Fichier Include
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: dc4281c17b92e1720625764a52a34a94d6f296ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "67176994"
---
Avant de créer des éléments, passons en revue le concept de portée. Azure fournit quatre niveaux de gestion : des groupes d’administration, d’abonnement, des groupes de ressources et des ressources. Les [groupes d’administration](../articles/billing/billing-enterprise-mgmt-group-overview.md) sont en préversion. L’image suivante représente un exemple de ces couches.

![Étendue](./media/resource-manager-governance-scope/scope-levels.png)

Vous appliquez les paramètres de gestion à tous ces niveaux de l’étendue. Le niveau que vous sélectionnez détermine à quel point le paramètre est appliqué. Les niveaux inférieurs héritent des paramètres des niveaux supérieurs. Lorsque vous appliquez un paramètre à l’abonnement, ce paramètre est appliqué à tous les groupes de ressources et les ressources de votre abonnement. Lorsque vous appliquez un paramètre sur le groupe de ressources, ce paramètre est appliqué sur le groupe de ressources et toutes ses ressources. Toutefois, un autre groupe de ressources ne dispose pas de ce paramètre.

En règle générale, il est judicieux d’appliquer les paramètres critiques à des niveaux supérieurs et les exigences spécifiques au projet à des niveaux inférieurs. Par exemple, vous voudrez vous assurer que toutes les ressources de votre organisation sont déployées dans certaines régions. Pour satisfaire cette exigence, appliquez une stratégie à l’abonnement qui spécifie les emplacements autorisés. Lorsque les autres utilisateurs de votre organisation ajouteront de nouveaux groupes de ressources et des ressources, les emplacements autorisés seront automatiquement appliqués. 
