---
title: Solutions de monitoring dans Azure Monitor | Microsoft Docs
description: Les solutions de monitoring d’Azure Monitor représentent une collection de règles logiques, de visualisation et d'acquisition de données qui fournissent des métriques reposant sur un domaine problématique en particulier.  Cet article donne des informations sur l’installation et l’utilisation des solutions de monitoring.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2020
ms.openlocfilehash: c2690ad7cc4dcaa295bfb08b8c0396438ada0807
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437539"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Solutions de monitoring dans Azure Monitor
Les solutions de monitoring exploitent les services Azure pour offrir des insights supplémentaires sur le fonctionnement d’une application ou d’un service en particulier. Cet article offre une vue d’ensemble des solutions de monitoring Azure et des informations sur leur utilisation et leur installation.

> [!NOTE]
> Les solutions de monitoring étaient précédemment nommées solutions de gestion.

En règle générale, les solutions de monitoring collectent des données de journal et fournissent des requêtes et des vues permettant d’analyser ces données. Ces solutions peuvent également tirer profit d’autres services tels qu’Azure Automation pour exécuter des actions liées à l’application ou au service.

Vous pouvez ajouter des solutions de monitoring à Azure Monitor pour toutes les applications et tous les services que vous utilisez. Ces solutions sont généralement disponibles gratuitement, mais elles collectent des données susceptibles d’occasionner des frais d’utilisation. Outre les solutions proposées par Microsoft, les partenaires et les clients peuvent [créer des solutions de gestion](solutions-creating.md) utilisables dans leur propre environnement ou mises à la disposition des clients par le biais de la communauté.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="use-monitoring-solutions"></a>Utiliser des solutions de supervision
Ouvrez la page **Vue d’ensemble** d’Azure Monitor pour afficher la vignette de chacune des solutions installées dans l’espace de travail. 

1. Accédez au [portail Azure](https://ms.portal.azure.com). Recherchez et sélectionnez **Surveiller**.
1. Sous le menu **Insights**, sélectionnez **Plus**.
1. Utilisez les zones de liste déroulante en haut de l’écran pour modifier l’espace de travail ou l’intervalle de temps utilisé pour les vignettes.
1. Cliquez sur la vignette d’une solution pour ouvrir la vue correspondante incluant une analyse plus détaillée des données collectées par la solution.

![Vue d’ensemble](media/solutions/overview.png)

Les solutions de monitoring peuvent contenir plusieurs types de ressources Azure, que vous pouvez visualiser comme n’importe quelle autre ressource. Par exemple, les requêtes de journal incluses dans la solution apparaissent sous **Requêtes de solutions** dans [Explorateur de requêtes](../log-query/get-started-portal.md#load-queries). Vous pouvez les utiliser lorsque vous effectuez une analyse ad hoc avec des [requêtes de journal](../log-query/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Lister les solutions de monitoring installées 
Suivez la procédure ci-dessous pour lister les solutions de monitoring installées dans votre abonnement.

1. Accédez au [portail Azure](https://ms.portal.azure.com). Recherchez et sélectionnez **Solutions**.
1. Vous obtenez la liste des solutions installées dans l’ensemble de vos espaces de travail. Le nom de la solution est suivi du nom de l’espace de travail dans lequel elle est installée.
1. Utilisez les zones de liste déroulante en haut de l’écran pour filtrer les solutions par abonnement ou par groupe de ressources.


![Affichage de toutes les solutions](media/solutions/list-solutions-all.png)

Cliquez sur le nom d’une solution pour ouvrir la page de résumé de cette dernière. Cette page affiche toutes les vues incluses dans la solution et fournit différentes options pour la solution proprement dite et pour son espace de travail. Pour visualiser la page de résumé d’une solution, suivez l’une des procédures ci-dessus pour afficher la liste des solutions, puis cliquez sur le nom de la solution.

![Propriétés d’une solution](media/solutions/solution-properties.png)



## <a name="install-a-monitoring-solution"></a>Installer une solution de supervision
Les solutions de monitoring de Microsoft et de ses partenaires sont disponibles sur la [Place de marché Azure](https://azuremarketplace.microsoft.com). Vous pouvez rechercher des solutions disponibles et les installer à l’aide de la procédure suivante. Lorsque vous installez une solution, vous devez sélectionner un [espace de travail Log Analytics](../platform/manage-access.md) dans lequel elles seront installées et où leurs données seront collectées.

1. Dans la [liste des solutions de votre abonnement](#list-installed-monitoring-solutions), cliquez sur **Ajouter**.
1. Parcourez ou recherchez une solution. Vous pouvez également parcourir les solutions à partir de [ce lien de recherche](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions).
1. Recherchez la solution de supervision qui vous intéresse et lisez sa description.
1. Cliquez sur **Créer** pour démarrer le processus d’installation.
1. Quand le processus d’installation démarre, vous êtes invité à spécifier l’espace de travail Log Analytics et à fournir la configuration exigée pour la solution.

![Installer une solution](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Installer une solution proposée par la communauté
Les membres de la communauté peuvent soumettre des solutions de gestion aux modèles de démarrage rapide Azure. Vous pouvez installer ces solutions directement ou en télécharger les modèles à des fins d’installation ultérieure.

1. Suivez la procédure décrite dans [Espace de travail Log Analytics et compte Automation](#log-analytics-workspace-and-automation-account) pour lier un compte et un espace de travail.
2. Accédez à [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/). 
3. Recherchez une solution qui vous intéresse.
4. Sélectionnez la solution dans les résultats pour en afficher les détails.
5. Cliquez sur le bouton **Déployer dans Azure**.
6. Vous êtes invité à fournir des informations comme le groupe de ressources et l’emplacement, en plus des valeurs des paramètres de la solution.
7. Cliquez sur **Achat** pour installer la solution.


## <a name="log-analytics-workspace-and-automation-account"></a>Espace de travail Log Analytics et compte Automation
Toutes les solutions de supervision ont besoin d’un [espace de travail Log Analytics](../platform/manage-access.md) pour stocker les données qu’elles collectent et héberger leurs recherches dans les journaux, ainsi que leurs vues. Certaines solutions nécessitent également un [compte Automation](../../automation/automation-security-overview.md#automation-account-overview) destiné à contenir les runbooks et les ressources associées. L’espace de travail et le compte doivent répondre aux exigences suivantes.

* Chaque installation d’une solution ne peut utiliser qu’un seul espace de travail Log Analytics et un seul compte Automation. Vous pouvez installer la solution séparément dans plusieurs espaces de travail.
* Si une solution nécessite un compte Automation, l’espace de travail Log Analytics et le compte Automation doivent être liés l’un à l’autre. Un espace de travail Log Analytics ne peut être lié qu’à un seul compte Automation, et un compte Automation ne peut être lié qu’à un seul espace de travail Log Analytics.
* Pour être liés, l’espace de travail Log Analytics et le compte Automation doivent se trouver dans le même groupe de ressources et la même région. La configuration suivante fait figure d’exception : un espace de travail Log Analytics dans la région USA Est et un compte Automation dans USA Est 2.

Quand vous installez une solution par le biais de la Place de marché Azure, vous êtes invité à choisir un espace de travail et un compte Automation. Un lien est automatiquement créé entre les deux s’il n’existe pas déjà.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Vérification du lien entre un espace de travail Log Analytics et un compte Automation
Vous pouvez vérifier le lien entre un espace de travail Log Analytics et un compte Automation à l’aide de la procédure suivante.

1. Sélectionnez le compte Automation dans le Portail Azure.
1. Accédez à la section **Ressources liées** du menu.
1. Si le paramètre **Espace de travail** est activé, ce compte est lié à un espace de travail Log Analytics. Vous pouvez cliquer sur **Espace de travail** pour afficher les détails de l’espace de travail.

## <a name="remove-a-monitoring-solution"></a>Supprimer une solution de supervision
Pour supprimer une solution installée, recherchez cette dernière dans la [liste des solutions installées](#list-installed-monitoring-solutions). Cliquez sur le nom de la solution pour ouvrir la page de résumé correspondante, puis cliquez sur **Supprimer**.


## <a name="next-steps"></a>Étapes suivantes
* Accédez à la [liste des solutions de monitoring de Microsoft](solutions-inventory.md).
* Apprenez à [créer des requêtes](../log-query/log-query-overview.md) pour analyser les données collectées par les solutions de monitoring.

