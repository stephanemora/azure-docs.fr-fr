---
title: Guide pratique pour gérer le service de certificats OPC Vault - Azure | Microsoft Docs
description: Gérez les certificats d’autorité de certification racine OPC Vault et les autorisations utilisateur.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 4420e5b0d895f8ea30dbd39fc50dd7480d57d086
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69995992"
---
# <a name="how-to-manage-the-opc-vault-certificate-service"></a>Guide pratique pour gérer le service de certificats OPC Vault

Cet article décrit les tâches d’administration relatives au service de gestion de certificats OPC Vault dans Azure. Il explique également comment renouveler des certificats d’autorité de certification émettrice, renouveler la liste de révocation de certificats (CRL), ainsi qu’accorder et révoquer l’accès utilisateur.

## <a name="create-or-renew-the-root-ca-certificate"></a>Créer ou renouveler le certificat d’autorité de certification racine

La création du certificat d’autorité de certification racine est une étape obligatoire après le déploiement. Sans certificat d’autorité de certification émettrice valide, aucun certificat d’application ne peut être signé et délivré.<br>Reportez-vous au chapitre sur la [durée de vie des certificats](howto-opc-vault-secure-ca.md#certificates) pour gérer vos certificats avec des durées de vie raisonnables et garanties. Un certificat d’autorité de certification émettrice doit être renouvelé à la moitié de sa durée de vie, mais au plus tard avant que la durée de vie configurée d’un certificat d’application nouvellement signé dépasse la durée de vie du certificat émetteur.<br>
> [!IMPORTANT]
> Le rôle « Administrateur » est exigé pour créer ou renouveler le certificat d’autorité de certification émettrice.

1. Ouvrez votre service de certificats sur `https://myResourceGroup-app.azurewebsites.net`, puis connectez-vous.
2. Accédez à la page `Certificate Groups`.
3. Un groupe de certificats `Default` est listé. Cliquez sur `Edit`.
4. Dans `Edit Certificate Group Details`, vous pouvez modifier le Nom du sujet et la Durée de vie de votre autorité de certification et de vos certificats d’application.<br>Le sujet et les durées de vie ne doivent être définis qu’une seule fois avant l’émission du premier certificat d’autorité de certification. Les changements de durée de vie pendant les opérations peuvent entraîner des durées de vie irrégulières pour les certificats émis et les listes de révocation de certificats.
5. Entrez un sujet valide, par exemple `CN=My CA Root, O=MyCompany, OU=MyDepartment`.<br>
   > [!IMPORTANT]
   > Le changement du sujet exige de renouveler le certificat émetteur, sans quoi le service ne parvient pas à signer les certificats d’application. Le sujet de la configuration fait l’objet de contrôles d’intégrité par rapport au sujet du certificat émetteur actif. Si les sujets ne correspondent pas, la signature du certificat est refusée.
6. Cliquez sur le bouton `Save`.
7. Si vous rencontrez une erreur « Interdit » à ce stade, vos informations d’identification d’utilisateur n’ont pas de l’autorisation d’administrateur pour modifier ou créer un certificat racine. Par défaut, l’utilisateur qui a déployé le service dispose des rôles d’administrateur et de signature avec le service. Les autres utilisateurs doivent être ajoutés aux rôles « Approbateur », « Auteur » ou « Administrateur », selon les besoins, dans l’inscription de l’application Azure AD.
8. Cliquez sur le bouton `Details`. L’option `View Certificate Group Details` doit afficher les informations mises à jour.
9. Cliquez sur le bouton `Renew CA Certificate` pour émettre le premier certificat d’autorité de certification émettrice ou renouveler le certificat émetteur. Appuyez sur `Ok` pour continuer.
10. Après quelques secondes, les détails du certificat (`Certificate Details`) s’affichent. Appuyez sur `Issuer` ou `Crl` pour télécharger le certificat d’autorité de certification et la liste de révocation de certificats (CLR) les plus récents en vue de les distribuer à vos applications OPC UA.
11. Le service de gestion de certificats OPC UA est maintenant prêt à émettre des certificats pour les applications OPC UA.

## <a name="renew-the-crl"></a>Renouveler la liste de révocation de certificats (CRL)

Le renouvellement de la liste de révocation de certificats (CRL) est une mise à jour qui doit être distribuée aux applications à intervalles réguliers. Les appareils OPC UA, qui prennent en charge l’extension Point de distribution de liste de révocation de certificats x509, peuvent mettre à jour la liste de révocation de certificats directement à partir du point de terminaison du microservice. Les autres appareils OPC UA peuvent nécessiter des mises à jour manuelles ou, dans le meilleur des cas, peuvent être mis à jour à l’aide d’extensions de transmission des messages par le serveur GDS (*) pour mettre à jour les listes de confiance avec les certificats et les listes de révocation de certificats.

Dans le workflow suivant, toutes les demandes de certificat dans l’état Supprimée sont révoquées dans les listes de révocation de certificats, ce qui correspond au certificat d’autorité de certification émettrice pour lequel elles ont été émises. Le numéro de version de la liste de révocation de certificats est incrémenté de 1. <br>
> [!NOTE]
> Toutes les listes de révocation de certificats émises sont valides jusqu’à l’expiration du certificat d’autorité de certification émettrice, car la spécification OPC UA ne nécessite pas de modèle de distribution déterministe obligatoire pour la liste de révocation de certificats.

> [!IMPORTANT]
> Le rôle « Administrateur » est exigé pour renouveler la liste de révocation de certificats émettrice.

1. Ouvrez votre service de certificats sur `https://myResourceGroup.azurewebsites.net`, puis connectez-vous.
2. Accédez à la page `Certificate Groups`.
3. Cliquez sur le bouton `Details`. L’option `View Certificate Group Details` doit afficher les informations sur le certificat et la liste de révocation de certificats actuels.
4. Cliquez sur le bouton `Update CRL Revocation List(CRL)` pour émettre une liste de révocation de certificats mise à jour pour tous les certificats émetteurs actifs dans le stockage OPC Vault.
5. Après quelques secondes, les détails du certificat (`Certificate Details`) s’affichent. Appuyez sur `Issuer` ou `Crl` pour télécharger le certificat d’autorité de certification et la liste de révocation de certificats (CLR) les plus récents en vue de les distribuer à vos applications OPC UA.

## <a name="manage-user-roles"></a>Gérer les rôles d’utilisateur

Les rôles d’utilisateur pour le microservice OPC Vault sont gérés dans l’application d’entreprise Azure Active Directory.

Pour obtenir une description détaillée des définitions de rôles, reportez-vous à la section [Rôles](howto-opc-vault-secure-ca.md#roles).

Par défaut, un utilisateur authentifié dans le locataire peut se connecter au service en tant que « Lecteur ». Les rôles dotés de privilèges supérieurs nécessitent une gestion manuelle dans le portail Azure ou à l’aide de PowerShell.

### <a name="add-user"></a>Ajouter un utilisateur

1. Ouvrez le portail Azure sur `portal.azure.com`.
2. Accédez à `Azure Active Directory`/`Enterprise applications`.
3. Choisissez l’inscription du microservice OPC Vault, qui est par défaut votre `resourceGroupName-service`.
4. Accédez à `Users and Groups`.
5. Cliquez sur `Add User`.
6. Sélectionnez ou invitez l’utilisateur afin de l’attribuer à un rôle spécifique.
7. Sélectionnez le rôle pour les utilisateurs.
8. Appuyez sur le bouton `Assign`.
9. Pour les utilisateurs bénéficiant du rôle `Administrator` ou `Approver`, continuez à ajouter des stratégies d’accès Azure Key Vault.

### <a name="remove-user"></a>Supprimer un utilisateur

1. Ouvrez le portail Azure sur `portal.azure.com`.
2. Accédez à `Azure Active Directory`/`Enterprise applications`.
3. Choisissez l’inscription du microservice OPC Vault, qui est par défaut votre `resourceGroupName-service`.
4. Accédez à `Users and Groups`.
5. Sélectionnez un utilisateur avec un rôle à supprimer.
6. Appuyez sur le bouton `Remove`.
7. Retirez également des stratégies Azure Key Vault les administrateurs et les approbateurs supprimés.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Ajouter une stratégie d’accès utilisateur à Azure Key Vault

Des stratégies d’accès supplémentaires sont nécessaires pour les **Approbateurs** et les **Administrateurs**.

Par défaut, l’identité du service dispose uniquement d’autorisations limitées pour accéder à Key Vault afin d’empêcher que des changements ou opérations avec élévation de privilèges aient lieu sans emprunt d’identité d’utilisateur. Les autorisations de service de base sont `Get` et `List` aussi bien pour les secrets que pour les certificats. Pour les secrets, il n’existe qu’une seule exception : le service peut supprimer (`Delete`) une clé privée du magasin des secrets une fois qu’elle a été acceptée par un utilisateur. Toutes les autres opérations exigent des autorisations d’utilisateur dont l’identité a été empruntée.<br>

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>Pour un **rôle Approbateur**, les autorisations suivantes doivent être ajoutées à Key Vault :

1. Ouvrez le portail Azure sur `portal.azure.com`.
2. Accédez à votre `resourceGroupName` OPC Vault utilisé pendant le déploiement.
3. Accédez au `resourceGroupName-xxxxx` Key Vault.
4. Accédez aux stratégies d’accès (`Access Policies`).
5. Cliquez sur `Add new`.
6. Ignorez le modèle. Il n’existe aucun modèle qui correspond aux exigences.
7. Cliquez sur `Select Principal`, puis sélectionnez l’utilisateur à ajouter ou invitez un nouvel utilisateur dans le locataire.
8. Vérifiez les autorisations de clé (`Key permissions`) : `Get`, `List` et surtout `Sign`.
9. Vérifiez les autorisations de secret (`Secret permissions`) : `Get`, `List`, `Set` et `Delete`.
10. Vérifiez les autorisations de certificat (`Certificate permissions`) : `Get` et `List`.
11. Cliquez sur `Ok`.
12. Enregistrez (`Save`) les changements.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>Pour un **rôle Administrateur**, les autorisations suivantes doivent être ajoutées à Key Vault :

1. Ouvrez le portail Azure sur `portal.azure.com`.
2. Accédez à votre `resourceGroupName` OPC Vault utilisé pendant le déploiement.
3. Accédez au `resourceGroupName-xxxxx` Key Vault.
4. Accédez aux stratégies d’accès (`Access Policies`).
5. Cliquez sur `Add new`.
6. Ignorez le modèle. Il n’existe aucun modèle qui correspond aux exigences.
7. Cliquez sur `Select Principal`, puis sélectionnez l’utilisateur à ajouter ou invitez un nouvel utilisateur dans le locataire.
8. Vérifiez les autorisations de clé (`Key permissions`) : `Get`, `List` et surtout `Sign`.
9. Vérifiez les autorisations de secret (`Secret permissions`) : `Get`, `List`, `Set` et `Delete`.
10. Vérifiez les autorisations de certificat (`Certificate permissions`) : `Get`, `List`, `Update`, `Create` et `Import`.
11. Cliquez sur `Ok`.
12. Enregistrez (`Save`) les changements.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Supprimer une stratégie d’accès utilisateur d’Azure Key Vault

1. Ouvrez le portail Azure sur `portal.azure.com`.
2. Accédez à votre `resourceGroupName` OPC Vault utilisé pendant le déploiement.
3. Accédez au `resourceGroupName-xxxxx` Key Vault.
4. Accédez aux stratégies d’accès (`Access Policies`).
5. Recherchez l’utilisateur à supprimer, puis cliquez sur `... / Delete` pour supprimer l’accès utilisateur.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à gérer les utilisateurs et les certificats OPC Vault, voici la prochaine étape suggérée :

> [!div class="nextstepaction"]
> [Sécuriser la communication des appareils OPC](howto-opc-vault-secure.md)
