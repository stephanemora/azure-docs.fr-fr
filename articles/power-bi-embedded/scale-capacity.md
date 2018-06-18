---
title: Mettre à l’échelle une capacité Power BI Embedded | Microsoft Docs
description: Cet article explique comment mettre à l’échelle une capacité Power BI Embedded dans Microsoft Azure.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/19/2018
ms.author: maghan
ms.openlocfilehash: a089d34b3f49188f3037ad57aab4d7bcc8c660f2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2018
ms.locfileid: "31408283"
---
# <a name="scale-your-power-bi-embedded-capacity"></a>Mettre à l’échelle une capacité Power BI Embedded

Cet article explique comment mettre à l’échelle une capacité Power BI Embedded dans Microsoft Azure. La mise à l’échelle permet d’augmenter ou diminuer la taille de la capacité.

Cela suppose d’avoir créé une capacité Power BI Embedded. Dans le cas contraire, consultez la page [Créer une capacité Power BI Embedded sur le Portail Azure](create-capacity.md) pour démarrer.

> [!NOTE]
> Une opération de mise à l’échelle peut prendre environ une minute. Pendant ce temps, la capacité n’est pas disponible. Le chargement du contenu incorporé peut échouer.

## <a name="scale-a-capacity"></a>Mettre à l’échelle une capacité

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Sélectionnez **Tous les services** > **Power BI Embedded** pour voir vos capacités.

    ![Tous les services dans le portail Azure](media/scale-capacity/azure-portal-more-services.png)

3. Sélectionnez la capacité que vous souhaitez mettre à l’échelle.

    ![Liste des capacités Power BI Embedded sur le Portail Azure](media/scale-capacity/azure-portal-capacity-list.png)

4. Sélectionnez **Niveau tarifaire** sous **Mettre à l’échelle** dans votre capacité.

    ![Option Niveau tarifaire sous Mettre à l’échelle](media/scale-capacity/azure-portal-scale-pricing-tier.png)

    Votre niveau tarifaire actuel est représenté en bleu.

    ![Niveau tarifaire actuel représenté en bleu](media/scale-capacity/azure-portal-current-tier.png)

5. Pour monter ou descendre en puissance, sélectionnez le niveau à atteindre. Lorsque vous sélectionnez un nouveau niveau, le contour de la sélection apparaît en pointillés bleus. Sélectionnez **Sélectionner** pour passer au nouveau niveau.

    ![Sélectionnez le nouveau niveau](media/scale-capacity/azure-portal-select-new-tier.png)

    La mise à l’échelle de la capacité peut prendre une ou deux minutes.

6. Vérifiez votre niveau en consultant l’onglet de vue d’ensemble. Le niveau tarifaire actuel est listé.

    ![Confirmer le niveau actuel](media/scale-capacity/azure-portal-confirm-tier.png)

## <a name="next-steps"></a>Étapes suivantes

Pour suspendre ou démarrer votre capacité, consultez la page [Suspendre et démarrer une capacité Power BI Embedded sur le Portail Azure](pause-start.md).

Pour commencer l’intégration de contenus Power BI dans votre application, consultez la page [Guide pratique pour incorporer des tableaux de bord, rapports et vignettes Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Des questions ? [Essayer d’interroger la Communauté Power BI](http://community.powerbi.com/).
