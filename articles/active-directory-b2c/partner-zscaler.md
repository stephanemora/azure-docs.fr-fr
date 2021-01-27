---
title: 'Tutoriel : configurer Azure Active Directory B2C avec Zscaler'
titleSuffix: Azure AD B2C
description: Découvrez comment intégrer l’authentification Azure AD B2C avec Zscaler.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: fe427150b15c6bccb97172ae751235d388c95c7b
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98675024"
---
# <a name="tutorial-configure-zscaler-private-access-with-azure-active-directory-b2c"></a>Tutoriel : Configurer Zscaler Private Access avec Azure Active Directory B2C

Dans ce tutoriel, découvrez comment intégrer l’authentification Azure Active Directory B2C (Azure AD B2C) avec [Zscaler Private Access (ZPA)](https://www.zscaler.com/products/zscaler-private-access). ZPA fournit un accès sécurisé et basé sur des stratégies aux applications privées et aux ressources, sans les risques liés aux coûts, à la complexité ou à la sécurité d’un réseau privé virtuel (VPN). L’offre d’accès hybride sécurisé Zscaler active une surface d’attaque nulle pour les applications accessibles aux consommateurs lorsqu’elle est associée à Azure AD B2C.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vous avez besoin des éléments suivants :

- Un abonnement Azure. Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).  
- [Un locataire Azure AD B2C](./tutorial-create-tenant.md) lié à votre abonnement Azure.  
- [Un abonnement ZPA](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview).

## <a name="scenario-description"></a>Description du scénario

L’intégration ZPA inclut les composants suivants :

- **Azure AD B2C** : Fournisseur d’identité chargé de vérifier les informations d’identification de l’utilisateur, également chargé de l’inscription d’un nouvel utilisateur.  
- **ZPA** : Le service responsable de la sécurisation de l’application web en appliquant l’[accès sans approbation](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.).  
- **l’application web** : héberge le service auquel l’utilisateur tente d’accéder.

Le diagramme suivant décrit comment ZPA s’intègre à Azure AD B2C.

![Diagramme de l’architecture Zscaler, montrant comment ZPA s’intègre avec Azure AD B2C.](media/partner-zscaler/zscaler-architecture-diagram.png)

Cette stratégie est décrite dans le tableau suivant :

|Étape | Description |
| :-----:| :-----------|
| 1 | L’utilisateur arrive sur un portail utilisateur ZPA ou une application d’accès au navigateur ZPA.
| 2 | ZPA requiert les informations de contexte utilisateur avant de pouvoir décider s’il faut autoriser l’utilisateur à accéder à l’application web. Pour authentifier l’utilisateur, ZPA effectue une redirection SAML vers la page de connexion Azure AD B2C.  
| 3 | L’utilisateur arrive sur la page de connexion Azure AB B2C. Les nouveaux utilisateurs s’inscrivent pour créer un compte et les utilisateurs existants se connectent avec leurs informations d’identification existantes. Azure AD B2C valide l’identité de l’utilisateur.
| 4 | Une fois l’authentification réussie, Azure AD B2C redirige l’utilisateur vers ZPA avec l’assertion SAML. ZPA vérifie l’assertion SAML et définit le contexte utilisateur.
| 5 | ZPA évalue les stratégies d’accès pour l’utilisateur. Si l’utilisateur est autorisé à accéder à l’application web, la connexion est autorisée.

## <a name="onboard-to-zpa"></a>Intégration à ZPA

Ce tutoriel part du principe que vous disposez déjà d’une installation fonctionnelle de ZPA. Si vous commencez tout juste à utiliser ZPA, reportez-vous au [Guide de configuration pas à pas pour ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa).

## <a name="integrate-zpa-with-azure-ad-b2c"></a>Intégrer ZPA avec Azure AD B2C

### <a name="step-1-configure-azure-ad-b2c-as-an-idp-on-zpa"></a>Étape 1 : Configurer Azure AD B2C en tant que fournisseur d’identité sur ZPA

Pour configurer Azure AD B2C en tant que [fournisseur d’identité sur ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign), procédez comme suit :

1. Connectez-vous au [portail d’administration ZPA](https://admin.private.zscaler.com).

1. Accédez à **Administration** > **Configuration du fournisseur d’identité**.

1. Sélectionnez à **Ajouter une configuration de fournisseur d’identité**.

   Le volet **Ajouter une configuration de fournisseur d'identité** s’ouvre.

   ![Capture d’écran de l’onglet « informations IdP » dans le volet « Ajouter une configuration IdP ».](media/partner-zscaler/add-idp-configuration.png)

1. Sélectionnez l’onglet **Informations IdP**, puis procédez comme suit :

   a. Dans la boîte **Nom**, entrez **Azure AD B2C**.  
   b. Sous **Authentification unique**, sélectionnez **Utilisateur**.  
   c. Dans la liste déroulante **Domaines**, sélectionnez les domaines d’authentification que vous souhaitez associer à cet IdP.

1. Sélectionnez **Suivant**.

1. Sélectionnez l’onglet **Métadonnées SP**, puis procédez comme suit :

   a. Sous **URL du fournisseur de services**, copiez ou notez la valeur pour une utilisation ultérieure.  
   b. Sous **ID de l’identité du fournisseur de services**, copiez ou notez la valeur pour une utilisation ultérieure.

   ![Capture d’écran de l’onglet « Métadonnées SP » dans le volet « Ajouter une configuration IdP ».](media/partner-zscaler/sp-metadata.png)

1. Sélectionnez **Suspendre**.

Après la configuration d’Azure AD B2C, le reste de la configuration du fournisseur d’identité reprend.

### <a name="step-2-configure-custom-policies-in-azure-ad-b2c"></a>Étape 2 : Configurer les stratégies personnalisées dans Azure AD B2C

>[!Note]
>Cette étape est requise uniquement si vous n’avez pas encore configuré de stratégies personnalisées. Si vous disposez déjà d’une ou de plusieurs stratégies personnalisées, vous pouvez ignorer cette étape.

Pour configurer des stratégies personnalisées sur votre abonné Azure Active Directory B2C, consultez [Démarrage avec des stratégies personnalisées dans Azure Active Directory B2C](./custom-policy-get-started.md).

### <a name="step-3-register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>Étape 3 : Inscrire ZPA en tant qu’application SAML dans Azure AD B2C

Pour configurer une application SAML dans Azure AD B2C, consultez [Inscrire une application SAML dans Azure AD B2C](./connect-with-saml-service-providers.md). 

À l’étape [« 3.2 chargez et testez vos métadonnées de stratégie »](./connect-with-saml-service-providers.md#32-upload-and-test-your-policy-metadata), copiez ou notez l’URL des métadonnées SAML du fournisseur d'identité utilisée par Azure AD B2C. Vous en aurez besoin ultérieurement.

Suivez les instructions de l’étape [« 4.2 Mettre à jour le manifeste de l’application »](./connect-with-saml-service-providers.md#42-update-the-app-manifest). À l’étape 4.2, mettez à jour les propriétés du manifeste de l’application comme suit :

- Pour **identifierUris** : Utilisez l’ID de l’entité du fournisseur de services que vous avez copié ou noté précédemment à « l’étape 1.6.b ».  
- Pour **samlMetadataUrl** : ignorez cette propriété, car ZPA n’héberge pas d’URL de métadonnées SAML.  
- Pour **replyUrlsWithType** : utilisez l’ID du fournisseur de services que vous avez copié ou noté précédemment à « l’étape 1.6.a ».  
- Pour **logoutUrl** : ignorez cette propriété, car ZPA ne prend pas en charge une URL de déconnexion.

Le reste des étapes ne s’applique pas à ce tutoriel.

### <a name="step-4-extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>Étape 4 : Extraire des métadonnées SAML du fournisseur d’identité à partir d’Azure AD B2C

Vous devrez, ensuite, obtenir une URL de métadonnées SAML au format suivant :

```https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata```

Notez que `<tenant-name>` est le nom de votre abonné Azure AD B2C et `<policy-name>` est le nom de la stratégie SAML personnalisée que vous avez créée à l’étape précédente.

Par exemple, l'URL peut être `https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml//Samlp/metadata`.

Ouvrez un navigateur web et accédez à l’URL des métadonnées SAML. Cliquez avec le bouton de droite n’importe où dans la page, sélectionnez **Enregistrer sous**, puis enregistrez le fichier sur votre ordinateur pour l’utiliser à l’étape suivante.

### <a name="step-5-complete-the-idp-configuration-on-zpa"></a>Étape 5 : Terminer la configuration du fournisseur d’identité sur ZPA

Terminez la [configuration du fournisseur d’identité dans le portail d’administration ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign) partiellement effectuée à « l’étape 1 : Configurer Azure AD B2C en tant que fournisseur d’identité sur ZPA ».

1. Dans le [Portail d’administration ZPA](https://admin.private.zscaler.com), allez à **Administration** > **Configuration du fournisseur d’identité**.

1. Sélectionnez le fournisseur d’identité configuré à « l’étape 1 », puis sélectionnez **Reprendre**.

1. Dans le volet **ajouter une configuration IdP**, sélectionnez l’onglet **Créer IdP** , puis procédez comme suit :

   a. Sous **Fichier de métadonnées du fournisseur d’identité**, chargez le fichier de métadonnées que vous avez enregistré à « l’étape 4 : Extraire les métadonnées SAML du fournisseur d’identité à partir d’Azure AD B2C ».  
   b. Vérifiez que **État** de la configuration du fournisseur d’identité est **Activé**.  
   c. Sélectionnez **Enregistrer**.

   ![Capture d’écran de l’onglet « Créer IdP » dans le volet « Ajouter une configuration IdP ».](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>Test de la solution

Accédez à un portail utilisateur ZPA ou à une application d’accès au navigateur et testez le processus d’inscription ou de connexion. Le test doit aboutir à une authentification SAML réussie.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants :

- [Bien démarrer avec les stratégies personnalisées dans Azure AD B2C](./custom-policy-get-started.md)
- [Inscrire une application SAML dans Azure AD B2C](./connect-with-saml-service-providers.md)
- [Guide de configuration pas à pas pour ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)
- [Configurer un fournisseur d’identité pour l’authentification unique](https://help.zscaler.com/zpa/configuring-idp-single-sign)