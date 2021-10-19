---
title: Meilleures pratiques pour l’hébergement d’une application cliente Kubernetes
titleSuffix: Azure Cache for Redis
description: Découvrez comment héberger une application cliente Kubernetes qui utilise Azure Cache pour Redis.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 10/11/2021
ms.author: shpathak
ms.openlocfilehash: 33628fc16dfc2693cad0f8cd13f555cc10e55f25
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129808736"
---
# <a name="kubernetes-hosted-client-application"></a>Application cliente hébergée par Kubernetes

## <a name="client-connections-from-multiple-pods"></a>Connexions clientes à partir de plusieurs pods

Lorsque vous avez plusieurs pods se connectant à un serveur Redis, assurez-vous que les nouvelles connexions en provenance des pods sont créées de manière échelonnée. Si plusieurs pods démarrent en peu de temps sans échelonnement, cela provoque un pic soudain du nombre de connexions client créées. Le nombre élevé de connexions entraîne une charge élevée sur le serveur Redis et peut entraîner des délais d’attente.

Évitez le même scénario lors de l’arrêt de plusieurs pods en même temps. Un échec d’échelonnement de l’arrêt peut entraîner une chute abrupte du nombre de connexions conduisant à une sollicitation du processeur.

## <a name="sufficient-pod-resources"></a>Ressources de pod suffisantes

Assurez-vous que le pod exécutant votre application cliente dispose de suffisamment de ressources processeur et mémoire. Si l’application cliente est exécutée à un niveau proche de ses limites de ressources, cela peut entraîner des délais d’attente.

## <a name="sufficient-node-resources"></a>Ressources de nœud suffisantes

Un pod exécutant l’application cliente peut être affecté par d’autres pods en cours d’exécution sur le même nœud, et limiter les connexions Redis ou les opérations d’E/S. Par conséquent, assurez-vous toujours que le nœud sur lequel vos pods d’application cliente sont exécutés disposent de mémoire, de processeur et de bande passante réseau en quantité suffisante. Si l’une de ces ressources présente un niveau insuffisant, cela peut entraîner des problèmes de connectivité.

## <a name="linux-hosted-client-applications-and-tcp-settings"></a>Applications clientes hébergées sur Linux et paramètres TCP

Si votre application cliente Azure Cache pour Redis est exécutée sur un conteneur Linux, nous vous recommandons de mettre à jour certains paramètres TCP comme indiqué dans la section [Paramètres TCP pour les applications clientes hébergées sur Linux](cache-best-practices-connection.md#tcp-settings-for-linux-hosted-client-applications).

## <a name="next-steps"></a>Étapes suivantes

- [Développement](cache-best-practices-development.md)
- [FAQ sur le développement d’Azure Cache pour Redis](cache-development-faq.yml)
