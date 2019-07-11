---
title: Sélectionner et déployer votre agent Azure Security Center pour IoT (préversion) | Microsoft Docs
description: Apprenez à sélectionner et déployer des agents de sécurité Azure Security Center pour IoT sur des appareils IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: c549e5ccbda9b364b3e7d20c9572eb777c32299e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616829"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Sélectionner et de déployer un agent de sécurité sur votre appareil IoT

> [!IMPORTANT]
> Azure Security Center pour IoT est disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Security Center (ASC) pour IoT fournit des architectures de référence pour les agents de sécurité qui surveillent et collectent des données à partir d’appareils IoT.
Consultez [Architecture de référence d’agent de sécurité](security-agent-architecture.md) pour en savoir plus.

Les agents sont développés en tant que projets open source et sont disponibles en deux versions : <br> [C](https://aka.ms/iot-security-github-c) et [C#](https://aka.ms/iot-security-github-cs).

Dans cet article, vous apprendrez comment : 
> [!div class="checklist"]
> * Comparer les versions d’agent de sécurité
> * Découvrir les plateformes d’agent prises en charge
> * Choisir la bonne version de l’agent pour votre solution

## <a name="understand-security-agent-options"></a>Comprendre les options d’agent de sécurité

Chaque version de l’agent de sécurité ASC pour IoT offre le même ensemble de fonctionnalités et prend en charge des options de configuration similaires. 

L’agent de sécurité basé sur C représente un encombrement mémoire moindre et est le choix idéal pour les appareils avec moins de ressources disponibles. 

|     | Agent de sécurité basé sur C | Agent de sécurité basé sur C# |
| --- | ----------- | --------- |
| Open source | Disponible sous [licence MIT](https://en.wikipedia.org/wiki/MIT_License) dans [GitHub](https://aka.ms/iot-security-github-cs) | Disponible sous [licence MIT](https://en.wikipedia.org/wiki/MIT_License) dans [GitHub](https://aka.ms/iot-security-github-c) |
| Langage de développement    | C | C# |
| Plateformes Windows prises en charge ? | Non | OUI |
| Conditions préalables pour Windows | --- | [WMI](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| Plateformes Linux prises en charge ? | Oui, x64 et x86 | Oui, x64 uniquement |
| Composants requis Linux | libunwind8, libcurl3, uuid-runtime, auditd, audispd-plugins | libunwind8, libcurl3, uuid-runtime, auditd, audispd-plugins, sudo, netstat, iptables |
| Encombrement disque | 10,5 Mo | 90 Mo |
| Encombrement mémoire (en moyenne) | 5,5 Mo | 33 Mo |
| [Authentification](concept-security-agent-authentication-methods.md) à IoT Hub | OUI | OUI |
| [Collection](how-to-agent-configuration.md#supported-security-events) de données de sécurité | OUI | OUI |
| Agrégation des événements | OUI | OUI |
| Configuration à distance via [jumeau de module de sécurité](concept-security-module.md) | OUI | OUI |


## <a name="choose-an-agent-flavor"></a>Choisir une version de l’agent 

Répondez aux questions suivantes concernant vos appareils IoT pour sélectionner l’agent approprié :

- Utilisez-vous _Windows Server_ ou _Windows IoT Core_ ? 

    [Déployez un agent de sécurité basé sur C# pour Windows](how-to-deploy-windows-cs.md).

- Utilisez-vous une distribution Linux avec architecture x86 ? 

    [Déployez un agent de sécurité basé sur C pour Linux](how-to-deploy-linux-c.md).

- Utilisez-vous une distribution Linux avec architecture x64 ?

    Vous pouvez utiliser la version de l’agent de votre choix. <br>
    [Déployer un agent de sécurité basé sur C pour Linux](how-to-deploy-linux-c.md) et/ou [Déployer un agent de sécurité basé sur C# pour Linux](how-to-deploy-linux-cs.md).

Les deux versions de l’agent offrent le même ensemble de fonctionnalités et prennent en charge des options de configuration similaires.
Consultez [Comparaison des agents de sécurité](how-to-deploy-agent.md#understand-security-agent-options) pour en savoir plus.

## <a name="supported-platforms"></a>Plateformes prises en charge

La liste suivante inclut toutes les plateformes prises en charge actuellement.

|Agent ASC pour IoT |Système d’exploitation |Architecture |
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

Pour en savoir plus sur les options de configuration, allez au guide pratique de configuration de l’agent. 
> [!div class="nextstepaction"]
> [Guide pratique de configuration de l’agent](./how-to-agent-configuration.md)
