---
title: Meilleures pratiques pour la résilience des connexions
titleSuffix: Azure Cache for Redis
description: Découvrez comment rendre vos connexions Azure Cache pour Redis résilientes.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 999d8f1f4bf5ad1eef2008678f1e46d1586dd40a
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223697"
---
# <a name="connection-resilience"></a>Résilience des connexions

## <a name="retry-commands"></a>Commandes de nouvelle tentative

Configurez vos connexions client pour effectuer de nouvelles tentatives avec un backoff exponentiel. Pour plus d’informations, consultez les [Instructions relatives aux nouvelles tentatives](/azure/architecture/best-practices/retry-service-specific#azure-cache-for-redis).

## <a name="test-resiliency"></a>Tester la résilience

Testez la résilience de votre système aux interruptions de connexion en effectuant un [redémarrage](cache-administration.md#reboot) pour simuler un correctif. Pour plus d’informations sur le tests de performance, consultez [Test de performance](cache-best-practices-performance.md).

## <a name="configure-appropriate-timeouts"></a>Configurer les délais d’attente appropriés

Configurez votre bibliothèque de client pour utiliser un *délai de connexion* de 10 à 15 secondes et un *délai de commande* de 5 secondes. Le *délai de connexion* est le temps pendant lequel votre client attend l’établissement d’une connexion avec un serveur Redis. La plupart des bibliothèques de client ont une autre configuration de délai d’attente pour les *délais de commande*, qui est le temps pendant lequel le client attend une réponse du serveur Redis.

Par défaut, certaines bibliothèques ont le *délai de commande* défini sur 5 secondes. Songez à définir un délai plus long ou plus court en fonction de votre scénario et de la taille des valeurs stockées dans votre cache.

Si le *délai de commande* est trop court, il se peut que la connexion semble instable. En revanche, si le *délai de commande* est trop long, il se peut que votre application doive attendre longtemps avant de pouvoir déterminer si la commande va expirer ou non.

Configurez votre bibliothèque de client pour qu’elle observe un *délai de connexion* d’au moins 15 secondes afin de laisser au système le temps de se connecter même dans des conditions de sollicitation plus importante du processeur. Un *délai de connexion* court ne garantit pas que la connexion puisse être établie dans ce laps de temps.

Si un problème se produit (par exemple, forte sollicitation du processeur du client ou du serveur), une valeur de délai de connexion basse entraîne l’échec de la tentative de connexion. Ce comportement aggrave souvent une situation déjà détériorée. Au lieu d’améliorer la situation, la diminution des délais d’attente aggrave le problème en forçant le système à redémarrer le processus de tentative de reconnexion, avec le risque de générer au final une boucle *connexion -> échec -> nouvelle tentative*.

Nous conseillons généralement de conserver un *délai de connexion* d’au moins 15 secondes. En effet, il est préférable d’attendre 15 ou 20 secondes que la première tentative de connexion aboutisse, plutôt que de faire plusieurs tentatives rapprochées qui échouent. Avec ce genre de boucle de nouvelles tentatives, votre système peut en fin de compte rester indisponible durant plus longtemps que si vous lui accordez initialement plus de temps.

## <a name="avoid-client-connection-spikes"></a>Éviter les pics de connexion client

Évitez de créer de nombreuses connexions en même temps lors de la reconnexion après une perte de connexion. De la même façon que des [délais de connexion courts](#configure-appropriate-timeouts) peuvent entraîner des interruptions plus longues, le démarrage simultané de nombreuses tentatives de reconnexion peut augmenter la charge du serveur et allonger le temps nécessaire pour la reconnexion de tous les clients.

Si vous reconnectez de nombreuses instances clientes, envisagez d’échelonner les nouvelles connexions afin d’éviter un pic abrupt du nombre de clients connectés.

> [!NOTE]
> Si vous utilisez la bibliothèque de client `StackExchange.Redis`, définissez `abortConnect` sur `false` dans votre chaîne de connexion.  Nous vous recommandons de laisser le `ConnectionMultiplexer` gérer la reconnexion. Pour plus d’informations, consultez [Meilleures pratiques StackExchange.Redis](/azure/azure-cache-for-redis/cache-management-faq#stackexchangeredis-best-practices).

## <a name="avoid-leftover-connections"></a>Éviter les connexions inutilisées

Les caches sont limités quant au nombre de connexions clientes par niveau de cache. Assurez-vous que, lorsque votre application cliente recrée des connexions, elle ferme et supprime les anciennes connexions.

## <a name="advance-maintenance-notification"></a>Notification de maintenance à l’avance

Utilisez des notifications pour être informé de la maintenance à venir. Pour plus d’informations, consultez [Puis-je être notifié à l’avance d’une maintenance planifiée ?](cache-failover.md#can-i-be-notified-in-advance-of-a-planned-maintenance).

## <a name="schedule-maintenance-window"></a>Fenêtre Planifier la maintenance

Ajustez vos paramètres de cache à la maintenance. Pour plus d’informations sur la création d’une fenêtre de maintenance afin de réduire les effets négatifs sur votre cache, consultez [Planifier les mises à jour](cache-administration.md#schedule-updates).

## <a name="more-design-patterns-for-resilience"></a>Autres modèles de conception pour la résilience

Appliquez des modèles de conception pour la résilience. Pour plus d’informations, consultez [Comment rendre mon application résiliente](cache-failover.md#how-do-i-make-my-application-resilient).

## <a name="idle-timeout"></a>Délai d’inactivité

Azure Cache pour Redis ayant actuellement un délai d’inactivité de 10 minutes pour les connexions. le paramètre de délai d’inactivité dans votre application cliente doit être inférieur à 10 minutes. La plupart des bibliothèques clientes courantes disposent d’un paramètre de configuration qui permet aux bibliothèques clientes d’envoyer des commandes Redis `PING` à un serveur Redis automatiquement et périodiquement. Toutefois, lors de l’utilisation de bibliothèques clientes sans ce type de paramètre, les applications clientes sont elles-mêmes responsables du maintien de la connexion active.
