---
title: Collecte d’événements ETW (Suivi d’événements pour Windows) pour l’analyse des journaux Azure Monitor
description: Découvrez comment collecter des suivis d’événements pour Windows (ETW) pour analyse dans des journaux d’activité Azure Monitor.
services: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: jamesfit
author: jimmyfit
ms.date: 01/29/2021
ms.openlocfilehash: 6239cf48794c74c5dd810fda42476df399300578
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100599128"
---
# <a name="collecting-event-tracing-for-windows-etw-events-for-analysis-azure-monitor-logs"></a>Collecte d’événements ETW (Suivi d’événements pour Windows) pour l’analyse des journaux Azure Monitor

La fonctionnalité *Suivi d’événements pour Windows (ETW)* fournit un mécanisme pour l’instrumentation des applications en mode utilisateur et des pilotes en mode noyau. L’agent Log Analytics est utilisé pour [collecter les événements Windows](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events) écrits sur les [canaux ETW](https://docs.microsoft.com/windows/win32/wes/eventmanifestschema-channeltype-complextype) administratif et opérationnel. Toutefois, il est parfois nécessaire de capturer et d’analyser d’autres événements, tels que ceux écrits dans le canal d’analyse.  

## <a name="event-flow"></a>Flux d’événements

Pour collecter avec succès les [événements ETW basés sur un manifeste](https://docs.microsoft.com/windows/win32/etw/about-event-tracing#types-of-providers) pour analyse dans des journaux d’activité Azure Monitor, vous devez utiliser [l’extension de diagnostic Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview) pour Windows (WAD). Dans ce scénario, l’extension de diagnostic joue le rôle de consommateur ETW, en écrivant des événements dans Stockage Azure (tables) en tant que magasin intermédiaire. Ici, il est stocké dans une table nommée **WADETWEventTable**. Log Analytics collecte ensuite les données de la table à partir de Stockage Azure, en les présentant sous la forme d’une table nommée **ETWEvent**.

![Flux d’événements](./media/data-sources-event-tracing-windows/event-flow.png)

## <a name="configuring-etw-log-collection"></a>Configuration de la collecte de journaux ETW

### <a name="step-1-locate-the-correct-etw-provider"></a>Étape 1 : Localiser le bon fournisseur d’ETW

Utilisez l’une des commandes suivantes pour énumérer les fournisseurs d’ETW sur un système Windows source.

Ligne de commande :

```
logman query providers
```

PowerShell :
```
Get-NetEventProvider -ShowInstalled | Select-Object Name, Guid
```
Si vous le souhaitez, vous pouvez choisir de canaliser cette sortie PowerShell vers Out-GridView pour faciliter la navigation.

Enregistrez le nom et le GUID du fournisseur d’ETW qui s’aligne sur le journal d’analyse ou de débogage présenté dans Observateur d’événements ou sur le module pour lequel vous souhaitez collecter des données d’événement.

### <a name="step-2-diagnostics-extension"></a>Étape 2 : Extension Diagnostics

Assurez-vous que l’*extension de diagnostic Windows* est [installée](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-windows-install#install-with-azure-portal) sur tous les systèmes sources.

### <a name="step-3-configure-etw-log-collection"></a>Étape 3 : Configurer la collecte des journaux ETW

1. Accédez au panneau **Paramètres de diagnostic** de la machine virtuelle.

2. Sélectionnez l’onglet **Journaux**.

3. Faites défiler l’écran et activez l’option **Événements ETW (Suivi d’événements pour Windows)** . ![Capture d’écran des paramètres de diagnostic](./media/data-sources-event-tracing-windows/enable-event-tracing-windows-collection.png)

4. Définissez le GUID du fournisseur ou la classe de fournisseur en fonction du fournisseur pour lequel vous configurez la collecte.

5. Définissez le [**niveau de journalisation**](https://docs.microsoft.com/windows/win32/etw/configuring-and-starting-an-event-tracing-session) comme il convient.

6. Cliquez sur les points de suspension adjacents au fournisseur fourni, puis cliquez sur **Configurer**.

7. Vérifiez que la **table de destination par défaut** est définie sur **etweventtable**.

8. Définissez un [**filtre de mots clés**](https://docs.microsoft.com/windows/win32/wes/defining-keywords-used-to-classify-types-of-events) si nécessaire.

9. Enregistrez les paramètres du fournisseur et du journal.

Une fois que les événements correspondants sont générés, vous devriez commencer à voir les événements ETW apparaître dans la table **WADetweventtable** dans Stockage Azure. Vous pouvez utiliser Explorateur Stockage Azure pour confirmer cela.

### <a name="step-4-configure-log-analytics-storage-account-collection"></a>Étape 4 : Configurer la collecte de comptes de stockage Log Analytics

Suivez [ces instructions](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-logs#collect-logs-from-azure-storage) pour collecter les journaux à partir de Stockage Azure. Une fois configurées, les données d’événement ETW doivent apparaître dans Log Analytics sous la table **ETWEvent**.

## <a name="next-steps"></a>Étapes suivantes
- Utilisez des [champs personnalisés](https://docs.microsoft.com/azure/azure-monitor/platform/custom-fields) pour créer une structure dans vos événements ETW.
- Découvrez les [requêtes dans les journaux](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) pour analyser les données collectées à partir de sources de données et de solutions.
