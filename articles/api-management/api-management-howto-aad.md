---
title: Autoriser des comptes de développeurs à l’aide d’Azure Active Directory - Gestion des API Azure | Microsoft Docs
description: Découvrez comment autoriser des utilisateurs à l’aide d’Azure Active Directory dans Gestion des API.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: 95e501eca0f7765cc5201f7b315703c3ca43df8f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60529480"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autoriser des comptes de développeurs à l’aide d’Azure Active Directory dans Gestion des API Azure

Cet article explique comment activer l’accès au portail des développeurs pour les utilisateurs à partir d’Azure Active Directory (Azure AD). Il montre également comment gérer des groupes d’utilisateurs Azure AD en ajoutant des groupes externes qui contiennent les utilisateurs.

## <a name="prerequisites"></a>Conditions préalables

- Suivez ce guide de démarrage rapide : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
- Importez et publiez une instance Gestion des API Azure. Pour plus d’informations, consultez [Importer et publier](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autoriser des comptes de développeurs à l’aide d’Azure AD

1. Connectez-vous au [Portail Azure](https://portal.azure.com). 
2. Sélectionnez ![flèche](./media/api-management-howto-aad/arrow.png).
3. Dans la zone de recherche, tapez **api**.
4. Sélectionnez **Services Gestion des API**.
5. Sélectionnez votre instance de service Gestion des API.
6. Sous **sécurité**, sélectionnez **identités**.
7. Cliquez sur **+Ajouter** en haut.

    Le volet **Ajouter le fournisseur d’identité** s’affiche à droite.
8. Sous **Type du fournisseur**, sélectionnez **Azure Active Directory**.

    Les contrôles qui vous permettent d’entrer d’autres informations nécessaires apparaissent dans le volet. Il s’agit notamment des contrôles **ID client** et **Clé secrète client**. (Vous trouverez des informations sur ces contrôles plus loin dans l’article.)
9. Prenez note du contenu de **l’URL de redirection**.
    
   ![Étapes à suivre pour ajouter un fournisseur d’identité dans le portail Azure](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. Dans votre navigateur, ouvrez un autre onglet. 
11. Accédez à la [portail Azure - inscriptions](https://go.microsoft.com/fwlink/?linkid=2083908) pour inscrire une application dans Active Directory.
12. Sous **gérer**, sélectionnez **inscriptions**.
13. Sélectionnez **Nouvelle inscription**. Sur le **inscrire une application** , définissez les valeurs comme suit :
    
* Définissez **nom** par un nom explicite. par exemple, *portail des développeurs*
* Définissez **pris en charge les types de comptes** à **comptes dans ce répertoire d’organisation uniquement**. 
* Définissez **URI de redirection** à la valeur que vous avez obtenu à l’étape 9. 
* Choisissez **inscrire**. 

14.  Une fois que l’application est inscrite, copiez la **ID d’Application (client)** à partir de la **vue d’ensemble** page. 
15. Revenez à votre instance gestion des API. Dans le **ajouter le fournisseur d’identité** fenêtre, collez la **ID d’Application (client)** valeur dans le **ID Client** boîte.
16. Revenez à la configuration d’Azure AD, sélectionnez **certificats et clés secrètes** sous **gérer**. Sélectionnez le **nouvelle clé secrète client** bouton. Entrez une valeur dans **Description**, sélectionnez une option pour **expiration** et choisissez **ajouter**. Copiez la valeur du secret client avant de quitter la page. Vous en aurez besoin à l’étape suivante. 
17. Revenez à votre instance gestion des API, collez la clé secrète dans le **clé secrète Client** boîte.

    > [!IMPORTANT]
    > Veillez à mettre à jour le **secret client** avant l’expiration de la clé. 
    >  
    >

18. La fenêtre **Ajouter le fournisseur d’identité** contient également la zone de texte **Locataires autorisés**. Spécifiez les domaines des instances Azure AD auxquelles vous souhaitez accorder l’accès aux API de l’instance de service Gestion des API. Vous pouvez séparer plusieurs domaines par des sauts de ligne, des espaces ou des virgules.

> [!NOTE]
> Vous pouvez spécifier plusieurs domaines dans la section **Locataires autorisés**. Pour qu’un utilisateur puisse se connecter à partir d’un autre domaine que le domaine d’origine dans lequel l’application a été enregistrée, l’administrateur général de l’autre domaine doit d’abord accorder à l’application l’autorisation d’accéder aux données de l’annuaire. Pour accorder l’autorisation, l’administrateur général doit : un. Accédez à `https://<URL of your developer portal>/aadadminconsent` (par exemple, https://contoso.portal.azure-api.net/aadadminconsent).
> b. Taper le nom de domaine du locataire Azure AD auquel il souhaite autoriser l’accès.
> c. Sélectionnez **Envoyer**. 

19.  Après avoir spécifié la configuration désirée, sélectionnez **Ajouter**.

Une fois les changements enregistrés, les utilisateurs de l’instance Azure AD spécifiée peuvent se connecter au portail des développeurs en suivant les étapes de la section [Se connecter au portail des développeurs avec un compte Azure AD](#log_in_to_dev_portal).


## <a name="add-an-external-azure-ad-group"></a>Ajouter un groupe Azure AD externe

Après avoir activé l’accès pour les utilisateurs dans une instance Azure AD, vous pouvez ajouter des groupes Azure AD dans Gestion des API. Vous pourrez ainsi gérer plus facilement l’association entre les développeurs du groupe et les produits désirés.

Pour configurer un groupe Azure AD externe, vous devez d’abord configurer l’instance Azure AD sous l’onglet **Identités** en suivant la procédure décrite dans la section précédente. 

Vous ajoutez des groupes Azure AD externes à partir de l’onglet **Groupes** de votre instance Gestion des API.

1. Sélectionnez l’onglet **Groupes** .
2. Sélectionnez le bouton **Ajouter un groupe AAD**.
   ![Bouton Ajouter un groupe AAD](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Sélectionnez le groupe à ajouter.
4. Appuyez sur le bouton **Sélectionner**.

Après avoir ajouté un groupe Azure AD externe, vous pouvez passer en revue ses propriétés et les configurer. Sélectionnez le nom du groupe sous l’onglet **Groupes**. À ce stade, vous pouvez modifier les informations **Nom** et **Description** du groupe.
 
Les utilisateurs de l’instance Azure AD configurée peuvent à présent se connecter au portail des développeurs. Ils peuvent afficher les groupes pour lesquels ils disposent d’une visibilité et s’abonner à ces groupes.

## <a name="a-idlogintodevportalsign-in-to-the-developer-portal-by-using-an-azure-ad-account"></a><a id="log_in_to_dev_portal"/>Se connecter au portail des développeurs avec un compte Azure AD

Pour vous connecter au portail des développeurs à l’aide d’un compte Azure AD que vous avez configuré dans les sections précédentes :

1. Ouvrez une nouvelle fenêtre de navigateur à l’aide de l’URL de connexion provenant de la configuration de l’application Active Directory, puis sélectionnez **Azure Active Directory**.

   ![page de connexion][api-management-dev-portal-signin]

1. Entrez les informations d’identification de l’un des utilisateurs dans Azure AD, puis sélectionnez **Se connecter**.

   ![Connexion avec un nom d’utilisateur et un mot de passe][api-management-aad-signin]

1. Un formulaire d’inscription peut vous être présenté si certaines informations supplémentaires sont nécessaires. Renseignez le formulaire d’inscription, puis sélectionnez **S’inscrire**.

   ![Bouton S’inscrire sur le formulaire d’inscription][api-management-complete-registration]

Votre utilisateur est maintenant inscrit auprès du portail des développeurs pour votre instance de service Gestion des API.

![Portail des développeurs une fois l’inscription terminée][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: https://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
