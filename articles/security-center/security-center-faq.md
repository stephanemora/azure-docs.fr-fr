---
title: FAQ de l’Azure Security Center | Microsoft Docs
description: Ce forum aux questions concerne le Centre de sécurité Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2018
ms.author: rkarlin
ms.openlocfilehash: 382f85c268b2e21a780756057f4bf78c41c791c2
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283501"
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>FAQ du Centre de sécurité Azure
Cette FAQ répond aux questions concernant Azure Security Center, qui vous aide à prévenir, détecter et résoudre les menaces grâce à une meilleure visibilité et à un meilleur contrôle de la sécurité de vos ressources Microsoft Azure.

> [!NOTE]
> Depuis début juin 2017, Security Center utilise Microsoft Monitoring Agent pour collecter et stocker des données. Pour en savoir plus, consultez [Migration de plateforme Azure Security Center](security-center-platform-migration.md). Les informations contenues dans cet article représentent les fonctionnalités de Security Center après la transition vers Microsoft Monitoring Agent.
>
>

## <a name="general-questions"></a>Questions générales
### <a name="what-is-azure-security-center"></a>Qu’est-ce que le Centre de sécurité Azure ?
Le Centre de sécurité Azure vous aide à prévenir, détecter et résoudre les menaces grâce à une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

### <a name="how-do-i-get-azure-security-center"></a>Comment obtenir le Centre de sécurité Azure ?
Azure Security Center est disponible avec votre abonnement Microsoft Azure et accessible à partir du [Portail Azure](https://azure.microsoft.com/features/azure-portal/). ([Connectez-vous au portail](https://portal.azure.com), sélectionnez **Parcourir**, puis faites défiler jusqu’à **Centre de sécurité**).  

## <a name="billing"></a>Facturation
### <a name="how-does-billing-work-for-azure-security-center"></a>Comment fonctionne la facturation pour le Centre de sécurité Azure ?
Security Center est proposé en deux niveaux :

Le **niveau Gratuit** vous permet de voir l’état de sécurité de vos ressources Azure, vos stratégies de sécurité de base, vos recommandations de sécurité, ainsi que l’intégration des produits et services de sécurité de partenaires.

Le **niveau Standard** fournit des fonctionnalités de détection avancée des menaces, notamment des informations sur les menaces, une analyse comportementale, une détection des anomalies, des informations sur les incidents de sécurité et des rapports sur l’attribution des menaces. Le niveau Standard est gratuit les 60 premiers jours. Une fois ces 60 jours écoulés, si vous décidez de continuer à utiliser le service, votre utilisation est automatiquement facturée.  Pour effectuer la mise à niveau, sélectionnez [Niveau tarifaire](https://docs.microsoft.com/azure/security-center/security-center-pricing) dans la stratégie de sécurité.

## <a name="permissions"></a>Autorisations
Azure Security Center utilise le [contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control/role-assignments-portal.md) qui fournit des [rôles intégrés](../role-based-access-control/built-in-roles.md) susceptibles d’être affectés à des utilisateurs, des groupes et des services dans Azure.

Security Center évalue la configuration de vos ressources pour identifier les vulnérabilités et les problèmes de sécurité. Dans Security Center, vous ne voyez les informations relatives à une ressource que lorsque vous avez reçu le rôle de propriétaire, de collaborateur ou de lecteur pour l’abonnement ou le groupe de ressources auquel appartient la ressource.

Pour plus d’informations sur les rôles et les actions autorisées dans Security Center, consultez l’article [Permissions in Azure Security Center (Autorisations dans Azure Security Center)](security-center-permissions.md).

## <a name="data-collection"></a>Collecte des données
Azure Security Center collecte des données à partir de vos machines virtuelles Azure et ordinateurs autres qu’Azure pour surveiller les menaces et vulnérabilités de sécurité. Les données sont collectées à l’aide de Microsoft Monitoring Agent, qui lit divers journaux d’événements et configurations liées à la sécurité de la machine et copie les données dans votre espace de travail à des fins d’analyse.

### <a name="how-do-i-disable-data-collection"></a>Comment désactiver la collecte des données ?
L’approvisionnement automatique est désactivé par défaut. Vous pouvez désactiver l’approvisionnement automatique à partir des ressources à tout moment en désactivant ce paramètre dans la stratégie de sécurité. L’approvisionnement automatique est fortement recommandé si vous souhaitez obtenir des alertes de sécurité et des recommandations sur les mises à jour système, les vulnérabilités du système d’exploitation et la protection du point de terminaison.

Pour désactiver la collecte des données, [connectez-vous au Portail Azure](https://portal.azure.com), sélectionnez **Parcourir**, **Centre de sécurité**, puis **Sélectionner une stratégie**. Sélectionnez l’abonnement pour lequel vous souhaitez désactiver l’approvisionnement automatique. Lorsque vous sélectionnez un abonnement, **Stratégie de sécurité - Collecte de données** s’ouvre. Sous **Auto provisioning** (Approvisionnement automatique), sélectionnez **Off** (Désactivé).

### <a name="how-do-i-enable-data-collection"></a>Comment activer la collecte des données ?
Vous pouvez activer la collecte des données pour votre abonnement Azure dans la stratégie de sécurité. Pour activer la collecte de données. [Connectez-vous au portail Azure](https://portal.azure.com), sélectionnez **Parcourir**, **Centre de sécurité**, puis **Stratégie de sécurité**. Sélectionnez l’abonnement pour lequel vous souhaitez activer l’approvisionnement automatique. Lorsque vous sélectionnez un abonnement, **Stratégie de sécurité - Collecte de données** s’ouvre. Sous **Auto provisioning** (Approvisionnement automatique), sélectionnez **On** (Activé).

### <a name="what-happens-when-data-collection-is-enabled"></a>Que se passe-t-il quand la collecte des données est activée ?
Lorsque l’approvisionnement automatique est activé, Security Center approvisionne Microsoft Monitoring Agent pour toutes les machines virtuelles Azure prises en charge et toutes celles nouvellement créées. Le provisionnement automatique est fortement recommandé. Toutefois, l’installation manuelle des agents est également disponible. [Découvrez comment installer l’extension Microsoft Monitoring Agent](../log-analytics/log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) 

L’agent active l’événement de création de processus 4688 et le champ *CommandLine* à l’intérieur de l’événement 4688. Les nouveaux processus créés sur la machine virtuelle sont enregistrés par le journal des événements et analysés par les services de détection Security Center. Pour plus d’informations sur les détails enregistrés pour chaque nouveau processus, consultez [Description des champs 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). L’agent collecte également les événements 4688 créés sur la machine virtuelle et les stocke dans la recherche.

L’agent permet également d’activer la collecte de données pour les [Contrôles d’application adaptatifs](security-center-adaptive-application.md), Security Center configure une stratégie AppLocker locale en mode Audit pour autoriser toutes les applications. Cela amène AppLocker à générer des événements qui sont ensuite recueillis et exploités par Security Center. Il est important de noter que cette stratégie ne sera configurée sur aucun ordinateur sur lequel une stratégie AppLocker est déjà configurée. 

Lorsque Security Center détecte une activité suspecte sur la machine virtuelle, le client est averti par e-mail si [des informations de contact de sécurité](security-center-provide-security-contact-details.md) ont été fournies. Une alerte est également visible dans le tableau de bord d’alertes de sécurité de Security Center.



### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Est-ce que l’agent d’analyse a un impact sur les performances de mes serveurs ?
L’agent utilise une quantité minime de ressources système et n’a donc qu’un faible impact sur les performances. Pour en savoir plus sur l’impact sur les performances, l’agent et l’extension, consultez le [Guide de planification et de fonctionnement](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>Où sont stockées mes données ?
Les données collectées à partir de cet agent sont stockées dans un espace de travail Log Analytics existant associé à votre abonnement Azure ou dans un nouvel espace de travail. Pour plus d’informations, consultez [Sécurité des données](security-center-data-security.md).

## <a name="using-azure-security-center"></a>Utilisation du Centre de sécurité Azure
### <a name="what-is-a-security-policy"></a>Qu’est-ce qu’une stratégie de sécurité ?
Une stratégie de sécurité définit l’ensemble des contrôles recommandés pour les ressources d’un abonnement spécifique. Dans Azure Security Center, vous définissez les stratégies de vos abonnements Azure en fonction des exigences de sécurité de votre entreprise et du type d’applications ou du niveau de confidentialité des données de chaque abonnement.

Les stratégies de sécurité activées dans Azure Security Center déterminent les recommandations et la surveillance liées à la sécurité. Pour plus d’informations sur les stratégies de sécurité, consultez [Surveillance de l’intégrité de la sécurité dans le Centre de sécurité Azure](security-center-monitoring.md).

### <a name="who-can-modify-a-security-policy"></a>Qui peut modifier une stratégie de sécurité ?
Pour modifier une stratégie de sécurité, vous devez avoir le rôle d’administrateur de la sécurité, de propriétaire ou de collaborateur pour l’abonnement concerné.

Pour savoir comment configurer une stratégie de sécurité, consultez [Définition de stratégies de sécurité dans le Centre de sécurité Azure](security-center-policies.md).

### <a name="what-is-a-security-recommendation"></a>Qu’est-ce qu’une recommandation de sécurité ?
Le Centre de sécurité Azure analyse l’état de sécurité de vos ressources Azure. Quand des failles de sécurité potentielles sont identifiées, des recommandations sont créées. Les recommandations vous guident tout au long du processus de configuration du contrôle. Voici quelques exemples :

* Approvisionnement d’un logiciel anti-programme malveillant pour identifier et supprimer les programmes malveillants
* [Groupes de sécurité réseau](../virtual-network/security-overview.md) et règles pour contrôler le trafic vers les machines virtuelles
* Approvisionnement d’un pare-feu d’applications web pour se défendre des attaques ciblant vos applications web
* Déploiement de mises à jour système manquantes
* Correction des configurations de système d’exploitation qui ne suivent pas les recommandations

Seules les recommandations qui sont activées dans les stratégies de sécurité sont affichées ici.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Comment connaître l’état de sécurité actuel de mes ressources Azure ?
Le panneau **Vue d’ensemble de Security Center** affiche les mesures de sécurité globales de votre environnement pour les ressources suivantes : Compute, Mise en réseau, Stockage et données, et Applications. Chaque type de ressource possède un indicateur montrant la présence éventuelle de failles de sécurité. Cliquez sur chaque vignette pour afficher une liste des problèmes de sécurité identifiés par Security Center, ainsi qu’un inventaire des ressources dans votre abonnement.

### <a name="what-triggers-a-security-alert"></a>Qu’est-ce qui déclenche une alerte de sécurité ?
Azure Security Center collecte, analyse et fusionne automatiquement les données du journal à partir de vos ressources Azure, du réseau et des solutions partenaires telles que les logiciels anti-programme malveillant et les pare-feu. Quand des menaces sont détectées, une alerte de sécurité est créée. Voici quelques exemples de détections :

* Des machines virtuelles compromises qui communiquent avec des adresses IP connues comme étant malveillantes
* Des programmes malveillants avancés qui sont détectés à l’aide du rapport d’erreurs Windows
* Des attaques par force brute contre des machines virtuelles
* Des alertes de sécurité émises par des solutions de sécurité partenaires intégrées, telles que des logiciels anti-programme malveillant ou des pare-feu d’applications web

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Quelle est la différence entre les menaces détectées et faisant l’objet d’une alerte par Microsoft Security Response Center et par Azure Security Center ?
Microsoft Security Response Center (MSRC) effectue certaines analyses de sécurité sur l'infrastructure et le réseau Azure et reçoit des informations sur les menaces et des plaintes pour mauvaise utilisation provenant de tiers. Lorsque MSRC constate que les données client ont été utilisées par un tiers illégal ou non autorisé ou que l'utilisation d’Azure par le client ne respecte pas les conditions de bon usage, un gestionnaire des incidents de sécurité informe le client. La notification correspond généralement à un courrier électronique envoyé aux contacts de sécurité spécifiés dans Azure Security Center ou au propriétaire de l’abonnement Azure si aucun contact de sécurité n’est spécifié.

Security Center est un service Azure qui surveille en continu l'environnement Azure du client et applique des analyses de façon à détecter automatiquement un large éventail d'activités potentiellement malveillantes. Ces détections sont signalées en tant qu'alertes de sécurité dans le tableau de bord du Security Center.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Quelles ressources Azure sont surveillées par Azure Security Center ?
Azure Security Center surveille les ressources Azure suivantes :

* Machines virtuelles (VM) (y compris [Cloud Services](../cloud-services/cloud-services-choose-me.md))
* Réseaux virtuels Azure
* Service SQL Azure
* un compte Azure Storage.
* Azure Web Apps (dans [App Service Environment](../app-service/environment/intro.md))
* Solutions de partenaires intégrées à votre abonnement Azure, par exemple pare-feu d’applications web sur les machines virtuelles et sur App Service Environment

## <a name="virtual-machines"></a>Virtual Machines
### <a name="what-types-of-virtual-machines-are-supported"></a>Quels sont les types de machines virtuelles pris en charge ?
Le monitoring et des recommandations sont disponibles pour les machines virtuelles créées à l’aide des [modèles de déploiement Classic et Resource Manager](../azure-classic-rm.md).

Consultez [Plateformes prises en charge dans Azure Security Center](security-center-os-coverage.md) pour obtenir la liste des plateformes prises en charge.

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Pourquoi Azure Security Center ne reconnaît-il pas la solution anti-programme malveillant de ma machine virtuelle Azure ?
Azure Security Center bénéficie d’une visibilité sur les logiciels anti-programme malveillant installés par le biais des extensions Azure. Par exemple, Security Center n’est pas en mesure de détecter les logiciels anti-programme malveillant préinstallés sur une image que vous avez fournie ou installés sur vos machines virtuelles à l’aide de votre propre processus (notamment les systèmes de gestion de la configuration).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Pourquoi reçois-je le message « Données d’analyse manquantes » pour ma machine virtuelle ?
Ce message s’affiche lorsqu’il n’existe aucune donnée d’analyse pour une machine virtuelle. Le remplissage des données d’analyse peut prendre un certain temps (inférieur à une heure) une fois la collecte de données activée dans Azure Security Center. Après le remplissage initial des données d’analyse, vous pouvez recevoir ce message s’il n’existe aucune donnée d’analyse ou s’il n’existe pas de données d’analyse récentes. Le remplissage des analyses n’est pas effectué pour une machine virtuelle à l’état Arrêté. Ce message peut également s’afficher si les données d’analyse n’ont pas été renseignées récemment (conformément à la stratégie de rétention de l’agent Windows, dont la valeur par défaut est 30 jours).

### <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>À quelle fréquence Security Center recherche-t-il les vulnérabilités du système d’exploitation, les mises à jour du système et les problèmes de protection du point de terminaison ?
La latence dans les analyses de Security Center à la recherche de vulnérabilités, de mises à jour et de problèmes est la suivante :

- Configuration de la sécurité du système d’exploitation ; les données sont mises à jour dans les 48 heures
- Mises à jour du système ; les données sont mises à jour dans les 24 heures
- Problèmes de protection du point de terminaison ; les données sont mises à jour dans les 8 heures

En règle générale, Security Center procède à une analyse toutes les heures pour rechercher de nouvelles données. Les valeurs de latence ci-dessus sont les pires situations qui peuvent se produire, dans lesquelles il n’y a pas d’analyse récente ou, s’il y a analyse, elle a échoué.

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Pourquoi reçois-je le message « Agent de machine virtuelle manquant » ?
L’agent de machine virtuelle doit être installé sur les machines virtuelles pour activer la collecte des données. L’agent de machine virtuelle est installé par défaut sur les machines virtuelles déployées depuis Azure Marketplace. Pour plus d’informations sur l’installation de l’agent de machine virtuelle sur d’autres machines virtuelles, consultez l’article de blog [Agent de machine virtuelle et extensions](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).
