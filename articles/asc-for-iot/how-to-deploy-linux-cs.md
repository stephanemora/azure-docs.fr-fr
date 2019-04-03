---
title: Pour installer et déployer Linux C# agent d’Azure Security Center pour la version préliminaire IoT | Microsoft Docs
description: Découvrez comment installer le centre de sécurité Azure pour l’agent IoT sur Linux 32 bits et 64 bits.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 4a53bcf77696d3aa53a4a404bfacd6f6d468885b
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862097"
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

1. Pour déployer l’agent de sécurité, les droits d’administrateur local sont requis sur l’ordinateur sur lequel vous souhaitez effectuer l’installation. 

1. [Créez un module de sécurité](quickstart-create-security-twin.md) pour l’appareil.

## <a name="installation"></a>Installation 

Pour déployer l’agent de sécurité, procédez comme suit :

1. Téléchargez la version la plus récente sur votre machine depuis [GitHub](https://aka.ms/iot-security-github-cs).

1. Extrayez le contenu du package et accédez au dossier _/Install_.

1. Ajoutez des autorisations en cours d’exécution sur le **InstallSecurityAgent script** en exécutant `chmod +x InstallSecurityAgent.sh` 

1. Ensuite, exécutez la commande suivante : 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   Pour plus d’informations sur les paramètres d’authentification, consultez [Guide pratique pour configurer l’authentification](concept-security-agent-authentication-methods.md).

Ce script effectue les opérations suivantes :

- Installation des composants requis.

- Ajout d’un utilisateur de service (avec connexion interactive désactivée).

- Installation de l’agent en tant que **démon**, ce qui suppose que l’appareil utilise **systemd** pour la gestion des services.

- Configuration de **sudoers** pour permettre à l’agent d’effectuer certaines tâches en tant que root.

- Configuration de l’agent avec les paramètres d’authentification fournis.


Pour obtenir de l’aide, exécutez le script avec le paramètre –help : `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Désinstaller l’agent

Pour désinstaller l’agent, exécutez le script avec le paramètre –u : `./InstallSecurityAgent.sh -u`. 

> [!NOTE]
> La désinstallation ne supprime pas les prérequis manquants qui ont été installés pendant l’installation.

## <a name="troubleshooting"></a>Résolution de problèmes  

1. Vérifiez l’état du déploiement en exécutant la commande suivante :

    `systemctl status ASCIoTAgent.service`

2. Activez la journalisation.  
   Si l’agent ne parvient pas à démarrer, activez la journalisation pour obtenir plus d’informations.

   Pour activer la journalisation, effectuez les étapes suivantes :

   1. Ouvrez le fichier de configuration dans l’éditeur Linux de votre choix :

        `vi /var/ASCIoTAgent/General.config`

   1. Modifiez les valeurs suivantes : 

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" /> 
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```
       La valeur **logFilePath** est configurable. 

       > [!NOTE]
       > Nous vous recommandons de **désactiver** la journalisation une fois les problèmes résolus. Le fait de laisser la journalisation **active** augmente la taille du fichier journal et l’utilisation des données.

   1. Redémarrez l’agent en exécutant la commande suivante :

       `systemctl restart ASCIoTAgent.service`

   1. Pour plus d’informations sur l’échec, affichez le fichier journal.  

       Emplacement du fichier journal est : `/var/ASCIoTAgent/IotAgentLog.log`

       Changez le chemin du fichier en fonction du nom que vous avez choisi pour le paramètre **logFilePath** à l’étape 2. 

## <a name="next-steps"></a>Étapes suivantes

- Lire la [vue d’ensemble](overview.md) du service ASC pour IoT
- Découvrir plus en détail l’[architecture](architecture.md) ASC pour IoT
- Activer le [service](quickstart-onboard-iot-hub.md)
- Consulter les [Questions fréquentes (FAQ)](resources-frequently-asked-questions.md)
- Comprendre les [alertes](concept-security-alerts.md)