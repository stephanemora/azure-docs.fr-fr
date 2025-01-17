---
title: Exemples de modèles Azure Resource Manager - Azure Front Door
description: Apprenez-en davantage sur les exemples de modèles Resource Manager pour Azure Front Door, notamment les modèles pour la création d’une instance Front Door de base et la configuration de la limitation du débit Front Door.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2021
ms.author: duau
ms.openlocfilehash: c76b78b8ed834b1cd3c86cab1bdbf5cb98141956
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130073237"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>Modèles de déploiement Azure Resource Manager pour Front Door

Le tableau suivant comprend des liens vers des modèles de déploiement Azure Resource Manager pour Azure Front Door.

| Modèle | Description |
| ---| ---|
| [Créer une porte d’entrée de base](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-create-basic)| Crée une configuration de porte d’entrée de base avec un seul backend. |
| [Créer une porte d’entrée avec plusieurs backends et pools de backends, et un routage basé sur les URL](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-create-multiple-backends)| Crée une porte d’entrée avec l’équilibrage de charge configuré pour plusieurs backends du pool de backends, ainsi qu’entre des pools de backends, basé sur les chemins d’URL. |
| [Intégrer un domaine personnalisé et un certificat TLS managé avec Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-custom-domain)| Ajoutez un domaine personnalisé à votre instance Front Door et utilisez un certificat TLS managé par Front Door. |
| [Intégrer un domaine personnalisé et un certificat TLS géré par le client avec Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-custom-domain-customer-certificate)| Ajoutez un domaine personnalisé à votre instance Front Door et utilisez votre propre certificat TLS à l’aide de Key Vault. |
| [Créer une porte d’entrée avec filtrage géographique](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-geo-filtering)| Créer une porte d’entrée qui autorise/bloque le trafic provenant de certains pays et de certaines régions. |
| [Contrôler les sondes d’intégrité pour vos backends sur Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-health-probes)| Mettez à jour votre porte d’entrée pour modifier les paramètres de sonde d’intégrité en changeant le chemin des sondes, ainsi que les intervalles auxquels les sondes sont envoyées. |
| [Créer une porte d’entrée avec une configuration de backend Active/En veille](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-priority-lb)| Crée une porte d’entrée qui montre le routage en fonction de la priorité pour la topologie d’application Actif/En veille, c’est-à-dire qui envoie par défaut tout le trafic au backend principal (priorité la plus élevée) jusqu’à ce qu’il devienne indisponible. |
| [Créer une porte d’entrée avec la mise en cache activée pour certaines routes](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-create-caching)| Crée une porte d’entrée avec la mise en cache activée pour la configuration de routage définie, ce qui a pour effet de mettre en cache les ressources statiques pour votre charge de travail. |
| [Configurer l’affinité de session pour les noms d’hôte de votre porte d’entrée](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-session-affinity) | Met à jour une porte d’entrée pour activer l’affinité de session pour l’hôte de votre frontend, envoyant ainsi le trafic résultant de la même session utilisateur au même backend. |
| [Configurer Front Door pour le placement en liste verte ou rouge d’adresses IP clientes](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-waf-clientip)| Configure une porte d’entrée pour limiter le trafic de certaines adresses IP avec un contrôle d’accès personnalisé basé sur les adresses IP clientes. |
| [Configurer Front Door pour effectuer une action avec des paramètres HTTP spécifiques](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-waf-http-params)| Configure une porte d’entrée pour autoriser ou bloquer un certain trafic en fonction des paramètres HTTP de la requête entrante, avec des règles personnalisées pour le contrôle d’accès en fonction de paramètres HTTP. |
| [Configurer la limitation du débit de Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-rate-limiting)| Configure une porte d’entrée pour limiter le débit du trafic entrant pour un hôte frontend donné. |
| | |

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).
- Découvrez [comment fonctionne Front Door](front-door-routing-architecture.md).
