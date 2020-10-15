---
title: Gérer Suivi des modifications et inventaire dans Azure Automation
description: Cet article explique comment utiliser le Change Tracking and Inventory pour suivre les modifications apportées aux logiciels et aux services Microsoft dans votre environnement.
services: automation
ms.subservice: change-inventory-management
ms.date: 06/15/2020
ms.topic: conceptual
ms.openlocfilehash: eab509e389c074232526aa93fcebb72f3bc986c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185600"
---
# <a name="manage-change-tracking-and-inventory"></a>Gérer Change Tracking et Inventory

Quand vous ajoutez un fichier ou une clé de Registre à suivre, Azure Automation l’active pour [Change Tracking et Inventory](change-tracking.md). Cet article explique comment configurer le suivi, passer en revue les résultats du suivi et gérer les alertes en cas de détection de modifications.

Avant d’appliquer les procédures décrites dans cet article, vérifiez que vous avez activé Change Tracking et Inventory sur vos machines virtuelles à l’aide de l’une des techniques suivantes :

* [Activer Change Tracking et Inventory à partir d’un compte Automation](automation-enable-changes-from-auto-acct.md)
* [Activer Change Tracking et Inventory en parcourant le portail Azure](automation-enable-changes-from-browse.md)
* [Activer Change Tracking et Inventory à partir d’un runbook](automation-enable-changes-from-runbook.md)
* [Activer Change Tracking et Inventory à partir d’une machine virtuelle Azure](automation-enable-changes-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Limiter l’étendue du déploiement

La fonctionnalité Change Tracking et Inventory utilise une configuration d’étendue dans l’espace de travail pour cibler les ordinateurs devant recevoir des modifications. Pour plus d’informations, consultez [Limiter l’étendue du déploiement de Change Tracking et Inventory](automation-scope-configurations-change-tracking.md).

## <a name="track-files"></a>Suivre des fichiers

Vous pouvez utiliser Change Tracking et Inventory pour suivre les modifications apportées aux fichiers et aux dossiers/répertoires. Cette section vous explique comment configurer le suivi des fichiers sur Windows et Linux.

### <a name="configure-file-tracking-on-windows"></a>Configurer le suivi des fichiers sur Windows

Utilisez les étapes suivantes pour configurer le suivi des fichiers sur des ordinateurs Windows :

1. Dans votre compte Automation, sélectionnez **Change Tracking** sous **Gestion de la configuration**. 
2. Cliquez sur **Modifier les paramètres** (le symbole engrenage).
3. Dans la page Configuration de l’espace de travail, sélectionnez **Fichiers Windows**, puis cliquez sur **+ Ajouter** pour ajouter un nouveau fichier à suivre.
4. Dans le volet Ajouter le fichier Windows pour le suivi des modifications, entrez les informations du fichier ou du dossier à suivre, puis cliquez sur **Enregistrer**. Le tableau suivant définit les propriétés que vous pouvez utiliser pour les informations.

    |Propriété  |Description  |
    |---------|---------|
    |activé     | True si le paramètre est appliqué, False sinon.        |
    |Item Name     | Nom convivial du fichier à suivre.        |
    |Groupe     | Nom de groupe pour le regroupement logique des fichiers.        |
    |Entrer le chemin     | Chemin où rechercher le fichier, par exemple **c:\temp\\\*.txt**. Vous pouvez également utiliser des variables d’environnement telles que `%winDir%\System32\\\*.*`.       |
    |Type de chemin     | Type du chemin. Les valeurs possibles sont Fichier et Dossier.        |    
    |Récursivité     | True si la récursivité est utilisée pour rechercher l’élément à suivre, False sinon.        |    
    |Charger le contenu du fichier | True pour charger le contenu du fichier d'après les modifications suivies, et False dans le cas contraire.|

5. Veillez à affecter la valeur True à **Charger le contenu du fichier**. Ce paramètre active le suivi de contenu de fichier pour le chemin de fichier indiqué.

### <a name="configure-file-tracking-on-linux"></a>Configurer le suivi des fichiers sur Linux

Utilisez les étapes ci-dessous pour configurer le suivi des fichiers sur des ordinateurs Linux :

1. Dans votre compte Automation, sélectionnez **Change Tracking** sous **Gestion de la configuration**. 
2. Cliquez sur **Modifier les paramètres** (le symbole engrenage).
3. Dans la page Configuration de l’espace de travail, sélectionnez **Fichiers Linux**, puis cliquez sur **+ Ajouter** pour ajouter un nouveau fichier à suivre.
4. Dans le volet Ajouter le fichier Linux pour le suivi des modifications, entrez les informations correspondant au fichier ou répertoire à suivre et cliquez sur **Enregistrer**. Le tableau suivant définit les propriétés que vous pouvez utiliser pour les informations.

    |Propriété  |Description  |
    |---------|---------|
    |activé     | True si le paramètre est appliqué, False sinon.        |
    |Item Name     | Nom convivial du fichier à suivre.        |
    |Groupe     | Nom de groupe pour le regroupement logique des fichiers.        |
    |Entrer le chemin     | Chemin dans lequel rechercher le fichier. Exemple : **/etc/*.conf**.       |
    |Type de chemin     | Type du chemin. Les valeurs possibles sont Fichier et Répertoire.        |
    |Récursivité     | True si la récursivité est utilisée pour rechercher l’élément à suivre, False sinon.        |
    |Utiliser sudo     | True pour utiliser sudo afin de rechercher l’élément, False sinon.         |
    |Liens     | Paramètre qui détermine comment traiter les liens symboliques quand vous parcourez des répertoires. Les valeurs possibles sont les suivantes :<br> Ignorer : ignore les liens symboliques et n’inclut pas les fichiers/répertoires référencés.<br>Suivre : suit les liens symboliques pendant la récursivité et inclut également les fichiers/répertoires référencés.<br>Gérer : suit les liens symboliques et autorise la modification du contenu retourné.<br>**Remarque :** L’option Gérer n’est pas recommandée, car elle ne prend pas en charge la récupération de contenu de fichier.    |
    |Charger le contenu du fichier | True pour charger le contenu du fichier d'après les modifications suivies, et False dans le cas contraire. |

5. Veillez à affecter la valeur True à **Charger le contenu du fichier**. Ce paramètre active le suivi de contenu de fichier pour le chemin de fichier indiqué.

   ![Ajouter le fichier Linux](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>Effectuer le suivi du contenu du fichier

Le suivi de contenu de fichier vous permet de voir le contenu d’un fichier avant et après un changement suivi. La fonctionnalité enregistre le contenu du fichier dans un [compte de stockage](../storage/common/storage-account-overview.md) chaque fois qu’un changement a lieu. Voici quelques règles à suivre pour le suivi du contenu de fichier :

* Un compte de stockage standard utilisant le modèle de déploiement Resource Manager est nécessaire pour stocker le contenu du fichier. 
* N’utilisez pas de comptes de stockage de modèles de déploiement Premium et Classic. Voir [À propos des comptes Stockage Azure](../storage/common/storage-account-create.md).
* Vous pouvez connecter le compte de stockage à un seul compte Automation.
* [Change Tracking et Inventory](change-tracking.md) doit être activé dans votre compte Automation.

### <a name="enable-tracking-for-file-content-changes"></a>Activer le suivi des modifications de contenu de fichier

Effectuez les étapes suivantes pour activer le suivi des modifications apportées au contenu des fichiers :

1. Dans le portail Azure, ouvrez votre compte Automation, puis sélectionnez **Change Tracking** sous **Gestion de la configuration**.
2. Cliquez sur **Modifier les paramètres** (le symbole engrenage).
3. Sélectionnez **Contenu du fichier** et cliquez sur **Lier**. Cette sélection ouvre le volet Ajouter l’emplacement du contenu pour Change Tracking.

   ![Ajouter l’emplacement du contenu](./media/change-tracking-file-contents/enable.png)

4. Sélectionnez l’abonnement et le compte de stockage à utiliser pour stocker le contenu du fichier. 

5. Si vous voulez activer le suivi de contenu de fichier pour tous les fichiers suivis existants, sélectionnez **Activé** pour **Charger le contenu de fichier pour tous les paramètres**. Vous pourrez modifier ce paramètre pour chaque chemin de fichier ultérieurement.

   ![Définir le compte de stockage](./media/change-tracking-file-contents/storage-account.png)

6. Suivi des modifications et inventaire affiche le compte de stockage et les URI de signature d’accès partagé (SAS) quand il active le suivi des modifications de contenu de fichier. Les signatures expirent au bout de 365 jours, et vous pouvez les recréer en cliquant sur **Regénérer**.

   ![Répertorier les clés de compte](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Afficher le contenu d’un fichier suivi

Une fois que Suivi des modifications et inventaire a détecté une modification apportée à un fichier suivi, vous pouvez afficher le contenu du fichier dans le volet Modifier les détails.  

![Lister les modifications](./media/change-tracking-file-contents/change-list.png)

1. Dans le portail Azure, ouvrez votre compte Automation, puis sélectionnez **Change Tracking** sous **Gestion de la configuration**.

2. Choisissez un fichier dans la liste des modifications et sélectionnez **Voir les modifications de contenu de fichier** pour afficher le contenu du fichier. Le volet Modifier les détails présente les informations de fichier standard, avant et après, pour chaque propriété.

   ![Détails des modifications](./media/change-tracking-file-contents/change-details.png)

3. Le contenu du fichier est présenté dans une vue côte à côte. Vous pouvez sélectionner **Inline** pour avoir une vue inline des modifications.

## <a name="track-registry-keys"></a>Effectuer le suivi de clés de Registre

Utilisez les étapes suivantes pour configurer le suivi des clés de Registre sur des ordinateurs Windows :

1. Dans le portail Azure, ouvrez votre compte Automation, puis sélectionnez **Change Tracking** sous **Gestion de la configuration**. 
2. Cliquez sur **Modifier les paramètres** (le symbole engrenage).
3. Dans la page Configuration de l’espace de travail, sélectionnez **Registre Windows**.
4. Cliquez sur **+ Ajouter** pour ajouter une nouvelle clé de Registre à suivre.
5. Dans le volet Ajouter le Registre Windows pour le suivi des modifications, entrez les informations correspondant à la clé à suivre puis cliquez sur **Enregistrer**. Le tableau suivant définit les propriétés que vous pouvez utiliser pour les informations.

    |Propriété  |Description  |
    |---------|---------|
    |activé     | True si un paramètre est appliqué, False sinon.        |
    |Item Name     | Nom convivial de la clé de Registre à suivre.        |
    |Groupe     | Nom de groupe pour le regroupement logique des clés de Registre.        |
    |Clé de Registre Windows   | Nom de clé avec chemin, par exemple `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup`.      |

## <a name="search-logs-for-change-records"></a>Rechercher des enregistrements de modifications dans les journaux

En matière d’enregistrements de modifications, vous pouvez effectuer différentes recherches dans les journaux Azure Monitor. Avec la page Change Tracking ouverte, cliquez sur **Log Analytics** pour ouvrir la page Journaux. Le tableau suivant fournit des exemples de recherche de modifications dans les journaux d’enregistrements.

|Requête  |Description  |
|---------|---------|
|`ConfigurationData`<br>&#124; `where ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"`<br>&#124; `where SvcState == "Stopped"`<br>&#124; `summarize arg_max(TimeGenerated, *) by SoftwareName, Computer`         | Affiche les enregistrements d’inventaire les plus récents des services Microsoft qui ont été définis sur Auto, mais qui ont été signalés comme étant arrêtés. Les résultats se limitent à l’enregistrement le plus récent pour le nom de logiciel et l’ordinateur spécifiés.    |
|`ConfigurationChange`<br>&#124; `where ConfigChangeType == "Software" and ChangeCategory == "Removed"`<br>&#124; `order by TimeGenerated desc`|Affiche les enregistrements de modifications des logiciels supprimés.|

## <a name="create-alerts-on-changes"></a>Créer des alertes en cas de modification

L’exemple suivant montre que le fichier **c:\windows\system32\drivers\etc\hosts** a été modifié sur une machine. Ce fichier est important car Windows l’utilise pour résoudre des noms d’hôtes en adresses IP. Cette opération est prioritaire par rapport à DNS et peut provoquer des problèmes de connectivité. Elle peut également entraîner une redirection du trafic vers des sites web malveillants ou dangereux.

![Graphique montrant la modification du fichier hosts](./media/change-tracking-file-contents/changes.png)

Utilisons cet exemple pour aborder les étapes de création d’alertes en cas de modification.

1. Dans votre compte Automation, sélectionnez **Change Tracking** sous **Gestion de la configuration**, puis **Log Analytics**. 
2. Dans Recherche dans les journaux, recherchez les modifications apportées au contenu du fichier **hosts** à l’aide de la requête `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`. Cette requête recherche des modifications de contenu pour les fichiers avec des noms de chemin complet contenant le mot `hosts`. Vous pouvez aussi rechercher un fichier spécifique en remplaçant le chemin par sa forme complète (par exemple `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`).

3. Une fois que la requête a retourné ses résultats, cliquez sur **Nouvelle règle d’alerte** dans la recherche dans les journaux pour ouvrir la page de création d’alerte. Vous pouvez également accéder à cette page par le biais d’**Azure Monitor** dans le portail Azure. 

4. Vérifiez à nouveau votre requête et modifiez la logique d’alerte. Pour cet exemple, vous souhaitez que l'alerte soit déclenchée à la moindre modification détectée sur l'ensemble des machines de l'environnement.

    ![Modification de la requête pour le suivi des modifications apportées au fichier hosts](./media/change-tracking-file-contents/change-query.png)

5. Une fois la logique d’alerte définie, attribuez des groupes d’actions pour effectuer des actions en réponse à l’alerte déclenchée. Ici, nous allons configurer des e-mails à envoyer et un ticket ITSM (IT Service Management) à créer. 

    ![Configuration du groupe d’actions pour alerter en cas de modification](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les configurations d’étendue, consultez [Limiter l’étendue du déploiement de Change Tracking et Inventory](automation-scope-configurations-change-tracking.md).
* Si vous avez besoin de rechercher dans les journaux d’activité stockés dans votre espace de travail Log Analytics, consultez [Recherches dans les journaux d’Azure Monitor](../azure-monitor/log-query/log-query-overview.md).
* Si vous en avez terminé avec les déploiements, consultez [Dissocier un espace de travail d’un compte Automation pour Change Tracking et Inventory](automation-unlink-workspace-change-tracking.md).
* Pour supprimer vos machines virtuelles de Change Tracking et Inventory, consultez [Supprimer des machines virtuelles de Change Tracking et Inventory](automation-remove-vms-from-change-tracking.md).
* Pour détecter un problème lié aux modifications apportées à une machine virtuelle Azure, consultez [Résoudre les problèmes liés à Change Tracking and Inventory](troubleshoot/change-tracking.md).
