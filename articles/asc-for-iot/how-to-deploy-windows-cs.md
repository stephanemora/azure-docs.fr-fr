---
title: Installation Windows d’Azure Security Center pour IoT (préversion) | Microsoft Docs
description: Découvrez comment installer l’agent Azure Security Center pour IoT sur les appareils Windows 32 bits ou 64 bits.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: 64fc576885bb9d9c3c46aafd808db65d2f8ff77f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65200615"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>Déployer l’agent de sécurité Azure Security Center pour IoT basé sur C# pour Windows

> [!IMPORTANT]
> Azure Security Center pour IoT est disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ce guide explique comment installer l’agent de sécurité Azure Security Center (ASC) pour IoT basé sur C# sur Windows.

Dans ce guide, vous apprendrez comment : 
> [!div class="checklist"]
> * Installer
> * Vérifier le déploiement
> * Désinstaller l’agent
> * Résolution des problèmes 

## <a name="prerequisites"></a>Prérequis

Pour d’autres plateformes et versions de l’agent, consultez [Choisir l’agent de sécurité adéquat](how-to-deploy-agent.md).

1. Les droits d’administrateur locaux sur l’ordinateur sur lequel vous souhaitez installer. 

1. [Créez un module de sécurité](quickstart-create-security-twin.md) pour l’appareil.

## <a name="installation"></a>Installation 

Pour installer l’agent de sécurité, procédez comme suit :

1. Pour installer l’agent ASC pour IoT Windows C# sur l’appareil, téléchargez la version la plus récente sur votre machine à partir du [dépôt GitHub](https://github.com/Azure/Azure-IoT-Security-Agent-CS) d’ASC pour IoT.

2. Extrayez le contenu du package et accédez au dossier /Install.

3. Ouvrez Windows PowerShell en tant qu’administrateur. 
    1. Ajoutez des autorisations en cours d’exécution au script InstallSecurityAgent en exécutant ```Unblock-File .\InstallSecurityAgent.ps1```.
    
        Exécutez ensuite la commande suivante :

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Par exemple :
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    Pour plus d’informations sur les paramètres d’authentification, consultez [Guide pratique pour configurer l’authentification](concept-security-agent-authentication-methods.md).

Ce script effectue les opérations suivantes :

- Installation des composants requis.

- Ajout d’un utilisateur de service (avec connexion interactive désactivée).

- Installation de l’agent en tant que **service système**.

- Configuration de l’agent avec les paramètres d’authentification fournis.


Pour obtenir de l’aide, utilisez la commande Get-Help dans PowerShell. <br>Exemple de commande Get-Help :  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Vérifier l’état du déploiement

- Vérifiez l’état du déploiement de l’agent en exécutant la commande suivante :<br>
    ```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Désinstaller l’agent

Pour désinstaller l’agent :

1. Exécutez le script PowerShell suivant avec le paramètre **-mode** défini sur **Uninstall**.  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>Résolution de problèmes

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

    **PowerShell**
     ```
     Restart-Service "ASC IoT Agent"
     ```
     
   or

    **CMD**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. Pour plus d’informations sur l’échec, examinez le fichier journal.

   Emplacement du fichier journal : `%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>Étapes suivantes
- Lire la [vue d’ensemble](overview.md) du service ASC pour IoT
- Découvrir plus en détail l’[architecture](architecture.md) ASC pour IoT
- Activer le [service](quickstart-onboard-iot-hub.md)
- Consulter les [Questions fréquentes (FAQ)](resources-frequently-asked-questions.md)
- Comprendre les [alertes](concept-security-alerts.md)