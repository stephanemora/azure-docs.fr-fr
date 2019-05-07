---
title: Instructions détaillées de Azure Status Monitor v2 | Microsoft Docs
description: Obtenir des instructions détaillées pour la mise en route avec Status Monitor v2. Surveiller les performances de site Web sans avoir à redéployer le site Web. Fonctionne avec les applications web ASP.NET hébergées localement, dans des machines virtuelles ou sur Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 3aca64c7b0f1ad04967782cb3349da302db557a0
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145084"
---
# <a name="status-monitor-v2-detailed-instructions"></a>État du moniteur v2 des instructions détaillées.

Ce document détails comment intégrer à PowerShell Gallery et télécharger le ApplicationMonitor Module. Nous avons documenté les paramètres les plus courants requis pour la prise en main.
Nous avons également inclus les instructions du manuel dans le cas où l’accès à internet n’est pas disponible.

> [!IMPORTANT]
> État du moniteur v2 est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [conditions d’utilisation supplémentaires pour les versions préliminaires de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="instrumentation-key"></a>Clé d’instrumentation

Pour commencer, vous devez disposer d’une clé d’instrumentation. Pour plus d’informations, consultez [créer une ressource Application Insights](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-administrator-with-an-elevated-execution-policy"></a>Exécutez PowerShell en tant qu’administrateur avec une stratégie d’exécution avec élévation de privilèges

**Exécuter en tant qu’administrateur**: 
- Description : PowerShell aurez besoin des autorisations de niveau administrateur pour apporter des modifications sur votre ordinateur.

**La stratégie d’exécution**:
- Description : Par défaut, les scripts PowerShell en cours d’exécution sera désactivé. Nous vous recommandons d’autoriser les scripts RemoteSigned pour l’étendue actuelle uniquement.
- Référence : [À propos des stratégies d’exécution](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) et [Set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
)
- Cmd : `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`
- Paramètres facultatifs :
    - `-Force` Il ignore l’invite de confirmation.

**Exemples d’erreurs :**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Prérequis pour PowerShell

Audit de votre version actuelle de PowerShell en exécutant la commande : `$PSVersionTable`.
La commande génère la sortie suivante :


```
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

Ces instructions ont été écrites et testées sur un ordinateur Windows 10 avec les versions répertoriées ci-dessus.

## <a name="prerequisites-for-powershell-gallery"></a>Conditions préalables pour PowerShell Gallery

Ces étapes seront préparer votre serveur pour télécharger les modules à partir de PowerShell Gallery.

> [!NOTE] 
> Prise en charge de PowerShell Gallery est inclus dans Windows 10, Windows Server 2016 et PowerShell 6. Pour les versions antérieures, passez en revue ce document : [L’installation de PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)


1. Avec une stratégie d’exécution avec élévation de privilèges, exécutez PowerShell en tant qu’administrateur.
2. Fournisseur de package NuGet 
    - Description : Ce fournisseur est nécessaire pour interagir avec les référentiels NuGet tels que PowerShell Gallery
    - Référence : [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6)
    - Cmd : `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`
    - Paramètres facultatifs :
        - `-Proxy` Spécifie un serveur proxy pour la demande.
        - `-Force` Il ignore l’invite de confirmation. 
    
    Vous recevez cette invite si NuGet n’est pas configuré :
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Référentiels fiables
    - Description : Par défaut, PowerShell Gallery est un référentiel non approuvé.
    - Référence : [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6)
    - Cmd : `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`
    - Paramètres facultatifs :
        - `-Proxy` Spécifie un serveur proxy pour la demande.

    Vous recevez cette invite si PowerShell Gallery n’est pas approuvé :

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    - Peut confirmer cette modification et d’audit de toutes les PSRepositories en exécutant la commande : `Get-PSRepository`

4. Version de PowerShellGet 
    - Description : Ce module contient les outils permettant d’obtenir d’autres modules à partir de PowerShell Gallery. V1.0.0.1 est livré avec Windows 10 et Windows Server. Version minimale requise est v1.6.0, étant. Pour auditer la version est installée, exécutez la commande : `Get-Command -Module PowerShellGet`
    - Référence : [L’installation de PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)
    - Cmd : `Install-Module -Name PowerShellGet`
    - Paramètres facultatifs :
        - `-Proxy` Spécifie un serveur proxy pour la demande.
        - `-Force` Il ignore l’avertissement « déjà installées » et installer la dernière version.

    Vous recevez cette erreur si vous n’utilisez pas de version la plus récente de PowerShellGet :
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Redémarrez PowerShell. Il n’est pas possible de charger la nouvelle version dans la session active. Les nouvelles sessions Powershell aura la dernière version de PowerShellGet chargé.

## <a name="download--install-via-powershell-gallery"></a>Télécharger et installer par le biais de PowerShell Gallery

Ces étapes télécharge le module Az.ApplicationMonitor à partir de PowerShell Gallery.

1. Conditions préalables pour PowerShell Gallery sont requis.
2. Avec une stratégie d’exécution avec élévation de privilèges, exécutez PowerShell en tant qu’administrateur.
3. Installer le Module Az.ApplicationMonitor
    - Référence : [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6)
    - Cmd : `Install-Module -Name Az.ApplicationMonitor`
    - Paramètres facultatifs :
        - `-Proxy` Spécifie un serveur proxy pour la demande.
        - `-AllowPrerelease` Cela permettra l’installation de versions alpha et bêta.
        - `-AcceptLicense` Il ignore l’invite « Accepter la licence »
        - `-Force` Il ignore l’avertissement « Référentiel non approuvé »

## <a name="download--install-manually-offline-option"></a>Télécharger et installer manuellement (option hors connexion)

Si pour une raison quelconque vous ne pouvez pas vous connecter au PowerShell Module, vous pouvez manuellement télécharger et installer le module Az.ApplicationMonitor.

### <a name="manually-download-the-latest-nupkg"></a>Télécharger manuellement le package NuGet plus récente

1. Accédez à : https://www.powershellgallery.com/packages/Az.ApplicationMonitor
2. Sélectionnez la version la plus récente à partir de l’historique des versions.
3. Recherchez les « Options d’Installation » et sélectionnez « Téléchargement manuel ».

### <a name="option-1-install-into-powershell-modules-directory"></a>Option 1 : installer dans le répertoire de modules PowerShell
Installer le PowerShell Module téléchargé manuellement dans un répertoire PowerShell afin de rendre détectable par les sessions PowerShell.
Pour plus d'informations, consultez les pages suivantes : [Installation d’un Module PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)


#### <a name="unzip-nupkg-as-zip-using-expand-archive-v1010"></a>Décompressez le package NuGet au format zip à l’aide de Expand-Archive (v1.0.1.0)

- Description : Version de base de Microsoft.PowerShell.Archive (v1.0.1.0) ne peut pas décompresser les fichiers de package NuGet. Renommez le fichier avec l’extension « .zip ».
- Référence : [Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)
- Cmd : 

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-using-expand-archive-v1100"></a>Décompressez le package NuGet à l’aide de Expand-Archive (v1.1.0.0).

- Description : Utilisez une version actuelle du Expand-Archive pour décompresser les nupkgs sans modification du nom de l’extension. 
- Référence : [Développez-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) et [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0)
- Cmd :

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-manually"></a>Option 2 : décompresser et importer manuellement
Installer le PowerShell Module téléchargé manuellement dans un répertoire PowerShell afin de rendre détectable par les sessions PowerShell.
Pour plus d'informations, consultez les pages suivantes : [Installation d’un Module PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)

Si vous installez dans n’importe quel autre annuaire, vous devez importer manuellement le module à l’aide [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6)

> [!IMPORTANT] 
> Installation installera les DLL par le biais de chemins d’accès relatifs. Store le contenu de ce package dans votre répertoire d’exécution prévue et confirmez que les autorisations d’accès autorisent en lecture mais pas d’écriture.

1. Remplacez l’extension « .zip » et extraire le contenu du package dans votre répertoire d’installation prévue.
2. Rechercher le chemin d’accès « Az.ApplicationMonitor.psd1 ».
3. Avec une stratégie d’exécution avec élévation de privilèges, exécutez PowerShell en tant qu’administrateur. 
4. Charger le module par le biais de la commande : `Import-Module Az.ApplicationMonitor.psd1`.
    

## <a name="proxy"></a>Proxy

Lorsque vous analysez un ordinateur sur votre intranet privé, il sera nécessaire acheminer le trafic http via un proxy.

Les commandes PowerShell pour télécharger et installer le Az.ApplicationMonitor à partir de la galerie PowerShell ne prennent pas en charge un `-Proxy` paramètre.
Passez en revue les instructions ci-dessus lorsque l’écriture de scripts d’installation.

Vous devez le SDK Application Insights envoyer des données de télémétrie de votre application à Microsoft. Nous recommandons la configuration des paramètres de proxy pour votre application dans votre fichier web.config. Consultez [Application Insights Forum aux questions : Relais proxy](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough) pour plus d’informations.


## <a name="enable-monitoring"></a>Activer la supervision 

Cmd : `Enable-ApplicationInsightsMonitoring`

Consultez notre [référence de l’API](status-monitor-v2-api-enable-monitoring.md) pour une description détaillée de l’utilisation de cette applet de commande. 



## <a name="next-steps"></a>Étapes suivantes

 Affichez vos données de télémétrie :

- [Explorez les mesures](../../azure-monitor/app/metrics-explorer.md) pour surveiller les performances et l’utilisation
- [Recherchez les événements et journaux](../../azure-monitor/app/diagnostic-search.md) pour diagnostiquer les problèmes
- [Utilisez la fonctionnalité Analytics](../../azure-monitor/app/analytics.md) pour des requêtes plus élaborées
- [Créez des tableaux de bord](../../azure-monitor/app/app-insights-dashboards.md)

 Ajoutez des données de télémétrie :

- [Créez des tests web](monitor-web-app-availability.md) s’assurer que votre site reste actif.
- [Ajoutez la télémétrie de client web](../../azure-monitor/app/javascript.md) pour afficher les exceptions à partir du code de page web et vous permettre d’insérer des appels de trace.
- [Ajoutez le kit SDK Application Insights à votre code](../../azure-monitor/app/asp-net.md) afin que vous pouvez insérer un suivi et enregistrer les appels

Faire plus avec Status Monitor v2 :

- Utilisez notre guide [dépannage](status-monitor-v2-troubleshoot.md) Status Monitor v2.
