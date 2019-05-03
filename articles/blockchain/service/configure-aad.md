---
title: Comment configurer l’accès d’Azure Active Directory
description: Comment configurer Azure Blockchain Service accès à Azure Active Directory
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
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028214"
---
# <a name="how-to-configure-azure-active-directory-access"></a>Comment configurer l’accès d’Azure Active Directory

Dans cet article, vous allez apprendre à accorder l’accès et de vous connecter aux nœuds Azure Blockchain Service à l’aide d’utilisateur, groupe ou ID d’application Azure Active Directory (Azure AD).

Azure AD fournit la gestion des identités basée sur le cloud et vous permet d’utiliser une identité unique dans un ensemble les applications enterprise et accès dans Azure. Le Service Azure Blockchain est intégré avec Azure AD et offre des avantages tels que le federation ID, seule l’authentification et l’authentification multifacteur.

## <a name="prerequisites"></a>Conditions préalables

* [Créer un membre de blockchain à l’aide du portail Azure](create-member.md)

## <a name="grant-access"></a>Accorder l'accès

Vous pouvez accorder l’accès au niveau du membre et le niveau de nœud. Accorde des droits d’accès au niveau du membre à son tour donne accès à tous les nœuds sous le membre.

### <a name="grant-member-level-access"></a>Accorder l’accès au niveau membre

Pour accorder l’autorisation d’accès au niveau du membre.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Accédez à **contrôle d’accès (IAM) > Ajouter > Ajouter une attribution de rôle**.
1. Sélectionnez le **Blockchain membre nœud accès (préversion)** rôle et ajoutez l’objet ID Azure AD que vous souhaitez accorder l’accès à. Objet d’ID AD Azure peut être :

    | Objet Azure AD | Exemples |
    |-----------------|---------|
    | Utilisateur Azure AD   | `frank@contoso.onmicrosoft.com` |
    | Groupe AD Azure  | `sales@contoso.onmicrosoft.com` |
    | ID de l'application  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Ajouter une attribution de rôle](./media/configure-aad/add-role-assignment.png)

1. Sélectionnez **Enregistrer**.

### <a name="grant-node-level-access"></a>Accorder l’accès au niveau du nœud

1. Vous pouvez accorder l’accès au niveau du nœud en accédant à la sécurité de nœud et cliquez sur le nom de nœud que vous souhaitez accorder l’accès.
1. Sélectionnez le rôle accès à un nœud membre Blockchain (version préliminaire) et ajoutez l’objet ID Azure AD que vous souhaitez accorder l’accès à. 

## <a name="connect-using-azure-blockchain-connector"></a>Se connecter à l’aide du connecteur de Azure Blockchain

Téléchargez ou clonez le [Azure Blockchain connecteur à partir de GitHub](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

Le suivi le démarrage rapide de section dans le **Lisez-moi** pour créer le connecteur à partir du code source.

### <a name="connect-using-an-azure-ad-user-account"></a>Se connecter à l’aide d’un compte d’utilisateur Azure AD

1. Exécutez la commande suivante pour s’authentifier à l’aide d’un compte d’utilisateur Azure AD. Remplacez \<myAADDirectory\> avec un domaine Azure AD. Par exemple : `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD vous invite à entrer des informations d’identification.
1. Connectez-vous avec votre nom d’utilisateur et le mot de passe.
1. Après une authentification réussie, votre proxy local se connecte au nœud de votre blockchain. Vous pouvez désormais joindre votre client Geth avec le point de terminaison local.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Se connecter à l’aide d’un ID d’application

De nombreuses applications s’authentifier auprès d’Azure AD à l’aide d’un ID d’application au lieu d’un compte d’utilisateur Azure AD.

Pour vous connecter à votre nœud à l’aide d’un ID d’application, remplacez **aadauthcode** avec **aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Paramètre | Description |
|-----------|-------------|
| tenant-id | Domaine Azure AD, par exemple, `yourdomain.onmicrosoft.com`
| client-id | ID client de l’application inscrite dans Azure AD
| client-secret | Clé secrète client de l’application inscrite dans Azure AD

Pour plus d’informations sur la façon d’inscrire une application dans Azure AD, consultez [Comment : Utiliser le portail pour créer une application Azure AD et un principal de service ayant accès aux ressources](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Se connecter à un navigateur mobile de l’appareil ou du texte

Pour un appareil mobile ou un navigateur textuel où l’affichage de fenêtre contextuelle d’authentification Azure AD n’est pas possible, Azure AD génère un code secret à usage unique. Vous pouvez copier le code secret et continuer avec l’authentification Azure AD dans un autre environnement.

Pour générer le code secret, remplacez **aadauthcode** avec **aaddevice**. Remplacez \<myAADDirectory\> avec un domaine Azure AD. Par exemple : `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la sécurité des données avec Azure Blockchain Service, consultez :

> [!div class="nextstepaction"]
> [Sécurité de Blockchain Service Azure](data-security.md)