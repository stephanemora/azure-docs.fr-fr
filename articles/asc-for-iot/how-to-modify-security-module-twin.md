---
title: Comment obtenir des instructions pour modifier un ASC pour jumeau de module IoT dans ASC pour IoT Preview | Microsoft Docs
description: Découvrez comment modifier un ASC pour la représentation de module de sécurité IoT de ASC pour IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 1bc5dc86-0f33-4625-b3d3-f9b6c1a54e14
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 4bb25db7feddea66e75d9dd069aaec7785faf738
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541946"
---
# <a name="modify-an-asc-for-iot-module-twin"></a>Modifier un ASC pour jumeau de module IoT

> [!IMPORTANT]
> ASC pour IoT est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article explique comment modifier la configuration d’un existant **jumeau de module AzureIoTSecurity** pour un appareil existant. 

Consultez [créer un ASC pour le module IoT](quickstart-create-security-twin.md) pour savoir comment rendre un nouveau module de sécurité pour un nouvel appareil.  

## <a name="modification-considerations"></a>Considérations relatives à la modification

> [!IMPORTANT]
> Chaque configuration dans la configuration de la représentation remplace la configuration par défaut. Si une configuration spécifique n’est plus présente dans la configuration de la représentation, la configuration par défaut est utilisée. 

## <a name="configuration-schema-and-validation"></a>Validation et le schéma de configuration 

Veillez à valider votre configuration de l’agent par rapport à ce [schéma](https://github.com/Azure/asc-for-iot/schema/security_module_twin). Un agent ne lancera pas si l’objet de configuration ne correspond pas au schéma.

 
Si, alors que l’agent est en cours d’exécution, l’objet de configuration est modifié pour une configuration non valide (la configuration ne correspond pas au schéma), l’agent ignore la configuration non valide et continuera à l’aide de la configuration actuelle. 

## <a name="edit-a-property"></a>Modifier une propriété  

Définissez toutes les propriétés personnalisées à l’intérieur de l’objet de configuration de l’agent au sein de la représentation de module AzureIoTSecurity. 

Pour définir une propriété, ajoutez la clé de propriété à l’objet de configuration avec la valeur souhaitée. Pour utiliser une valeur de propriété par défaut, supprimez la propriété de l’objet de configuration :

```json
"desired": { //AzureIoTSecurity Module Identity Twin – desired properties section  
  "azureiot*com^securityAgentConfiguration^1*0*0": { //ASC for IoT Agent 
      // configuration section  
    "hubResourceId": "/subscriptions/82392767-31d3-4bd2-883d-9b60596f5f42/resourceGroups/myResourceGroup/providers/Microsoft.Devices/IotHubs/myIotHub",     
    "lowPriorityMessageFrequency": "PT1H",     
    "highPriorityMessageFrequency": "PT7M",    
    "eventPriorityFirewallConfiguration": "High",     
    "eventPriorityConnectionCreate": "Off" 
  } 
}, 
```

## <a name="properties"></a>properties 
Le tableau suivant contient toutes les propriétés configurables qui contrôlent ASC pour les agents de IoT. 
          

| Nom| Statut | Valeurs valides| Valeurs par défaut| Description |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Requis : false |Valeurs valides :  Durée en Format ISO 8601 |Valeur par défaut : PT7M |Durée maximale avant que les messages de priorité élevée sont envoyés.|
|lowPriorityMessageFrequency |Requis : false|Valeurs valides :  Durée en Format ISO 8601 |Valeur par défaut : PT5H |Durée maximale avant que les messages de faible priorité sont envoyés.| 
|snapshotFrequency |Exiger : false|Valeurs : durée valide dans le Format ISO 8601 |Valeur par défaut PT13H |Intervalle de temps pour la création d’instantanés d’état de périphérique.| 
|maxLocalCacheSizeInBytes |Requis : false |Valeurs valides :  |Valeur par défaut : 2560000, supérieur à 8192 | Stockage maximal (en octets) autorisé pour le cache des messages d’un agent. Quantité maximale d’espace autorisé pour stocker les messages sur l’appareil, avant l’envoi de messages.| 
|maxMessageSizeInBytes |Requis : false |Valeurs valides :  Un nombre positif, supérieur à 8192, 262144 inférieur à |Valeur par défaut : 204800 |Maximale, la taille de message de cloud d’un agent est autorisée. Ce paramètre contrôle la quantité de données maximales envoyées dans chaque message. |
|eventPriority${EventName} |Requis : false |Valeurs valides :  Haute, basse, désactivé |Valeurs par défaut : |Priorité de chaque agent a généré l’événement. | 
|

### <a name="events"></a>Événements

La liste suivante d’événements sont tous les événements de l’ASC pour IoT agent peuvent collecter à partir de vos appareils. La représentation de module AzureIotSecurity permet de configurer laquelle de ces événements sont collectés et décider de leur priorité dans votre solution. 
 
|Nom de l'événement| PropertyName | Valeur par défaut| Événement de capture instantanée| État détaillé  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Événement de diagnostic|eventPriorityDiagnostic| Off| False| Événements de diagnostic liés à l’agent. Utilisez cet événement pour la journalisation détaillée.| 
Erreur de configuration |eventPriorityConfigurationError |Faible |False |Échec de l’agent d’analyser la configuration. Vérifier la configuration par rapport au schéma.| 
|Statistiques des événements supprimés |eventPriorityDroppedEventsStatistics |Faible |True|Statistiques des événements liés à l’agent. |
|Statistiques de message|eventPriorityMessageStatistics |Faible |True |Statistiques des messages liés à l’agent. |
|Matériel connecté|eventPriorityConnectedHardware |Faible |True |Instantané de tout le matériel connecté à l’appareil.|
|Ports d’écoute|eventPriorityListeningPorts |Élevé |True |Instantané de tous les ports d’écoute sur l’appareil.|
| La création du processus |eventPriorityProcessCreate |Faible |False |Audits de traitement la création sur l’appareil.|
| Arrêt de processus|eventPriorityProcessTerminate |Faible |False |Arrêt sur l’appareil des audits de processus.| 
 Informations système |eventPrioritySystemInformation |Faible |True |Instantané des informations système telles que le système d’exploitation ou du processeur.|
|Utilisateurs locaux| eventPriorityLocalUsers |Élevé |True|Une capture instantanée des utilisateurs inscrits locales au sein du système. |
|Connexion|  eventPriorityLogin |Élevé|False|Audite les événements de connexion à l’appareil (accès locales et distants).|
|Créer de connexion |eventPriorityConnectionCreate|Faible|False|Audit des connexions TCP créées vers et à partir de l’appareil. |
|Configuration du pare-feu| eventPriorityFirewallConfiguration|Faible|True|Instantané de la configuration du pare-feu périphérique (règles de pare-feu). |
|Ligne de base du système d’exploitation| eventPriorityOSBaseline| Faible|True|Vérification de l’instantané de ligne de base du système d’exploitation de périphérique.| 
|


## <a name="next-steps"></a>Étapes suivantes

- Lire l’ASC pour IoT [vue d’ensemble](overview.md)
- En savoir plus sur ASC pour IoT [Architecture](architecture.md)
- Comprendre et Explorer [ASC pour les alertes de l’IoT](concept-security-alerts.md)
- Découvrez comment accéder à votre [les données de sécurité](how-to-security-data-access.md)
