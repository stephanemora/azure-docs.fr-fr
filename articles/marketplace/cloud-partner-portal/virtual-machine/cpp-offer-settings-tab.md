---
title: Onglet Paramètres de l’offre de machine virtuelle dans le portail Microsoft Cloud Partner pour la Place de marché Microsoft Azure
description: Décrit l’onglet Paramètres de l’offre utilisé pour créer une offre de machine virtuelle pour la Place de marché Microsoft Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: 94be2e5d3c2c941ab17401a743ea86acbe8b6252
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273798"
---
# <a name="virtual-machine-offer-settings-tab"></a>Onglet Paramètres de l’offre de machine virtuelle

> [!IMPORTANT]
> Depuis le 13 avril 2020, nous avons commencé à déplacer la gestion de vos offres de machines virtuelles Azure vers l’Espace partenaires. Après la migration, vous créerez et gérerez vos offres dans l’Espace partenaires. Suivez les instructions fournies dans [Créer une offre de machine virtuelle Azure](https://aka.ms/CreateAzureVMoffer) pour gérer vos offres migrées.

La page **Nouvelle offre** des machines virtuelles s’ouvre dans le premier onglet nommé **Paramètres de l’offre**.  

![Page Nouvelle offre des machines virtuelles](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>Champs Paramètres de l’offre

Dans l’onglet **Paramètres de l’offre**, vous devez renseigner les champs suivants.  Un astérisque (*) en regard du nom du champ indique que ce champ est obligatoire. 

|  **Champ**       |     **Description**                                                          |
|  ---------       |     ---------------                                                          |
| **ID de l’offre\***   | Un identificateur unique (avec un profil d’éditeur) pour l’offre. Cet identificateur est visible dans les URL de produits, les modèles Azure Resource Manager et les rapports de facturation. Il doit contenir 50 caractères maximum, être uniquement composé de minuscules, de caractères alphanumériques et de tiret, mais ne peut pas se terminer par un tiret. Ce champ ne peut pas être modifié après la mise en ligne d’une offre. <br> Par exemple, si Contoso publie une offre avec l’ID d’offre **sample-vm**, il est présent dans l’URL de la Place de marché Microsoft Azure `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview`. |
| **Éditeur\***  | L’identificateur unique de votre organisation dans la Place de marché Microsoft Azure. Votre ID d’éditeur doit être associé à toutes vos offres. Une fois l’offre enregistrée, cette valeur n’est pas modifiable. |
| **Nom\***       | Nom d’affichage de votre offre. Ce nom s’affiche dans la Place de marché Microsoft Azure et dans le Portail Cloud Partner. Il ne peut pas comprendre plus de 50 caractères. Il est conseillé ici d’inclure un nom de marque reconnaissable pour votre produit. N’indiquez pas le nom de votre organisation ici, sauf s’il s’agit du nom sous lequel il est commercialisé. Si vous commercialisez cette offre dans d’autres sites web et publications, vérifiez que le nom est exactement le même dans toutes les publications. |
|   |   |
 
Cliquez sur **Enregistrer** une fois que vous avez renseigné tous les champs. 


## <a name="next-steps"></a>Étapes suivantes

Dans l’onglet suivant, vous allez ajouter des [références SKU](./cpp-skus-tab.md) à votre offre.
