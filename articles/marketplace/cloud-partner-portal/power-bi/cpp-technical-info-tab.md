---
title: Informations techniques pour une offre d’application Power BI | Place de marché Azure
description: Configurez les champs d’informations techniques d'une offre d'application Power BI pour la Place de marché Microsoft AppSource.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d814ebc34193f5d7c0c3828d32aa3d2af29f3679
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80981356"
---
# <a name="power-bi-apps-technical-info-tab"></a>Onglet Informations techniques des applications Power BI

>[!Important]
>À partir du 13 avril 2020, nous commencerons à déplacer la gestion de vos offres d’applications Power BI vers l’Espace partenaires. Après la migration, vous créerez et gérerez vos offres dans l’Espace partenaires. Suivez les instructions indiquées dans [Vue d’ensemble de la création d’applications Power BI](https://aka.ms/AzureCreatePBIServiceApp) pour gérer vos offres migrées.

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

