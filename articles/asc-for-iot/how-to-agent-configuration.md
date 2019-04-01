---
title: Configurer Azure Security Center pour l’agent IoT préversion | Microsoft Docs
description: Découvrez comment configurer des agents pour une utilisation avec Azure Security Center pour IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 27b548459bd1fee3c6596cce624b00d052e608fe
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757383"
---
# <a name="tutorial-configure-security-agents"></a>Didacticiel : Configurer des agents de sécurité

> [!IMPORTANT]
> Azure Security Center pour IoT est actuellement en version préliminaire publique.
> Cette version préliminaire est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article explique l’Azure Security Center (ASC) pour l’agent de sécurité IoT, comment modifier les configurer ASC pour les agents de sécurité IoT.

> [!div class="checklist"]
> * Configurer des agents de sécurité
> * Modifier le comportement de l’agent en modifiant les propriétés des représentations
> * Découvrir la configuration par défaut

## <a name="agents"></a>Agents

ASC pour les agents de sécurité IoT collecter des données à partir d’appareils IoT et effectuer des actions de sécurité pour atténuer les vulnérabilités détectées. Configuration de l’agent de sécurité peut être contrôlable à l’aide d’un ensemble de propriétés de jumeau de module que vous pouvez personnaliser. En règle générale, les mises à jour secondaires à ces propriétés sont peu fréquentes.  

ASC pour objet de configuration des représentations de IoT sécurité agent est un objet de format .json. L’objet de configuration est un ensemble de propriétés contrôlables que vous pouvez définir pour contrôler le comportement de l’agent. 

Ces configurations vous aider à personnaliser l’agent pour chaque scénario requis. Par exemple, automatiquement à l’exclusion de certains événements, ou de conserver la consommation d’énergie à un niveau minimal sont possibles en configurant ces propriétés.  

Utiliser l’ASC pour la configuration de l’agent de sécurité IoT [schéma](https://aka.ms/iot-security-github-module-schema) pour apporter des modifications.  

## <a name="configuration-objects"></a>Objets de configuration 

Chaque ASC pour l’agent de sécurité IoT liés propriété se trouve dans l’objet de configuration de l’agent, dans la section propriétés souhaitées, de la **azureiotsecurity** module. 

Pour modifier la configuration, créer et modifier cet objet à l’intérieur de la **azureiotsecurity** identité jumeau de module. 

Si l’objet de configuration de l’agent n’existe pas dans le **azureiotsecurity** jumeau de module, toutes les valeurs de propriété de l’agent de sécurité sont définies par défaut. 

```json
"desired": {
  "azureiot*com^securityAgentConfiguration^1*0*0": {
  } 
}
```

Veillez à valider vos modifications de configuration de l’agent par rapport à ce [schéma](https://aka.ms/iot-security-github-module-schema).
L’agent ne lancera pas si l’objet de configuration ne correspond pas au schéma.

## <a name="configuration-schema-and-validation"></a>Validation et le schéma de configuration 

Veillez à valider votre configuration de l’agent par rapport à ce [schéma](https://aka.ms/iot-security-github-module-schema). Un agent ne lancera pas si l’objet de configuration ne correspond pas au schéma.

 
Si, alors que l’agent est en cours d’exécution, l’objet de configuration est modifié pour une configuration non valide (la configuration ne correspond pas au schéma), l’agent ignore la configuration non valide et continuera à l’aide de la configuration actuelle. 

## <a name="editing-a-property"></a>Modification d’une propriété 

Toutes les propriétés personnalisées doivent être définies à l’intérieur de l’objet de configuration de l’agent dans le **azureiotsecurity** jumeau de module.
Pour utiliser une valeur de propriété par défaut, supprimez la propriété de l’objet de configuration.

### <a name="setting-a-property"></a>Définition d’une propriété

1. Dans votre IoT Hub, recherchez et sélectionnez le périphérique que vous souhaitez modifier.

1. Cliquez sur votre appareil, puis sur **azureiotsecurity** module.

1. Cliquez sur **jumeau de Module identité**.

1. Modifier les propriétés souhaitées du module de sécurité.
   
   Par exemple, pour configurer les événements de connexion avec une priorité élevée et collecter les événements de haute priorité 7 minutes, utilisez la configuration suivante.
   
   ```json
    "desired": {
      "azureiot*com^securityAgentConfiguration^1*0*0": {
        "highPriorityMessageFrequency": "PT7M",    
        "eventPriorityConnectionCreate": "High" 
      } 
    }, 
    ```

1. Cliquez sur **Enregistrer**.

### <a name="using-a-default-value"></a>À l’aide d’une valeur par défaut

Pour utiliser une valeur de propriété par défaut, supprimez la propriété de l’objet de configuration.

## <a name="default-properties"></a>Propriétés par défaut 

Le tableau suivant contient les propriétés contrôlables de ASC pour les agents de sécurité IoT.

Valeurs par défaut sont disponibles dans le schéma approprié dans [Github](https://aka.ms/iot-security-module-default).

| Nom| Statut | Valeurs valides| Valeurs par défaut| Description |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Requis : false |Valeurs valides :  Durée en Format ISO 8601 |Valeur par défaut : PT7M |Durée maximale avant que les messages de priorité élevée sont envoyés.|
|lowPriorityMessageFrequency |Requis : false|Valeurs valides :  Durée en Format ISO 8601 |Valeur par défaut : PT5H |Durée maximale avant que les messages de faible priorité sont envoyés.| 
|snapshotFrequency |Exiger : false|Valeurs : durée valide dans le Format ISO 8601 |Valeur par défaut PT13H |Intervalle de temps pour la création d’instantanés d’état de périphérique.| 
|maxLocalCacheSizeInBytes |Requis : false |Valeurs valides :  |Valeur par défaut : 2560000, supérieur à 8192 | Stockage maximal (en octets) autorisé pour le cache des messages d’un agent. Quantité maximale d’espace autorisé pour stocker les messages sur l’appareil, avant l’envoi de messages.| 
|maxMessageSizeInBytes |Requis : false |Valeurs valides :  Un nombre positif, supérieur à 8192, 262144 inférieur à |Valeur par défaut : 204800 |Maximale, la taille de message de cloud d’un agent est autorisée. Ce paramètre contrôle la quantité de données maximales envoyées dans chaque message. |
|eventPriority${EventName} |Requis : false |Valeurs valides :  Haute, basse, désactivé |Valeurs par défaut : |Priorité de chaque agent a généré l’événement | 

### <a name="supported-security-events"></a>Événements de sécurité prises en charge

|Nom de l'événement| PropertyName | Valeur par défaut| Événement de capture instantanée| Détails de l’état  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Événement de diagnostic|eventPriorityDiagnostic| Off| False| Événements de diagnostic liés à l’agent. Utilisez cet événement pour la journalisation détaillée.| 
|Erreur de configuration |eventPriorityConfigurationError |Faible |False |Échec de l’agent d’analyser la configuration. Vérifier la configuration par rapport au schéma.| 
|Statistiques des événements supprimés |eventPriorityDroppedEventsStatistics |Faible |True|Statistiques des événements liés à l’agent. |
|Statistiques de message|eventPriorityMessageStatistics |Faible |True |Statistiques des messages liés à l’agent. |
|Matériel connecté|eventPriorityConnectedHardware |Faible |True |Instantané de tout le matériel connecté à l’appareil.|
|Ports d’écoute|eventPriorityListeningPorts |Élevé |True |Instantané de tous les ports d’écoute sur l’appareil.|
|La création du processus |eventPriorityProcessCreate |Faible |False |Audits de traitement la création sur l’appareil.|
|Arrêt de processus|eventPriorityProcessTerminate |Faible |False |Arrêt sur l’appareil des audits de processus.| 
|Informations système |eventPrioritySystemInformation |Faible |True |Un instantané des informations système (par exemple : Système d’exploitation ou de processeur).| 
|Utilisateurs locaux| eventPriorityLocalUsers |Élevé |True|Une capture instantanée des utilisateurs inscrits locales au sein du système. |
|Connexion|  eventPriorityLogin |Élevé|False|Auditer les événements de connexion à l’appareil (accès locales et distants).|
|Créer de connexion |eventPriorityConnectionCreate|Faible|False|Audit des connexions TCP créées vers et à partir de l’appareil. |
|Configuration du pare-feu| eventPriorityFirewallConfiguration|Faible|True|Instantané de la configuration du pare-feu périphérique (règles de pare-feu). |
|Ligne de base du système d’exploitation| eventPriorityOSBaseline| Faible|True|Vérification de l’instantané de ligne de base du système d’exploitation de périphérique.|
 

## <a name="next-steps"></a>Étapes suivantes

- [Comprendre ASC pour obtenir des recommandations IoT](concept-recommendations.md)
- [Explorez ASC pour les alertes de l’IoT](concept-security-alerts.md)
- [Accéder aux données de sécurité brute](how-to-security-data-access.md)
