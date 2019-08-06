---
title: Fournir une preuve de concept - Azure DevTest Labs | Microsoft Docs
description: Découvrez comment fournir une preuve de concept visant à bien intégrer Azure DevTest Labs dans un environnement d’entreprise.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: takamath
ms.openlocfilehash: 6739679b1687393737dda1ded4265a95c4fce169
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501603"
---
# <a name="delivering-a-proof-of-concept"></a>Fournir une preuve de concept 

L’un des principaux scénarios d'Azure DevTest Labs consiste à permettre des environnements de développement et de test dans le cloud. Voici quelques exemples :

* créer des bureaux de développeur dans le cloud,
* configurer des environnements de test,
* activer l’accès aux machines virtuelles et autres ressources Azure,
* configurer une zone de bac à sable pour permettre aux développeurs d'apprendre et d'expérimenter.

DevTest Labs fournit également des rails de sécurité pour les stratégies et des contrôles des coûts permettant à l’entreprise de proposer un « libre-service Azure» aux développeurs qui adhèrent aux stratégies de l'entreprise en matière de sécurité, de réglementation et de conformité. 

Chaque entreprise a des exigences différentes en termes d'intégration d'Azure DevTest Labs à son environnement. Cet article décrit les principales étapes que les entreprises doivent généralement suivre pour fournir une preuve de concept, qui constitue la première étape vers un déploiement réussi de bout en bout. 

## <a name="getting-started"></a>Mise en route 

Pour commencer à fournir une preuve de concept. Il est important de prendre le temps de découvrir Azure et DevTest Labs.  Pour ce faire, voici quelques ressources utiles : 

* [Compréhension du portail Azure](https://azure.microsoft.com/features/azure-portal/)
* [Notions de base sur DevTest Labs](devtest-lab-overview.md)
* [Scénarios pris en charge par DevTest Labs](devtest-lab-guidance-get-started.md)
* [Documentation DevTest Labs pour les entreprises](devtest-lab-guidance-prescriptive-adoption.md)
* [Présentation de la mise en réseau Azure](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Prérequis 

Pour mener à bien un pilote ou une preuve de concept avec DevTest Labs, il existe plusieurs conditions préalables requises : 

* **Abonnement Azure** : Les entreprises disposent souvent d'un [Contrat Entreprise ](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) qui permet d'accéder à Azure, et un abonnement existant ou nouveau peut être utilisé pour DevTest Labs. Un [abonnement Visual Studio](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) peut également être utilisé pendant le pilote (en tirant parti des crédits Azure gratuits). Si aucune de ces options n’est disponible, un [compte Azure gratuit](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ) peut être créé et utilisé. Si un Contrat Entreprise est en place, l'utilisation d'un [ abonnement Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) est une excellente option pour accéder aux systèmes d'exploitation clients Windows 10/Windows 8.1 et bénéficier de tarifs réduits pour les charges de travail de développement et de test. 
* **Locataire Azure Active Directory** :  Pour activer la gestion des utilisateurs (ajout d'utilisateurs ou de propriétaires de lab, par exemple), ces utilisateurs doivent faire partie du [locataire Azure Active Directory](https://azure.microsoft.com/services/active-directory/) utilisé dans l'abonnement Azure pour le pilote. Les entreprises configurent souvent l'[identité hybride ](../active-directory/hybrid/whatis-hybrid-identity.md) pour permettre aux utilisateurs d'exploiter leur identité locale dans le cloud, ce qui n'est pas nécessaire pour le pilote DevTest Labs. 

## <a name="scoping-of-the-pilot"></a>Étendue du pilote 

Il est très important de planifier un pilote adapté avant de procéder à l’implémentation. Savoir à l’avance que les ressources ne seront pas conservées indéfiniment permet de définir des attentes claires pour les utilisateurs du pilote. 

> [!IMPORTANT]
> Nous n'insisterons jamais assez sur l'importance de bien définir l'étendue du pilote et les attentes en amont.

Avant de lancer le pilote, il convient de répondre à quelques questions clés : 

* Que souhaitez-vous apprendre et comment envisagez-vous la réussite du pilote ? 
* Quels scénarios ou charges de travail le pilote couvrira-t-il ? Il est important d'en limiter le nombre pour bien définir le pilote et le mener à bien rapidement. 
* Quelles ressources doivent être disponibles dans le lab ? Par exemple : images personnalisées, images de place de marché, stratégies, topologie de mise en réseau, etc. 
* Qui sont les utilisateurs finaux/équipes impliqués dans le pilote pour vérifier l’expérience ?  
* Quelle est la durée du pilote ? Choisissez une plage de temps adaptée à l'étendue planifiée, comme deux semaines ou un mois. 
* Une fois le pilote terminé, que deviendront les ressources correspondantes allouées et utilisées ? Envisagez-vous de supprimer les ressources du pilote ?
   
   S'il est possible de supprimer les machines virtuelles et labs au terme du pilote, il est possible de configurer un abonnement unique pour le pilote et d'y effectuer notre travail tout en résolvant les questions de déploiement d'échelle en parallèle. 

Il est courant de vouloir créer le pilote « parfait » afin qu’il représente de manière précise l'état final du service une fois celui-ci déployé dans l’entreprise. Ce n'est pas la meilleure solution. Plus vous visez la « perfection », plus votre charge de travail est importante *avant* de pouvoir utiliser le pilote. L'objectif du pilote consiste à prendre les bonnes décisions en matière d'extension et de déploiement du service final. 

Le pilote doit permettre de choisir les charges de travail et dépendances minimales requises afin de déterminer si Azure DevTest Labs est adapté à votre entreprise. Pour le bon déroulement du pilote, il est conseillé de choisir la charge de travail la plus simple dotée du moins dépendances possible. Si ce n'est pas possible, il convient de choisir la charge de travail la plus représentative en termes de complexités potentielles pour permettre de répliquer la phase pilote lors de la phase de scale-out. 

L’exemple suivant illustre une preuve de concept bien définie. 

## <a name="example-proof-of-concept-plan"></a>Exemple : plan de preuve de concept 

Cette section présente un exemple à suivre afin de définir une preuve de concept de pilote pour DevTest Labs. 

> [!TIP]
> Pour configurer un projet viable, nous vous recommandons vivement de ne pas ignorer l’exemple décrit dans cette section. 

### <a name="overview"></a>Vue d'ensemble 

Nous envisageons de développer un nouvel environnement Azure basé sur DevTest Labs dont les fournisseurs se serviront en tant qu'environnement isolé du réseau d’entreprise. Pour savoir si la solution répondra aux exigences, nous allons développer une preuve de concept afin de valider la solution de bout en bout, et avons inclus plusieurs fournisseurs pour expérimenter et vérifier l'expérience. 

### <a name="outcomes"></a>Résultats 

Lors de la création d’une preuve de concept, nous nous concentrons d’abord sur les résultats (ce que nous essayons d’atteindre), répertoriés ici.  Au terme de la preuve de concept, nous espérons obtenir ce qui suit : 

* Solution opérationnelle de bout en bout permettant aux fournisseurs de tirer parti des comptes invités dans Azure Active Directory (Azure AD) pour accéder à un environnement isolé dans Azure, doté des ressources requises pour être productifs. 
* Tous les problèmes de blocage potentiels ayant un impact sur une utilisation et une adoption à plus grande échelle sont énumérés et compris.
* Les personnes chargées de développer la preuve de concept ont une bonne compréhension du code. Elles comprennent également les éléments collatéraux impliqués et se montrent confiantes quant à une plus large adoption.

### <a name="open-questions--prerequisites"></a>Questions ouvertes et conditions préalables 

* Disposons-nous d'un abonnement que nous pourrions utiliser pour ce projet ? 
* Disposons-nous d'un locataire Azure AD et d'un administrateur général Azure AD identifié capable de fournir aide et conseils pour les questions liées à Azure AD ? 
* Un espace de collaboration pour les personnes travaillant sur le projet : 

   * Code source et scripts (Azure Repos, par exemple) 
   * Documents (Teams ou SharePoint, par exemple)  
   * Conversations (Microsoft Teams, par exemple) 
   * Éléments de travail (Azure Boards, par exemple) 
* Quelles sont les ressources requises pour les fournisseurs ? Cela englobe les machines virtuelles et autres serveurs requis, applications disponibles sur le réseau. 
* Les machines virtuelles seront-elles jointes à un domaine dans Azure ? Si tel est le cas, s'agira-t-il d'Azure AD Domain Services ou d'autre chose ? 
* L’équipe/les fournisseurs ont-ils été identifiés comme cible de la preuve de concept ? Quels seront les clients de l’environnement ?
* Quelle région Azure utiliserons-nous pour la preuve de concept ? 
* Disposons-nous de la liste des services que les fournisseurs sont autorisés à utiliser via DevTest Labs en plus de IaaS (machines virtuelles) ? 
* Comment envisageons-nous de former les fournisseurs/utilisateurs du lab ? 

### <a name="proof-of-concept-solution-components"></a>Composants de la solution de preuve de concept 

Nous attendons de la solution les composants suivants : 

* Ensemble de DevTest Labs dans Azure pour différentes équipes de fournisseurs.
* Les labs sont connectés à une infrastructure de mise en réseau qui prend en charge la configuration requise.
* Les fournisseurs ont accès aux labs via Azure AD et des rôles sont attribués au niveau des labs.
* Méthode permettant aux fournisseurs de se connecter à leurs ressources. Plus précisément, un VPN de site à site pour permettre l’accès direct aux machines virtuelles, sans adresses IP publiques.
* Ensemble d’artefacts couvrant les logiciels requis par les fournisseurs sur les machines virtuelles.

## <a name="additional-planning-and-design-decisions"></a>Décisions supplémentaires relatives à la planification et à la conception 

Avant de publier une solution DevTest Labs complète, il vous faut prendre plusieurs décisions importantes relatives à la planification et à la conception. Votre expérience en matière de preuve de concept peut vous y aider. Autres points à prendre en compte : 

* **Topologie de l’abonnement** : Au niveau de l'entreprise, les ressources requises dans Azure peuvent s'étendre au-delà des [quotas disponibles au sein d'un même abonnement](https://docs.microsoft.com/azure/azure-subscription-service-limits), ce qui implique plusieurs abonnements Azure et/ou requêtes de service pour augmenter les limites de l’abonnement initial. Il est important de décider à l’avance comment distribuer les ressources entre les abonnements et pour ce faire, le [guide de décision concernant les abonnements](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) est très utile, car il est difficile de déplacer des ressources vers un autre abonnement ensuite. À titre d'exemple, un lab DevTest ne peut pas être déplacé vers un autre abonnement après avoir été créé.  
* **Topologie de mise en réseau** : L'[infrastructure de mise en réseau par défaut](../app-service/networking-features.md) créée automatiquement par DevTest Labs peut ne pas répondre pleinement aux exigences et contraintes des utilisateurs d’entreprise. Il est courant de rencontrer des [réseaux virtuels connectés à ExpressRoute](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/), des [réseaux en étoile](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) pour une connectivité entre les abonnements, et même un [routage forcé](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) offrant une connectivité locale uniquement. DevTest Labs peut connecter les réseaux virtuels existants au lab pour permettre leur utilisation lors de la création de nouvelles machines virtuelles dans ce lab. 
* **Accès à distance aux machines virtuelles** : De nombreuses options permettent d'accéder à distance aux machines virtuelles situées dans DevTest Labs. Le plus simple consiste à utiliser des adresses IP publiques ou adresses IP partagées ; ces [paramètres sont disponibles dans le lab](devtest-lab-shared-ip.md). Si ces options ne suffisent pas, l'utilisation d’une passerelle d’accès à distance est également possible comme illustré dans [l'architecture de référence d'entreprise DevTest Labs](devtest-lab-reference-architecture.md) et décrit en détail dans la [documentation relative à la passerelle Bureau à distance DevTest Labs](configure-lab-remote-desktop-gateway.md). Les entreprises peuvent également utiliser Express route ou un VPN site à site pour connecter leur instance DevTest Labs à leur réseau local. Cela permet de connecter directement le Bureau à distance ou les connexions SSH aux machines virtuelles en fonction de leur adresse IP privée, sans aucune exposition à Internet. 
* **Gestion des autorisations** : Les deux autorisations clés couramment utilisées dans DevTest Labs sont [« Propriétaire » et « Utilisateur de lab »](devtest-lab-add-devtest-user.md). Avant de déployer DevTest Labs à grande échelle, il est important de décider qui sera en charge de chaque niveau d’accès du lab. Souvent, le chargé du budget (responsable d'équipe, par exemple) est le propriétaire du lab et les membres de l'équipe, les utilisateurs du lab. La personne (responsable d’équipe) en charge du budget peut ainsi ajuster les paramètres de stratégie pour gérer le budget de l'équipe.  

## <a name="completing-the-proof-of-concept"></a>Finalisation de la preuve de concept 

Une fois ces différents points abordés, le moment est venu de conclure et de finaliser le pilote. C’est le moment de recueillir les commentaires des utilisateurs, de déterminer si le pilote a réussi et de décider si l'organisation déploiera DevTest Labs à grande échelle dans l'entreprise. C’est également le moment idéal pour envisager le déploiement automatisé de DevTest Labs et des ressources associées afin de garantir une certaine cohérence tout au long du déploiement. 

## <a name="next-steps"></a>Étapes suivantes 

* [Documentation DevTest Labs pour les entreprises](devtest-lab-guidance-prescriptive-adoption.md)
* [Architecture de référence pour une entreprise](devtest-lab-reference-architecture.md)
* [Monter en puissance votre déploiement DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
* [Orchestrer l’implémentation d’Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
