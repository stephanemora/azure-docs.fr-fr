---
title: Déplacer un magasin de Configuration d’applications vers une autre région
description: Découvrez comment déplacer un magasin de Configuration d’applications vers une autre région.
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: c3ffe773e16eb7a658f219e980711eca2add1aac
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123077485"
---
# <a name="move-an-app-configuration-store-to-another-region"></a>Déplacer un magasin de Configuration d’applications vers une autre région 

Les magasins de Configuration d’applications sont spécifiques à une région et ne peuvent pas être déplacées d’une région à l’autre. Vous devez créer un magasin de Configuration d’applications dans la région cible, puis déplacer votre contenu du magasin source vers le nouveau magasin cible. Vous pouvez être amené à déplacer votre configuration vers une autre région pour diverses raisons. Par exemple, pour tirer parti d’une nouvelle région Azur avec prise en charge d’une Zone de disponibilité, pour déployer des fonctionnalités ou des services disponibles dans des régions spécifiques uniquement ou pour répondre à des exigences de stratégie et de gouvernance internes. 

Les étapes suivantes vous guident tout au long du processus de création d’un nouveau magasin cible et d’exportation de votre magasin actuel vers la nouvelle région. 

## <a name="design-considerations"></a>Remarques relatives à la conception

Avant de commencer, tenez compte des concepts suivants :

* Les noms des magasins de configuration sont globalement uniques. 
* Vous devez reconfigurer vos stratégies d’accès et paramètres de configuration réseau dans le nouveau magasin de configuration.

## <a name="create-the-target-configuration-store"></a>Créer le magasin de configuration cible

### <a name="portal"></a>[Portail](#tab/portal)     
Pour créer un nouveau magasin de Configuration d’applications dans le Portail, procédez comme suit : 
1.  Connectez-vous au [portail Azure](https://portal.azure.com). En haut à gauche de la page d’accueil, sélectionnez **Créer une ressource**. Dans la zone **Rechercher dans la Place de marché**, entrez *App Configuration* et sélectionnez <kbd>Entrée</kbd>. 

    ![Rechercher App Configuration](../../includes/media/azure-app-configuration-create/azure-portal-search.png)
1. Sélectionnez **Configuration d’application** dans les résultats de la recherche, puis **Créer**.

    ![Sélectionner Créer](../../includes/media/azure-app-configuration-create/azure-portal-app-configuration-create.png)
1. Dans le volet **Créer une ressource App Configuration**, entrez les paramètres suivants :
    
    | Paramètre | Valeur suggérée | Description |
    |---|---|---|
    | **Abonnement** | Votre abonnement | Sélectionnez l’abonnement Azure de votre magasin d’origine. |
    | **Groupe de ressources** | Votre groupe de ressources | Sélectionner le groupe de ressources Azure de votre magasin d’origine |
    | **Nom de la ressource** | Nom globalement unique | Entrez un nom de ressource unique à utiliser pour le magasin de Configuration d’applications cible. Ce nom ne peut pas être le même que celui du magasin de configuration précédent. |
    | **Lieu** | Votre emplacement cible | Sélectionnez la région cible vers laquelle vous souhaitez déplacer votre magasin de configuration. |
    | **Niveau tarifaire** | *Standard* | Sélectionnez le niveau tarifaire souhaité. Pour plus d’informations, consultez la [page de tarification App Configuration](https://azure.microsoft.com/pricing/details/app-configuration). |
1. Sélectionnez **Vérifier + créer** pour valider vos paramètres.
1. Sélectionnez **Create** (Créer). Le déploiement peut prendre quelques minutes.
1. Une fois que la ressource a été déployée, recréez les stratégies d’accès et les paramètres de configuration réseau de notre magasin source. Celles-ci ne seront pas transférées avec la configuration. Cela peut inclure l’utilisation d’identités managées, de réseaux virtuels et de l’accès au réseau public. 
    
#### <a name="azure-cli"></a>[Azure CLI](#tab/azcli)
Pour créer un nouveau magasin de Configuration d’applications dans l’interface CLI, procédez comme suit : 
1. Connectez-vous à l’interface CLI Azure avec vos informations d’identification.
    ```azurecli
    az login
    ```
1. Créez un nouveau magasin de configuration à l’aide de la commande `create`,
    ```azurecli
    az appconfig create -g MyResourceGroup -n MyResourceName -l targetlocation --sku Standard 
    ```
    puis entrez les paramètres suivants :

    | Paramètre | Valeur suggérée | Description |
    |---|---|---|
    | **Groupe de ressources** | Votre groupe de ressources | Sélectionner le groupe de ressources Azure de votre magasin d’origine |
    | **Nom de la ressource** | Nom globalement unique | Entrez un nom de ressource unique à utiliser pour le magasin de Configuration d’applications cible. Ce nom ne peut pas être le même que celui du magasin de configuration précédent. |
    | **Lieu** | Votre emplacement cible | Sélectionnez la région cible vers laquelle vous souhaitez déplacer votre magasin de configuration. |
    | **Sku** | *Standard* | Sélectionnez le niveau tarifaire souhaité. Pour plus d’informations, consultez la [page de tarification App Configuration](https://azure.microsoft.com/pricing/details/app-configuration). |
1. Le déploiement peut prendre quelques minutes. Une fois l’opération terminée, recréez les stratégies d’accès et les paramètres de configuration réseau de notre magasin source. Celles-ci ne seront pas transférées avec les valeurs de la configuration. Cela peut inclure l’utilisation d’identités managées, de réseaux virtuels et de l’accès au réseau public. Pour plus d’informations, consultez la [documentation CLI](./cli-samples.md).
---

## <a name="transfer-your-configuration-key-values"></a>Transférer la clé-les valeurs de votre configuration  

### <a name="portal"></a>[Portail](#tab/portal)
Pour exporter votre configuration dans le magasin cible à l’aide du Portail, procédez comme suit :
1. Accédez à votre magasin de configuration source dans le [portail Azure](https://portal.azure.com) et sélectionnez **Import/Export** sous **Opérations** .
1. Sélectionnez **Exporter**, puis choisissez  **Configuration de l'application** dans la liste déroulante **Service cible**. 
    ![Exporter vers un autre magasin de configuration](media/export-to-config-store.png)
1. Cliquez sur **Sélectionner une ressource**, puis entrez votre **Abonnement** et votre **Groupe de ressources**. La **Ressource** est le nom du magasin de configuration cible que vous avez créée précédemment. 
1. Sélectionnez **Appliquer** pour vérifier votre magasin de configuration cible. 
1. Laissez les valeurs par défaut des champs heure et Etiquette, puis sélectionnez **Appliquer**. 
1. Pour vérifier que vos configurations ont été transférées avec succès de votre source vers votre magasin cible, accédez à votre magasin de configuration cible dans le portail. Sélectionnez **Explorateur de configuration** sous **Opérations** , puis vérifiez qu’il contient les mêmes paires clé/valeur que celles de votre magasin d’origine. 
    > [!NOTE]
    > Ce processus autorise uniquement l’exportation des valeurs-de la clé de la configuration en une seule étiquette à la fois. Pour en exporter plusieurs, répétez les étapes 2-5 pour chaque étiquette. 

### <a name="azure-cli"></a>[Azure CLI](#tab/azcli)
Pour exporter votre configuration dans le magasin cible à l’aide via Azure, procédez comme suit :
1. Dans l’interface CLI Azure, entrez la commande suivante pour exporter toutes les valeurs de votre magasin de configuration source vers votre magasin de configuration cible. 
    ```azurecli
    az appconfig kv export -n SourceConfigurationStore -d appconfig --dest-name TargetConfigurationStore --key * --label * --preserve-labels
    ```
1. Pour vérifier que vos configurations ont été transférées avec succès de votre source vers votre magasin cible, répertoriez toutes les valeurs de clé dans votre magasin cible. 
    ```azurecli
    az appconfig kv list -n TargetAppConfiguration --all
    ```
---
## <a name="delete-your-source-configuration-store"></a>Supprimer votre magasin de configuration source 

Si la configuration a été transférée vers le magasin cible, vous pouvez choisir de supprimer votre magasin de configuration source. 

### <a name="portal"></a>[Portail](#tab/portal)
Pour supprimer votre magasin de configuration source dans le Portail, procédez comme suit :
1. Connectez-vous au [portail Azure](https://portal.azure.com), puis sélectionnez **Groupes de ressources**.
1. Dans la zone **Filtrer par nom**, entrez le nom de votre groupe de ressources. 
1. Dans la liste de résultats, sélectionnez le nom du groupe de ressources pour afficher une vue d’ensemble.
1. Sélectionnez votre magasin de configuration source, puis, dans le panneau **Vue d’ensemble**, sélectionnez **Supprimer**. 
1. Vous êtes invité à confirmer la suppression du magasin de configuration, sélectionnez **Oui**.

Après quelques instants, le magasin de configuration source aura été supprimé.

### <a name="azure-cli"></a>[Azure CLI](#tab/azcli)
Pour supprimer votre magasin de configuration source dans l’interface CLI, procédez comme suit :
1. Dans Azure CLI, exécutez la commande suivante : 
    ```azurecli
    az appconfig delete -g ResourceGroupName -n SourceConfiguration
    ```
    Notez que le **Groupe de ressources** est celui associé à votre magasin de Configuration source. 
1. La suppression du magasin de configuration source peut prendre quelques instants. Vous pouvez vérifier que l’opération a réussi en répertoriant tous les magasins de configuration actuels dans votre groupe de ressources. 
    ```azurecli
    az appconfig list -g MyResourceGroup
    ```
    Après quelques instants, le magasin de configuration source aura été supprimé.

---
## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Sauvegarder automatiquement les clés-valeurs à partir des magasins de configuration d’applications Azure](./howto-move-resource-between-regions.md)
>[Résilience et récupération d’urgence de la Configuration d’applications Azure](./concept-disaster-recovery.md)
