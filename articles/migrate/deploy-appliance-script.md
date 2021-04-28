---
title: Configurer une appliance Azure Migrate avec un script
description: Découvrez comment configurer une appliance Azure Migrate avec un script
ms.topic: how-to
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: ff05a01ad8173923ff614657d0231f743f38ba1c
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714755"
---
# <a name="set-up-an-appliance-with-a-script"></a>Configurer une appliance avec un script

Suivez cet article pour créer une [appliance Azure Migrate](./migrate-appliance-architecture.md) à des fins d'évaluation ou de migration de serveurs sur VMware et sur Hyper-V. Vous devez exécuter un script pour créer une appliance et vérifier qu'elle peut se connecter à Azure. 

Vous pouvez déployer l’appliance pour des serveurs sur VMware et Hyper-V à l’aide d’un script ou d'un modèle que vous téléchargez à partir du portail Azure. Un script s'avère utile si vous ne parvenez pas à créer une appliance à l’aide du modèle téléchargé.

- Pour utiliser un modèle, suivez les tutoriels pour [VMware](./tutorial-discover-vmware.md) ou [Hyper-V](./tutorial-discover-hyper-v.md).
- Pour configurer une appliance pour les serveurs physiques, seul un script peut être utilisé. Suivez [cet article](how-to-set-up-appliance-physical.md).
- Pour configurer une appliance dans le cloud Azure Government, consultez [cet article](deploy-appliance-script-government.md).

## <a name="prerequisites"></a>Prérequis

Le script configure l’appliance Azure Migrate sur un serveur existant.

- Le serveur qui fera office d’appliance doit remplir les conditions suivantes en matière de matériel et de système d’exploitation :

Scénario | Spécifications
--- | ---
VMware | Windows Server 2016 avec 32 Go de mémoire, huit processeurs virtuels, environ 80 Go de stockage sur disque
Hyper-V | Windows Server 2016 avec 16 Go de mémoire, huit processeurs virtuels, environ 80 Go de stockage sur disque

- Le serveur a également besoin d’un commutateur virtuel externe. Il nécessite une adresse IP statique ou dynamique. 
- Avant de déployer l’appliance, examinez en détail la configuration requise d’appliance pour les [serveurs sur VMware](migrate-appliance.md#appliance---vmware) et [sur Hyper-V](migrate-appliance.md#appliance---hyper-v).
- N’exécutez pas le script sur une appliance de Azure Migrate existante.

## <a name="set-up-the-appliance-for-vmware"></a>Configurer l'appliance pour VMware

Pour configurer l'appliance pour VMware, téléchargez le fichier compressé AzureMigrateInstaller-Server-Public.zip à partir du portail ou de [cette page](https://go.microsoft.com/fwlink/?linkid=2140334), puis extrayez son contenu. Exécutez le script PowerShell pour lancer l'application web de l'appliance. Installez l'appliance et configurez-la pour la première fois. Inscrivez ensuite l'appliance auprès du projet.

### <a name="verify-file-security"></a>Vérifier la sécurité du fichier

Vérifiez que le fichier compressé est sécurisé avant de le déployer.

1. Sur le serveur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier compressé
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple : ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-Public.zip SHA256```
3. Vérifiez la dernière version de l’appliance et le script du cloud public Azure :

    **Algorithme** | **Télécharger** | **SHA256**
    --- | --- | ---
    VMware (85,8 Mo) | [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2116601) | 85b74d93dfcee43412386141808d82147916330e6669df94c7969fe1b3d0fe72

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

1. Extrayez le fichier compressé dans un dossier sur le serveur qui hébergera l’appliance. Veillez à ne pas exécuter le script sur une appliance Azure Migrate existante.
2. Lancez PowerShell sur le serveur avec des privilèges (élevés) d’administrateur.
3. Modifiez le répertoire PowerShell en sélectionnant le dossier contenant l’extraction du fichier zip téléchargé.
4. Exécutez le script **AzureMigrateInstaller.ps1** comme suit :

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario VMware ```
  
5. Une fois correctement exécuté, le script lance l’application web de l’appliance afin que vous puissiez configurer celle-ci. En cas de problèmes, consultez les journaux du script sous C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Vérifier l’accès

Vérifiez que l’appliance peut se connecter aux URL Azure pour le cloud [public](migrate-appliance.md#public-cloud-urls).

## <a name="set-up-the-appliance-for-hyper-v"></a>Configurer l'appliance pour Hyper-V

Pour configurer l'appliance pour Hyper-V, téléchargez le fichier compressé AzureMigrateInstaller-Server-Public.zip à partir du portail ou de [cette page](https://go.microsoft.com/fwlink/?linkid=2105112), puis extrayez son contenu. Exécutez le script PowerShell pour lancer l'application web de l'appliance. Installez l'appliance et configurez-la pour la première fois. Inscrivez ensuite l'appliance auprès du projet.


### <a name="verify-file-security"></a>Vérifier la sécurité du fichier

Vérifiez que le fichier compressé est sécurisé avant de le déployer.

1. Sur le serveur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier compressé
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple : ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-HyperV.zip SHA256```

3. Vérifiez la dernière version de l’appliance et le script du cloud public Azure :

    **Scénario** | **Télécharger** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 Mo) | [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2116657) |  9bbef62e2e22481eda4b77c7fdf05db98c3767c20f0a873114fb0dcfa6ed682a

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

1. Extrayez le fichier compressé dans un dossier sur le serveur qui hébergera l’appliance. Veillez à ne pas exécuter le script sur une appliance Azure Migrate existante.
2. Lancez PowerShell sur le serveur avec des privilèges (élevés) d’administrateur.
3. Modifiez le répertoire PowerShell en sélectionnant le dossier contenant l’extraction du fichier zip téléchargé.
4. Exécutez le script **AzureMigrateInstaller.ps1** comme suit :

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. Une fois correctement exécuté, le script lance l’application web de l’appliance afin que vous puissiez configurer celle-ci. En cas de problèmes, consultez les journaux du script sous C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Vérifier l’accès

Vérifiez que l’appliance peut se connecter aux URL Azure pour le cloud [public](migrate-appliance.md#public-cloud-urls).

## <a name="next-steps"></a>Étapes suivantes

Après avoir déployé l'appliance, vous devez la configurer pour la première fois et l'inscrire auprès du projet.

- Configurez l’appliance pour [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance).
- Configurez l’appliance pour [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).