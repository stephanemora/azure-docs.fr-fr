---
title: Créer un espace de travail sécurisé
titleSuffix: Azure Machine Learning
description: Créez un espace de travail Azure Machine Learning et les services Azure requis au sein d’un réseau virtuel sécurisé.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jhirono
ms.author: larryfr
author: blackmist
ms.date: 09/15/2021
ms.topic: how-to
ms.custom: subject-rbac-steps
ms.openlocfilehash: f0b4f19e8c1e06aa8ab5657fd1c70a75814451ad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128612184"
---
# <a name="how-to-create-a-secure-workspace"></a>Procédure de création d’un espace de travail sécurisé

Dans cet article, vous allez apprendre à créer un espace de travail Azure Machine Learning sécurisé et à vous y connecter. Un espace de travail sécurisé utilise Réseau virtuel Microsoft Azure pour créer une limite de sécurité autour des ressources utilisées par Azure Machine Learning. 

Dans ce tutoriel, vous accomplissez les tâches suivantes :

> [!div class="checklist"]
> * Créer un réseau virtuel (VNet) Azure pour __sécuriser les communications entre les services du réseau virtuel__.
> * Créer un compte de stockage Azure (blob et fichier) derrière le réseau virtuel. Ce service est utilisé comme __stockage par défaut pour l’espace de travail__.
> * Créer un coffre de clés Azure derrière le réseau virtuel. Ce service est utilisé pour __stocker les secrets utilisés par l’espace de travail__. Par exemple, les informations de sécurité nécessaires pour accéder au compte de stockage.
> * Créer un registre de conteneurs Azure. Ce service est utilisé comme référentiel pour les images Docker. __Les images Docker fournissent les environnements Compute nécessaires à la formation d’un modèle Machine Learning ou au déploiement d’un modèle formé en tant que point de terminaison__.
> * Créez un espace de travail Machine Learning.
> * Créer un serveur de rebond. Un serveur de rebond est une machine virtuelle Azure qui se trouve derrière le réseau virtuel. Étant donné que le réseau virtuel restreint l’accès à l’Internet public, __le serveur de rebond est utilisé comme moyen de se connecter aux ressources derrière le réseau virtuel__.
> * Configurer Azure Machine Learning studio pour qu’il fonctionne derrière un réseau virtuel. Le studio fournit une __interface web pour Azure Machine Learning__.
> * Créer un cluster de calcul Azure Machine Learning Un cluster de calcul est utilisé pour __former des modèles Machine Learning dans le cloud__. Dans les configurations où Azure Container Registry se trouve derrière le réseau virtuel, il est également utilisé pour créer des images Docker.
> * Se connecter au serveur de rebond et utiliser Azure Machine Learning studio.

Si votre environnement remplit les conditions préalables et que vous avez l’habitude d’utiliser des modèles ARM, les cinq premières étapes de ce tutoriel peuvent également être réalisées en sélectionnant le bouton « Déployer sur Azure ». Vous pouvez poursuivre la lecture à partir de [Se connecter à l’espace de travail](#connect-to-the-workspace).

[![Déployer sur Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.machinelearningservices%2Fmachine-learning-advanced%2Fazuredeploy.json)
[![Déployer sur Azure US Gov](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazuregov.svg?sanitize=true)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.machinelearningservices%2Fmachine-learning-advanced%2Fazuredeploy.json)

## <a name="prerequisites"></a>Configuration requise

* Connaissance des réseaux virtuels Azure et de la mise en réseau des adresses IP.
* Bien que la plupart des étapes décrites dans cet article utilisent le portail Azure ou Azure Machine Learning studio, certaines étapes utilisent l’extension Azure CLI pour Machine Learning.

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Pour créer un réseau virtuel, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez le menu du portail dans le coin supérieur gauche. Dans le menu, sélectionnez __+ Créer une ressource__, puis entrez __Réseau virtuel__ dans le champ de recherche. Sélectionnez l’entrée __Réseau virtuel__, puis sélectionnez __Créer__.


    :::image type="content" source="./media/tutorial-create-secure-workspace/create-resource-search-vnet.png" alt-text="Recherche dans l’interface utilisateur Créer une ressource":::

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-resource-vnet.png" alt-text="Création d’un réseau virtuel":::

1. Dans l’onglet __Informations de base__, sélectionnez l’__abonnement__ Azure à utiliser pour cette ressource, puis sélectionnez ou créez un __groupe de ressources__. Sous __Détails de l’instance__, saisissez un __nom__ convivial pour votre réseau virtuel et sélectionnez la __région__ dans laquelle le créer.

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-vnet-basics.png" alt-text="Image de la configuration de base du réseau virtuel":::

1. Sélectionnez l’onglet __Adresses IP__. Les paramètres par défaut doivent être similaires à l’image suivante :

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-vnet-ip-address-default.png" alt-text="Écran Adresse IP par défaut":::

    Procédez comme suit pour configurer l’adresse IP et configurer un sous-réseau pour les ressources de formation et de scoring :

    > [!TIP]
    > Bien que vous puissiez utiliser un seul sous-réseau pour toutes les ressources Azure ML, les étapes décrites dans cet article montrent comment créer deux sous-réseaux pour séparer les ressources de formation et de scoring.
    >
    > L’espace de travail et les autres services de dépendance iront dans le sous-réseau de formation. Ils peuvent toujours être utilisés par des ressources dans d’autres sous-réseaux, comme le sous-réseau de scoring.

    1. Regardez la valeur de l’__espace d’adressage IPv4__ par défaut. Dans la capture d’écran, la valeur est __172.17.0.0/16__. __Cette valeur peut être différente pour vous__. Bien que vous puissiez utiliser une valeur différente, le reste des étapes de ce tutoriel est basé sur la valeur 172.17.0.0/16.
    1. Sélectionnez le sous-réseau __Par défaut__, puis sélectionnez __Supprimer le sous-réseau__.
    
        :::image type="content" source="./media/tutorial-create-secure-workspace/delete-default-subnet.png" alt-text="Capture d’écran de la suppression du sous-réseau par défaut":::

    1. Pour créer un sous-réseau contenant l’espace de travail, les services de dépendance et les ressources utilisées pour la formation, sélectionnez __+ Ajouter un sous-réseau__ et utilisez les valeurs suivantes pour le sous-réseau :
        * __Nom du sous-réseau__ : Formation
        * __Plage d’adresses de sous-réseau__ : 172.17.0.0/24

        :::image type="content" source="./media/tutorial-create-secure-workspace/vnet-add-training-subnet.png" alt-text="Capture d’écran du sous-réseau Formation":::

        > [!TIP]
        > Si vous prévoyez d’utiliser un _point de terminaison de service_ pour ajouter vos instances Stockage Azure, Azure Key Vault et Azure Container Registry au réseau virtuel, sélectionnez les éléments suivants sous __Services__ :
        > * __Microsoft.Storage__
        > * __Microsoft.KeyVault__
        > * __Microsoft.ContainerRegistry__
        >
        > Si vous envisagez d’utiliser un _point de terminaison privé_ pour ajouter ces services au réseau virtuel, vous n’avez pas besoin de sélectionner ces entrées. Les étapes décrites dans cet article utilisent un point de terminaison privé pour ces services. Vous n’avez donc pas besoin de les sélectionner lorsque vous suivez ces étapes.

    1. Pour créer un sous-réseau pour les ressources de calcul utilisées pour le scoring de vos modèles, sélectionnez à nouveau __+ Ajouter un sous-réseau__, et utilisez les valeurs suivantes :
        * __Nom du sous-réseau__ : Scoring
        * __Plage d’adresses de sous-réseau__ : 172.17.1.0/24

        :::image type="content" source="./media/tutorial-create-secure-workspace/vnet-add-scoring-subnet.png" alt-text="Capture d’écran du sous-réseau Scoring":::

        > [!TIP]
        > Si vous prévoyez d’utiliser un _point de terminaison de service_ pour ajouter vos instances Stockage Azure, Azure Key Vault et Azure Container Registry au réseau virtuel, sélectionnez les éléments suivants sous __Services__ :
        > * __Microsoft.Storage__
        > * __Microsoft.KeyVault__
        > * __Microsoft.ContainerRegistry__
        >
        > Si vous envisagez d’utiliser un _point de terminaison privé_ pour ajouter ces services au réseau virtuel, vous n’avez pas besoin de sélectionner ces entrées. Les étapes décrites dans cet article utilisent un point de terminaison privé pour ces services. Vous n’avez donc pas besoin de les sélectionner lorsque vous suivez ces étapes.

1. Sélectionnez __Sécurité__. Pour __BastionHost__, sélectionnez __Activer__. [Azure Bastion](../bastion/bastion-overview.md) fournit un moyen sécurisé d’accéder au serveur de rebond de la machine virtuelle que vous créerez à l’intérieur du réseau virtuel à une étape ultérieure. Utilisez les valeurs suivantes pour les champs restants :

    * __Nom Bastion__ : nom unique pour cette instance Bastion
    * __Espace AzureBastionSubnetAddress__ : 172.17.2.0/27
    * __IP publique__ : créez une nouvelle IP publique

    Pour les autres champs, utilisez les valeurs par défaut.

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-bastion.png" alt-text="Capture d’écran de la configuration de Bastion":::

1. Sélectionnez __Revoir + créer__.

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-vnet-ip-address-final.png" alt-text="Capture d’écran montrant le bouton Vérifier + créer":::

1. Vérifiez que les informations sont correctes, puis sélectionnez __Créer__.

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-vnet-review.png" alt-text="Capture d’écran de la page de révision":::

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez le menu du portail dans le coin supérieur gauche. Dans le menu, sélectionnez __+ Créer une ressource__, puis entrez __Compte de stockage__. Sélectionnez l’entrée __Compte de stockage__, puis sélectionnez __Créer__.
1. Dans l’onglet __Informations de base__, sélectionnez l’__abonnement__, le __groupe de ressources__ et la __région__ que vous avez précédemment utilisés pour le réseau virtuel. Entrez un __nom de compte de stockage__ unique et définissez __Redondance__ sur __Stockage localement redondant (LRS)__ .

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-storage.png" alt-text="Image de la configuration de base d’un compte de stockage":::

1. Dans l’onglet __Mise en réseau__, sélectionnez __Point de terminaison privé__, puis __+ Ajouter un point de terminaison privé__.

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-enable-private-endpoint.png" alt-text="Interface utilisateur pour ajouter le réseau privé blob":::

1. Dans le formulaire __Créer un point de terminaison privé__, utilisez les valeurs suivantes :
    * __Abonnement__ : Le même abonnement Azure qui contient les ressources précédentes que vous avez créées.
    * __Groupe de ressources__ : Le même groupe de ressources Azure qui contient les ressources précédentes que vous avez créées.
    * __Localisation__ : La même région Azure qui contient les ressources précédentes que vous avez créées.
    * __Nom__ : Un nom unique pour ce point de terminaison privé.
    * __Sous-ressource cible__ : blob.
    * __Réseau virtuel__ : Le réseau virtuel que vous avez créé précédemment.
    * __Sous-réseau__ : Formation (172.17.0.0/24).
    * __Intégration à un DNS privé__ : Oui.
    * __Zone DNS privée__ : privatelink.blob.core.windows.net.

    Sélectionnez __OK__ pour créer le point de terminaison privé.

1. Sélectionnez __Revoir + créer__. Vérifiez que les informations sont correctes, puis sélectionnez __Créer__.

1. Une fois le compte de stockage créé, sélectionnez __Accéder à la ressource__ :

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-go-to-resource.png" alt-text="Aller à la nouvelle ressource de stockage":::

1. Dans la navigation de gauche, sélectionnez __Mise en réseau__ puis l’onglet __Connexions de point de terminaison privé__, puis sélectionnez __+ Point de terminaison privé__ :

    > [!NOTE]
    > Si vous avez créé un point de terminaison privé pour le stockage Blob dans les étapes précédentes, vous devez également en créer un pour le stockage Fichier.

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-file-networking.png" alt-text="Interface utilisateur pour la mise en réseau du compte de stockage":::

1. Dans le formulaire __Créer un point de terminaison privé__, utilisez les mêmes __abonnement__, __groupe de ressources__ et __région__ que ceux que vous avez utilisés pour les ressources précédentes. Entrez un __nom__ unique.

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-file-private-endpoint.png" alt-text="Interface utilisateur pour ajouter le point de terminaison privé de fichier":::

1. Sélectionnez __Suivant : Ressource__, puis définissez __Sous-ressource cible__ sur __fichier__.

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-file-private-endpoint-resource.png" alt-text="Ajouter la sous-ressource « fichier »":::

1. Sélectionnez __Suivant : Configuration__, puis utilisez les valeurs suivantes :
    * __Réseau virtuel__ : réseau virtuel que vous avez créé précédemment
    * __Sous-réseau__ : Formation
    * __Intégrer à une zone DNS privée__ : Oui
    * __Zone DNS privée__ : privatelink.file.core.windows.net

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-file-private-endpoint-config.png" alt-text="Interface utilisateur pour configurer le point de terminaison privé de fichier":::

1. Sélectionnez __Vérifier + créer__. Vérifiez que les informations sont correctes, puis sélectionnez __Créer__.

## <a name="create-a-key-vault"></a>Création d’un coffre de clés

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez le menu du portail dans le coin supérieur gauche. Dans le menu, sélectionnez __+ Créer une ressource__, puis entrez __Coffre de clés__. Sélectionnez l’entrée __Coffre de clés__, puis sélectionnez __Créer__.
1. Dans l’onglet __Informations de base__, sélectionnez l’__abonnement__, le __groupe de ressources__ et la __région__ que vous avez précédemment utilisés pour le réseau virtuel. Entrez un __nom de coffre de clés__ unique. Conservez la valeur par défaut dans les autres champs.

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-key-vault.png" alt-text="Crée un coffre de clés":::

1. Dans l’onglet __Mise en réseau__, sélectionnez __Point de terminaison privé__, puis __+ Ajouter__.

    :::image type="content" source="./media/tutorial-create-secure-workspace/key-vault-networking.png" alt-text="Mise en réseau du coffre de clés":::

1. Dans le formulaire __Créer un point de terminaison privé__, utilisez les valeurs suivantes :
    * __Abonnement__ : Le même abonnement Azure qui contient les ressources précédentes que vous avez créées.
    * __Groupe de ressources__ : Le même groupe de ressources Azure qui contient les ressources précédentes que vous avez créées.
    * __Localisation__ : La même région Azure qui contient les ressources précédentes que vous avez créées.
    * __Nom__ : Un nom unique pour ce point de terminaison privé.
    * __Sous-ressource cible__ : coffre
    * __Réseau virtuel__ : Le réseau virtuel que vous avez créé précédemment.
    * __Sous-réseau__ : Formation (172.17.0.0/24).
    * __Intégration à un DNS privé__ : Oui.
    * __Zone DNS privée__ : privatelink.vaultcore.azure.net

    Sélectionnez __OK__ pour créer le point de terminaison privé.

    :::image type="content" source="./media/tutorial-create-secure-workspace/key-vault-private-endpoint.png" alt-text="Configurer un point de terminaison privé de coffre de clés":::

1. Sélectionnez __Revoir + créer__. Vérifiez que les informations sont correctes, puis sélectionnez __Créer__.

## <a name="create-a-container-registry"></a>Créer un registre de conteneur

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez le menu du portail dans le coin supérieur gauche. Dans le menu, sélectionnez __+ Créer une ressource__, puis entrez __Registre de conteneurs__. Sélectionnez l’entrée __Registre de conteneurs__, puis sélectionnez __Créer__.
1. Dans l’onglet __Informations de base__, sélectionnez l’__abonnement__, le __groupe de ressources__ et la __localisation__ que vous avez précédemment utilisés pour le réseau virtuel. Entrez un __nom de registre__ unique et définissez le __niveau tarifaire__ sur __Premium__.

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-container-registry.png" alt-text="Créer un registre de conteneur":::

1. Dans l’onglet __Mise en réseau__, sélectionnez __Point de terminaison privé__, puis __+ Ajouter__.

    :::image type="content" source="./media/tutorial-create-secure-workspace/container-registry-networking.png" alt-text="Mise en réseau du registre de conteneurs":::

1. Dans le formulaire __Créer un point de terminaison privé__, utilisez les valeurs suivantes :
    * __Abonnement__ : Le même abonnement Azure qui contient les ressources précédentes que vous avez créées.
    * __Groupe de ressources__ : Le même groupe de ressources Azure qui contient les ressources précédentes que vous avez créées.
    * __Localisation__ : La même région Azure qui contient les ressources précédentes que vous avez créées.
    * __Nom__ : Un nom unique pour ce point de terminaison privé.
    * __Sous-ressource cible__ : registre
    * __Réseau virtuel__ : Le réseau virtuel que vous avez créé précédemment.
    * __Sous-réseau__ : Formation (172.17.0.0/24).
    * __Intégration à un DNS privé__ : Oui.
    * __Zone DNS privée__ : privatelink.azurecr.io

    Sélectionnez __OK__ pour créer le point de terminaison privé.

    :::image type="content" source="./media/tutorial-create-secure-workspace/container-registry-private-endpoint.png" alt-text="Configurer un point de terminaison privé de registre de conteneurs":::

1. Sélectionnez __Revoir + créer__. Vérifiez que les informations sont correctes, puis sélectionnez __Créer__.
1. Une fois le registre de conteneurs créé, sélectionnez __Accéder à la ressource__.

    :::image type="content" source="./media/tutorial-create-secure-workspace/container-registry-go-to-resource.png" alt-text="Sélectionner « accéder à la ressource »":::

1. Dans la partie gauche de la page, sélectionnez __Clés d’accès__, puis activez __Utilisateur administrateur__. Ce paramètre est nécessaire lorsque vous utilisez Azure Container Registry à l’intérieur d’un réseau virtuel avec Azure Machine Learning.

    :::image type="content" source="./media/tutorial-create-secure-workspace/container-registry-admin-user.png" alt-text="Capture d’écran du bouton bascule utilisateur administrateur":::

## <a name="create-a-workspace"></a>Créer un espace de travail

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez le menu du portail dans le coin supérieur gauche. Dans le menu, sélectionnez __+ Créer une ressource__, puis entrez __Machine Learning__. Sélectionnez l’entrée __Machine Learning__, puis sélectionnez __Créer__.

    :::image type="content" source="./media/tutorial-create-secure-workspace/machine-learning-create.png" alt-text="{alt-text}":::

1. Dans l’onglet __Informations de base__, sélectionnez l’__abonnement__, le __groupe de ressources__ et la __région__ que vous avez précédemment utilisés pour le réseau virtuel. Utilisez les valeurs suivantes pour les autres champs :
    * __Nom de l’espace de travail__ : nom unique pour votre espace de travail.
    * __Compte de stockage__ : sélectionnez le compte de stockage que vous avez créé précédemment.
    * __Coffre de clés__ : sélectionnez le coffre de clés que vous avez créé précédemment.
    * __Application Insights__ : utilisez la valeur par défaut.
    * __Registre des conteneurs__ : utilisez le registre de conteneurs que vous avez créé précédemment.

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-machine-learning-workspace.png" alt-text="Configuration de base de l’espace de travail":::

1. Dans l’onglet __Mise en réseau__, sélectionnez __Point de terminaison privé__, puis __+ Ajouter__.

    :::image type="content" source="./media/tutorial-create-secure-workspace/machine-learning-workspace-networking.png" alt-text="Mise en réseau de l’espace de travail":::

1. Dans le formulaire __Créer un point de terminaison privé__, utilisez les valeurs suivantes : 
    * __Abonnement__ : Le même abonnement Azure qui contient les ressources précédentes que vous avez créées.
    * __Groupe de ressources__ : Le même groupe de ressources Azure qui contient les ressources précédentes que vous avez créées.
    * __Localisation__ : La même région Azure qui contient les ressources précédentes que vous avez créées.
    * __Nom__ : Un nom unique pour ce point de terminaison privé.
    * __Sous-ressource cible__ : amlworkspace
    * __Réseau virtuel__ : Le réseau virtuel que vous avez créé précédemment.
    * __Sous-réseau__ : Formation (172.17.0.0/24).
    * __Intégration à un DNS privé__ : Oui.
    * __Zone DNS privée__ : Laissez les deux zones DNS privées aux valeurs par défaut de __privatelink.api.azureml.ms__ et __privatelink.notebooks.azure.net__.

    Sélectionnez __OK__ pour créer le point de terminaison privé.

    :::image type="content" source="./media/tutorial-create-secure-workspace/machine-learning-workspace-private-endpoint.png" alt-text="Capture d’écran de la configuration du réseau privé de l’espace de travail":::

1. Sélectionnez __Revoir + créer__. Vérifiez que les informations sont correctes, puis sélectionnez __Créer__.
1. Une fois l’espace de travail créé, sélectionnez __Accéder à la ressource__.
1. Dans la section __Paramètres__ à gauche, sélectionnez __Connexions de point de terminaison privé__, puis sélectionnez le lien dans la colonne __Point de terminaison privé__ :

    :::image type="content" source="./media/tutorial-create-secure-workspace/workspace-private-endpoint-connections.png" alt-text="Capture d’écran des connexions de point de terminaison privé de l’espace de travail":::

1. Une fois les informations sur le point de terminaison privé affichées, sélectionnez __Configuration DNS__ dans la partie gauche de la page. Enregistrez l’adresse IP et le nom de domaine complet (FQDN) qui sont sur cette page, car ils seront utilisés plus tard.

    :::image type="content" source="./media/tutorial-create-secure-workspace/workspace-private-endpoint-dns.png" alt-text="Capture d’écran des entrées IP et FQDN":::

> [!IMPORTANT]
> Certaines étapes de configuration sont encore nécessaires avant que vous puissiez utiliser pleinement l’espace de travail. Toutefois, celles-ci nécessitent que vous vous connectiez à l’espace de travail.

## <a name="enable-studio"></a>Activer le studio

Azure Machine Learning studio est une application web qui vous permet de gérer facilement votre espace de travail. Toutefois, elle nécessite une configuration supplémentaire avant de pouvoir être utilisée avec des ressources sécurisées à l’intérieur d’un réseau virtuel. Suivez les étapes suivantes pour activer le studio :

1. Lorsque vous utilisez un compte stockage Azure disposant d’un point de terminaison privé, ajoutez le principal du service pour l’espace de travail en tant que __Lecteur__ pour le(s) point(s) de terminaison privé(s) de stockage. À partir du portail Azure, sélectionnez votre compte de stockage, puis sélectionnez __Mise en réseau__. Ensuite, sélectionnez __Connexions de point de terminaison privé__.

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-private-endpoint-select.png" alt-text="Capture d’écran des points de terminaison privés de stockage":::

1. Pour __chaque point de terminaison privé répertorié__, procédez comme suit :

    1. Sélectionnez le lien dans la colonne __Point de terminaison privé__.
    
        :::image type="content" source="./media/tutorial-create-secure-workspace/storage-private-endpoint-selected.png" alt-text="Capture d’écran des points de terminaison à sélectionner":::

    1. Sélectionnez __Contrôle d’accès (IAM)__ sur le côté gauche.
    1. Sélectionnez __+ Ajouter__, puis __Ajouter une attribution de rôle (préversion)__ .

        ![Page Contrôle d’accès (IAM) avec le menu Ajouter une attribution de rôle ouvert.](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

    1. Dans l’onglet __Rôle__, sélectionnez __Lecteur__.

        ![Page Ajouter une attribution de rôle avec l’onglet Rôle sélectionné.](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

    1. Dans l’onglet __Membres__, sélectionnez __Utilisateur, groupe ou principal de service__ dans la zone __Attribuer l’accès à__, puis sélectionnez __+ Sélectionner des membres__. Dans la boîte de dialogue __Sélectionner des membres__, entrez le nom de votre espace de travail Azure Machine Learning. Sélectionnez le principal de service de l’espace de travail, puis utilisez le bouton __Sélectionner__.

    1. Dans l’onglet **Passer en revue + affecter**, sélectionnez **Passer en revue + affecter** pour affecter le rôle.

## <a name="connect-to-the-workspace"></a>Se connecter à l’espace de travail

Il existe plusieurs façons de se connecter à l’espace de travail sécurisé. Les étapes de cet article utilisent un __serveur de rebond__, qui est une machine virtuelle dans le réseau virtuel. Vous pouvez vous y connecter à l’aide de votre navigateur web et d’Azure Bastion. Le tableau suivant répertorie plusieurs autres façons de se connecter à l’espace de travail sécurisé :

| Méthode | Description |
| ----- | ----- |
| [Passerelle VPN Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md) | Permet de connecter des réseaux locaux au réseau virtuel via une connexion privée. La connexion est établie via l’Internet public. |
| [ExpressRoute](https://azure.microsoft.com/services/expressroute/) | Permet de connecter des réseaux locaux au cloud via une connexion privée. La connexion est établie à l’aide d’un fournisseur de connectivité. |

> [!IMPORTANT]
> Quand vous utilisez une __passerelle VPN__ ou __ExpressRoute__, vous devez planifier le fonctionnement de la résolution de noms entre vos ressources locales et celles se trouvant dans le réseau virtuel. Pour plus d’informations, consultez [Utiliser un serveur DNS personnalisé](how-to-custom-dns.md).

### <a name="create-a-jump-box-vm"></a>Créer un serveur de rebond (machine virtuelle)

Utilisez les étapes suivantes pour créer une machine virtuelle de science des données à utiliser comme serveur de rebond :

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez le menu du portail dans le coin supérieur gauche. Dans le menu, sélectionnez __+ Créer une ressource__, puis entrez __Machine virtuelle de science des données__. Sélectionnez l’entrée __Machine virtuelle de science des données – Windows__, puis sélectionnez __Créer__.
1. Dans l’onglet __Informations de base__, sélectionnez l’__abonnement__, le __groupe de ressources__ et la __région__ que vous avez précédemment utilisés pour le réseau virtuel. Fournissez un __nom de machine virtuelle__ unique, un __nom d’utilisateur__ et un __mot de passe__. Pour les autres champs, utilisez les valeurs par défaut.

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-virtual-machine-basic.png" alt-text="Image de la configuration de base d’une machine virtuelle":::

1. Sélectionnez __Mise en réseau__, puis sélectionnez le __réseau virtuel__ que vous avez créé précédemment. Utilisez les informations suivantes pour définir les champs restants :

    * Sélectionnez le sous-réseau __Formation__.
    * Définissez l’__IP publique__ sur __Aucune__.
    * Conservez la valeur par défaut dans les autres champs.

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-virtual-machine-network.png" alt-text="Image de la configuration réseau de la machine virtuelle":::

1. Sélectionnez __Revoir + créer__. Vérifiez que les informations sont correctes, puis sélectionnez __Créer__.


### <a name="connect-to-the-jump-box"></a>Se connecter au serveur de rebond

1. Une fois la machine virtuelle créée, sélectionnez __Accéder à la ressource__.
1. En haut de la page, sélectionnez __Connexion__ et ensuite __Bastion__.

    :::image type="content" source="./media/tutorial-create-secure-workspace/virtual-machine-connect.png" alt-text="Image de l’interface utilisateur Connexion/Bastion":::

1. Sélectionnez __Utiliser Bastion__, puis fournissez vos informations d’authentification pour la machine virtuelle, et une connexion sera établie dans votre navigateur.

    :::image type="content" source="./media/tutorial-create-secure-workspace/use-bastion.png" alt-text="Image de la boîte de dialogue Utiliser Bastion":::

## <a name="create-a-compute-cluster-and-compute-instance"></a>Créer un cluster de calcul et une instance de calcul

Un cluster de calcul est utilisé par vos travaux de formation. Une instance de calcul fournit une expérience Jupyter Notebook sur une ressource de calcul partagée jointe à votre espace de travail.

1. À partir d’une connexion Azure Bastion au serveur de rebond, ouvrez le navigateur __Microsoft Edge__ sur le bureau distant.
1. Dans la session de navigation à distance, accédez à __https://ml.azure.com__ . Lorsque vous y êtes invité, authentifiez-vous à l’aide de votre compte Azure AD.
1. Dans l’écran __Bienvenue !__ , sélectionnez l’__espace de travail Machine Learning__ que vous avez créé précédemment, puis sélectionnez __Démarrage__.

    > [!TIP]
    > Si votre compte Azure AD a accès à plusieurs abonnements ou répertoires, utilisez la liste déroulante __Annuaire et abonnement__ pour sélectionner celui qui contient l’espace de travail.

    :::image type="content" source="./media/tutorial-create-secure-workspace/studio-select-workspace.png" alt-text="Capture d’écran de la boîte de dialogue Sélectionner un espace de travail":::

1. Dans le studio, sélectionnez __Calcul__, __Clusters de calcul__, puis __+ Nouveau__.

    :::image type="content" source="./media/tutorial-create-secure-workspace/studio-new-compute-cluster.png" alt-text="Capture d’écran du flux de travail d’un nouveau cluster de calcul":::

1. Dans la boîte de dialogue __Machine virtuelle__, sélectionnez __Suivant__ pour accepter la configuration par défaut de la machine virtuelle.

    :::image type="content" source="./media/tutorial-create-secure-workspace/studio-new-compute-vm.png" alt-text="Capture d’écran des paramètres de machine virtuelle du cluster de calcul":::
    
1. Dans la boîte de dialogue __Configurer les paramètres__, entrez __cpu-cluster__ comme __nom de cluster__. Définissez le __sous-réseau__ sur __Formation__, puis sélectionnez __Créer__ pour créer le cluster.

    > [!TIP]
    > Les clusters de calcul mettent à l’échelle les nœuds du cluster dynamiquement selon les besoins. Nous recommandons de laisser le nombre minimum de nœuds à 0 pour réduire les coûts lorsque le cluster n’est pas utilisé.

    :::image type="content" source="./media/tutorial-create-secure-workspace/studio-new-compute-settings.png" alt-text="Capture d’écran des paramètre d’un nouveau cluster de calcul":::

1. Dans le studio, sélectionnez __Calcul__, __Instance de calcul__, puis __+ Nouveau__.

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-compute-instance.png" alt-text="Capture d’écran du flux de travail d’une nouvelle instance de calcul":::

1. Dans la boîte de dialogue __Machine virtuelle__, entrez un __nom d’ordinateur__ unique et sélectionnez __Suivant : Paramètres avancés__.

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-compute-instance-vm.png" alt-text="Capture d’écran des paramètres de machine virtuelle de l’instance de calcul":::

1. Dans la boîte de dialogue __Paramètres avancés__, définissez le __sous-réseau__ sur __Formation__, puis sélectionnez __Créer__.

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-compute-instance-settings.png" alt-text="Capture d’écran des paramètres de l’instance de calcul":::

> [!TIP]
> Lorsque vous créez un cluster de calcul ou une instance de calcul, Azure Machine Learning ajoute dynamiquement un groupe de sécurité réseau (NSG). Ce groupe de sécurité réseau contient les règles suivantes, qui sont spécifiques au cluster de calcul et à l’instance de calcul :
> 
> * Autoriser le trafic TCP entrant sur les ports 29876-29877 depuis l’étiquette de service `BatchNodeManagement`.
> * Autoriser le trafic TCP entrant sur le port 44224 depuis l’étiquette de service `AzureMachineLearning`.
>
> La capture d’écran suivante présente un exemple de ces règles :
>
> :::image type="content" source="./media/how-to-secure-training-vnet/compute-instance-cluster-network-security-group.png" alt-text="Capture d’écran de NSG":::

Pour plus d’informations sur la création d’un cluster de calcul et d’une instance de calcul, y compris sur la façon de le faire avec Python et l’interface CLI, consultez les articles suivants :

* [Créer un cluster de calcul](how-to-create-attach-compute-cluster.md)
* [Créer une instance de calcul](how-to-create-manage-compute-instance.md)

## <a name="configure-image-builds"></a>Configurer des builds d’image

Lorsqu’Azure Container Registry se trouve derrière le réseau virtuel, Azure Machine Learning ne peut pas l’utiliser pour générer directement des images Docker (utilisées pour la formation et le déploiement). Au lieu de cela, configurez l’espace de travail pour utiliser le cluster de calcul que vous avez créé précédemment. Utilisez les étapes suivantes pour créer un cluster de calcul et configurer l’espace de travail pour l’utiliser afin de générer des images :

1. Accédez à [https://shell.azure.com/](https://shell.azure.com/) pour ouvrir Azure Cloud Shell.
1. À partir de Cloud Shell, utilisez la commande suivante pour installer l’interface CLI 1.0 pour Azure Machine Learning :

    ```azurecli-interactive
    az extension add -n azure-cli-ml
    ```

1. Pour mettre à jour l’espace de travail afin d’utiliser le cluster de calcul pour générer des images Docker : Remplacez `docs-ml-rg` par votre groupe de ressources. Remplacez `docs-ml-ws` par votre espace de travail. Remplacez `cpu-cluster` par le cluster de calcul à utiliser :

    ```azurecli-interactive
    az ml workspace update -g docs-ml-rg -w docs-ml-ws --image-build-compute cpu-cluster
    ```

    > [!NOTE]
    > Vous pouvez utiliser le même cluster de calcul pour effectuer l’apprentissage de modèles et générer des images Docker pour l’espace de travail.

## <a name="use-the-workspace"></a>Utiliser l'espace de travail

À ce stade, vous pouvez utiliser le studio pour travailler de manière interactive avec les notebooks sur l’instance de calcul et exécuter des tâches de formation sur le cluster de calcul. Pour lire un tutoriel sur l’utilisation de l’instance de calcul et du cluster de calcul, consultez [Exécuter un script Python](tutorial-1st-experiment-hello-world.md).

## <a name="stop-compute-instance-and-jump-box"></a>Arrêter l’instance de calcul et le serveur de rebond

> [!WARNING]
> Tant qu’ils sont en cours d’exécution (démarrés), l’instance de calcul et le serveur de rebond continuent de facturer votre abonnement. Pour éviter tout coût excessif, __arrêtez__-les lorsqu’ils ne sont pas utilisés.

Le cluster de calcul effectue une mise à l’échelle dynamique entre le nombre de nœuds minimum et maximum défini lors de sa création. Si vous avez accepté les valeurs par défaut, la valeur minimale est 0, ce qui a pour effet de désactiver le cluster en cas de non-utilisation.
### <a name="stop-the-compute-instance"></a>Arrêter l’instance de calcul

Dans le studio, sélectionnez __Calcul__, __Clusters de calcul__, puis sélectionnez l’instance de calcul. Enfin, sélectionnez __Arrêter__ en haut de la page.

:::image type="content" source="./media/tutorial-create-secure-workspace/compute-instance-stop.png" alt-text="Capture d’écran du bouton Arrêter de l’instance de calcul":::
### <a name="stop-the-jump-box"></a>Arrêter le serveur de rebond

Une fois qu’elle a été créée, sélectionnez la machine virtuelle dans le portail Azure, puis utilisez le bouton __Arrêter__. Lorsque vous êtes prêt à l’utiliser à nouveau, utilisez le bouton __Démarrer__ pour la démarrer.

:::image type="content" source="./media/tutorial-create-secure-workspace/virtual-machine-stop.png" alt-text="Capture d’écran du bouton Arrêter de la machine virtuelle":::

Vous pouvez également configurer le serveur de rebond pour qu’il s’arrête automatiquement à une heure précise. Pour ce faire, sélectionnez __Arrêt automatique__, __Activer__, définissez une heure, puis sélectionnez __Enregistrer__.

:::image type="content" source="./media/tutorial-create-secure-workspace/virtual-machine-auto-shutdown.png" alt-text="Capture d’écran de l’option d’arrêt automatique":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous prévoyez de continuer à utiliser l’espace de travail sécurisé et les autres ressources, passez cette section.

Pour supprimer toutes les ressources créées dans ce tutoriel, procédez comme suit :

1. Dans le portail Azure, sélectionnez __Groupes de ressources__ tout à gauche.
1. Dans la liste, sélectionnez le groupe de ressources que vous avez créé dans ce tutoriel.
1. Sélectionnez __Supprimer le groupe de ressources__.

    :::image type="content" source="./media/tutorial-create-secure-workspace/delete-resources.png" alt-text="Capture d’écran du bouton Supprimer le groupe de ressources":::

1. Entrez le nom du groupe de ressources, puis sélectionnez __Supprimer__.
## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé un espace de travail sécurisé et que vous pouvez accéder au studio, apprenez à [exécuter un script Python à l’aide d’Azure Machine Learning](tutorial-1st-experiment-hello-world.md).