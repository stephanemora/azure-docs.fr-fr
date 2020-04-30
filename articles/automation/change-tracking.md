---
title: Suivre les modifications avec Azure Automation
description: La solution Change Tracking permet d’identifier les modifications apportées aux logiciels et au service Windows dans votre environnement.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/28/2019
ms.topic: conceptual
ms.openlocfilehash: d84566c7680081561f60d4825f25a9ce19e02b24
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682975"
---
# <a name="track-environment-changes-with-change-tracking"></a>Suivre les modifications d'environnement avec Change Tracking

Cet article vous aide à utiliser la solution Change Tracking pour identifier facilement les modifications apportées dans votre environnement. La solution suit les modifications de configuration suivantes pour vous aider à identifier les problèmes de fonctionnement :

- Logiciels Windows
- Logiciel Linux (packages)
    >[!NOTE]
    >Change Tracking suit uniquement les logiciels gérés avec le gestionnaire de package de la distribution.

- Fichiers Windows et Linux
- Clés de Registre Windows
- Services Windows
- Démons Linux

Une fois que la solution est activée, vous pouvez afficher le résumé des modifications de vos ordinateurs surveillés en sélectionnant **Change Tracking** sous **Gestion de la configuration** dans votre compte Automation.

> [!NOTE]
> Azure Automation Change Tracking suit les modifications apportées aux machines virtuelles. Pour suivre les modifications de propriété Azure Resource Manager, consultez [Historique des modifications](../governance/resource-graph/how-to/get-resource-changes.md) d'Azure Resource Graph.

Vous pouvez afficher les modifications apportées à vos ordinateurs, puis explorer les détails de chaque événement. Des listes déroulante sont disponibles en haut du graphique pour les informations en fonction du type de modification et des intervalles de temps. Vous pouvez également cliquer et faire glisser le curseur sur le graphique pour sélectionner un intervalle de temps personnalisé. **Type de modification** peut présenter l'une des valeurs suivantes : **Événements**, **Démons**, **Fichiers**, **Registre**, **Logiciels**, **Services Windows**. La catégorie affiche le type de modification parmi les suivant : **Ajouté**, **Modifié** ou **Supprimé**.

![image du tableau de bord de suivi des modifications](./media/change-tracking/change-tracking-dash01.png)

Cliquer sur une modification ou un événement permet de faire apparaître des informations détaillées s’y rapportant. Comme vous pouvez le voir à partir de l’exemple, le type de démarrage du service est passé de Manuel à Auto.

![image des détails du suivi des modifications](./media/change-tracking/change-tracking-details.png)

Les modifications apportées aux logiciels installés, aux services Windows, aux fichiers et au Registre Windows ainsi qu’aux démons Linux sur les serveurs supervisés sont envoyées au service cloud Azure Monitor pour traitement. La logique est appliquée aux données reçues et le service cloud enregistre les données. En utilisant les informations du tableau de bord de suivi des modifications, vous pouvez facilement voir les modifications apportées à votre infrastructure de serveur.

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

### <a name="windows-operating-systems"></a>Systèmes d’exploitation Windows

Les versions suivantes du système d’exploitation Windows sont officiellement prises en charge pour l’agent Windows :

* Windows Server 2008 R2 ou version ultérieure

### <a name="linux-operating-systems"></a>Systèmes d’exploitation Linux

Les distributions Linux suivantes sont officiellement prises en charge. Toutefois, l’agent Linux peut également s’exécuter sur d’autres distributions, qui ne se trouvent pas dans la liste. Sauf indication contraire, toutes les versions mineures sont prises en charge pour chaque version majeure répertoriée.

#### <a name="64-bit"></a>64 bits

* CentOS 6 et 7
* Amazon Linux 2017.09
* Oracle Linux 6 et 7
* Red Hat Enterprise Linux Server 6 et 7
* Debian GNU/Linux 8 et 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS et 18.04 LTS
* SUSE Linux Enterprise Server 12

#### <a name="32-bit"></a>32 bits

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 et 9
* Ubuntu Linux 14.04 LTS et 16.04 LTS

## <a name="limitations"></a>Limites

Actuellement, la solution Change Tracking ne prend pas en charge les éléments suivants :

* Récursivité pour le suivi du Registre Windows
* Systèmes de fichiers réseau
* Méthodes d'installation différentes
* Fichiers * **. exe** pour Windows

Autres limitations :

* La colonne **Taille maximale des fichiers** et ses valeurs ne sont pas utilisées dans l’implémentation actuelle.
* Si vous collectez plus de 2500 fichiers dans le cycle de collecte de 30 minutes, les performances de la solution peuvent être dégradées.
* Lorsque le trafic réseau est élevé, l’affichage des enregistrements de modifications peut prendre jusqu’à six heures.
* Si vous modifiez la configuration lorsqu’un ordinateur est arrêté, l’ordinateur risque de publier des modifications appartenant à la configuration précédente.

## <a name="known-issues"></a>Problèmes connus

La solution Change Tracking connaît les problèmes suivants :

* Les mises à jour de correctif logiciel ne sont pas collectées sur les machines Windows 2016 Core RS3.
* Les démons Linux peuvent indiquer un état modifié, même si aucune modification n’est intervenue. Cela est dû à la façon dont le champ `SvcRunLevels` est capturé.

## <a name="network-requirements"></a>Configuration requise pour le réseau

Change Tracking requiert spécifiquement les adresses suivantes. Les communications vers ces adresses utilisent le port 443.

|Azure (public)  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|\* .azure-automation.net|*.azure-automation.us|

## <a name="wildcard-recursion-and-environment-settings"></a>Caractère générique, récursivité et paramètres d’environnement

La récursivité vous permet de spécifier des caractères génériques pour simplifier le suivi entre les répertoires, et des variables d’environnement pour vous permettre d’effectuer le suivi de fichiers entre les environnements avec plusieurs noms de lecteurs ou des noms de lecteurs dynamiques. La liste suivante répertorie les informations courantes que vous devez connaître lors de la configuration de la récursivité :

* Les caractères génériques sont requis pour effectuer le suivi de plusieurs fichiers.
* Les caractères génériques ne peuvent être utilisés que dans le dernier segment d’un chemin d’accès, par exemple, c:\folder\\file* ou /etc/*.conf.
* Si le chemin d’accès d’une variable d’environnement n’est pas valide, la validation réussit, mais ce chemin d’accès échoue lors de l’exécution de l’inventaire.
* Évitez les chemins d’accès généraux lors de la définition du chemin d’accès, auquel cas un trop grand nombre de dossiers peuvent être parcourus.

## <a name="change-tracking-data-collection-details"></a>Détails de la collecte de données de suivi des modifications

Le tableau suivant indique la fréquence de collecte de données selon les types de modification. Pour chaque type de capture instantanée de données, l’état actuel est également actualisé au moins toutes les 24 heures :

| **Type de modification** | **Fréquence** |
| --- | --- |
| Registre Windows | 50 minutes |
| Fichier Windows | 30 minutes |
| Fichier Linux | 15 minutes |
| Services Windows | 10 secondes à 30 minutes</br> Valeur par défaut : 30 minutes |
| Démons Linux | 5 minutes |
| Logiciels Windows | 30 minutes |
| Logiciels Linux | 5 minutes |

Le tableau suivant montre les limites des éléments suivis par machine pour Change Tracking.

| **Ressource** | **Limite**| **Remarques** |
|---|---|---|
|Fichier|500||
|Registre|250||
|Logiciels Windows|250|N’inclut pas les correctifs logiciels|
|Packages Linux|1250||
|Services|250||
|Daemon|250||

La consommation moyenne de données Log Analytics d'une machine utilisant la solution Change Tracking est d'environ 40 Mo par mois. Cette valeur est approximative et sujette à modification en fonction de votre environnement. Nous vous recommandons de surveiller votre environnement pour connaître votre consommation exacte.

### <a name="windows-service-tracking"></a>Suivi du service Windows

Pour les services Windows, la fréquence de collecte par défaut est de 30 minutes. Pour configurer la fréquence, accédez à **Change Tracking**. Sous l'option **Modifier les paramètres** de l'onglet **Services Windows**, un curseur vous permet de modifier la fréquence de collecte des services Windows (de 10 secondes à 30 minutes). Déplacez la barre du curseur sur la fréquence de votre choix (celle-ci est automatiquement enregistrée).

![Curseur des services Windows](./media/change-tracking/windowservices.png)

L’agent effectue uniquement le suivi des modifications, ce qui permet d’optimiser ses performances. Lorsqu'un seuil élevé est défini, certaines modifications peuvent être omises si le service est rétabli à son état d'origine. Le fait de définir une fréquence moins élevée vous permet d’intercepter les modifications susceptibles d’être omises.

> [!NOTE]
> Même si l’agent peut enregistrer les modifications toutes les 10 secondes, les données mettent quelques minutes à s’afficher dans le portail. Les modifications effectuées pendant l’affichage des données dans le portail continuent d’être suivies et enregistrées.

### <a name="registry-key-change-tracking"></a>Suivi des modifications des clés de Registre

Le contrôle des modifications apportées aux clés de Registre a pour objectif d’identifier les points d’extension où peuvent s’activer du code tiers et des logiciels malveillants. La liste suivante présente les clés de Registre préconfigurées. Ces clés sont configurées, mais pas activées. Pour suivre ces clés de Registre, vous devez les activer.

> [!div class="mx-tdBreakAll"]
> |Clé de Registre | Objectif |
> |---------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Surveille les entrées courantes de démarrage automatique qui se raccordent directement à l’Explorateur Windows et s’exécutent généralement in-process avec Explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Surveille les scripts qui s’exécutent au démarrage.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Surveille les scripts qui s’exécutent à l’arrêt.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Surveille les clés qui sont chargées avant que l’utilisateur ne se connecte à son compte Windows. La clé est utilisée pour les programmes 32 bits s’exécutant sur des ordinateurs 64 bits.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Surveille les modifications apportées aux paramètres d’application.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Surveille les entrées courantes de démarrage automatique qui se raccordent directement à l’Explorateur Windows et s’exécutent généralement in-process avec Explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Surveille les entrées courantes de démarrage automatique qui se raccordent directement à l’Explorateur Windows et s’exécutent généralement in-process avec Explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Surveille l’enregistrement du gestionnaire des icônes de recouvrement.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Surveille l’enregistrement du gestionnaire des icônes de recouvrement pour les programmes 32 bits s’exécutant sur des ordinateurs 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Surveille la présence de nouveaux plug-ins d’objet application d’assistance du navigateur pour Internet Explorer. Utilisé pour accéder à l’objet DOM (Document Object Model) de la page actuelle et contrôler la navigation.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Surveille la présence de nouveaux plug-ins d’objet application d’assistance du navigateur pour Internet Explorer. Utilisé pour accéder à l’objet DOM (Document Object Model) de la page actuelle et contrôler la navigation pour les programmes 32 bits s’exécutant sur des ordinateurs 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Surveille les nouvelles extensions Internet Explorer, notamment les menus d’outils personnalisés et les boutons de barre d’outils personnalisés.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Surveille les nouvelles extensions Internet Explorer, notamment les menus d’outils personnalisés et les boutons de barre d’outils personnalisés pour les programmes 32 bits s’exécutant sur des ordinateurs 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Surveille les pilotes 32 bits associés à wavemapper, wave1 et wave2, msacm.imaadpcm, .msadpcm, .msgsm610 et vidc. Analogue à la section [pilotes] du fichier SYSTEM.INI.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Surveille les pilotes 32 bits associés à wavemapper, wave1 et wave2, msacm.imaadpcm, .msadpcm, .msgsm610 et vidc pour les programmes 32 bits s’exécutant sur des ordinateurs 64 bits. Analogue à la section [pilotes] du fichier SYSTEM.INI.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Surveille la liste des DLL système connues ou couramment utilisées ; ce système empêche quiconque d’exploiter des autorisations faibles de répertoire d’application en déposant des versions de type cheval de Troie des DLL système.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Surveille la liste des packages capables de recevoir des notifications d’événements de la part de Winlogon, le modèle de prise en charge de l’ouverture de session interactive du système d’exploitation Windows.

## <a name="enable-change-tracking"></a><a name="onboard"></a>Activer Change Tracking

Pour commencer à suivre les modifications, vous devez activer la solution Change Tracking. Il existe de nombreuses façons d’intégrer les machines à Change Tracking. Voici des méthodes recommandées et prises en charge pour intégrer la solution.

* [Depuis une machine virtuelle](automation-onboard-solutions-from-vm.md)
* [Depuis plusieurs machines](automation-onboard-solutions-from-browse.md)
* [Depuis votre compte Automation](automation-onboard-solutions-from-automation-account.md)
* [Avec un runbook Azure Automation](automation-onboard-solutions.md)

## <a name="configure-change-tracking"></a>Configurer Change Tracking

Pour découvrir comment intégrer des ordinateurs à la solution, consultez [Intégration de solutions Automation](automation-onboard-solutions-from-automation-account.md). Une fois que vous avez une machine intégrée à la solution Change Tracking, vous pouvez configurer les éléments à suivre. Lorsque vous activez le suivi d’un nouveau fichier ou d’une nouvelle clé de Registre, ceux-ci sont activés pour Change Tracking.

Pour suivre les modifications apportées à des fichiers sur Windows et Linux, les hachages MD5 de fichiers sont utilisés. Ces hachages sont ensuite utilisés pour détecter si une modification a été apportée depuis le dernier inventaire.

## <a name="enable-file-integrity-monitoring-in-azure-security-center"></a>Activer le monitoring d'intégrité de fichier dans Azure Security Center

Azure Security Center a ajouté le monitoring d'intégrité de fichier (FIM) basé sur Azure Change Tracking. Alors que FIM surveille uniquement les fichiers et les registres, la solution Change Tracking complète comprend également les éléments suivants :

- Modifications de logiciel
- Services Windows
- Démons Linux

Si vous avez déjà activé FIM et que vous souhaitez essayer la solution complète de suivi des modifications, vous devez effectuer les étapes suivantes. Vos paramètres ne sont pas supprimés par ce processus.

> [!NOTE]
> L’activation de la solution complète de suivi des modifications peut entraîner des frais supplémentaires. Pour plus d’informations, consultez [Tarification d’Automation](https://azure.microsoft.com/pricing/details/automation/).

1. Supprimez la solution de surveillance en accédant à l’espace de travail et en la recherchant dans la [liste des solutions de surveillance installées](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions).
2. Cliquez sur le nom de la solution pour ouvrir la page de résumé correspondante, puis cliquez sur Supprimer, comme indiqué dans [Supprimer une solution de surveillance](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).
3. Pour réactiver la solution, accédez au compte Automation et sélectionnez **Change Tracking** sous **Gestion de la configuration**.
4. Confirmez les détails de votre paramètre d’espace de travail, puis cliquez sur **Activer**.

## <a name="configure-file-content-change-tracking"></a>Configurer File Content Change Tracking

Avec File Content Change Tracking, vous pouvez voir le contenu d’un fichier avant et après modification. Cette fonctionnalité est disponible pour les fichiers Windows et Linux. Pour chaque modification apportée à un fichier, le contenu du fichier est stocké dans un compte de stockage. Le fichier est affiché avant et après la modification, inline ou côte à côte. Pour plus d’informations, consultez [Afficher le contenu d’un fichier suivi](change-tracking-file-contents.md).

![Afficher les modifications d’un fichier](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="configure-windows-registry-keys-to-track"></a>Configurer les clés de Registre Windows pour effectuer le suivi

Utilisez les étapes suivantes pour configurer le suivi des clés de Registre sur des ordinateurs Windows :

1. Dans votre compte Automation, sélectionnez **Change Tracking** sous **Gestion de la configuration**. Cliquez sur **Modifier les paramètres** (le symbole engrenage).
2. Dans la page Change Tracking, sélectionnez **Registre Windows**, puis cliquez sur **+ Ajouter** pour ajouter une nouvelle clé de Registre à suivre.
3. Dans la fenêtre **Ajouter le Registre Windows pour le suivi des modifications**, entrez les informations correspondant à la clé à suivre et cliquez sur **Enregistrer**.

|Propriété  |Description  |
|---------|---------|
|activé     | Détermine si le paramètre est appliqué.        |
|Item Name     | Nom convivial de la clé de Registre à suivre.        |
|Groupe     | Nom de groupe pour le regroupement logique des clés de Registre.        |
|Clé de Registre Windows   | Chemin dans lequel rechercher la clé de Registre. Par exemple : « HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup »      |

## <a name="configure-file-tracking-on-windows"></a>Configurer le suivi des fichiers sur Windows

Utilisez les étapes suivantes pour configurer le suivi des fichiers sur des ordinateurs Windows :

1. Dans votre compte Automation, sélectionnez **Change Tracking** sous **Gestion de la configuration**. Cliquez sur **Modifier les paramètres** (le symbole engrenage).
2. Dans la page Change Tracking, sélectionnez **Fichiers Windows**, puis cliquez sur **+ Ajouter** pour ajouter un nouveau fichier à suivre.
3. Dans la fenêtre **Ajouter le fichier Windows pour le suivi des modifications**, entrez les informations du fichier à suivre et cliquez sur **Enregistrer**.

|Propriété  |Description  |
|---------|---------|
|activé     | True si le paramètre est appliqué, et false dans le cas contraire.        |
|Item Name     | Nom convivial du fichier à suivre.        |
|Groupe     | Nom de groupe pour le regroupement logique des fichiers.        |
|Entrer le chemin     | Chemin d’accès pour rechercher le fichier, par exemple, **c:\temp\\\*.txt**<br>Vous pouvez également utiliser des variables d’environnement telles que `%winDir%\System32\\\*.*`.       |
|Récursivité     | True si la récursivité est utilisée lorsque vous recherchez l’élément à suivre, et False dans le cas contraire.        |
|Télécharger le contenu du fichier pour tous les paramètres| True pour charger le contenu du fichier d'après les modifications suivies, et False dans le cas contraire.|

## <a name="configure-file-tracking-on-linux"></a>Configurer le suivi des fichiers sur Linux

Utilisez les étapes ci-dessous pour configurer le suivi des fichiers sur des ordinateurs Linux :

1. Dans votre compte Automation, sélectionnez **Change Tracking** sous **Gestion de la configuration**. Cliquez sur **Modifier les paramètres** (le symbole engrenage).
2. Dans la page Change Tracking, sélectionnez **Fichiers Linux**, puis cliquez sur **+ Ajouter** pour ajouter un nouveau fichier à suivre.
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
|Liens     | Ce paramètre détermine le traitement des liens symboliques lorsque vous parcourez les répertoires.<br> **Ignorer** : ignore les liens symboliques et n'inclut pas les fichiers/répertoires référencés.<br>**Suivre** : suit les liens symboliques pendant les opérations de récursivité et inclut aussi les fichiers/répertoires référencés.<br>**Gérer** : suit les liens symboliques et autorise la modification du contenu retourné.     |
|Télécharger le contenu du fichier pour tous les paramètres| Active ou désactive le chargement du contenu du fichier pour le suivi des modifications. Options disponibles : **True** ou **False**.|

> [!NOTE]
> L’option permettant de « Gérer » les liens n’est pas recommandée. L’extraction du contenu du fichier n’est pas prise en charge.

## <a name="search-logs"></a>Rechercher des journaux d’activité

En matière d'enregistrements de modification, vous pouvez effectuer différentes recherches dans les journaux. Avec la page Change Tracking ouverte, cliquez sur **Log Analytics** pour ouvrir la page Journaux. Le tableau suivant fournit des exemples de recherches dans les journaux d’enregistrements de modification collectés par cette solution :

|Requête  |Description  |
|---------|---------|
|ConfigurationData<br>&#124; where   ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"<br>&#124; where SvcState == "Stopped"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | Affiche les enregistrements d’inventaire les plus récents des services Windows qui ont été définis sur Auto, mais qui ont été signalés comme étant arrêtés.<br>Les résultats se limitent à l’enregistrement le plus récent pour ce SoftwareName et ce Computer.      |
|ConfigurationChange<br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Removed"<br>&#124; order by TimeGenerated desc|Affiche les enregistrements de modification des logiciels supprimés.|

## <a name="alert-on-changes"></a>Alerte sur les modifications

L'une des principales caractéristiques de la solution Change Tracking consiste à alerter sur l'état de la configuration et sur les modifications apportées à l'état de la configuration de votre environnement hybride. L’exemple suivant montre que le fichier **C:\windows\system32\drivers\etc\hosts** a été modifié sur une machine. Ce fichier est important car Windows l'utilise pour associer des noms d'hôtes à des adresses IP. Cette opération prime sur le DNS, ce qui peut entraîner des problèmes de connectivité ou la redirection du trafic vers des sites web malveillants ou dangereux.

![Graphique montrant la modification du fichier Hosts](./media/change-tracking/changes.png)

Pour analyser cette modification plus en détail, accédez à Recherche dans les journaux en cliquant sur **Log Analytics**. Une fois dans Recherche dans les journaux, recherchez les modifications apportées au contenu du fichier Hosts à l'aide de la requête `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`. Cette requête permet de rechercher les modifications apportées au contenu des fichiers dont le chemin d'accès complet contient le mot « hosts ». Vous pouvez également rechercher un fichier spécifique en remplaçant le chemin d'accès par sa forme complète (par exemple, `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`).

Une fois que la requête a renvoyé les résultats souhaités, cliquez sur **Nouvelle règle d'alerte** dans la recherche dans les journaux pour ouvrir la page de création d'alerte. Vous pouvez également accéder à cette expérience via **Azure Monitor** sur le portail Azure. 

Vérifiez à nouveau votre requête et modifiez la logique d’alerte. Pour cet exemple, vous souhaitez que l'alerte soit déclenchée à la moindre modification détectée sur l'ensemble des machines de l'environnement.

![Image illustrant la requête de modification pour le suivi des modifications apportées au fichier Hosts](./media/change-tracking/change-query.png)

Une fois la logique de condition définie, attribuez des groupes d'actions pour répondre à l'alerte déclenchée. J'ai ici configuré les actions suivantes : envoi d'e-mails et création d'un ticket ITSM.  De nombreuses autres actions utiles peuvent également être entreprises, telles que le déclenchement d’une fonction Azure, d’un runbook Automation, d’un webhook ou d’une application logique.

![Image illustrant la configuration d'un groupe d'actions pour alerter de la modification](./media/change-tracking/action-groups.png)

Une fois tous les paramètres et la logique définis, nous pouvons appliquer l'alerte à l'environnement.

### <a name="alert-suggestions"></a>Suggestions d'alertes

L'alerte relative aux modifications apportées au fichier hosts est une bonne application des alertes disponibles pour les données Change Tracking or Inventory, mais il existe de nombreux autres scénarios d'alerte, y compris les cas définis dans la section ci-dessous ainsi que leurs exemples de requêtes.

|Requête  |Description  |
|---------|---------|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Files" and FileSystemPath contains " c:\\windows\\system32\\drivers\\"|Utile pour le suivi des modifications apportées aux fichiers critiques du système|
|ConfigurationChange <br>&#124; where FieldsChanged contains "FileContentChecksum" and FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|Utile pour le suivi des modifications apportées aux fichiers de configuration de clés|
|ConfigurationChange <br>&#124; where ConfigChangeType == "WindowsServices" and SvcName contains "w3svc" and SvcState == "Stopped"|Utile pour le suivi des modifications apportées aux services critiques du système|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Daemons" and SvcName contains "ssh" and SvcState != "Running"|Utile pour le suivi des modifications apportées aux services critiques du système|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Added"|Utile pour les environnements nécessitant des configurations logicielles verrouillées|
|ConfigurationData <br>&#124; where SoftwareName contains "Monitoring Agent" and CurrentVersion != "8.0.11081.0"|Utile pour voir sur quelles machines une version obsolète ou non conforme d'un logiciel est installée. Présente le dernier état de configuration signalé, pas les modifications.|
|ConfigurationChange <br>&#124; où RegistryKey == @"HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| Utile pour le suivi des modifications apportées aux clés antivirus essentielles|
|ConfigurationChange <br>&#124; où RegistryKey contient @"HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\SharedAccess\\Parameters\\FirewallPolicy"| Utile pour le suivi des modifications apportées aux paramètres de pare-feu|

## <a name="next-steps"></a>Étapes suivantes

Consultez le didacticiel sur Change Tracking pour en savoir plus sur l’utilisation de la solution :

> [!div class="nextstepaction"]
> [Dépanner les modifications apportées à votre environnement](automation-tutorial-troubleshoot-changes.md)

* Effectuez des [recherches dans les journaux Azure Monitor](../log-analytics/log-analytics-log-searches.md) pour afficher les données détaillées du suivi des modifications.
