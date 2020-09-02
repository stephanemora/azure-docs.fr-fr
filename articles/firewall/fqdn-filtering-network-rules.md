---
title: Filtrage de nom de domaine complet Pare-feu Azure dans les règles de réseau (préversion)
description: Utiliser le filtrage de nom de domaine complet Pare-feu Azure dans des règles de réseau
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/25/2020
ms.author: victorh
ms.openlocfilehash: 1a35d9c48dd46d5d220699589f4ed758d21feca8
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854276"
---
# <a name="use-fqdn-filtering-in-network-rules-preview"></a>Utiliser le filtrage de nom de domaine complet dans les règles de réseau (préversion)

> [!IMPORTANT]
> Le filtrage de nom de domaine complet dans les règles de réseau est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Un nom de domaine complet (FQDN) représente le nom de domaine d’un hôte ou d’une adresse IP. Vous pouvez utiliser des noms de domaine complets dans les règles de réseau basées sur la résolution DNS dans le pare-feu et la stratégie de pare-feu Azure. Cette fonctionnalité vous permet de filtrer le trafic sortant avec n’importe quel protocole TCP/UDP (y compris NTP, SSH, RDP, etc.). Pour utiliser des noms de domaine complet (FQDN) dans des règles de réseau, vous devez activer le proxy DNS. Pour plus d’informations, consultez [Paramètres DNS du Pare-feu Azure (préversion)](dns-settings.md).

> [!NOTE]
> Par conception, le filtrage FQDN ne prend pas en charge les caractères génériques.

## <a name="how-it-works"></a>Fonctionnement

Une fois que vous avez défini le serveur DNS dont votre organisation a besoin (Azure DNS ou votre propre DNS personnalisé), le pare-feu Azure convertit le nom de domaine complet en adresse IP basée sur le serveur DNS sélectionné. Cette traduction se produit à la fois pour le traitement des règles d’application et de réseau.

Quelle est la différence entre l’utilisation des noms de domaine dans les règles d’application et celle des règles de réseau ? 

- Le filtrage de nom de domaine complet dans les règles d’application pour HTTP/S et MSSQL est basé sur un proxy transparent au niveau de l’application et sur l’en-tête SNI. Par conséquent, il peut discerner deux noms de domaine complets qui sont résolus sur la même adresse IP. Ce n’est pas le cas avec le filtrage de nom de domaine complet dans les règles de réseau. Utilisez toujours les règles d’application lorsque cela est possible.
- Dans les règles d’application, vous pouvez utiliser HTTP/S et MSSQL comme protocoles sélectionnés. Dans les règles de réseau, vous pouvez utiliser n’importe quel protocole TCP/UDP avec vos noms de domaine complets de destination.

## <a name="next-steps"></a>Étapes suivantes

[Paramètres DNS du Pare-feu Azure](dns-settings.md)
