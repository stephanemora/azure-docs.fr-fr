---
title: Activer la journalisation des diagnostics pour les applications dans Azure App Service
description: Découvrez comment activer la journalisation de diagnostic et ajouter la fonctionnalité d’instrumentation à votre application, mais aussi comment accéder aux informations enregistrées par Azure.
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: b73656e2bb7c413d2c29fafb682f39154499854a
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2019
ms.locfileid: "54904452"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Activer la journalisation des diagnostics pour les applications dans Azure App Service
## <a name="overview"></a>Vue d’ensemble
Azure fournit des diagnostics intégrés pour aider au débogage d’une [application App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Cet article vous explique comment activer la journalisation de diagnostic et ajouter la fonctionnalité d’instrumentation à votre application, et comment accéder aux informations enregistrées par Azure.

Cet article utilise le [portail Azure](https://portal.azure.com) et Azure CLI pour l’exploitation des journaux de diagnostic. Pour plus d’informations sur l’utilisation de journaux de diagnostic avec Visual Studio, consultez [Résolution des problèmes Azure dans Visual Studio](troubleshoot-dotnet-visual-studio.md).

## <a name="whatisdiag"></a>Diagnostics de serveur Web et diagnostics d’application
App Service fournit des fonctionnalités de diagnostic pour les informations de journalisation provenant du serveur Web et de l’application web. Ces informations sont réparties, en toute logique, en **diagnostics de serveur web** et en **diagnostics d’application**.

### <a name="web-server-diagnostics"></a>Diagnostics de serveur web
Vous pouvez activer ou désactiver les types de journaux suivants :

* **Messages d’erreur détaillés** : informations d’erreur détaillées pour les codes d’état HTTP qui indiquent un échec (code d’état 400 ou supérieur). Il peut s’agir d’informations permettant de déterminer la raison pour laquelle le serveur a renvoyé le code d’erreur.
* **Suivi des demandes ayant échoué** : informations détaillées sur les demandes qui ont échoué, y compris une trace des composants IIS utilisés pour traiter la demande et la durée dans chaque composant. Ces informations sont utiles si vous souhaitez améliorer les performances du site ou isoler une erreur HTTP spécifique.
* **Journalisation du serveur Web** : informations sur les transactions HTTP à l’aide du [format de fichier journal étendu W3C](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Ces informations peuvent se révéler utiles pour déterminer les métriques globales d’un site, comme le nombre de requêtes traitées ou le nombre de requêtes émanant d’une adresse IP spécifique.

### <a name="application-diagnostics"></a>Diagnostic d'application
Le diagnostic d'application vous permet de capturer des informations générées par une application Web. Les applications ASP.NET peuvent utiliser la classe [System.Diagnostics.Trace](https://msdn.microsoft.com/library/36hhw2t6.aspx) pour enregistrer des informations dans le journal de diagnostic d'application. Par exemple : 

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

Au moment de l’exécution, vous pouvez récupérer ces journaux pour vous aider durant le dépannage. Pour plus d’informations, consultez la page [Résolution des problèmes Azure App Service dans Visual Studio](troubleshoot-dotnet-visual-studio.md).

App Service journalise également les informations de déploiement quand vous publiez du contenu dans une application. Cette opération est automatique et il n’existe aucun paramètre de configuration pour la journalisation du déploiement. Cette dernière vous permet de déterminer le motif d'échec d'un déploiement. Si vous utilisez, par exemple, un script de déploiement personnalisé, vous pouvez recourir à la journalisation de déploiement pour déterminer la cause de l’échec du script.

## <a name="enablediag"></a>Activation des diagnostics
Pour activer les diagnostics sur le [portail Azure](https://portal.azure.com), accédez à la page de votre application, puis cliquez sur **Paramètres > Journaux de diagnostics**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Partie des journaux](./media/web-sites-enable-diagnostic-log/logspart.png)

Quand vous activez les **diagnostics d’application**, choisissez également le **niveau**. Le tableau suivant présente les catégories de journaux offertes par chaque niveau :

| Niveau| Catégories de journaux incluses |
|-|-|
|**Désactivé** | Aucun |
|**Erreur** | Erreur, Critique |
|**Avertissement** | Avertissement, Erreur, Critique|
|**Informations** | Info, Avertissement, Erreur, Critique|
|**Verbose** | Trace, Débogage, Info, Avertissement, Erreur, Critique (toutes les catégories) |
|-|-|

Pour **Journal des applications**, vous pouvez temporairement activer l’option système à des fins de débogage. Cette option se désactive automatiquement au bout de 12 heures. Vous pouvez également activer l’option de stockage Blob pour sélectionner un conteneur d’objets blob pour y écrire des journaux.

> [!NOTE]
> Actuellement, seuls les journaux des applications .NET peuvent être écrits dans le Stockage Blob. Il n’est possible de stocker les journaux des applications Java, PHP, Node.js, Python que dans le système de fichiers (sans modification du code pour écrire des journaux dans un stockage externe).
>
>

Pour **Journalisation du serveur web**, vous pouvez sélectionner **Stockage** ou **Système de fichiers**. Si vous sélectionnez le **stockage**, vous avez également la possibilité de sélectionner un compte de stockage, puis un conteneur d’objets blob dans lequel les journaux sont écrits. 

Si vous stockez les journaux sur le système de fichiers, vous pouvez accéder à ces fichiers par FTP ou les télécharger sous forme d’archive ZIP en utilisant Azure PowerShell ou Azure CLI.

Par défaut, les journaux ne sont pas automatiquement supprimés (à l’exception du **Journal des applications (Système de fichiers)**). Pour supprimer automatiquement les journaux, définissez le champ **Période de rétention (jours)**.

> [!NOTE]
> Si vous [régénérez les clés d’accès de votre compte de stockage](../storage/common/storage-create-storage-account.md), vous devez réinitialiser la configuration de journalisation correspondante pour utiliser les clés mises à jour. Pour ce faire :
>
> 1. Sous l’onglet **Configurer**, définissez la fonctionnalité de journalisation correspondante sur **Désactivé**. Enregistrez votre paramètre.
> 2. Réactivez la journalisation de l’objet blob du compte de stockage. Enregistrez votre paramètre.
>
>

Vous pouvez activer simultanément toute combinaison de système de fichiers ou stockage d’objets blob. Des configurations de niveau de journalisation individuelles sont également possibles. Vous pouvez, par exemple, consigner les erreurs et les avertissements dans le stockage d'objets blob dans le cadre d'une solution de journalisation à long terme, tout en activant un niveau de journalisation détaillé du système de fichiers.

Bien que ces deux emplacements de stockage fournissent les mêmes informations de base pour les événements consignés, le **stockage blob** consigne des informations supplémentaires, telles que l’ID d’instance, l’ID de thread et un horodatage plus précis que lorsque vous optez pour la journalisation dans le **système de fichiers**.

> [!NOTE]
> Les informations stockées dans le **stockage blob** n’est accessible qu’à l’aide d’un client de stockage ou d’une application capable d’utiliser directement ces systèmes de stockage. Par exemple, Visual Studio 2013 contient un Explorateur de stockage qui peut être utilisé pour explorer un système de stockage d’objets blob, tandis que HDInsight peut accéder aux données stockées dans un stockage d’objets blob. Vous pouvez également écrire une application qui accède à Azure Storage en utilisant l'un des [Kits de développement logiciel (SDK) Azure](https://azure.microsoft.com/downloads/).
>

## <a name="download"></a>Guide pratique : Télécharger des journaux
Les informations de diagnostic stockées dans le système de fichiers d’application sont directement accessibles via FTP. Vous pouvez également les télécharger sous la forme d’une archive ZIP en utilisant Azure CLI.

La structure de répertoires dans laquelle les journaux sont stockés est la suivante :

* **Journaux d'application** : /LogFiles/Application/. Ce dossier contient un ou plusieurs fichiers texte contenant des informations générées dans le cadre de la journalisation des applications.
* **Suivi des demandes ayant échoué** : /LogFiles/W3SVC#########/. Ce dossier contient un fichier XSL et un ou plusieurs fichiers XML. Assurez-vous de télécharger le fichier XSL dans le même répertoire que le(s) fichier(s) XML, car le fichier XSL possède des attributs permettant de formater et de filtrer le contenu de fichiers XML lorsqu'ils sont affichés dans Internet Explorer.
* **Journaux d'erreurs détaillés** : /LogFiles/DetailedErrors/. Ce dossier contient un ou plusieurs fichiers .htm fournissant des informations détaillées sur toute erreur HTTP qui s'est produite.
* **Journaux des serveurs Web** : /LogFiles/http/RawLogs. Ce dossier contient un ou plusieurs fichiers texte au format [de fichier journal étendu W3C](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx).
* **Journaux de déploiement** : /LogFiles/Git. Ce dossier contient les journaux générés par les processus de déploiement internes utilisés par Azure App Service, ainsi que les journaux des déploiements Git. Vous trouverez également les journaux de déploiement sous D:\home\site\deployments.

### <a name="ftp"></a>FTP

Pour ouvrir une connexion FTP sur le serveur FTP de votre application, consultez [Déployer votre application dans Azure App Service avec FTP/S](deploy-ftp.md).

Une fois connecté au serveur FTP/S de votre application, ouvrez le dossier **LogFiles**, où les fichiers journaux sont stockés.

### <a name="download-with-azure-cli"></a>Téléchargement avec Azure CLI
Pour télécharger les fichiers journaux à l’aide de l’interface de ligne de commande Azure, ouvrez une nouvelle session d’invite de commandes, PowerShell, Bash ou Terminal, puis entrez la commande suivante :

    az webapp log download --resource-group resourcegroupname --name appname

Cette commande enregistre les journaux de l’application nommée « appname » dans un fichier **diagnostics.zip** du répertoire actif.

> [!NOTE]
> Si vous n’avez pas installé ou configuré Azure CLI de manière à utiliser votre abonnement Azure, consultez [Guide pratique pour utiliser Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Activation Afficher les journaux dans Application Insights
Visual Studio Application Insights fournit des outils de filtrage et de recherche dans les journaux, mais aussi de mise en corrélation des journaux avec les requêtes et d’autres événements.

1. Ajoutez le Kit de développement logiciel Application Insights à votre projet dans Visual Studio.
   * Dans l’Explorateur de solutions, cliquez avec le bouton droit sur votre projet, puis sélectionnez Ajouter Application Insights. L’interface vous guide tout au long de la création de la ressource Application Insights. [En savoir plus](../azure-monitor/app/asp-net.md)
2. Ajoutez le package de l’écouteur de suivi à votre projet.
   * Cliquez avec le bouton droit sur votre projet et choisissez Gérer les packages NuGet. Sélectionnez `Microsoft.ApplicationInsights.TraceListener` [En savoir plus](../azure-monitor/app/asp-net-trace-logs.md)
3. Téléchargez votre projet et exécutez-le pour générer des données de journal.
4. Dans le [portail Azure](https://portal.azure.com/), accédez à votre nouvelle ressource Application Insights, puis ouvrez la fonction de **recherche**. Vous devriez voir vos données de journal, ainsi que la requête, l’utilisation et les autres mesures de télémétrie. Vous devrez parfois patienter quelques minutes pour accéder à certaines mesures de télémétrie : dans ce cas, cliquez sur Actualiser. [En savoir plus](../azure-monitor/app/diagnostic-search.md)

[En savoir plus sur le suivi des performances avec Application Insights](../azure-monitor/app/azure-web-apps.md)

## <a name="streamlogs"></a>Guide pratique : Diffuser les journaux en continu
Lors du développement d’une application, il est utile de visualiser des informations de journalisation en temps quasi réel. Vous pouvez diffuser ces informations vers votre environnement de développement en utilisant Azure CLI.

> [!NOTE]
> Certains types de mémoire tampon de journalisation sont écrits dans le fichier journal. Dès lors, il se peut que les événements apparaissent de manière désordonnée dans le flux. Ainsi, il est possible qu'une entrée du journal d'application qui se produit lorsqu'un utilisateur visite une page soit affichée dans le flux avant l'entrée de journal HTTP correspondante pour la demande de page.
>
> [!NOTE]
> Pendant le streaming des journaux, les informations écrites dans tout fichier texte stocké dans le dossier **D:\\home\\LogFiles\\** sont également diffusées.
>
>

### <a name="streaming-with-azure-cli"></a>Diffusion en continu à l’aide d’Azure CLI
Pour diffuser des informations de journalisation, ouvrez une nouvelle session d'invite de commandes, PowerShell, Bash ou Terminal, puis entrez la commande suivante :

    az webapp log tail --name appname --resource-group myResourceGroup

Cette commande établie une connexion avec l’application nommée « appname », puis diffuse les informations vers la fenêtre à mesure que des événements de journalisation se produisent sur l’application. Toute information enregistrée dans un fichier ayant l’extension .txt, .log ou .htm et stocké dans le répertoire /LogFiles (d:/home/logfiles) est diffusée vers la console locale.

Pour filtrer des événements spécifiques, tels que des erreurs, utilisez le paramètre **--Filter** . Par exemple : 

    az webapp log tail --name appname --resource-group myResourceGroup --filter Error

Pour filtrer des types de journaux spécifiques, tels que HTTP, utilisez le paramètre **--Path** . Par exemple : 

    az webapp log tail --name appname --resource-group myResourceGroup --path http

> [!NOTE]
> Si vous n’avez pas installé ou configuré Azure CLI de manière à utiliser votre abonnement Azure, consultez la page [Guide pratique pour utiliser Azure CLI](../cli-install-nodejs.md).
>
>

## <a name="understandlogs"></a>Guide pratique : Comprendre les journaux de diagnostic
### <a name="application-diagnostics-logs"></a>Journaux de diagnostic d'application
Le diagnostic d’application stocke les informations dans un format spécifique pour les applications .NET selon que vous stockez les journaux dans le système de fichiers ou le stockage d’objets blob. 

L’ensemble de base des données stockées est le même dans les deux types de stockage, à savoir : date et heure auxquelles l’événement s’est produit, ID de processus qui a généré l’événement, type d’événement (informations, avertissement, erreur) et message d’événement. L’utilisation du système de fichiers pour le stockage des journaux est utile lorsque vous avez besoin d’un accès immédiat pour résoudre un problème car les fichiers journaux sont mis à jour presque instantanément. Le stockage d’objets blob est utilisé à des fins d’archivage car les fichiers sont mis en cache puis acheminés vers le conteneur de stockage selon un calendrier précis.

**Système de fichiers**

Chaque ligne journalisée dans le système de fichiers ou reçue par le biais d’un streaming se présente au format suivant :

    {Date}  PID[{process ID}] {event type/level} {message}

Par exemple, un événement d’erreur peut se présenter comme suit :

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

Parmi les trois méthodes disponibles, la journalisation d’informations dans le système de fichiers est celle qui fournit les informations les plus élémentaires. L’heure, l’ID de processus, le niveau d’événement et le message sont, en effet, les seules informations fournies.

**Stockage Blob**

Lorsque vous consignez des données dans un stockage d'objets blob, elles sont stockées au format CSV (valeurs séparées par des virgules). Des champs supplémentaires sont consignés afin de fournir des informations plus précises sur l’événement. Les propriétés suivantes sont utilisées pour chaque ligne du fichier CSV :

| Nom de la propriété | Valeur/format |
| --- | --- |
| Date |Date et heure auxquelles l'événement s'est produit |
| Niveau |Niveau d’événement (par exemple, erreur, avertissement ou information) |
| ApplicationName |Nom de l’application |
| InstanceId |Instance d’application sur laquelle l’événement s’est produit |
| EventTickCount |Date et heure auxquelles l'événement s'est produit, au format Tick (précision accrue) |
| EventId |ID de cet événement<p><p>Il est, par défaut, défini sur 0 |
| Pid |ID du processus |
| Tid |ID de thread qui a généré l'événement |
| Message |Message détaillé sur l'événement |

Les données stockées dans un objet blob peuvent se présenter comme suit :

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> Pour ASP.NET Core, la journalisation s’effectue à l’aide du fournisseur [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices). Ce fournisseur dépose des fichiers journaux supplémentaires dans le conteneur blob. Pour plus d’informations, consultez l’article [ASP.NET Core logging in Azure](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#logging-in-azure) (Journalisation ASP.NET Core dans Azure).
>
>

### <a name="failed-request-traces"></a>Suivi des demandes ayant échoué
Le suivi des demandes ayant échoué est stocké dans des fichiers XML nommés **fr######.xml**. Pour faciliter la consultation des informations consignées, une feuille de style XSL nommée **freb.xsl** est fournie dans le même répertoire que les fichiers XML. Si vous ouvrez un des fichiers XML dans Internet Explorer, ce dernier utilise la feuille de style XSL pour fournir un affichage mis en forme des informations de suivi, comme dans l’exemple suivant :

![affichage d'une demande ayant échoué dans le navigateur](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### <a name="detailed-error-logs"></a>Journaux d'erreurs détaillés
Les journaux d'erreurs détaillés sont des documents HTML qui fournissent des informations plus détaillées sur les erreurs HTTP qui se sont produites. Puisqu'il s'agit simplement de documents HTML, ils peuvent être consultés à l'aide d'un navigateur Web.

### <a name="web-server-logs"></a>Journaux des serveurs Web
Les journaux de serveur Web utilisent le [format de fichier journal étendu W3C](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Ces informations peuvent être lues à l'aide d'un éditeur de texte ou analysées à l'aide d'utilitaires tels que [Log Parser](https://go.microsoft.com/fwlink/?LinkId=246619).

> [!NOTE]
> Les journaux générés par Azure App Service ne prennent pas en charge les champs **s-computername**, **s-ip** ou **cs-version**.
>
>

## <a name="nextsteps"></a> Étapes suivantes
* [Surveillance des applications dans Azure App Service](web-sites-monitor.md)
* [Dépannage d’une application web dans le Service d’application Microsoft Azure à l’aide de Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analyse des journaux d’application dans HDInsight (en anglais)](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
