---
title: Installation de Windows de ASC pour l’agent IoT préversion | Microsoft Docs
description: Découvrez comment installer ASC pour l’agent IoT sur les appareils Windows 32 bits ou 64 bits.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: 5c3293746fcc52570e708fd4bfab446981d49c24
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621828"
---
# <a name="deploy-an-asc-for-iot-c-based-security-agent-for-windows"></a>Déployer un ASC pour IoT C#-en fonction de l’agent de sécurité pour Windows

> [!IMPORTANT]
> ASC pour IoT est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ce guide explique comment installer l’ASC pour IoT C#-en fonction de l’agent de sécurité sur Windows.

Dans ce guide, vous apprendrez comment : 
> [!div class="checklist"]
> * Installer
> * Vérifier le déploiement
> * Désinstaller l’agent
> * Résolution des problèmes 

## <a name="prerequisites"></a>Conditions préalables

Pour d’autres plateformes et les types de l’agent, consultez [choisir l’agent de sécurité adéquat](how-to-deploy-agent.md).

1. Droits d’administrateur local sur l’ordinateur que vous souhaitez installer sur. 

1. [Créer un module de sécurité](quickstart-create-security-twin.md) pour l’appareil.

## <a name="installation"></a>Installation 

Pour installer l’agent de sécurité, procédez comme suit :

1. Pour installer l’ASC pour IoT Windows C# agent sur l’appareil, téléchargez la version la plus récente sur votre ordinateur à partir de l’ASC pour IoT [référentiel GitHub](https://github.com/Azure/Azure-IoT-Security-Agent-CS).

2. Extrayez le contenu du package et accédez au dossier /Install.

3. Ouvrez Windows PowerShell en tant qu’administrateur. 
    1. Ajouter des autorisations en cours d’exécution du script InstallSecurityAgent en exécutant ```Unblock-File .\InstallSecurityAgent.ps1```
    
        et exécutez :

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Par exemple : 
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    Consultez [comment configurer l’authentification](concept-security-agent-authentication-methods.md) pour plus d’informations sur les paramètres d’authentification.

Ce script effectue les opérations suivantes :

- Installation des composants requis.

- Ajoute un utilisateur de service (avec une connexion interactive désactivée).

- Installe l’agent en tant qu’un **système Service**.

- Configure l’agent avec les paramètres d’authentification fourni.


Pour plus d’aide, utilisez la commande Get-Help dans PowerShell <br>Exemple de Get-Help :  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Vérifier l’état du déploiement

- Vérifier l’état de déploiement de l’agent en exécutant :<br>
    ```sc.exe query "ASC IoT Agent" ```

### <a name="uninstall-the-agent"></a>Désinstaller l’agent

Pour désinstaller l’agent :

1. Exécutez le script PowerShell suivant avec la **-mode** paramètre défini sur **désinstallation**.  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>Résolution de problèmes

Si l’agent ne parvient pas à démarrer, activer la journalisation (journalisation est *hors* par défaut) pour obtenir plus d’informations.

Pour activer la journalisation :

1. Ouvrez le fichier de configuration (General.config) pour le modifier à l’aide d’un éditeur de fichier standard.

1. Modifiez les valeurs suivantes :

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Nous recommandons l’activation de la journalisation **hors** après la résolution des problèmes sont terminée. En laissant journalisation **sur** augmente connecter à l’utilisation des données et la taille de fichier. 

1. Redémarrez l’agent en exécutant les PowerShell ou la ligne de commande suivants :

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

1. Passez en revue le fichier journal pour plus d’informations sur l’échec.

   Emplacement du fichier journal : `%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>Étapes suivantes
- Lire l’ASC pour le service IoT [vue d’ensemble](overview.md)
- En savoir plus sur ASC pour IoT [Architecture](architecture.md)
- Activer la [service](quickstart-onboard-iot-hub.md)
- Lire le [Forum aux questions](resources-frequently-asked-questions.md)
- Comprendre [alertes](concept-security-alerts.md)