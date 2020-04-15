---
title: Paramètres de l’offre d’un module Azure IoT Edge | Place de marché Azure
description: Configurez les paramètres de l’offre pour un module IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 7a35277a61217dab2982d6436903cd09abfe8c7d
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984961"
---
# <a name="iot-edge-module-offer-settings-tab"></a>Onglet Paramètres d’offre de module IoT Edge

>[!Important]
>À partir du 13 avril 2020, nous commencerons à déplacer la gestion de vos offres de module IoT Edge vers l’Espace partenaires. Après la migration, vous créerez et gérerez vos offres dans l’Espace partenaires. Suivez les instructions fournies dans [Créer une offre de module Azure IoT Edge](https://aka.ms/AzureCreateIoT) pour gérer vos offres migrées.

La page **Modules IoT Edge > Nouvelle offre** s’ouvre avec la cible sur l’onglet **Paramètres de l’offre**. 

![Page Nouvelle offre pour les modules IoT Edge](./media/iot-edge-module-offer-settings-tab.png)


## <a name="offer-identity-settings"></a>Paramètres d’identité de l’offre

Sous **Identité de l’offre**, vous devez fournir des informations pour les champs décrits dans le tableau suivant. Si un astérisque (*) se trouve en regard du nom du champ, cela signifie que ce champ est obligatoire. 

|  **Champ**       |     **Description**                                                          |
|  ---------       |     ---------------                                                          |
| **ID de l’offre\***       | Un identificateur unique (avec un profil d’éditeur) pour l’offre. Cet identificateur est visible dans les URL des produits et sur les rapports Insight. Sa longueur maximale est de 50 caractères (caractères alphanumériques minuscules et tirets (-) ). L’identificateur ne peut pas se terminer par un tiret. **Remarque :** ce champ ne peut pas être modifié après la mise en ligne d’une offre. <br> Par exemple, si Contoso publie une offre avec l’ID d’offre **sample-iot-edge-module**, il est présent dans l’URL de la Place de marché Microsoft Azure `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-iot-edge-module?tab=Overview`. |
| **Éditeur\***     | L’identificateur unique de votre organisation dans la Place de marché Microsoft Azure. Votre ID d’éditeur doit être associé à toutes vos offres. Cette valeur ne peut pas être modifiée après l’enregistrement de l’offre. |
| **Nom\***          | Nom d’affichage de votre offre. Ce nom s’affiche dans la Place de marché Azure et dans le Portail Cloud Partner. Il ne peut pas comprendre plus de 50 caractères. Nous vous recommandons d’utiliser un nom de marque reconnaissable pour votre produit. N’indiquez pas le nom de votre organisation, sauf s’il s’agit du nom sous lequel votre produit est commercialisé. Si vous commercialisez cette offre dans d’autres sites web et publications, vérifiez que le nom est exactement le même dans toutes les publications. |
|  |  |


Sélectionnez **Enregistrer** pour enregistrer vos paramètres de l’offre.


## <a name="next-steps"></a>Étapes suivantes

Utilisez l’onglet [Références SKU](./cpp-skus-tab.md) pour configurer les références SKU pour votre offre.
