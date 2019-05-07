---
title: Sélectionner et de déployer Azure Security Center pour l’agent IoT préversion | Microsoft Docs
description: En savoir plus sur comment sélectionner et de déployer Azure Security Center pour les agents de sécurité IoT sur les appareils IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 5c76cce5a3d9add8a1dcd8acc439a70c4a8655a9
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65200569"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Sélectionner et de déployer un agent de sécurité sur votre appareil IoT

> [!IMPORTANT]
> Azure Security Center pour IoT est disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Security Center (ASC) pour IoT fournit des architectures de référence pour les agents de sécurité qui surveillent et collectant des données à partir d’appareils IoT.
Consultez [architecture de référence de l’agent de sécurité](security-agent-architecture.md) pour en savoir plus.

Les agents sont développées en tant que projets open source et sont disponibles dans les deux versions : <br> [C](https://aka.ms/iot-security-github-c), et [ C# ](https://aka.ms/iot-security-github-cs).

Dans cet article, vous apprendrez comment : 
> [!div class="checklist"]
> * Comparer les versions de l’agent de sécurité
> * Découvrir les plateformes prises en charge de l’agent
> * Choisissez la version de l’agent approprié pour votre solution

## <a name="understand-security-agent-options"></a>Comprendre les options de l’agent de sécurité

Chaque ASC pour la version de l’agent de sécurité IoT offre le même ensemble de fonctionnalités et prend en charge les options de configuration similaires. 

L’agent de sécurité basée sur C a un moindre encombrement mémoire et est le choix idéal pour les appareils avec moins de ressources disponibles. 

|     | Agent de sécurité basée sur C | C#-en fonction de l’agent de sécurité |
| --- | ----------- | --------- |
| Open source | Disponible sous [licence MIT](https://en.wikipedia.org/wiki/MIT_License) dans [Github](https://aka.ms/iot-security-github-cs) | Disponible sous [licence MIT](https://en.wikipedia.org/wiki/MIT_License) dans [Github](https://aka.ms/iot-security-github-c) |
| Langage de développement    | C | C# |
| Plateformes Windows prises en charge ? | Non  | Oui |
| Conditions préalables de Windows | --- | [WMI](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| Plateformes Linux prises en charge ? | Oui, x64 et x86 | Oui, x64 uniquement |
| Conditions préalables de Linux | libunwind8, libcurl3, uuid-runtime, auditd, audispd-plugins | libunwind8, libcurl3, uuid-runtime, auditd, audispd-plugins, sudo, netstat, iptables |
| Encombrement de disque | 10.5 MO | 90 MO |
| Encombrement de mémoire (en moyenne) | 5.5 MO | 33 MO |
| [Authentification](concept-security-agent-authentication-methods.md) à IoT Hub | Oui | Oui |
| Données de sécurité [collection](how-to-agent-configuration.md#supported-security-events) | Oui | Oui |
| Agrégation des événements | Oui | Oui |
| Configuration à distance via [jumeau de module de sécurité](concept-security-module.md) | Oui | Oui |


## <a name="choose-an-agent-flavor"></a>Choisissez une version de l’agent 

Répondez aux questions suivantes concernant vos appareils IoT pour sélectionner l’agent approprié :

- Utilisez-vous _Windows Server_ ou _Windows IoT Core_? 

    [Déployer un C#-en fonction de l’agent de sécurité pour Windows](how-to-deploy-windows-cs.md).

- Vous utilisez une distribution Linux avec x86 architecture ? 

    [Déployer un agent de sécurité basée sur C pour Linux](how-to-deploy-linux-c.md).

- Vous utilisez une distribution Linux avec x64 architecture ?

    Vous pouvez utiliser une version de l’agent. <br>
    [Déployer un agent de sécurité basée sur C pour Linux](how-to-deploy-linux-c.md) et/ou [déployer un C#-en fonction de l’agent de sécurité pour Linux](how-to-deploy-linux-cs.md).

Les deux versions d’agent offrent le même ensemble de fonctionnalités et prend en charge les options de configuration similaires.
Consultez [comparaison de l’agent de sécurité](how-to-deploy-agent.md#understand-security-agent-options) pour en savoir plus.

## <a name="supported-platforms"></a>Plateformes prises en charge

La liste suivante inclut toutes les plateformes prises en charge actuellement.

|ASC pour l’agent IoT |Système d’exploitation |Architecture |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   x64|
|C|Ubuntu 18.04 |   x64|
|C|Debian 9 |   x64, x86|
|C#|Ubuntu 16.04    |x64|
|C#|Ubuntu 18.04    |x64|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core build 17763 |x64|

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les options de configuration, continuer au guide pratique pour configurer l’agent. 
> [!div class="nextstepaction"]
> [Configuration de l’agent comment guide](./how-to-agent-configuration.md)
