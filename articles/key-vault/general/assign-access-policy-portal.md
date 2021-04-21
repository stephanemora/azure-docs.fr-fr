---
title: Attribuer une stratégie d’accès Azure Key Vault (portail)
description: Guide pratique pour utiliser le portail Azure afin d’attribuer une stratégie d’accès Key Vault à un principal de sécurité ou à une identité d’application.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 443b269e7155fc206ee50e7907a7acded2c22f53
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751487"
---
# <a name="assign-a-key-vault-access-policy-using-the-azure-portal"></a>Attribuer une stratégie d’accès Key Vault à l’aide du portail Azure

Une stratégie d’accès Key Vault détermine si un principal de sécurité donné, à savoir un utilisateur, une application ou un groupe d’utilisateurs, peut effectuer différentes opérations sur des [secrets](../secrets/index.yml), [clés](../keys/index.yml) et [certificats](../certificates/index.yml) de Key Vault. Vous pouvez attribuer des stratégies d’accès à l’aide du portail Azure (cet article), d’[Azure CLI](assign-access-policy-cli.md) ou d’[Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Pour plus d’informations sur la création de groupes dans Azure Active Directory via le portail Azure, consultez [Créer un groupe de base et ajouter des membres](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="assign-an-access-policy"></a>Attribuer une stratégie d’accès

1.  Dans le [portail Azure](https://portal.azure.com), accédez à la ressource Key Vault. 

1.  Sous **Paramètres**, sélectionnez **Stratégies d’accès**, puis sélectionnez **Ajouter une stratégie d’accès** :

    ![Sélectionner des stratégies d’accès, sélection d’Ajouter une attribution de rôle](../media/authentication/assign-policy-portal-01.png)

1.  Sélectionnez les autorisations souhaitées sous **Autorisations de certificat**, **Autorisations de clé** et **Autorisations de secret**. Vous pouvez également sélectionner un modèle contenant des combinaisons d’autorisations courantes :

    ![Spécification des autorisations de stratégie d’accès](../media/authentication/assign-policy-portal-02.png)

1. Sous **Sélectionner le principal**, choisissez le lien **Aucune sélectionnée** pour ouvrir le volet de sélection **Principal**. Entrez le nom de l’utilisateur, de l’application ou du principal de service dans le champ de recherche, sélectionnez le résultat approprié, puis choisissez **Sélectionner**.

    ![Sélection du principal de sécurité pour la stratégie d’accès](../media/authentication/assign-policy-portal-03.png)

    Si vous utilisez une identité managée pour l’application, recherchez et sélectionnez le nom de l’application (pour plus d’informations sur l’identité managée et les principaux de service, consultez [Authentification du coffre de clés – Identité d’application et principaux de service](authentication.md#app-identity-and-security-principals)).
 
1.  De retour dans le volet **Ajouter une stratégie d’accès**, sélectionnez **Ajouter** pour enregistrer la stratégie d’accès.

    ![Ajout de la stratégie d’accès avec le principal de sécurité attribué](../media/authentication/assign-policy-portal-04.png)

1. De retour sur la page **Stratégies d’accès**, vérifiez que votre stratégie d’accès figure sous **Stratégies d’accès actuelles**, puis sélectionnez **Enregistrer**. Les stratégies d’accès ne sont pas appliquées tant que vous ne les enregistrez pas.

    ![Enregistrement des changements de stratégie d’accès](../media/authentication/assign-policy-portal-05.png)


## <a name="next-steps"></a>Étapes suivantes

- [Sécurité d’Azure Key Vault : Gestion des identités et des accès](security-overview.md#identity-management)
- [Sécuriser votre coffre de clés](security-overview.md)
- [Guide du développeur Azure Key Vault](developers-guide.md)