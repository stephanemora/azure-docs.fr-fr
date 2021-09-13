---
title: Entonnoirs Application Insights
description: Apprenez à utiliser les entonnoirs pour découvrir de quelle façon les clients interagissent avec votre application.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 07/30/2021
ms.openlocfilehash: 0c0ab7da554b85f9adcde4d4991e1271f2acb433
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562444"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Découvrir comment les clients utilisent votre application avec les entonnoirs Application Insights

Comprendre l’expérience de vos utilisateurs est primordial pour votre entreprise. Si votre application implique plusieurs étapes, vous devez savoir si la plupart des clients vont au bout du processus, ou s’ils arrêtent celui-ci à un moment donné. La progression via une série d’étapes dans une application web est appelée *entonnoir*. Vous pouvez utiliser les entonnoirs Application Insights pour obtenir des informations sur vos utilisateurs et surveiller les taux de conversion étape par étape. 

## <a name="create-your-funnel"></a>Créer votre entonnoir
Avant de créer votre entonnoir, choisissez la question à laquelle vous souhaitez répondre. Par exemple, vous souhaitez peut-être savoir combien d’utilisateurs sont en train d’afficher la page d’accueil, de consulter le profil d’un client ou de créer un ticket. 

Pour créer un entonnoir :

1. Dans l’onglet **Entonnoirs**, sélectionnez **Modifier**.
1. Choisissez votre *première étape*.

     :::image type="content" source="./media/usage-funnels/funnel.png" alt-text="Capture d’écran de l’onglet Entonnoir et de la sélection des étapes dans l’onglet de modification" lightbox="./media/usage-funnels/funnel.png":::

1. Pour appliquer des filtres à l’étape, sélectionnez l’option **Ajouter des filtres**, qui apparaît une fois que vous choisissez un élément pour la première étape.
1. Ensuite, choisissez votre *deuxième étape*, etc.
1. Sélectionnez l’onglet **Affichage** pour voir vos résultats d’entonnoir.

      :::image type="content" source="./media/usage-funnels/funnel-2.png" alt-text="Capture d’écran de l’onglet Entonnoir sous l’onglet Affichage montrant les résultats de la première et de la deuxième étape" lightbox="./media/usage-funnels/funnel-2.png":::

1. Pour enregistrer votre entonnoir dans le but de le consulter ultérieurement, sélectionnez **Enregistrer** en haut. Vous pouvez utiliser **Ouvrir** pour ouvrir vos entonnoirs enregistrés.

### <a name="funnels-features"></a>Fonctionnalités des entonnoirs

- Si votre application est échantillonnée, une bannière d’échantillonnage est affichée. La sélection de la bannière ouvre un volet contextuel, qui explique comment désactiver l’échantillonnage. 
- Sélectionnez une étape pour plus d’informations sur la droite. 
- Le graphique de conversion historique indique les taux de conversion au cours des 90 derniers jours. 
- Comprenez mieux vos utilisateurs en accédant à l’outil correspondant. Vous pouvez utiliser des filtres à chaque étape. 

## <a name="next-steps"></a>Étapes suivantes
  * [Vue d’ensemble de l’utilisation](usage-overview.md)
  * [Utilisateurs, Sessions et Événements](usage-segmentation.md)
  * [Rétention](usage-retention.md)
  * [Classeurs](../visualize/workbooks-overview.md)
  * [Ajouter du contexte utilisateur](./usage-overview.md)
  * [Exporter vers Power BI](./export-power-bi.md)