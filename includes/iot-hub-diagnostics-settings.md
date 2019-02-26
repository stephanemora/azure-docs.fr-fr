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
ms.openlocfilehash: 150b800bfa6bfa20f10dbba7e8d55981ecb9df34
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56422819"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Activer la journalisation avec les paramètres de diagnostic

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre hub IoT.

2. Sélectionnez **Paramètres de diagnostic**.

3. Sélectionnez **Activer les diagnostics**.

   ![Activer les diagnostics](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Nommez les paramètres de diagnostic.

5. Indiquez où vous voulez envoyer les journaux. Vous pouvez sélectionner n’importe quelle combinaison des trois options suivantes :

   * Archiver dans un compte de stockage
   * Diffuser vers un hub d’événements
   * Envoyer à Log Analytics

6. Choisissez les opérations à surveiller et activez les journaux de ces opérations. Les opérations sur lesquelles les paramètres de diagnostic peuvent établir des rapports sont :

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
   * Traçage distribué (préversion)

6. Enregistrez les nouveaux paramètres. 

Si vous voulez activer les paramètres de diagnostic avec PowerShell, utilisez le code suivant :

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzureRmDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Les nouveaux paramètres prennent effet au bout de 10 minutes environ. Après cela, les journaux apparaissent dans la cible d’archivage configurée, dans le panneau **Paramètres de diagnostic**. Pour en savoir plus sur la configuration des diagnostics, consultez [Collecter et utiliser des données de journaux à partir de vos ressources Azure](../articles/azure-monitor/platform/diagnostic-logs-overview.md).