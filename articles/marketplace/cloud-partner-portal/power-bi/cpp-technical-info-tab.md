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
ms.openlocfilehash: d96ef2fd318d6164e1b7dfc5c4b72d6957af0f3e
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55744432"
---
# <a name="power-bi-apps-technical-info-tab"></a>Onglet Informations techniques des applications Power BI

L’onglet **Informations techniques** de la page **Nouvelle offre** est l’endroit où vous fournissez l’URL du package d’installation Power BI et toutes les informations supplémentaires requises pour la validation de la nouvelle offre.  Pour la version initiale, toutes les applications Power BI sont gratuites, disponibles en téléchargement à partir d’AppSource sans frais supplémentaires. Par conséquent, vous ne serez pas en mesure de définir les références SKU pour ce type d’offre.

![Onglet Informations techniques](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Champs Informations techniques 

Dans l’onglet **Informations techniques**, vous devez fournir les champs suivants.  Un astérisque (*) en regard de l’étiquette de champ indique que ce champ est obligatoire.

|        Champ          |  Description                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **URL du programme d’installation**     | Adresse généré par Power BI lorsque vous publiez l’application et passez en production.  Pour plus d’informations sur la façon de générer l’URL, consultez [Publier des applications de service dans Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).  |
|  **Instructions de validation**  |  Instructions textuelles facultatives (maximum 3 000 caractères) permettant à l'équipe de validation Microsoft de vous aider à configurer, connecter et tester votre application, notamment : paramètres de configuration standard, comptes de test ou paramètres pouvant être utilisés pour tester l'option « Connecter les données », etc. Ces informations sont uniquement visibles par l’équipe de validation et servent uniquement à des fins de validation.  |
| **Cette application est-elle créée comme pack de contenu Power BI ?** | Actuellement, il s’agit d’un champ utilisé en interne. Laissez la valeur définie sur sa valeur par défaut, `No` ; sinon, la modification de ce champ sur `Yes` risque d’entraver la publication.  |  
|  |  |


## <a name="next-steps"></a>Étapes suivantes

Dans l’onglet [Détails de la vitrine](./cpp-storefront-details-tab.md) suivant, vous fournissez des informations marketing et juridiques relatives à votre application.

