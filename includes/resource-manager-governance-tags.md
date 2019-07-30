---
title: Fichier Include
description: Fichier Include
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 07/11/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 099bca7483100da1a4ee2f8f10057c416ad145b0
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841434"
---
Vous allez appliquer des balises à vos ressources Azure en fournissant des métadonnées pour les organiser de façon logique par catégories. Chaque balise se compose d’une paire nom-valeur. Par exemple, vous pouvez appliquer le nom « Environnement » et la valeur « Production » à toutes les ressources en production.

Une fois que vous avez appliqué une balise, vous pouvez utiliser son nom et sa valeur pour récupérer toutes les ressources dans votre abonnement. Les balises vous permettent de récupérer les ressources associées de groupes de ressources différents. Cette approche est utile lorsque vous devez organiser les ressources à des fins de facturation ou de gestion.

Votre taxonomie doit prendre en compte une stratégie de balisage des métadonnées en libre-service en plus d’une stratégie de balisage automatique pour réduire la charge pesant sur les utilisateurs et augmenter la précision.

Les limites suivantes s’appliquent aux balises :

* Les types de ressources ne prennent pas tous en charge les étiquettes. Pour déterminer si vous pouvez appliquer une étiquette à un type de ressource, consultez [Prise en charge des étiquettes pour les ressources Azure](../articles/azure-resource-manager/tag-support.md).
* Chaque ressource ou groupe de ressources peut inclure un maximum de 50 paires nom/valeur de balise. Actuellement, les comptes de stockage ne prennent en charge que 15 balises, mais cette limite sera augmentée à 50 dans une version ultérieure. Si vous devez appliquer plus de balises que le nombre maximal autorisé, utilisez une chaîne JSON comme valeur de balise. La chaîne JSON peut contenir plusieurs valeurs appliquées à un seul nom de balise. Un groupe de ressources peut contenir de nombreuses ressources qui ont chacune 50 paires nom/valeur de balise.
* Le nom de balise est limité à 512 caractères, et la valeur de balise à 256 caractères. Pour les comptes de stockage, le nom de balise est limité à 128 caractères, et la valeur de balise à 256 caractères.
* Les machines virtuelles généralisées ne prennent pas en charge les balises.
* Les ressources d’un groupe de ressources n’héritent pas des balises appliquées à ce groupe de ressources.
* Les balises ne peuvent pas être appliquées à des ressources classiques comme les Services cloud.
* Les noms de balise ne peuvent pas contenir ces caractères : `<`, `>`, `%`, `&`, `\`, `?`, `/`
