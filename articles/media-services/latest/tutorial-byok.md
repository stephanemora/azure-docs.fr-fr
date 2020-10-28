---
title: Clés CMK ou clés BYOK avec Media Services
description: Ce tutoriel explique comment utiliser les clés gérées par le client avec un compte de stockage Media Services.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: c26da9d888bbcdf72c052fa4bd7fcdf443a2d8f7
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92325859"
---
# <a name="tutorial-use-customer-managed-keys-or-bring-your-own-key-byok-with-media-services"></a>Tutoriel : Utiliser des clés CMK ou des clés BYOK avec Media Services

Avec l’API 2020-05-01, vous pouvez utiliser une clé RSA gérée par le client avec un compte Media Services disposant d’une identité managée affectée par le système.  Ce tutoriel comprend une collection et un environnement Postman permettant d’envoyer des requêtes REST aux services Azure.  Services utilisés :

- Inscription d’application Azure Active Directory pour Postman
- API Microsoft Graph
- Stockage Azure
- Azure Key Vault
- Media Services

Ce tutoriel explique comment utiliser Postman pour effectuer les opérations suivantes :

> [!div class="checklist"]
> * Obtenir les jetons à utiliser avec les services Azure
> * Créer un groupe de ressources et un compte de stockage
> * Créer un compte Media Services avec une identité managée affectée par le système
> * Créer un coffre de clés pour stocker une clé RSA (gérée par le client) à utiliser avec le compte de stockage
> * Mettre à jour le compte Media Services pour qu’il utilise la clé RSA avec le compte de stockage
> * Utiliser des variables dans Postman

Si vous n’avez pas d’abonnement Azure, [créez un compte d’essai gratuit](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prérequis

- Inscrire un principal de service avec les autorisations nécessaires
- Installer [Postman](https://www.postman.com)
- Télécharger la collection Postman de ce tutoriel : [Azure Samples: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)

### <a name="register-a-service-principal-with-the-needed-permissions"></a>Inscrire un principal de service avec les autorisations nécessaires

[Créez un principal du service](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).  Pour obtenir le secret du principal de service, consultez [Option 2](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#authentication-two-options).  Prenez note du secret, car une fois que vous aurez quitté la page du secret dans le portail, vous ne pourrez plus y accéder.

Les tâches de ce tutoriel nécessitent que le principal de service dispose des autorisations suivantes :

![Autorisations nécessaires pour le principal de service](./media/tutorial-byok/service-principal-permissions-1.png)

### <a name="install-postman"></a>Installer Postman

Si vous n’avez pas déjà installé Postman afin de l’utiliser avec Azure, vous pouvez l’obtenir sur [postman.com](https://www.postman.com/).

### <a name="download-and-import-the-collection"></a>Télécharger et importer la collection

Télécharger la collection Postman de ce tutoriel : [Azure Samples: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)

## <a name="installation-of-collection-and-environment"></a>Installation de la collection et de l’environnement

1. Exécutez Postman.
1. Sélectionnez **Importer** .
1. Sélectionnez **Charger des fichiers** .
1. Accédez à l’emplacement où vous avez enregistré les fichiers de collection et d’environnement.
1. Sélectionnez le fichier d’environnement et le fichier de collection.
1. Sélectionnez **Ouvrir** .  (vous verrez un avertissement indiquant que les fichiers ne seront pas importés comme une API, mais sous forme de collections.  C’est normal.  C’est ce que nous voulons).
1. Cette collection s’affiche désormais sous Collections comme une clé BYOK.
1. Les variables d’environnement s’affichent désormais sous Environnements.

### <a name="understand-the-rest-api-requests-in-the-collection"></a>Comprendre les requêtes d’API REST de la collection

La collection fournit les requêtes d’API REST suivantes. Les requêtes doivent être envoyées dans la séquence fournie, car la plupart ont des scripts de test qui créent dynamiquement des variables globales pour la requête qui suit dans la séquence. Il n’est pas nécessaire de créer manuellement les variables globales.

Dans Postman, vous verrez que ces variables sont entre crochets `{{ }}`.  Par exemple, `{{bearerToken}}`.

1. Obtenir le jeton AAD : le test définit la variable globale *bearerToken*
2. Obtenir le jeton Graph : le test définit la variable globale *graphToken*
3. Obtenir les détails du principal du service : le test définit la variable globale *servicePrincipalObjectId*
4. Créer un compte de stockage : le test définit la variable globale *storageAccountId*
5. Créer un compte Media Services avec une identité managée affectée par le système : le test définit la variable globale *principalId*
6. Créer un coffre de clés en accordant l’accès au principal de service : le test définit la variable globale *keyVaultId*
7. Obtenir un jeton Key Vault : le test définit la variable globale *keyVaultToken*
8. Créer une clé RSA dans le coffre de clés : le test définit la variable globale *keyId*
9. Mettez à jour le compte Media Services pour utiliser la clé avec le compte de stockage. Il n’existe pas de script de test pour cette requête.

## <a name="define-environment-variables"></a>Définir des variables d’environnement

1. Basculez vers l’environnement que vous avez téléchargé en le sélectionnant dans la liste des environnements.
1. Établissez vos variables d’environnement dans Postman. Elles sont également utilisées comme des variables placées entre crochets `{{ }}`.  Par exemple, `{{tenantId}}`.

    * *tenantId* = votre ID de locataire
    * *servicePrincipalId* = ID du principal de service que vous établissez à l’aide de la méthode de votre choix : portail, CLI, etc.
    * *servicePrincipalSecret* = secret créé pour le principal de service
    * *subscription* = votre ID d’abonnement
    * *storageName* = nom que vous souhaitez attribuer à votre stockage
    * *accountName* = nom du compte Media Services que vous souhaitez utiliser
    * *keyVaultName* = nom que vous souhaitez utiliser pour le coffre de clés
    * *resourceLocation* = centralus (ou tout autre endroit auquel placer vos ressources.  Cette collection a été testée uniquement avec centralus).
    * *resourceGroup* = nom du groupe de ressources

    Les variables suivantes sont des variables standard pour l’utilisation des ressources Azure. Vous n’avez donc pas besoin de les modifier.

    * *armResource* = `https://management.core.windows.net`
    * *graphResource* = `https://graph.windows.net/`
    * *keyVaultResource* = `https://vault.azure.net`
    * *armEndpoint* = `management.azure.com`
    * *graphEndpoint* = `graph.windows.net`
    * *aadEndpoint* = `login.microsoftonline.com`
    * *keyVaultDomainSuffix* = `vault.azure.net`

## <a name="send-the-requests"></a>Envoyer les requêtes

Une fois que vos variables d’environnement ont été définies, vous pouvez exécuter les requêtes une par une dans la séquence ci-dessus, ou utiliser l’exécuteur Postman pour exécuter la collection.

## <a name="change-the-key"></a>Modifier la clé

Media Services détecte automatiquement lorsqu’une clé a été changée.  Pour tester cela, créez une autre version de la même clé. Media Services doit détecter cette clé en moins de 15 minutes.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne comptez pas utiliser les ressources que vous avez créées et **ne souhaitez plus qu’elles vous soient facturées** , vous pouvez les supprimer.

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour savoir comment…
> [!div class="nextstepaction"]
> [Encoder un fichier distant basé sur une URL et diffuser la vidéo en continu avec REST](stream-files-tutorial-with-rest.md)
