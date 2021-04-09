---
title: Configurer une appliance Azure Migrate dans Azure Government
description: Apprenez à configurer une appliance Azure Migrate dans Azure Government
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: c4ca8d8ac24ac174158957e44b5eabe4a89a5340
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775202"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Configurer une appliance dans Azure Government 

Suivez cet article afin de déployer une [appliance Azure Migrate](./migrate-appliance-architecture.md) pour les serveurs en environnement VMware, les serveurs sur Hyper-V et les serveurs physiques dans un cloud Azure Government. Vous devez exécuter un script pour créer l'appliance et vérifier qu'elle peut se connecter à Azure. Si vous souhaitez configurer une appliance dans le cloud public, suivez [cet article](deploy-appliance-script.md).


> [!NOTE]
> L’option de déploiement d’une appliance à l’aide d’un modèle (pour les serveurs en environnement VMware et Hyper-V) n’est pas prise en charge dans Azure Government.


## <a name="prerequisites"></a>Prérequis

Le script configure l’appliance Azure Migrate serveur physique ou sur un serveur virtualisé.

- Le serveur faisant office d’appliance doit exécuter Windows Server 2016, avec 32 Go de mémoire, 8 processeurs virtuels, environ 80 Go de stockage sur disque et 1 commutateur virtuel externe. Elle a besoin d’une adresse IP statique ou dynamique et d’un accès à Internet.
- Avant de déployer l’appliance, examinez en détail la configuration d’appliance requise pour les [serveurs sur VMware](migrate-appliance.md#appliance---vmware), [sur Hyper-V](migrate-appliance.md#appliance---hyper-v) et pour les [serveurs physiques](migrate-appliance.md#appliance---physical).
- N’exécutez pas le script sur une appliance de Azure Migrate existante.

## <a name="set-up-the-appliance-for-vmware"></a>Configurer l'appliance pour VMware

Afin de configurer l'appliance pour VMware, téléchargez un fichier zip à partir du portail Azure et extrayez son contenu. Exécutez le script PowerShell pour lancer l'application web de l'appliance. Installez l'appliance et configurez-la pour la première fois. Inscrivez ensuite l’appliance auprès du projet.

### <a name="download-the-script"></a>Télécharger le script

1. Dans **Objectifs de migration** > **Windows, Linux et SQL Server** > **Azure Migrate : Découverte et évaluation**, cliquez sur **Découvrir**.
2. Dans **Découvrir les serveurs** > **Vos serveurs sont-ils virtualisés ?** , sélectionnez **Oui, avec l’hyperviseur VMware vSphere**.
3. Cliquez sur **Télécharger** pour télécharger le fichier zip.

### <a name="verify-file-security"></a>Vérifier la sécurité du fichier

Vérifiez que le fichier compressé est sécurisé avant de le déployer.

1. Sur le serveur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier compressé
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple : ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. Vérifiez la dernière version de l’appliance et les valeurs de hachage :

    **Algorithme** | **Télécharger** | **SHA256**
    --- | --- | ---
    VMware (85,8 Mo) | [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca


### <a name="run-the-script"></a>Exécuter le script

Le script effectue les opérations suivantes :

- Installe les agents et une application web.
- Installe des rôles Windows, à savoir le service d’activation Windows (WAS), IIS et PowerShell ISE.
- Télécharge et installe un module réinscriptible IIS. [Plus d’informations](https://www.microsoft.com/download/details.aspx?id=7435)
- Met à jour une clé de Registre (HKLM) avec des paramètres persistants pour Azure Migrate.
- Crée les fichiers journaux et de configuration comme suit :
    - **Fichiers config** : %ProgramData%\Microsoft Azure\Config
    - **Fichiers journaux** : %ProgramData%\Microsoft Azure\Logs

Pour exécuter le script :

1. Extrayez le fichier compressé dans un dossier sur le serveur qui hébergera l’appliance. Veillez à ne pas exécuter le script sur un serveur disposant d’une appliance Azure Migrate.
2. Lancez PowerShell sur le serveur avec des privilèges (élevés) d’administrateur.
3. Modifiez le répertoire PowerShell en sélectionnant le dossier contenant l’extraction du fichier zip téléchargé.
4. Exécutez le script **AzureMigrateInstaller.ps1** comme suit :
    
    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>.\AzureMigrateInstaller.ps1 ```
1. Une fois correctement exécuté, le script lance l’application web de l’appliance afin que vous puissiez configurer celle-ci. En cas de problèmes, consultez les journaux du script sous C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Vérifier l'accès

Vérifiez que l'appliance peut se connecter aux URL Azure des [clouds du secteur public](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-hyper-v"></a>Configurer l'appliance pour Hyper-V

Afin de configurer l'appliance pour Hyper-V, téléchargez un fichier zip à partir du portail Azure et extrayez son contenu. Exécutez le script PowerShell pour lancer l'application web de l'appliance. Installez l'appliance et configurez-la pour la première fois. Inscrivez ensuite l’appliance auprès du projet.

### <a name="download-the-script"></a>Télécharger le script

1.  Dans **Objectifs de migration** > **Windows, Linux et SQL Server** > **Azure Migrate : Découverte et évaluation**, cliquez sur **Découvrir**.
2.  Dans **Découvrir les serveurs** > **Vos serveurs sont-ils virtualisés ?** , sélectionnez **Oui, avec Hyper-V**.
3.  Cliquez sur **Télécharger** pour télécharger le fichier zip. 


### <a name="verify-file-security"></a>Vérifier la sécurité du fichier

Vérifiez que le fichier compressé est sécurisé avant de le déployer.

1. Sur le serveur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier compressé
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple : ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. Vérifiez la dernière version de l’appliance et la valeur de hachage :

    **Scénario** | **Télécharger** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 Mo) | [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2140424) |  db5311de3d1d4a1167183a94e8347456db9c5749c7332ff2eb4b777798765e48

          

### <a name="run-the-script"></a>Exécuter le script

Le script effectue les opérations suivantes :

- Installe les agents et une application web.
- Installe des rôles Windows, à savoir le service d’activation Windows (WAS), IIS et PowerShell ISE.
- Télécharge et installe un module réinscriptible IIS. [Plus d’informations](https://www.microsoft.com/download/details.aspx?id=7435)
- Met à jour une clé de Registre (HKLM) avec des paramètres persistants pour Azure Migrate.
- Crée les fichiers journaux et de configuration comme suit :
    - **Fichiers config** : %ProgramData%\Microsoft Azure\Config
    - **Fichiers journaux** : %ProgramData%\Microsoft Azure\Logs

Pour exécuter le script :

1. Extrayez le fichier compressé dans un dossier sur le serveur qui hébergera l’appliance. Veillez à ne pas exécuter le script sur un serveur exécutant une appliance Azure Migrate.
2. Lancez PowerShell sur le serveur avec des privilèges (élevés) d’administrateur.
3. Modifiez le répertoire PowerShell en sélectionnant le dossier contenant l’extraction du fichier zip téléchargé.
4. Exécutez le script **AzureMigrateInstaller.ps1** comme suit : 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>.\AzureMigrateInstaller.ps1 ``` 
1. Une fois correctement exécuté, le script lance l’application web de l’appliance afin que vous puissiez configurer celle-ci. En cas de problèmes, consultez les journaux du script sous C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Vérifier l'accès

Vérifiez que l'appliance peut se connecter aux URL Azure des [clouds du secteur public](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-physical-servers"></a>Configurer l'appliance pour des serveurs physiques

Afin de configurer l'appliance pour VMware, téléchargez un fichier zip à partir du portail Azure et extrayez son contenu. Exécutez le script PowerShell pour lancer l'application web de l'appliance. Installez l'appliance et configurez-la pour la première fois. Inscrivez ensuite l’appliance auprès du projet.

### <a name="download-the-script"></a>Télécharger le script

1. Dans **Objectifs de migration** > **Windows, Linux et SQL Server** > **Azure Migrate : Découverte et évaluation**, cliquez sur **Découvrir**.
2. Dans **Découvrir les serveurs** > **Vos serveurs sont-ils virtualisés ?** , sélectionnez **Non virtualisé/Autre**.
3. Cliquez sur **Télécharger** pour télécharger le fichier zip.

### <a name="verify-file-security"></a>Vérifier la sécurité du fichier

Vérifiez que le fichier compressé est sécurisé avant de le déployer.

1. Sur les serveurs sur lesquels vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier compressé
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple : ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. Vérifiez la dernière version de l’appliance et la valeur de hachage :

    **Scénario** | **Téléchargement** _ | _ *Valeur de hachage**
    --- | --- | ---
    Physique (85 Mo) | [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2140338) | cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f
          

### <a name="run-the-script"></a>Exécuter le script

Le script effectue les opérations suivantes :

- Installe les agents et une application web.
- Installe des rôles Windows, à savoir le service d’activation Windows (WAS), IIS et PowerShell ISE.
- Télécharge et installe un module réinscriptible IIS. [Plus d’informations](https://www.microsoft.com/download/details.aspx?id=7435)
- Met à jour une clé de Registre (HKLM) avec des paramètres persistants pour Azure Migrate.
- Crée les fichiers journaux et de configuration comme suit :
    - **Fichiers config** : %ProgramData%\Microsoft Azure\Config
    - **Fichiers journaux** : %ProgramData%\Microsoft Azure\Logs

Pour exécuter le script :

1. Extrayez le fichier compressé dans un dossier sur le serveur qui hébergera l’appliance. Veillez à ne pas exécuter le script sur un serveur exécutant une appliance Azure Migrate.
2. Lancez PowerShell sur le serveur avec des privilèges (élevés) d’administrateur.
3. Modifiez le répertoire PowerShell en sélectionnant le dossier contenant l’extraction du fichier zip téléchargé.
4. Exécutez le script **AzureMigrateInstaller.ps1** comme suit :

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```
1. Une fois correctement exécuté, le script lance l’application web de l’appliance afin que vous puissiez configurer celle-ci. En cas de problèmes, consultez les journaux du script sous C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Vérifier l'accès

Vérifiez que l'appliance peut se connecter aux URL Azure des [clouds du secteur public](migrate-appliance.md#government-cloud-urls).

## <a name="next-steps"></a>Étapes suivantes

Après avoir déployé l’appliance, vous devez la configurer pour la première fois et l’inscrire auprès du projet.

- Configurez l’appliance pour [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance).
- Configurez l’appliance pour [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Configurez l’appliance pour les [serveurs physiques](how-to-set-up-appliance-physical.md).
