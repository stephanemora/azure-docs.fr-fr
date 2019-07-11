---
title: Configurer votre agent Azure Security Center pour IoT (préversion) | Microsoft Docs
description: Découvrez comment configurer les agents pour une utilisation avec Azure Security Center pour IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 39539bb14877208e5f6af957e735a136b077f16a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618279"
---
# <a name="tutorial-configure-security-agents"></a>Didacticiel : Configurer des agents de sécurité

> [!IMPORTANT]
> Azure Security Center pour IoT est disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article explique les agents de sécurité Azure Security Center (ASC) pour IoT, comment les modifier et comment configurer les agents de sécurité ASC pour IoT.

> [!div class="checklist"]
> * Configurer des agents de sécurité
> * Modifier le comportement de l’agent en modifiant les propriétés de jumeaux
> * Découvrir la configuration par défaut

## <a name="agents"></a>Agents

Les agents de sécurité ASC pour IoT collectent des données à partir d’appareils IoT et effectuent des actions de sécurité pour atténuer les vulnérabilités détectées. La configuration de l’agent de sécurité peut être contrôlée à l’aide d’un ensemble de propriétés de jumeau de module que vous pouvez personnaliser. En règle générale, les mises à jour secondaires pour ces propriétés sont peu fréquentes.  

L’objet de configuration des jumeaux d’agent de sécurité ASC pour IoT est un objet au format .json. L’objet de configuration est un ensemble de propriétés contrôlables que vous pouvez définir pour contrôler le comportement de l’agent. 

Ces configurations vous aident à personnaliser l’agent pour chaque scénario requis. Par exemple, l’exclusion automatique de certains événements, ou le maintien de la consommation d’énergie à un niveau minimal sont possibles en configurant ces propriétés.  

Utilisez le [schéma](https://aka.ms/iot-security-github-module-schema) de configuration de l’agent de sécurité ASC pour IoT pour apporter des modifications.  

## <a name="configuration-objects"></a>Objets de configuration 

Chaque propriété liée à l’agent de sécurité ASC pour IoT se trouve dans l’objet de configuration de l’agent, dans la section Propriétés souhaitées du module **azureiotsecurity**. 

Pour modifier la configuration, créez et modifiez cet objet à l’intérieur de l’identité de jumeau de module **azureiotsecurity**. 

Si l’objet de configuration de l’agent n’existe pas dans le jumeau de module **azureiotsecurity**, toutes les valeurs de propriété de l’agent de sécurité sont définies aux valeurs par défaut. 

```json
"desired": {
  "azureiot*com^securityAgentConfiguration^1*0*0": {
  } 
}
```

Veillez à valider vos modifications à la configuration de l’agent par rapport à ce [schéma](https://aka.ms/iot-security-github-module-schema).
L’agent ne se lancera pas si l’objet de configuration ne correspond pas au schéma.

## <a name="configuration-schema-and-validation"></a>Schéma de configuration et validation 

Veillez à valider votre configuration d’agent par rapport à ce [schéma](https://aka.ms/iot-security-github-module-schema). Un agent ne se lancera pas si l’objet de configuration ne correspond pas au schéma.

 
Si, lorsque l’agent est en cours d’exécution, l’objet de configuration est modifié en une configuration non valide (la configuration ne correspond pas au schéma), l’agent ignore la configuration non valide et continue avec la configuration actuelle. 

## <a name="editing-a-property"></a>Modification d’une propriété 

Toutes les propriétés personnalisées doivent être définies à l’intérieur de l’objet de configuration de l’agent dans le jumeau de module **azureiotsecurity**.
Pour utiliser une valeur de propriété par défaut, supprimez la propriété de l’objet de configuration.

### <a name="setting-a-property"></a>Définition d’une propriété

1. Dans votre IoT Hub, recherchez et sélectionnez l’appareil que vous souhaitez modifier.

1. Cliquez sur votre appareil, puis sur le module **azureiotsecurity**.

1. Cliquez sur **Jumeau d’identité de module**.

1. Modifiez les propriétés souhaitées du module de sécurité.
   
   Par exemple, pour configurer les événements de connexion sur une priorité élevée et collecter les événements de haute priorité toutes les 7 minutes, utilisez la configuration suivante.
   
   ```json
    "desired": {
      "azureiot*com^securityAgentConfiguration^1*0*0": {
        "highPriorityMessageFrequency": "PT7M",    
        "eventPriorityConnectionCreate": "High" 
      } 
    }, 
    ```

1. Cliquez sur **Enregistrer**.

### <a name="using-a-default-value"></a>Avec une valeur par défaut

Pour utiliser une valeur de propriété par défaut, supprimez la propriété de l’objet de configuration.

## <a name="default-properties"></a>Propriétés par défaut 

Le tableau suivant contient les propriétés contrôlables des agents de sécurité ASC pour IoT.

Les valeurs par défaut sont disponibles dans le schéma approprié dans [Github](https://aka.ms/iot-security-module-default).

| Nom| Statut | Valeurs valides| Valeurs par défaut| Description |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Requis : false |Valeurs valides :  Durée au format ISO 8601 |Valeur par défaut : PT7M |Durée maximale avant que les messages de priorité élevée soient envoyés.|
|lowPriorityMessageFrequency |Requis : false|Valeurs valides :  Durée au format ISO 8601 |Valeur par défaut : PT5H |Durée maximale avant que les messages de basse priorité soient envoyés.| 
|snapshotFrequency |Requis : false|Valeurs valides : durée valide au format ISO 8601 |La valeur par défaut est PT13H |Intervalle de temps pour la création d’instantanés d’état d’appareil.| 
|maxLocalCacheSizeInBytes |Requis : false |Valeurs valides : |Valeur par défaut : 2560000, supérieur à 8192 | Stockage maximal (en octets) autorisé pour le cache de messages d’un agent. Quantité maximale d’espace pour le stockage des messages sur l’appareil avant leur envoi.| 
|maxMessageSizeInBytes |Requis : false |Valeurs valides :  Un nombre positif, supérieur à 8192 et inférieur à 262144 |Valeur par défaut : 204800 |Taille maximale autorisée d’un message d’agent au cloud. Ce paramètre contrôle la quantité maximale de données envoyées dans chaque message. |
|eventPriority${EventName} |Requis : false |Valeurs valides :  High, Low, Off |Valeurs par défaut : |Priorité de chaque événement généré par l’agent | 

### <a name="supported-security-events"></a>Événements de sécurité pris en charge

|Nom de l'événement| PropertyName | Valeur par défaut| Événement de capture instantanée| État des détails  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Événement de diagnostic|eventPriorityDiagnostic| Off| False| Événements de diagnostic liés à l’agent. Utilisez cet événement pour la journalisation détaillée.| 
|Erreur de configuration |eventPriorityConfigurationError |Faible |False |L’agent a échoué à analyser la configuration. Vérifiez la configuration par rapport au schéma.| 
|Statistiques des événements annulés |eventPriorityDroppedEventsStatistics |Faible |True|Statistiques des événements liés à l’agent. |
|Statistiques de message|eventPriorityMessageStatistics |Faible |True |Statistiques des messages liés à l’agent. |
|Matériel connecté|eventPriorityConnectedHardware |Faible |True |Instantané de tout le matériel connecté à l’appareil.|
|Ports d’écoute|eventPriorityListeningPorts |Élevé |True |Instantané de tous les ports d’écoute sur l’appareil.|
|Création de processus |eventPriorityProcessCreate |Faible |False |Vérifie la création de processus sur l’appareil.|
|Arrêt de processus|eventPriorityProcessTerminate |Faible |False |Vérifie l’arrêt de processus sur l’appareil.| 
|Informations système |eventPrioritySystemInformation |Faible |True |Un instantané des informations système (par exemple : Système d’exploitation ou processeur).| 
|Utilisateurs locaux| eventPriorityLocalUsers |Élevé |True|Une capture instantanée des utilisateurs locaux inscrits sur le système. |
|Connexion|  eventPriorityLogin |Élevé|False|Vérification des événements de connexion à l’appareil (accès locaux et distants).|
|Création de connexion |eventPriorityConnectionCreate|Faible|False|Vérification des connexions TCP créées de et vers l’appareil. |
|Configuration du pare-feu| eventPriorityFirewallConfiguration|Faible|True|Instantané de la configuration du pare-feu de l’appareil (règles de pare-feu). |
|Base de référence système d’exploitation| eventPriorityOSBaseline| Faible|True|Instantané de vérification de ligne de base du système d’exploitation de l’appareil.|
 

## <a name="next-steps"></a>Étapes suivantes

- [Comprendre les recommandations ASC pour IoT](concept-recommendations.md)
- [Explorer les alertes ASC pour IoT](concept-security-alerts.md)
- [Accéder aux données de sécurité brutes](how-to-security-data-access.md)
