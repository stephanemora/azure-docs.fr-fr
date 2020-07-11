---
title: Présentation de la fonctionnalité Suivi des modifications et inventaire dans Azure Automation
description: Cet article présente la fonctionnalité Change Tracking and Inventory, qui permet d’identifier les modifications apportées aux logiciels et aux services Microsoft de votre environnement.
services: automation
ms.subservice: change-inventory-management
ms.date: 06/08/2020
ms.topic: conceptual
ms.openlocfilehash: 192fd0fe73a34ca4d6ffc49badeac7ca8a080793
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185583"
---
# <a name="change-tracking-and-inventory-overview"></a>Présentation de la fonctionnalité Suivi des modifications et inventaire

Cet article présente Suivi des modifications et inventaire dans Azure Automation. Cette fonctionnalité effectue le suivi des modifications apportées à l’infrastructure de machines virtuelles et de serveurs pour vous aider à identifier les problèmes opérationnels et environnementaux liés aux logiciels gérés par le gestionnaire de package de distribution. Les éléments suivis par Suivi des modifications et inventaire sont les suivants : 

- Logiciels Windows
- Logiciel Linux (packages)
- Fichiers Windows et Linux
- Clés de Registre Windows
- Services Microsoft
- Démons Linux

> [!NOTE]
> Pour suivre les modifications de propriétés Azure Resource Manager, consultez l’[historique des modifications](../governance/resource-graph/how-to/get-resource-changes.md) d’Azure Resource Graph.

Suivi des modifications et inventaire obtient ses données à partir d’Azure Monitor. Les machines virtuelles connectées à des espaces de travail Log Analytics utilisent des agents Log Analytics pour collecter des données sur les modifications apportées aux logiciels installés, aux services Microsoft, au registre et aux fichiers Windows, ainsi qu’aux démons Linux sur les serveurs analysés. Lorsque des données sont disponibles, les agents les envoient à Azure Monitor pour qu’elles soient traitées. Azure Monitor applique une logique aux données reçues, les enregistre et les rend disponibles. 

> [!NOTE]
> Pour utiliser la fonctionnalité Suivi des modifications et inventaire, vous devez placer toutes vos machines virtuelles dans le même abonnement et la même région du compte Automation.

Suivi des modifications et inventaire ne prend actuellement pas en charge les éléments suivants :

- Récursivité pour le suivi du Registre Windows
- Systèmes de fichiers réseau
- Méthodes d'installation différentes
- Fichiers * **. exe** pour Windows

Autres limitations :

- La colonne **Taille maximale des fichiers** et ses valeurs ne sont pas utilisées dans l’implémentation actuelle.
- Si vous collectez plus de 2 500 fichiers dans le cycle de collecte de 30 minutes, les performances de Change Tracking and Inventory peuvent être dégradées.
- Lorsque le trafic réseau est élevé, l’affichage des enregistrements de modifications peut prendre jusqu’à six heures.
- Si vous modifiez une configuration quand un ordinateur est arrêté, l’ordinateur risque de publier des modifications appartenant à la configuration précédente.

Suivi des modifications et inventaire présente actuellement les problèmes suivants :

- Les mises à jour de correctif logiciel ne sont pas collectées sur les machines Windows 2016 Core RS3.

- Les démons Linux peuvent indiquer un état modifié même si aucune modification n’est intervenue. Ce problème est dû à la façon dont les données `SvcRunLevels` dans le journal [ConfigurationChange](/azure/azure-monitor/reference/tables/configurationchange) d’Azure Monitor sont capturées.

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

Suivi des modifications et inventaire est pris en charge sur tous les systèmes d’exploitation qui répondent aux exigences de l’agent Log Analytics. Les versions officielles du système d’exploitation sont Windows Server 2008 SP1 ou version ultérieure et Windows 7 SP1 ou version ultérieure. La fonctionnalité est aussi prise en charge sur un certain nombre de systèmes d’exploitation Linux. Pour les systèmes d’exploitation prenant en charge Log Analytics, consultez [Vue d’ensemble de l’agent Log Analytics](../azure-monitor/platform/log-analytics-agent.md).

Pour comprendre la configuration requise du client pour le protocole TLS 1.2, consultez [Application de TLS 1.2 pour Azure Automation](automation-managing-data.md#tls-12-enforcement-for-azure-automation).

## <a name="network-requirements"></a>Configuration requise pour le réseau

Suivi des modifications et inventaire nécessite spécifiquement les adresses réseau indiquées dans le tableau suivant. Les communications vers ces adresses utilisent le port 443.

|Azure (public)  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|\* .azure-automation.net | *.azure-automation.us|

## <a name="change-tracking-and-inventory-user-interface"></a>Interface utilisateur de Suivi des modifications et inventaire

Utilisez Suivi des modifications et inventaire dans le portail Azure afin d’afficher le résumé des modifications pour les ordinateurs supervisés. La fonctionnalité est disponible en sélectionnant **Suivi des modifications** ou **Inventaire** sous **Gestion de la configuration** dans votre compte Automation.  

![Tableau de bord de Suivi des modifications](./media/change-tracking/change-tracking-dash01.png)

Des listes déroulantes sont disponibles en haut du tableau de bord pour limiter les informations en fonction du type de modification et des intervalles de temps. Vous pouvez également cliquer et faire glisser le curseur sur le graphique pour sélectionner un intervalle de temps personnalisé. 

Vous pouvez cliquer sur une modification ou un événement pour afficher ses détails. Les types de modification disponibles sont les suivants :

- Événements
- Démons
- Fichiers
- Registre
- Logiciel
- Services Microsoft

Vous pouvez ajouter, modifier ou supprimer chaque modification. L’exemple ci-dessous montre que le type de démarrage d’un service est passé de Manuel à Auto.

![Détails sur Change Tracking and Inventory](./media/change-tracking/change-tracking-details.png)

## <a name="fim-support-in-azure-security-center"></a>Prise en charge FIM dans Azure Security Center

Suivi des modifications et inventaire utilise le [Monitoring d’intégrité de fichier dans Azure Security Centers](../security-center/security-center-file-integrity-monitoring.md). Le Monitoring d’intégrité de fichier supervise uniquement les fichiers et les registres, alors que la fonctionnalité Suivi des modifications et inventaire complète offre aussi le suivi des éléments suivants :

- Modifications de logiciel
- Services Microsoft
- Démons Linux

> [!NOTE]
> L’activation de la fonctionnalité complète Suivi des modifications et inventaire peut entraîner des frais supplémentaires. Consultez [Tarification Automation](https://azure.microsoft.com/pricing/details/automation/). Il est possible de supprimer FIM de la [liste des solutions de supervision installées](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions) disponible dans le portail Azure. Consultez [Supprimer une solution de supervision](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).

## <a name="tracking-of-file-changes"></a>Suivi des modifications de fichiers

Pour suivre les modifications apportées à des fichiers sur Windows et Linux, Suivi des modifications et inventaire utilise des hachages MD5 des fichiers. La fonctionnalité utilise les hachages pour détecter si des modifications ont été apportées depuis le dernier inventaire.

## <a name="tracking-of-file-content-changes"></a>Suivi des modifications de contenu de fichier

Change Tracking and Inventory vous permet d’afficher le contenu d’un fichier Windows ou Linux. Pour chaque modification apportée à un fichier, Suivi des modifications et inventaire stocke le contenu du fichier dans un [compte Stockage Azure](../storage/common/storage-account-create.md). Quand vous effectuez le suivi d’un fichier, vous pouvez afficher son contenu avant ou après une modification. Le contenu du fichier peut être affiché en ligne ou côte à côte. 

![Afficher les modifications d’un fichier](./media/change-tracking/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>Suivi des clés de Registre

Change Tracking and Inventory autorise la supervision des modifications apportées aux clés de Registre Windows. La supervision vous permet d’identifier les points d’extensibilité où du code tiers et des logiciels malveillants peuvent être activés. Le tableau suivant liste les clés de Registre préconfigurées (mais non activées). Pour effectuer le suivi de ces clés de Registre, vous devez les activer.

> [!div class="mx-tdBreakAll"]
> |Clé de Registre | Objectif |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Surveille les scripts qui s’exécutent au démarrage.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Surveille les scripts qui s’exécutent à l’arrêt.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Supervise les clés qui sont chargées avant que l’utilisateur se connecte à son compte Windows. La clé est utilisée pour les applications 32 bits s’exécutant sur des ordinateurs 64 bits.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Surveille les modifications apportées aux paramètres d’application.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Supervise les gestionnaires de menu contextuel qui se raccordent directement à l’Explorateur Windows et s’exécutent généralement in-process avec **explorer.exe**.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Supervise les gestionnaires de raccordement de copie qui se raccordent directement à l’Explorateur Windows et s’exécutent généralement in-process avec **explorer.exe**.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Surveille l’enregistrement du gestionnaire des icônes de recouvrement.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Supervise l’inscription du gestionnaire de superposition d’image sur une icône pour les applications 32 bits s’exécutant sur des ordinateurs 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Surveille la présence de nouveaux plug-ins d’objet application d’assistance du navigateur pour Internet Explorer. Utilisé pour accéder à l’objet DOM (Document Object Model) de la page actuelle et contrôler la navigation.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Surveille la présence de nouveaux plug-ins d’objet application d’assistance du navigateur pour Internet Explorer. Utilisée pour accéder à l’objet DOM (Document Object Model) de la page actuelle et contrôler la navigation pour les applications 32 bits s’exécutant sur des ordinateurs 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Surveille les nouvelles extensions Internet Explorer, notamment les menus d’outils personnalisés et les boutons de barre d’outils personnalisés.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Supervise les nouvelles extensions Internet Explorer, telles que les menus d’outils et boutons de barre d’outils personnalisés pour les applications 32 bits s’exécutant sur des ordinateurs 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Supervise les pilotes 32 bits associés à wavemapper, wave1 et wave2, msacm.imaadpcm, .msadpcm, .msgsm610 et vidc. Analogue à la section [pilotes] du fichier **system.ini**.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Supervise les pilotes 32 bits associés à wavemapper, wave1 et wave2, msacm.imaadpcm, .msadpcm, .msgsm610 et vidc pour les applications 32 bits s’exécutant sur des ordinateurs 64 bits. Analogue à la section [pilotes] du fichier **system.ini**.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Supervise la liste des DLL système connues ou couramment utilisées. La supervision empêche quiconque d’exploiter des autorisations faibles de répertoire d’application en déposant des versions de type cheval de Troie des DLL système.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Supervise la liste des packages capables de recevoir des notifications d’événements de la part de **winlogon.exe**, le modèle de prise en charge de l’ouverture de session interactive de Windows.

## <a name="recursion-support"></a>Prise en charge de la récursivité

Suivi des modifications et inventaire prend en charge la récursivité, qui vous permet de spécifier des caractères génériques pour simplifier le suivi parmi les répertoires. La récursivité fournit également des variables d’environnement pour vous permettre d’effectuer le suivi des fichiers dans des environnements avec des noms de lecteurs multiples ou dynamiques. La liste suivante indique les informations courantes que vous devez connaître lors de la configuration de la récursivité :

- Les caractères génériques sont requis pour effectuer le suivi de plusieurs fichiers.

- Les caractères génériques ne peuvent être utilisés que dans le dernier segment d’un chemin de fichier, par exemple **c:\dossier\\fichier*** ou **/etc/*.conf**.

- Si le chemin d’une variable d’environnement n’est pas valide, la validation réussit, mais ce chemin échoue lors de l’exécution.

- Nous vous conseillons d’éviter les chemins généraux lors de la définition du chemin, car dans ce cas un trop grand nombre de dossiers peuvent être parcourus.

## <a name="change-tracking-and-inventory-data-collection"></a>Collecte de données dans Suivi des modifications et inventaire

Le tableau suivant indique la fréquence de collecte des données pour les types de modifications pris en charge par Suivi des modifications et inventaire. Pour chaque type, la capture instantanée de données de l’état actuel est également actualisée au moins toutes les 24 heures.

| **Modifier de type** | **Fréquence** |
| --- | --- |
| Registre Windows | 50 minutes |
| Fichier Windows | 30 minutes |
| Fichier Linux | 15 minutes |
| Services Microsoft | 10 secondes à 30 minutes</br> Valeur par défaut : 30 minutes |
| Démons Linux | 5 minutes |
| Logiciels Windows | 30 minutes |
| Logiciels Linux | 5 minutes |

Le tableau suivant montre les limites des éléments suivis par machine pour Suivi des modifications et inventaire.

| **Ressource** | **Limite** |
|---|---|---|
|Fichier|500|
|Registre|250|
|Logiciels Windows (correctifs non compris) |250|
|Packages Linux|1250|
|Services|250|
|Démons|250|

La consommation moyenne de données Log Analytics d’une machine utilisant Suivi des modifications et inventaire est d’environ 40 Mo par mois, selon votre environnement. Avec la fonctionnalité Utilisation et estimation des coûts de l’espace de travail Log Analytics, vous pouvez afficher les données ingérées par Change Tracking and Inventory dans un graphique d’utilisation. Utilisez cette vue pour évaluer votre utilisation des données et déterminer la manière dont elle se répercute sur votre facture. Voir [Comprendre votre utilisation et estimer les coûts](../azure-monitor/platform/manage-cost-storage.md#understand-your-usage-and-estimate-costs).

### <a name="microsoft-service-data"></a>Données de services Microsoft

Pour les services Microsoft, la fréquence de collecte par défaut est de 30 minutes. Vous pouvez configurer la fréquence à l’aide d’un curseur sous l’onglet **Services Microsoft** sous **Modifier les paramètres**.

![Curseur de services Microsoft](./media/change-tracking/windowservices.png)

Pour optimiser les performances, l’agent Log Analytics effectue uniquement le suivi des modifications. Quand un seuil élevé est défini, certaines modifications peuvent être omises si le service revient à son état d’origine. Le fait de définir une fréquence moins élevée vous permet d’intercepter les modifications susceptibles d’être omises.

> [!NOTE]
> Même si l’agent peut enregistrer les modifications toutes les 10 secondes, les données mettent quelques minutes à s’afficher dans le portail Azure. Les modifications apportées pendant ce laps de temps continuent à être suivies et enregistrées.

## <a name="support-for-alerts-on-configuration-state"></a>Prise en charge des alertes relatives à l’état de configuration

L’une des principales caractéristiques de Suivi des modifications et inventaire est sa capacité à alerter en cas de modifications apportées à l’état de la configuration de votre environnement hybride. De nombreuses actions utiles peuvent être déclenchées en réponse à des alertes, par exemple des actions sur des fonctions Azure, des runbooks Automation, des webhooks et autres. L’alerte en cas de modification apportée au fichier **c:\windows\system32\drivers\etc\hosts** pour une machine est une bonne application des alertes pour Change Tracking and Inventory. Il existe de nombreux autres scénarios d’alerte, notamment les scénarios de requête définis dans le tableau suivant.

|Requête  |Description  |
|---------|---------|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Files" and FileSystemPath contains " c:\\windows\\system32\\drivers\\"|Utile pour le suivi des modifications apportées aux fichiers critiques du système.|
|ConfigurationChange <br>&#124; where FieldsChanged contains "FileContentChecksum" and FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|Utile pour le suivi des modifications apportées aux fichiers de configuration de clés.|
|ConfigurationChange <br>&#124; where ConfigChangeType == "WindowsServices" and SvcName contains "w3svc" and SvcState == "Stopped"|Utile pour le suivi des modifications apportées aux services critiques du système|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Daemons" and SvcName contains "ssh" and SvcState!= "Running"|Utile pour le suivi des modifications apportées aux services critiques du système|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Added"|Utile pour les environnements nécessitant des configurations logicielles verrouillées.|
|ConfigurationData <br>&#124; where SoftwareName contains "Monitoring Agent" and CurrentVersion!= "8.0.11081.0"|Utile pour voir sur quelles machines une version obsolète ou non conforme d’un logiciel est installée. Cette requête indique le dernier état de configuration signalé, mais n’indique pas les modifications.|
|ConfigurationChange <br>&#124; où RegistryKey == @"HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| Utile pour le suivi des modifications apportées aux clés antivirus essentielles.|
|ConfigurationChange <br>&#124; où RegistryKey contient @"HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\SharedAccess\\Parameters\\FirewallPolicy"| Utile pour le suivi des modifications apportées aux paramètres de pare-feu.|

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment activer la fonctionnalité dans un compte Automation, consultez [Activer la fonctionnalité Suivi des modifications et inventaire dans un compte Automation](automation-enable-changes-from-auto-acct.md).

- Pour activer la fonctionnalité en parcourant la portail Azure, consultez [Activer la fonctionnalité Change Tracking and Inventory sur le portail Azure](automation-onboard-solutions-from-browse.md).

- Pour activer la fonctionnalité sur un runbook, consultez [Activer la fonctionnalité Suivi des modifications et inventaire sur un runbook](automation-enable-changes-from-runbook.md).

- Pour activer la fonctionnalité sur une machine virtuelle, consultez [Activer la fonctionnalité Suivi des modifications et inventaire sur une machine virtuelle](automation-enable-changes-from-vm.md).
