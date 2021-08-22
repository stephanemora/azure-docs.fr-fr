---
title: Connecter Thycotic Secret Server à Azure Sentinel | Microsoft Docs
description: Découvrez comment utiliser le connecteur de données Thycotic Secret Server pour exporter les journaux de Thycotic Secret Server dans Azure Sentinel. Affichez les données Thycotic Secret Server dans des classeurs, créez des alertes et améliorez l’examen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: b023745d8882d639cf9a39fd483dd7b82d1a782d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531833"
---
# <a name="connect-your-thycotic-secret-server-to-azure-sentinel"></a>Connecter Thycotic Secret Server à Azure Sentinel

> [!IMPORTANT]
> Le connecteur Thycotic Secret Server est actuellement disponible en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Cet article explique comment connecter votre appliance Thycotic Secret Server à Azure Sentinel. Le connecteur de données Thycotic Secret Server vous permet de connecter facilement vos journaux Thycotic Secret Server à Azure Sentinel, ce qui vous permet ensuite de consulter les données dans des classeurs, de les utiliser pour créer des alertes personnalisées et de les incorporer pour améliorer l’investigation. L’intégration entre Thycotic et Azure Sentinel utilise le connecteur de données CEF pour analyser et afficher correctement les messages Secret Server Syslog.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’autorisations en lecture et en écriture dans l’espace de travail Azure Sentinel.

- Vous devez disposer d’autorisations de lecture sur les clés partagées pour accéder à l’espace de travail.

- Votre appliance Thycotic Secret Server doit être configurée pour exporter les journaux via Syslog.

## <a name="send-thycotic-secret-server-logs-to-azure-sentinel"></a>Envoyer des journaux Thycotic Secret Server à Azure Sentinel

Pour récupérer ses journaux dans Azure Sentinel, configurez votre appliance Thycotic Secret Server pour envoyer des messages Syslog au format CEF à votre serveur de transfert de journaux basé sur Linux (exécutant rsyslog ou syslog-ng). L’agent Log Analytics sera installé sur ce serveur et transfèrera les journaux à votre espace de travail Azure Sentinel.

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Dans la galerie **Connecteurs de données**, sélectionnez **Thycotic Secret Server (préversion)** , puis sélectionnez **Ouvrir la page du connecteur**.

1. Suivez les instructions de l’onglet **Instructions**, sous **Configuration** :

    1. Sous **1. Configurer l’agent Syslog Linux** : Effectuez cette étape si vous n’avez pas encore de redirecteur de journal en cours d’exécution, ou si vous en avez besoin d’un autre. Consultez [ÉTAPE 1 : Déployer le redirecteur de journal](connect-cef-agent.md) dans la documentation d’Azure Sentinel pour obtenir des instructions et des explications plus détaillées.

    1. Sous **2. Transférer les journaux d’activité au format CEF vers l’agent Syslog** : Suivez les instructions de Thycotic pour [configurer Secret Server](https://docs.thycotic.com/ssi/1.0.0/splunk/splunk-on-prem/config/syslog-events.md). Cette configuration doit inclure les éléments suivants :
        - Destination du journal : nom d’hôte et/ou adresse IP de votre serveur de transfert de journaux
        - Protocole et port : **TCP 514** (si cela est recommandé, veillez à effectuer la modification parallèle dans le démon Syslog sur votre serveur de transfert de journaux)
        - Format du journal : CEF
        - Types de journaux : tous ceux disponibles

    1. Sous **3. Valider la connexion**  : Vérifiez l’ingestion des données en copiant la commande sur la page du connecteur et en l’exécutant sur votre redirecteur de journal. Consultez [ÉTAPE 3 : Valider la connectivité](connect-cef-verify.md) dans la documentation d’Azure Sentinel pour obtenir des instructions et des explications plus détaillées.

        Environ 20 minutes peuvent être nécessaires avant que vos journaux commencent à apparaître dans Log Analytics.

## <a name="find-your-data"></a>Recherche de données

Après l’établissement d’une connexion réussie, les données s’affichent dans **Journaux**, sous la section **Azure Sentinel** dans le tableau *CommonSecurityLog*.

Pour interroger les données de Thycotic Secret Server dans Log Analytics, copiez le code suivant dans la fenêtre de requête, en appliquant d’autres filtres au choix :

```kusto
CommonSecurityLog 
| where DeviceVendor == "Thycotic Software"
```

Consultez l’onglet **Étapes suivantes** de la page du connecteur pour obtenir des classeurs et des exemples de requêtes utiles.

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a montré comment connecter Thycotic Secret Server à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](detect-threats-built-in.md).
- [Utilisez des classeurs](monitor-your-data.md) pour superviser vos données.
