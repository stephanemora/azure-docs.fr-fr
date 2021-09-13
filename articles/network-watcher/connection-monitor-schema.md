---
title: Schémas du Moniteur de connexion Azure Network Watcher | Microsoft Docs
description: Comprendre le schéma des données de tests et le schéma de données de chemin d’accès du Moniteur de connexion Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: mjha
manager: vinigam
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/05/2021
ms.author: mjha
ms.openlocfilehash: 4cad1ea0d90f85a12e7d7f9b7dbc869a61a91a39
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122969763"
---
# <a name="azure-network-watcher-connection-monitor-schemas"></a>Schémas du Moniteur de connexion Azure Network Watcher

La fonctionnalité Moniteur de connexion permet une vérification unifiée et de bout en bout de la connectivité dans Azure Network Watcher. Elle prend en charge les déploiements hybrides et cloud Azure. Network Watcher fournit des outils pour surveiller, diagnostiquer et consulter les métriques de connectivité de vos déploiements Azure.

Voici quelques cas d’usage de Moniteur de connexion :

- Votre machine virtuelle de serveur web front-end communique avec une machine virtuelle de serveur de base de données dans une application multiniveau. Vous souhaitez vérifier la connectivité réseau entre les deux machines virtuelles.
- Vous souhaitez que les machines virtuelles de la région USA Est puissent effectuer un test Ping ciblant les machines virtuelles de la région USA Centre, et vous souhaitez comparer les temps de réponse du réseau entre les régions.
- Vous disposez de plusieurs sites locaux à Seattle, Washington et Ashburn (Virginie). Vos sites de bureau se connectent à des URL Microsoft 365. Pour vos utilisateurs d’URL Microsoft 365, comparez les latences entre Seattle et Ashburn.
- Votre application hybride doit être connectée à un point de terminaison Stockage Azure. Votre site local et votre application Azure se connectent au même point de terminaison Stockage Azure. Vous souhaitez comparer les temps de réponse du site local avec ceux de l'application Azure.
- Vous souhaitez vérifier la connectivité entre vos installations locales et les machines virtuelles Azure qui hébergent votre application cloud.

Moniteur de connexion présente notamment les avantages suivants :

* Expérience unifiée et intuitive pour les besoins de supervision des clouds Azure et hybride
* Surveillance de la connectivité entre les régions et entre les espaces de travail
* Fréquences de sondage supérieures et meilleure visibilité des performances du réseau
* Alertes plus rapides pour vos déploiements hybrides
* Prise en charge des vérifications de la connectivité reposant sur HTTP, TCP et ICMP 
* Prise en charge des métriques et de Log Analytics pour les initialisations (tearDown) de test Azure et non Azure

Deux types de journaux ou de données sont ingérés dans Log Analytics. Les données de test (requête NWConnectionMonitorTestResult) sont mises à jour en fonction de la fréquence d’analyse d’un groupe de test particulier. Les données de chemin d’accès (requête NWConnectionMonitorPathResult) sont mises à jour en cas de modification importante du pourcentage de perte ou du temps d’aller-retour. Les données de test peuvent continuer à être mises à jour pendant un certain temps tandis que les données de chemin d’accès ne seront pas fréquemment mises à jour, car elles sont indépendantes.

## <a name="connection-monitor-tests-schema"></a>Schéma de test du Moniteur de connexion

La table suivante répertorie et donne la signification des champs du schéma des données de test du Moniteur de connexion. 

| Champ  |    Description   |
|---|---|
| TimeGenerated | Horodatage (UTC) de la génération du journal |
| RecordId  | ID d’enregistrement pour l’identification unique de l’enregistrement des résultats de test |
| ConnectionMonitorResourceId   | ID de la ressource du Moniteur de connexion du test |
| TestGroupName | Nom du groupe de test auquel le test appartient |
| TestConfigurationName | Nom de la configuration de test à laquelle le test appartient |
| SourceType    | Type de l’ordinateur source configuré pour le test |
| SourceResourceId  | ID de ressource de la machine source |
| SourceAddress | Adresse de la source configurée pour le test |
| SourceSubnet  | Sous-réseau de la source |
| SourceIP  | Adresse IP de la source |
| SourceName    | Nom du point de terminaison source |
| SourceAgentId | ID de l’agent source |
| DestinationPort   | Port de destination configuré pour le test |
| DestinationType   | Type de l’ordinateur source configuré pour le test |
| DestinationResourceId | ID de la ressource de la machine de destination |
| DestinationAddress    | Adresse de la destination configurée pour le test |
| DestinationSubnet | Le cas échéant, le sous-réseau de la destination |
| DestinationIP | Adresse IP de la destination |
| DestinationName   | Nom du point de terminaison de destination |
| DestinationAgentId    | ID de l’agent de destination |
| Protocol  | Protocole du test |
| ChecksTotal   | Nombre total de vérifications effectuées dans le cadre du test |
| ChecksFailed  | Nombre total de vérifications échouées dans le cadre du test |
| TestResult    | Résultat du test |
| TestResultCriterion   | Les critères de résultat du test |
| ChecksFailedPercentThreshold  | Le seuil de pourcentage de vérification des échecs a été défini pour le test |
| RoundTripTimeMsThreshold  | Seuil d’aller-retour (en millisecondes) défini pour le test |
| MinRoundTripTimeMs    | Durée d’aller-retour minimale (en millisecondes) pour le test |
| MaxRoundTripTimeMs    | Durée d’aller-retour maximale pour le test |
| AvgRoundTripTimeMs    | Durée d’aller-retour moyenne pour le test |
| JitterMs  | Durée moyenne des boucles d’écart pour le test |
| AdditionalData    | Données supplémentaires pour le test |


## <a name="connection-monitor-path-schema"></a>Schéma du chemin d’accès du Moniteur de connexion

La table suivante répertorie et donne la signification des champs du schéma des données de chemin d’accès du Moniteur de connexion. 

| Champ  |    Description   |
|---|---|
| TimeGenerated  | Horodatage (UTC) de la génération du journal |
| RecordId  | ID d’enregistrement pour l’identification unique de l’enregistrement des résultats de test |
| TopologyId    | ID de la topologie de l’enregistrement du chemin d’accès |
| ConnectionMonitorResourceId   | ID de la ressource du Moniteur de connexion du test |
| TestGroupName | Nom du groupe de test auquel le test appartient |
| TestConfigurationName | Nom de la configuration de test à laquelle le test appartient |
| SourceType    | Type de l’ordinateur source configuré pour le test |
| SourceResourceId  | ID de ressource de la machine source |
| SourceAddress | Adresse de la source configurée pour le test |
| SourceSubnet  | Sous-réseau de la source |
| SourceIP  | Adresse IP de la source | 
| SourceName    | Nom du point de terminaison source |
| SourceAgentId | ID de l’agent source |
| DestinationPort   | Port de destination configuré pour le test |
| DestinationType   | Type de l’ordinateur source configuré pour le test |
| DestinationResourceId | ID de la ressource de la machine de destination |
| DestinationAddress    | Adresse de la destination configurée pour le test |
| DestinationSubnet | Le cas échéant, le sous-réseau de la destination |
| DestinationIP | Adresse IP de la destination |
| DestinationName   | Nom du point de terminaison de destination |
| DestinationAgentId    | ID de l’agent de destination |
| Protocol  | Protocole du test |
| ChecksTotal   | Nombre total de vérifications effectuées dans le cadre du test |
| ChecksFailed  | Nombre total de vérifications échouées dans le cadre du test |
| PathTestResult    | Résultat du test |
| PathResultCriterion   | Les critères de résultat du test | 
| ChecksFailedPercentThreshold  | Le seuil de pourcentage de vérification des échecs a été défini pour le test |
| RoundTripTimeMsThreshold  | Seuil d’aller-retour (en millisecondes) défini pour le test |
| MinRoundTripTimeMs    | Durée d’aller-retour minimale (en millisecondes) pour le test |
| MaxRoundTripTimeMs    | Durée d’aller-retour maximale pour le test |
| AvgRoundTripTimeMs    | Durée d’aller-retour moyenne pour le test |
| JitterMs  | Durée moyenne des boucles d’écart pour le test |
| HopAddresses | Adresses de tronçon identifiées pour le test |
| HopTypes  | Types de tronçons identifiés pour le test |
| HopLinkTypes  | Types de tronçons identifiés pour le test |
| HopResourceIds    | ID de ressource de tronçons identifiés pour le test |
| Problèmes    | Problèmes identifiés pour le test |
| Hops  | Tronçons identifiés pour le test |
| AdditionalData | Données supplémentaires pour le test |
