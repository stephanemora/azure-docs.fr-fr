---
title: Pour installer et déployer Linux C# agent d’Azure Security Center pour la version préliminaire IoT | Microsoft Docs
description: Découvrez comment installer le centre de sécurité Azure pour l’agent IoT sur Linux 32 bits et 64 bits.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: be4c663d3a1e99ef67cbbbc2f39b315f1080125c
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758335"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Déployer Azure Security Center pour IoT C#-en fonction de l’agent de sécurité pour Linux

> [!IMPORTANT]
> Azure Security Center pour IoT est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ce guide explique comment installer et déployer Azure Security Center (ASC) pour IoT C#-en fonction de la sécurité de l’agent sur Linux.

Dans ce guide, vous apprendrez comment : 
> [!div class="checklist"]
> * Installer
> * Vérifier le déploiement
> * Désinstaller l’agent
> * Résolution des problèmes 

## <a name="prerequisites"></a>Conditions préalables

Pour d’autres plateformes et les versions de l’agent, consultez [choisir l’agent de sécurité adéquat](how-to-deploy-agent.md).

1. Pour déployer l’agent de sécurité, les droits d’administrateur local sont requis sur l’ordinateur que vous souhaitez installer sur. 

1. [Créer un module de sécurité](quickstart-create-security-twin.md) pour l’appareil.

## <a name="installation"></a>Installation 

Pour déployer l’agent de sécurité, procédez comme suit :

1. Téléchargez la version la plus récente sur votre ordinateur à partir de [Github](https://aka.ms/iot-security-github-cs).

1. Extrayez le contenu du package et accédez à la _/installer_ dossier.

1. Ajoutez des autorisations en cours d’exécution sur le **InstallSecurityAgent script** en exécutant `chmod +x InstallSecurityAgent.sh` 

1. Ensuite, exécutez : 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   Consultez [comment configurer l’authentification](concept-security-agent-authentication-methods.md) pour plus d’informations sur les paramètres d’authentification.

Ce script effectue les opérations suivantes :

- Installation des composants requis.

- Ajoute un utilisateur de service (avec une connexion interactive désactivée).

- Installe l’agent en tant qu’un **démon** -cela suppose que l’appareil utilise **systemd** pour la gestion de service.

- Configure **sudoers** pour permettre à l’agent effectuer certaines tâches en tant que racine.

- Configure l’agent avec les paramètres d’authentification fourni.


Pour obtenir une aide supplémentaire, exécutez le script avec le paramètre de l’aide : `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Désinstaller l’agent

Pour désinstaller l’agent, exécutez le script avec le paramètre – u : `./InstallSecurityAgent.sh -u`. 

> [!NOTE]
> Désinstallation ne supprime pas les composants requis manquants qui ont été installés lors de l’installation.

## <a name="troubleshooting"></a>Résolution de problèmes  

1. Vérifier l’état du déploiement en exécutant :

    `systemctl status ASCIoTAgent.service`

2. Activer la journalisation.  
   Si l’agent ne parvient pas à démarrer, activer la journalisation pour obtenir plus d’informations.

   Activer la journalisation par :

   1. Ouvrez le fichier de configuration pour le modifier dans n’importe quel éditeur de Linux :

        `vi /var/ASCIoTAgent/General.config`

   1. Modifiez les valeurs suivantes : 

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" /> 
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```
       Le **logFilePath** la valeur est configurable. 

       > [!NOTE]
       > Nous recommandons l’activation de la journalisation **hors** après la résolution des problèmes sont terminée. En laissant journalisation **sur** augmente connecter à l’utilisation des données et la taille de fichier.

   1. Redémarrez l’agent en exécutant :

       `systemctl restart ASCIoTAgent.service`

   1. Afficher le fichier journal pour plus d’informations sur l’échec.  

       Emplacement du fichier journal est : `/var/ASCIoTAgent/IotAgentLog.log`

       Modifier le chemin en fonction du nom que vous avez choisi pour le **logFilePath** à l’étape 2. 

## <a name="next-steps"></a>Étapes suivantes

- Lire l’ASC pour le service IoT [vue d’ensemble](overview.md)
- En savoir plus sur ASC pour IoT [Architecture](architecture.md)
- Activer la [service](quickstart-onboard-iot-hub.md)
- Lire le [Forum aux questions](resources-frequently-asked-questions.md)
- Comprendre [alertes](concept-security-alerts.md)