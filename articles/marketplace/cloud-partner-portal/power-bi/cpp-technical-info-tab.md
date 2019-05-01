---
title: Informations techniques pour une offre d’application Power BI | Place de marché Azure
description: Configurez les champs d’informations techniques d'une offre d'application Power BI pour la Place de marché Microsoft AppSource.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 15f4e2a76724a70c15411dea767cc9bc433e4d4a
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943230"
---
# <a name="power-bi-apps-technical-info-tab"></a>Onglet Informations techniques des applications Power BI

Sur le **nouvelle offre** page, utilisez la **informations techniques** onglet pour fournir le programme d’installation de Power BI, l’URL du package et autres informations que vous avez besoin valider la nouvelle offre.  Pour la version initiale, toutes les applications Power BI sont gratuites et sont disponibles en téléchargement à partir de AppSource. Pour cette raison, vous ne pouvez pas définir les unités de stock (SKU) pour ce type d’offre.

![L’onglet informations techniques](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Champs Informations techniques 

Sur le **informations techniques** onglet, renseignez les champs décrits dans le tableau suivant. Un astérisque (*) à la fin d’une étiquette de champ signifie que le champ est obligatoire.

|        Champ          |  Description                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **URL du programme d’installation\***     | Power BI génère cette URL lorsque vous publiez l’application et passez en production.  Pour plus d’informations, consultez [publier des applications avec des tableaux de bord et rapports dans Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).  |
|  **Instructions de validation**  |  Si vous le souhaitez, ajoutez des instructions (jusqu'à 3 000 caractères) pour aider l’équipe de validation de Microsoft de configurer, de se connecter et de tester votre application. Inclure les paramètres de configuration courants, des comptes, des paramètres ou d’autres informations qui peuvent être utilisées pour tester l’option de connexion de données. Ces informations sont visibles uniquement par l’équipe de validation, et il est utilisé uniquement à des fins de validation.  |
| **Cette application est-elle créée comme pack de contenu Power BI ?** | Actuellement, ce champ est utilisé uniquement en interne. Laissez le paramètre par défaut **non**. Si vous modifiez le paramètre pour **Oui**, vous pouvez arrêter le processus de publication.  |  
|  |  |


## <a name="next-steps"></a>Étapes suivantes

Sur le [détails de la vitrine](./cpp-storefront-details-tab.md) onglet, fournissez les informations légales et marketing pour votre application.

