---
title: Configurer des références SKU pour une offre d’application Azure | Place de marché Azure
description: Comment configurer les références SKU pour une application managée Azure et un modèle de solution Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 2430d7e6fa74438c148d3cb849510be06243faa0
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76543152"
---
# <a name="azure-application-skus-tab"></a>Onglet Références de l’application Azure

Cet article décrit comment utiliser l’onglet Références pour créer des références SKU pour votre application Azure. 

> [!IMPORTANT]
> Les étapes de configuration d’une référence SKU sont différentes pour une offre d’application managée et une offre de modèle de solution. Ces différences sont présentées dans cet article. 

## <a name="configure-azure-application-skus"></a>Configurer des références SKU pour une application Azure

### <a name="create-a-new-sku"></a>Créer une référence SKU

Pour créer une référence SKU, suivez les étapes ci-dessous :

1. Sélectionnez l’onglet **Références**.
2. Sous Références, sélectionnez **+ New SKU** (+ Nouvelle référence SKU).

    ![Invite de nouvelle référence SKU](./media/azureapp-plus-sku.png)

3. Dans la fenêtre New SKU (Nouvelle référence SKU), entrez un **identificateur pour la référence SKU**. Cet ID doit être composé d’un maximum de 50 caractères (uniquement des minuscules, des caractères alphanumériques, des tirets ou des traits de soulignement). L’ID de la référence SKU ne peut pas se terminer par un tiret.
4. L’ID de référence SKU est visible par les clients dans les URL de produit, les modèles Resource Manager (le cas échéant) et les états de facturation. Cet ID n’est pas modifiable une fois l’offre publiée.

### <a name="sku-details-for-a-solution-template"></a>Détails de la référence SKU pour un modèle de solution

La capture d’écran suivante montre le formulaire comportant les détails de la référence SKU pour un modèle de solution.

![Détails de la référence SKU pour un modèle de solution](./media/azureapp-sku-details-solutiontemplate.png)

Fournissez les valeurs de référence SKU suivantes.  Les champs marqués d’un astérisque sont obligatoires.

|    Champ         |       Description                                                            |
|  ---------       |     ---------------                                                          |
|  **Titre\***     | Titre de la référence SKU. Ce titre s’affiche dans la galerie pour cet élément.   |
| **Résumé\***    | Courte description de la référence SKU. (La longueur maximale est de 100 caractères.)  |
| **Description\*** | Description détaillée de la référence SKU. Le code HTML de base est pris en charge.                 | 
| **Type de référence SKU\***   | Type de solution d’application Azure. Sélectionnez ***Modèle de Solution** pour ce scénario. |
| **Disponibilité dans le cloud\*** | Emplacement de la référence SKU. La valeur par défaut est **Public Azure**.  <b/>   **Azure public** : l’application pourra être déployée sur des clients dans toutes les régions Azure publiques intégrées à la Place de marché.  <b/>   **Cloud Azure Government** : l’application sera déployée dans le cloud Azure Government. Avant de publier sur [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), Microsoft recommande aux éditeurs de tester et valider le fait que leur solution fonctionne comme prévu dans cet environnement. Pour effectuer une copie intermédiaire et tester, demandez un [compte d’essai](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).  |
| **S’agit-il d’une référence SKU privée ?\*** | Sélectionnez **Oui** si cette référence SKU est accessible uniquement à un groupe choisi de clients. |
|   |   |

  > [!NOTE] 
  > Microsoft Azure Government est un cloud communautaire pour le secteur public, accessible à des clients d’administrations fédérales, étatiques, locales ou tribales des États-Unis, ainsi qu’à des partenaires éligibles pour servir ces entités.


### <a name="sku-details-for-managed-application"></a>Détails de la référence SKU pour une application managée

La capture d’écran suivante montre le formulaire comportant les détails de la référence SKU pour une application managée.

   ![Formulaire comportant les détails de la référence SKU pour une application managée](./media/azureapp-sku-details-managedapplication.png)

Configurez les paramètres de référence SKU suivants. Les champs marqués d’un astérisque sont obligatoires.

|    Champ         |       Description                                                            |
|  ---------       |     ---------------                                                          |
|  **Titre\***     | Titre de la référence SKU. Ce titre s’affiche dans la galerie pour cet élément.   |
| **Résumé\***    | Courte description de la référence SKU. (La longueur maximale est de 100 caractères.)  |
| **Description\*** | Description détaillée de la référence SKU. Le code HTML de base est pris en charge.                 | 
| **Type de référence SKU\***   | Type de solution d’application Azure. Sélectionnez ***Application managée** pour ce scénario. 
| **Disponibilité dans le cloud\*** | Emplacement de la référence SKU. La valeur par défaut est **Public Azure**.  <b/>   **Azure public** : l’application pourra être déployée sur des clients dans toutes les régions Azure publiques intégrées à la Place de marché.  <b/>   **Cloud Azure Government** : l’application sera déployée dans le cloud Azure Government. Avant de publier sur [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), Microsoft recommande aux éditeurs de tester et valider le fait que leur solution fonctionne comme prévu dans cet environnement. Pour effectuer une copie intermédiaire et tester, demandez un [compte d’essai](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).   Microsoft Azure Government est un cloud communautaire pour le secteur public, accessible à des clients d’administrations fédérales, étatiques, locales ou tribales des États-Unis, ainsi qu’à des partenaires éligibles pour servir ces entités. |
| **S’agit-il d’une référence SKU privée ?\*** | Sélectionnez **Oui** si cette référence SKU est accessible uniquement à un groupe choisi de clients. |
| **Disponibilité par pays/région\*** | Utilisez **Sélectionner des régions** pour afficher la liste des pays/régions disponibles. Sélectionnez chaque pays/région, puis cliquez sur **OK** pour enregistrer votre sélection.  <b/>   ![Liste des disponibilités par pays et région](./media/azure-app-select-country-region.png)  |
| **Ancienne tarification\*** | Prix de la référence (SKU), en USD par mois. Les prix sont définis dans la devise locale à l’aide du taux de change actuel au moment de la configuration. Vous pouvez les valider étant donné que vous êtes au final propriétaire de ces paramètres. Pour définir ou afficher individuellement le prix dans chaque pays/région, exportez la feuille de calcul des tarifs, puis importez-la avec une tarification personnalisée.  Vous devez enregistrer les modifications de votre tarification pour autoriser l’export/import des données de tarification.  |
| **Tarifs simplifiés en devises\*** | Prix de la référence (SKU), en USD par mois. Il doit être identique à l’ancienne tarification. Pour plus d’informations, consultez [Tarification simplifiée des devises](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer). |
|  |  |


### <a name="package-details-for-solution-template"></a>Détails du package pour un modèle de solution

![Détails du package pour un modèle de solution](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

Fournissez les valeurs de **Détails du package** suivantes.  Les champs marqués d’un astérisque sont obligatoires.

- **Version\***  : version du package que vous allez charger. Les balises de version doivent être au format X.Y.Z, où X, Y et Z sont des entiers.
- **Fichier de package (.zip)\*** : ce package contient les fichiers suivants, compressés dans un fichier .zip.
  - **mainTemplate.json\***  : fichier de modèle de déploiement utilisé pour déployer la solution/l’application et créer les ressources définies pour la solution. Pour plus d’informations, consultez [Comment créer des fichiers de modèle de déploiement](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template).
  - **createUIDefinition.json\***  : ce fichier est utilisé par le portail Azure afin de générer l’interface utilisateur pour le provisionnement de cette solution/application. Pour plus d’informations, consultez [Créer une interface utilisateur de portail Azure pour votre application managée](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - Scripts (si nécessaire) : tous les scripts supplémentaires qui peuvent être nécessaires lors de l’exécution du modèle, par exemple `Microsoft.Compute/virtualMachines/extensions`.
  - Modèles imbriqués (si nécessaire) : tous les modèles imbriqués supplémentaires.

  > [!IMPORTANT] 
  > Ce package doit contenir tous les modèles ou scripts imbriqués nécessaires à l’approvisionnement de cette application. Les fichiers mainTemplate.json et createUiDefinition.json doivent se trouver dans le dossier racine. Pour plus d’informations sur les artefacts de déploiement, consultez [Modèles Azure Resource Manager - Guide des bonnes pratiques](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#deployment-artifacts-nested-templates-scripts).


### <a name="package-details-for-managed-application"></a>Détails du package pour une application managée

   ![Détails du package pour une application managée](./media/azureapp-sku-pkgdetails-managedapplication.png)

Fournissez les détails du package suivants.  Les champs marqués d’un astérisque sont obligatoires.

- **Version\***  : version du package que vous allez charger. Les balises de version doivent être au format X.Y.Z, où X, Y et Z sont des entiers.
- **Fichier de package (.zip)\*** : ce package contient les fichiers suivants, compressés dans un fichier .zip.
  - applianceMainTemplate.json : fichier de modèle de déploiement utilisé pour déployer la solution/l’application et créer les ressources qui sont définies. Pour plus d’informations, consultez [Démarrage rapide : Créer et déployer des modèles Azure Resource Manager à l’aide du portail Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal). 
  - applianceCreateUIDefinition.json : ce fichier est utilisé par le portail Azure pour générer l’interface utilisateur pour l’approvisionnement de cette solution/application. Pour plus d’informations, consultez [Créer une interface utilisateur de portail Azure pour votre application managée](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - mainTemplate.json : le fichier de modèle qui contient uniquement la ressource Microsoft.Solution/appliances. Pour plus d’informations, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). <br>
Notez les propriétés de clé suivantes de cette ressource :
    - « kind » : la valeur doit être « Marketplace » dans le cas d’une application gérée par la Place de marché.
    - « ManagedResourceGroupId » : le groupe de ressources de l’abonnement du client dans lequel toutes les ressources définies dans applianceMainTemplate.json sont déployées.
    - « PublisherPackageId » : la chaîne qui identifie de façon unique le package. Cette valeur doit être construite de la façon suivante : il s’agit d’une concaténation de [ID_éditeur].[ID_offre]-preview[ID_SKU].[version_package].

  >[!IMPORTANT] 
  >Ce package doit contenir tous les modèles ou scripts imbriqués nécessaires à l’approvisionnement de cette application. Les fichiers suivants doivent être dans le dossier racine :  MainTemplate.json, applianceMainTemplate.json et applianceCreateUIDefinition.json.

- **ID de locataire\*** : ID de locataire Azure Active Directory de votre organisation.
- **Activer l’accès JIT ?\***  : sélectionnez **Oui** pour activer l’accès à l’administration juste-à-temps pour les déploiements de clients utilisant cette offre.

  >[!NOTE] 
  >Si vous activez le juste-à-temps, vous devez mettre à jour le fichier CreateUiDefinition.json pour prendre en charge l’accès JIT.

Pour une application managée, vous devez configurer l’autorisation et les paramètres de stratégie.


#### <a name="authorization"></a>Autorisation

Ajoutez l’identificateur Azure Active Directory de l’utilisateur, du groupe ou de l’application à laquelle vous souhaitez accorder l’autorisation pour le groupe de ressources managées. L’autorisation accordée est indiquée par l’ID de définition de rôle. Il peut s’agir d’un propriétaire, d’un contributeur ou de tout rôle personnalisé.


#### <a name="policy-settings"></a>Paramètres de stratégie

Ajoutez les stratégies auxquelles l’application managée est conforme. Pour en savoir plus sur les stratégies Azure Resource, consultez [Présentation d’Azure Policy](../../../governance/policy/overview.md).

   ![Autorisation et paramètres de stratégie pour une application managée](./media/azureapp-sku-details-managedapp-auth-policy.png)

**Pour créer une nouvelle autorisation :**

1. Sous **Autorisation**, sélectionnez **+ Nouvelle autorisation**.
2. Dans **ID du principal**, indiquez l’identificateur Azure Active Directory de l’utilisateur, du groupe ou de l’application à laquelle vous souhaitez accorder l’autorisation pour le groupe de ressources managées. L’autorisation accordée est indiquée par la définition de rôle.
3. Dans **Définition de rôle**, sélectionnez l’une des options suivantes dans la liste déroulante :  Propriétaire ou Contributeur. Pour plus d’informations, consultez [Rôles intégrés pour les ressources Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

>[!NOTE] 
>Plusieurs autorisations peuvent être ajoutées. Toutefois, il est recommandé de créer un groupe d’utilisateurs Active Directory et de spécifier son ID dans « PrincipalId ». Cela permettra d’ajouter davantage d’utilisateurs au groupe d’utilisateurs sans avoir à mettre à jour la référence SKU.

**Pour créer une nouvelle stratégie :**

1. Dans **Paramètres de stratégie**, sélectionnez **+ Nouvelle stratégie**.
2. Dans **Nom de la stratégie**, entrez le nom de la stratégie. La longueur maximale de ce nom est de 50 caractères.
3. Dans **Stratégies**, sélectionnez l’une des options de la liste déroulante. Choisissez la stratégie que le fournisseur de données souhaite activer lorsque l’application utilise les données. Pour plus d’informations, consultez [Exemples Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/index).

    ![Paramètres de stratégie pour une application managée](./media/azureapp-sku-policy-settings.png)

4. Dans **Référence de stratégie**, sélectionnez Gratuit ou Standard en tant que type de référence SKU de la stratégie. La référence SKU Standard est requise pour les stratégies d’audit.


## <a name="next-steps"></a>Étapes suivantes

Vous allez décrire plus précisément votre offre et fournir des ressources marketing sous l’[onglet Place de marché](./cpp-marketplace-tab.md). 
