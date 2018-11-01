---
title: Diagnostics dans Azure Stack
description: Comment collecter des fichiers journaux de diagnostics dans Azure Stack
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 10/31/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: 3dd3e3391cc2536f56a5e42610c09c85b4068234
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50740551"
---
# <a name="azure-stack-diagnostics-tools"></a>Outils de diagnostics Azure Stack

Azure Stack est une grande collection de composants qui fonctionnent ensemble et interagissent. Tous ces composants génèrent leurs propres journaux. Cela peut compliquer le diagnostic des problèmes, notamment quand les erreurs proviennent de plusieurs composants Azure Stack en interaction. 

Nos outils de diagnostic aident à garantir la simplicité d’utilisation et l’efficacité du mécanisme de collection de journaux. Le diagramme suivant illustre le fonctionnement des outils de collecte de journaux Azure Stack :

![Outils de diagnostic Azure Stack](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>Collecteur de traces
 
Le collecteur de traces est activé par défaut et s’exécute en continu en arrière-plan pour collecter tous les journaux de suivi d’événements pour Windows (ETW) à partir des services composants d’Azure Stack. Les journaux ETW sont stockés dans un partage local commun avec une durée de vie de cinq jours. Une fois cette limite atteinte, les fichiers les plus anciens sont supprimés à mesure que de nouveaux sont créés. La taille maximale autorisée par défaut pour chaque fichier est de 200 Mo. Une vérification de la taille a lieu toutes les deux minutes ; si le fichier actuel a une taille supérieure ou égale à 200 Mo, il est enregistré et un nouveau fichier est généré. Il existe également une limite de 8 Go portant sur la taille totale des fichiers générés par session d’événements. 

## <a name="log-collection-tool"></a>Outil de collecte de journaux
 
Vous pouvez utiliser l’applet de commande PowerShell **Get-AzureStackLog** pour collecter des journaux à partir de tous les composants dans un environnement Azure Stack. Elle les enregistre dans des fichiers zip à un emplacement défini par l’utilisateur. Si l’équipe de support technique Azure Stack a besoin de vos journaux pour résoudre un problème, elle peut vous demander d’exécuter cet outil.

> [!CAUTION]
> Ces fichiers journaux peuvent contenir des informations d’identification personnelle. Pensez-y avant de publier des fichiers journaux publiquement.
 
Voici quelques exemples de types de journaux collectés :
*   **Journaux de déploiement Azure Stack**
*   **Journaux des événements Windows**
*   **Journaux Panther**
*   **Journaux de cluster**
*   **Journaux de diagnostics de stockage**
*   **Journaux ETW**

Ces fichiers sont collectés et enregistrés dans un partage par le Collecteur de traces. Vous pouvez ensuite utiliser l’applet de commande PowerShell **Get-AzureStackLog** pour les collecter en cas de nécessité.

### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems"></a>Pour exécuter Get-AzureStackLog sur des systèmes intégrés Azure Stack 
Pour exécuter l’outil de collection de journaux sur un système intégré, vous devez avoir accès au point de terminaison privilégié (PEP). Voici un exemple de script que vous pouvez exécuter à l’aide du point de terminaison privilégié pour collecter des journaux sur un système intégré :

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

- Les paramètres **OutputSharePath** et **OutputShareCredential** sont utilisés pour charger des journaux dans un dossier partagé externe.
- Comme indiqué dans l’exemple précédent, vous pouvez utiliser les paramètres **FromDate** et **ToDate** pour collecter des journaux pour une période donnée. Cela peut être pratique pour les scénarios, tels que la collection de journaux après l’application d’un package de mise à jour sur un système intégré.


 
### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Pour exécuter Get-AzureStackLog sur un système ASDK (Kit de développement Azure Stack)
1. Connectez-vous en tant qu’**AzureStack\CloudAdmin** sur l’hôte.
2. Ouvrez une fenêtre PowerShell en tant qu’administrateur.
3. Exécutez l’applet de commande PowerShell **Get-AzureStackLog**.

**Exemples :**

  Collecter tous les journaux pour tous les rôles :

  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred
  ```

  Collecter les journaux à partir des rôles VirtualMachines et BareMetal :

  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal
  ```

  Collecter les journaux à partir des rôles VirtualMachines et BareMetal, avec un filtrage de date pour les fichiers journaux pour les huit dernières heures :
    
  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

  Collecter les journaux à partir des rôles VirtualMachines et BareMetal, avec un filtrage de date pour les fichiers journaux des 2 à 8 dernières heures :

  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>Considérations relatives aux paramètres du kit ASDK et des systèmes intégrés

- Si vous ne spécifiez pas les paramètres **FromDate** et **ToDate**, par défaut les journaux sont collectés pour les quatre dernières heures.
- Utilisez le paramètre **FilterByNode** pour filtrer les journaux par nom d’ordinateur. Par exemple : 
```powershell
Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred ` -FilterByNode azs-xrp01
```
- Utilisez le paramètre **FilterByLogType** pour filtrer les journaux par type. Vous pouvez choisir de filtrer par fichier, partage ou événement Windows. Par exemple : 
```powershell
Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred ` -FilterByLogType File
```
- Vous pouvez utiliser le paramètre **TimeOutInMinutes** pour définir le délai d’expiration pour la collecte des journaux. Il est défini sur 150 (2,5 heures) par défaut.
- Dans les versions 1805 et ultérieures, la collecte de journaux de fichiers de vidage est désactivée par défaut. Pour l’activer, utilisez le paramètre booléen **IncludeDumpFile**. 
- Actuellement, vous pouvez utiliser le paramètre **FilterByRole** pour filtrer la collecte de journaux en fonction des rôles suivants :

 |   |   |   |    |
 | - | - | - | -  |   
 |ACS|Calcul|InfraServiceController|QueryServiceCoordinator|
 |ACSBlob|IPC|Infrastructure|QueryServiceWorker|
 |ACSDownloadService|CRP|KeyVaultAdminResourceProvider|SeedRing|
 |ACSFabric|DatacenterIntegration|KeyVaultControlPlane|SeedRingServices|
 |ACSFrontEnd|DeploymentMachine|KeyVaultDataPlane|SLB|
 |ACSMetrics|DiskRP|KeyVaultInternalControlPlane|SlbVips|
 |ACSMigrationService|Domaine|KeyVaultInternalDataPlane|SQL|
 |ACSMonitoringService|ECE|KeyVaultNamingService|SRP|
 |ACSSettingsService|EventAdminRP|MDM|Stockage|
 |ACSTableMaster|EventRP|MetricsAdminRP|StorageAccounts|
 |ACSTableServer|ExternalDNS|MetricsRP|StorageController|
 |ACSWac|Structure|MetricsServer|Locataire|
 |ADFS|FabricRing|MetricsStoreService|TraceCollector|
 |ApplicationController|FabricRingServices|MonAdminRP|URP|
 |ASAppGateway|FirstTierAggregationService|MonitoringAgent|Usage|
 |AzureBridge|FRP|MonRP|UsageBridge|
 |AzureMonitor|Galerie|NC|VirtualMachines|
 |AzureStackBitlocker|Passerelle|Réseau|WAS|
 |BareMetal|HealthMonitoring|NonPrivilegedAppGateway|WASBootstrap|
 |BRP|HintingServiceV2|NRP|WASPUBLIC|
 |CA|HRP|OboService|WindowsDefender|
 |CacheService|IBC|OEM|     |
 |Cloud|IdentityProvider|OnboardRP|     |   
 |Cluster|iDns|PXE|     |
 |   |   |   |    |

### <a name="additional-considerations"></a>Considérations supplémentaires

* L’exécution de cette commande peut prendre un certain temps, en fonction des données du ou des rôles collectées par les journaux. Les facteurs qui entrent en compte sont la durée spécifiée pour la collecte de journaux et le nombre de nœuds de l’environnement Azure Stack.
* Une fois la collecte de journaux en cours, vérifiez le dossier créé dans le paramètre **OutputSharePath** spécifié dans la commande.
* Les journaux de chaque rôle se trouvent à l’intérieur de fichiers zip individuels. Selon leur taille, les journaux d’un rôle collectés peuvent être séparés en plusieurs fichiers zip. Pour ce type de rôle, si vous souhaitez disposer de tous les fichiers journaux décompressés dans un dossier unique, utilisez un outil qui peut effectuer cette opération en blocs (7zip, par exemple). Sélectionnez tous les fichiers compressés du rôle, puis sélectionnez **Extract Here**. Cette opération permet de décompresser tous les fichiers journaux de ce rôle, au sein d’un dossier fusionné unique.
* Un fichier nommé **Get-AzureStackLog_Output.log** est également créé dans le dossier qui contient les fichiers journaux compressés. Ce fichier est un journal de la sortie de la commande, qui peut être utilisé pour résoudre des problèmes lors de la collection de journaux. Parfois, le fichier journal inclut des entrées `PS>TerminatingError` qui peuvent être ignorées, sauf si des fichiers journaux attendus sont manquants après l’exécution de la collecte des journaux.
* Pour examiner un échec spécifique, vous aurez peut-être besoin des journaux de plusieurs composants.
    -   Les journaux système et des événements pour toutes les machines virtuelles de l’infrastructure sont collectés dans le rôle *VirtualMachines*.
    -   Les journaux système et des événements pour tous les hôtes sont collectés dans le rôle *BareMetal*.
    -   Les journaux des événements de cluster de basculement et Hyper-V sont collectés dans le rôle *Storage*.
    -   Les journaux ACS sont collectés dans les rôles *Storage* et *ACS*.

> [!NOTE]
> Nous appliquons des limites de taille et d’âge aux journaux collectés, car il est essentiel de garantir une utilisation efficace de votre espace de stockage afin de s’assurer que vous ne vous retrouverez pas submergé de journaux. Toutefois, lorsque vous diagnostiquez un problème, vous avez parfois besoin de journaux qui n’existent plus, à cause de ces limites. Par conséquent, nous vous **recommandons vivement** de décharger vos journaux vers un espace de stockage externe (un compte de stockage dans Azure, un dispositif de stockage local supplémentaire, etc.) toutes les 8 à 12 heures, et de les conserver pendant 1 à 3 mois, en fonction de vos besoins. Vérifiez également que l’emplacement de stockage est chiffré.

## <a name="next-steps"></a>Étapes suivantes
[Dépannage de Microsoft Azure Stack](azure-stack-troubleshooting.md)

