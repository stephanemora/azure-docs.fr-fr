---
title: Données à collecter quand vous ouvrez un cas pour Microsoft Azure Automation | Microsoft Docs
description: Cet article décrit certaines informations de base que vous devez rassembler avant d’ouvrir un cas pour Azure Automation auprès du support Microsoft Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: v-miegge
ms.openlocfilehash: 4839ce7a0188c782656fd3a4c42cbdd116b165e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849375"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Données à collecter quand vous ouvrez un cas pour Microsoft Azure Automation

Cet article décrit certaines informations de base que vous devez rassembler avant d’ouvrir un cas pour Azure Automation auprès du support Microsoft Azure. Ces informations ne sont pas nécessaires pour ouvrir le cas. Toutefois, elles peuvent aider Microsoft à résoudre votre problème plus rapidement. En outre, l’ingénieur de support peut vous demander ces données une fois le cas ouvert.

## <a name="collect-more-information"></a>Collecter des informations supplémentaires

Nous vous recommandons de collecter les informations suivantes avant d’ouvrir un cas auprès du support Microsoft Azure Automation.

### <a name="basic-data-collection"></a>Collecte des données de base

Collectez les données décrites dans l’article suivant de la base de connaissances :

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) Comment capturer des diagnostics à base de scripts Azure Automation

## <a name="collect-data-depending-on-issue"></a>Collecter les données en fonction du problème
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>Pour les problèmes qui affectent Update Management sur Linux

1. Collectez les données décrites dans l’article [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) de la base de connaissances et exécutez l’outil de collecte de journaux suivant :

   [Collecteur de journaux de l’agent OMS pour Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Compressez le contenu du dossier suivant, puis envoyez le fichier compressé au support Azure :

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. Vérifiez que l’ID d’espace de travail auquel l’agent OMS pour Linux est associé correspond à l’espace de travail supervisé pour les mises à jour.

### <a name="for-issues-that-affect-update-management-on-windows"></a>Pour les problèmes qui affectent Update Management sur Windows

Collectez les données décrites dans l’article [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) et effectuez les étapes suivantes :

1. Exportez les journaux d’événements suivants au format EVTX :

   * Système
   * Application
   * Sécurité
   * Operations Manager
   * Microsoft-SMA/journal des opérations

2. Vérifiez que l’ID d’espace de travail auquel l’agent est associé correspond à l’espace de travail supervisé pour les mises à jour Windows.

### <a name="for-issues-that-affect-a-job"></a>Pour les problèmes qui affectent un travail

Collectez les données décrites dans l’article [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) ainsi que les informations suivantes :

1. Collectez le numéro **JobID** (pour le travail affecté par le problème) :

   1. Dans le portail Azure, accédez au panneau **Comptes Automation**.
   2. Sous **Compte Automation**, sélectionnez le compte affecté par le problème.
   3. Sélectionnez **Travaux**.
   4. Sélectionnez le travail affecté par le problème.
   5. Sous **Résumé du travail**, recherchez un **ID de travail** (GUID).

   ![ID de travail dans le volet Résumé du travail](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. Collectez le nom du compte :

   1. Dans le portail Azure, accédez au panneau **Comptes Automation**.
   2. Sous **Compte Automation**, récupérez le nom du compte affecté par le problème.

3. Collectez un exemple du script que vous exécutez.

4. Collectez les fichiers journaux :

   1. Dans le portail Azure, accédez au panneau **Comptes Automation**.
   2. Sous **Compte Automation**, sélectionnez le compte affecté par le problème.
   3. Sélectionnez **Travaux**.
   4. Sélectionnez le travail affecté par le problème.
   5. Sélectionnez **Tous les journaux**.
   6. Collectez les données fournies dans le panneau qui s’affichent.

   ![Données listées sous Tous les journaux](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>Pour les problèmes qui affectent des modules

Outre les éléments décrits dans la section « Collecte des données de base », rassemblez les informations suivantes :

* Les étapes que vous avez suivies, de sorte que le problème puisse être reproduit
* Les captures d’écran des messages d’erreur, le cas échéant
* Les captures d’écran des modules actuels et leurs numéros de version

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, contactez les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/).

Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.
