---
title: Informations techniques pour une offre d'application Power BI - Place de marché Azure | Microsoft Docs
description: Configurez les champs d’informations techniques d'une offre d'application Power BI pour la Place de marché Microsoft AppSource.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: pbutlerm
ms.openlocfilehash: ca77da897eed51c8d832cad7052c2144d6ada562
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806484"
---
# <a name="power-bi-apps-technical-info-tab"></a>Onglet Informations techniques des applications Power BI

Sur le **nouvelle offre** page, utilisez la **informations techniques** onglet pour fournir le programme d’installation de Power BI, l’URL du package et autres informations que vous avez besoin valider la nouvelle offre.  Pour la version initiale, toutes les applications Power BI sont gratuites et sont disponibles en téléchargement à partir de AppSource. Pour cette raison, vous ne pouvez pas définir les unités de stock (SKU) pour ce type d’offre.

![L’onglet informations techniques](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Champs Informations techniques 

Sur le **informations techniques** onglet, renseignez les champs décrits dans le tableau suivant. Un astérisque (*) à la fin d’une étiquette de champ signifie que le champ est obligatoire.

|        Champ          |  Description                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **URL du programme d’installation**     | Power BI génère cette URL lorsque vous publiez l’application et passez en production.  Pour plus d’informations, consultez [publier des applications avec des tableaux de bord et rapports dans Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).  |
|  **Instructions de validation**  |  Si vous le souhaitez, ajoutez des instructions (jusqu'à 3 000 caractères) pour aider l’équipe de validation de Microsoft de configurer, de se connecter et de tester votre application. Inclure les paramètres de configuration courants, des comptes, des paramètres ou d’autres informations qui peuvent être utilisées pour tester l’option de connexion de données. Ces informations sont visibles uniquement par l’équipe de validation, et il est utilisé uniquement à des fins de validation.  |
| **Cette application est-elle créée comme pack de contenu Power BI ?** | Actuellement, ce champ est utilisé uniquement en interne. Laissez le paramètre par défaut **non**. Si vous modifiez le paramètre pour **Oui**, vous pouvez arrêter le processus de publication.  |  
|  |  |


## <a name="next-steps"></a>Étapes suivantes

Sur le [détails de la vitrine](./cpp-storefront-details-tab.md) onglet, fournissez les informations légales et marketing pour votre application.

