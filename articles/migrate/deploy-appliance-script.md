---
title: Configurer une appliance Azure Migrate avec un script
description: Découvrez comment configurer une appliance Azure Migrate avec un script
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 0c4d85909bbfa623b5ad8590e973250474d9d95a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676311"
---
# <a name="set-up-an-appliance-with-a-script"></a>Configurer une appliance avec un script

Suivez cet article pour créer une [appliance Azure Migrate](deploy-appliance.md) à des fins d'évaluation/de migration des machines virtuelles VMware et Hyper-V. Vous devez exécuter un script pour créer une appliance et vérifier qu'elle peut se connecter à Azure. 

Vous pouvez déployer l’appliance pour des machines virtuelles VMware et Hyper-V à l’aide d’un script ou d'un modèle que vous téléchargez à partir du portail Azure. Un script s'avère particulièrement utile si vous ne parvenez pas à créer de machine virtuelle à l’aide du modèle téléchargé.

- Pour utiliser un modèle, suivez les tutoriels pour [VMware](tutorial-prepare-vmware.md) ou [Hyper-V](tutorial-prepare-hyper-v.md).
- Pour configurer une appliance pour les serveurs physiques, seul un script peut être utilisé. Suivez [cet article](how-to-set-up-appliance-physical.md).
- Pour configurer une appliance dans le cloud Azure Government, consultez [cet article](deploy-appliance-script-government.md).

## <a name="prerequisites"></a>Prérequis

Le script configure l’appliance Azure Migrate sur une machine physique ou virtuelle existante.

- La machine faisant office d’appliance doit exécuter Windows Server 2016, avec 32 Go de mémoire, huit processeurs virtuels, environ 80 Go de stockage sur disque et un commutateur virtuel externe. Elle a besoin d’une adresse IP statique ou dynamique et d’un accès à Internet.
- Avant de déployer l’appliance, examinez en détail la configuration requise pour les [machines virtuelles VMware](migrate-appliance.md#appliance---vmware), les [machines virtuelles Hyper-V](migrate-appliance.md#appliance---hyper-v) et les [serveurs physiques](migrate-appliance.md#appliance---physical).
- N’exécutez pas le script sur une appliance de Azure Migrate existante.

## <a name="set-up-the-appliance-for-vmware"></a>Configurer l’appliance pour VMware

Afin de configurer l'appliance pour VMware, téléchargez un fichier zip à partir du portail Azure et extrayez son contenu. Exécutez le script PowerShell pour lancer l'application web de l'appliance. Installez l'appliance et configurez-la pour la première fois. Inscrivez ensuite l'appliance auprès du projet Azure Migrate.

### <a name="download-the-script"></a>Télécharger le script

1.  Dans **Objectifs de migration** > **Serveurs** > **Azure Migrate : Server Assessment**, cliquez sur **Découvrir**.
2.  Dans **Découvrir des machines** > **Vos machines sont-elles virtualisées ?** , sélectionnez **Oui, avec l’hyperviseur vSphere VMWare**.
3.  Cliquez sur **Télécharger** pour télécharger le fichier compressé. 


### <a name="verify-file-security"></a>Vérifier la sécurité du fichier

Vérifiez que le fichier compressé est sécurisé avant de le déployer.

1. Sur l’ordinateur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier compressé
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple : ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
3. Vérifiez la valeur de hachage générée. Pour la dernière version de l'appliance :

    **Algorithme** | **Valeur de hachage**
    --- | ---
    MD5 | 1e92ede3e87c03bd148e56a708cdd33f
    SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c



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

1. Extrayez le fichier zip dans un dossier sur la machine qui doit héberger l’appliance. Veillez à ne pas exécuter le script sur une machine d’une appliance Azure Migrate existante.
2. Lancez PowerShell sur la machine avec un privilège administrateur (élevé).
3. Modifiez le répertoire PowerShell en sélectionnant le dossier contenant l’extraction du fichier zip téléchargé.
4. Exécutez le script **AzureMigrateInstaller.ps1** comme suit :

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware ```
   
5. Une fois correctement exécuté, le script lance l’application web de l’appliance afin que vous puissiez configurer celle-ci. En cas de problèmes, consultez les journaux du script dans C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Vérifier l’accès

Vérifiez que l’appliance peut se connecter aux URL Azure pour le cloud [public](migrate-appliance.md#public-cloud-urls).

## <a name="set-up-the-appliance-for-hyper-v"></a>Configurez l’appliance pour Hyper-V.

Afin de configurer l'appliance pour Hyper-V, téléchargez un fichier zip à partir du portail Azure et extrayez son contenu. Exécutez le script PowerShell pour lancer l'application web de l'appliance. Installez l'appliance et configurez-la pour la première fois. Inscrivez ensuite l'appliance auprès du projet Azure Migrate.

### <a name="download-the-script"></a>Télécharger le script

1.  Dans **Objectifs de migration** > **Serveurs** > **Azure Migrate : Server Assessment**, cliquez sur **Découvrir**.
2.  Dans **Découvrir des machines** > **Vos machines sont-elles virtualisées ?** , sélectionnez **Oui, avec Hyper-V**.
3.  Cliquez sur **Télécharger** pour télécharger le fichier compressé. 


### <a name="verify-file-security"></a>Vérifier la sécurité du fichier

Vérifiez que le fichier compressé est sécurisé avant de le déployer.

1. Sur l’ordinateur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier compressé
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple : ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. Vérifiez les valeurs de hachage générées. Pour la dernière version de l'appliance :

    **Algorithme** | **Valeur de hachage**
    --- | ---
    MD5 | 1e92ede3e87c03bd148e56a708cdd33f
    SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

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

1. Extrayez le fichier zip dans un dossier sur la machine qui doit héberger l’appliance. Veillez à ne pas exécuter le script sur une machine d’une appliance Azure Migrate existante.
2. Lancez PowerShell sur la machine avec un privilège administrateur (élevé).
3. Modifiez le répertoire PowerShell en sélectionnant le dossier contenant l’extraction du fichier zip téléchargé.
4. Exécutez le script **AzureMigrateInstaller.ps1** comme suit : ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. Une fois correctement exécuté, le script lance l’application web de l’appliance afin que vous puissiez configurer celle-ci. En cas de problèmes, consultez les journaux du script dans C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Vérifier l’accès

Vérifiez que l’appliance peut se connecter aux URL Azure pour le cloud [public](migrate-appliance.md#public-cloud-urls).

## <a name="next-steps"></a>Étapes suivantes

Après avoir déployé l'appliance, vous devez la configurer pour la première fois et l'inscrire auprès du projet Azure Migrate.

- Configurez l’appliance pour [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Configurez l’appliance pour [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
