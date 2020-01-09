---
title: Dépanner les modifications apportées à une machine virtuelle Azure | Microsoft Docs
description: Utilisez Change Tracking pour dépanner celles apportées à une machine virtuelle Azure.
services: automation
ms.subservice: change-inventory-management
keywords: modification, suivi, automatisation
ms.date: 12/05/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 60ca1ef3d5c14a0f3dea5b662fc5c95184e6574d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75420631"
---
# <a name="troubleshoot-changes-in-your-environment"></a>Dépanner les modifications apportées à votre environnement

Dans ce didacticiel, vous allez apprendre à dépanner les modifications apportées à une machine virtuelle Azure. En activant le suivi des modifications, vous pouvez suivre celles apportées aux logiciels, fichiers, démons Linux, services Windows et clés de registre Windows présents sur vos ordinateurs.
L’identification de ces modifications de configuration peut vous aider à mettre à jour les problèmes opérationnels constatés dans votre environnement.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Intégrer une machine virtuelle pour le suivi des modifications et l’inventaire
> * Rechercher les journaux d’activité des modifications des services arrêtés
> * Configurer le suivi des modifications
> * Activer la connexion du journal d’activité
> * Déclencher un événement
> * Afficher les modifications
> * Configurer des alertes

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un [compte Automation](automation-offering-get-started.md) qui contiendra les runbooks Watcher et d’action, ainsi que la tâche d’observateur.
* Une [machine virtuelle](../virtual-machines/windows/quick-create-portal.md) à intégrer.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Activer le suivi des modifications et l’inventaire

Pour ce didacticiel, vous devez d’abord activer Suivi des modifications et inventaire pour votre machine virtuelle. Si vous avez déjà activé une autre solution d’automatisation pour une machine virtuelle, cette étape n’est pas nécessaire.

1. Dans le menu de gauche, sélectionnez **Machines virtuelles**, puis choisissez une machine virtuelle dans la liste
1. Dans le menu de gauche, dans la section **OPÉRATIONS**, cliquez sur **Inventaire**. La page **Suivi des modifications** s’ouvre.

![Activer la modification](./media/automation-tutorial-troubleshoot-changes/enableinventory.png) L’écran **Change Tracking** s’ouvre. Configurez l’emplacement, l’espace de travail Log Analytics et un compte Automation à utiliser, puis cliquez sur **Activer**. Si les champs sont grisés, cela signifie qu’une autre solution d’automatisation est activée pour la machine virtuelle, et les mêmes espace de travail et compte Automation doivent être utilisés.

Un espace de travail [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) est utilisé pour collecter les données générées par les fonctionnalités et les services, comme l’inventaire.
L’espace de travail fournit un emplacement unique permettant de consulter et d’analyser les données provenant de plusieurs sources.

Au cours de l’intégration, la machine virtuelle est approvisionnée avec l’agent Microsoft Monitoring Agent (MMA) et un Worker hybride.
Cet agent sert à communiquer avec la machine virtuelle et à obtenir des informations sur les logiciels installés.

L’activation de la solution peut prendre jusqu’à 15 minutes. Pendant ce temps, vous ne devez pas fermer la fenêtre du navigateur.
Une fois la solution activée, des informations sur les logiciels installés et les changements apportés à la machine virtuelle sont envoyées aux journaux d’activité Azure Monitor.
Entre 30 minutes et 6 heures peuvent être nécessaires pour que les données soient disponibles pour l’analyse.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-change-tracking-in-azure-monitor-logs"></a>Utilisation du suivi des changements dans les journaux Azure Monitor

Le suivi des changements génère des données de journal qui sont envoyées aux journaux Azure Monitor.
Pour rechercher les journaux d’activité en exécutant des requêtes, sélectionnez **Log Analytics** en haut de la fenêtre **Suivi des modifications**.
Les données de suivi des modifications sont stockées sous le type **ConfigurationData**.
L’exemple de requête Log Analytics ci-après renvoie tous les services Windows arrêtés.

```loganalytics
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

Pour en savoir plus sur l’exécution et la recherche de fichiers journaux dans les journaux d’activité Azure Monitor, consultez [Journaux d’activité Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="configure-change-tracking"></a>Configurer le suivi des modifications

Le suivi des modifications vous donne la possibilité d’effectuer le suivi des changements de configuration apportés à votre machine virtuelle. Les étapes suivantes vous montrent comment configurer le suivi des clés de Registre et des fichiers.

Pour choisir les fichiers et clés de Registre à collecter et dont effectuer le suivi, sélectionnez **Modifier les paramètres** en haut de la page **Suivi des modifications**.

> [!NOTE]
> L’inventaire et le suivi des modifications utilisent les mêmes paramètres de collecte, et ces paramètres sont configurés au niveau de l’espace de travail.

Dans la fenêtre **Configuration de l’espace de travail**, ajoutez les clés de Registre Windows, puis les fichiers Windows ou Linux à suivre, comme décrit dans les trois sections suivantes.

### <a name="add-a-windows-registry-key"></a>Ajouter une clé de Registre Windows

1. Dans l’onglet **Registre Windows**, sélectionnez **Ajouter**.
    La fenêtre **Ajouter le Registre Windows pour le suivi des modifications**.

1. Dans la fenêtre **Ajouter le Registre Windows pour le suivi des modifications**, entrez les informations correspondant à la clé à suivre et cliquez sur **Enregistrer**.

|Propriété  |Description  |
|---------|---------|
|activé     | Détermine si le paramètre est appliqué.        |
|Item Name     | Nom convivial du fichier à suivre.        |
|Groupe     | Nom de groupe pour le regroupement logique des fichiers.        |
|Clé de Registre Windows   | Chemin d’accès pour rechercher le fichier. Exemple : « HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup »      |

### <a name="add-a-windows-file"></a>Ajouter un fichier Windows

1. Dans l’onglet **Fichiers Windows**, sélectionnez **Ajouter**. La fenêtre **Ajouter le fichier Windows pour le suivi des modifications**.

1. Dans la fenêtre **Ajouter le fichier Windows pour le suivi des modifications**, entrez les informations du fichier ou du répertoire à suivre et cliquez sur **Enregistrer**.

|Propriété  |Description  |
|---------|---------|
|activé     | Détermine si le paramètre est appliqué.        |
|Item Name     | Nom convivial du fichier à suivre.        |
|Groupe     | Nom de groupe pour le regroupement logique des fichiers.        |
|Entrer le chemin     | Chemin d’accès pour rechercher le fichier. Exemple : « c:\temp\\\*.txt »<br>Vous pouvez également utiliser des variables d’environnement telles que « %winDir%\System32\\\*.* »         |
|Récursivité     | Détermine si la récursivité est utilisée lorsque vous recherchez l’élément à suivre.        |
|Télécharger le contenu du fichier pour tous les paramètres| Active ou désactive le chargement du contenu du fichier pour le suivi des modifications. Options disponibles : **True** ou **False**.|

### <a name="add-a-linux-file"></a>Ajouter un fichier Linux

1. Dans l’onglet **Fichiers Linux**, sélectionnez **Ajouter**. La fenêtre **Ajouter le fichier Linux pour le suivi des modifications**.

1. Dans la fenêtre **Ajouter le fichier Linux pour le suivi des modifications**, entrez les informations du fichier ou du répertoire à suivre et cliquez sur **Enregistrer**.

|Propriété  |Description  |
|---------|---------|
|activé     | Détermine si le paramètre est appliqué.        |
|Item Name     | Nom convivial du fichier à suivre.        |
|Groupe     | Nom de groupe pour le regroupement logique des fichiers.        |
|Entrer le chemin     | Chemin d’accès pour rechercher le fichier. Exemple : « /etc/*.conf ».       |
|Type de chemin     | Type d’élément à suivre. Valeurs possibles : fichier et répertoire.        |
|Récursivité     | Détermine si la récursivité est utilisée lorsque vous recherchez l’élément à suivre.        |
|Utiliser sudo     | Ce paramètre détermine si sudo est utilisé lorsque vous vérifiez l’élément.         |
|Liens     | Ce paramètre détermine le traitement des liens symboliques lorsque vous parcourez les répertoires.<br> **Ignorer** : ignore les liens symboliques et n’inclut pas les fichiers/répertoires référencés.<br>**Suivre** : suit les liens symboliques pendant les opérations de récursivité et inclut aussi les fichiers/répertoires référencés.<br>**Gérer** : suit les liens symboliques et autorise la modification du traitement du contenu retourné.      |
|Télécharger le contenu du fichier pour tous les paramètres| Active ou désactive le chargement du contenu du fichier pour le suivi des modifications. Options disponibles : **True** ou **False**.|

   > [!NOTE]
   > L’option permettant de « Gérer » les liens n’est pas recommandée. L’extraction du contenu du fichier n’est pas prise en charge.

## <a name="enable-activity-log-connection"></a>Activer la connexion du journal d’activité

À partir de la page **Suivi des modifications** sur votre machine virtuelle, sélectionnez **Manage Activity Log Connection** (Gérer la connexion du journal d’activité). Cette tâche ouvre la page **Journal des activités Azure**. Sélectionnez **Connexion** pour connecter le suivi des modifications au journal des activités Azure pour votre machine virtuelle.

Une fois ce paramètre activé, accédez à la page **Vue d’ensemble** de votre machine virtuelle, puis sélectionnez **Arrêter** pour l’arrêter. Lorsque vous y êtes invité, sélectionnez **Oui** pour arrêter la machine virtuelle. Celle-ci étant libérée, sélectionnez **Démarrer** pour redémarrer votre machine virtuelle.

Le fait d’arrêter et de démarrer une machine virtuelle enregistre un événement dans son journal d’activité. Revenez à la page **Suivi des modifications**. Sélectionnez l’onglet **Événements** au bas de la page. Après un certain temps, les événements apparaissent dans le graphique et le tableau. Comme dans l’étape précédente, chaque événement peut être sélectionné pour en afficher le détail.

![Comment afficher le détail des modifications dans le portail](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

## <a name="view-changes"></a>Afficher les modifications

Lorsque la solution de suivi des modifications et d’inventaire est activée, vous pouvez afficher les résultats sur la page **Suivi des modifications**.

À partir de votre machine virtuelle, sélectionnez **Suivi des modifications** sous **OPÉRATIONS**.

![Capture d’écran montrant la liste des modifications apportées à la machine virtuelle](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

Le graphique affiche les modifications qui se sont produites au fil du temps.
Après avoir ajouté une connexion au journal d’activité, le graphique linéaire dans la partie supérieure affiche les événements du journal des activités Azure.
Chacune des lignes des graphiques à barres représente un autre type de modification dont il est possible d’effectuer le suivi.
Ces types sont les démons Linux, les fichiers, les clés de Registre Windows, les logiciels et les services Windows.
L’onglet Modification présente le détail des modifications indiquées dans la visualisation, par ordre décroissant, c’est-à-dire le plus récent en premier.
L’onglet **Événements** du tableau affiche les événements du journal d’activité connecté, ainsi que les informations correspondantes, les plus récentes en premier.

Vous pouvez voir, dans les résultats, que plusieurs modifications ont été apportées au système, y compris aux logiciels et services. Vous pouvez utiliser les filtres en haut de la page pour filtrer les résultats par **type de modification** ou par plage de temps.

En sélectionnant une modification **WindowsServices**, vous ouvrez la fenêtre **Détails de la modification**. Cette fenêtre présente les détails de la modification, ainsi que les valeurs avant et après. Dans ce cas, le service Protection logicielle a été arrêté.

![Comment afficher le détail des modifications dans le portail](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="configure-alerts"></a>Configurer des alertes

L’affichage des modifications dans le Portail Azure est pratique, mais pouvoir être averti d’une modification (l’arrêt d’un service, par exemple) peut être bien plus avantageux.

Pour ajouter une alerte associée à l’arrêt d’un service, accédez à **Surveiller** dans le Portail Azure. Sous **Services partagés**, sélectionnez **Alertes**, puis cliquez sur **+ Nouvelle règle d’alerte**.

Cliquez sur **Sélectionner** pour choisir une ressource. Sur la page **Sélectionner une ressource**, sélectionnez **Log Analytics** dans le menu déroulant **Filtrer par type de ressource**. Sélectionnez votre espace de travail Log Analytics, puis sélectionnez **Terminé**.

![Sélectionner une ressource](./media/automation-tutorial-troubleshoot-changes/select-a-resource.png)

Cliquez sur **Ajouter une condition**, sur la page **Configurer la logique du signal**, dans le tableau, sélectionnez **Recherche de journal personnalisée**. Entrez la requête suivante dans la zone de texte de la requête de recherche :

```loganalytics
ConfigurationChange | where ConfigChangeType == "WindowsServices" and SvcName == "W3SVC" and SvcState == "Stopped" | summarize by Computer
```

Cette requête renvoie les ordinateurs dont le service W3SVC s’est arrêté au cours de l’intervalle spécifié.

Sous **Logique d’alerte**, pour **Seuil**, entrez **0**. Quand vous avez terminé, cliquez sur **Terminé**.

![Configurer la logique du signal](./media/automation-tutorial-troubleshoot-changes/configure-signal-logic.png)

Sous **Groupes d’action**, sélectionnez **Créer**. Un groupe d’actions est un groupe que vous pouvez utiliser dans plusieurs alertes. Les actions peuvent inclure, sans s’y limiter, les notifications par e-mail, les runbooks, les webhooks et bien plus encore. Pour en savoir plus sur les groupes d’actions, consultez [Créer et gérer des groupes d’actions](../azure-monitor/platform/action-groups.md).

Sous **Détails de l’alerte**, entrez un nom et une description pour l’alerte. Définissez le niveau de **Gravité** sur **Information (gravité 2)** , **Avertissement (gravité 1)** ou **Critique (gravité 0)** .

Dans la zone **Nom du groupe d’actions** , entrez un nom pour l’alerte et un nom court. Le nom court est utilisé à la place du nom complet du groupe d’actions lorsque les notifications sont envoyées à l’aide de ce groupe.

Sous **Actions**, entrez un nom pour l’action, tel que **Administrateurs de la messagerie**. Sous **TYPE D’ACTION**, sélectionnez **E-mail/SMS/Push/Voix**. Sous **DÉTAILS**, sélectionnez **Modifier les détails**.

![Ajouter un groupe d'actions](./media/automation-tutorial-troubleshoot-changes/add-action-group.png)

Dans le volet **E-mail/SMS/Push/Voix**, entrez un nom. Sélectionnez la case à cocher **E-mail**, puis entrez une adresse e-mail valide. Cliquez sur **OK** dans la page **E-mail/SMS/Push/Voix**, puis sur **OK** dans la page **Ajouter un groupe d’actions**.

Pour personnaliser l’objet de l’e-mail d’alerte, sous **Créer une règle** > **Personnaliser les actions**, sélectionnez **Objet de l’e-mail**. Lorsque vous avez terminé, cliquez sur **Créer une règle d’alerte**. L’alerte vous avertit quand un déploiement de mises à jour s’est correctement déroulé et précise quelles machines en ont bénéficié.

L’illustration suivante est un exemple d’e-mail reçu lorsque le service W3SVC s’arrête.

![email](./media/automation-tutorial-troubleshoot-changes/email.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Intégrer une machine virtuelle pour le suivi des modifications et l’inventaire
> * Rechercher les journaux d’activité des modifications des services arrêtés
> * Configurer le suivi des modifications
> * Activer la connexion du journal d’activité
> * Déclencher un événement
> * Afficher les modifications
> * Configurer des alertes

Accédez ensuite à la vue d’ensemble de la solution de suivi des modifications et d’inventaire pour en savoir plus.

> [!div class="nextstepaction"]
> [Solution de gestion des changements et d’inventaire](automation-change-tracking.md)

