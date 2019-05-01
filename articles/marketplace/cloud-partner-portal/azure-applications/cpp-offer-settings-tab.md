---
title: Paramètres de l’application Windows Azure offre | Place de marché Azure
description: Configurez les paramètres d’une offre d’application Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 789b783629b3cc3528eba1883b21051604cf6e14
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942927"
---
# <a name="azure-application-offer-settings-tab"></a>Onglet des paramètres de l’offre d’application Azure

Cet article décrit comment configurer les paramètres d’une offre d’application Azure.

La page **Applications Azure > Nouvelle offre** s’ouvre sur l’onglet **Paramètres de l’offre**. Si un astérisque (*) se trouve en regard du nom du champ, cela signifie que ce champ est obligatoire.

![Formulaire Identité de l’offre](./media/azureapp-offer-settings-tab.png)

## <a name="offer-identity-settings"></a>Paramètres d’identité de l’offre

Sous **Identité de l’offre**, vous devez fournir des informations pour les champs décrits dans le tableau suivant.  

|    Champ         |       Description                                                            |
|  ---------       |     ---------------                                                          |
| **ID de l’offre\***       | Un identificateur unique (avec un profil d’éditeur) pour l’offre. Cet identificateur est visible dans les URL des produits et sur les rapports Insight. Sa longueur maximale est de 50 caractères (caractères alphanumériques minuscules et tirets (-) ). L’identificateur ne peut pas se terminer par un tiret. **Remarque :** ce champ ne peut pas être modifié après la mise en ligne d’une offre. <br> Par exemple, si Contoso publie une offre avec l’ID d’offre **sample-container**, il est présent dans l’URL de la Place de marché Microsoft Azure `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`. |
| **ID de l’éditeur\***     | L’identificateur unique de votre organisation dans la Place de marché Microsoft Azure. Votre ID d’éditeur doit être associé à toutes vos offres. Cette valeur ne peut pas être modifiée après l’enregistrement de l’offre. |
| **Nom\***          | Nom d’affichage de votre offre. Ce nom s’affiche dans la Place de marché Azure et dans le Portail Cloud Partner. Il ne peut pas comprendre plus de 50 caractères. Nous vous recommandons d’utiliser un nom de marque reconnaissable pour votre produit. N’incluez pas le nom de votre organisation, sauf s’il s’agit du nom sous lequel votre produit est commercialisé. Si vous commercialisez cette offre dans d’autres sites web et publications, vérifiez que le nom est exactement le même dans toutes les publications. |
|  |  |

Sélectionnez **Enregistrer** pour enregistrer vos paramètres de l’offre.

## <a name="next-steps"></a>Étapes suivantes

Utilisez l’onglet [Références SKU](./cpp-skus-tab.md) pour configurer les références SKU pour votre offre.