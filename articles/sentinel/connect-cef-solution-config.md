---
title: Configurer votre solution de sécurité pour connecter des données CEF à Azure Sentinel en préversion | Microsoft Docs
description: Découvrez comment configurer votre solution de sécurité pour connecter des données CEF à Azure Sentinel.
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
ms.openlocfilehash: e2ed3680a0867ab8f7e2ad41603883f07a4be427
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655746"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>ÉTAPE 2 : Configurer votre solution de sécurité pour envoyer des messages CEF

Au cours de cette étape, vous allez effectuer les modifications de configuration nécessaires sur votre solution de sécurité pour envoyer des journaux à l’agent CEF.

## <a name="configure-a-solution-with-a-connector"></a>Configurer une solution avec un connecteur

Si votre solution de sécurité possède déjà un connecteur existant, utilisez les instructions spécifiques au connecteur comme suit :

- [AI Vectra Detect](connect-ai-vectra-detect.md)
- [Check Point](connect-checkpoint.md)
- [Cisco](connect-cisco.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5 ASM](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>Configurer une autre solution

Si aucun connecteur n’existe pour votre solution de sécurité spécifique, utilisez les instructions génériques suivantes pour transférer les journaux à l’agent CEF.

1. Accédez à l’article de configuration spécifique pour connaître la procédure de configuration de votre solution pour envoyer des messages CEF. Si votre solution n’est pas répertoriée sur l’appliance, vous devez définir ces valeurs pour que l’appliance envoie les journaux nécessaires dans le format approprié à l’agent Azure Sentinel Syslog, en fonction de l’agent Log Analytics. Vous pouvez modifier ces paramètres dans votre appliance, à condition de les modifier également dans le démon Syslog sur l'agent Azure Sentinel.
    - Protocole = TCP
    - Port = 514
    - Format = CEF
    - Adresse IP : assurez-vous d’envoyer les messages CEF à l’adresse IP de la machine virtuelle dédiée à cet effet.

   > [!NOTE]
   > Cette solution prend en charge Syslog RFC 3164 ou RFC 5424.

1. Pour utiliser le schéma pertinent dans Log Analytics pour les événements CEF, recherchez `CommonSecurityLog`.

1. Continuez jusqu’à l’ÉTAPE 3 : [valider la connectivité](connect-cef-verify.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter les appliances CEF à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](./tutorial-detect-threats-built-in.md).