---
title: Comment choisir entre le mode débit approvisionné et le mode serverless sur Azure Cosmos DB
description: Découvrez comment choisir entre le mode débit approvisionné et le mode serverless pour votre charge de travail.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 0adb346a693beaa905438cfdc1249c1646c28811
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608633"
---
# <a name="how-to-choose-between-provisioned-throughput-and-serverless"></a>Comment choisir entre le mode débit approvisionné et le mode serverless

Azure Cosmos DB est disponible en deux modes de capacité différents : [débit approvisionné](set-throughput.md) et [serverless](serverless.md). Vous pouvez effectuer exactement les mêmes opérations de base de données dans les deux modes, mais la façon dont vous êtes facturé pour ces opérations est radicalement différente. La vidéo suivante explique les principales différences entre ces modes, et la façon dont ils s’adaptent aux différents types de charges de travail :

> [!VIDEO https://www.youtube.com/embed/CgYQo6uHyt0]

> [!NOTE]
> Le mode serverless n’est actuellement pris en charge que sur l’API Azure Cosmos DB Core (SQL).

## <a name="detailed-comparison"></a>Comparaison détaillée

| Critères | Débit approvisionné | Sans serveur |
| --- | --- | --- |
| Statut | Mise à la disposition générale | En préversion |
| Idéale pour | Charges de travail stratégiques nécessitant des performances prévisibles | Charges de travail non critiques de taille petite à moyenne avec trafic léger |
| Fonctionnement | Pour chacun de vos conteneurs, vous approvisionnez un débit exprimé en [unités de requête](request-units.md) par seconde. Chaque seconde, cette quantité d’unités de requête est disponible pour vos opérations de base de données. Le débit approvisionné peut être mis à jour manuellement ou ajusté automatiquement avec une [mise à l’échelle automatique](provision-throughput-autoscale.md). | Vous exécutez vos opérations de base de données sur vos conteneurs sans avoir à approvisionner de capacité. |
| Limitations par compte | Nombre maximal de régions Azure : illimité | Nombre maximal de régions Azure : 1 |
| Limitations par conteneur | Débit maximal : illimité<br>Stockage maximal : illimité | Débit maximal : 5 000 RU/s<br>Stockage maximal : 50 Go |
| Performances | Disponibilité de 99,99 % à 99,999 % couvertes par contrat SLA<br>Latence inférieure à 10 ms pour les lectures et écritures ponctuelles couverte par contrat SLA<br>Débit garanti de 99,99 % couvert par contrat SLA | Disponibilité de 99,9 % à 99,99 % couverte par contrat SLA<br>Latence inférieure à 10 ms pour les lectures ponctuelles et à 30 ms pour les écritures couvertes par SLO<br>Extensibilité en rafale de 95 % couverte par SLO |
| Modèle de facturation | La facturation s’effectue à l’heure pour les RU/s approvisionnés, quel que soit le nombre d’unités de requêtes consommées. | La facturation est effectuée sur une base horaire pour la quantité de RU consommées par vos opérations de base de données. |

> [!IMPORTANT]
> Certaines limitations du mode serverless pourront être atténuées ou supprimées lorsque ce mode sera mis à la disposition générale, et **votre retour d’expérience** sera décisif. Contactez-nous pour nous faire part de votre expérience en mode serverless : [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com).

## <a name="burstability-and-expected-consumption"></a>Extensibilité en rafale et consommation attendue

Dans certains cas, il peut être difficile de choisir entre le mode débit approvisionné ou le mode serverless pour une charge de travail donnée. Pour vous aider à prendre cette décision, vous pouvez estimer les aspects suivants :

- Le besoin d’**extensibilité en rafale** de votre charge de travail, c’est-à-dire le nombre maximal de RU que vous pouvez être amené à devoir consommer en une seconde.
- L’ensemble de votre **consommation attendue**, c’est-à-dire le nombre total de RU que vous pouvez consommer sur un mois (vous pouvez estimer cela avec l’aide du tableau présenté [ici](plan-manage-costs.md#estimating-serverless-costs)).

Si votre charge de travail nécessite une extensibilité en rafale supérieure à 5 000 RU par seconde, vous devriez choisir un débit approvisionné, car les conteneurs sans serveur ne peuvent pas dépasser cette limite. Si ce n’est pas le cas, vous pouvez comparer le coût des deux modes en fonction de la consommation attendue.

**Exemple 1** : la charge de travail attendue en rafale est d’au maximum 10 000 RU/s, et la consommation attendue totale de 20 millions de RU par mois.

- Seul le mode de débit approvisionné peut fournir un débit de 10 000 RU/s.

**Exemple 1** : la charge de travail attendue en rafale est d’au maximum 500 RU/s, et la consommation attendue totale de 20 millions de RU par mois.

- En mode débit approvisionné, vous pouvez approvisionner un conteneur avec 500 RU/s pour un coût mensuel de 0,008 USD * 5 * 730 = **29,20 USD**.
- En mode serverless, vous payez pour les RU consommées, soit 0,25 USD * 20 = **5,00 USD**.

**Exemple 1** : la charge de travail attendue en rafale est d’au maximum 500 RU/s, et la consommation attendue totale de 250 millions de RU par mois.

- En mode débit approvisionné, vous pouvez approvisionner un conteneur avec 500 RU/s pour un coût mensuel de 0,008 USD * 5 * 730 = **29,20 USD**.
- En mode serverless, vous payez pour le RU consommées, soit 0,25 USD * 250 = **62,50 USD**

(ces exemples ne sont pas pris en compte pour le coût de stockage, qui est le même dabs les deux modes).

> [!NOTE]
> Les coûts indiqués dans l’exemple précédent sont fournis à des fins de démonstration uniquement. Pour obtenir les informations les plus récentes sur la tarification, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [mode débit d’approvisionnement d’Azure Cosmos DB](set-throughput.md).
- En savoir plus sur le [mode serverless d’Azure Cosmos DB](serverless.md).
- Familiarisez-vous avec le concept d’[unités de requête (RU)](request-units.md).
