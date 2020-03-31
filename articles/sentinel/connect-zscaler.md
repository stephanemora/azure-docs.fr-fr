---
title: Connecter des données Zscaler à Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter des données Zscaler à Azure Sentinel.
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: cc784afe5db64ccc4aad13fae7a2fa748e4befa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587989"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Connecter Zscaler Internet Access à Azure Sentinel

> [!IMPORTANT]
> Le connecteur de données Zscaler dans Azure Sentinel est actuellement en préversion publique.
> Cette fonctionnalité est fournie sans contrat de niveau de service et est déconseillée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article explique comment connecter votre appliance Zscaler Internet Access à Azure Sentinel. Le connecteur de données Zscaler vous permet de connecter facilement vos journaux Zscaler Internet Access (ZIA) à Azure Sentinel, de consulter des tableaux de bord, de créer des alertes personnalisées et d’améliorer l’investigation. En utilisant Zscaler sur Azure Sentinel, vous obtenez davantage d’insights sur l’usage d’Internet dans votre organisation, ce qui vous aide à améliorer la sécurité des opérations. 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>Configurer votre appliance Zscaler pour envoyer des messages CEF

1. Sur l’appliance Zscaler, vous devez définir ces valeurs pour que l’appliance envoie les journaux nécessaires dans le format approprié à l’agent Azure Sentinel Syslog, en fonction de l’agent Log Analytics. Vous pouvez modifier ces paramètres dans votre appliance, à condition de les modifier également dans le démon Syslog sur l'agent Azure Sentinel.
    - Protocole = TCP
    - Port = 514
    - Format = CEF
    - Adresse IP : assurez-vous d’envoyer les messages CEF à l’adresse IP de la machine virtuelle dédiée à cet effet.
 Pour plus d’informations, consultez le [Guide de déploiement Zscaler et Azure Sentinel](https://aka.ms/ZscalerCEFInstructions).
 
   > [!NOTE]
   > Cette solution prend en charge Syslog RFC 3164 ou RFC 5424.


1. Pour utiliser le schéma pertinent dans Log Analytics pour les événements CEF, recherchez `CommonSecurityLog`.
1. Passez à l’[ÉTAPE 3 : Valider la connectivité](connect-cef-verify.md).


## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Zscaler Internet Access à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).
- [Utilisez des classeurs](tutorial-monitor-your-data.md) pour superviser vos données.


