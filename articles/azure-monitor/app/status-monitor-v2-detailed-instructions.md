---
title: Instructions détaillées de Azure Status Monitor v2 | Microsoft Docs
description: Obtenir des instructions détaillées pour la mise en route avec Status Monitor v2. Surveiller les performances de site Web sans avoir à redéployer le site Web. Fonctionne avec les applications web ASP.NET hébergées en local, dans les machines virtuelles, ou sur Azure.
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
ms.openlocfilehash: d0960c749d74903acc778c0f21d5c49f380195ae
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734199"
---
# <a name="status-monitor-v2-detailed-instructions"></a>État du moniteur v2 : Instructions détaillées

Cet article décrit comment intégrer à PowerShell Gallery et télécharger le module ApplicationMonitor.
Il décrit les paramètres les plus courants que vous aurez besoin pour commencer.
Il inclut également les instructions du manuel au cas où vous n’avez accès à internet.

> [!IMPORTANT]
> État du moniteur v2 est actuellement en version préliminaire publique.
> Cette version préliminaire est fournie sans contrat de niveau de service, et nous n’est pas recommandé pour les charges de travail de production. Certaines fonctionnalités ne peuvent pas être pris en charge, et certains peuvent avoir des fonctionnalités limitées.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="get-an-instrumentation-key"></a>Obtenir une clé d’instrumentation

Pour commencer, vous avez besoin d’une clé d’instrumentation. Pour plus d’informations, consultez [créer une ressource Application Insights](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Exécutez PowerShell en tant qu’administrateur avec une stratégie d’exécution avec élévation de privilèges

**Exécuter en tant qu’administrateur**

PowerShell a besoin d’autorisations de niveau administrateur pour apporter des modifications sur votre ordinateur.

**Stratégie d’exécution**
- Description : Par défaut, l’exécution des scripts PowerShell est désactivée. Nous vous recommandons d’autoriser les scripts RemoteSigned pour uniquement l’étendue actuelle.
- Référence : [À propos des stratégies d’exécution](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) et [Set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
).
- Commande : `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`.
- Paramètre facultatif :
    - `-Force`. Contourne l’invite de confirmation.

**Exemples d’erreurs**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Prérequis pour PowerShell

Audit de votre instance de PowerShell en exécutant la `$PSVersionTable` commande.
Cette commande génère la sortie suivante :


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

Ces instructions ont été écrites et testées sur un ordinateur exécutant Windows 10 et les versions répertoriées ci-dessus.

## <a name="prerequisites-for-powershell-gallery"></a>Conditions préalables pour PowerShell Gallery

Ces étapes seront préparer votre serveur pour télécharger les modules à partir de PowerShell Gallery.

> [!NOTE] 
> PowerShell Gallery est pris en charge sur Windows 10, Windows Server 2016 et PowerShell 6.
> Pour plus d’informations sur les versions antérieures, consultez [PowerShellGet installation](https://docs.microsoft.com/powershell/gallery/installing-psget).


1. Avec une stratégie d’exécution avec élévation de privilèges, exécutez PowerShell en tant qu’administrateur.
2. Installez le fournisseur de package NuGet.
    - Description : Vous avez besoin de ce fournisseur pour interagir avec les référentiels NuGet comme PowerShell Gallery.
    - Référence : [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - Commande : `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`.
    - Paramètres facultatifs :
        - `-Proxy`. Spécifie un serveur proxy pour la demande.
        - `-Force`. Contourne l’invite de confirmation.
    
    Vous recevez cette invite si NuGet n’est pas configuré :
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Configurer PowerShell Gallery comme un référentiel fiable.
    - Description : Par défaut, PowerShell Gallery est un référentiel non approuvé.
    - Référence : [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - Commande : `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`.
    - Paramètre facultatif :
        - `-Proxy`. Spécifie un serveur proxy pour la demande.

    Vous recevez cette invite si PowerShell Gallery n’est pas approuvé :

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    Vous pouvez confirmer cette modification et d’audit de toutes les PSRepositories en exécutant la `Get-PSRepository` commande.

4. Installez la dernière version de PowerShellGet.
    - Description : Ce module contient les outils permettant d’obtenir d’autres modules à partir de PowerShell Gallery. La version 1.0.0.1 est livré avec Windows 10 et Windows Server. Version 1.6.0 ou ultérieure est nécessaire. Pour déterminer quelle version est installée, exécutez le `Get-Command -Module PowerShellGet` commande.
    - Référence : [L’installation de PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).
    - Commande : `Install-Module -Name PowerShellGet`.
    - Paramètres facultatifs :
        - `-Proxy`. Spécifie un serveur proxy pour la demande.
        - `-Force`. Ignore l’avertissement « déjà installées » et installe la dernière version.

    Vous recevez cette erreur si vous n’utilisez pas la version la plus récente de PowerShellGet :
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Redémarrez PowerShell. Impossible de charger la nouvelle version dans la session active. Nouvelles sessions PowerShell chargera la dernière version de PowerShellGet.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Téléchargez et installez le module via PowerShell Gallery

Ces étapes télécharge le module Az.ApplicationMonitor à partir de PowerShell Gallery.

1. Assurez-vous que toutes les conditions préalables pour PowerShell Gallery sont remplies.
2. Avec une stratégie d’exécution avec élévation de privilèges, exécutez PowerShell en tant qu’administrateur.
3. Installez le module Az.ApplicationMonitor.
    - Référence : [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - Commande : `Install-Module -Name Az.ApplicationMonitor`.
    - Paramètres facultatifs :
        - `-Proxy`. Spécifie un serveur proxy pour la demande.
        - `-AllowPrerelease`. Permet l’installation des versions alpha et bêta.
        - `-AcceptLicense`. Contourne l’invite « Accepter la licence »
        - `-Force`. Permet d’ignorer l’avertissement « Référentiel non approuvé ».

## <a name="download-and-install-the-module-manually-offline-option"></a>Télécharger et installer le module manuellement (option hors connexion)

Si pour une raison quelconque vous ne pouvez pas vous connecter au module PowerShell, vous pouvez manuellement télécharger et installer le module Az.ApplicationMonitor.

### <a name="manually-download-the-latest-nupkg-file"></a>Télécharger manuellement le dernier fichier de package NuGet

1. Accédez à https://www.powershellgallery.com/packages/Az.ApplicationMonitor
2. Sélectionnez la dernière version du fichier dans le **historique des versions** table.
3. Sous **Options d’Installation**, sélectionnez **téléchargement manuel**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Option 1 : Installer dans un répertoire de modules PowerShell
Installer le module PowerShell manuellement téléchargé dans un répertoire de PowerShell afin d’être détectable par les sessions PowerShell.
Pour plus d’informations, consultez [installation d’un PowerShell Module](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module).


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Décompressez le package NuGet dans un fichier zip à l’aide de Expand-Archive (v1.0.1.0)

- Description : Version de base de Microsoft.PowerShell.Archive (v1.0.1.0) ne peut pas décompresser les fichiers de package NuGet. Renommez le fichier avec l’extension .zip.
- Référence : [Développez-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6).
- Commande :

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Décompressez le package NuGet à l’aide de Expand-Archive (v1.1.0.0)

- Description : Utiliser une version actuelle de Expand-Archive de décompresser des fichiers de package NuGet sans modifier l’extension.
- Référence : [Développez-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) et [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Commande :

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Option 2 : Décompressez et importer le package NuGet manuellement
Installer le module PowerShell manuellement téléchargé dans un répertoire de PowerShell afin d’être détectable par les sessions PowerShell.
Pour plus d’informations, consultez [installation d’un PowerShell Module](https://docs.mircrosoft.com/powershell/developer/module/installing-a-powershell-module).

Si vous installez le module dans n’importe quel autre annuaire, importer manuellement le module à l’aide de [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6).

> [!IMPORTANT] 
> DLL installera via les chemins d’accès relatifs.
> Store le contenu du package dans votre répertoire d’exécution prévue et confirmez que les autorisations d’accès autorisent en lecture mais pas d’écriture.

1. Remplacez l’extension « .zip » et extraire le contenu du package dans votre répertoire d’installation prévue.
2. Recherchez le chemin de fichier du Az.ApplicationMonitor.psd1.
3. Avec une stratégie d’exécution avec élévation de privilèges, exécutez PowerShell en tant qu’administrateur.
4. Charger le module à l’aide de la `Import-Module Az.ApplicationMonitor.psd1` commande.
    

## <a name="route-traffic-through-a-proxy"></a>Acheminer le trafic via un proxy

Lorsque vous analysez un ordinateur sur votre intranet privé, vous devez acheminer le trafic HTTP via un proxy.

Les commandes PowerShell pour télécharger et installer Az.ApplicationMonitor à partir de PowerShell Gallery prennent en charge un `-Proxy` paramètre.
Lorsque vous écrivez vos scripts d’installation, passez en revue les instructions précédentes.

Vous devez le SDK Application Insights envoyer des données de télémétrie de votre application à Microsoft. Nous vous recommandons de configurer les paramètres de proxy pour votre application dans votre fichier web.config. Pour plus d’informations, consultez [le Forum aux questions sur Application Insights : Relais proxy](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough).


## <a name="enable-monitoring"></a>Activer la supervision

Utilisez le `Enable-ApplicationInsightsMonitoring` commande pour activer l’analyse.

Consultez le [référence de l’API](status-monitor-v2-api-enable-monitoring.md) pour une description détaillée de l’utilisation de cette applet de commande.



## <a name="next-steps"></a>Étapes suivantes

 Affichez vos données de télémétrie :

- [Exploration des mesures](../../azure-monitor/app/metrics-explorer.md) pour surveiller les performances et l’utilisation.
- [Recherchez les événements et journaux](../../azure-monitor/app/diagnostic-search.md) pour diagnostiquer les problèmes.
- [Utiliser l’Analytique](../../azure-monitor/app/analytics.md) pour plus d’informations de requêtes avancées.
- [Créer des tableaux de bord](../../azure-monitor/app/overview-dashboard.md).

 Ajoutez des données de télémétrie :

- [Créez des tests web](monitor-web-app-availability.md) s’assurer que votre site reste actif.
- [Ajoutez la télémétrie de client web](../../azure-monitor/app/javascript.md) pour afficher les exceptions à partir du code de page web et pour activer les appels de trace.
- [Ajouter le SDK Application Insights à votre code](../../azure-monitor/app/asp-net.md) afin de pouvoir insérer un suivi et enregistrer les appels.

Faire plus avec Status Monitor v2 :

- Utilisez notre guide [dépanner](status-monitor-v2-troubleshoot.md) Status Monitor v2.
