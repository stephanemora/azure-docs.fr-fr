---
title: Fichier Include
description: Fichier Include
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/20/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c426df2293cfb2d8ba4dc02e8fc5519c3d822168
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64868759"
---
Vous allez appliquer des balises à vos ressources Azure en fournissant des métadonnées pour les organiser de façon logique par catégories. Chaque balise se compose d’une paire nom-valeur. Par exemple, vous pouvez appliquer le nom « Environnement » et la valeur « Production » à toutes les ressources en production.

Une fois que vous avez appliqué une balise, vous pouvez utiliser son nom et sa valeur pour récupérer toutes les ressources dans votre abonnement. Les balises vous permettent de récupérer les ressources associées de groupes de ressources différents. Cette approche est utile lorsque vous devez organiser les ressources à des fins de facturation ou de gestion.

Votre taxonomie doit prendre en compte une stratégie de balisage des métadonnées en libre-service en plus d’une stratégie de balisage automatique pour réduire la charge pesant sur les utilisateurs et augmenter la précision.

Les limites suivantes s’appliquent aux balises :

* Les types de ressources ne prennent pas tous en charge les étiquettes. Pour déterminer si vous pouvez appliquer une étiquette à un type de ressource, consultez [Prise en charge des étiquettes pour les ressources Azure](../articles/azure-resource-manager/tag-support.md).
* Chaque ressource ou groupe de ressources peut inclure un maximum de 15 paires nom/valeur de balise. Cette limitation s’applique uniquement aux balises directement appliquées au groupe de ressources ou à la ressource. Un groupe de ressources peut contenir de nombreuses ressources qui ont chacune 15 paires nom/valeur de balise. Si vous devez associer plus de 15 valeurs à une ressource, utilisez une chaîne JSON pour la valeur de balise. La chaîne JSON peut contenir plusieurs valeurs appliquées à un seul nom de balise. Cet article présente un exemple d’affectation d’une chaîne JSON à la balise.
* Le nom de balise est limité à 512 caractères, et la valeur de balise à 256 caractères. Pour les comptes de stockage, le nom de balise est limité à 128 caractères, et la valeur de balise à 256 caractères.
* Machines virtuelles et des machines virtuelles identiques sont limités à un total de 2 048 caractères pour tous les noms de balise et les valeurs.
* Les ressources d’un groupe de ressources n’héritent pas des balises appliquées à ce groupe de ressources.
* Les balises ne peuvent pas être appliquées à des ressources classiques comme les Services cloud.
* Les noms de balise ne peuvent pas contenir ces caractères : `<`, `>`, `%`, `&`, `\`, `?`, `/`
