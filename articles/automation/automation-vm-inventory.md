---
title: Gérer une machine virtuelle Azure avec la collection d’inventaire | Microsoft Docs
description: Gérer une machine virtuelle avec la collection d’inventaire
services: automation
ms.subservice: change-inventory-management
keywords: inventaire, automatisation, suivi des modifications
ms.date: 01/28/2020
ms.topic: conceptual
ms.openlocfilehash: 0627d2daa70c276535dc43b722e22e1d73b0c8d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617371"
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Gérer une machine virtuelle Azure avec la collecte d’inventaire

Vous pouvez activer le suivi d’inventaire pour une machine virtuelle Azure à partir de la page de ressources de cette machine. Vous pouvez collecter et afficher les informations d’inventaire suivantes sur vos ordinateurs :

- Logiciels (applications Windows et mises à jour Windows), services, fichiers et clés de Registre Windows
- Logiciels (packages), démons et fichiers Linux

Cette méthode fournit une interface utilisateur basée sur un navigateur pour la configuration de la collecte d’inventaire.

## <a name="before-you-begin"></a>Avant de commencer

Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit](https://azure.microsoft.com/free/).

Cet article suppose que vous avez une machine virtuelle pour configurer la solution sur. Si vous n’avez pas de machine virtuelle Azure, [créez-en une](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Activer la collecte d’inventaire à partir de la page de ressources de la machine virtuelle

1. Dans le volet de gauche du portail Azure, sélectionnez **Machines virtuelles**.
2. Dans la liste des machines virtuelles, sélectionnez-en une.
3. Dans le menu **Ressource**, sous **Opérations**, sélectionnez **Inventaire**.
4. Sélectionnez un espace de travail Log Analytics pour stocker vos journaux de données.
    Si aucun espace de travail n’est disponible pour cette région, vous êtes invité à créer un compte Automation et un espace de travail par défaut.
5. Cliquez sur **Activer** pour démarrer l’intégration de votre ordinateur.

   ![Afficher les options d’intégration](./media/automation-vm-inventory/inventory-onboarding-options.png)

    Une barre d’état vous informe que la solution est en cours d’activation. Ce processus peut prendre jusqu’à 15 minutes. Pendant ce temps, vous pouvez fermer la fenêtre ou la laisser ouverte, afin qu’elle vous tienne informé de l’activation de la solution. Vous pouvez surveiller l’état du déploiement dans le volet des notifications.

   ![Afficher la solution d’inventaire immédiatement après l’intégration](./media/automation-vm-inventory/inventory-onboarded.png)

Une fois le déploiement terminé, la barre d’état disparaît. Le système est toujours en cours de collection des données d’inventaire ; ces données peuvent ne pas être encore visibles. Une collecte complète des données peut prendre 24 heures.

## <a name="configure-your-inventory-settings"></a>Configurer les paramètres d’inventaire

Par défaut, les logiciels, les services Windows et les démons Linux sont configurés pour la collection. Pour collecter l’inventaire des fichiers et registres Windows, configurez les paramètres de collection d’inventaire.

1. En haut de la page Inventaire, cliquez sur **Modifier les paramètres**.
2. Pour ajouter un nouveau paramètre de collection, accédez à la catégorie de paramètres concernée en sélectionnant l’onglet **Registre Windows**, **Fichiers Windows** ou **Fichiers Linux**.
3. Sélectionnez la catégorie appropriée et cliquez sur **Ajouter** en haut de la page.

Les tables suivantes fournissent des informations sur chaque propriété qui peut être configurée pour les différentes catégories.

### <a name="windows-registry"></a>Registre Windows

|Propriété  |Description  |
|---------|---------|
|activé     | Détermine si le paramètre est appliqué.        |
|Item Name     | Nom convivial du fichier à suivre.        |
|Groupe     | Nom de groupe pour le regroupement logique des fichiers.        |
|Clé de Registre Windows   | Chemin d’accès pour rechercher le fichier. Exemple : « HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup »      |

### <a name="windows-files"></a>Fichiers Windows

|Propriété  |Description  |
|---------|---------|
|activé     | True si le paramètre est appliqué, False sinon.        |
|Item Name     | Nom convivial du fichier à suivre.        |
|Groupe     | Nom de groupe pour le regroupement logique des fichiers.       |
|Entrer le chemin     | Chemin dans lequel rechercher le fichier. Exemple : **C:\temp\monfichier.txt**.

### <a name="linux-files"></a>Fichiers Linux

|Propriété  |Description  |
|---------|---------|
|activé     | True si le paramètre est appliqué, False sinon.        |
|Item Name     | Nom convivial du fichier à suivre.        |
|Groupe     | Nom de groupe pour le regroupement logique des fichiers.        |
|Entrer le chemin     | Chemin dans lequel rechercher le fichier. Exemple : **/etc/*.conf**.       |
|Type de chemin     | Type d’élément à suivre. Valeurs possibles : Fichier et Répertoire.        |
|Récursivité     | True si la récursivité est utilisée pour rechercher l’élément à suivre, False sinon.        |
|Utiliser sudo     | True si sudo est utilisé pour rechercher l’élément, False sinon.         |
|Liens     | Valeur indiquant le traitement des liens symboliques lors du parcours des répertoires. Les valeurs possibles sont les suivantes : <br> Ignorer : ignore les liens symboliques et n’inclut pas les fichiers/répertoires référencés.<br>Suivre : suit les liens symboliques pendant la récursivité et inclut également les fichiers/répertoires référencés.<br>Gérer : suit les liens symboliques et autorise la modification du traitement du contenu retourné.      |

## <a name="manage-machine-groups"></a>Gérer des groupes de machines

L’inventaire vous permet de créer et de visualiser des groupes de machines dans les journaux Azure Monitor. Les groupes de machines sont des collections de machines définies par une requête dans les journaux Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Pour visualiser vos groupes de machines, sélectionnez l’onglet **Groupes de machines** sur la page Inventaire.

![Affichage des groupes de machines sur la page d’inventaire](./media/automation-vm-inventory/inventory-machine-groups.png)

La sélection d’un groupe de machines dans la liste affiche la page Groupes de machines. Cette page affiche des détails sur le groupe de machines. Ces détails comprennent la requête d’analytique des journaux d’activité utilisée pour définir le groupe. La partie inférieure de la page présente une liste paginée des machines qui font partie de ce groupe.

![Affichage de la page d’un groupe de machines](./media/automation-vm-inventory/machine-group-page.png)

Cliquez sur **+ Cloner** pour cloner le groupe de machines. Vous devez donner au groupe un nouveau nom, ainsi qu’un alias. Vous pouvez modifier la définition à ce stade. Après avoir modifié la requête, cliquez sur **Valider la requête** pour afficher un aperçu des machines sélectionnées. Lorsque le groupe de machines vous convient, cliquez sur **Créer** pour le créer.

Si vous souhaitez créer un autre groupe de machines, cliquez sur **+ Créer un groupe de machines**. Ce bouton ouvre la page **Créer un groupe de machines** permettant de définir le nouveau groupe. Cliquez sur **Créer** pour créer le groupe.

![Création d’un groupe de machines](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-virtual-machine-from-management"></a>Déconnecter la machine virtuelle de la fonction de gestion

Pour supprimer votre machine virtuelle de la gestion de l’inventaire, procédez comme suit :

1. Dans le volet de gauche du portail Azure, cliquez sur **Log Analytics**, puis sélectionnez l’espace de travail que vous avez utilisé lors de l’intégration de votre machine virtuelle.
2. Sur la page Log Analytics, ouvrez le menu **Ressource**.
3. Sélectionnez **Machines virtuelles** sous **Sources de données de l’espace de travail**.
4. Dans la liste, sélectionnez la machine virtuelle que vous souhaitez déconnecter. Une coche verte s’affiche en regard du texte indiquant **Cet espace de travail** dans la colonne **Connexion OMS**, sur la machine virtuelle.

   >[!NOTE]
   >Pour faire référence à Operations Management Suite (OMS), nous parlons maintenant de journaux Azure Monitor.
   
5. En haut de la page suivante, cliquez sur **Déconnecter**.
6. Dans la fenêtre de confirmation, cliquez sur **Oui** pour déconnecter la machine de la gestion.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la gestion des modifications dans les paramètres de registre et les fichiers sur vos machines virtuelles, consultez l’article relatif au [suivi des modifications apportées à votre environnement logiciel avec la solution Change Tracking](../log-analytics/log-analytics-change-tracking.md).
* Pour en savoir plus sur la gestion des mises à jour Windows et de packages sur des machines virtuelles, consultez [La solution Update Management dans Azure](../operations-management-suite/oms-solution-update-management.md).