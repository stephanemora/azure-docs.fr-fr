---
title: Activer le studio Azure Machine Learning dans un réseau virtuel
titleSuffix: Azure Machine Learning
description: Le studio Azure Machine Learning permet d’accéder aux données stockées au sein d’un réseau virtuel.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/21/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: aca7b7e8590c9c8eb3db987c5d1527d9f135bf3f
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392994"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Utiliser le studio Azure Machine Learning dans un réseau virtuel Azure

Dans cet article, vous allez apprendre à utiliser le studio Azure Machine Learning dans un réseau virtuel. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> - Accédez au studio à partir d’une ressource à l’intérieur d’un réseau virtuel.
> - Configurez des points de terminaison privés pour les comptes de stockage.
> - Accordez au studio l’accès aux données stockées au sein d’un réseau virtuel.
> - Découvrez l'impact du studio sur la sécurité du stockage.

Cet article est le cinquième d’une série de cinq qui vous guide à travers le processus de sécurisation d’un workflow Azure Machine Learning. Nous vous recommandons vivement de parcourir tout d’abord la [Première partie : Présentation du réseau virtuel](how-to-network-security-overview.md) pour en savoir plus sur l’architecture globale. 

Consultez les autres articles de cette série :

[1. Présentation du réseau virtuel](how-to-network-security-overview.md) > [2. Sécurisation de l’espace de travail](how-to-secure-workspace-vnet.md) > [3. Sécurisation de l’environnement d’entraînement](how-to-secure-training-vnet.md) > [4. Sécurisation de l’environnement d’inférence](how-to-secure-inferencing-vnet.md) > **5. Activation de la fonctionnalité de studio**


> [!IMPORTANT]
> Si votre espace de travail se trouve dans un __cloud souverain__, comme Azure Government ou Azure China 21Vianet, les notebooks intégrés _ne prennent pas_ en charge l’utilisation du stockage qui se trouve dans un réseau virtuel. Cela étant, vous pouvez utiliser des notebooks Jupyter à partir d’une instance de calcul. Pour plus d’informations, consultez la section [Accéder aux données d’un notebook d’instance de capacité de calcul](how-to-secure-training-vnet.md#access-data-in-a-compute-instance-notebook).


## <a name="prerequisites"></a>Prérequis

+ Lisez [Vue d’ensemble de la sécurité réseau](how-to-network-security-overview.md) pour comprendre les scénarios courants des réseaux virtuels et l’architecture globale des réseaux virtuels.

+ Un réseau virtuel et un sous-réseau préexistants à utiliser.

+ Un [espace de travail Azure Machine Learning existant avec Liaison privée activée](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint).

+ Un [compte de stockage Azure existant a ajouté votre réseau virtuel](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints).

## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>Accéder au studio à partir d’une ressource au sein d’un réseau virtuel

Si vous accédez à Studio à partir d’une ressource au sein d’un réseau virtuel (par exemple une instance de calcul ou une machine virtuelle), vous devez autoriser le trafic sortant du réseau virtuel vers Studio. 

Par exemple, si vous utilisez des groupes de sécurité réseau pour limiter le trafic sortant, ajoutez une règle à une destination d’__étiquette de service__ __AzureFrontDoor.Frontend__.

## <a name="access-data-using-the-studio"></a>Accéder aux données à l’aide du studio

Après avoir ajouté un compte de stockage Azure à votre réseau virtuel avec un [point de terminaison de service](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints) ou un [point de terminaison privé](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints), vous devez configurer votre compte de stockage afin d'utiliser une [identité managée](../active-directory/managed-identities-azure-resources/overview.md) pour autoriser le studio à accéder à vos données.

Si vous n’activez pas l’identité managée, cette erreur s’affichera : `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.`. En outre, les opérations suivantes seront désactivées :

* Aperçu des données dans Studio
* Visualisation des données dans le concepteur
* Envoi d’une expérience AutoML
* Démarrage d’un projet d’étiquetage

Studio prend en charge la lecture de données à partir des types de magasins de données suivants sur un réseau virtuel :

* Objets blob Azure
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL Database

### <a name="grant-workspace-managed-identity-__reader__-access-to-storage-private-link"></a>Accorder à l’identité managée de l’espace de travail un accès __Lecteur__ à la liaison privée de stockage

Cette étape est requise uniquement si vous avez ajouté le compte de stockage Azure à votre réseau virtuel avec un [point de terminaison privé](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints). Pour plus d’informations, voir le rôle intégré [Lecteur](../role-based-access-control/built-in-roles.md#reader).

### <a name="configure-datastores-to-use-workspace-managed-identity"></a>Configurer des magasins de données pour utiliser l’identité managée de l’espace de travail

Azure Machine Learning utilise des [magasins de données](concept-data.md#datastores) pour se connecter aux comptes de stockage. Procédez comme suit pour configurer vos magasins de données afin d’utiliser l’identité managée. 

1. Dans Studio, sélectionnez __Magasins de données__.

1. Pour créer un magasin de données, sélectionnez __+ Nouveau magasin de données__.

    Pour mettre à jour un magasin de données existant, sélectionnez-le et sélectionnez __Mettre à jour les informations d’identification__.

1. Dans les paramètres du magasin de données, sélectionnez __Oui__ pour __Autoriser le service Azure Machine Learning à accéder au stockage en utilisant l’identité managée de l’espace de travail__.


Ces étapes ajoutent l’identité managée de l’espace de travail en tant que __Lecteur__ au service de stockage à l’aide du contrôle d’accès en fonction du rôle Azure (Azure RBAC). L’accès __Lecteur__ permet à l’espace de travail de récupérer les paramètres du pare-feu et de s’assurer que les données ne quittent pas le réseau virtuel.

> [!NOTE]
> La prise en compte de ces modifications peut prendre jusqu’à 10 minutes.

## <a name="technical-notes-for-managed-identity"></a>Notes techniques pour l’identité managée

L’utilisation de l’identité managée pour accéder aux services de stockage a un impact sur certaines considérations en matière de sécurité. Cette section décrit les modifications pour chaque type de compte de stockage.

> [!IMPORTANT]
> Ces considérations sont propres au __type de compte de stockage__ auquel vous accédez.

### <a name="azure-blob-storage"></a>Stockage Blob Azure

Pour __Stockage Blob Azure__, l’identité managée de l’espace de travail est également ajoutée en tant que [Lecteur des données Blob](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) afin qu’elle puisse lire les données à partir du stockage d’objets Blob.

### <a name="azure-data-lake-storage-gen2-access-control"></a>Contrôle d’accès Azure Data Lake Storage Gen2

Vous pouvez utiliser à la fois des listes de contrôle d’accès (ACL, access-control list) de style POSIX et le contrôle Azure RBAC pour contrôler l’accès aux données au sein d’un réseau virtuel.

Pour utiliser Azure RBAC, ajoutez l’identité managée de l’espace de travail au rôle [Lecteur de données Blob](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). Pour plus d'informations, consultez [Contrôle d'accès en fonction du rôle Azure](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control).

Pour utiliser des listes de contrôle d’accès, vous pouvez accorder l’accès à l’identité managée de l’espace de travail comme tout autre principal de sécurité. Pour plus d’informations, consultez [Listes de contrôle d’accès sur les fichiers et répertoires](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="azure-data-lake-storage-gen1-access-control"></a>Contrôle d’accès Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 prend uniquement en charge les listes de contrôle d’accès de type POSIX. Vous pouvez accorder à l’identité managée de l’espace de travail l’accès aux ressources comme n’importe quel autre principal de sécurité. Pour plus d’informations, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

### <a name="azure-sql-database-contained-user"></a>Utilisateur autonome Azure SQL Database

Pour accéder aux données stockées dans Azure SQL Database à l’aide d’une identité managée, vous devez créer un utilisateur autonome SQL mappé à l’identité managée. Pour plus d’informations sur la création d’un utilisateur à partir d’un fournisseur externe, consultez [Créer des utilisateurs à relation contenant-contenu mappés à des identités Azure AD](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

Après avoir créé un utilisateur autonome SQL, vous devez lui accorder des autorisations à l’aide de la [commande T-SQL GRANT](/sql/t-sql/statements/grant-object-permissions-transact-sql).

### <a name="azure-machine-learning-designer-default-datastore"></a>Magasin de données par défaut du concepteur Azure Machine Learning

Le concepteur utilise le compte de stockage attaché à votre espace de travail pour stocker la sortie par défaut. Toutefois, vous pouvez le spécifier afin qu’il stocke la sortie vers un magasin de données auquel vous avez accès. Si votre environnement utilise des réseaux virtuels, vous pouvez utiliser ces contrôles pour vous assurer que les données restent accessibles et en sécurité.

Pour définir un nouveau stockage par défaut pour un pipeline :

1. Dans un brouillon de pipeline, sélectionnez l’**icône d’engrenage des Paramètres** située près du titre de votre pipeline.
1. Sélectionnez l’option **Sélectionner le magasin de données par défaut**.
1. Spécifiez un nouveau magasin de données.

Vous pouvez également écraser le magasin de données par défaut sur une base par module. Cela vous donne le contrôle sur l’emplacement de stockage pour chaque module individuel.

1. Sélectionnez le module dont vous souhaitez spécifier la sortie.
1. Développez la section **Paramètres de sortie**.
1. Sélectionnez **Remplacer les paramètres de sortie par défaut**.
1. Sélectionnez **Définir les paramètres de sortie**.
1. Spécifiez un nouveau magasin de données.

## <a name="next-steps"></a>Étapes suivantes

Cet article est une partie facultative d’une série en quatre parties sur les réseaux virtuels. Consultez les autres articles pour découvrir comment sécuriser un réseau virtuel :

* [Partie 1 : Vue d’ensemble des réseaux virtuels](how-to-network-security-overview.md)
* [Partie 2 : Sécuriser les ressources d’espace de travail](how-to-secure-workspace-vnet.md)
* [Partie 3 : Sécuriser l’environnement d’entraînement](how-to-secure-training-vnet.md)
* [Partie 4 : Sécuriser l’environnement d’inférence](how-to-secure-inferencing-vnet.md)