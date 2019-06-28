---
title: Configuration d’un accès Azure Active Directory
description: Procédure de configuration d’Azure Blockchain Service avec un accès Azure Active Directory
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: seal
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: 616e342f1d52179c40c225c5dafc9de13ce85e06
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028214"
---
# <a name="how-to-configure-azure-active-directory-access"></a>Configuration d’un accès Azure Active Directory

Dans cet article, vous allez apprendre à octroyer l’accès et vous connecter aux nœuds Azure Blockchain Service à l’aide d’un compte d’utilisateur, d’un groupe ou d’un ID d’application Azure Active Directory (Azure AD).

Azure AD assure la gestion des identités basée sur le cloud, et permet aux clients d’utiliser une seule et même identité dans l’ensemble d’une entreprise et d’accéder aux applications sur Azure. Azure Blockchain Service s’intègre à Azure AD et offre ainsi différents avantages, tels que la fédération d’ID, l’authentification unique et l’authentification multifacteur.

## <a name="prerequisites"></a>Prérequis

* [Créer un membre de blockchain à l’aide du Portail Azure](create-member.md)

## <a name="grant-access"></a>Accorder l'accès

Vous pouvez octroyer l’accès à la fois au niveau membre et au niveau nœud. L’octroi de droits d’accès au niveau membre accorde alors un accès à tous les nœuds situés sous ce membre.

### <a name="grant-member-level-access"></a>Octroyer l’accès au niveau membre

Pour octroyer une autorisation d’accès au niveau membre :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Accédez à **Contrôle d’accès (IAM) > Ajouter > Ajouter une attribution de rôle**.
1. Sélectionnez le rôle **Accès au nœud Membre Blockchain (préversion)** et ajoutez l’objet d’ID Azure AD auquel vous souhaitez octroyer l’accès. L’objet d’ID AD Azure peut correspondre à l’un des éléments suivants :

    | Objet Azure AD | Exemples |
    |-----------------|---------|
    | Utilisateur Azure AD   | `frank@contoso.onmicrosoft.com` |
    | Groupe Azure AD  | `sales@contoso.onmicrosoft.com` |
    | ID de l'application  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Ajouter une attribution de rôle](./media/configure-aad/add-role-assignment.png)

1. Sélectionnez **Enregistrer**.

### <a name="grant-node-level-access"></a>Octroyer l’accès au niveau nœud

1. Vous pouvez octroyer l’accès au niveau nœud en accédant à la sécurité des nœuds et en cliquant sur le nom du nœud auquel vous voulez accorder l’accès.
1. Sélectionnez le rôle Accès au nœud Membre Blockchain (préversion) et ajoutez l’objet d’ID Azure AD auquel vous souhaitez octroyer l’accès. 

## <a name="connect-using-azure-blockchain-connector"></a>Se connecter à l’aide d’Azure Blockchain Connector

Téléchargez ou clonez le connecteur [Azure Blockchain Connector à partir de GitHub](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

Suivez la section de démarrage rapide du fichier **Lisez-moi** pour générer le connecteur à partir du code source.

### <a name="connect-using-an-azure-ad-user-account"></a>Se connecter à l’aide d’un compte d’utilisateur Azure AD

1. Exécutez la commande ci-après pour vous authentifier à l’aide d’un compte d’utilisateur Azure AD. Remplacez \<myAADDirectory\> par un domaine Azure AD. Par exemple : `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD vous invite à entrer des informations d’identification.
1. Connectez-vous avec votre nom d’utilisateur et votre mot de passe.
1. Une fois l’authentification réussie, votre proxy local se connecte à votre nœud de blockchain. Vous pouvez alors attacher votre client Geth au point de terminaison local.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Se connecter à l’aide d’un ID d’application

De nombreuses applications s’authentifient auprès d’Azure AD à l’aide d’un ID d’application plutôt qu’avec un compte d’utilisateur Azure AD.

Pour vous connecter à votre nœud à l’aide d’un ID d’application, remplacez **aadauthcode** par **aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Paramètre | Description |
|-----------|-------------|
| tenant-id | Domaine Azure AD ; par exemple, `yourdomain.onmicrosoft.com`
| client-id | ID client de l’application inscrite dans Azure AD
| client-secret | Clé secrète client de l’application inscrite dans Azure AD

Pour plus d’informations sur la procédure d’inscription d’une application dans Azure AD, consultez l’article [ Utiliser le portail pour créer une application Azure AD et un principal de service ayant accès aux ressources](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Se connecter à un appareil mobile ou à un navigateur de texte

Dans le cas d’un appareil mobile ou d’un navigateur de texte sur lesquels la fenêtre contextuelle d’authentification Azure AD ne peut pas s’afficher, Azure AD génère un code secret à usage unique. Vous pouvez copier ce code secret, puis poursuivre l’authentification Azure AD dans un autre environnement.

Pour générer le code secret, remplacez **aadauthcode** par **aaddevice**. Remplacez \<myAADDirectory\> par un domaine Azure AD. Par exemple : `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la sécurité des données dans Azure Blockchain Service, consultez l’article :

> [!div class="nextstepaction"]
> [Sécurité d’Azure Blockchain Service](data-security.md)