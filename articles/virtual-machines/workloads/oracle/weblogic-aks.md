---
title: Quelles sont les solutions permettant d’exécuter Oracle WebLogic Server sur Azure Kubernetes Service ?
description: Découvrez comment exécuter Oracle WebLogic Server sur Azure Kubernetes Service.
author: rezar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 02/23/2021
ms.author: rezar
ms.reviewer: cynthn
ms.openlocfilehash: 63a902d57c8a3ec5d9876e0682674cdbea70a4cc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524196"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-the-azure-kubernetes-service"></a>Quelles sont les solutions permettant d’exécuter Oracle WebLogic Server sur Azure Kubernetes Service ?

Cette page décrit les solutions permettant d’exécuter Oracle WebLogic Server (WLS) sur Azure Kubernetes Service (AKS). Ces solutions sont le fruit d’un développement et bénéficient d’un support conjoints d’Oracle et de Microsoft.

Il est également possible d’exécuter WebLogic Server sur des machines virtuelles Azure. Pour ce faire, les solutions sont décrites dans [cet article de Microsoft](./oracle-weblogic.md).

WebLogic Server est un des principaux serveurs d’applications Java qui exécutent certaines des applications d’entreprise Java les plus critiques dans le monde entier. WebLogic Server forme la base de l’intergiciel (middleware) pour la suite de logiciels Oracle. Oracle et Microsoft s’engagent à donner aux clients WebLogic Server les choix et la flexibilité nécessaires pour exécuter des charges de travail sur Azure en tant que plateforme cloud de premier plan.

## <a name="wls-on-aks-certified-and-supported"></a>WLS sur AKS certifié et pris en charge
WebLogic Server est certifié par Oracle et Microsoft pour s’exécuter correctement sur AKS. Les solutions WebLogic Server sur AKS visent à faciliter autant que possible l’exécution de vos applications Java conteneurisées et orchestrées sur l’infrastructure Docker et Kubernetes. Les solutions sont axées sur la fiabilité, la scalabilité, la facilité de gestion et la prise en charge de l’entreprise.

Les clusters WebLogic Server sont entièrement compatibles pour s’exécuter sur Kubernetes à l’aide de l’opérateur WebLogic Kubernetes (appelé simplement « opérateur » à partir de là). L’opérateur suit le modèle de l’opérateur Kubernetes standard. Il simplifie la gestion et le fonctionnement des domaines et déploiements WebLogic sur Kubernetes en automatisant les tâches manuelles et en ajoutant des fonctionnalités de fiabilité opérationnelle supplémentaires. L’opérateur prend en charge Oracle WebLogic Server 12c, l’infrastructure d’intergiciel (middleware) Oracle Fusion 12c et les versions ultérieures. Nous avons testé les images Docker officielles pour WebLogic Server 12.2.1.3 et 12.2.1.4 avec l’opérateur. Pour plus d’informations sur l’opérateur, reportez-vous à la [documentation officielle d’Oracle](https://oracle.github.io/weblogic-kubernetes-operator/).

## <a name="guidance-scripts-and-samples-for-wls-on-aks"></a>Aide, scripts et exemples pour WLS sur AKS
Au-delà de la certification WebLogic Server sur AKS, Oracle et Microsoft fournissent conjointement des instructions détaillées, des scripts et des exemples pour l’exécution de WebLogic Server sur AKS. L’aide est incorporée dans la section de l’exemple Azure Kubernetes Service de la [documentation de l’opérateur](https://oracle.github.io/weblogic-kubernetes-operator/). L’aide est destinée à rendre les déploiements WebLogic Server sur AKS en production les plus simples possibles. L’aide utilise les images Docker WebLogic Server officielles fournies par Oracle. Le basculement est effectué avec Azure Files accessible via des revendications de volume persistant Kubernetes. Les équilibreurs Azure Load Balancer sont pris en charge lors du provisionnement à l’aide d’un service Kubernetes de type « LoadBalancer ». Azure Container Registry (ACR) est pris en charge pour le déploiement de domaines WLS dans des images Docker personnalisées. L’aide permet d’obtenir un degré élevé de configuration et de personnalisation.

:::image type="content" source="media/oracle-weblogic/wls-on-aks.gif" alt-text="Vous pouvez utiliser les exemples de scripts pour déployer WebLogic Server sur AKS":::

Les solutions incluent deux façons de déployer des domaines WLS sur AKS. Les domaines peuvent être déployés directement sur les volumes persistants Kubernetes. Cette option de déploiement est intéressante si vous souhaitez migrer vers AKS tout en conservant l’administration de WLS à l’aide de la console d’administration ou de WLST (WebLogic Scripting Tool). L’option vous permet également de passer à AKS sans adopter le développement de Docker. Le moyen se rapprochant le plus du moyen natif Kubernetes pour déployer des domaines WLS sur AKS consiste à créer des images Docker personnalisées basées sur des images WLS officielles à partir du Container Registry d’Oracle, à publier les images personnalisées dans ACR et à déployer le domaine sur AKS à l’aide de l’opérateur. Cette option dans la solution vous permet également de mettre à jour le domaine par l’intermédiaire de Kubernetes ConfigMaps une fois le déploiement terminé.

Une facilité d’utilisation et des intégrations de service Azure supplémentaires peuvent se produire à l’avenir via les offres de la Place de marché reflétant les solutions d’Oracle WebLogic Server sur des machines virtuelles Azure.

_Ces solutions sont des BYOL (apportez votre propre licence)_ . Elles supposent que vous avez déjà obtenu les licences appropriées auprès d’Oracle et que vous disposez d’une licence adéquate pour exécuter des offres dans Azure.

_Si vous souhaitez travailler étroitement sur vos scénarios de migration avec l’équipe d’ingénierie développant ces solutions, répondez à [ce court sondage](https://aka.ms/wls-on-azure-survey) et incluez vos coordonnées_. Des responsables, des architectes et des ingénieurs du programme reviendront rapidement vers vous pour démarrer une étroite collaboration. L’opportunité de collaborer sur un scénario de migration est gratuite pendant que les solutions sont en cours de développement initial.

## <a name="deployment-architectures"></a>Architectures de déploiement

Les solutions d’exécution d’Oracle WebLogic Server sur Azure Kubernetes Service offriront une large gamme d’architectures de déploiement prêtes pour la production, avec une relative facilité d’utilisation.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-aks.png" alt-text="Les déploiements complexes de WebLogic Server sont activés sur AKS":::

Au-delà de ce que les solutions offrent, les clients ont une flexibilité totale pour personnaliser davantage leurs déploiements. Il est probable qu’en plus de déployer des applications, les clients vont intégrer d’autres ressources Azure à leurs déploiements. Les clients sont encouragés à fournir des commentaires visant à l’amélioration des solutions lorsqu’ils répondent au sondage.

## <a name="next-steps"></a>Étapes suivantes

Découvrez l’exécution d’Oracle WebLogic Server sur Azure Kubernetes Service.

> [!div class="nextstepaction"]
> [Aide, scripts et exemples pour l’exécution de WLS sur AKS](https://oracle.github.io/weblogic-kubernetes-operator/)

> [!div class="nextstepaction"]
> [Opérateur WebLogic Kubernetes](https://oracle.github.io/weblogic-kubernetes-operator/)
