---
title: Redirecteur local Azure Application Insights OpenCensus (préversion)
description: Découvrez comment transférer des traces et étendues distribuées OpenCensus à partir de langages tels que Python et Go vers Azure Application Insights.
ms.topic: conceptual
ms.date: 09/18/2018
ms.reviewer: nimolnar
ms.openlocfilehash: bcf7ba495897eb1c9b40c78f00825e863390b5d1
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669962"
---
# <a name="local-forwarder-preview"></a>Redirecteur local (préversion)

Le redirecteur local est un agent qui collecte des données de télémétrie Application Insights ou [OpenCensus](https://opencensus.io/) à partir de plusieurs SDK, et les achemine vers Application Insights. Il peut être exécuté sous Windows et Linux. Vous pouvez également l’exécuter sous macOS, mais la prise en charge n’est pas encore officielle.

## <a name="running-local-forwarder"></a>Exécuter le redirecteur local

Le redirecteur local est un [projet Open Source sur GitHub](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases). Plusieurs méthodes permettent d’exécuter un redirecteur local sur des plateformes différentes.

### <a name="windows"></a>Windows

#### <a name="windows-service"></a>Service Windows

Pour exécuter un redirecteur local sur Windows, le moyen le plus simple consiste à l’installer en tant que service Windows. La mise en production est fournie avec un exécutable de service Windows (*WindowsServiceHost/Microsoft.LocalForwarder.WindowsServiceHost.exe*) qui peut être facilement enregistré auprès du système d’exploitation.

> [!NOTE]
> .NET Framework 4.7 (au minimum) doit être installé pour que le service du redirecteur local fonctionne. Si .NET Framework 4.7 n’est pas installé, le service se charge de l’installation mais ne démarre pas. Pour accéder à la dernière version de .NET Framework, **[rendez-vous sur la page de téléchargement de .NET Framework](
https://www.microsoft.com/net/download/dotnet-framework-runtime/net472?utm_source=getdotnet&utm_medium=referral)** .

1. Téléchargez le fichier LF.WindowsServiceHost.zip à partir de la [page de mise en production du redirecteur local](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases) sur GitHub.

    ![Capture d’écran de la page de téléchargement de la mise en production du redirecteur local](./media/opencensus-local-forwarder/001-local-forwarder-windows-service-host-zip.png)

2. Dans cet exemple, pour faciliter la démonstration, nous extrayons simplement le fichier .zip vers le chemin d’accès `C:\LF-WindowsServiceHost`.

    Pour inscrire le service et le configurer de sorte qu’il est lancé au démarrage du système, exécutez la commande suivante à partir de la ligne de commande, en tant qu’administrateur :

    ```
    sc create "Local Forwarder" binpath="C:\LF-WindowsServiceHost\Microsoft.LocalForwarder.WindowsServiceHost.exe" start=auto
    ```
    
    Vous devez recevoir la réponse suivante :
    
    `[SC] CreateService SUCCESS`
    
    Pour examiner votre nouveau service via l’interface utilisateur graphique des services, tapez ``services.msc``.
        
     ![Capture d’écran du service de redirecteur local](./media/opencensus-local-forwarder/002-services.png)

3. **Cliquez avec le bouton droit** sur le nouveau redirecteur local, puis sélectionnez **Démarrer**. Votre service est désormais en cours d’exécution.

4. Par défaut, le service est créé sans aucune action de récupération. Cliquez **avec le bouton droit de la souris** et sélectionnez **Propriétés** > **Récupération** pour configurer des réponses automatiques en cas de panne du service.

    Si vous préférez définir les options de récupération automatique par programmation en cas de défaillance, vous pouvez utiliser la commande suivante :

    ```
    sc failure "Local Forwarder" reset= 432000 actions= restart/1000/restart/1000/restart/1000
    ```

5. Dans le même emplacement que votre fichier ``Microsoft.LocalForwarder.WindowsServiceHost.exe`` (``C:\LF-WindowsServiceHost``, dans cet exemple), il existe un fichier intitulé ``LocalForwarder.config``. Ce fichier basé sur xml vous permet d’ajuster la configuration de votre redirecteur local et de spécifier la clé d’instrumentation de la ressource Application Insights vers laquelle vous souhaitez que vos données de traçage distribué soient transférées. 

    Après avoir modifié le fichier ``LocalForwarder.config`` pour ajouter votre clé d’instrumentation, veillez à redémarrer le **service de redirecteur local** pour que vos modifications soient appliquées.
    
6. Pour confirmer vos paramètres et vérifier que le redirecteur local écoute les données de trace comme prévu, consultez le fichier ``LocalForwarder.log``. Vous devez voir des résultats similaires à l’image ci-dessous, en bas du fichier :

    ![Capture d’écran du fichier LocalForwarder.log](./media/opencensus-local-forwarder/003-log-file.png)

#### <a name="console-application"></a>Application de console

Dans certains cas, il peut être utile d’exécuter le redirecteur local en tant qu’application de console. La mise en production est fournie avec les versions exécutables suivantes de l’hôte de console :
* Un fichier binaire .NET Core dépendant de l’infrastructure */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. Pour exécuter ce fichier binaire, l’environnement d’exécution .NET Core doit être installé ; consultez cette [page](https://www.microsoft.com/net/download/dotnet-core/2.1) de téléchargement pour en savoir plus.
  ```batchfile
  E:\uncdrop\ConsoleHost\publish>dotnet Microsoft.LocalForwarder.ConsoleHost.dll
  ```
* Un ensemble autonome de fichiers binaires .NET Core pour les plates-formes x86 et x64. L’environnement d’exécution .NET Core n’est pas nécessaire. */ConsoleHost/win-x86/publish/Microsoft.LocalForwarder.ConsoleHost.exe*, */ConsoleHost/win-x64/publish/Microsoft.LocalForwarder.ConsoleHost.exe*.
  ```batchfile
  E:\uncdrop\ConsoleHost\win-x86\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  E:\uncdrop\ConsoleHost\win-x64\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  ```

### <a name="linux"></a>Linux

Comme pour Windows, la mise en production est fournie avec les versions exécutables suivantes de l’hôte de console :
* Un fichier binaire .NET Core dépendant de l’infrastructure */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. Pour exécuter ce fichier binaire, l’environnement d’exécution .NET Core doit être installé ; consultez cette [page](https://www.microsoft.com/net/download/dotnet-core/2.1) de téléchargement pour en savoir plus.

```batchfile
dotnet Microsoft.LocalForwarder.ConsoleHost.dll
```

* Un ensemble autonome de fichiers binaires .NET Core pour Linux-64. L’environnement d’exécution .NET Core n’est pas nécessaire. */ConsoleHost/linux-x64/publish/Microsoft.LocalForwarder.ConsoleHost*.

```batchfile
user@machine:~/ConsoleHost/linux-x64/publish$ sudo chmod +x Microsoft.LocalForwarder.ConsoleHost
user@machine:~/ConsoleHost/linux-x64/publish$ ./Microsoft.LocalForwarder.ConsoleHost
```

De nombreux utilisateurs Linux souhaitent exécuter le redirecteur local en tant que démon. Un large éventail de solutions de gestion des services (Upstart, sysv ou systemd, par exemple) sont fournies avec les systèmes Linux. Quelle que soit votre version, vous pouvez l’utiliser pour exécuter le redirecteur local de la manière la mieux adaptée à votre scénario.

Par exemple, créons un service démon à l’aide de systemd. Nous utiliserons la version dépendante de l’infrastructure, mais la même procédure s’applique aux versions autonomes.

* Créez le fichier de service suivant intitulé *localforwarder.service* et placez-le dans */lib/systemd/system*.
Dans cet exemple, imaginons que votre nom d’utilisateur est SAMPLE_USER et que vous avez copié les fichiers binaires dépendant de l’infrastructure du redirecteur local (à partir de */ConsoleHost/publish*) vers */home/SAMPLE_USER/LOCALFORWARDER_DIR*.

```
# localforwarder.service
# Place this file into /lib/systemd/system/
# Use 'systemctl enable localforwarder' to start the service automatically on each boot
# Use 'systemctl start localforwarder' to start the service immediately

[Unit]
Description=Local Forwarder service
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=1
User=SAMPLE_USER
WorkingDirectory=/home/SAMPLE_USER/LOCALFORWARDER_DIR
ExecStart=/usr/bin/env dotnet /home/SAMPLE_USER/LOCALFORWARDER_DIR/Microsoft.LocalForwarder.ConsoleHost.dll noninteractive

[Install]
WantedBy=multi-user.target
```

* Exécutez la commande suivante pour demander à systemd de démarrer le redirecteur local à chaque démarrage.

```
systemctl enable localforwarder
```

* Exécutez la commande suivante pour demander à systemd de démarrer le redirecteur local immédiatement.

```
systemctl start localforwarder
```

* Surveillez le service en examinant les fichiers * *.log* du répertoire /home/SAMPLE_USER/LOCALFORWARDER_DIR.

### <a name="mac"></a>Mac
Le redirecteur local peut fonctionner avec macOS, mais il n’est pas officiellement pris en charge.

### <a name="self-hosting"></a>Auto-hébergement
Le redirecteur local est également distribué sous forme de package NuGet .NET Standard, ce qui vous permet de l’héberger au sein de votre propre application .NET.

```csharp
using Library;
...
Host host = new Host();

// see section below on configuring local forwarder
string configuration = ...;
    
host.Run(config, TimeSpan.FromSeconds(5));
...
host.Stop();
```

## <a name="configuring-local-forwarder"></a>Configuration du redirecteur local

* Lorsque vous exécutez l’un des hôtes du redirecteur local (hôte de la console ou hôte de service Windows), vous trouverez le fichier **LocalForwarder.config** à côté du fichier binaire.
* Lorsque le redirecteur local NuGet est auto-hébergé, la configuration du même format doit être fournie dans le code (consultez la section sur l’auto-hébergement). Pour connaître la syntaxe de configuration, vérifiez le fichier [LocalForwarder.config](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/ConsoleHost/LocalForwarder.config) dans le référentiel GitHub. 

> [!NOTE]
> La configuration est susceptible de varier d’une mise en production à une autre. Par conséquent, faites attention à la version que vous utilisez.

## <a name="monitoring-local-forwarder"></a>Surveillance du redirecteur local

Les traces sont écrites dans le système de fichiers à côté de l’exécutable pour le redirecteur local (recherchez les fichiers * *.log*). Vous pouvez placer un fichier intitulé *NLog.config* à côté de l’exécutable pour utiliser votre propre configuration à la place de la configuration par défaut. Consultez la [documentation](https://github.com/NLog/NLog/wiki/Configuration-file#configuration-file-format) pour obtenir la description du format.

Si aucun fichier de configuration n’est fourni (cas par défaut), le redirecteur local utilise la configuration par défaut, qui se trouve [ici](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/Common/NLog.config).

## <a name="next-steps"></a>Étapes suivantes

* [OpenCensus](https://opencensus.io/)
