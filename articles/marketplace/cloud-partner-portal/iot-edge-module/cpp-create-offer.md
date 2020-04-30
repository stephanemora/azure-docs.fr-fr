---
title: Créer une offre de module Azure IoT Edge | Place de marché Azure
description: Comment publier un nouveau module IoT Edge sur la Place de marché.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d8f4300f45fc626c104055f987d123301ba8f39d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82148062"
---
# <a name="create-a-new-iot-edge-module-offer-with-the-cloud-partner-portal"></a>Créer une nouvelle offre de module IoT Edge sur le Portail Cloud Partner

>[!Important]
>À partir du 13 avril 2020, nous commencerons à déplacer la gestion de vos offres de module IoT Edge vers l’Espace partenaires. Après la migration, vous créerez et gérerez vos offres dans l’Espace partenaires. Suivez les instructions indiquées dans [Créer une offre de module Azure IoT Edge](https://docs.microsoft.com//azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) pour gérer vos offres migrées.

Cet article décrit comment créer et publier une entrée d’offre de module IoT Edge pour la place de marché Azure. Les offres apparaissent sous la forme d’une entité distincte dans la Place de marché Microsoft Azure et sont associées à un ou plusieurs références SKU.  Une offre de module IoT Edge est composée des regroupements de ressources et de services de prise en charge suivants :

|  **Groupe de ressources**   |  **Description**  |
|  ---------------   |  ---------------  |
|    Références (SKU)            |  Plus petite unité d’une offre pouvant être déployée. Plusieurs références (SKU) peuvent être associées à l’offre unique (classe de produit) . Vous pouvez utiliser des références (SKU) pour faire la distinction entre les fonctionnalités prises en charge et les modèles de facturation. |
|  Marketplace       | Contient des ressources et des spécifications marketing, juridique et de gestion des prospects.  <ul><li> Les ressources marketing incluent le nom, la description et les logos de l’offre</li> <li> Les ressources juridiques incluent une politique de confidentialité, des conditions d’utilisation et d’autres documents juridiques</li>  <li> La stratégie de gestion des prospects vous permet de spécifier comment gérer les prospects à partir du portail de l’utilisateur final de la Place de marché Microsoft Azure.</li> </ul> |
| Support            | Contient les informations de contact et de stratégie du support |


## <a name="new-offer-form"></a>Formulaire de nouvelle offre 

Connectez-vous au [portail Cloud Partner](https://cloudpartner.azure.com/), puis sélectionnez **+ Nouvelle offre** dans la barre de menu de gauche. Dans le menu Nouvelle offre, sélectionnez les **modules IoT Edge** pour afficher le formulaire **Nouvelle offre** et démarrer le processus de définition des ressources pour une nouvelle offre de Module IoT Edge. 

![Sélection de l’interface utilisateur de nouvelle offre de module IoT Edge](./media/new-iot-edge-module-offer.png)

## <a name="next-steps"></a>Étapes suivantes

La page **Nouvelle offre** pour le type d’offre de module IoT Edge fournit un ensemble d’onglets et de champs de formulaire que vous allez utiliser pour créer une nouvelle offre. Les articles suivants expliquent comment utiliser l’onglet pour définir les groupes de ressources et les services de prise en charge de votre nouvelle offre de module IoT Edge.

- [Onglet des paramètres de l’offre](./cpp-offer-settings-tab.md)
- [Onglet des références SKU](./cpp-skus-tab.md)
- [Onglet de la Place de marché](./cpp-marketplace-tab.md)
- [Onglet du support](./cpp-support-tab.md)
