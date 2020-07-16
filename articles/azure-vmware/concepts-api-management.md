---
title: Concepts - Gestion des API
description: Découvrez comment la Gestion des API protège les API s’exécutant sur des machines virtuelles de la solution VMware Azure (AVS)
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 62112bf3c0bf551232e09e5910e3eaae228dc202
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85306749"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-avs-based-vms"></a>Gestion des API pour publier et protéger des API exécutées sur des machines virtuelles basées sur AVS

La [Gestion des API](https://azure.microsoft.com/services/api-management/) Microsoft Azure permet aux développeurs et aux équipes DevOps de publier en toute sécurité des consommateurs internes ou externes.

Bien qu’elles soient proposées dans plusieurs références SKU, seules les SKU Développeur et Premium permettent à Azure Virtual Network Intégration de publier des API s’exécutant sur des charges de travail de solution VMware Azure (AVS). Ces deux références SKU activent en toute sécurité la connectivité entre le service de Gestion des API et le principal. La référence SKU Développeur est destinée au développement et aux tests, tandis que la référence SKU Premium est destinée aux déploiements de production.

Pour les services principaux qui s’exécutent par-dessus les machines virtuelles AVS, la configuration dans la Gestion des API, par défaut, est la même que les services principaux locaux. Pour les déploiements internes et externes, la Gestion des API configure l’adresse IP virtuelle (VIP) de l’équilibreur de charge en tant que point de terminaison principal lorsque le serveur principal est placé derrière un équilibreur de charge NSX du côté AVS.

## <a name="external-deployment"></a>Déploiement externe

Un déploiement externe publie les API consommées par les utilisateurs externes à l’aide d’un point de terminaison public. Les développeurs et les ingénieurs DevOps peuvent gérer les API via le Portail Azure ou PowerShell et le portail des développeurs de la Gestion des API.

Le diagramme de déploiement externe montre l’ensemble du processus et des acteurs impliqués (affiché en haut). Les acteurs sont les suivants :

- **Administrateur(s) :** Représente l’équipe d’administrateurs ou DevOps qui gère AVS via le Portail Azure et les mécanismes d’automatisation tels que PowerShell ou Azure DevOps.

- **Utilisateurs :**  Représente les consommateurs des API exposées et représente à la fois les utilisateurs et les services qui consomment les API.

Le flux de trafic passe par l’instance de Gestion des API, qui soustrait les services principaux, branchés au réseau virtuel Hub. La passerelle ExpressRoute achemine le trafic vers le canal ExpressRoute Global Reach et atteint un équilibreur de charge NSX distribuant le trafic entrant aux différentes instances de service principales.

La Gestion des API a une API publique Azure et l’activation du service de protection DDOS Azure est recommandée. 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="Déploiement externe - Gestion des API pour AVS":::


## <a name="internal-deployment"></a>Déploiement interne

Un déploiement interne publie des API consommées par des utilisateurs ou des systèmes internes. L’équipe DevOps et les développeurs d’API utilisent les mêmes outils de gestion et le même portail des développeurs que dans le cadre du déploiement externe.

Les déploiements internes peuvent être [avec Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) pour créer un point de terminaison public et sécurisé pour l’API en tirant parti de ses fonctionnalités et en créant un déploiement hybride qui permet différents scénarios.  L’API tire parti de ses fonctionnalités et crée un déploiement hybride qui permet différents scénarios.

* Utilisez la même ressource de Gestion des API pour la consommation à la fois par les consommateurs internes et externes.

* Utilisez une seule ressource de Gestion des API avec un sous-ensemble d’API défini et disponible pour les consommateurs externes.

* Fournissez un moyen simple pour activer et désactiver l’accès à la Gestion des API à partir de l’Internet public.

Le diagramme de déploiement ci-dessous montre les consommateurs qui peuvent être internes ou externes, chaque type accédant à des API identiques ou différentes.

Dans un déploiement interne, les API sont exposées à la même instance de Gestion des API. Devant la Gestion des API, Application Gateway est déployée avec la fonctionnalité de pare-feu d’applications web (WAF) Azure activée et un ensemble d’écouteurs HTTP et de règles pour filtrer le trafic, en exposant uniquement un sous-ensemble des services principaux exécutés sur AVS.

* Le trafic interne est acheminé via la passerelle ExpressRoute vers le Pare-feu Azure, puis vers la Gestion des API si les règles de trafic sont établies ou directement dans la Gestion des API.  

* Le trafic externe entre dans Azure via Application Gateway, qui utilise la couche de protection externe pour la Gestion des API.


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="Déploiement interne - Gestion des API pour AVS":::