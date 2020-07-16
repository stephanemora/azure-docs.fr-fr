---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 06/30/2020
ms.topic: include
ms.openlocfilehash: ba695337a1da1e440895f6b9f6fe2eb34f5041e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800600"
---
Security Center fournit une protection contre les menaces en temps réel pour vos environnements conteneurisés, et génère des alertes en cas d’activités suspectes. Vous pouvez utiliser ces informations pour remédier rapidement aux problèmes de sécurité et améliorer la sécurité de vos conteneurs.

Security Center offre une protection contre les menaces à différents niveaux : 

* Au **niveau de l’hôte**, l’agent Log Analytics analyse Linux pour détecter les activités suspectes. L’agent déclenche des alertes s’il détecte des activités suspectes réalisées à partir du nœud ou d’un conteneur qui y est exécuté. Parmi ces activités, citons la détection de shell web et la connexion avec des adresses IP suspectes connues.

    Pour fournir des insights plus complets sur la sécurité de votre environnement conteneurisé, l’agent supervise l’analytique des conteneurs. Il déclenche des alertes sur des événements tels que la création de conteneurs privilégiés, l’accès suspect à des serveurs d’API et à des serveurs Secure Shell (SSH) s’exécutant dans un conteneur Docker.

    >[!IMPORTANT]
    > Si vous choisissez de ne pas installer les agents sur vos machines hôtes, vous ne recevrez qu’une partie des avantages et alertes de sécurité relatifs à la protection contre les menaces. Vous recevrez toujours les alertes liées à l’analyse réseau et aux communications avec des serveurs malveillants.

    Pour obtenir la liste des alertes au niveau de l’hôte AKS, consultez la [Table de référence des alertes](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost).


* Au **niveau du cluster AKS**, la protection contre les menaces est basée sur l’analyse des journaux d’audit de Kubernetes. Pour activer cette supervision **sans agent**, ajoutez l’option Kubernetes à votre abonnement à partir de la page **Tarification et paramètres** (voir les [tarifs](https://docs.microsoft.com/azure/security-center/security-center-pricing)). Pour générer des alertes à ce niveau, Security Center supervise vos services managés par AKS à l’aide des journaux collectés par AKS. Les tableaux de bord Kubernetes exposés, la création de rôles dotés de privilèges élevés et la création de montages sensibles sont des exemples d’événements à ce niveau.

    >[!NOTE]
    > Security Center génère des alertes de sécurité pour les actions et les déploiements Azure Kubernetes Service, qui se produisent après l’activation de l’option Kubernetes dans les paramètres de l’abonnement. 

    Pour obtenir la liste des alertes au niveau du cluster AKS, consultez la [table de référence des alertes](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster).

De plus, notre équipe mondiale d’experts en sécurité surveille en permanence l’évolution des menaces. Ils ajoutent des alertes et des vulnérabilités propres aux conteneurs au fur et à mesure de leur découverte.

> [!TIP]
> Vous pouvez simuler des alertes de conteneur en suivant les instructions données dans [ce billet de blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).