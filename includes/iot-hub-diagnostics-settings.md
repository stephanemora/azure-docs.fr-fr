---
title: Fichier Include
description: Fichier Include
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 1c7f006c066a4f1505a642af04a1ef027fde0a44
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34666940"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Activer la journalisation avec les paramètres de diagnostic

1. Connectez-vous au [portail Azure][lnk-portal] et accédez à votre IoT Hub.
1. Sélectionnez **Paramètres de diagnostic**.
1. Sélectionnez **Activer les diagnostics**.

   ![Activer les diagnostics][1]

1. Nommez les paramètres de diagnostic.
1. Indiquez où vous voulez envoyer les journaux. Vous pouvez sélectionner n’importe quelle combinaison des trois options suivantes :
   * Archiver dans un compte de stockage
   * Diffuser vers un hub d’événements
   * Envoyer à Log Analytics
1. Choisissez les opérations à surveiller et activez les journaux de ces opérations. Les opérations sur lesquelles les paramètres de diagnostic peuvent établir des rapports sont :
   * connexions
   * Télémétrie d’appareil
   * Messages Cloud vers appareil
   * Opérations d’identité des appareils
   * Chargements de fichiers
   * Routage de messages
   * Opérations jumelles cloud à appareil
   * Opérations jumelles appareil à cloud
   * Opérations jumelles
   * Opérations de travail
   * Méthodes directes  
1. Enregistrez les nouveaux paramètres. 

Si vous voulez activer les paramètres de diagnostic avec PowerShell, utilisez le code suivant :

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzureRmDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Les nouveaux paramètres prennent effet au bout de 10 minutes environ. Après cela, les journaux apparaissent dans la cible d’archivage configurée, dans le panneau **Paramètres de diagnostic**. Pour en savoir plus sur la configuration des diagnostics, consultez l’article [Collecter et utiliser des données de journaux à partir de vos ressources Azure][lnk-diagnostics-settings].

<!-- Images -->
[1]: ./media/iot-hub-diagnostics-settings/turnondiagnostics.png

<!-- Links -->
[lnk-portal]: https://portal.azure.com
[lnk-diagnostics-settings]: ../articles/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
