---
title: Installer l'agent C# sur un appareil Windows
description: Découvrez comment installer l’agent Azure Security Center pour IoT sur les appareils Windows 32 bits ou 64 bits.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 4d7d2f0a423a50f85160a856480eaa973be7e2b0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537608"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>Déployer l’agent de sécurité Azure Security Center pour IoT basé sur C# pour Windows

Ce guide explique comment installer l’agent de sécurité Azure Security Center pour IoT basé sur C# sur Windows.

Dans ce guide, vous apprendrez comment :

> [!div class="checklist"]
> * Installer
> * Vérifier le déploiement
> * Désinstaller l’agent
> * Dépanner

## <a name="prerequisites"></a>Prérequis

Pour d’autres plateformes et versions de l’agent, consultez [Choisir l’agent de sécurité adéquat](how-to-deploy-agent.md).

1. Les droits d’administrateur locaux sur l’ordinateur sur lequel vous souhaitez installer.

1. [Créez un module de sécurité](quickstart-create-security-twin.md) pour l’appareil.

## <a name="installation"></a>Installation

Pour installer l’agent de sécurité, procédez comme suit :

1. Installez l’agent C# Windows Azure Security Center pour IoT sur l’appareil. Téléchargez la version la plus récente sur votre ordinateur à partir du [référentiel GitHub](https://github.com/Azure/Azure-IoT-Security-Agent-CS) Azure Security Center pour IoT.

1. Extrayez le contenu du package et accédez au dossier /Install.

1. Ouvrez Windows PowerShell en tant qu’administrateur.
1. Ajoutez des autorisations en cours d’exécution au script InstallSecurityAgent en exécutant :

    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```

    Exécutez ensuite la commande suivante :

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```

    Par exemple :

    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```

    Pour plus d’informations sur les paramètres d’authentification, consultez [Guide pratique pour configurer l’authentification](concept-security-agent-authentication-methods.md).

Ce script effectue les actions suivantes :

* Installation des composants requis.
* Ajout d’un utilisateur de service (avec connexion interactive désactivée).
* Installation de l’agent en tant que **service système**.
* Configuration de l’agent avec les paramètres d’authentification fournis.

Pour obtenir de l'aide, utilisez la commande Get-Help de PowerShell.

Exemple de commande Get-Help :    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Vérifier l’état du déploiement

Vérifiez l’état du déploiement de l’agent en exécutant la commande suivante :

```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Désinstaller l’agent

Pour désinstaller l’agent :

1. Exécutez le script PowerShell suivant avec le paramètre **-mode** défini sur **Uninstall**.

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ```

## <a name="troubleshooting"></a>Dépannage

Si l’agent ne parvient pas à démarrer, activez la journalisation (qui est *désactivée* par défaut) pour obtenir plus d’informations.

Pour activer la journalisation :

1. Ouvrez le fichier de configuration (General.config) dans l’éditeur de fichier de votre choix.

1. Modifiez les valeurs suivantes :

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/>
   <add key="diagnosticVerbosityLevel" value="Some" />
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Nous vous recommandons de **désactiver** la journalisation une fois les problèmes résolus. Le fait de laisser la journalisation **active** augmente la taille du fichier journal et l’utilisation des données.

1. Redémarrez l’agent en exécutant la ligne de commande PowerShell suivante :

    **Powershell**

     ```
     Restart-Service "ASC IoT Agent"
     ```

   or

    **CMD**

     ```
     sc.exe stop "ASC IoT Agent"
     sc.exe start "ASC IoT Agent"
     ```

1. Pour plus d’informations sur l’échec, examinez le fichier journal. Le fichier journal est présent dans le répertoire de travail où nous exécutons le script. 

   Emplacement du fichier journal : `.\IoTAgentLog.log`

## <a name="next-steps"></a>Étapes suivantes

* Lire la [Vue d’ensemble](overview.md) du service Microsoft Azure Security Center pour IoT
* En savoir plus sur l’[architecture](architecture.md) d’Azure Security Center pour IoT
* Activer le [service](quickstart-onboard-iot-hub.md)
* Consulter les [Questions fréquentes (FAQ)](resources-frequently-asked-questions.md)
* Comprendre les [alertes](concept-security-alerts.md)
