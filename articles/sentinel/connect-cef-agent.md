---
title: Déployer l’agent pour connecter des données CEF à Azure Sentinel en préversion | Microsoft Docs
description: Découvrez comment déployer l’agent pour connecter des données CEF à Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: b0c9335357cb793ea76e1dbe68575f716a50372a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588465"
---
# <a name="step-1-deploy-the-agent"></a>Étape 1 : Déployer l’agent


Au cours de cette étape, vous devez sélectionner la machine Linux qui fera office de proxy entre Azure Sentinel et votre solution de sécurité. Vous devez exécuter un script sur la machine proxy qui :
- Installe l’agent Log Analytics et le configure si nécessaire pour écouter les messages Syslog.
- Configure le démon Syslog pour écouter les messages Syslog sur le port TCP 514, puis transfère uniquement les messages CEF à l’agent Log Analytics via le port TCP 25226.
- Définit l’agent Syslog pour collecter les données et les envoyer de façon sécurisée à Azure Sentinel, où elles sont analysées et enrichies.
 
## <a name="deploy-the-agent"></a>Déployer l’agent
 
1. Dans le portail Azure Sentinel, cliquez sur **Data Connectors** (Connecteurs de données) et sélectionnez **Common Event Format (CEF)** puis **Open connector page** (Ouvrir la page du connecteur). 

1. Sous **Installer et configurer l’agent Syslog**, sélectionnez le type de votre machine (Azure, autre cloud ou locale). 
   > [!NOTE]
   > Étant donné que le script de l’étape suivante installe l’agent Log Analytics et connecte la machine à votre espace de travail Azure Sentinel, vérifiez que cette machine n’est pas connectée à un autre espace de travail.
1. Vous devez disposer d’autorisations élevées (sudo) sur votre machine. Vérifiez que vous disposez de Python sur votre machine à l’aide de la commande suivante : `python –version`

1. Exécutez le script suivant sur votre machine proxy.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Pendant l’exécution du script, vérifiez que vous ne recevez pas de messages d’erreur ou d’avertissement.

Passez à [l’ÉTAPE 2 : Configurez votre solution de sécurité pour transférer des messages CEF](connect-cef-solution-config.md) .


## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter les appliances CEF à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).

