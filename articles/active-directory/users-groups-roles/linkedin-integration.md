---
title: Activer l’intégration de connexions LinkedIn dans Azure Active Directory | Microsoft Docs
description: Explique comment activer et désactiver les connexions de comptes LinkedIn pour les applications Microsoft dans Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/11/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 0eaa2656313ecd9b64503051265dc16285f0a1f3
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44492838"
---
# <a name="linkedin-account-connections"></a>Connexions de comptes LinkedIn

Dans cet article, vous pouvez découvrir comment activer ou désactiver les connexions de comptes LinkedIn pour votre locataire dans le centre d’administration Azure Active Directory (Azure AD).

> [!IMPORTANT]
> Le paramètre de connexions de comptes LinkedIn est en cours de déploiement sur les locataires Azure AD. Une fois déployée sur votre locataire, elle est activée par défaut. 
> 
> Exceptions :
> * Le paramètre n’est pas disponible pour les clients qui utilisent Microsoft Cloud for US Government, Microsoft Cloud Germany ou Azure et Office 365 gérés par 21Vianet en Chine.
> * Le paramètre est désactivé par défaut pour les locataires approvisionnés en Allemagne. Notez que le paramètre n’est pas disponible pour les clients qui utilisent Microsoft Cloud Germany.
> * Le paramètre est désactivé par défaut pour les locataires approvisionnés en France.

> L’intégration ne fonctionne que si vous avez activé *et* si vous autorisez les utilisateurs à donner leur consentement pour que les applications accèdent aux données d’entreprise en leur nom. Pour plus d’informations sur le paramètre de consentement, voir [Comment supprimer l’accès d’un utilisateur à une application](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-or-disable-linkedin-account-connections-for-your-tenant-in-the-azure-portal"></a>Activer ou désactiver les connexions de comptes LinkedIn pour votre locataire dans le portail Azure

Vous pouvez activer ou désactiver les connexions de comptes LinkedIn pour votre locataire entier ou pour seulement les utilisateurs sélectionnés dans votre locataire.

1. Connectez-vous au [centre d’administration Azure Active Directory](https://aad.portal.azure.com/) en utilisant un compte d’administrateur général pour le locataire Azure AD.
2. Sélectionnez **Utilisateurs**.
3. Dans le panneau **Utilisateurs**, sélectionnez **Paramètres utilisateur**.
4. Sous **LinkedIn account connections (Connexions de comptes LinkedIn)**  :
  * Sélectionnez **Oui** pour activer les connexions de comptes LinkedIn pour tous les utilisateurs dans votre locataire.
  * Sélectionnez **Sélectionnés** pour activer les connexions de comptes LinkedIn uniquement pour les utilisateurs de locataire sélectionnés.
  * Sélectionnez **Non** pour désactiver les connexions de comptes LinkedIn pour tous les utilisateurs. ![Activation des connexions de comptes LinkedIn](./media/linkedin-integration/linkedin-integration.png)
5. Enregistrer vos paramètres lorsque vous avez terminé en sélectionnant **Enregistrer**.

## <a name="enable-or-disable-linkedin-account-connections-for-your-tenant-using-group-policy"></a>Activer ou désactiver les connexions de comptes LinkedIn pour votre locataire à l’aide d’une stratégie de groupe

1. Télécharger les [fichiers modèles d’administration Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Extrayez les fichiers **ADMX** et copiez-les dans votre magasin central.
3. Ouvrez la gestion des stratégies de groupe.
4. Créez un objet de stratégie de groupe avec le paramètre suivant : **Configuration utilisateur** > **Modèles d’administration** > **Microsoft Office 2016**  >  **Divers** > **Afficher les fonctionnalités LinkedIn dans les applications Office**.
5. Sélectionnez **Activé** ou **Désactivé**.
  
 État | Résultat
------ | ------
**Activé** | Le paramètre **Afficher les fonctionnalités LinkedIn dans les applications Office** dans les options d’Office 2016 est activé. Les utilisateurs de votre organisation peuvent utiliser les fonctionnalités LinkedIn dans leurs applications Office.
 **Désactivé** | Le paramètre **Afficher les fonctionnalités LinkedIn dans les applications Office** dans les options d’Office 2016 est désactivé et les utilisateurs finaux ne peuvent pas le modifier. Les utilisateurs de votre organisation ne peuvent pas utiliser les fonctionnalités LinkedIn dans leurs applications Office 2016.

Cette stratégie de groupe affecte uniquement les applications Office 2016 installées sur un ordinateur local. Les utilisateurs peuvent voir les fonctionnalités LinkedIn dans les cartes de profil Office 365 même s’ils désactivent LinkedIn dans leurs applications Office 2016.

## <a name="learn-more"></a>En savoir plus

* [Intégrer LinkedIn à votre organisation](linkedin-user-consent.md)

* [Informations et fonctionnalités LinkedIn dans les applications Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centre d’aide LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Étapes suivantes
Utilisez le lien suivant pour afficher vos paramètres de connexions de comptes LinkedIn actuels dans le portail Azure :

[Configurer les connexions de comptes LinkedIn](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 