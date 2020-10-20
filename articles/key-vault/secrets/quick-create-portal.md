---
title: Démarrage rapide Azure - Définir et récupérer un secret depuis Key Vault à l’aide du portail Azure | Microsoft Docs
description: Ce guide de démarrage rapide vous montre comment définir et récupérer un secret depuis Azure Key Vault à l’aide du portail Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 126df6e7f4d227c20c2173a1e2d4c0d7361b043f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962443"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>Démarrage rapide : Définir et récupérer un secret depuis Azure Key Vault à l’aide du portail Azure

Azure Key Vault est un service cloud qui fonctionne comme un magasin sécurisé contenant des secrets. Vous pouvez stocker des clés, des mots de passe, des certificats et d’autres secrets en toute sécurité. Vous pouvez créer et gérer des coffres de clés Azure grâce au portail Azure. Dans ce démarrage rapide, vous créez un coffre de clés, puis l’utilisez pour stocker un secret. Pour plus d’informations sur Key Vault, consultez la [présentation](../general/overview.md).

Pour plus d’informations sur les secrets, consultez (about-secrets.md).

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-a-vault"></a>Création d'un coffre

1. Dans le menu du Portail Azure ou dans la page **Accueil**, sélectionnez **Créer une ressource**.
2. Dans la zone de recherche, entrez **Key Vault**.
3. Dans la liste des résultats, choisissez **Key Vault**.
4. Dans la section Key Vault, choisissez **Créer**.
5. Dans la section **Créer un coffre de clés**, renseignez les informations suivantes :
    - **Name** : un nom unique est obligatoire. Pour ce démarrage rapide, nous utilisons **Contoso-vault2**. 
    - **Abonnement**: Choisissez un abonnement.
    - Sous **Groupe de ressources**, choisissez **Créer** et entrez le nom du groupe de ressources.
    - Dans le menu déroulant **Emplacement**, choisissez un emplacement.
    - Conservez les valeurs par défaut des autres options.
6. Après avoir renseigné les informations ci-dessus, sélectionnez **Créer**.

Notez les deux propriétés ci-dessous :

* **Nom du coffre** : dans l’exemple, il s’agit de **Contoso-Vault2**. Vous allez utiliser ce nom pour les autres étapes.
* **URI du coffre** : dans l’exemple, il s’agit de https://contoso-vault2.vault.azure.net/. Les applications qui utilisent votre coffre via son API REST doivent utiliser cet URI.

Vous pouvez également créer un coffre de clés avec Azure CLI et PowerShell : [Créer un coffre de clés à l’aide de PowerShell](../general/quick-create-powershell.md)
[Créer un coffre de clés à l’aide d’Azure CLI](../general/quick-create-cli.md)

À ce stade, votre compte Azure est le seul autorisé à effectuer des opérations sur ce nouveau coffre.

![Sortie après la création du coffre de clés](../media/quick-create-portal/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Ajouter un secret au coffre de clés

Pour ajouter un secret au coffre, vous devez effectuer deux autres opérations. Dans ce cas, nous ajoutons un mot de passe qu’une application est susceptible d’utiliser. Le mot de passe est appelé **ExamplePassword** et nous y stockons la valeur **hVFkk965BuUv**.

1. Dans les pages des propriétés Key Vault, sélectionnez **Secrets**.
2. Cliquez sur **Generate/Import (Générer/Importer)** .
3. Dans l’écran **Create a secret (Créer un secret)** , choisissez les valeurs suivantes :
    - **Options de chargement** : Manuel.
    - **Name** : ExamplePassword.
    - **Valeur** : hVFkk965BuUv
    - Conservez les valeurs par défaut des autres options. Cliquez sur **Créer**.

Lorsque vous recevez le message confirmant la création du secret, cliquez dessus dans la liste. 

## <a name="retrieve-a-secret-from-key-vault"></a>Récupérer un secret à partir de Key Vault

Si vous cliquez sur la version actuelle, vous voyez la valeur que vous avez spécifiée à l’étape précédente.

![Propriétés de secret](../media/quick-create-portal/current-version-hidden.png)

Vous pouvez afficher la valeur masquée en cliquant sur le bouton « Afficher la valeur secrète » dans le volet de droit. 

![Valeur secrète affichée](../media/quick-create-portal/current-version-shown.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

D’autres démarrages rapides et didacticiels sur les coffres de clés reposent sur ce démarrage rapide. Si vous prévoyez d’utiliser d’autres démarrages rapides et didacticiels, il peut être utile de conserver ces ressources.
Si vous n’en avez plus besoin, supprimez le groupe de ressources. Ce faisant, vous supprimez le coffre de clés et les ressources associées. Pour supprimer le groupe de ressources à l’aide du portail :

1. Entrez le nom de votre groupe de ressources dans la zone Recherche en haut du portail. Lorsque vous voyez le groupe de ressources utilisé dans ce démarrage rapide dans les résultats de recherche, sélectionnez-le.
2. Sélectionnez **Supprimer le groupe de ressources**.
3. Dans le champ **TYPE THE RESOURCE GROUP NAME: (TAPER LE NOM DU GROUPE DE RESSOURCES :)** , tapez le nom du groupe de ressources et sélectionnez **Supprimer**.


## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un coffre de clés et vous y avez stocké un secret. Pour en savoir plus sur Key Vault et sur la manière de l’intégrer à vos applications, consultez les articles ci-dessous.

- Lire la [vue d’ensemble Azure Key Vault](../general/overview.md)
- Lire [Sécuriser l’accès à un coffre de clés](../general/secure-your-key-vault.md)
- Consulter [Utiliser Key Vault avec une application web App Service](../general/tutorial-net-create-vault-azure-web-app.md)
- Consulter [Utiliser Key Vault avec une application déployée sur une machine virtuelle](../general/tutorial-net-virtual-machine.md)
- Consulter le [Guide du développeur Azure Key Vault](../general/developers-guide.md)
- Passer en revue les [bonnes pratiques relatives à Azure Key Vault](../general/best-practices.md)
