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
ms.openlocfilehash: f411504b0f4b7872e92a64c57fecbde863f532c6
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52269999"
---
Vous allez appliquer des étiquettes à vos ressources Azure en fournissant des métadonnées pour les organiser de façon logique par catégories. Chaque étiquette se compose d’une paire nom-valeur. Par exemple, vous pouvez appliquer le nom « Environnement » et la valeur « Production » à toutes les ressources en production.

Une fois que vous avez appliqué une étiquette, vous pouvez utiliser son nom et sa valeur pour récupérer toutes les ressources de votre abonnement. Les étiquettes vous permettent de récupérer les ressources associées de groupes de ressources différents. Cette approche est utile lorsque vous devez organiser les ressources à des fins de facturation ou de gestion.

Votre taxonomie doit prendre en compte une stratégie d’étiquetage des métadonnées en libre-service en plus d’une stratégie d’étiquetage automatique pour réduire la charge des utilisateurs et augmenter la précision.

Les limites suivantes s’appliquent aux étiquettes :

* Les types de ressources ne prennent pas tous en charge les étiquettes. Pour déterminer si vous pouvez appliquer une étiquette à un type de ressource, consultez [Prise en charge des étiquettes pour les ressources Azure](../articles/azure-resource-manager/tag-support.md).
* Chaque ressource ou groupe de ressources peut inclure un maximum de 15 paires nom/valeur d’étiquette. Cette limitation s’applique uniquement aux étiquettes directement appliquées au groupe de ressources ou à la ressource. Un groupe de ressources peut contenir de nombreuses ressources qui ont chacune 15 paires nom/valeur d’étiquette. Si vous devez associer plus de 15 valeurs à une ressource, utilisez une chaîne JSON pour la valeur d’étiquette. La chaîne JSON peut contenir plusieurs valeurs appliquées à un seul nom d’étiquette. Cet article présente un exemple d’affectation d’une chaîne JSON à l’étiquette.
* Le nom d’une étiquette est limité à 512 caractères, et la valeur de l’étiquette à 256 caractères. Pour les comptes de stockage, le nom de l’étiquette est limité à 128 caractères, et la valeur d’étiquette à 256 caractères.
* Les machines virtuelles sont limitées à un total de 2 048 caractères pour tous les noms et les valeurs d’étiquettes.
* Les ressources d’un groupe de ressources n’héritent pas des étiquettes appliquées à ce groupe de ressources.
* Les étiquettes ne peuvent pas être appliquées à des ressources classiques comme les services cloud.
* Les noms d’étiquette ne peuvent pas contenir ces caractères : `<`, `>`, `%`, `&`, `\`, `?`, `/`
