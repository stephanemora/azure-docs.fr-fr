---
title: Connecter des données Syslog à Azure Sentinel en préversion | Microsoft Docs
description: Découvrez comment connecter des données Syslog à Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5dd59729-c623-4cb4-b326-bb847c8f094b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 673b1df6094703bebcbfd9d82c1268c01d46e814
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233583"
---
# <a name="connect-your-external-solution-using-syslog"></a>Connectez votre solution externe à l’aide de Syslog

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez connecter n’importe quelle appliance locale prenant en charge Syslog à Azure Sentinel. C’est possible via un agent basé sur une machine Linux entre l’appliance et Azure Sentinel. Si votre machine Linux est dans Azure, vous pouvez diffuser les journaux de votre appliance ou application vers un espace de travail dédié que vous créez dans Azure, avant de le connecter. Si votre machine Azure n’est pas dans Azure, vous pouvez diffuser les journaux de votre appliance vers une machine virtuelle locale dédiée sur laquelle vous installez l’Agent pour Linux. 

> [!NOTE]
> Si votre appliance prend en charge Syslog CEF, la connexion est plus complète et vous devez choisir cette option et suivre les instructions présentes dans [Connexion des données à partir de CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Fonctionnement

La connexion Syslog est accomplie via un agent pour Linux. Par défaut, l’agent pour Linux reçoit les événements du démon Syslog par UDP, mais dans les cas où une machine Linux doit collecter un volume important d’événements Syslog (par exemple, lorsqu’un agent Linux reçoit des événements d’autres appareils), la configuration est modifiée afin d’utiliser le transport TCP entre le démon Syslog et l’agent.

## <a name="connect-your-syslog-appliance"></a>Connecter votre appliance Syslog

1. Dans le portail Azure Sentinel, sélectionnez **Connecteurs de données** et choisissez la vignette **Syslog**.
2. Si votre machine Linux n’est pas dans Azure, téléchargez et installez l’**Agent pour Linux** Azure Sentinel sur votre appliance. 
1. Si vous travaillez dans Azure, sélectionnez ou créez une machine virtuelle au sein de l’espace de travail Azure Sentinel dédié à la réception des messages de Syslog. Sélectionnez la machine virtuelle dans les espaces de travail Azure Sentinel et cliquez sur **Connecter** en haut du volet gauche.
3. Cliquez sur **Configurer les journaux pour leur connexion** dans le programme d’installation du connecteur de Syslog. 
4. Cliquez sur **Appuyez pour ouvrir le panneau de configuration**.
1. Sélectionnez **Données**, puis **Syslog**.
   - Veillez à ce que chaque installation que vous envoyez via Syslog se trouve dans le tableau. Pour chaque installation à surveiller, définissez une gravité. Cliquez sur **Appliquer**.
1. Dans votre machine Syslog, assurez-vous que vous envoyez ces installations. 

3. Pour utiliser le schéma pertinent dans Log Analytics pour les journaux Syslog, recherchez **Syslog**.




## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter les appliances locales Syslog à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).
