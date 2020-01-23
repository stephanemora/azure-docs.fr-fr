---
title: Détection des menaces pour le calcul natif cloud dans Azure Security Center | Microsoft Docs
description: Cet article présente les alertes de calcul natif cloud disponibles dans Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: a85ec565077ac229cd24bb7b9cf753015aa56ebf
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76024858"
---
# <a name="threat-detection-for-cloud-native-computing-in-azure-security-center"></a>Détection des menaces pour le calcul natif cloud dans Azure Security Center

La solution native Azure Security Center offre une visibilité unique sur les journaux internes qui facilite l’identification de la méthodologie d’attaque sur différentes cibles. Cet article présente les alertes disponibles pour les services Azure suivants :

* [Azure App Service](#app-services)
* [Conteneurs Azure](#azure-containers) 

> [!NOTE]
> Ces services ne sont pas disponibles actuellement dans les régions Azure Government et de cloud souverain.

## Azure App Service <a name="app-services"></a>

Security Center utilise l’échelle du cloud pour identifier les attaques ciblant les applications exécutées sur App Service. Les attaquants sondent les applications web pour trouver et exploiter les faiblesses. Avant d’être routées vers des environnements spécifiques, les demandes adressées aux applications s’exécutant dans Azure passent par plusieurs passerelles, où elles sont inspectées et journalisées. Ces données sont ensuite utilisées pour identifier les codes malveillants exploitant une faille de sécurité ainsi que les attaquants, et pour découvrir de nouveaux modèles qui seront utilisés ultérieurement.

En utilisant la visibilité dont Azure bénéficie en tant que fournisseur de cloud, Security Center analyse les journaux internes d’App Service pour identifier la méthodologie d’attaque sur plusieurs cibles. Les attaques distribuées et les attaques par analyse généralisée sont des exemples de méthodologie. En général, ce type d’attaque provient d’un petit sous-ensemble d’adresses IP, et présente des modèles de progression vers des points de terminaison similaires sur plusieurs hôtes. Les attaques recherchent une page ou un plug-in vulnérable et ne peuvent pas être identifiées du point de vue d’un seul hôte.

Si vous exécutez un plan App Service Windows, Security Center a également accès aux machines virtuelles et aux bacs à sable sous-jacents. Couplée aux données de journal mentionnées ci-dessus, l’infrastructure peut raconter l’histoire, depuis une nouvelle attaque lâchée dans la nature à la compromission des machines des clients. Par conséquent, même si Security Center est déployé après qu’une application web a été attaquée, il est capable de détecter les attaques en cours.

Pour obtenir la liste des alertes Azure App Service, consultez la [table de référence des alertes](alerts-reference.md#alerts-azureappserv).

## Conteneurs Azure <a name="azure-containers"></a>

Security Center assure une détection des menaces en temps réel pour vos environnements conteneurisés et génère des alertes en cas d’activités suspectes. Vous pouvez utiliser ces informations pour remédier rapidement aux problèmes de sécurité et améliorer la sécurité de vos conteneurs.

Nous détectons les menaces à différents niveaux : 

* **Au niveau de l’hôte** : l’agent de Security Center (disponible avec le niveau tarifaire Standard ; consultez les [tarifs](security-center-pricing.md) pour plus d’informations) supervise Linux pour détecter les activités suspectes. L’agent déclenche des alertes s’il détecte des activités suspectes réalisées à partir du nœud ou d’un conteneur qui y est exécuté. Parmi ces activités, citons la détection de shell web et la connexion avec des adresses IP suspectes connues.

    Pour fournir des insights plus complets sur la sécurité de votre environnement conteneurisé, l’agent supervise l’analytique des conteneurs. Il déclenche des alertes sur des événements tels que la création de conteneurs privilégiés, l’accès suspect à des serveurs d’API et à des serveurs Secure Shell (SSH) s’exécutant dans un conteneur Docker.

    >[!NOTE]
    > Si vous choisissez de ne pas installer les agents sur vos machines hôtes, vous ne recevrez qu’une partie des avantages et alertes de détection des menaces. Vous recevrez toujours les alertes liées à l’analyse réseau et aux communications avec des serveurs malveillants.

    Pour obtenir la liste des alertes au niveau de l’hôte, consultez la [table de référence des alertes](alerts-reference.md#alerts-containerhost).


* **Au niveau du cluster AKS**, une supervision de la détection des menaces est effectuée sur la base de l’analyse des journaux d’audit Kubernetes. Pour activer cette supervision **sans agent**, ajoutez l’option Kubernetes à votre abonnement à partir de la page **Tarification et paramètres** (voir les [tarifs](security-center-pricing.md)). Pour générer des alertes à ce niveau, Security Center supervise vos services managés par AKS à l’aide des journaux collectés par AKS. Les tableaux de bord Kubernetes exposés, la création de rôles à privilèges élevés et la création de montages sensibles sont des exemples d’événements à ce niveau.

    >[!NOTE]
    > Security Center génère des alertes de détection pour les déploiements et les actions d’Azure Kubernetes Service qui se produisent après l’activation de l’option Kubernetes dans les paramètres de l’abonnement. 

    Pour obtenir la liste des alertes au niveau du cluster AKS, consultez la [table de référence des alertes](alerts-reference.md#alerts-akscluster).

De plus, notre équipe mondiale d’experts en sécurité surveille en permanence l’évolution des menaces. Ils ajoutent des alertes et des vulnérabilités propres aux conteneurs au fur et à mesure de leur découverte.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les plans App Service, consultez [Plans App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).