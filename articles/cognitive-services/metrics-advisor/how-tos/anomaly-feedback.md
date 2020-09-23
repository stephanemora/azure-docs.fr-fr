---
title: Envoi de commentaires sur les anomalies au service Metrics Advisor
titleSuffix: Azure Cognitive Services
description: Découvrez comment envoyer des commentaires sur les anomalies détectées par votre instance Metrics Advisor et ajuster les résultats.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 50d422edf1a4b45132d0b86eac9d4947cef5e5bf
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929977"
---
# <a name="adjust-anomaly-detection-using-feedback"></a>Ajustement de la détection d’anomalie selon les commentaires

Si les résultats de détection d’anomalies fournis par Metrics Monitor ne vous satisfont pas, vous pouvez ajouter manuellement des commentaires qui affecteront le modèle appliqué à vos données. 

Utilisez les boutons situés en haut à droite de la page pour activer le mode d’annotation Commentaires.

:::image type="content" source="../media/feedback/annotation-mode.png" alt-text="Mode d’annotation Commentaires":::

Une fois le mode d’annotation Commentaires activé, il est possible d’ajouter des commentaires pour un point ou plusieurs points continus.

## <a name="give-feedback-for-one-point"></a>Ajout de commentaires pour un point 

Lorsque le mode d’annotation Commentaires est activé, cliquez sur un point pour ouvrir un panneau **Ajouter des commentaires**. Vous pouvez définir le type de commentaire que vous souhaitez appliquer. Ces commentaires seront incorporés dans la détection des futurs points.  

* Sélectionnez **Anomalie** si vous pensez que le point a été mal étiqueté par Metrics Monitor. Vous pouvez spécifier si un point doit ou non être une anomalie. 
* Sélectionnez **Point de changement** si vous pensez que le point indique le début d’une modification de tendance.
* Sélectionnez **Période** pour indiquer un caractère saisonnier. Metrics Monitor peut détecter automatiquement les intervalles de saisonnalité. Sinon, vous avez la possibilité de les spécifier manuellement. 

Si vous le souhaitez, laissez un commentaire dans la zone de texte **Commentaires** par la même occasion, puis cliquez sur **Enregistrer** pour enregistrer vos commentaires.

:::image type="content" source="../media/feedback/feedback-menu.png" alt-text="Menu Commentaires":::

## <a name="give-feedback-for-multiple-continuous-points"></a>Envoi de commentaires sur plusieurs points continus

Pour envoyer des commentaires pour plusieurs points continus à la fois, maintenez le bouton de la souris enfoncé et faites-la glisser sur les points que vous souhaitez annoter. Le menu de commentaires ci-dessus apparaît. Les mêmes commentaires seront appliqués à tous les points choisis une fois que vous aurez cliqué sur **Enregistrer**.

:::image type="content" source="../media/feedback/continuous-points.png" alt-text="Choix de plusieurs points":::

## <a name="how-to-view-my-feedback"></a>Affichage des commentaires

Pour voir si la détection d’anomalie d’un point a changé, placez le curseur sur le point. L’info-bulle indique **Affecté par les commentaires : true** si la détection a été modifiée. Si elle affiche **false**, cela signifie que le calcul des commentaires a été effectué sur le point, mais que le résultat de la détection d’anomalie n’a pas été modifié.

:::image type="content" source="../media/feedback/affected-point.png" alt-text="Point affecté par les commentaires":::

## <a name="when-should-i-annotate-an-anomaly-as-normal"></a>Circonstances dans lesquelles une anomalie doit être annotée comme « normale »

Il existe de nombreuses raisons pour lesquelles on peut considérer qu’une anomalie est une fausse alerte. Si l’un des scénarios suivants s’applique, envisagez d’utiliser les fonctionnalités Metrics Advisor ci-dessous :


|Scénario  |Recommandation |
|---------|---------|
|L’anomalie est due à une modification connue de la source de données, par exemple, une modification du système.     | N’annotez pas cette anomalie si ce scénario n’est pas censé se reproduire régulièrement.        |
|L’anomalie est due à un jour non ouvré.     | Utilisez les [Événements prédéfinis](configure-metrics.md#preset-events) pour signaler la détection d’anomalie à des moments spécifiés.       |
|Il existe un modèle régulier des anomalies détectées (par exemple, le weekend) et elles ne doivent pas être des anomalies.      |Utilisez la fonctionnalité de commentaires ou les événements prédéfinis.        |

## <a name="next-steps"></a>Étapes suivantes
- [Diagnostic d’un incident](diagnose-incident.md)
- [Configuration des métriques et affinage de la configuration de la détection](configure-metrics.md)
- [Configuration des alertes et réception de notifications à l’aide d’un hook](../how-tos/alerts.md)