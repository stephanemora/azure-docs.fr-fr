---
title: Niveaux tarifaires d’Azure Security Center
description: Azure Security Center est proposé en deux modes, avec et sans Azure Defender.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: a01d4137217aa594d4636f3338d3f33dc03cc836
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91713736"
---
# <a name="pricing-of-azure-security-center"></a>Niveaux tarifaires d’Azure Security Center
Azure Security Center fournit une gestion unifiée de la sécurité et une protection avancée contre les menaces pour les charges de travail s’exécutant dans Azure, en local et dans d’autres clouds. Il fournit une visibilité et un contrôle sur les charges de travail cloud hybrides, des défenses actives qui réduisent votre exposition aux menaces et une détection intelligente pour vous aider à suivre le rythme des cyberattaques en constante évolution.


## <a name="free-option-vs-azure-defender-enabled"></a>Option gratuite contre Azure Defender activé

Security Center est proposé en deux modes :

- **Azure Defender désactivé** (gratuit) : Security Center sans Azure Defender est activé gratuitement sur tous vos abonnements Azure quand vous consultez pour la première fois le tableau de bord Azure Security Center dans le portail Azure. Il peut aussi être activé par programmation par le biais d’une API. Ce mode vous donne accès à une stratégie de sécurité, à une fonctionnalité d'évaluation en continu de la sécurité et à des recommandations de sécurité exploitables pour vous aider à protéger vos ressources Azure.

- **Azure Defender activé** : Activer Azure Defender étend les fonctionnalités du mode Gratuit aux charges de travail exécutées dans des clouds privés et dans d’autres clouds publics, pour une gestion unifiée de la sécurité et une protection contre les menaces sur l’ensemble des charges de travail de cloud hybride. Voici quelques-unes des principales fonctionnalités d’Azure Defender :

    - **Sécurité hybride** : obtenez une vue unifiée de la sécurité sur l’ensemble de vos charges de travail cloud et locales. Appliquez des stratégies de sécurité et évaluez en continu la sécurité de vos charges de travail cloud hybrides pour garantir la conformité aux normes de sécurité. Collectez, recherchez et analysez des données de sécurité à partir d’un large éventail de sources, dont les pare-feu et autres solutions partenaires.
    - **Alertes de protection contre les menaces** : les analyses comportementales avancées et Microsoft Intelligent Security Graph offrent une longueur d’avance face à l’évolution des attaques informatiques. Tirez parti des analytiques comportementales intégrées et de l’apprentissage machine pour identifier les attaques et les vulnérabilités zero-day. Surveillez les réseaux, machines et services cloud pour prévenir les attaques entrantes et les activités consécutives à une violation. Simplifiez l’investigation avec des outils interactifs et des informations sur les menaces contextuelles.
    - **Analyse des vulnérabilités pour les machines virtuelles et registres de conteneurs** : déployez facilement un scanneur sur toutes vos machines virtuelles qui offre la solution de gestion des vulnérabilités la plus avancée du secteur. Affichez, examinez et corrigez les résultats directement dans Security Center. 
    - **Contrôles d’accès et d’application** : bloquez les programmes malveillants et les autres applications indésirables en appliquant des suggestions de mise en liste verte/noire adaptées à vos charges de travail spécifiques et alimentées par Machine Learning. Réduisez la surface d’attaque réseau grâce à un accès contrôlé et juste à temps aux ports de gestion sur les machines virtuelles Azure. Cela réduit considérablement l’exposition à la force brute et à d’autres attaques réseau.
    - **Fonctionnalités de sécurité des conteneurs** : tirez parti de la gestion des vulnérabilités et de la protection contre les menaces en temps réel sur vos environnements en conteneur. Lors de l’activation d’**Azure Defender pour les registres de conteneurs**, 12 heures peuvent s’écouler avant que toutes les fonctionnalités soient activées. Les frais sont basés sur le nombre d’images conteneur uniques transmises à votre registre connecté. Une fois qu’une image a été analysée une fois, elle ne sera plus facturée à moins qu’elle ne soit modifiée et transmise une fois de plus. 

## <a name="try-azure-defender-free-for-30-days"></a>Essayer Azure Defender gratuitement pendant 30 jours

Azure Defender est gratuit pendant les 30 premiers jours. Une fois les 30 jours écoulés, si vous décidez de continuer à utiliser le service, votre utilisation est automatiquement facturée.

## <a name="enable-azure-defender"></a>Activer Azure Defender

Vous pouvez protéger l’intégralité d’un abonnement Azure avec Azure Defender et les protections seront héritées par toutes les ressources de l’abonnement.

Pour activer Azure Defender :

1. Dans le menu principal de Security Center, sélectionnez **Tarification et paramètres**.
1. Sélectionnez l’abonnement à mettre à niveau.
1. Sélectionnez **Azure Defender activé** pour mettre à niveau.
1. Sélectionnez **Enregistrer**.

Vous trouverez ci-dessous la page de tarification d’un exemple d’abonnement. Vous remarquerez que chaque plan dans Azure Defender est facturé séparément et peut être défini individuellement sur activé ou désactivé.

:::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="Page de tarification de Security Center dans le portail":::

> [!NOTE]
> Pour activer les fonctionnalités de protection contre les menaces de Security Center, vous devez activer Azure Defender sur l’abonnement contenant les charges de travail applicables. L’activation au niveau espace de travail espace de travail n’active pas l’accès juste-à-temps à la machine virtuelle, les contrôles d’application adaptatifs et les détections réseau pour les ressources Azure. De plus, les seuls plans Azure Defender disponibles au niveau de l’espace de travail sont Azure Defender pour les serveurs et Azure Defender pour les serveurs SQL sur les machines.
>
> Vous pouvez activer des **comptes Azure Defender pour le stockage** au niveau de l’abonnement ou de la ressource.
> Vous pouvez activer **Azure Defender pour SQL** au niveau de l’abonnement ou de la ressource.
> Vous pouvez activer la protection contre les menaces pour **Azure Database for MariaDB/Azure Database pour MySQL/Azure Database pour PostgreSQL** au niveau de la ressource uniquement.


## <a name="next-steps"></a>Étapes suivantes
Cet article vous a présenté la tarification de Security Center. Pour des documents connexes, consultez :

- [Optimisation des coûts de votre charge de travail Azure](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Détails de la tarification dans la devise de votre choix et en fonction de votre région](https://azure.microsoft.com/pricing/details/security-center/)
- Vous pouvez gérer vos coûts et limiter la quantité de données collectées pour une solution en limitant celle-ci à un ensemble spécifique d’agents. Le [ciblage de solution](../operations-management-suite/operations-management-suite-solution-targeting.md) vous permet d’appliquer une étendue à la solution et de cibler un sous-ensemble d’ordinateurs dans l’espace de travail. Si vous utilisez le ciblage de solution, Security Center répertorie l’espace de travail comme n’ayant pas de solution.