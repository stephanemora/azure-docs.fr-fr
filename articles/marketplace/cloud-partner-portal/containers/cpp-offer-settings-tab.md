---
title: Paramètres d’offre pour une image de conteneurs Azure | Microsoft Docs
description: Configurez les paramètres de l’offre pour un conteneur Azure.
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
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 9a669ebad674f938d11c8e372e6f586ca3c67f03
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2018
ms.locfileid: "50980166"
---
# <a name="container-offer-settings-tab"></a>Onglet des paramètres de l’offre de conteneur

La page **Conteneurs > Nouvelle offre** s’ouvre avec la cible sur l’onglet **Paramètres de l’offre**. 

![Identité de l’offre](./media/containers-offer-settings.png)

## <a name="offer-identity-settings"></a>Paramètres d’identité de l’offre

Sous **Identité de l’offre**, vous devez fournir des informations pour les champs décrits dans le tableau suivant. Si un astérisque (*) se trouve en regard du nom du champ, cela signifie que ce champ est obligatoire. 

|  **Champ**       |     **Description**                                                          |
|  ---------       |     ---------------                                                          |
| **ID de l’offre**       | Un identificateur unique (avec un profil d’éditeur) pour l’offre. Cet identificateur est visible dans les URL des produits et sur les rapports Insight. Sa longueur maximale est de 50 caractères (caractères alphanumériques minuscules et tirets (-) ). L’identificateur ne peut pas se terminer par un tiret. **Remarque :** ce champ ne peut pas être modifié après la mise en ligne d’une offre. <br> Par exemple, si Contoso publie une offre avec l’ID d’offre **sample-container**, il est présent dans l’URL de la Place de marché Microsoft Azure `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`. |
| **ID de l’éditeur**     | L’identificateur unique de votre organisation dans la Place de marché Microsoft Azure. Votre ID d’éditeur doit être associé à toutes vos offres. Cette valeur ne peut pas être modifiée après l’enregistrement de l’offre. |
| **Nom**          | Nom d’affichage de votre offre. Ce nom s’affiche dans la Place de marché Azure et dans le Portail Cloud Partner. Il ne peut pas comprendre plus de 50 caractères. Nous vous recommandons d’utiliser un nom de marque reconnaissable pour votre produit. N’incluez pas le nom de votre organisation, sauf s’il s’agit du nom sous lequel votre produit est commercialisé. Si vous commercialisez cette offre dans d’autres sites web et publications, vérifiez que le nom est exactement le même dans toutes les publications. |
|  |  |

Sélectionnez **Enregistrer** pour enregistrer vos paramètres de l’offre.

## <a name="next-steps"></a>Étapes suivantes

Utilisez l’onglet [Références SKU](./cpp-skus-tab.md) pour configurer les références SKU pour votre offre.
