---
title: Valider la connectivité à Azure Sentinel | Microsoft Docs
description: Validez la connectivité de votre solution de sécurité pour vous assurer que les messages CEF sont transférés vers Azure Sentinel.
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
ms.openlocfilehash: e224f6d5cfd82dfc6cb1ce107d111ee0e031247b
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588431"
---
# <a name="step-3-validate-connectivity"></a>ÉTAPE 3 : Valider la connectivité



Après avoir déployé l’agent et configuré votre solution de sécurité pour transférer les messages CEF, utilisez cet article pour comprendre comment vérifier la connectivité entre Azure Sentinel et votre solution de sécurité. 

## <a name="how-to-validate-connectivity"></a>Comment valider la connectivité

1. Ouvrez Log Analytics pour vous assurer que les journaux sont reçus à l’aide du schéma CommonSecurityLog.<br> Plus de 20 minutes peuvent être nécessaires avant que vos journaux ne commencent à apparaître dans Log Analytics. 

1. Avant d’exécuter le script, nous vous recommandons d’envoyer des messages à partir de votre solution de sécurité pour vous assurer qu’ils sont transmis à la machine proxy Syslog que vous avez configurée. 
1. Vous devez disposer d’autorisations élevées (sudo) sur votre machine. Vérifiez que vous disposez de Python sur votre machine à l’aide de la commande suivante : `python –version`
1. Exécutez le script suivant pour vérifier la connectivité entre l’agent, Azure Sentinel et votre solution de sécurité. Celui-ci vérifie que le transfert de démon est correctement configuré, écoute les ports appropriés et s’assure que rien ne bloque la communication entre le démon et l’agent Log Analytics. Le script envoie également des messages fictifs « TestCommonEventFormat » pour vérifier la connectivité de bout en bout. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter les appliances CEF à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).
- [Utilisez des classeurs](tutorial-monitor-your-data.md) pour superviser vos données.

