---
title: Sélectionner et de déployer un ASC pour l’agent IoT préversion | Microsoft Docs
description: En savoir plus sur comment sélectionner et de déployer ASC pour les agents de sécurité IoT sur les appareils IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 5ce583fe98acb7e0a4aaeb720cb2d5ed5eebb9e3
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541961"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Sélectionner et de déployer un agent de sécurité sur votre appareil IoT

> [!IMPORTANT]
> ASC pour IoT est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


ASC pour IoT fournit des architectures de référence pour les agents de sécurité qui surveillent et collectant des données à partir d’appareils IoT.

Agents de sécurité sont développées en tant que projets open source et sont disponibles dans les deux versions : <br> [C](https://aka.ms/iot-security-github-c), et [ C# ](https://aka.ms/iot-security-github-cs).

## <a name="supported-platforms-for-asc-for-iot-agents"></a>Plateformes prises en charge pour ASC pour les agents de IoT

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


## <a name="which-flavor-should-i-use"></a>Quelle version dois-je utiliser ?

Posez-vous les questions suivantes par rapport à vos appareils IoT :

- Utilisez-vous _Windows Server_ ou _Windows IoT Core_? 

    Utilisez [ASC pour l’installation de IoT pour Windows avec le C#-en fonction de l’agent de sécurité](tutorial-deploy-windows-cs.md).

- Vous utilisez une distribution Linux avec x86 architecture ? 

    Utilisez [ASC pour l’installation de IoT pour Linux avec l’agent de sécurité basée sur C](tutorial-deploy-linux-c.md).
- Vous utilisez une distribution Linux avec x64 architecture ?

    Vous pouvez utiliser les deux versions. <br>
    [ASC pour l’installation de IoT pour Linux avec l’agent de sécurité basée sur C](tutorial-deploy-linux-c.md) et/ou [ASC pour l’installation de IoT pour Linux avec le C#-en fonction de l’agent de sécurité](tutorial-deploy-linux-cs.md).

Les deux versions ont le même ensemble de fonctionnalités et prend en charge les options de configuration similaires. L’agent de sécurité basée sur C a un moindre encombrement mémoire.


## <a name="next-steps"></a>Étapes suivantes
- [Vue d'ensemble](overview.md)
- [Agents de sécurité](security-agent-architecture.md)
- [Méthodes d’authentification de l’agent de sécurité](concept-security-agent-authentication-methods.md)
- [ASC pour IoT Forum aux questions](resources-frequently-asked-questions.md)