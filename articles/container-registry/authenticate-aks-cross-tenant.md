---
title: S’authentifier à partir d’un cluster AKS auprès d’un registre de conteneurs Azure dans un locataire AD différent
description: Configurer le principal de service du cluster AKS avec les autorisations d’accès à votre registre de conteneurs Azure dans un locataire AD différent
ms.topic: article
author: dlepow
ms.author: danlep
ms.date: 05/21/2021
ms.openlocfilehash: 154ef93ca89c6d117d23a80986fe30ef2e14a426
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111442058"
---
# <a name="pull-images-from-a-container-registry-to-an-aks-cluster-in-a-different-azure-ad-tenant"></a>Extraire des images d’un registre de conteneurs vers un cluster AKS dans un locataire Azure AD différent

Dans certains cas, vous pouvez avoir votre cluster AKS Azure dans un locataire Azure Active Directory (Azure AD) et votre registre de conteneurs Azure dans un autre locataire. Cet article décrit les étapes à suivre pour activer l’authentification interlocataire à l’aide des informations d’identification du principal de service d’AKS à extraire du registre de conteneurs.

## <a name="scenario-overview"></a>Présentation du scénario
Hypothèses pour cet exemple :

* Le cluster AKS est dans le **Locataire A** et le registre de conteneurs Azure est dans le **Locataire B**. 
* Le cluster AKS est configuré avec l’authentification du principal de service dans le **Locataire A**. En savoir plus sur la création et l’utilisation d’un [principal de service pour votre cluster AKS](../aks/kubernetes-service-principal.md).

Vous avez au minimum besoin du rôle Contributeur dans l’abonnement du cluster AKS et du rôle Propriétaire dans l’abonnement du registre de conteneurs.

Procédez comme suit pour :

* créer une nouvelle application multilocataire (principal de service) dans le **Locataire A** ; 
* approvisionner l’application dans le **Locataire B** ;
* configurer le principal de service pour extraire à partir du registre dans le **Locataire B** ;
* mettre à jour le cluster AKS dans le **Locataire A** pour une authentification à l’aide du nouveau principal de service.


## <a name="step-by-step-instructions"></a>Instructions pas à pas

### <a name="step-1-create-multitenant-azure-ad-application"></a>Étape 1 : Créer une application multilocataire Azure AD

1. Connectez-vous au [Portail Azure](https://portal.azure.com/) dans le **Locataire A**.
1. Recherchez et sélectionnez **Azure Active Directory**.
1. Sous **Gérer**, sélectionnez **Inscriptions d’applications > + Nouvelle inscription**.
1. Dans **Types de compte pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel**.
1. Dans **URI de redirection**, entrez *https://www.microsoft.com* .
1. Sélectionnez **Inscription**.
1. Sur la page **Vue d’ensemble**, prenez note de l’**ID d’application (client)** . Il sera utilisé aux étapes 2 et 4.

    :::image type="content" source="media/authenticate-kubernetes-cross-tenant/service-principal-overview.png" alt-text="ID d’application du principal de service":::
1. Dans **Certificats et secrets**, sous **Clés secrètes client**, sélectionnez **+ Nouvelle clé secrète client**.
1. Entrez une **Description**, telle que *Mot de passe*, puis sélectionnez **Ajouter**.
1. Dans **Clés secrètes client**, prenez note de la valeur de la clé secrète client. Vous allez l’utiliser pour mettre à jour le principal de service du cluster AKS à l’étape 4.

    :::image type="content" source="media/authenticate-kubernetes-cross-tenant/configure-client-secret.png" alt-text="Configurer une clé secrète client":::
### <a name="step-2-provision-the-service-principal-in-the-acr-tenant"></a>Étape 2 : Approvisionner le principal de service dans le locataire ACR

1. Ouvrez le lien suivant en utilisant un compte d’administrateur dans le **Locataire B**. Lorsqu’il est indiqué, insérez l’**ID du locataire B** et l’**ID d’application** (ID client) de l’application multilocataire.

    ```console
    https://login.microsoftonline.com/<Tenant B ID>/oauth2/authorize?client_id=<Multitenant application ID>&response_type=code&redirect_uri=<redirect url>
    ```
1. Sélectionnez **Consentement pour le compte de votre organisation**, puis **Accepter**. 
    
    :::image type="content" source="media/authenticate-kubernetes-cross-tenant/multitenant-app-consent.png" alt-text="Octroyer au locataire l’accès à l’application":::
 

### <a name="step-3-grant-service-principal-permission-to-pull-from-registry"></a>Étape 3 : Accorder au principal de service l’autorisation d’extraire du registre

Dans le **Locataire B**, attribuez le rôle AcrPull au principal de service, étendu au registre de conteneurs cible. Vous pouvez utiliser le [Portail Azure](../role-based-access-control/role-assignments-portal.md) ou d’autres outils pour attribuer le rôle. Pour obtenir un exemple de la procédure utilisant Azure CLI, consultez [Authentification Azure Container Registry avec des principaux de service](container-registry-auth-service-principal.md#use-an-existing-service-principal).

:::image type="content" source="media/authenticate-kubernetes-cross-tenant/multitenant-app-acr-pull.png" alt-text="Attribuer le rôle acrpull à une application multilocataire":::

### <a name="step-4-update-aks-with-the-azure-ad-application-secret"></a>Étape 4 : Mettre à jour AKS avec le secret de l’application Azure AD

Utilisez l’ID d’application (client) multilocataire et la clé secrète client collectés à l’étape 1 pour [mettre à jour les informations d’identification du principal de service d’AKS](../aks/update-credentials.md#update-aks-cluster-with-new-service-principal-credentials).

La mise à jour du principal de service peut prendre plusieurs minutes.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur l’[Authentification Azure Container Registry avec des principaux de service](container-registry-auth-service-principal.md)
* Découvrez plus d’informations sur les secrets d’extraction d’image dans la [documentation Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)
- En savoir plus sur les [Objets application et principal du service dans Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md)


