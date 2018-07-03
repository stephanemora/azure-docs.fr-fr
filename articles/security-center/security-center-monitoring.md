---
title: Surveillance de la sécurité dans Azure Security Center | Microsoft Docs
description: Cet article a pour but de vous aider à vous familiariser avec les fonctionnalités de surveillance d’Azure Security Center.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2018
ms.author: terrylan
ms.openlocfilehash: dc362306e20b4d39aa73a552e47cbcbd3037edbd
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063389"
---
# <a name="security-health-monitoring-in-azure-security-center"></a>Surveillance de l’intégrité de la sécurité dans le Centre de sécurité Azure
Cet article a pour but de vous aider à utiliser les fonctionnalités de surveillance d’Azure Security Center, afin de contrôler la conformité aux stratégies.

## <a name="what-is-security-health-monitoring"></a>Qu’est-ce que la surveillance de l’intégrité de la sécurité ?
Nous pensons souvent que la surveillance consiste à veiller et à attendre qu’un événement se produise pour y répondre. Ici, la surveillance de la sécurité fait référence à une stratégie proactive qui audite vos ressources afin d’identifier les systèmes qui ne répondent pas aux normes organisationnelles ou aux meilleures pratiques.

## <a name="monitoring-security-health"></a>Surveillance de l’intégrité de la sécurité
Une fois que vous avez activé les [stratégies de sécurité](security-center-policies.md) pour les ressources d’un abonnement, Azure Security Center analyse la sécurité de vos ressources afin d’identifier les vulnérabilités potentielles. Les informations sur la configuration du réseau sont instantanément disponibles. Selon le nombre de machines virtuelles et d’ordinateurs sur lesquels est installé l’agent, il se peut que la collecte d’informations sur la configuration des ordinateurs et des machines virtuelles (statut des mises à jour de sécurité et configuration du système d’exploitation) ne soit disponible qu’au bout d’une heure, voire plus. Vous pouvez visualiser l’état de sécurité de vos ressources et les problèmes éventuels dans la section **Prévention**. Vous pouvez également afficher une liste de ces problèmes dans la mosaïque **Recommandations** .

Pour plus d’informations sur la façon d’appliquer des recommandations, consultez [Implémentation des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md).

Sous **Surveillance de l’intégrité des ressources**, vous pouvez surveiller l’état de sécurité de vos ressources. Dans l’exemple suivant, vous pouvez voir dans la vignette de chaque ressource (Compute et applications, Mise en réseau, Sécurité des données, et Identité et accès) le nombre total de problèmes qui ont été identifiés.

![Mosaïque Intégrité de la sécurité des ressources](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)


### <a name="monitor-compute--apps"></a>Suivre les calculs et les applications
Consultez [Protéger vos machines et applications dans le centre de sécurité Azure](security-center-virtual-machine-recommendations.md) pour plus d’informations.

### <a name="monitor-virtual-networks"></a>Surveillance des réseaux virtuels
Lorsque vous cliquez sur la mosaïque **Mise en réseau**, le panneau **Mise en réseau** s’ouvre et affiche des informations détaillées, comme illustré dans la capture d’écran suivante :

![Panneau Mise en réseau](./media/security-center-monitoring/security-center-monitoring-fig9-new3.png)

#### <a name="networking-recommendations"></a>Recommandations pour la mise en réseau
À l’instar des informations relatives à l’intégrité des ressources de la machine virtuelle, une liste récapitulative des problèmes s’affiche en haut, et une liste de réseaux surveillés s’affiche en bas.

La section de répartition des états du réseau répertorie les problèmes de sécurité potentiels et propose des [recommandations](security-center-network-recommendations.md)pour leur résolution. Voici des exemples de problèmes potentiels :

* Absence d’installation d’un pare-feu de nouvelle génération
* Non-activation des groupes de sécurité réseau
* Non-activation des groupes de sécurité réseau sur les machines virtuelles
* Restriction de l’accès externe via le point de terminaison externe public
* Intégrité des points de terminaison exposés à Internet

Lorsque vous cliquez sur une recommandation, des informations détaillées s’affichent, comme illustré dans l’exemple suivant :

![Détails relatifs à une recommandation dans la section de mise en réseau](./media/security-center-monitoring/security-center-monitoring-fig9-ga.png)

Dans cet exemple, le panneau **Configurer les groupes de sécurité réseau manquants pour les sous-réseaux** contient une liste de sous-réseaux et de machines virtuelles ne disposant pas de la protection des groupes de sécurité réseau. Si vous cliquez sur le sous-réseau auquel vous souhaitez appliquer le groupe de sécurité réseau, le panneau **Choisir un groupe de sécurité réseau**s’affiche. Ici, vous pouvez sélectionner le groupe de sécurité réseau le mieux adapté au sous-réseau, ou créer un groupe de sécurité réseau.

#### <a name="internet-facing-endpoints-section"></a>Section des points de terminaison accessibles sur Internet
La section **Points de terminaison accessibles sur Internet** présente les machines virtuelles qui sont actuellement configurées avec un point de terminaison accessible sur Internet, ainsi que leur état actuel.

![Les machines virtuelles configurées avec un point de terminaison accessible sur Internet et son état](./media/security-center-monitoring/security-center-monitoring-fig10-ga.png)

Ce tableau indique le nom du point de terminaison qui représente la machine virtuelle, l’adresse IP Internet et l’état de gravité actuel du groupe de sécurité réseau et du pare-feu nouvelle génération. Le tableau est trié par niveau de gravité :

* Rouge (en haut) : priorité élevée ; doivent être traités immédiatement
* Orange : priorité moyenne ; doivent être traités dès que possible
* Vert (le dernier) : état d’intégrité

#### <a name="networking-topology-section"></a>Section de topologie de mise en réseau
La section **Topologie de mise en réseau** contient une vue hiérarchique des ressources, comme illustré dans la capture d’écran suivante :

![Vue hiérarchique des ressources dans la section Topologie de mise en réseau](./media/security-center-monitoring/security-center-monitoring-fig121-new4.png)

Ce tableau est trié (machines virtuelles et sous-réseaux) par niveau de gravité :

* Rouge (en haut) : priorité élevée ; doivent être traités immédiatement
* Orange : priorité moyenne ; doivent être traités dès que possible
* Vert (le dernier) : état d’intégrité

Dans cette topologie, le premier niveau se décompose comme suit : [réseaux virtuels](../virtual-network/virtual-networks-overview.md), [passerelles de réseau virtuel](/vpn-gateway/vpn-gateway-site-to-site-create.md) et [réseaux virtuels (classiques)](/virtual-network/virtual-networks-create-vnet-classic-pportal.md). Le deuxième niveau comprend des sous-réseaux et le troisième niveau regroupe les machines virtuelles appartenant à ces sous-réseaux. La colonne de droite présente l’état actuel du groupe de sécurité réseau pour ces ressources, comme illustré dans l’exemple suivant :

![État du groupe de sécurité réseau dans la section Topologie de mise en réseau](./media/security-center-monitoring/security-center-monitoring-fig12-ga.png)

La partie inférieure de ce panneau regroupe les recommandations pour cette machine virtuelle, similaires celles décrites ci-dessus. Vous pouvez cliquer sur une recommandation pour en savoir plus, ou appliquer la configuration ou le contrôle de sécurité nécessaire.

### <a name="monitor-data-security"></a>Surveiller la sécurité des données

Lorsque vous cliquez sur **Sécurité des données** dans la section **Prévention**, le panneau **Ressources de données** s’ouvre et affiche des recommandations pour SQL et le stockage. Il contient également des [recommandations](security-center-sql-service-recommendations.md) pour l’état général de la base de données. Pour plus d’informations sur le chiffrement du stockage, consultez [Enable encryption for Azure storage account in Azure Security Center (Activer le chiffrement pour le compte de stockage Azure dans Azure Security Center)](security-center-enable-encryption-for-storage-account.md).

![Ressources de données](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

Sous **Recommandations SQL**, vous pouvez cliquer sur une recommandation et obtenir des informations sur les actions permettant de résoudre un problème. L’exemple suivant montre le détail de la recommandation **Database Auditing & Threat detection on SQL databases (Audit de base de données et détection des menaces sur les bases de données SQL)**.

![Détails relatifs à une recommandation SQL](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

Le panneau **Activer l’audit et la détection de menaces sur les bases de données SQL** contient les informations suivantes :

* Une liste des bases de données SQL.
* Le serveur sur lequel elles se trouvent.
* Des informations indiquant si ce paramètre a été hérité du serveur ou s’il est unique dans cette base de données.
* L’état actuel du problème.
* Le niveau de gravité du problème.

Lorsque vous cliquez sur la base de données pour suivre cette recommandation, le panneau **Audit et détection des menaces** s’ouvre, comme illustré dans la capture d’écran suivante.

![Audit et détection des menaces](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

Pour activer l’audit, sélectionnez **ACTIVÉ** sous **Audit**.

### <a name="monitor-identity--access"></a>Surveiller l’identité et l’accès

Consultez [Surveiller l’identité et l’accès dans Azure Security Center](security-center-identity-access.md) pour plus d’informations.

## <a name="see-also"></a>Voir aussi
Dans cet article, vous avez vu comment utiliser les fonctionnalités de surveillance d’Azure Security Center. Pour plus d’informations sur le Centre de sécurité Azure, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des paramètres de sécurité dans Azure Security Center.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions partenaires.
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.
