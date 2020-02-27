---
title: Connecter des données F5 à Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter des données F5 à Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 6f33cecca1c67f91d0f2be64ab156f45ee500521
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588261"
---
# <a name="connect-f5-to-azure-sentinel"></a>Connecter F5 à Azure Sentinel

Cet article explique comment connecter votre appliance F5 à Azure Sentinel. Le connecteur de données F5 vous permet de connecter facilement vos journaux F5 à Azure Sentinel, de consulter des tableaux de bord, de créer des alertes personnalisées et d'améliorer les enquêtes. L'utilisation de F5 sur Azure Sentinel vous permettra d'obtenir davantage d'informations sur l'utilisation d'Internet au sein de votre organisation et améliorera ses fonctionnalités de sécurité. 

## <a name="configure-your-f5-to-send-cef-messages"></a>Configurer votre appliance F5 pour envoyer des messages CEF

1. Accédez à [Configuration de la journalisation des événements de sécurité des applications](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html) et suivez les instructions pour configurer la journalisation à distance, en suivant les recommandations ci-dessous :
   - Définissez le **Type de stockage distant** sur **CEF**.
   - Définissez le **Protocole** sur **TCP**.
   - Définissez l'**adresse IP** sur l'adresse IP du serveur Syslog.
   - Définissez le **numéro de port** sur **514** ou le port que vous avez défini pour l'agent.
   - Vous pouvez définir la **Taille maximale de chaîne de requête** sur la taille que vous avez définie dans votre agent.

1. Pour utiliser le schéma pertinent dans Log Analytics pour les événements CEF, recherchez `CommonSecurityLog`.

1. Passez à l’[ÉTAPE 3 : Valider la connectivité](connect-cef-verify.md).


## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter F5 à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).
- [Utilisez des classeurs](tutorial-monitor-your-data.md) pour superviser vos données.

