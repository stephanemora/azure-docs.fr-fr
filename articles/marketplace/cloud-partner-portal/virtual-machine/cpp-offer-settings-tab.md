---
title: Onglet Paramètres de l’offre de machine virtuelle dans le portail Microsoft Cloud Partner pour la Place de marché Microsoft Azure | Microsoft Docs
description: Décrit l’onglet Paramètres de l’offre utilisé pour créer une offre de machine virtuelle pour la Place de marché Microsoft Azure.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 43c830bea57a4c6f3b6c56552dbcacaccc75d54e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844323"
---
# <a name="virtual-machine-offer-settings-tab"></a>Onglet Paramètres de l’offre de machine virtuelle

La page **Nouvelle offre** des machines virtuelles s’ouvre dans le premier onglet nommé **Paramètres de l’offre**.  Un astérisque (*) en regard du nom du champ indique que ce champ est obligatoire. 

![Page Nouvelle offre des machines virtuelles](./media/publishvm_004.png)

Dans l’onglet **Paramètres de l’offre**, vous devez fournir les champs obligatoires suivants.

|  **Champ**       |     **Description**                                                          |
|  ---------       |     ---------------                                                          |
| **ID de l’offre**       | Identificateur unique (avec un profil d’éditeur) pour l’offre. Cet identificateur est visible dans les URL de produits, les modèles Azure Resource Manager et les rapports de facturation. Il doit contenir 50 caractères maximum, être uniquement composé de minuscules, de caractères alphanumériques et de tiret, mais ne peut pas se terminer par un tiret. Ce champ ne peut pas être modifié après la mise en ligne d’une offre. <br> Par exemple, si Contoso publie une offre avec l’ID d’offre **sample-vm**, il est présent dans l’URL de la Place de marché Microsoft Azure `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview`. |
| **Publisher**     | L’identificateur unique de votre organisation dans la Place de marché Microsoft Azure. Votre ID d’éditeur doit être associé à toutes vos offres. Une fois l’offre enregistrée, cette valeur n’est pas modifiable. |
| **Name**          | Nom d’affichage de votre offre. Ce nom s’affiche dans la Place de marché Microsoft Azure et dans le Portail Cloud Partner. Il ne peut pas comprendre plus de 50 caractères. Il est conseillé ici d’inclure un nom de marque reconnaissable pour votre produit. N’incluez pas ici le nom de votre organisation, sauf s’il s’agit du nom sous lequel l’offre est commercialisée. Si vous commercialisez cette offre dans d’autres sites web et publications, vérifiez que le nom est exactement le même dans toutes les publications. |
|  |  |
 
Cliquez sur **Enregistrer** pour enregistrer votre progression. Dans l’onglet suivant, vous allez ajouter des [références SKU](./cpp-skus-tab.md) à votre offre.
