---
title: Suivre les modifications avec Azure Automation
description: La solution Change Tracking permet d’identifier les modifications apportées aux logiciels et au service Windows dans votre environnement.
services: automation
ms.service: automation
ms.component: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 86b8f76bd221be9f30a5b9336af858359ae0af8f
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238877"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Suivre les modifications apportées à votre environnement grâce à la solution Suivi des modifications

Cet article vous aide à utiliser la solution Change Tracking pour identifier facilement les modifications apportées dans votre environnement. La solution suit les modifications apportées aux logiciels Windows et Linux, aux fichiers Windows et Linux, aux clés de Registre Windows, aux services Windows et aux démons Linux. Identifier les modifications de configuration peut vous aider à identifier les problèmes opérationnels.

Les modifications apportées aux logiciels installés, aux services Windows, aux fichiers et Registre Windows et aux démons Linux sur les serveurs surveillés sont envoyées au service cloud Log Analytics pour traitement. La logique est appliquée aux données reçues et le service cloud enregistre les données. En utilisant les informations du tableau de bord de suivi des modifications, vous pouvez facilement voir les modifications apportées à votre infrastructure de serveur.

## <a name="supported-windows-operating-systems"></a>Systèmes d’exploitation Windows pris en charge

Les versions suivantes du système d’exploitation Windows sont officiellement prises en charge pour l’agent Windows :

* Windows Server 2008 Service Pack 1 (SP1) (ou version ultérieure) ;
* Windows 7 SP1 (ou version ultérieure).

## <a name="supported-linux-operating-systems"></a>Systèmes d’exploitation Linux pris en charge

Les distributions Linux suivantes sont officiellement prises en charge. Toutefois, l’agent Linux peut également s’exécuter sur d’autres distributions, qui ne se trouvent pas dans la liste. Sauf indication contraire, toutes les versions mineures sont prises en charge pour chaque version majeure répertoriée.  

* Amazon Linux 2012.09 à 2015.09 (x86/x64)
* CentOS Linux 5, 6 et 7 (x86/x64)  
* Oracle Linux 5, 6 et 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 et 7 (x86/x64)
* Debian GNU/Linux 6, 7 et 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 et 12 (x86/x64)

## <a name="enable-change-tracking-and-inventory"></a>Activer Change Tracking et Inventory

Pour commencer à suivre les modifications, vous devez activer la solution Change Tracking et Inventory pour votre compte Automation.

1. Dans le Portail Azure, accédez à votre compte Automation.
1. Sélectionnez **Change Tracking** sous **CONFIGURATION**.
1. Sélectionnez un espace de travail Log Analytics existant ou **créez un espace de travail** et cliquez sur **Activer**.

La solution est ainsi activée pour votre compte Automation. L’activation de la solution peut prendre jusqu’à 15 minutes. La bannière bleue vous avertit quand la solution est activée. Revenez à la page **Change Tracking** pour gérer la solution.

## <a name="configuring-change-tracking-and-inventory"></a>Configuration de Change Tracking et Inventory

Pour découvrir comment intégrer des ordinateurs à la solution, visitez : [Intégration de solutions Automation](automation-onboard-solutions-from-automation-account.md). Lorsque vous disposez de l’intégration d’une machine avec la solution Change Tracking and Inventory, vous pouvez configurer les éléments à suivre. Quand vous activez le suivi d’un nouveau fichier ou d’une nouvelle clé de Registre, ceux-ci sont activés à la fois pour Change Tracking et Inventory.

Pour suivre les modifications apportées à des fichiers sur Windows et Linux, les hachages MD5 de fichiers sont utilisés. Ces hachages sont ensuite utilisés pour détecter si une modification a été apportée depuis le dernier inventaire.

### <a name="configure-linux-files-to-track"></a>Configuration des fichiers Linux à suivre

Utilisez les étapes ci-dessous pour configurer le suivi des fichiers sur des ordinateurs Linux :

1. Dans votre compte Automation, sélectionnez **Change Tracking** sous **GESTION DE LA CONFIGURATION**. Cliquez sur **Modifier les paramètres** (le symbole engrenage).
2. Dans la page **Change Tracking**, sélectionnez **Fichiers Linux**, puis cliquez sur **+ Ajouter** pour ajouter un nouveau fichier à suivre.
3. Dans la fenêtre **Ajouter le fichier Linux pour le suivi des modifications**, entrez les informations du fichier ou du répertoire à suivre et cliquez sur **Enregistrer**.

|Propriété  |Description  |
|---------|---------|
|activé     | Détermine si le paramètre est appliqué.        |
|Item Name     | Nom convivial du fichier à suivre.        |
|Groupe     | Nom de groupe pour le regroupement logique des fichiers.        |
|Entrer le chemin     | Chemin dans lequel rechercher le fichier. Par exemple : « /etc/* .conf »       |
|Type de chemin     | Type d’élément à suivre. Valeurs possibles : fichier et répertoire.        |
|Récursivité     | Détermine si la récursivité est utilisée lorsque vous recherchez l’élément à suivre.        |
|Utiliser sudo     | Ce paramètre détermine si sudo est utilisé lorsque vous vérifiez l’élément.         |
|Liens     | Ce paramètre détermine le traitement des liens symboliques lorsque vous parcourez les répertoires.<br> **Ignorer** : ignore les liens symboliques et n’inclut pas les fichiers/répertoires référencés.<br>**Suivre** : suit les liens symboliques pendant les opérations de récursivité et inclut aussi les fichiers/répertoires référencés.<br>**Gérer** : suit les liens symboliques et autorise la modification du contenu retourné.     |
|Télécharger le contenu du fichier pour tous les paramètres| Active ou désactive le chargement du contenu du fichier pour le suivi des modifications. Options disponibles : **True** ou **False**.|

> [!NOTE]
> L’option permettant de « Gérer » les liens n’est pas recommandée. L’extraction du contenu du fichier n’est pas prise en charge.

### <a name="configure-windows-files-to-track"></a>Configuration des fichiers Windows à suivre

Utilisez les étapes suivantes pour configurer le suivi des fichiers sur des ordinateurs Windows :

1. Dans votre compte Automation, sélectionnez **Change Tracking** sous **GESTION DE LA CONFIGURATION**. Cliquez sur **Modifier les paramètres** (le symbole engrenage).
2. Dans la page **Change Tracking**, sélectionnez **Fichiers Windows**, puis cliquez sur **+ Ajouter** pour ajouter un nouveau fichier à suivre.
3. Dans la fenêtre **Ajouter le fichier Windows pour le suivi des modifications**, entrez les informations du fichier à suivre et cliquez sur **Enregistrer**.

|Propriété  |Description  |
|---------|---------|
|activé     | Détermine si le paramètre est appliqué.        |
|Item Name     | Nom convivial du fichier à suivre.        |
|Groupe     | Nom de groupe pour le regroupement logique des fichiers.        |
|Entrer le chemin     | Chemin dans lequel rechercher le fichier. Exemple : « c:\temp\myfile.txt ».       |
|Télécharger le contenu du fichier pour tous les paramètres| Active ou désactive le chargement du contenu du fichier pour le suivi des modifications. Options disponibles : **True** ou **False**.|

## <a name="configure-file-content-tracking"></a>Configurer le suivi de contenu de fichier

Avec File Content Change Tracking, vous pouvez voir le contenu d’un fichier avant et après modification. Cette fonctionnalité est disponible pour les fichiers Windows et Linux. À chaque modification du fichier, le contenu est stocké dans un compte de stockage, puis il est montré avant et après modification, à la suite ou côte à côte. Pour plus d’informations, consultez [Afficher le contenu d’un fichier suivi](change-tracking-file-contents.md).

![Afficher les modifications d’un fichier](./media/change-tracking-file-contents/view-file-changes.png)

### <a name="configure-windows-registry-keys-to-track"></a>Configurer les clés de Registre Windows pour effectuer le suivi

Utilisez les étapes suivantes pour configurer le suivi des clés de Registre sur des ordinateurs Windows :

1. Dans votre compte Automation, sélectionnez **Change Tracking** sous **GESTION DE LA CONFIGURATION**. Cliquez sur **Modifier les paramètres** (le symbole engrenage).
2. Dans la page **Change Tracking**, sélectionnez **Registre Windows**, puis cliquez sur **+ Ajouter** pour ajouter une nouvelle clé de Registre à suivre.
3. Dans la fenêtre **Ajouter le Registre Windows pour le suivi des modifications**, entrez les informations correspondant à la clé à suivre et cliquez sur **Enregistrer**.

|Propriété  |Description  |
|---------|---------|
|activé     | Détermine si le paramètre est appliqué.        |
|Item Name     | Nom convivial du fichier à suivre.        |
|Groupe     | Nom de groupe pour le regroupement logique des fichiers.        |
|Clé de Registre Windows   | Chemin dans lequel rechercher le fichier. Exemple : « HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup »      |

## <a name="limitations"></a>Limites

Actuellement, la solution Change Tracking ne prend pas en charge les éléments suivants :

* Dossiers (répertoires) pour le suivi des fichiers Windows
* Récursivité pour le suivi des fichiers Windows
* Caractères génériques pour le suivi des fichiers Windows
* Récursivité pour le suivi du Registre Windows
* Variables de chemin d’accès
* Systèmes de fichiers réseau
* Contenu du fichier

Autres limitations :

* La colonne **Taille maximale des fichiers** et ses valeurs ne sont pas utilisées dans l’implémentation actuelle.
* Si vous collectez plus de 2500 fichiers dans le cycle de collecte de 30 minutes, les performances de la solution peuvent être dégradées.
* Lorsque le trafic réseau est élevé, l’affichage des enregistrements de modifications peut prendre jusqu’à six heures.
* Si vous modifiez la configuration lorsqu’un ordinateur est arrêté, l’ordinateur risque de publier des modifications appartenant à la configuration précédente.

## <a name="known-issues"></a>Problèmes connus

La solution Change Tracking connaît les problèmes suivants :

* Les mises à jour de correctif logiciel ne sont pas collectées pour les machines Windows 10 Creators Update et Windows Server 2016 Core RS3.

## <a name="change-tracking-data-collection-details"></a>Détails de la collecte de données de suivi des modifications

Le tableau suivant indique la fréquence de collecte de données selon les types de modification. Pour chaque type de capture instantanée de données, l’état actuel est également actualisé au moins toutes les 24 heures :

| **Type de modification** | **Fréquence** |
| --- | --- |
| Registre Windows | 50 minutes |
| Fichier Windows | 30 minutes |
| Fichier Linux | 15 minutes |
| Services Windows | 10 secondes à 30 minutes</br> Par défaut : 30 minutes |
| Démons Linux | 5 minutes |
| Logiciels Windows | 30 minutes |
| Logiciels Linux | 5 minutes |

### <a name="windows-service-tracking"></a>Suivi du service Windows

Pour les services Windows, la fréquence de collecte par défaut est de 30 minutes. Pour configurer la fréquence, ouvrez **Change Tracking**. Sous **Modifier les paramètres** de l’onglet **Services Windows**, un curseur vous permet de modifier la fréquence de collecte des services Windows (de 10 secondes à 30 minutes). Déplacez la barre du curseur sur la fréquence de votre choix (celle-ci est automatiquement enregistrée).

![Curseur des services Windows](./media/automation-change-tracking/windowservices.png)

L’agent effectue uniquement le suivi des modifications, ce qui permet d’optimiser ses performances. Si vous définissez un seuil trop élevé, des modifications risquent d’être omises lorsque le service est rétabli à son état d’origine. Le fait de définir une fréquence moins élevée vous permet d’intercepter les modifications susceptibles d’être omises.

> [!NOTE]
> Même si l’agent peut enregistrer les modifications toutes les 10 secondes, les données mettent quelques minutes à s’afficher dans le portail. Les modifications effectuées pendant l’affichage des données dans le portail continuent d’être suivies et enregistrées.
  
### <a name="registry-key-change-tracking"></a>Suivi des modifications des clés de Registre

Le contrôle des modifications apportées aux clés de Registre a pour objectif d’identifier les points d’extension où peuvent s’activer du code tiers et des logiciels malveillants. La liste suivante présente les clés de Registre préconfigurées. Ces clés sont configurées, mais pas activées. Pour suivre ces clés de Registre, vous devez les activer.

> [!div class="mx-tdBreakAll"]
> |  |
> |---------|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille les entrées courantes de démarrage automatique qui se raccordent directement à l’Explorateur Windows et s’exécutent généralement in-process avec Explorer.exe.    |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille les scripts qui s’exécutent au démarrage.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown**    |
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille les scripts qui s’exécutent à l’arrêt.     |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille les clés qui sont chargées avant que l’utilisateur ne se connecte à son compte Windows. La clé est utilisée pour les programmes 32 bits s’exécutant sur des ordinateurs 64 bits.    |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille les modifications apportées aux paramètres d’application.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille les entrées courantes de démarrage automatique qui se raccordent directement à l’Explorateur Windows et s’exécutent généralement in-process avec Explorer.exe.|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille les entrées courantes de démarrage automatique qui se raccordent directement à l’Explorateur Windows et s’exécutent généralement in-process avec Explorer.exe.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille l’inscription du gestionnaire de superposition d’image sur une icône.|
|**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille l’inscription du gestionnaire de superposition d’image sur une icône pour les programmes 32 bits s’exécutant sur des ordinateurs 64 bits.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille la présence de nouveaux plug-ins d’objet application d’assistance du navigateur pour Internet Explorer. Utilisé pour accéder à l’objet DOM (Document Object Model) de la page actuelle et contrôler la navigation.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille la présence de nouveaux plug-ins d’objet application d’assistance du navigateur pour Internet Explorer. Utilisé pour accéder à l’objet DOM (Document Object Model) de la page actuelle et contrôler la navigation pour les programmes 32 bits s’exécutant sur des ordinateurs 64 bits.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille les nouvelles extensions Internet Explorer, notamment les menus d’outils et boutons de barre d’outils personnalisés.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille les nouvelles extensions Internet Explorer, notamment les menus d’outils et boutons de barre d’outils personnalisés pour les programmes 32 bits s’exécutant sur des ordinateurs 64 bits.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille les pilotes 32 bits associés à wavemapper, wave1 et wave2, msacm.imaadpcm, .msadpcm, .msgsm610 et vidc. Analogue à la section [pilotes] du fichier SYSTEM.INI.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille les pilotes 32 bits associés à wavemapper, wave1 et wave2, msacm.imaadpcm, .msadpcm, .msgsm610 et vidc pour les programmes 32 bits s’exécutant sur des ordinateurs 64 bits. Analogue à la section [pilotes] du fichier SYSTEM.INI.|
> |**HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls**|
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille la liste des DLL système connues ou couramment utilisées. Ce système empêche quiconque d’exploiter des autorisations faibles de répertoire d’application en déposant des versions de type cheval de Troie des DLL système.|
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify**|
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille la liste des packages capables de recevoir des notifications d’événements de la part de Winlogon, le modèle de prise en charge de l’ouverture de session interactive du système d’exploitation Windows.|

## <a name="use-change-tracking"></a>Utilisation du suivi des modifications

Une fois que la solution est activée, vous pouvez afficher le résumé des modifications de vos ordinateurs surveillés en sélectionnant **Change Tracking** sous **GESTION DE LA CONFIGURATION** dans votre compte Automation.

Vous pouvez afficher les modifications apportées à vos ordinateurs, puis explorer les détails de chaque événement. Des listes déroulante sont disponibles en haut du graphique pour les informations en fonction du type de modification et des intervalles de temps. Vous pouvez également cliquer et faire glisser le curseur sur le graphique pour sélectionner un intervalle de temps personnalisé.

![image du tableau de bord de suivi des modifications](./media/automation-change-tracking/change-tracking-dash01.png)

Cliquer sur une modification ou un événement permet de faire apparaître des informations détaillées s’y rapportant. Comme vous pouvez le voir à partir de l’exemple, le type de démarrage du service est passé de Manuel à Auto.

![image des détails du suivi des modifications](./media/automation-change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Rechercher dans les journaux

En plus des détails fournis dans le portail, des recherches peuvent être effectuées dans les journaux. Avec la page **Change Tracking** ouverte, cliquez sur **Log Analytics** pour ouvrir la page **Recherche dans les journaux**.

### <a name="sample-queries"></a>Exemples de requêtes

Le tableau suivant fournit des exemples de recherches dans les journaux d’enregistrements de modification collectés par cette solution :

|Requête  |Description  |
|---------|---------|
|ConfigurationData<br>&#124; where   ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"<br>&#124; where SvcState == "Stopped"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | Affiche les enregistrements d’inventaire les plus récents des services Windows qui ont été définis sur Auto, mais qui ont été signalés comme étant arrêtés.<br>Les résultats se limitent à l’enregistrement le plus récent pour ce SoftwareName et ce Computer.      |
|ConfigurationChange<br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Removed"<br>&#124; order by TimeGenerated desc|Affiche les enregistrements de modification des logiciels supprimés.|

## <a name="next-steps"></a>Étapes suivantes

Consultez le didacticiel sur Change Tracking pour en savoir plus sur l’utilisation de la solution :

> [!div class="nextstepaction"]
> [Dépanner les modifications apportées à votre environnement](automation-tutorial-troubleshoot-changes.md)

* Utilisez les [recherches de journaux dans Log Analytics](../log-analytics/log-analytics-log-searches.md) pour afficher les données détaillées du suivi des modifications.
