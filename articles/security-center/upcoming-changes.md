---
title: Changements importants à venir sur Azure Security Center
description: Changements à venir sur Azure Security Center dont vous devez peut-être tenir compte et pour lesquels une planification peut être nécessaire
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2020
ms.author: memildin
ms.openlocfilehash: 549a95b0b2ffc2b2d2bf5670a961e0454683e33a
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026715"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Changements importants à venir sur Azure Security Center

> [!IMPORTANT]
> Les informations de cette page concernent des produits ou fonctionnalités en préversion, qui peuvent être modifiés de façon substantielle avant d’être commercialisés, le cas échéant. Microsoft n’offre aucun engagement ni garantie, formel ou implicite, concernant les informations contenues dans ce document.

Dans cette page, vous allez découvrir les changements planifiés pour Security Center. Elle décrit les changements planifiés du produit qui peuvent avoir un impact sur votre degré de sécurisation ou vos workflows.

Si vous recherchez les notes de publication les plus récentes, vous les trouverez dans [Nouveautés d’Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Changements planifiés

### <a name="recommendations-related-to-azure-security-benchmark-to-be-added-preview"></a>Recommandations relatives au Benchmark de sécurité Azure à ajouter (préversion)

| Aspect | Détails |
|---------|---------|
|Date d’annonce | 26 octobre 2020  |
|Date de ce changement  |  Novembre 2020 |
|Impact     | Potentiellement, davantage de recommandations à examiner.<br>Pas d’impact immédiat sur le degré de sécurisation - Les recommandations en préversion n’affectent pas votre degré de sécurisation.<br>Pas d’impact immédiat sur l’état d’intégrité de vos ressources - Les recommandations en préversion ne rendent pas une ressource « non saine ».|
|  |  |

Le Benchmark de sécurité Azure est l’ensemble des directives propres à Azure et créées par Microsoft contenant les bonnes pratiques de sécurité et de conformité basées sur les infrastructures de conformité courantes. [En savoir plus sur le Benchmark de sécurité Azure](../security/benchmarks/introduction.md).

Les 18 nouvelles recommandations suivantes seront ajoutées à Security Center pour étendre la couverture du benchmark.

Les recommandations en préversion ne rendent pas une ressource non saine et ne sont pas incluses dans les calculs de votre degré de sécurisation. Corrigez-les là où c’est possible, de sorte que quand la période de préversion se termine, elles soient prises en compte dans le calcul de votre degré de sécurisation. Découvrez comment répondre à ces recommandations dans [Corriger les recommandations dans Azure Security Center](security-center-remediate-recommendations.md).

- Sauvegarde Azure doit être activé pour les machines virtuelles
- L’application de la connexion SSL doit être activée pour les serveurs de base de données MySQL
- L’application de la connexion SSL doit être activée pour les serveurs de base de données PostgreSQL
- La sauvegarde géoredondante doit être activée pour Azure Database for MariaDB
- La sauvegarde géoredondante doit être activée pour Azure Database pour MySQL
- La sauvegarde géoredondante doit être activée pour Azure Database pour PostgreSQL
- Java doit être mis à jour vers la dernière version pour votre application API
- Java doit être mis à jour vers la dernière version pour votre application de fonction
- Java doit être mis à jour vers la dernière version pour votre application web
- PHP doit être mis à jour vers la dernière version pour votre application API
- PHP doit être mis à jour vers la dernière version pour votre application web
- Le point de terminaison privé doit être activé pour les serveurs MariaDB
- Le point de terminaison privé doit être activé pour les serveurs MySQL
- Le point de terminaison privé doit être activé pour les serveurs PostgreSQL
- Python doit être mis à jour vers la dernière version pour votre application API
- Python doit être mis à jour vers la dernière version pour votre application de fonction
- Python doit être mis à jour vers la dernière version pour votre application web
- Les applications web doivent exiger un certificat SSL pour toutes les demandes entrantes

Liens connexes :

- [En savoir plus sur le Benchmark de sécurité Azure](../security/benchmarks/introduction.md)
- [En savoir plus sur les applications API Azure](../app-service/app-service-web-tutorial-rest-api.md)
- [En savoir plus sur les applications de fonction Azure](../azure-functions/functions-overview.md)
- [En savoir plus sur les applications web Azure](../app-service/overview.md)
- [En savoir plus sur Azure Database for MariaDB](../mariadb/overview.md)
- [En savoir plus sur Azure Database pour MySQL](../mysql/overview.md)
- [En savoir plus sur Azure Database pour PostgreSQL](../postgresql/overview.md)

## <a name="next-steps"></a>Étapes suivantes

Pour tous les changements récents du produit, consultez [Nouveautés d’Azure Security Center](release-notes.md).