---
title: Recommandations relatives à Azure Security Center pour les machines et les applications
description: Recommandations relatives à la sécurité d’Azure Security Center qui vous permettent de protéger vos machines virtuelles, vos ordinateurs, vos applications web et vos environnements App Service.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2019
ms.author: memildin
ms.openlocfilehash: c0cf5951daf004a0c805b688f08d1ccfa4679615
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74781901"
---
# <a name="compute-and-app-recommendations---reference-guide"></a>Suggestions de calcul et d’applications - Guide de référence

Cet article fournit la liste complète des recommandations que vous pouvez voir dans Azure Security Center concernant les types de ressources suivants :

* Machines virtuelles et ordinateurs
* Groupes de machines virtuelles identiques (VMSS)
* Cloud Services
* App Services
* Containers
* Ressources de calcul

Vous trouverez une explication de la façon de les trouver et de les résoudre [ici](security-center-virtual-machine-protection.md).

## Suggestions de calcul et d’applications <a name="compute-and-app-recs"></a>
|Type de ressource|Degré de sécurisation|Recommandation|Description|
|----|----|----|----|
|App Service|20|L'application web ne doit pas être accessible via HTTPS|Limitez l’accès des applications web via le protocole HTTPS uniquement.|
|App Service|20|Function App ne doit pas être accessible via HTTPS|Limitez l’accès d’applications de fonction via HTTPS uniquement.|
|App Service|5\.|Les journaux de diagnostic doivent être activés dans App Services|Activez les journaux d’activité et conservez-les un an maximum. Permet de recréer les pistes d’activité à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. |
|App Service|10|Le débogage à distance doit être désactivé pour l'application web|Désactivez le débogage pour les applications web si vous n’en avez plus besoin. Le débogage distant requiert que des ports d’entrée soient ouverts sur une Function App.|
|App Service|10|Le débogage à distance devrait être désactivé pour Function App|Désactivez le débogage pour Function App si vous n’en avez plus besoin. Le débogage distant requiert que des ports d’entrée soient ouverts sur une Function App.|
|App Service|10|Ne pas autoriser toutes (’*’) les ressources à accéder à votre application| Ne pas autoriser la définition du paramètre WEBSITE_LOAD_CERTIFICATES sur "". La définition du paramètre sur ‘’ signifie que tous les certificats sont chargés dans votre magasin de certificats personnels d’applications web. Cela peut conduire à un abus du principe des privilèges minimum, car il est peu probable que le site ait besoin d’accéder à tous les certificats lors de l’exécution.|
|App Service|20|CORS ne devrait pas autoriser toutes les ressources à accéder à vos applications web|Autorisez uniquement les domaines requis à interagir avec votre application web. Le partage des ressources cross-origin (CORS) ne devrait pas autoriser tous les domaines à accéder à votre application web.|
|App Service|20|CORS ne devrait pas autoriser toutes les ressources à accéder à votre Function App| Recommande d’autoriser uniquement les domaines requis à interagir avec votre application de fonction. Le partage des ressources cross-origin (CORS) ne devrait pas autoriser tous les domaines à accéder à votre application de fonction.|
|Ressources de calcul (Batch)|1|Des règles d’alerte de métrique doivent être configurées sur les comptes Batch|Configurer les règles d'alerte pour les métriques sur le compte Batch et activer les métriques Pool Delete Complete Events et Pool Delete Start Events|
|Ressources de calcul (Service Fabric)|10|Les clusters Service Fabric ne doivent utiliser Azure Active Directory que pour l’authentification client|Effectuez l'authentification cliente uniquement par le biais d'Azure Active Directory dans Service Fabric.|
|Ressources de calcul (compte Automation)|5\.|Les variables de compte Automation doivent être chiffrées|Activez le chiffrement des ressources variables du compte Automation lors du stockage de données sensibles.|
|Ressources de calcul (recherche)|5\.|Auditer l’activation des journaux de diagnostic dans le service Recherche|Activez les journaux d’activité et conservez-les un an maximum. Permet de recréer les pistes d'activité à des fins d'investigation en cas d'incident de sécurité ou de compromission du réseau. |
|Ressources de calcul (Service Bus)|5\.|Les journaux de diagnostic doivent être activés dans Service Bus|Activez les journaux d’activité et conservez-les un an maximum. Permet de recréer les pistes d'activité à des fins d'investigation en cas d'incident de sécurité ou de compromission du réseau. |
|Ressources de calcul (Stream Analytics)|5\.|Les journaux de diagnostic dans Azure Stream Analytics doivent être activés|Activez les journaux d’activité et conservez-les un an maximum. Permet de recréer les pistes d'activité à des fins d'investigation en cas d'incident de sécurité ou de compromission du réseau. |
|Ressources de calcul (Batch)|5\.|Activer les journaux de diagnostic dans les comptes Batch|Activez les journaux d’activité et conservez-les un an maximum. Permet de recréer les pistes d'activité à des fins d'investigation en cas d'incident de sécurité ou de compromission du réseau. |
|Ressources de calcul (Event Hub)|5\.|Les journaux de diagnostic doivent être activés dans Event Hub|Activez les journaux d’activité et conservez-les un an maximum. Permet de recréer les pistes d'activité à des fins d'investigation en cas d'incident de sécurité ou de compromission du réseau. |
|Ressources de calcul (Logic Apps)|5\.|Activer les journaux de diagnostic dans Logic Apps|Activez les journaux d’activité et conservez-les un an maximum. Permet de recréer les pistes d'activité à des fins d'investigation en cas d'incident de sécurité ou de compromission du réseau. |
|Ressources de calcul (Service Fabric)|15|Définir la propriété ClusterProtectionLevel sur EncryptAndSign dans Service Fabric|Service Fabric fournit trois niveaux de protection (None, Sign et EncryptAndSign) pour la communication nœud à nœud à l’aide d’un certificat de cluster principal. Définissez le niveau de protection pour vous assurer que tous les messages de nœud à nœud sont chiffrés et signés numériquement. |
|Ressources de calcul (Service Bus)|1|Supprimer toutes les règles d'autorisation, sauf RootManageSharedAccessKey de l'espace de noms Service Bus |Les clients Service Bus ne doivent pas utiliser une stratégie d'accès au niveau de l'espace de noms qui donne accès à l'ensemble des files d'attente et rubriques d'un espace de noms. Pour respecter le modèle de sécurité basé sur le privilège minimum, vous devez créer des stratégies d'accès au niveau de l'entité pour les files d'attente et les rubriques afin de limiter l'accès à l'entité spécifique.|
|Ressources de calcul (Event Hub)|1|Toutes les règles d’autorisation, sauf RootManageSharedAccessKey, doivent être supprimées de l’espace de noms Event Hub|Les clients Event Hub ne doivent pas utiliser une stratégie d'accès au niveau de l'espace de noms qui donne accès à l'ensemble des files d'attente et rubriques d'un espace de noms. Pour respecter le modèle de sécurité basé sur le privilège minimum, vous devez créer des stratégies d'accès au niveau de l'entité pour les files d'attente et les rubriques afin de limiter l'accès à l'entité spécifique.|
|Ressources de calcul (Event Hub)|5\.|Des règles d’autorisation sur l’entité Event Hub doivent être définies|Auditez des règles d'autorisation sur l'entité Event Hub pour accorder un accès à privilèges minimum.|
|Ordinateur|50|Installer l'agent d'analyse sur vos machines|Installer l'agent d'analyse pour activer la collecte de données, l'analyse des mises à jour, l'analyse de la ligne de base et la protection du point de terminaison sur chaque machine.|
|Ordinateur|50|Activer le provisionnement et la collecte automatiques des données pour vos abonnements |Activez le provisionnement et la collecte automatiques des données pour les machines dans vos abonnements afin d’activer la collecte de données, l'analyse des mises à jour, l'analyse de la ligne de base et la protection du point de terminaison sur chaque machine ajoutée à vos abonnements.|
|Ordinateur|40|Résoudre les problèmes d'intégrité de l'agent d'analyse sur vos machines|Pour une protection Security Center complète, corriger les problèmes de l’agent de surveillance sur vos machines en suivant les instructions du guide de résolution des problèmes| 
|Ordinateur|40|Résoudre les problèmes d'intégrité de la protection du point de terminaison sur vos machines|Pour une protection Security Center complète, corrigez les problèmes de l’agent de surveillance sur vos machines en suivant les instructions du guide de résolution des problèmes.|
|Ordinateur|40|Résoudre les problèmes de données d'analyse manquantes sur vos machines|Résolvez les problèmes de données d’analyse manquantes sur les machines virtuelles et les ordinateurs. Les données d’analyse manquantes sur vos machines entraînent des évaluations de sécurité manquantes telles que l'analyse des mises à jour, l'analyse de la ligne de base et un manque de solution d’analyse de protection du point de terminaison.|
|Ordinateur|40|Des mises à jour système doivent être installées sur vos machines|Installer les mises à jour critiques et de sécurité système manquantes afin de sécuriser vos machines virtuelles et ordinateurs Windows et Linux
|Ordinateur|15|Ajouter un pare-feu d’applications web| Déployez une solution de pare-feu d’applications web (WAF) pour sécuriser vos applications web. |
|Ordinateur|40|Mettre à jour la version du système d'exploitation pour vos rôles de service cloud|Mettez à jour la version du système d'exploitation (SE) pour les rôles de votre service cloud vers la version la plus récente disponible pour la gamme de votre SE.|
|Ordinateur|35|Les vulnérabilités de la configuration de sécurité sur vos machines doivent être corrigées|Corrigez les vulnérabilités dans la configuration de la sécurité sur vos machines afin de les protéger des attaques.|
|Ordinateur|35|Corriger les vulnérabilités dans la configuration de sécurité sur vos conteneurs|Corriger les vulnérabilités dans la configuration de sécurité sur les ordinateurs où Docker est installé afin de les protéger contre les attaques.|
|Ordinateur|25|Activer les contrôles d'applications adaptatifs|Activez les contrôles d’applications pour contrôler les applications qui peuvent s’exécuter sur vos machines virtuelles hébergées dans Azure. Cela vous permet de renforcer la protection de vos machines virtuelles contre les logiciels malveillants. Security Center utilise le machine learning pour analyser les applications en cours d’exécution sur chaque machine virtuelle et vous permet d’appliquer des règles d’autorisation. Cette fonctionnalité simplifie le processus de configuration et de gestion des règles d’autorisation des applications.|
|Ordinateur|20|Installer la solution de protection de point de terminaison sur vos machines|Installez une solution de protection de point de terminaison sur vos machines virtuelles pour les protéger des menaces et des vulnérabilités.|
|Ordinateur|20|Redémarrer vos machines pour appliquer les mises à jour système|Redémarrez vos machines pour appliquer les mises à jour système et protéger la machine contre les vulnérabilités.|
|Ordinateur|15|Le chiffrement de disque doit être appliqué sur les machines virtuelles|Chiffrez vos disques de machine virtuelle à l’aide d’Azure Disk Encryption à la fois pour les machines virtuelles Windows et Linux. zure Disk Encryption (ADE) s’appuie sur les fonctionnalités standard de l’industrie BitLocker de Windows et DM-Crypt de Linux pour fournir le chiffrement du disque de données et du système d’exploitation, afin de protéger vos données et de respecter les engagements de sécurité et de conformité de votre organisation dans le coffre de clés Azure client. Lorsque vos besoins de conformité et de sécurité vous imposent de chiffrer les données de bout en bout à l’aide de vos clés de chiffrement, y compris le chiffrement du disque éphémère (temporaire et attaché localement), utilisez Azure Disk Encryption. Par ailleurs, par défaut, les disques managés sont chiffrés au repos à l’aide d’Azure Storage Service Encryption, où les clés de chiffrement sont des clés gérées par Microsoft dans Azure. Si cela répond à vos exigences de sécurité et de conformité, vous pouvez utiliser le chiffrement de disque managé par défaut pour répondre à vos besoins.|
|Ordinateur|30|Installer une solution d'évaluation des vulnérabilités sur vos machines virtuelles|Installer une solution d'évaluation des vulnérabilités sur vos machines virtuelles|
|Ordinateur|15|Ajouter un pare-feu d’applications web| Déployez une solution de pare-feu d’applications web (WAF) pour sécuriser vos applications web. |
|Ordinateur|30|Les vulnérabilités doivent être corrigées avec une solution d’évaluation des vulnérabilités|Les machines virtuelles pour lesquelles une solution tierce d’évaluation des vulnérabilités est déployée sont évaluées en permanence afin d’y détecter d’éventuelles vulnérabilités au niveau de l’application et du système d’exploitation. Chaque fois que ces vulnérabilités sont détectées, elles sont disponibles pour plus d’informations dans le cadre de la recommandation.|
|Ordinateur|30|Installer une solution d'évaluation des vulnérabilités sur vos machines virtuelles|Installer une solution d'évaluation des vulnérabilités sur vos machines virtuelles|
|Ordinateur|1|Les machines virtuelles doivent être migrées vers de nouvelles ressources AzureRM|Utilisez Azure Resource Manager pour vos machines virtuelles afin de fournir des améliorations de sécurité comme : contrôle d’accès renforcé (RBAC), audit amélioré, déploiement et gouvernance basés sur Resource Manager, accès aux identités managées, accès au coffre de clés pour les secrets, authentification basée sur Azure AD, prise en charge des étiquettes et des groupes de ressources pour faciliter la gestion de la sécurité. |
|Ordinateur|30|Les vulnérabilités doivent être corrigées avec une solution d’évaluation des vulnérabilités|Les machines virtuelles pour lesquelles une solution tierce d’évaluation des vulnérabilités est déployée sont évaluées en permanence afin d’y détecter d’éventuelles vulnérabilités au niveau de l’application et du système d’exploitation. Chaque fois que ces vulnérabilités sont détectées, elles sont disponibles pour plus d’informations dans le cadre de la recommandation.|
|Jeu de mise à l’échelle de machine virtuelle |4|Les journaux de diagnostic dans Virtual Machine Scale Sets doivent être activés|Activez les journaux d’activité et conservez-les pendant jusqu’à un an. Cela vous permet de recréer les pistes d’activité à des fins d’investigation. Cela est utile lorsqu’un incident de sécurité se produit, ou quand votre réseau est compromis.|
|Jeu de mise à l’échelle de machine virtuelle|35|Les vulnérabilités détectées dans la configuration de la sécurité de vos groupes de machines virtuelles identiques doivent être corrigées|Corriger les vulnérabilités dans la configuration de la sécurité sur vos groupes de machines virtuelles identiques afin de les protéger des attaques. |
|Jeu de mise à l’échelle de machine virtuelle|5\.|Corriger les échecs d’intégrité de la protection de point de terminaison sur les groupes de machines virtuelles identiques|Corrigez les échecs d’intégrité de la protection du point de terminaison sur vos groupes de machines virtuelles identiques afin de les protéger des menaces et des vulnérabilités. |
|Jeu de mise à l’échelle de machine virtuelle|10|Endpoint Protection doit être installé sur les machines virtuelles|Installer une solution de protection de point de terminaison sur vos groupes de machines virtuelles identiques, pour les protéger des menaces et des vulnérabilités. |
|Jeu de mise à l’échelle de machine virtuelle|40|Les mises à jour système doivent être installées sur les groupes de machines virtuelles identiques|Installer les mises à jour critiques et de sécurité système manquantes afin de sécuriser vos groupes de machines virtuelles identiques Windows et Linux. |
|


## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressources Azure, consultez les rubriques suivantes :

* [Surveiller l’identité et l’accès dans Azure Security Center](security-center-identity-access.md)
* [Protection de votre réseau dans Azure Security Center](security-center-network-recommendations.md)
* [Protection de votre service SQL Azure dans Azure Security Center](security-center-sql-service-recommendations.md)
