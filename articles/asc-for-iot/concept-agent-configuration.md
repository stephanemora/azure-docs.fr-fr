---
title: Configurer un ASC pour l’agent IoT préversion | Microsoft Docs
description: Découvrez comment configurer des agents pour une utilisation avec ASC pour IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.service: ascforiot
ms.devlang: na
ms.topic: concept
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 3a3806c73ef254abab91bd28cd8761917371235f
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541916"
---
# <a name="configure-security-agents"></a>Configurer les agents de sécurité

> [!IMPORTANT]
> ASC pour IoT est actuellement en version préliminaire publique.
> Cette version préliminaire est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article explique comment configurer un agent pour une utilisation avec ASC pour IoT.

## <a name="agents"></a>Agents

ASC pour les agents de sécurité IoT collecter des données à partir d’appareils IoT et effectuer des actions de sécurité pour atténuer les vulnérabilités détectées. Configuration de l’agent de sécurité peut être contrôlable à l’aide d’un ensemble de propriétés de jumeau de module que vous pouvez personnaliser. En règle générale, les mises à jour secondaires à ces propriétés sont peu fréquentes.  

ASC pour objet de configuration des représentations de IoT sécurité agent est un objet de format .json. L’objet de configuration est un ensemble de propriétés contrôlables que vous pouvez définir pour contrôler le comportement de l’agent. 

Ces configurations vous aider à personnaliser l’agent pour chaque scénario requis. Par exemple, automatiquement à l’exclusion de certains événements, ou de conserver la consommation d’énergie à un niveau minimal sont possibles en configurant ces propriétés.  

Utiliser l’ASC pour la configuration de l’agent de sécurité IoT [schéma](https://github.com/azure/asc-for-iot-schemas/security/module/twin) pour apporter des modifications.  

## <a name="configuration-objects"></a>Objets de configuration 

Chaque ASC pour IoT liés à l’agent de sécurité propriété se trouve à l’intérieur de l’objet de configuration de l’agent, dans la section propriétés souhaitées, du module azureiotsecurity. 

Pour modifier la configuration, créer et modifier cet objet à l’intérieur de l’identité de jumeau de module azureiotsecurity. Si l’objet de configuration de l’agent n’existe pas dans la représentation de module azureiotsecurity, toutes les valeurs de propriété de l’agent de sécurité sont définies par défaut. 

```json
"desired": { //azureiotsecurity Module Identity Twin – desired properties section  
  "azureiot*com^securityAgentConfiguration^1*0*0": { //Agent configuration object 
  … 
} 
}
```

Veillez à valider vos modifications de configuration de l’agent par rapport à ce [schéma](https://aka.ms/iot-security-github-module-schema).
L’agent ne lancera pas si l’objet de configuration ne correspond pas au schéma.


## <a name="editing-a-property"></a>Modification d’une propriété 

Toutes les propriétés personnalisées doivent être définies à l’intérieur de l’objet de configuration de l’agent au sein de la représentation de module azureiotsecurity. 

Définition d’une propriété remplace la valeur par défaut. Pour définir une propriété, ajoutez la clé de propriété à l’objet de configuration avec la valeur souhaitée. 

Pour utiliser une valeur de propriété par défaut, supprimez la propriété de l’objet de configuration. 

```json
"desired": { //azureiotsecurity Module Identity Twin – desired properties section  
  "azureiot*com^securityAgentConfiguration^1*0*0": { //ASC for IoT Agent 
      // configuration section  
    "lowPriorityMessageFrequency": "PT1H",     
    "highPriorityMessageFrequency": "PT7M",    
    "eventPriorityFirewallConfiguration": "High",     
    "eventPriorityConnectionCreate": "High" 
  } 
}, 
```

## <a name="default-properties"></a>Propriétés par défaut 
Jeu de propriétés contrôlables qui contrôlent l’ASC pour les agents de sécurité IoT.

Valeurs par défaut sont disponibles dans le schéma approprié dans [Github](https://aka.ms/iot-security-module-default).

| Nom| Statut | Valeurs valides| Valeurs par défaut| Description |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Requis : false |Valeurs valides :  Durée en Format ISO 8601 |Valeur par défaut : PT7M |Durée maximale avant que les messages de priorité élevée sont envoyés.|
|lowPriorityMessageFrequency |Requis : false|Valeurs valides :  Durée en Format ISO 8601 |Valeur par défaut : PT5H |Durée maximale avant que les messages de faible priorité sont envoyés.| 
|snapshotFrequency |Exiger : false|Valeurs : durée valide dans le Format ISO 8601 |Valeur par défaut PT13H |Intervalle de temps pour la création d’instantanés d’état de périphérique.| 
|maxLocalCacheSizeInBytes |Requis : false |Valeurs valides :  |Valeur par défaut : 2560000, supérieur à 8192 | Stockage maximal (en octets) autorisé pour le cache des messages d’un agent. Quantité maximale d’espace autorisé pour stocker les messages sur l’appareil, avant l’envoi de messages.| 
|maxMessageSizeInBytes |Requis : false |Valeurs valides :  Un nombre positif, supérieur à 8192, 262144 inférieur à |Valeur par défaut : 204800 |Maximale, la taille de message de cloud d’un agent est autorisée. Ce paramètre contrôle la quantité de données maximales envoyées dans chaque message. |
|eventPriority${EventName} |Requis : false |Valeurs valides :  Haute, basse, désactivé |Valeurs par défaut : |Priorité de chaque agent a généré l’événement | 

### <a name="events"></a>Événements

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
 

## <a name="see-also"></a>Voir aussi

- [Comprendre ASC pour obtenir des recommandations IoT](concept-recommendations.md)
- [Explorez ASC pour les alertes de l’IoT](concept-security-alerts.md)
- [Accéder aux données de sécurité brute](how-to-security-data-access.md)