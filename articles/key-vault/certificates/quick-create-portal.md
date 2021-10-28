---
title: Démarrage rapide Azure - Définir et récupérer un certificat dans Key Vault à l’aide du portail Azure | Microsoft Docs
description: Démarrage rapide montrant comment définir et récupérer un certificat dans Azure Key Vault à l’aide du portail Azure
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2020
ms.author: mbaldwin
ms.openlocfilehash: c7559ed3f1a232cf39d2484b007a13b40b635ced
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130258744"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-the-azure-portal"></a>Démarrage rapide : Définir et récupérer un certificat dans Azure Key Vault à l’aide du portail Azure

Azure Key Vault est un service cloud qui fonctionne comme un magasin sécurisé contenant des secrets. Vous pouvez stocker des clés, des mots de passe, des certificats et d’autres secrets en toute sécurité. Vous pouvez créer et gérer des coffres de clés Azure grâce au portail Azure. Dans ce guide de démarrage rapide, vous créez un coffre de clés, puis l’utilisez pour stocker un certificat. Pour plus d’informations sur Key Vault, consultez la [présentation](../general/overview.md).

## <a name="prerequisites"></a>Prérequis

Pour accéder à Azure Key Vault, vous avez besoin d’un abonnement Azure. Si vous n’avez pas d’abonnement, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Tout accès aux secrets s’effectue par le biais d’Azure Key Vault. Pour ce guide de démarrage rapide, créez un coffre de clés à l’aide du [portail Azure](../general/quick-create-portal.md), de l’interface [Azure CLI](../general/quick-create-cli.md) ou d’[Azure PowerShell](../general/quick-create-powershell.md).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="add-a-certificate-to-key-vault"></a>Ajouter un certificat à Key Vault

Pour ajouter un certificat au coffre, vous devez effectuer deux autres opérations. Dans ce cas, nous ajoutons un certificat auto-signé qui peut être utilisé par une application. Le certificat est appelé **ExampleCertificate**.

1. Dans les pages des propriétés Key Vault, sélectionnez **Certificats**.
2. Cliquez sur **Generate/Import (Générer/Importer)** .
3. Dans l’écran **Créer un certificat**, choisissez les valeurs suivantes :
    - **Méthode de création de certificat** : Générer.
    - **Nom du certificat** : ExampleCertificate.
    - **Objet** : CN=ExampleDomain
    - Conservez les valeurs par défaut des autres options. (par défaut, si vous ne spécifiez rien dans la stratégie Avancée, cela sera utilisé comme un certificat d’authentification client)
 4. Cliquez sur **Créer**.

Lorsque vous recevez le message confirmant la création du certificat, vous pouvez cliquer dessus dans la liste. Certaines propriétés s’affichent. Si vous cliquez sur la version actuelle, vous voyez la valeur que vous avez spécifiée à l’étape précédente.

![Propriétés du certificat](../media/certificates/quick-create-portal/current-version-hidden.png)

## <a name="export-certificate-from-key-vault"></a>Exporter un certificat à partir de Key Vault

En cliquant sur le bouton « Télécharger au format CER » ou « Télécharger au format PFX/PEM », vous pouvez télécharger le certificat. 

![Téléchargement du certificat](../media/certificates/quick-create-portal/current-version-shown.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

D’autres démarrages rapides et didacticiels sur les coffres de clés reposent sur ce démarrage rapide. Si vous prévoyez d’utiliser d’autres démarrages rapides et didacticiels, il peut être utile de conserver ces ressources.
Si vous n’en avez plus besoin, supprimez le groupe de ressources. Ce faisant, vous supprimez le coffre de clés et les ressources associées. Pour supprimer le groupe de ressources à l’aide du portail :

1. Entrez le nom de votre groupe de ressources dans la zone Recherche en haut du portail. Lorsque vous voyez le groupe de ressources utilisé dans ce démarrage rapide dans les résultats de recherche, sélectionnez-le.
2. Sélectionnez **Supprimer le groupe de ressources**.
3. Dans le champ **TYPE THE RESOURCE GROUP NAME: (TAPER LE NOM DU GROUPE DE RESSOURCES :)** , tapez le nom du groupe de ressources et sélectionnez **Supprimer**.


## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un coffre de clés et vous y avez stocké un certificat. Pour en savoir plus sur Key Vault et sur la manière de l’intégrer à vos applications, consultez les articles ci-dessous.

- Lire la [vue d’ensemble Azure Key Vault](../general/overview.md)
- Consulter le [Guide du développeur Azure Key Vault](../general/developers-guide.md)
- Passer en revue la [Vue d’ensemble de la sécurité de Key Vault](../general/security-features.md)
