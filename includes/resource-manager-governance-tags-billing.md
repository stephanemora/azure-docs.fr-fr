---
title: Fichier Include
description: Fichier Include
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: e7fcb72cecbfad2de80b844ed5281267d5e4c0c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64732723"
---
Après avoir appliqué des balises aux ressources, vous pouvez afficher les coûts des ressources à l’aide de ces balises. L’analyse des coûts prend un certain temps pour afficher les dernières données d’utilisation. Il est donc possible que vous ne voyiez pas encore les coûts. Lorsqu’ils sont disponibles, vous pouvez afficher les coûts des ressources des différents groupes de ressources de votre abonnement. Pour voir les coûts, les utilisateurs doivent avoir un [accès de niveau d’abonnement aux informations de facturation](../articles/billing/billing-manage-access.md).

Pour afficher les coûts par balise dans le portail, sélectionnez votre abonnement, puis sélectionnez **Analyse du coût**.

![Analyse des coûts](./media/resource-manager-governance-tags-billing/select-cost-analysis.png)

Ensuite, filtrez par valeur de balise, puis sélectionnez **Appliquer**.

![Afficher les coûts par balise](./media/resource-manager-governance-tags-billing/view-costs-by-tag.png)

Vous pouvez également utiliser les [API Facturation Azure](../articles/billing/billing-usage-rate-card-overview.md) pour afficher les coûts par programmation.
