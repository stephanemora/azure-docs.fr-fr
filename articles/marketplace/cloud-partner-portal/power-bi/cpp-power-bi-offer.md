---
title: Power BI offre de l’application - place de marché Azure | Microsoft Docs
description: Comment publier une application Power BI à la place de marché Microsoft AppSource.
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
ms.date: 03/27/2019
ms.author: pbutlerm
ms.openlocfilehash: f18a1b05e5a38a79945d8df6706dd2147d6b43df
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009714"
---
# <a name="power-bi-app-offer"></a>Offre d’application Power BI

|              |                                |
|--------------|--------------------------------|
| Cet article explique comment publier une application Power BI de Microsoft [place de marché AppSource](https://appsource.microsoft.com/).  Une application Power BI regroupe personnalisable contenu Power BI, y compris les jeux de données, des rapports et tableaux de bord. Vous pouvez ensuite déployer l’application à d’autres locataires Power BI via AppSource, effectuer les ajustements et les personnalisations autorisées par le développeur et vous connecter à vos propres données. | ![Icône Power BI](./media/powerbi-icon.png) |


Cet article est divisé en trois parties principales :

-   [Conditions préalables](./cpp-prerequisites.md). Les exigences techniques et professionnelles pour la création et publication d’une offre d’application Power BI.
-   [Créer une offre d’application Power BI](./cpp-create-offer.md). Création d’une application Power BI offre entrée à l’aide de la [portail Microsoft Cloud Partner](https://cloudpartner.azure.com).
-   [Publier une offre d’application Power BI](./cpp-publish-offer.md). Comment soumettre une offre sur AppSource pour la publication et comment mettre à jour une offre existante.


## <a name="publishing-steps"></a>Étapes de publication

Voici les étapes principales pour la publication d’une offre d’application Power BI :

![Application Power BI offre des étapes de la publication](media/publishing-steps.png)

Voici l’offre d’application Power BI processus de publication :

1. Créer un modèle d’application dans Power BI. Cette action génère une URL d’installation de package, qui représente la ressource technique principale pour l’offre. Également à ce stade, vous devez promouvoir le package de test de préproduction. Pour plus d’informations, consultez [que sont les applications de modèle Power BI ?](https://docs.microsoft.com/power-bi/service-template-apps-overview). 
2. Collecter ou créer des documents marketing de l’offre, y compris : nom officiel, description, logos, etc. 
3. Collecter ou créer juridiques de l’offre et prendre en charge des documents : *conditions d’utilisation*, *politique de confidentialité*, *politique de support*, aide de l’utilisateur, etc.
4. Créez l’offre : utiliser le portail Cloud Partner pour configurer les détails de l’offre, y compris la description de l’offre, des documents commerciaux, informations légales, les informations de prise en charge, les spécifications de ressource.  Une fois que l’offre est complètement spécifié, le soumettrez pour publication.
5. Surveiller le processus de publication dans le portail Cloud Partner.  Pendant cette étape, l’équipe d’intégration de AppSource teste, valide et certifie que votre application. 
6. Une fois que l’application est certifiée avec succès, examinez-la dans son environnement de test et le libérer. 
7. L’application Power BI est répertoriée sur AppSource (il « conduit en direct »).
8. Dans Power BI, promouvoir le package de préproduction en production. Pour plus d’informations, consultez [gérer la version d’application de modèle](https://docs.microsoft.com/power-bi/service-template-apps-create#manage-the-template-app-release).


## <a name="next-steps"></a>Étapes suivantes

Avant de créer votre offre d’application Power BI et le publier sur AppSource, vous devez respecter les [exigences](./cpp-prerequisites.md) pour la publication d’une application Power BI à AppSource.
