---
title: Connecter des données Fortinet à Azure Sentinel | Microsoft Docs
description: Connectez votre appliance Fortinet à Azure Sentinel pour consulter des tableaux de bord, créer des alertes personnalisées et améliorer les enquêtes. 
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 8aa8599cbaab6af00d7b4122b94c9e24870881f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86511328"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Connecter Fortinet à Azure Sentinel



Cet article explique comment connecter votre appliance Fortinet à Azure Sentinel. Le connecteur de données Fortinet vous permet de connecter facilement vos journaux Fortinet à Azure Sentinel, de consulter des tableaux de bord, de créer des alertes personnalisées et d’améliorer les enquêtes. L’utilisation de Fortinet sur Azure Sentinel vous permet d’obtenir davantage d’informations sur l’utilisation d’Internet de votre organisation et améliore ses fonctionnalités de sécurité. 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>Transférer les journaux Fortinet à l’agent Syslog

Configurez Fortinet pour transférer les messages Syslog au format CEF à votre espace de travail Azure par le biais de l’agent Syslog.

1. Ouvrez l’interface CLI sur votre appliance Fortinet et exécutez les commandes suivantes :

    ```console
    config log syslogd setting
    set format cef
    set port 514
    set server <ip_address_of_Receiver>
    set status enable
    end
    ```

    - Remplacez l’**adresse IP** du serveur par l’adresse IP de l’agent.
    - Affectez au **port Syslog** la valeur **514** ou la valeur du port défini sur l’agent.
    - Pour activer le format CEF dans les premières versions de FortiOS, vous devrez peut-être exécuter le jeu de commandes **csv disable**.
 
   > [!NOTE] 
   > Pour plus d’informations, accédez à la [bibliothèque de documents Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Sélectionnez votre version et utilisez le manuel d’utilisation (**Handbook**) et la référence des messages de journaux (**Log Message Reference**).

1. Pour utiliser le schéma pertinent dans Azure Monitor Log Analytics pour les événements Fortinet, recherchez `CommonSecurityLog`.

1. Passez à [l’ÉTAPE 3 : Valider la connectivité](connect-cef-verify.md).


## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à connecter des appliances Fortinet à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilisez des classeurs](tutorial-monitor-your-data.md) pour superviser vos données.


