---
title: Créer et modifier des plans pour une offre de module IoT Edge sur la Place de marché Azure
description: Créez et modifiez des plans pour une offre de module IoT Edge sur la Place de marché Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: aarathin
ms.author: aarathin
ms.date: 05/21/2021
ms.openlocfilehash: 65284852a0e52c79339e31a54b829d63bb78adbf
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111955630"
---
# <a name="create-and-edit-plans-for-an-iot-edge-module-offer"></a>Créer et modifier des plans pour une offre de module IoT Edge

Cette page de présentation vous permet de créer différentes options de plan dans une même offre. Les plans (auparavant appelés « SKU ») peuvent varier selon l’endroit où ils sont disponibles (Azure Global ou Azure Government), et l’image qu’ils référencent. Votre offre doit contenir au moins un plan.

Vous pouvez créer jusqu’à 100 plans pour chaque offre : jusqu’à 45 peuvent être privés. En savoir plus sur les offres privées dans les [offres du marketplace commercial de Microsoft](private-offers.md).

Une fois votre plan créé, la page **Présentation du plan** affiche les éléments suivants :

- Noms des plans
- Modèle de tarification
- Régions Azure (globales ou gouvernementales)
- État actuel de la publication
- Toutes les actions disponibles

Les actions disponibles pour un plan varient selon l’état actuel de celui-ci. Notamment :

- **Supprimer le brouillon** : si l’état du plan est Brouillon.
- **Arrêter la vente du plan** : si l’état du plan est « publié en ligne ».

## <a name="edit-a-plan"></a>Modifier un plan

Sélectionnez un **nom** de plan pour modifier les détails le concernant.

## <a name="create-a-plan"></a>Créer un plan

Pour configurer un nouveau plan, sélectionnez **+ Créer un plan**.

Entrez un **ID de plan** unique pour chaque plan. Cet ID sera visible par les clients dans l’adresse web du produit. Utilisez uniquement des lettres minuscules et des chiffres, des tirets ou des traits de soulignement, dans la limite de 50 caractères. Vous ne pouvez pas modifier l’ID de plan après avoir sélectionné **Créer**.

Entrez un **nom de plan**. Les clients voient ce nom quand ils décident du plan à sélectionner dans votre offre. Chaque plan de l’offre doit avoir un nom unique. Par exemple, vous pouvez utiliser le nom d’offre **Windows Server** avec les plans **Windows Server 2016** et **Windows Server 2019**.

Sélectionnez **Créer** et continuez ci-dessous.

## <a name="next-steps"></a>Étapes suivantes

- [+ Créer un plan](iot-edge-plan-setup.md), ou
- Quitter la configuration du plan et poursuivre avec [Co-vendre avec Microsoft](./co-sell-overview.md), ou
- [Passer en revue et publier votre offre](review-publish-offer.md)