---
title: Niveaux tarifaires d’Azure Security Center
description: 'Azure Security Center est proposé en deux niveaux de service : Gratuit et Standard. Cette page indique comment effectuer la mise à niveau du niveau de service Gratuit vers le niveau de service Standard.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2019
ms.author: memildin
ms.openlocfilehash: 60a88e667918533f2c507846fa75b0e036ba5262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921281"
---
# <a name="upgrade-to-standard-tier-for-enhanced-security"></a>Passer au niveau Standard pour une sécurité renforcée
Azure Security Center fournit une gestion unifiée de la sécurité et une protection avancée contre les menaces pour les charges de travail s’exécutant dans Azure, en local et dans d’autres clouds. Il fournit une visibilité et un contrôle sur les charges de travail cloud hybrides, des défenses actives qui réduisent votre exposition aux menaces et une détection intelligente pour vous aider à suivre le rythme des cyberattaques en constante évolution.

## <a name="pricing-tiers"></a>Niveaux de tarification
Security Center est proposé en deux niveaux :

- Le niveau **Gratuit** est activé sur tous vos abonnements Azure après votre première consultation du tableau de bord Azure Security Center dans le Portail Azure. Il peut aussi être activé par programme via une API. Le niveau Gratuit vous donne accès à une stratégie de sécurité, à une fonctionnalité d'évaluation en continu de la sécurité et à des recommandations de sécurité exploitables pour vous aider à protéger vos ressources Azure.
- Le niveau **Standard** étend les fonctionnalités du niveau Gratuit aux charges de travail s’exécutant en privé et sur d’autres clouds publics, fournissant une gestion unifiée de la sécurité et une protection contre les menaces dans l’ensemble de vos charges de travail cloud hybrides. Le niveau Standard comprend également des capacités de protection contre les menaces, qui utilisent des fonctionnalités intégrées d’analytique des comportements et de Machine Learning pour identifier les attaques et les vulnérabilités zero-day, des contrôles d’accès et d’application pour réduire l’exposition aux attaques réseau et aux programmes malveillants, et bien plus encore. En outre, le niveau Standard ajoute une analyse des vulnérabilités pour vos machines virtuelles. Vous pouvez essayer le niveau Standard gratuitement. Le niveau Standard de Security Center prend en charge les ressources Azure, notamment les machines virtuelles, les groupes de machines virtuelles identiques, App Service, les serveurs SQL et les comptes de stockage. Si vous disposez d’Azure Security Center Standard, vous pouvez refuser la prise en charge en fonction du type de ressource. 

La plupart des évaluations de sécurité de niveau Gratuit pour les machines virtuelles, ainsi que la plupart des alertes de sécurité de niveau Standard, nécessitent l’installation de la capacité Microsoft Monitoring Agent (MMA). Vous pouvez activer l’approvisionnement automatique sur Security Center afin de déployer automatiquement l’agent pour vos machines virtuelles Azure.

## <a name="try-standard-tier-free-for-30-days"></a>Essayer gratuitement le niveau Standard pendant 30 jours
Le niveau Standard est gratuit les 30 premiers jours. Une fois ces 30 jours écoulés, si vous décidez de continuer à utiliser le service, votre utilisation est automatiquement facturée.

Vous pouvez mettre à niveau un abonnement Azure entier vers le niveau Standard, qui est hérité par toutes les ressources dans l’abonnement.

Pour obtenir le niveau Standard :

1. Dans le menu principal de **Security Center**, sélectionnez **Tarification et paramètres**.
2. Sélectionnez l’abonnement que vous souhaitez mettre à niveau vers la version Standard.
3. Sélectionnez **Niveau tarifaire**.
4. Sélectionnez **Standard** pour effectuer la mise à niveau.
5. Cliquez sur **Enregistrer**.

[![Tarification de Security Center](media/security-center-pricing/pricing-tier-page.png)](media/security-center-pricing/pricing-tier-page.png#lightbox)

> [!NOTE]
> Pour activer toutes les fonctionnalités de Security Center, vous devez appliquer le niveau tarifaire Standard à l’abonnement contenant les machines virtuelles concernées. La configuration du tarif pour un espace de travail n’active pas l’accès juste-à-temps à la machine virtuelle, les contrôles d’application adaptatifs et les détections réseau pour les ressources Azure.
>

## <a name="why-upgrade-to-standard"></a>Pourquoi passer au niveau Standard ?
Security Center offre une sécurité renforcée et une meilleure protection contre les menaces à vos charges de travail cloud hybrides, y compris :

- **Sécurité hybride** : obtenez une vue unifiée de la sécurité sur l’ensemble de vos charges de travail cloud et locales. Appliquez des stratégies de sécurité et évaluez en continu la sécurité de vos charges de travail cloud hybrides pour garantir la conformité aux normes de sécurité. Collectez, recherchez et analysez des données de sécurité à partir d’un large éventail de sources, dont les pare-feu et autres solutions partenaires.
- **Alertes de sécurité** : utilisez l’analytique avancée et Microsoft Intelligent Security Graph pour avoir un avantage sur les cyberattaques en constante évolution. Tirez parti des analytiques comportementales intégrées et de l’apprentissage machine pour identifier les attaques et les vulnérabilités zero-day. Surveillez les réseaux, machines et services cloud pour prévenir les attaques entrantes et les activités consécutives à une violation. Simplifiez l’investigation avec des outils interactifs et des informations sur les menaces contextuelles.
- **Analyse des vulnérabilités pour les machines virtuelles** : déployez facilement un scanneur sur toutes vos machines virtuelles qui offre la solution de gestion des vulnérabilités la plus avancée du secteur. Affichez, examinez et corrigez les résultats directement dans Security Center. 
- **Contrôles d’accès et d’application** : bloquez les programmes malveillants et les autres applications indésirables en appliquant des suggestions de mise en liste verte adaptées à vos charges de travail spécifiques et alimentées par Machine Learning. Réduisez la surface d’attaque réseau grâce à un accès contrôlé et juste à temps aux ports de gestion sur les machines virtuelles Azure. Cela réduit considérablement l’exposition à la force brute et à d’autres attaques réseau.
- **Fonctionnalités de sécurité des conteneurs** : tirez parti de la gestion des vulnérabilités et de la protection contre les menaces en temps réel sur vos environnements en conteneur. Lors de l’activation de la ressource des registres de conteneurs, 12 heures peuvent s’écouler avant que toutes les fonctionnalités soient activées.


## <a name="next-steps"></a>Étapes suivantes
Cet article vous a présenté la tarification de Security Center. Pour en savoir plus sur la sécurité renforcée et la protection avancée contre les menaces du niveau Standard, consultez :

- [Protection contre les menaces dans Azure Security Center](threat-protection.md)
- [Contrôle d’accès juste-à-temps à la machine virtuelle](security-center-just-in-time.md)
- [Aperçu de la sécurité des conteneurs](container-security.md)
- [Détails de la tarification dans la devise de votre choix et en fonction de votre région](https://azure.microsoft.com/pricing/details/security-center/)