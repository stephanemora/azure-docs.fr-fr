---
title: Paramètres de l’offre d’application Azure SaaS | Microsoft Docs
description: Configurez les paramètres de l’offre d’application SaaS sur la Place de marché Azure.
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
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 8b8810d3dc899a87b99422c093b6901ed9683325
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101130"
---
# <a name="saas-application-offer-settings-tab"></a>Onglet des paramètres de l’offre d’application SaaS

Cet article décrit comment configurer les paramètres de l’offre.

La page **Application SaaS > Nouvelle offre** s’ouvre avec la cible sur l’onglet **Paramètres de l’offre**. 

Utilisez l’onglet Paramètres de l’offre pour configurer l’**identité de l’offre**, comme indiqué dans la capture d’écran suivante. Si un astérisque (*) se trouve en regard du nom du champ, cela signifie que ce champ est obligatoire.

![Onglet des paramètres de l’offre](./media/saas-new-offer.png)

## <a name="offer-identity-settings"></a>Paramètres d’identité de l’offre

Sous Identité de l’offre, vous devez fournir des informations pour les champs décrits dans le tableau suivant. 


|  **Nom du champ**   |  **Description**  |
|  ---------------   |  ---------------  |
|    ID de l’offre  |  Identificateur unique de l’offre au sein d’un profil d’éditeur. Cet ID est visible dans les URL des produits et sur les états de facturation. Il ne peut comprendre que des caractères alphanumériques en minuscules ou des tirets (-). L’ID ne peut pas se terminer par un tiret et ne peut pas contenir plus de 50 caractères. Notez que ce champ est verrouillé une fois l’offre publiée. Par exemple, si l’éditeur Contoso publie une offre avec l’ID d’offre sample-vm, celle-ci apparaît sur la Place de marché Microsoft Azure sous la forme suivante : https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview.                 |
|  ID de l’éditeur    |  L'ID d'éditeur est votre identificateur unique sur la Place de marché. Votre ID d'éditeur doit être associé à toutes vos offres. Une fois l’offre enregistrée, l'ID d'éditeur n'est pas modifiable.                |
|  Nom      |   Ceci est le nom d’affichage de votre offre. Il s'agit du nom qui apparaît sur la Place de marché Microsoft Azure et sur le portail Azure. Il ne peut pas comprendre plus de 50 caractères. Incluez un nom de marque reconnaissable pour votre produit. N'incluez pas ici le nom de votre entreprise, sauf s'il s'agit du nom sous lequel l'offre est commercialisée. Si vous commercialisez cette offre sur votre propre site web, assurez-vous que le nom présente rigoureusement le même aspect que sur votre site.               |

Sélectionnez **Save** (Enregistrer) pour enregistrer votre progression.

## <a name="next-steps"></a>Étapes suivantes

[Onglet des informations techniques](./cpp-technical-info-tab.md)
