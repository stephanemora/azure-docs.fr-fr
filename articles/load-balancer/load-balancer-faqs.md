---
title: Forum aux questions (FAQ) - Azure Load Balancer
description: Réponses aux questions fréquemment posées sur Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 94a2398879007e7ecd6d2f1920157eb4627f33cb
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84014925"
---
# <a name="frequently-asked-questions"></a>Forum aux questions

## <a name="what-types-of-load-balancer-exist"></a>Quels types d’instances Load Balancer existent ?
Les équilibreurs de charge internes qui équilibrent le trafic au sein d’un réseau virtuel et les équilibreurs de charge externes qui équilibrent le trafic vers et à partir d’un point de terminaison connecté à Internet. Pour plus d’informations, consultez [Types de Load balancer](components.md#frontend-ip-configurations). 

Pour ces deux types, Azure propose une référence SKU de base et une référence SKU standard qui ont des fonctionnalités de suivi d’intégrité, de performances et de sécurité différentes. Ces différences sont expliquées dans notre article [Comparaison des références SKU](skus.md).

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>Comment puis-je effectuer une mise à niveau de Load Balancer Basic vers Standard Load Balancer ?
Pour obtenir un script automatisé et des conseils sur la mise à niveau d’une référence Load Balancer, consultez l’article [Mise à niveau de l’instance de base vers l’instance standard](upgrade-basic-standard.md).

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Quelles sont les différentes options d’équilibrage de charge dans Azure ?
Consultez le [guide de la technologie de l’équilibreur de charge](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) pour connaître les services d’équilibrage de charge disponibles et les utilisations recommandées pour chacun d’entre eux.

## <a name="where-can-i-find-load-balancer-arm-templates"></a>Où puis-je trouver les modèles ARM de Load Balancer ?
Consultez la [liste des modèles de démarrage rapide Azure Load Balancer](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers#quickstart-templates) pour les modèles ARM de déploiements courants.

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>Quelle est la différence entre les règles NAT entrantes et les règles d’équilibrage de charge ?
Les règles NAT sont utilisées pour spécifier une ressource backend vers laquelle acheminer le trafic. Par exemple, la configuration d’un port d’équilibrage de charge spécifique pour envoyer le trafic RDP vers une machine virtuelle spécifique. Les règles d’équilibrage de charge sont utilisées pour spécifier un pool de ressources backend vers lequel acheminer le trafic, en équilibrant la charge sur chaque instance. Par exemple, une règle d’équilibreur de charge peut acheminer les paquets TCP sur le port 80 de l’équilibreur de charge sur un pool de serveurs Web.

## <a name="what-is-ip-1686312916"></a>Qu’est-ce que l’adresse IP 168.63.129.16 ?
Adresse IP virtuelle de l’hôte marquée en tant qu’infrastructure Azure Load Balancer d’où proviennent les sondes d’intégrité Azure. Lors de la configuration d’instances backend, elles doivent autoriser le trafic à partir de cette adresse IP pour répondre correctement aux sondes d’intégrité. Cette règle n’interagit pas avec l’accès à votre frontend Load Balancer. Vous pouvez remplacer cette règle si vous n’utilisez pas Azure Load Balancer. Apprenez-en davantage sur les balises de service [ici](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags).

## <a name="next-steps"></a>Étapes suivantes
Si votre question ne figure pas dans la liste ci-dessus, veuillez envoyer vos commentaires sur cette page avec votre question. Cela créera un problème GitHub pour l’équipe produit afin de s’assurer que toutes les questions des clients reçoivent une réponse.
