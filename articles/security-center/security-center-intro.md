---
title: Qu’est-ce que l’Azure Security Center ? | Microsoft Docs
description: Découvrez le Centre de sécurité Azure, ses fonctionnalités principales et son fonctionnement.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: rkarlin
ms.openlocfilehash: 1b0a6c6d6daa686404021afb2e10b25bb9f54191
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49319383"
---
# <a name="what-is-azure-security-center"></a>Qu’est-ce que le Centre de sécurité Azure ?
Azure Security Center fournit des fonctionnalités unifiées de gestion de la sécurité et de protection avancée contre les menaces sur l’ensemble des charges de travail cloud hybrides. Avec Security Center, vous pouvez appliquer des stratégies de sécurité sur l’ensemble de vos charges de travail, limiter votre exposition aux menaces, détecter et répondre aux attaques.

Pourquoi utiliser Security Center ?

- **Gestion de stratégie centralisée** : assurez la conformité aux exigences de sécurité obligatoires ou de votre société en gérant de façon centralisée les stratégies de sécurité dans toutes vos charges de travail cloud hybride.
- **Évaluation continue de la sécurité** : contrôlez la situation de sécurité des machines, réseaux, applications, services de stockage et de données pour découvrir d’éventuels problèmes de sécurité.
- **Recommandations exploitables** : corrigez les failles de sécurité avant qu’elles puissent être exploitées par des attaquants avec des recommandations de sécurité exploitables et classées par ordre de priorité.
- **Alertes et incidents classés par ordre de priorité** : concentrez-vous en premier lieu sur les menaces les plus importantes avec les incidents et alertes de sécurité classés par ordre de priorité.
- **Défenses cloud avancées** : minimisez les menaces avec un accès juste-à-temps aux ports de gestion, et des contrôles d’applications adaptatifs exécutés sur vos machines virtuelles.
- **Solutions de sécurité intégrées** : collectez, recherchez et analysez les données de sécurité d’un large éventail de sources, notamment les solutions partenaires connectées.

La **vue d’ensemble de Security Center** fournit un aperçu rapide de l’état de sécurité de vos charges de travail Azure et autres qu’Azure, ce qui vous permet de détecter et d’évaluer la sécurité de vos charges de travail, ainsi que d’identifier et d’atténuer les risques. Le tableau de bord intégré offre des insights instantanés sur les alertes de sécurité et les vulnérabilités nécessitant votre attention.

![Vue d’ensemble][1]

## <a name="centralized-policy-management"></a>Gestion de stratégie centralisée
La section **Stratégie et conformité** (développée ci-dessus) fournit des informations rapides sur la couverture de votre abonnement, la conformité à la stratégie et la situation de sécurité.

### <a name="subscription-coverage"></a>Couverture de l’abonnement
Cette section affiche le nombre total des abonnements auxquels vous avez accès (lecture ou écriture), et le niveau de couverture du Security Center (Standard ou Gratuit) sous lequel un abonnement est en cours :

- **Couverts (Standard)** – Les abonnements couverts s’exécutent sous le niveau maximal de la protection offerte par Security Center
- **Couverts (Gratuit)** – Les abonnements couverts s’exécutent sous le niveau gratuit et limité de la protection offerte par Security Center
- **Non couverts** – Les abonnements affichant cet état ne sont pas supervisés par Security Center

La sélection du graphique ouvre la fenêtre **Couverture**. La sélection d’un onglet (**Non couverts**, **Couverture De base**, ou **Couverture Standard**) fournit la liste des abonnements dans chaque état. La sélection d’un abonnement sous un de ces onglets donne des informations supplémentaires sur l’abonnement. Ces informations vous permettent d’identifier le propriétaire d’un abonnement, et de le contacter afin d’activer Security Center, ou d’augmenter la couverture de l’abonnement.

![Couverture de Security Center][9]

### <a name="policy-compliance"></a>Conformité à la stratégie
La conformité à la stratégie est déterminée par les facteurs de conformité de toutes les stratégies attribuées. Le score de conformité générale d’un groupe d’administration, d’un abonnement ou d’un espace de travail représente la moyenne pondérée des affectations. La moyenne pondérée prend en compte le nombre de stratégies dans une seule affectation et le nombre de ressources auxquelles l’affectation s’applique.

Par exemple, si votre abonnement contient deux machines virtuelles et une initiative de cinq stratégies qui lui est affectée, vous disposez de dix évaluations dans votre abonnement. Si l’une des machines virtuelles n’est pas conforme à deux des stratégies, le score de conformité générale de l’affectation de votre abonnement est de 80 %.

Cette section affiche votre taux de conformité générale, et vos abonnements les moins conformes. La sélection de **Affichez la conformité à la stratégie de votre environnement** ouvre la fenêtre **Gestion de stratégie**. La **Gestion de stratégie** affiche la structure hiérarchique des groupes d’administration, des abonnements et des espaces de travail. Ici, vous gérez vos stratégies de sécurité en choisissant un abonnement ou un groupe d’administration.

![Gestion des stratégies][10]

Une stratégie de sécurité définit la configuration souhaitée de vos charges de travail, tout en garantissant leur conformité aux exigences de sécurité réglementaires. Dans Security Center, vous définissez les stratégies et vous les adaptez à votre type de charge de travail ou à la sensibilité de vos données, en déterminant quels contrôles Security Center supervise et recommande. Vous pouvez modifier la stratégie de sécurité dans Security Center en cliquant sur un groupe d’administration ou un abonnement. Vous pouvez également utiliser Azure Policy pour créer de nouvelles définitions, définir des stratégies supplémentaires et affecter les stratégies dans des groupes d’administration.

Sélectionnez **Modifier les paramètres >** pour modifier les paramètres de Security Center suivants au niveau de l’abonnement, du groupe d’administration, du groupe de ressources ou de l’espace de travail :

- **Collecte de données** : permet de déterminer le provisionnement d’agent et les paramètres de la [collecte de données](security-center-enable-data-collection.md) de sécurité.
- **Notifications par e-mail** : permettent de déterminer les contacts de sécurité et les paramètres des [notifications par e-mail](security-center-provide-security-contact-details.md).
- **Niveau tarifaire** : permet de définir la [sélection d’un tarif](security-center-pricing.md) gratuit ou standard. Le niveau que vous choisissez détermine les fonctionnalités de Security Center qui sont disponibles pour les ressources visées.
- **Modifier les configurations de sécurité** : vous permet de voir et de modifier les configurations de système d’exploitation qui sont évaluées par Security Center pour identifier les failles de sécurité potentielles.

Pour plus d’informations, consultez la page [Intégrer des stratégies de sécurité Security Center avec Azure Policy](security-center-azure-policy.md).

### <a name="manage-and-govern-your-security-posture"></a>Gérer et gouverner votre situation de sécurité
La partie droite du tableau de bord sous **Stratégie et conformité** fournit des insights sur lesquels vous pouvez agir immédiatement pour améliorer votre situation de sécurité générale. Voici quelques exemples :

- Définir et affecter des stratégies Security Center, afin d’examiner et de suivre la conformité aux normes de sécurité
- Rendre les alertes de sécurité Security Center disponibles pour un connecteur SIEM
- Conformité à la stratégie au fil du temps

## <a name="continuous-security-assessment"></a>Évaluation continue de la sécurité
La section Hygiène de sécurité de la ressource sous **Security Center - Vue d’ensemble** fournit un aperçu rapide de l’hygiène de la sécurité de vos ressources, en affichant le nombre de problèmes identifiés et l’état de sécurité pour chaque type de ressource. L’évaluation continue vous aide à découvrir d’éventuels problèmes de sécurité, comme les systèmes avec des mises à jour de sécurité manquantes ou des ports réseau exposés.

### <a name="secure-score"></a>Degré de sécurisation
Le degré de sécurisation Azure Security Center examine vos recommandations de sécurité et les classe par priorité, afin que vous sachiez quelles recommandations suivre avant tout ; il vous aide ainsi à trouver les failles de sécurité les plus graves, ce qui vous permet de hiérarchiser l’investigation. Le degré de sécurisation est un outil de mesure qui vous permet de renforcer votre sécurité pour parvenir à une charge de travail sécurisée. Pour plus d’informations, consultez [Degré de sécurisation dans Azure Security Center](security-center-secure-score.md).

### <a name="health-monitoring"></a>Surveillance de l’intégrité
La sélection d’un type de ressource sous **Monitoring de l’intégrité de la ressource** fournit une liste de ressources ainsi que toutes les vulnérabilités qui ont été identifiées. Les types de ressources sont calcul et applications, réseau, données et stockage, et identité et accès.

Nous avons sélectionné **Calcul et applications**. Sous **Compute**, vous trouverez quatre onglets :

- **Vue d’ensemble** : la surveillance et les recommandations identifiées par Security Center.
- **Machines virtuelles et ordinateurs** : liste de vos machines virtuelles et ordinateurs, et état actuel de leur sécurité.
- **Services cloud** : liste des rôles web et de travail contrôlés par Security Center.
- **App Services (préversion)** : liste de vos applications web et environnements App Service, ainsi que l’état actuel de la sécurité de chacun d’eux.

![Monitoring de l’intégrité de la sécurité][3]

Pour plus d’informations, consultez [Monitoring de l’intégrité de la sécurité](security-center-monitoring.md).

## <a name="actionable-recommendations"></a>Recommandations exploitables
Security Center analyse l’état de sécurité de vos ressources Azure et autres qu’Azure pour identifier les vulnérabilités de sécurité potentielles. La sélection de **Recommandations** sous **Ressources** fournit une liste de recommandations de sécurité, par ordre de priorité, qui vous guide tout au long du processus de gestion des problèmes de sécurité.

![Recommandations][4]

Pour plus d’informations, consultez [Gestion des recommandations de sécurité](security-center-recommendations.md).

### <a name="most-prevalent-recommendations"></a>Recommandations les plus répandues
La partie droite du tableau de bord, sous **Ressources**, fournit une liste de recommandations répandues qui existent pour la majeure partie des ressources. Ces recommandations mettent en évidence les points sur lesquels vous devez concentrer votre attention. La sélection de la flèche droite renseigne sur la recommandation dont l’incidence est la plus importante.

![Recommandations les plus répandues][11]

Il s’agit de la recommandation à l’impact le plus fort qui existe dans votre environnement. En résolvant cette recommandation, vous améliorez votre conformité de façon optimale. Dans cet exemple, la recommandation consiste à « appliquer le chiffrement de disque ». La sélection de **Améliorez votre conformité** fournit une description de la recommandation et une liste de ressources affectées.

![Apply disk encryption][12]

## <a name="threat-protection"></a>Protection contre les menaces
Cette zone offre une visibilité sur les alertes de sécurité détectées dans vos ressources, avec le niveau de gravité de ces alertes.

### <a name="prioritized-alerts-and-incidents"></a>Alertes et incidents classés par ordre de priorité
Security Center utilise l’analytique avancée et une fonctionnalité de Threat Intelligence globale pour détecter les attaques entrantes et toute activité suivant la violation. Les alertes sont classées par ordre de priorité et regroupées en incidents, ce qui vous permet de vous concentrer tout d’abord sur les menaces les plus critiques. Vous pouvez aussi créer vos propres alertes de sécurité personnalisées.

La sélection de **Alertes de sécurité par gravité** ou **Alertes de sécurité dans le temps** fournit des informations détaillées sur ces alertes.

![Alertes et incidents classés par ordre de priorité][7]

Vous pouvez évaluer rapidement l’étendue et l’impact d’une attaque à l’aide d’une expérience d’enquête visuelle et interactive. Vous pouvez utiliser des requêtes prédéfinies ou ad hoc pour une exploration plus approfondie des données de sécurité.

Pour plus d’informations, consultez [Gestion et résolution des alertes de sécurité](security-center-managing-and-responding-alerts.md).

La partie droite du tableau de bord fournit des informations pour vous aider à hiérarchiser les alertes à résoudre en priorité, et à comprendre où se trouvent vos vulnérabilités courantes :

- **Ressources les plus attaquées**. Les ressources particulières qui enregistrent le nombre d’alertes le plus élevé
- **Alertes les plus fréquentes**. Les types d’alertes qui affectent le plus grand nombre de ressources

## <a name="just-in-time-vm-access"></a>Accès juste-à-temps aux machines virtuelles
Réduisez la surface exposée aux attaques du réseau avec l’accès contrôlé juste à temps aux ports de gestion sur les machines virtuelles Azure, ce qui diminue de façon significative l’exposition aux attaques par force brute et autres attaques réseau.

![Accès Juste à temps à la machine virtuelle][5]

Définissez des règles sur la façon dont les utilisateurs peuvent se connecter aux machines virtuelles. Si nécessaire, l’accès peut être demandé à partir de Security Center ou via PowerShell. Tant que la requête est conforme aux règles, l’accès est accordé automatiquement pendant la durée demandée.

Pour plus d’informations, consultez [Gérer l’accès Juste à temps à la machine virtuelle](security-center-just-in-time.md).

## <a name="adaptive-application-controls"></a>Contrôles d’application adaptative
Bloquez les programmes malveillants et les autres applications indésirables en appliquant des recommandations de mise en liste verte adaptées à vos charges de travail Azure spécifiques et alimentées par l’apprentissage machine.

![Contrôles d’application adaptative][6]

Passez en revue et cliquez pour appliquer les règles recommandées de mise sur liste verte d’applications générées par Security Center ou modifiez les règles déjà configurées.

Pour plus d’informations, consultez [Contrôles d’application adaptatifs](security-center-adaptive-application.md).

## <a name="integrate-your-security-solutions"></a>Intégrer vos solutions de sécurité
Dans Security Center, vous pouvez collecter, rechercher et analyser les données de sécurité d’un large éventail de sources, notamment les solutions partenaires connectées comme les pare-feu réseau et d’autres services Microsoft.

![Intégrer des solutions de sécurité][8]

Pour plus d’informations, consultez [Intégrer des solutions de sécurité](security-center-partner-integration.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour utiliser le Centre de sécurité, vous devez disposer d’un abonnement à Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/free/).
- Le niveau tarifaire Gratuit de Security Center est activé avec votre abonnement Azure. Pour tirer parti des fonctionnalités avancées de gestion de la sécurité et de détection des menaces, vous devez mettre à niveau vers le niveau tarifaire Standard. Le niveau Standard est gratuit les 60 premiers jours. Pour plus d’informations, consultez la [page de tarification de Security Center](https://azure.microsoft.com/pricing/details/security-center/).
- Si vous êtes prêt à activer Security Center Standard dès maintenant, le [démarrage rapide : Intégrer votre abonnement Azure à Security Center Standard](security-center-get-started.md) vous guidera.


<!--Image references-->
[1]: ./media/security-center-intro/overview.png
[3]: ./media/security-center-intro/compute.png
[4]: ./media/security-center-intro/recommendations.png
[5]: ./media/security-center-intro/just-in-time-vm-access.png
[6]: ./media/security-center-intro/adaptive-app-controls.png
[7]: ./media/security-center-intro/security-alerts.png
[8]: ./media/security-center-intro/security-solutions.png
[9]: ./media/security-center-intro/coverage.png
[10]: ./media/security-center-intro/policy-management.png
[11]: ./media/security-center-intro/highest-impact.png
[12]: ./media/security-center-intro/apply-disk-encryption.png
