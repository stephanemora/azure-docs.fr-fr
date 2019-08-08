---
title: Connecter des données Syslog à Azure Sentinel en préversion | Microsoft Docs
description: Découvrez comment connecter des données Syslog à Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5dd59729-c623-4cb4-b326-bb847c8f094b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2019
ms.author: rkarlin
ms.openlocfilehash: dad74410562aa54aeb61675e7dc1c0adccc44797
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679203"
---
# <a name="connect-your-external-solution-using-syslog"></a>Connectez votre solution externe à l’aide de Syslog

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez connecter n’importe quelle appliance locale prenant en charge Syslog à Azure Sentinel. C’est possible via un agent basé sur une machine Linux entre l’appliance et Azure Sentinel. Si votre machine Linux est dans Azure, vous pouvez diffuser les journaux de votre appliance ou application vers un espace de travail dédié que vous créez dans Azure, avant de le connecter. Si votre machine Azure n’est pas dans Azure, vous pouvez diffuser les journaux de votre appliance vers une machine virtuelle locale dédiée sur laquelle vous installez l’Agent pour Linux. 

> [!NOTE]
> Si votre appliance prend en charge Syslog CEF, la connexion est plus complète et vous devez choisir cette option et suivre les instructions présentes dans [Connexion des données à partir de CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Fonctionnement

Syslog est un protocole de journalisation d’événements commun à Linux. Les applications envoient les messages qui peuvent être stockés sur l’ordinateur local ou remis à un collecteur Syslog. Lorsque l’agent Log Analytics pour Linux est installé, il configure le démon Syslog local pour qu’il transfère des messages à l’agent. L’agent envoie ensuite le message à Azure Monitor, où un enregistrement correspondant est créé.

Pour en savoir plus, voir [Sources de données Syslog dans Azure Monitor](../azure-monitor/platform/data-sources-syslog.md).

> [!NOTE]
> L’agent peut collecter les journaux à partir de plusieurs sources, mais doit être installé sur une machine proxy dédiée.

## <a name="connect-your-syslog-appliance"></a>Connecter votre appliance Syslog

1. Dans le portail Azure Sentinel, sélectionnez **Connecteurs de données**, sélectionnez la ligne **Syslog** dans le tableau et dans le volet syslog à droite, cliquez sur **Open connector page (Ouvrir la page du connecteur)** .
2. Si votre machine Linux se trouve dans Azure, sélectionnez **Download and install agent on Azure Linux virtual machine (Télécharger et installer l’agent sur la machine virtuelle Linux Azure)** . Dans la fenêtre Machines virtuelles, sélectionnez les machines sur lesquelles vous souhaitez installer l’agent, puis cliquez sur **Connect (Se connecter)** en haut.
1. Si votre machine Linux ne se trouve pas dans Azure, sélectionnez **Download and install agent on Linux non-Azure machine (Télécharger et installer l’agent sur la machine virtuelle Linux non-Azure)** . Dans la fenêtre **Direct agent (Agent direct)** , copiez la commande sous **Download and onboard agent for Linux (Télécharger et intégrer l’agent pour Linux)** et exécutez-la sur votre ordinateur. 
   > [!NOTE]
   > Veillez à configurer la sécurité de la machine en fonction de la stratégie de sécurité de votre organisation. Par exemple, vous pouvez configurer votre réseau de sorte qu’il s’accorde à la stratégie de sécurité de votre réseau d’entreprise, et modifier les ports et les protocoles dans le démon pour les adapter à vos besoins. 

1. Sous **Configure the logs to be connected (Configurer les journaux à connecter)** dans la fenêtre de configuration du connecteur syslog, suivez les instructions suivantes :
    1. Cliquez sur le lien pour **ouvrir la configuration des paramètres avancés de votre espace de travail**. 
    1. Sélectionnez **Données**, puis **Syslog**.
    1. Ensuite, dans la table, définissez les fonctions que vous souhaitez que syslog collecte. Vous devez ajouter ou sélectionner les fonctions que votre appliance syslog inclut dans ses en-têtes de journal. Vous pouvez voir cette configuration dans votre appliance Syslog dans Syslog-d dans le dossier : /etc/rsyslog.d/security-config-omsagent.conf et dans r-Syslog sous /etc/syslog-ng/security-config-omsagent.conf. 
       > [!NOTE]
       > Si vous activez la case à cocher pour **Appliquer la configuration ci-dessous à mes machines**, cette configuration s’applique à toutes les machines Linux connectées à cet espace de travail. Vous pouvez voir cette configuration dans votre machine Syslog sous 
1. Cliquez sur **Appuyez pour ouvrir le panneau de configuration**.
1. Sélectionnez **Données**, puis **Syslog**.
   - Veillez à ce que chaque installation que vous envoyez via Syslog se trouve dans le tableau. Pour chaque installation à surveiller, définissez un niveau de gravité. Cliquez sur **Appliquer**.
1. Dans votre machine Syslog, assurez-vous que vous envoyez ces installations. 

1. Pour utiliser le schéma pertinent dans Log Analytics pour les journaux Syslog, recherchez **Syslog**.
1. Vous pouvez utiliser la fonction Kusto décrite dans [Utilisation de fonctions dans les requêtes de journal Azure Monitor](../azure-monitor/log-query/functions.md) pour analyser vos messages Syslog, puis les enregistrer en tant que nouvelle fonction Log Analytics, puis utiliser la fonction en tant que nouveau type de données.




## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter les appliances locales Syslog à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).
