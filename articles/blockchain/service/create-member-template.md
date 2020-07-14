---
title: Créer un membre Azure Blockchain Service à l’aide d’un modèle Azure Resource Manager
description: Découvrez comment créer un membre Azure Blockchain Service à l’aide d’un modèle Azure Resource Manager.
services: azure-resource-manager
author: PatAltimore
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: patricka
ms.date: 06/04/2020
ms.openlocfilehash: 8eabb6806dee96871648ea419ba36d768d32a2ab
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86078164"
---
# <a name="quickstart-create-an-azure-blockchain-service-member-using-an-arm-template"></a>Démarrage rapide : Créer un membre d’Azure Blockchain Service à l’aide d’un modèle ARM

Dans ce guide de démarrage rapide, vous allez déployer un nouveau membre blockchain et un nouveau consortium dans Azure Blockchain Service à l’aide d’un modèle ARM (Azure Resource Manager). Un membre Azure Blockchain Service est un nœud blockchain dans un réseau blockchain de consortium privé. Lors du provisionnement d’un membre, vous pouvez créer ou rejoindre un réseau de consortium. Vous avez besoin d’au moins un membre pour former un réseau de consortium. Le nombre de membres blockchain nécessaires par participant dépend de votre scénario. Les participants au consortium peuvent avoir un ou plusieurs membres blockchain ou ils peuvent partager des membres avec d’autres participants. Pour plus d’informations sur les consortiums, consultez [Consortium Azure Blockchain Service](consortium.md).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure.

[![Déployer sur Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/201-blockchain-asaservice/).

:::code language="json" source="~/quickstart-templates/201-blockchain-asaservice/azuredeploy.json" range="1-84" highlight="52-80":::

Ressources Azure définies dans le modèle :

* [**Microsoft.Blockchain/blockchainMembers**](/azure/templates/microsoft.blockchain/blockchainmembers)

## <a name="deploy-the-template"></a>Déployer le modèle

1. Sélectionnez le lien suivant pour vous connecter à Azure et ouvrir un modèle.

    [![Déployer sur Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

1. Spécifiez les paramètres du membre Azure Blockchain Service.

    Paramètre | Description
    --------|------------
    Abonnement | Sélectionnez l’abonnement Azure que vous souhaitez utiliser pour votre serveur. Si vous avez plusieurs abonnements, sélectionnez l’abonnement dans lequel la ressource est facturée.
    Resource group | Créez un nom de groupe de ressources ou choisissez un nom de groupe existant dans votre abonnement.
    Emplacement | Choisissez un emplacement où créer le membre. Tous les membres du consortium doivent se trouver au même emplacement. Les emplacements disponibles pour le déploiement sont *westeurope, eastus, southeastasia, westeurope, northeurope, westus2* et *japaneast*.
    Nom du membre | Choisissez un nom unique pour le membre Azure Blockchain Service. Le nom du membre blockchain doit être uniquement composé de lettres minuscules et de chiffres. Le premier caractère doit être une lettre. Le nom doit comprendre entre 2 et 20 caractères.
    Nom du consortium | Entrez un nom unique. Pour plus d’informations sur les consortiums, consultez [Consortium Azure Blockchain Service](consortium.md).
    Mot de passe du membre | Le mot de passe du compte du membre sert à chiffrer la clé privée du compte Ethereum créé pour votre membre. Vous utilisez le compte du membre et le mot de passe du compte du membre pour la gestion du consortium.
    Niveau de référence SKU | Niveau tarifaire pour votre nouveau service. Choisissez le niveau **Standard** ou **De base**. Utilisez le niveau *De base* pour le développement, les tests et les preuves de concept. Utilisez le niveau *Standard* pour les déploiements en production. Vous devez également utiliser le niveau *Standard* si vous utilisez Blockchain Data Manager, ou si vous envoyez un volume élevé de transactions privées. Le passage du niveau tarifaire de base au niveau standard (ou inversement) après la création du membre n'est pas pris en charge.
    Nom de la référence SKU | Configuration des nœuds et coût de votre nouveau service.

1. Sélectionnez **Acheter** pour déployer le modèle.

  Le portail Azure est utilisé ici pour déployer le modèle. Vous pouvez également utiliser Azure PowerShell, Azure CLI ou l’API REST. Pour découvrir d’autres méthodes de déploiement, consultez [Déployer des modèles](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

Vous pouvez utiliser le portail Azure pour afficher les détails du membre Azure Blockchain Service déployé. Dans le portail, accédez au groupe de ressources contenant votre membre Azure Blockchain Service. Sélectionnez le membre blockchain que vous avez créé.

![Détails dans le portail Azure de la vue d’ensemble du membre Azure Blockchain déployé](./media/create-member-template/deployed-member.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Vous pouvez utiliser le membre blockchain que vous avez créé pour le guide de démarrage rapide ou tutoriel suivant. Quand vous n’en aurez plus besoin, vous pourrez supprimer les ressources en supprimant le groupe de ressources que vous avez créé pour le guide de démarrage rapide.

Pour supprimer le groupe de ressources :

1. Dans le portail Azure, accédez à **Groupe de ressources** dans le volet de navigation de gauche et sélectionnez le groupe de ressources que vous souhaitez supprimer.
2. Sélectionnez **Supprimer le groupe de ressources**. Validez la suppression en entrant le nom du groupe de ressources, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé un membre Azure Blockchain Service et un nouveau consortium. Dans le guide de démarrage rapide qui suit, vous allez utiliser Azure Blockchain Development Kit pour Ethereum afin d’effectuer une jonction à un membre Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Utiliser Visual Studio Code pour se connecter à Azure Blockchain Service](connect-vscode.md)