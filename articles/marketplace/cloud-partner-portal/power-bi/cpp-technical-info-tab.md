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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943230"
---
# <a name="power-bi-apps-technical-info-tab"></a>Onglet Informations techniques des applications Power BI

Dans la page **Nouvelle offre**, utilisez l’onglet **Informations techniques** pour fournir l’URL du package du programme d’installation de Power BI et d’autres informations dont vous avez besoin pour valider la nouvelle offre.  Pour la version initiale, toutes les applications Power BI sont gratuites et disponibles en téléchargement à partir d’AppSource. Pour cette raison, vous ne pouvez pas définir de références SKU pour ce type d’offre.

![Onglet Informations techniques](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Champs Informations techniques 

Dans l’onglet **Informations techniques**, renseignez les champs décrits dans le tableau suivant. Un astérisque (*) à la fin d’une étiquette de champ signifie que le champ est obligatoire.

|        Champ          |  Description                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **URL du programme d’installation\***     | Power BI génère cette URL lorsque vous publiez l’application et passez en production.  Pour plus d’informations, consultez [Publish apps with dashboards and reports in Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps) (Publier des applications avec tableaux de bord et rapports dans Power BI).  |
|  **Instructions de validation**  |  Si vous le souhaitez, ajoutez des instructions (jusqu’à 3 000 caractères) pour aider l’équipe de validation de Microsoft à configurer votre application, s’y connecter et la tester. Insérez des paramètres de configuration standard, des comptes, des paramètres ou d’autres informations qui peuvent être utilisés pour tester l’option Connecter des données. Ces informations sont uniquement visibles par l’équipe de validation et sont dédiées à la validation.  |
| **Cette application est-elle créée comme pack de contenu Power BI ?** | Actuellement, ce champ est utilisé uniquement en interne. Laissez le paramètre par défaut **Non**. Si vous modifiez le paramètre pour le définir sur **Oui**, vous pouvez arrêter le processus de publication.  |  
|  |  |


## <a name="next-steps"></a>Étapes suivantes

Dans l’onglet [Détails de la vitrine](./cpp-storefront-details-tab.md), indiquez des informations marketing et juridiques relatives à votre application.

