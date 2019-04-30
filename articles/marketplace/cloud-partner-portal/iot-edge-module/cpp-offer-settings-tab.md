---
title: Paramètres d’offre pour un module Azure IoT Edge | Microsoft Docs
description: Configurez les paramètres de l’offre pour un module IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 2a2ec41d7e2f21dc1550cd3c56009204842e1641
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60912080"
---
# <a name="iot-edge-module-offer-settings-tab"></a>Onglet Paramètres d’offre de module IoT Edge

La page **Modules IoT Edge > Nouvelle offre** s’ouvre avec la cible sur l’onglet **Paramètres de l’offre**. 

![Page Nouvelle offre pour les modules IoT Edge](./media/iot-edge-module-offer-settings-tab.png)

## <a name="offer-identity-settings"></a>Paramètres d’identité de l’offre

Sous **Identité de l’offre**, vous devez fournir des informations pour les champs décrits dans le tableau suivant. Si un astérisque (*) se trouve en regard du nom du champ, cela signifie que ce champ est obligatoire. 

|  **Champ**       |     **Description**                                                          |
|  ---------       |     ---------------                                                          |
| **ID de l’offre**       | Un identificateur unique (avec un profil d’éditeur) pour l’offre. Cet identificateur est visible dans les URL des produits et sur les rapports Insight. Sa longueur maximale est de 50 caractères (caractères alphanumériques minuscules et tirets (-) ). L’identificateur ne peut pas se terminer par un tiret. **Remarque :** ce champ ne peut pas être modifié après la mise en ligne d’une offre. <br> Par exemple, si Contoso publie une offre avec l’ID d’offre **sample-iot-edge-module**, il est présent dans l’URL de la Place de marché Microsoft Azure `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-iot-edge-module?tab=Overview`. |
| **Publisher**     | L’identificateur unique de votre organisation dans la Place de marché Microsoft Azure. Votre ID d’éditeur doit être associé à toutes vos offres. Cette valeur ne peut pas être modifiée après l’enregistrement de l’offre. |
| **Name**          | Nom d’affichage de votre offre. Ce nom s’affiche dans la Place de marché Azure et dans le Portail Cloud Partner. Il ne peut pas comprendre plus de 50 caractères. Nous vous recommandons d’utiliser un nom de marque reconnaissable pour votre produit. N’incluez pas le nom de votre organisation, sauf s’il s’agit du nom sous lequel votre produit est commercialisé. Si vous commercialisez cette offre dans d’autres sites web et publications, vérifiez que le nom est exactement le même dans toutes les publications. |
|  |  |

Sélectionnez **Enregistrer** pour enregistrer vos paramètres de l’offre.

## <a name="next-steps"></a>Étapes suivantes

Utilisez l’onglet [Références SKU](./cpp-skus-tab.md) pour configurer les références SKU pour votre offre.
