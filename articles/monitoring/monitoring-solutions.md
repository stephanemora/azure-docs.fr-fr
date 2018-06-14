---
title: Solutions de gestion dans Azure | Microsoft Docs
description: Les solutions de gestion dans Azure représentent une collection de règles logiques, de visualisation et d'acquisition des données qui fournissent des mesures cernant un domaine problématique en particulier.  Cet article fournit des informations sur l’installation et l’utilisation des solutions de gestion.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: bwren
ms.openlocfilehash: 0df54d1758693bce5fb5fd74c3be9c4cfd7dccb6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33885266"
---
# <a name="management-solutions-in-azure"></a>Solutions de gestion dans Azure
Les solutions de gestion tirent profit des services disponibles dans Azure pour offrir des informations supplémentaires sur le fonctionnement d’une application ou d’un service spécifiques. Cet article fournit un bref aperçu des solutions de gestion dans Azure et détaille l’utilisation et l’installation de ces dernières.

En règle générale, les solutions de gestion collectent des informations dans Log Analytics et vous offrent des recherches dans les journaux et des vues pour analyser les données collectées. Ces solutions peuvent également tirer profit d’autres services tels qu’Azure Automation pour exécuter des actions liées à l’application ou au service.

Vous pouvez ajouter des solutions de gestion à votre abonnement Azure pour les applications et services que vous utilisez. Ces solutions sont généralement disponibles gratuitement, mais elles collectent des données susceptibles d’occasionner des frais d’utilisation. Outre les solutions proposées par Microsoft, les partenaires et les clients peuvent [créer des solutions de gestion](../monitoring/monitoring-solutions-creating.md) utilisables dans leur propre environnement ou mises à la disposition des clients par le biais de la communauté.

## <a name="using-management-solutions"></a>Utilisation des solutions de gestion
La page **Vue d’ensemble** de chaque espace de travail Log Analytics affiche une vignette pour chacune des solutions installées dans l’espace de travail. Cliquez sur la vignette d’une solution pour ouvrir la vue correspondante incluant une analyse plus détaillée des données collectées par la solution.

![Vue d'ensemble](media/monitoring-solutions/overview.png)

Les solutions de gestion peuvent contenir plusieurs types de ressources Azure, que vous pouvez visualiser comme n’importe quelle autre ressource. Par exemple, toutes les recherches dans les journaux qui sont incluses dans la solution figurent dans **Recherches enregistrées** dans l’espace de travail. Vous pouvez utiliser ces recherches lorsque vous effectuez une analyse ad hoc dans Log Analytics.

## <a name="list-installed-management-solutions"></a>Répertorier les solutions de gestion installées 
Pour répertorier les solutions de gestion installées dans votre abonnement, procédez comme suit.

1. Connectez-vous au portail Azure.
2. Dans le volet gauche, sélectionnez **Tous les services**.
3. Faites défiler la liste jusqu’à **Solutions**, ou bien tapez *solutions* dans la boîte de dialogue **Filtre**.
4. Vous obtenez la liste des solutions installées dans l’ensemble de vos espaces de travail. Le nom de la solution est suivi du nom de l’espace de travail Log Analytics dans lequel la solution est installée.
1. Utilisez les zones de liste déroulante en haut de l’écran pour filtrer les solutions par abonnement ou par groupe de ressources.


![Affichage de toutes les solutions](media/monitoring-solutions/list-solutions-all.png)

Cliquez sur le nom d’une solution pour ouvrir la page de résumé de cette dernière. Cette page affiche toutes les vues Log Analytics incluses dans la solution et fournit différentes options pour la solution proprement dite et pour son espace de travail. Pour visualiser la page de résumé d’une solution, suivez l’une des procédures ci-dessus pour afficher la liste des solutions, puis cliquez sur le nom de la solution.

![Propriétés d’une solution](media/monitoring-solutions/solution-properties.png)


## <a name="find-management-solutions"></a>Rechercher des solutions de gestion
Vous pouvez parcourir et installer les solutions de gestion proposées par Microsoft et par ses partenaires à partir de la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace). Effectuez une [recherche portant sur les *solutions de gestion*](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions) pour filtrer les solutions, puis cliquez sur l’un des résultats pour plus de détails.

![Marketplace](media/monitoring-solutions/marketplace.png)

## <a name="install-a-management-solution"></a>Installer une solution de gestion

### <a name="install-a-management-solution-from-the-azure-marketplace"></a>Installer une solution de gestion à partir de la Place de marché Microsoft Azure
Pour localiser et démarrer l’installation d’une solution de gestion, vous pouvez utiliser l’une des méthodes ci-après.

- Dans la [Place de marché Microsoft Azure](#find-management-solutions), cliquez sur le lien **Obtenir maintenant** sous une solution de gestion.
- Dans la [liste des solutions de votre abonnement](#list-installed-management-solutions), cliquez sur **Ajouter**. À droite de **Solutions de gestion**, cliquez sur **Plus**. Recherchez la solution de gestion qui vous intéresse, puis cliquez sur **Créer**.
- Dans le Portail Azure, sélectionnez **Créer une ressource** > **Surveillance + gestion** > **Afficher tout**. À droite de **Solutions de gestion**, cliquez sur **Plus**. Recherchez la solution de gestion qui vous intéresse, puis cliquez sur **Créer**.

Lorsque le processus d’installation démarre, vous êtes invité à fournir la configuration requise qui varie selon chaque solution. Toutes les solutions requièrent la sélection d’un espace de travail Log Analytics dans lequel elles seront installées et où leurs données seront collectées. En outre, vous devrez peut-être [spécifier un compte Automation](#log-analytics-workspace-and-automation-account) si la solution l’exige.

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
Toutes les solutions de gestion requièrent un [espace de travail Log Analytics](../log-analytics/log-analytics-manage-access.md) pour y stocker les données qu’elles collectent et pour héberger leurs recherches dans les journaux, ainsi que leurs vues. Certaines solutions nécessitent également un [compte Automation](../automation/automation-security-overview.md#automation-account-overview) destiné à contenir les runbooks et les ressources associées. L’espace de travail et le compte doivent répondre aux exigences suivantes.

* Chaque installation d’une solution ne peut utiliser qu’un seul espace de travail Log Analytics et un seul compte Automation. Vous pouvez installer la solution séparément dans plusieurs espaces de travail.
* Si une solution nécessite un compte Automation, l’espace de travail Log Analytics et le compte Automation doivent être liés l’un à l’autre. Un espace de travail Log Analytics ne peut être lié qu’à un seul compte Automation, et un compte Automation ne peut être lié qu’à un seul espace de travail Log Analytics.
* Pour être liés, l’espace de travail Log Analytics et le compte Automation doivent se trouver dans le même groupe de ressources et la même région. La configuration suivante fait figure d’exception : un espace de travail Log Analytics dans la région États-Unis de l’Est et un compte Automation dans États-Unis de l’Est 2.

### <a name="creating-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Création d’un lien entre un espace de travail Log Analytics et un compte Automation
Le mode de spécification de l’espace de travail Log Analytics et du compte Automation dépend de la méthode d’installation de votre solution.

* Quand vous installez une solution par le biais de la Place de marché Azure, vous êtes invité à choisir un espace de travail et un compte Automation. Un lien est automatiquement créé entre les deux s’il n’existe pas déjà.
* Pour les solutions extérieures à la Place de marché Azure, vous devez lier l’espace de travail Log Analytics et le compte Automation avant d’installer la solution. Pour ce faire, vous pouvez sélectionner n’importe quelle solution de la Place de marché Azure et choisir l’espace de travail Log Analytics et le compte Automation. Vous n’êtes pas obligé d’installer effectivement la solution, car le lien est créé dès que l’espace de travail Log Analytics et le compte Automation sont sélectionnés. Une fois le lien créé, vous pouvez utiliser cet espace de travail Log Analytics et ce compte Automation pour toutes les solutions.

### <a name="verifying-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Vérification du lien entre un espace de travail Log Analytics et un compte Automation
Vous pouvez vérifier le lien entre un espace de travail Log Analytics et un compte Automation à l’aide de la procédure suivante.

1. Sélectionnez le compte Automation dans le Portail Azure.
1. Accédez à la section **Ressources liées** du menu.
1. Si le paramètre **Espace de travail** est activé, ce compte est lié à un espace de travail Log Analytics. Vous pouvez cliquer sur **Espace de travail** pour afficher les détails de l’espace de travail.

## <a name="remove-a-management-solution"></a>Suppression d’une solution de gestion
Pour supprimer une solution installée, recherchez cette dernière dans la [liste des solutions installées](#list-installed-management-solutions). Cliquez sur le nom de la solution pour ouvrir la page de résumé correspondante, puis cliquez sur **Supprimer**.




## <a name="next-steps"></a>Étapes suivantes
* Obtenez la [liste des solutions de gestion proposées par Microsoft](monitoring-solutions-inventory.md).
* Découvrez comment [créer des requêtes](../log-analytics/log-analytics-log-searches.md) pour analyser les données collectées par les solutions de gestion.

