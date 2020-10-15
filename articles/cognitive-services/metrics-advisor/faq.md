---
title: Forum Aux Questions sur Metrics Advisor
titleSuffix: Azure Cognitive Services
description: Forum aux questions sur le service Metrics Advisor.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: mbullwin
ms.openlocfilehash: 42b23876761afa213b07f07b3a61e125dcf0824b
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92046806"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>Forum Aux Questions sur Metrics Advisor

### <a name="what-is-the-cost-of-my-instance"></a>Quel est le coût de mon instance ?

Il n’y a actuellement pas de coût d’utilisation de votre instance au cours de la préversion.

### <a name="why-is-the-demo-website-readonly"></a>Pourquoi le site Web de la version de démonstration est-il en lecture seule ?

Le [site Web de la version de démonstration](https://anomaly-detector.azurewebsites.net/) est disponible publiquement. Cette instance est mise en lecture seule pour empêcher le téléchargement accidentel de données.

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>Pourquoi ne puis-je pas créer la ressource ? Le « Niveau tarifaire » n’est pas disponible et il indique « Vous avez déjà créé 1 S0 pour cet abonnement » ?

:::image type="content" source="media/pricing.png" alt-text="Message lorsqu’une ressource F0 existe déjà":::

Pendant la préversion publique, une seule instance de Metrics Advisor peut être créée sous un abonnement, dans une région.

Si vous avez déjà créé une instance dans la même région à l’aide du même abonnement, vous pouvez essayer une autre région ou un autre abonnement pour créer une nouvelle instance. Vous pouvez également supprimer une instance existante pour en créer une nouvelle.

Si vous avez déjà supprimé l’instance existante mais que l’erreur persiste, patientez environ 20 minutes après la suppression de la ressource avant de créer une nouvelle instance.

## <a name="basic-concepts"></a>Concepts de base

### <a name="what-is-multi-dimensional-time-series-data"></a>Qu’est-ce que les données de séries chronologiques multidimensionnelles ?

Consultez la définition de la [Métrique multidimensionnelle](glossary.md#multi-dimensional-metric) dans le glossaire.

### <a name="how-much-data-is-needed-for-metrics-advisor-to-start-anomaly-detection"></a>Quelle quantité de données est nécessaire pour que Metrics Advisor démarrent la détection des anomalies ?

Au minimum, un point de données peut déclencher la détection d’anomalie. Toutefois, cela n’apporte pas la meilleure précision. Le service envisage une fenêtre de points de données précédents en utilisant la valeur que vous avez spécifiée comme règle de remplissage lors de la création du flux de données.

Nous vous recommandons d’avoir des données avant le timestamp sur lequel vous souhaitez effectuer la détection.
En fonction de la granularité de vos données, la quantité de données recommandée varie comme indiqué ci-dessous.

| Granularité | Quantité de données recommandée pour la détection |
| ----------- | ------------------------------------- |
| moins de 5 minutes | 4 jours de données |
| de 5 minutes à 1 jour | 28 jours de données |
| de plus d’1 jour à 31 jours | 4 ans de données |
| plus de 31 jours | 48 jours de données |

### <a name="why-metrics-advisor-doesnt-detect-anomalies-from-historical-data"></a>Pourquoi Metrics Advisor ne détecte-t-il pas les anomalies à partir des données historiques ?

Metrics Advisor est conçu pour détecter les données de streaming en direct. Il existe une limitation de la longueur maximale des données d’historique que le service examinera pour exécuter la détection des anomalies. Cela signifie que seuls les points de données après un timestamp précédent donné auront des résultats de détection d’anomalie. Ce timestamp précédent dépend de la granularité de vos données.

En fonction de la granularité de vos données, les longueurs des données historiques qui auront des résultats de détection d’anomalie sont les suivantes.

| Granularité | Longueur maximale des données d’historique pour la détection d’anomalie |
| ----------- | ------------------------------------- |
| moins de 5 minutes | heure d’intégration : 13 heures |
| de 5 minutes à moins de 1 heure | heure d’intégration : 4 heures  |
| de 1 heure à moins de 1 jour | heure d’intégration : 14 heures  |
| 1 jour | heure d’intégration : 28 heures  |
| de plus d’1 jour à moins de 31 jours | heure d’intégration : 2 heures  |
| plus de 31 jours | heure d’intégration : 24 heures   |

### <a name="more-concepts-and-technical-terms"></a>Plus de concepts et de termes techniques

Pour plus d’informations, consultez également le [Glossaire](glossary.md).

###  <a name="how-do-i-write-a-valid-query-for-ingesting-my-data"></a>Comment écrire une requête valide pour l’ingestion de mes données ?  

Pour que Metrics Advisor ingère vos données, vous devez créer une requête qui retourne les dimensions de vos données à un horodatage unique. Metrics Advisor exécute cette requête plusieurs fois pour obtenir les données de chaque horodatage. 

Notez que la requête doit retourner au plus un enregistrement pour chaque combinaison de dimensions, à un horodatage donné. Tous les enregistrements retournés doivent avoir le même horodatage. Aucun enregistrement en double ne doit être retourné par la requête.

Par exemple, supposons que vous créez la requête ci-dessous, pour une métrique quotidienne : 
 
`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(DAY, 1, @StartTime)`

Veillez à utiliser la précision appropriée pour votre série chronologique. Pour une métrique horaire, vous utilisez : 

`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(hour, 1, @StartTime)`

Notez que ces requêtes retournent uniquement des données à un horodatage unique et contiennent toutes les combinaisons de dimensions à ingérer par Metrics Advisor. 

:::image type="content" source="media/query-result.png" alt-text="Message lorsqu’une ressource F0 existe déjà" lightbox="media/query-result.png":::


### <a name="how-do-i-detect-spikes--dips-as-anomalies"></a>Comment détecter des mines et pixels indépendants du périphérique comme des anomalies ?

Si des seuils définitifs sont prédéfinis, vous pouvez définir manuellement le « seuil définitif » dans [configurations de la détection d’anomalie](how-tos/configure-metrics.md#anomaly-detection-methods).
S’il n’y a aucun seuil, vous pouvez utiliser la « détection intelligente » qui est alimentée par l’intelligence artificielle. Pour plus d’informations, reportez-vous à [régler la configuration de détection](how-tos/configure-metrics.md#tune-the-detecting-configuration).

### <a name="how-do-i-detect-inconformity-with-regular-seasonal-patterns-as-anomalies"></a>Comment faire détecter la non-conformité avec les modèles standard (saisonnier) comme des anomalies ?

La « Détection intelligente » peut apprendre le modèle de vos données, y compris les modèles saisonniers. Elle détecte ensuite les points de données qui ne sont pas conformes aux modèles standard comme des anomalies. Pour plus d’informations, reportez-vous à [régler la configuration de détection](how-tos/configure-metrics.md#tune-the-detecting-configuration).

### <a name="how-do-i-detect-flat-lines-as-anomalies"></a>Comment détecter les lignes plates comme des anomalies ?

Si vos données sont normalement assez instables et fluctuent beaucoup et que vous souhaitez être alerté lorsqu’elles deviennent trop stables ou qu’elles se transforment en une ligne plate, le « Seuil de modification » peut être configuré pour détecter ces points de données lorsque la modification est trop légère.
Pour plus d’informations, consultez les [configurations de détection d’anomalie](how-tos/configure-metrics.md#anomaly-detection-methods).

## <a name="next-steps"></a>Étapes suivantes
- [Présentation de Metrics Advisor](overview.md)
- [Essayer le site de la version de démonstration](quickstarts/explore-demo.md)
- [Utiliser le Portail web](quickstarts/web-portal.md)