---
title: Configurer une appliance Azure Migrate avec un script
description: Découvrez comment configurer une appliance Azure Migrate avec un script
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: bf8d7148f685d4ac6a5f33603020a0503b0c62e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337367"
---
# <a name="set-up-an-appliance-with-a-script"></a>Configurer une appliance avec un script

Cet article explique comment configurer l’[appliance Azure Migrate](deploy-appliance.md) à l’aide d’un script d’installation PowerShell.

Le script offre les avantages suivants :
- Une alternative à la configuration de l’appliance à l’aide d’un modèle OVA, pour l’évaluation et la migration sans agent des machines virtuelles VMware.
- Une alternative à la configuration de l’appliance à l’aide d’un modèle VHD, pour l’évaluation et la migration des machines virtuelles Hyper-V.
- Pour l’évaluation de serveurs physiques (ou de machines virtuelles que vous souhaitez migrer en tant que serveurs physiques), le script est la seule méthode de configuration de l’appliance.

## <a name="prerequisites"></a>Conditions préalables

Le script configure l’appliance Azure Migrate sur une machine physique ou virtuelle existante.

- La machine faisant office d’appliance doit exécuter Windows Server 2016, avec 32 Go de mémoire, huit processeurs virtuels, environ 80 Go de stockage sur disque et un commutateur virtuel externe. Elle a besoin d’une adresse IP statique ou dynamique et d’un accès à Internet.
- Avant de déployer l’appliance, examinez en détail la configuration requise pour les [machines virtuelles VMware](migrate-appliance.md#appliance---vmware), les [machines virtuelles Hyper-V](migrate-appliance.md#appliance---hyper-v) et les [serveurs physiques](migrate-appliance.md#appliance---physical).
- N’exécutez pas le script sur une appliance de Azure Migrate existante.


## <a name="download-the-script"></a>Télécharger le script

1. Localisez la machine physique/virtuelle qui fera office d’appliance Azure Migrate.
2. Sur la machine, procédez comme suit :

    - Pour utiliser l’appliance avec des machines virtuelles VMware ou Hyper-V, [téléchargez](https://go.microsoft.com/fwlink/?linkid=2105112) le dossier zippé contenant le script d’installation et les MSI.
    - Pour utiliser l’appliance avec des serveurs physiques, téléchargez le script à partir du portail Azure Migrate, comme décrit dans ce [tutoriel](tutorial-assess-physical.md#set-up-the-appliance).

## <a name="verify-file-security"></a>Vérifier la sécurité du fichier

Vérifiez que le fichier compressé est sécurisé avant de le déployer.

1. Sur l’ordinateur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier compressé
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple d’utilisation : ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. Vérifiez que les valeurs de hachage générées correspondent à ces paramètres (pour la dernière version de l’appliance) :

    **Algorithme** | **Valeur de hachage**
      --- | ---
      MD5 | 1e92ede3e87c03bd148e56a708cdd33f
      SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

## <a name="run-the-script"></a>Exécuter le script

Le script effectue les opérations suivantes :

- Installe les agents et une application web.
- Installe des rôles Windows, à savoir le service d’activation Windows (WAS), IIS et PowerShell ISE.
- Télécharge et installe un module réinscriptible IIS. [Apprenez-en davantage](https://www.microsoft.com/download/details.aspx?id=7435).
- Met à jour une clé de Registre (HKLM) avec des paramètres persistants pour Azure Migrate.
- Crée les fichiers journaux et de configuration comme suit :
    - **Fichiers config** : %ProgramData%\Microsoft Azure\Config
    - **Fichiers journaux** : %ProgramData%\Microsoft Azure\Logs

Pour exécuter le script :

1. Extrayez le fichier zip dans un dossier sur la machine qui doit héberger l’appliance.
2. Lancez PowerShell sur la machine avec un privilège administrateur (élevé).
3. Modifiez le répertoire PowerShell en sélectionnant le dossier contenant l’extraction du fichier zip téléchargé.
4. Exécutez le script **AzureMigrateInstaller.ps1** comme suit :

    - Pour VMware : 
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware
        ```
    - Pour Hyper-V :
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv
        ```
    - Pour les serveurs physiques :
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
        ```      
5. Une fois correctement exécuté, le script lance l’application web de l’appliance afin que vous puissiez configurer celle-ci. En cas de problèmes, vous pouvez consulter les journaux du script dans C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>timestamp</em>.log.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez configuré l’appliance à l’aide du script, suivez les instructions suivantes :

- Configurez l’appliance pour [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Configurez l’appliance pour [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Configurez l’appliance pour les [serveurs physiques](how-to-set-up-appliance-physical.md).