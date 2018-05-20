---
title: Aide sur la limitation de requêtes Azure Key Vault
description: La limitation Key Vault permet de réduire le nombre d’appels simultanés afin d’empêcher toute surexploitation des ressources.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: ''
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 05/10/2018
ms.author: alleonar
ms.openlocfilehash: 59968f2bccbe2828ebe5fb33c57ed28d4f8509b6
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2018
---
# <a name="azure-key-vault-throttling-guidance"></a>Aide sur la limitation de requêtes Azure Key Vault

La limitation de requêtes est le processus permettant de réduire le nombre d’appels simultanés au service Azure afin d’empêcher toute surexploitation des ressources. Azure Key Vault (AKV) est conçu pour gérer un volume élevé de requêtes. En cas d’affluence, la limitation des requêtes du client permet de maintenir des performances et une fiabilité optimales pour le service AKV.

Les limites varient selon les scénarios. Par exemple, si vous réalisez un volume important d’écritures, la possibilité de limitation est plus élevée que si vous effectuez uniquement des lectures.

## <a name="how-does-key-vault-handle-its-limits"></a>Comment Key Vault gère-t-il ses limites ?

Les limites de service de Key Vault sont là pour empêcher tout usage abusif des ressources et garantir la qualité du service pour tous les clients de Key Vault. En cas de dépassement d’un seuil de service, Key Vault limite toutes les autres requêtes de ce client sur une période donnée. Dans ce cas, Key Vault retourne le code d’état HTTP 429 (Trop de requêtes), et les requêtes échouent. Par ailleurs, les requêtes qui ont échoué et retournent une erreur 429 sont comptabilisées dans les limites suivies par Key Vault. 

Si vous avez un scénario valide justifiant une limitation supérieure, contactez-nous.


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Guide pratique pour limiter une application en réponse à des limites de service

Voici les **meilleures pratiques** pour limiter une application :
- Réduisez le nombre d’opérations par requête.
- Réduisez la fréquence des requêtes.
- Évitez les nouvelles tentatives immédiates. 
    - Toutes les requêtes sont comptabilisées dans le cadre de vos limites d’utilisation.

Lorsque vous implémentez la gestion des erreurs de votre application, utilisez le code d’erreur HTTP 429 pour détecter si une limitation côté client est nécessaire. Si la requête échoue à nouveau avec un code d’erreur HTTP 429, cela signifie que vous rencontrez toujours une limite de service Azure. Continuez à utiliser la méthode de limitation côté client recommandée, en réessayant la requête jusqu’à ce qu’elle aboutisse.

### <a name="recommended-client-side-throttling-method"></a>Méthode de limitation côté client recommandée

En cas de code d’erreur HTTP 429, commencez à limiter votre client suivant une approche d’interruption exponentielle :

1. Patientez une seconde, relancez la requête ;
2. Si la limitation persiste, patientez deux secondes, relancez la requête ;
3. Si la limitation persiste, patientez quatre secondes, relancez la requête ;
4. Si la limitation persiste, patientez huit secondes, relancez la requête ;
5. Si la limitation persiste, patientez seize secondes, relancez la requête.

À ce stade, vous ne devriez pas recevoir de code de réponse HTTP 429.

## <a name="see-also"></a>Voir aussi

Pour orienter la limitation de façon plus approfondie sur Microsoft Cloud, consultez la page [Modèle de limitation](https://docs.microsoft.com/azure/architecture/patterns/throttling).

