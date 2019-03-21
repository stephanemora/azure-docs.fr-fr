---
title: Collecter les données Syslog Sentinel version préliminaire d’Azure | Microsoft Docs
description: Découvrez comment collecter les données Syslog dans Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5dd59729-c623-4cb4-b326-bb847c8f094b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 55949da97f58f1d8c1670f69d25e92d6bb4e9eef
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57447300"
---
# <a name="connect-your-external-solution-using-syslog"></a>Connectez votre solution externe à l’aide de Syslog

> [!IMPORTANT]
> Sentinel Azure est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez vous connecter n’importe quel matériel sur site qui prend en charge Syslog à Azure Sentinel. Cela permet à l’aide d’un agent basé sur une machine Linux entre l’appliance et Sentinel Azure. Si votre machine Linux dans Azure, vous pouvez diffuser les journaux à partir de votre appliance ou d’une application pour un espace de travail dédié vous créez dans Azure et connectez. Si votre machine Linux n’est pas dans Azure, vous pouvez diffuser les journaux à partir de votre appliance à un dédié en local de machine virtuelle ou l’ordinateur sur lequel vous installez l’Agent pour Linux. 

> [!NOTE]
> Si votre matériel prend en charge Syslog CEF, la connexion est plus complète et vous devez choisir cette option et suivez les instructions de [collecte des données à partir du format CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Fonctionnement

Collection de Syslog s’effectue à l’aide d’un agent pour Linux. Par défaut, l’agent pour Linux reçoit des événements à partir du démon Syslog sur UDP, mais dans les cas où une machine Linux doit collecter un volume élevé d’événements Syslog, telles que lorsqu’un agent Linux reçoit des événements à partir d’autres appareils, la configuration est modifiée pour Utilisez le transport TCP entre le démon Syslog et l’agent.

## <a name="connect-your-syslog-appliance"></a>Se connecter à votre appliance de Syslog

1. Dans le portail Azure Sentinel, sélectionnez **collecte des données** et choisissez le **Syslog** vignette.
2. Si votre machine Linux n’est pas dans Azure, télécharger et installer le Azure Sentinel **Agent pour Linux** sur votre appliance. 
1. Si vous travaillez dans Azure, sélectionnez ou créez une machine virtuelle qui au sein de l’espace de travail Azure Sentinel qui est dédié à la réception des messages de Syslog. Sélectionnez la machine virtuelle dans les espaces de travail Sentinel Azure et cliquez sur **Connect** en haut du volet gauche.
3. Cliquez sur **configurer les journaux à collecter** dans le programme d’installation du connecteur de Syslog. 
4. Cliquez sur **Appuyez ici pour ouvrir le panneau de configuration**.
1. Sélectionnez **données** , puis **Syslog**.
   - Assurez-vous que chaque fonctionnalité que vous envoyez par Syslog se trouve dans la table. Pour chaque fonctionnalité, vous vous apprêtez à surveiller, de définir un niveau de gravité. Cliquez sur **Appliquer**.
1. Dans votre machine Syslog, assurez-vous que vous envoyez ces installations. 

3. Pour utiliser le schéma pertinent dans Analytique de journal pour les journaux Syslog, recherchez **Syslog**.




## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter des appliances locales de Syslog à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, consultez les articles suivants :
- Découvrez comment [obtenez une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main [détecter des menaces avec Azure Sentinel](tutorial-detect-threats.md).
