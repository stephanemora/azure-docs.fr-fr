---
title: Protection de vos machines et de vos applications dans Azure Security Center | Microsoft Docs
description: Ce document traite des recommandations de Security Center qui peuvent vous aider à protéger vos machines virtuelles, vos ordinateurs, vos applications web et vos environnements App Service.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: d1e7046ed1fc87a85a08b21a48accaaf3b5087b9
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52316648"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>Protection de vos machines et de vos applications dans Azure Security Center
Le Centre de sécurité Azure analyse l’état de sécurité de vos ressources Azure. Lorsque Security Center identifie des failles de sécurité potentielles, il crée des recommandations qui vous guident tout au long du processus de configuration des contrôles nécessaires. Ces recommandations s’appliquent aux types de ressources Azure : machines virtuelles et ordinateurs, applications, mise en réseau, SQL et Identité et accès.

Cet article traite des recommandations qui s’appliquent aux machines et applications.

## <a name="monitoring-security-health"></a>Surveillance de l’intégrité de la sécurité
Vous pouvez surveiller l’état de sécurité de vos ressources dans le tableau de bord **Security Center - Vue d’ensemble**. La section **Ressources** indique le nombre de problèmes identifiés et l’état de sécurité de chaque type de ressource.

Vous pouvez consulter une liste de tous les problèmes en sélectionnant **Recommandations**. Pour plus d’informations sur la façon d’appliquer des recommandations, consultez [Implémentation des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md).

Pour obtenir la liste complète des recommandations Compute et App services, consultez [Recommandations](security-center-virtual-machine-recommendations.md).

Pour continuer, sélectionnez **Compute et applications** sous **Ressources**, ou dans le menu principal de Security Center.
![Tableau de bord Security Center][1]

## <a name="monitor-compute-and-app-services"></a>Surveiller Compute et App Services
Sous **Compute**, vous trouverez quatre onglets :

- **Vue d’ensemble** : la surveillance et les recommandations identifiées par Security Center.
- **Machines virtuelles et ordinateurs** : liste de vos machines virtuelles et ordinateurs, et état actuel de leur sécurité.
- **Services cloud** : liste des rôles web et de travail contrôlés par Security Center.
- **App Services (préversion)**: liste de vos environnements App Service et état actuel de leur sécurité.
Pour continuer, sélectionnez **Compute et applications** sous **Ressources**, ou dans le menu principal de Security Center.

![Calcul][2]

Chaque onglet peut contenir plusieurs sections, et dans chaque section vous pouvez sélectionner une option afin d’afficher plus de détails sur les étapes recommandées pour résoudre le problème en question.

### <a name="monitoring-recommendations"></a>Recommandations concernant la surveillance
Cette section indique le nombre total de machines virtuelles et d’ordinateurs qui ont été initialisés pour l’approvisionnement automatique et leurs états actuels. Cet exemple comprend une recommandation, **Problèmes d’intégrité de l’agent d’analyse**. Sélectionnez cette recommandation.

![Problèmes d’intégrité de l’agent de surveillance][3]

**Problèmes d’intégrité de l’agent de surveillance** s’ouvre. Les machines virtuelles et les ordinateurs que le Security Center ne peut pas surveiller correctement s’affichent ici. Pour plus d’informations, sélectionnez une machine virtuelle ou un ordinateur. **MONITORING STATE** fournit une raison pour laquelle Security Center ne peut pas surveiller. Consultez le [guide de dépannage de Security Center](security-center-troubleshooting-guide.md) pour obtenir une liste de valeurs, de descriptions et d’étapes de résolution de **MONITORING STATE**.

### Machines virtuelles et ordinateurs non surveillés <a name="unmonitored-vms-and-computers"></a>
Une machine virtuelle ou un ordinateur n’est pas surveillé par Security Center si la machine n’exécute pas l’extension Microsoft Monitoring Agent. Un agent local peut être déjà installé sur une machine, par exemple l’agent direct OMS ou l’agent SCOM. Les machines sur lesquelles sont installés ces agents sont considérées comme non surveillées, car ces agents ne sont pas entièrement pris en charge par Security Center. Pour tirer pleinement parti de toutes les fonctionnalités de Security Center, vous devez utiliser l’extension Microsoft Monitoring Agent.

Vous pouvez installer l’extension sur la machine virtuelle ou l’ordinateur non surveillé, en plus de l’agent local déjà installé. Configurez les deux agents de la même manière en les connectant au même espace de travail. Ainsi, Security Center pourra interagir avec l’extension Microsoft Monitoring Agent et collecter des données. Consultez la section [Activer l’extension de machine virtuelle](../log-analytics/log-analytics-quick-collect-azurevm.md) pour obtenir des instructions sur la façon d’installer l’extension Microsoft Monitoring Agent.

Consultez la section [Problèmes d’intégrité de l’agent de surveillance](security-center-troubleshooting-guide.md#mon-agent) pour en savoir plus sur la raison pour laquelle Security Center ne peut pas surveiller correctement les machines virtuelles et ordinateurs initialisés pour l’approvisionnement automatique.

### <a name="recommendations"></a>Recommandations
Cette section contient un ensemble de recommandations pour chaque machine virtuelle et ordinateur, les rôles web et de travail, les applications web Azure App Service et l’environnement Azure App Service surveillés par Security Center. La première colonne indique la recommandation. La deuxième colonne précise le nombre total de ressources qui sont concernées par cette recommandation. La troisième colonne indique la gravité du problème, comme illustré dans la capture d’écran suivante :

![Recommandations][4]

Chaque recommandation dispose d’un ensemble d’actions pouvant être effectuées après les avoir sélectionnées. Par exemple, si vous sélectionnez **Mises à jour système manquantes**, le nombre de machines virtuelles et d’ordinateurs ayant des correctifs manquants et la gravité de la mise à jour manquante apparaissent, comme illustré dans la capture d’écran suivante :

![Appliquer des mises à jour système][5]

**Appliquer les mises à jour système** contient un résumé des mises à jour critiques dans un format graphique, l’un pour Windows et l’autre pour Linux. La deuxième partie contient un tableau avec les informations suivantes :

- **NOM** : nom de la mise à jour manquante.
- **NO. DE MACHINES VIRTUELLES ET D’ORDINATEURS** : nombre total de machines virtuelles et d’ordinateurs sur lesquels cette mise à jour n’a pas été installée.
- **GRAVITÉ DE LA MISE À JOUR** : donne le niveau de gravité de chaque recommandation :

    - **Critique** : existence d’une vulnérabilité sur une ressource importante (application, machine virtuelle, groupe de sécurité réseau). Le problème doit être analysé.
    - **Importante** : certaines étapes supplémentaires sont nécessaires pour terminer un processus ou éliminer une vulnérabilité.
    - **Modérée** : existence d’une vulnérabilité devant être prise en compte, mais qui ne nécessite aucune attention immédiate. Par défaut, les recommandations de niveau Faible ne sont pas affichées, mais vous pouvez filtrer les recommandations pour les faire apparaître.


- **ÉTAT**: état actuel de la recommandation :

    - **Ouverte** : la recommandation n’a pas encore été prise en compte.
    - **En cours** : la recommandation est actuellement appliquée à ces ressources ; aucune action de votre part n’est nécessaire.
    - **Résolue** : la recommandation a déjà été achevée. (Une fois problème résolu, l’entrée a été grisée).

Pour afficher des informations détaillées sur les recommandations, cliquez sur le nom de la mise à jour manquante dans la liste.

![Détails de la recommandation][6]

> [!NOTE]
> Les recommandations de sécurité ci-dessus sont les mêmes que celles figurant sous la mosaïque **Recommandations**. Pour plus d’informations sur la résolution des recommandations, consultez l’article [Implémentation des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md).
>
>

### <a name="vms-and-computers"></a>Machines virtuelles et ordinateurs
La section Machines virtuelles et ordinateurs vous fournit une vue d’ensemble de toutes les recommandations relatives aux machines virtuelles et aux ordinateurs. Chaque colonne représente un ensemble de recommandations, comme illustré dans la capture d’écran suivante :

![Recommandations relatives aux machines virtuelles et aux ordinateurs][7]

Cette liste contient quatre types d’icônes :

![Ordinateur extérieur à Azure][8] Ordinateur ne relevant pas d’Azure.

![Machine virtuelle Azure Resource Manager][9] Machine virtuelle Azure Resource Manager.

![Machine virtuelle Azure Classic][10] Machine virtuelle Azure Classic.

![Machines virtuelles identifiées à partir de l’espace de travail][11] Machines virtuelles identifiées uniquement à partir de l’espace de travail qui fait partie de l’abonnement affiché. Cela inclut les machines virtuelles d’autres abonnements qui se rapportent à l’espace de travail associé à cet abonnement, ainsi que les machines virtuelles qui ont été installées avec l’agent SCOM Direct et qui ne possèdent pas d’ID de ressource.

L’icône qui s’affiche sous chaque recommandation vous aide à identifier rapidement la machine virtuelle et l’ordinateur qui requièrent votre attention, ainsi que le type de recommandation. Vous pouvez également utiliser l’option Filtre pour sélectionner les options qui s’afficheront sur cet écran.

![Filtrer][12]

Dans l’exemple précédent, une machine virtuelle a une recommandation critique concernant la protection de point de terminaison. Sélectionnez la machine virtuelle pour obtenir plus d’informations :

![Recommandation critique][13]

Ce panneau comporte des informations détaillées sur la sécurité de la machine virtuelle ou de l’ordinateur. L’action recommandée et le niveau de gravité de chaque problème sont affichés en bas.

### <a name="cloud-services"></a>Services cloud
Pour les services cloud, une recommandation est créée lorsque la version du système d’exploitation est obsolète, comme illustré dans la capture d’écran suivante :

![Services cloud][14]

Si vous avez une recommandation (ce qui n’est pas le cas dans l’exemple précédent), vous devez suivre les étapes de la recommandation pour mettre à jour la version du système d’exploitation. Lorsqu’une mise à jour est disponible, vous recevez une alerte (rouge ou orange en fonction de la gravité du problème). Lorsque vous sélectionnez cette alerte dans les lignes WebRole1 (exécute Windows Server avec votre application web automatiquement déployée sur IIS) ou WorkerRole1 (exécute Windows Server avec votre application web automatiquement déployée sur IIS), vous obtenez des informations détaillées sur cette recommandation, comme illustré dans la capture d’écran suivante :

![WorkerRole1][15]

Pour voir une explication plus normative concernant cette recommandation, cliquez sur **Mettre à jour la version du système d’exploitation** sous la colonne **DESCRIPTION**.

![Mettre à jour la version du système d’exploitation][16]

### <a name="app-services-preview"></a>App Services (préversion)

> [!NOTE]
> La surveillance d’App Service est uniquement disponible en préversion, au niveau Standard du Security Center. Consultez [Tarification](security-center-pricing.md) pour en savoir plus sur les niveaux tarifaires de Security Center.
>
>

Sous **App Services**, vous trouverez la liste de vos environnements App Service et le résumé de l'intégrité basé sur l’évaluation effectuée par Security Center.

![App Services][17]

Cette liste contient trois types d’icônes :

![Environnement App Services][18] Environnement App Services.

![Application web][19] Application web.

![Application de fonction][24] Application de fonction.

1. Sélectionnez une application web. Une vue récapitulative s’ouvre avec trois onglets :

  - **Recommandations** : basées sur les évaluations effectuées par Security Center et qui ont échoué.
  - **Évaluations passées** : liste des évaluations effectuées par Security Center et qui ont réussi.
  - **Évaluations non disponibles** : liste des évaluations qui n’ont pas été exécutées en raison d’une erreur ou la recommandation n’est pas pertinente pour l’App Service en question.

  Sous **Recommandations** se trouve la liste des recommandations pour l’application web sélectionnée, avec le niveau de gravité pour chaque recommandation.

  ![Vue Summary (Résumé)][20]

2. Sélectionnez une recommandation pour obtenir une description de la recommandation et la liste des ressources défectueuses, saines et non analysées.

  ![Description de la recommandation][21]

  Sous **Évaluations passées** se trouve une liste d’évaluations réussies.  L’état de gravité de ces évaluations est toujours vert.

  ![Évaluations réussies][22]

3. Sélectionnez une évaluation réussie dans la liste pour obtenir une description de l’évaluation et la liste des ressources défectueuses, saines et non analysées. Il existe un onglet pour les ressources défectueuses, mais cette liste est toujours vide car l’évaluation a réussi.

    ![Ressources saines][23]

## <a name="compute-and-app-recommendations"></a>Suggestions de calcul et d’applications
|Type de ressource|Degré de sécurisation|Recommandation|Description|
|----|----|----|----|
|Ordinateur|50|Installer l'agent d'analyse sur vos machines|Installer l'agent d'analyse pour activer la collecte de données, l'analyse des mises à jour, l'analyse de la ligne de base et la protection du point de terminaison sur chaque machine.|
|Ordinateur|50|Activer le provisionnement et la collecte automatiques des données pour vos abonnements |Activez le provisionnement et la collecte automatiques des données pour les machines dans vos abonnements afin d’activer la collecte de données, l'analyse des mises à jour, l'analyse de la ligne de base et la protection du point de terminaison sur chaque machine ajoutée à vos abonnements.|
|Ordinateur|40|Résoudre les problèmes d'intégrité de l'agent d'analyse sur vos machines|Pour une protection Security Center complète, corriger les problèmes de l’agent de surveillance sur vos machines en suivant les instructions du guide de résolution des problèmes| 
|Ordinateur|40|Résoudre les problèmes d'intégrité de la protection du point de terminaison sur vos machines|Pour une protection Security Center complète, corrigez les problèmes de l’agent de surveillance sur vos machines en suivant les instructions du guide de résolution des problèmes.|
|Ordinateur|40|Résoudre les problèmes de données d'analyse manquantes sur vos machines|Résolvez les problèmes de données d’analyse manquantes sur les machines virtuelles et les ordinateurs. Les données d’analyse manquantes sur vos machines entraînent des évaluations de sécurité manquantes telles que l'analyse des mises à jour, l'analyse de la ligne de base et un manque de solution d’analyse de protection du point de terminaison.|
|Ordinateur|40|Installer les mises à jour système sur vos machines|Installer les mises à jour critiques et de sécurité système manquantes afin de sécuriser vos machines virtuelles et ordinateurs Windows et Linux
|Ordinateur|40|Mettre à jour la version du système d'exploitation pour vos rôles de service cloud|Mettez à jour la version du système d'exploitation (SE) pour les rôles de votre service cloud vers la version la plus récente disponible pour la gamme de votre SE.|
|Ordinateur|35|Corriger les vulnérabilités dans la configuration de sécurité sur vos machines|Corrigez les vulnérabilités dans la configuration de la sécurité sur vos machines afin de les protéger des attaques. |
|Ordinateur|35|Corriger les vulnérabilités dans la configuration de sécurité sur vos conteneurs|Corriger les vulnérabilités dans la configuration de sécurité sur les ordinateurs où Docker est installé afin de les protéger contre les attaques.|
|Ordinateur|25|Activer les contrôles d'applications adaptatifs|Activez les contrôles d’applications pour contrôler les applications qui peuvent s’exécuter sur vos machines virtuelles hébergées dans Azure. Cela vous permet de renforcer la protection de vos machines virtuelles contre les logiciels malveillants. Security Center utilise le machine learning pour analyser les applications en cours d’exécution sur chaque machine virtuelle et vous permet d’appliquer des règles d’autorisation. Cette fonctionnalité simplifie le processus de configuration et de gestion des règles d’autorisation des applications.|
|Ordinateur|20|Installer la solution de protection de point de terminaison sur vos machines|Installez une solution de protection de point de terminaison sur vos machines virtuelles pour les protéger des menaces et des vulnérabilités.|
|Ordinateur|20|Redémarrer vos machines pour appliquer les mises à jour système|Redémarrez vos machines pour appliquer les mises à jour système et protéger la machine contre les vulnérabilités.|
|App Service|20|L'application web ne doit pas être accessible via HTTPS|Limitez l’accès des applications web via le protocole HTTPS uniquement.|
|App Service|20|Function App ne doit pas être accessible via HTTPS|Limitez l’accès d’applications de fonction via HTTPS uniquement.|
|Ordinateur|15|Appliquer le chiffrement de disque sur vos machines virtuelles|Chiffrez vos disques de machine virtuelle à l’aide d’Azure Disk Encryption à la fois pour les machines virtuelles Windows et Linux. zure Disk Encryption (ADE) s’appuie sur les fonctionnalités standard de l’industrie BitLocker de Windows et DM-Crypt de Linux pour fournir le chiffrement du disque de données et du système d’exploitation, afin de protéger vos données et de respecter les engagements de sécurité et de conformité de votre organisation dans le coffre de clés Azure client. Lorsque vos besoins de conformité et de sécurité vous imposent de chiffrer les données de bout en bout à l’aide de vos clés de chiffrement, y compris le chiffrement du disque éphémère (temporaire et attaché localement), utilisez Azure Disk Encryption. Par ailleurs, par défaut, les disques managés sont chiffrés au repos à l’aide d’Azure Storage Service Encryption, où les clés de chiffrement sont des clés gérées par Microsoft dans Azure. Si cela répond à vos exigences de sécurité et de conformité, vous pouvez utiliser le chiffrement de disque managé par défaut pour répondre à vos besoins.|
|Ressources de calcul (Service Fabric)|10|Utiliser Azure Active Directory pour l'authentification cliente dans Service Fabric|Effectuez l'authentification cliente uniquement par le biais d'Azure Active Directory dans Service Fabric.|
|Ressources de calcul (compte Automation)|5.| Activer le chiffrement du compte Automation|Activez le chiffrement des ressources variables du compte Automation lors du stockage de données sensibles.|
|App Service|5.|Activer les journaux de diagnostic dans App Service|Activer les journaux et les conserver un an maximum. Permet de recréer les pistes d'activité à des fins d'investigation en cas d'incident de sécurité ou de compromission du réseau. |
|Ressources de calcul (Load Balancer)|5.|Activer les journaux de diagnostic dans Load Balancer|Activer les journaux et les conserver un an maximum. Permet de recréer les pistes d'activité à des fins d'investigation en cas d'incident de sécurité ou de compromission du réseau. |
|Ressources de calcul (recherche)|5.|Activer les journaux de diagnostic dans le service de recherche|Activer les journaux et les conserver un an maximum. Permet de recréer les pistes d'activité à des fins d'investigation en cas d'incident de sécurité ou de compromission du réseau. |
|Ressources de calcul (Service Bus)|5.|Activer les journaux de diagnostic dans Service Bus|Activer les journaux et les conserver un an maximum. Permet de recréer les pistes d'activité à des fins d'investigation en cas d'incident de sécurité ou de compromission du réseau. |
|Ressources de calcul (Stream Analytics)|5.|Activer les journaux de diagnostic dans Azure Stream Analytics|Activer les journaux et les conserver un an maximum. Permet de recréer les pistes d'activité à des fins d'investigation en cas d'incident de sécurité ou de compromission du réseau. |
|Ressources de calcul (Service Fabric)|5.|Activer les journaux de diagnostic dans Service Fabric|Activer les journaux et les conserver un an maximum. Permet de recréer les pistes d'activité à des fins d'investigation en cas d'incident de sécurité ou de compromission du réseau. |
|Ressources de calcul (Batch)|5.|Activer les journaux de diagnostic dans les comptes Batch|Activer les journaux et les conserver un an maximum. Permet de recréer les pistes d'activité à des fins d'investigation en cas d'incident de sécurité ou de compromission du réseau. |
|Ressources de calcul (Event Hub)|5.|Activer les journaux de diagnostic dans Event Hub|Activer les journaux et les conserver un an maximum. Permet de recréer les pistes d'activité à des fins d'investigation en cas d'incident de sécurité ou de compromission du réseau. |
|Ressources de calcul (Logic Apps)|5.|Activer les journaux de diagnostic dans Logic Apps|Activer les journaux et les conserver un an maximum. Permet de recréer les pistes d’activité à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. |
|Ordinateur|30|Installer une solution d'évaluation des vulnérabilités sur vos machines virtuelles|Installer une solution d'évaluation des vulnérabilités sur vos machines virtuelles|
|Ordinateur|15|Ajouter un pare-feu d’applications web| Déployez une solution de pare-feu d’applications web (WAF) pour sécuriser vos applications web. |
|Ordinateur|30|Corriger les vulnérabilités - avec une solution d'évaluation de la vulnérabilité|Les machines virtuelles pour lesquelles une solution tierce d’évaluation des vulnérabilités est déployée sont évaluées en permanence afin d’y détecter d’éventuelles vulnérabilités au niveau de l’application et du système d’exploitation. Chaque fois que ces vulnérabilités sont détectées, elles sont disponibles pour plus d’informations dans le cadre de la recommandation.|
|Ressources de calcul (Service Fabric)|15|Définir la propriété ClusterProtectionLevel sur EncryptAndSign dans Service Fabric|Service Fabric fournit trois niveaux de protection (None, Sign et EncryptAndSign) pour la communication nœud à nœud à l’aide d’un certificat de cluster principal.  Définissez le niveau de protection pour vous assurer que tous les messages de nœud à nœud sont chiffrés et signés numériquement. |
|App Service|10|Le débogage à distance doit être désactivé pour l'application web|Désactivez le débogage pour les applications web si vous n’en avez plus besoin. Le débogage distant requiert que des ports d’entrée soient ouverts sur une Function App.|
|App Service|10|Le débogage à distance devrait être désactivé pour Function App|Désactivez le débogage pour Function App si vous n’en avez plus besoin. Le débogage distant requiert que des ports d’entrée soient ouverts sur une Function App.|
|App Service|10|Configurer des restrictions d’adresse IP pour l’application web|Définissez une liste d’adresses IP autorisées à accéder à votre application. L’utilisation de restrictions d’adresse IP protège une application web contre des attaques courantes.|
|App Service|10|Configurer les restrictions IP pour Function App| Définissez une liste d’adresses IP autorisées à accéder à votre application. L’utilisation de restrictions d’adresse IP protège une application de fonction contre des attaques courantes.|
|App Service|10|Ne pas autoriser toutes (’*’) les ressources à accéder à votre application| Ne pas autoriser la définition du paramètre WEBSITE_LOAD_CERTIFICATES sur "". La définition du paramètre sur ‘’ signifie que tous les certificats sont chargés dans votre magasin de certificats personnels d’applications web. Cela peut conduire à un abus du principe des privilèges minimum, car il est peu probable que le site ait besoin d’accéder à tous les certificats lors de l’exécution.|
|App Service|5.|Web Sockets doit être désactivé pour l’application web|Examinez l’utilisation de Web Sockets à l’intérieur d’applications web. Le protocole Web Sockets est vulnérable à différents types de menaces de sécurité.|
|App Service|5.|Web Sockets devrait être désactivé pour Function App|Examinez l’utilisation de Web Sockets à l’intérieur de Function App. Le protocole Web Sockets est vulnérable à différents types de menaces de sécurité.|
|App Service|5.|Utiliser des domaines personnalisés pour votre application web|Utilisez des domaines personnalisés pour protéger une application web contre des attaques courantes telles que l’hameçonnage et d’autres attaques liées au DNS.|
|App Service|5.|Utiliser des domaines personnalisés pour Function App|Utilisez des domaines personnalisés pour protéger une application de fonction contre des attaques courantes telles que l’hameçonnage et d’autres attaques liées au DNS.|
|Ressources de calcul (Batch)|1|Configurer les règles d'alerte sur les métriques pour le compte Batch|Configurer les règles d'alerte pour les métriques sur le compte Batch et activer les métriques Pool Delete Complete Events et Pool Delete Start Events|
|Ressources de calcul (Service Bus)|1|Supprimer toutes les règles d'autorisation, sauf RootManageSharedAccessKey de l'espace de noms Service Bus |Les clients Service Bus ne doivent pas utiliser une stratégie d'accès au niveau de l'espace de noms qui donne accès à l'ensemble des files d'attente et rubriques d'un espace de noms. Pour respecter le modèle de sécurité basé sur le privilège minimum, vous devez créer des stratégies d'accès au niveau de l'entité pour les files d'attente et les rubriques afin de limiter l'accès à l'entité spécifique.|
|Ressources de calcul (Event Hub)|1|Supprimer toutes les règles d'autorisation, sauf RootManageSharedAccessKey de l'espace de noms Event Hub |Les clients Event Hub ne doivent pas utiliser une stratégie d'accès au niveau de l'espace de noms qui donne accès à l'ensemble des files d'attente et rubriques d'un espace de noms. Pour respecter le modèle de sécurité basé sur le privilège minimum, vous devez créer des stratégies d'accès au niveau de l'entité pour les files d'attente et les rubriques afin de limiter l'accès à l'entité spécifique.|
|Ressources de calcul (Event Hub)|5.|Définir des règles d'autorisation sur l'entité Event Hub|Auditez des règles d'autorisation sur l'entité Event Hub pour accorder un accès à privilèges minimum.|
|Ordinateur|30|Installer une solution d'évaluation des vulnérabilités sur vos machines virtuelles|Installer une solution d'évaluation des vulnérabilités sur vos machines virtuelles|
|App Service|20|CORS ne devrait pas autoriser toutes les ressources à accéder à vos applications web|Autorisez uniquement les domaines requis à interagir avec votre application web. Le partage des ressources cross-origin (CORS) ne devrait pas autoriser tous les domaines à accéder à votre application web.|
|App Service|20|CORS ne devrait pas autoriser toutes les ressources à accéder à votre Function App| Recommande d’autoriser uniquement les domaines requis à interagir avec votre application de fonction. Le partage des ressources cross-origin (CORS) ne devrait pas autoriser tous les domaines à accéder à votre application de fonction.|
|Ordinateur|15|Ajouter un pare-feu d’applications web| Déployez une solution de pare-feu d’applications web (WAF) pour sécuriser vos applications web. |
|App Service|10|Utiliser la dernière version de .NET Framework prise en charge pour l’application web|Utilisez la dernière version de .NET Framework pour les classes de sécurité les plus récentes. L’utilisation de classes et types plus anciens peut rendre votre application vulnérable.|
|App Service|10|Utiliser la dernière version de Java prise en charge pour l’application web|Utilisez la dernière version de Java pour les classes de sécurité les plus récentes. L’utilisation de classes et types plus anciens peut rendre votre application vulnérable.|
|App Service|10|Utiliser la dernière version de PHP prise en charge pour l’application web|Utilisez la dernière version de PHP pour les classes de sécurité les plus récentes. L’utilisation de classes et types plus anciens peut rendre votre application vulnérable.|
|App Service|10|Utiliser la dernière version de Node.js prise en charge pour l’application web|Utilisez la dernière version de Node.js pour les classes de sécurité les plus récentes. L’utilisation de classes et types plus anciens peut rendre votre application vulnérable.|
|App Service|10|Utiliser la dernière version de Python prise en charge pour l'application web|Utilisez la dernière version de Python pour les classes de sécurité les plus récentes. L’utilisation de classes et types plus anciens peut rendre votre application vulnérable.|
|Machines virtuelles et ordinateurs|1|Migrer les machines virtuelles vers de nouvelles ressources AzureRM|Profitez des améliorations apportées à Azure Resource Manager v2 pour renforcer la sécurité de vos machines virtuelles : contrôle d'accès plus puissant, audit amélioré, déploiement et gouvernance basés sur ARM, accès aux identités managées, accès au coffre de clés pour les secrets, authentification basée sur Azure AD, prise en charge des balises et des groupes de ressources pour faciliter la gestion de la sécurité. |
|Ordinateur|30|Corriger les vulnérabilités - avec une solution d'évaluation de la vulnérabilité|Les machines virtuelles pour lesquelles une solution tierce d’évaluation des vulnérabilités est déployée sont évaluées en permanence afin d’y détecter d’éventuelles vulnérabilités au niveau de l’application et du système d’exploitation. Chaque fois que ces vulnérabilités sont détectées, elles sont disponibles pour plus d’informations dans le cadre de la recommandation.|




## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressources Azure, consultez les rubriques suivantes :


* [Comprendre les recommandations d’Azure Security Center pour les machines virtuelles](security-center-virtual-machine-recommendations.md)
* [Surveiller l’identité et l’accès dans Azure Security Center](security-center-identity-access.md)
* [Protection de votre réseau dans Azure Security Center](security-center-network-recommendations.md)
* [Protection de votre service SQL Azure dans Azure Security Center](security-center-sql-service-recommendations.md)

Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-azure-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.

<!--Image references-->
[1]: ./media/security-center-virtual-machine-recommendations/overview.png
[2]: ./media/security-center-virtual-machine-recommendations/compute.png
[3]: ./media/security-center-virtual-machine-recommendations/monitoring-agent-health-issues.png
[4]: ./media/security-center-virtual-machine-recommendations/compute-recommendations.png
[5]: ./media/security-center-virtual-machine-recommendations/apply-system-updates.png
[6]: ./media/security-center-virtual-machine-recommendations/missing-update-details.png
[7]: ./media/security-center-virtual-machine-recommendations/vm-computers.png
[8]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png
[9]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png
[10]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png
[11]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png
[12]: ./media/security-center-virtual-machine-recommendations/filter.png
[13]: ./media/security-center-virtual-machine-recommendations/vm-detail.png
[14]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png
[15]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new3.png
[16]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png
[17]: ./media/security-center-virtual-machine-recommendations/app-services.png
[18]: ./media/security-center-virtual-machine-recommendations/ase.png
[19]: ./media/security-center-virtual-machine-recommendations/web-app.png
[20]: ./media/security-center-virtual-machine-recommendations/recommendation.png
[21]: ./media/security-center-virtual-machine-recommendations/recommendation-desc.png
[22]: ./media/security-center-virtual-machine-recommendations/passed-assessment.png
[23]: ./media/security-center-virtual-machine-recommendations/healthy-resources.png
[24]: ./media/security-center-virtual-machine-recommendations/function-app.png
