---
title: Guide d’installation et déploiement de l’agent Linux C d’Azure Security Center pour l’agent IoT préversion | Microsoft Docs
description: Découvrez comment installer le centre de sécurité Azure pour l’agent IoT sur Linux 32 bits et 64 bits.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: mlottner
ms.openlocfilehash: 147813ae096114b4dfc1a20d2e0a70639aa82445
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58754450"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Déployer Azure Security Center pour l’agent de sécurité basée sur les IoT C pour Linux

> [!IMPORTANT]
> Azure Security Center pour IoT est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ce guide explique comment installer et déployer Azure Security Center (ASC) pour l’agent de sécurité basée sur les IoT C sur Linux.

Dans ce guide, vous apprendrez comment : 
> [!div class="checklist"]
> * Installer
> * Vérifier le déploiement
> * Désinstaller l’agent
> * Résolution des problèmes 

## <a name="prerequisites"></a>Conditions préalables

Pour d’autres plateformes et les versions de l’agent, consultez [choisir l’agent de sécurité adéquat](how-to-deploy-agent.md).

1. Pour déployer l’agent de sécurité, les droits d’administrateur local sont requis sur l’ordinateur que vous souhaitez installer sur (sudo).

1. [Créer un module de sécurité](quickstart-create-security-twin.md) pour l’appareil.

## <a name="installation"></a>Installation 

Pour installer et déployer l’agent de sécurité, procédez comme suit :


1. Téléchargez la version la plus récente sur votre ordinateur à partir de [Github](https://aka.ms/iot-security-github-c).

1. Extrayez le contenu du package et accédez à la _/installer_ dossier.

1. Ajoutez des autorisations en cours d’exécution sur le **InstallSecurityAgent script** en exécutant la commande suivante :
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Ensuite, exécutez : 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   Consultez [comment configurer l’authentification](concept-security-agent-authentication-methods.md) pour plus d’informations sur les paramètres d’authentification.

Ce script effectue les opérations suivantes :

1. Installation des composants requis.

2. Ajoute un utilisateur de service (avec une connexion interactive désactivée).

3. Installe l’agent en tant qu’un **démon** -part du principe que l’appareil utilise **systemd** pour la gestion de service.

4. Configure l’agent avec les paramètres d’authentification fournis. 

Pour obtenir une aide supplémentaire, exécutez le script avec le paramètre de l’aide : 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>Désinstaller l’agent

Pour désinstaller l’agent, exécutez le script avec l’option –-Désinstaller paramètre :

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>Résolution de problèmes
Vérifier l’état du déploiement en exécutant :

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>Étapes suivantes
- Lire l’ASC pour le service IoT [vue d’ensemble](overview.md)
- En savoir plus sur ASC pour IoT [Architecture](architecture.md)
- Activer la [service](quickstart-onboard-iot-hub.md)
- Lire le [Forum aux questions](resources-frequently-asked-questions.md)
- Comprendre [alertes de sécurité](concept-security-alerts.md)