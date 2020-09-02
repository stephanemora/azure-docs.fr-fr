---
title: Tutoriel pour configurer Azure Active Directory B2C avec whoIam
titleSuffix: Azure AD B2C
description: Dans ce tutoriel, découvrez comment intégrer l’authentification Azure AD B2C avec whoIam pour la vérification de l’utilisateur
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 6545c5f40004dc50904618a8ea734eb73eeee933
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817066"
---
# <a name="tutorial-for-configuring-whoiam-with-azure-active-directory-b2c"></a>Tutoriel de configuration de WhoIAM avec Azure Active Directory B2C

Dans cet exemple de tutoriel, nous fournissons des conseils sur la façon de configurer le système de gestion des identités personnalisé (BRIMS) [WhoIAM](https://www.whoiam.ai/brims/) dans votre environnement et de l’intégrer dans Azure AD B2C.

Le système BRIMS de WhoIAM est un ensemble d’applications et de services qui est déployé dans votre environnement. Il fournit une vérification de votre base d’utilisateurs par le biais de la voix, de SMS et d’adresses e-mails. Le système BRIMS fonctionne en liaison avec votre solution existante de gestion des identités et des accès et n’est dépendant d’aucune plateforme.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous avez :

- Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).

- Un [locataire Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) qui est lié à votre abonnement Azure.

- Un [compte d'évaluation gratuite](https://www.whoiam.ai/contact-us/) WhoIAM

## <a name="scenario-description"></a>Description du scénario

L’intégration de WhoIAM inclut les composants suivants :

- Locataire Azure AD B2C : serveur d’autorisation chargé de vérifier les informations d’identification de l’utilisateur en fonction de stratégies personnalisées définies dans le locataire. Également connu sous le nom de fournisseur d’identité.

- Un portail d’administration pour la gestion des clients et de leurs configurations

- Un service d’API qui expose diverses fonctionnalités via des points de terminaison  

- Azure Cosmos DB, qui est utilisé comme serveur principal pour le portail d’administration du système BRIMS et le service d’API

Le diagramme d’architecture suivant illustre l’implémentation.

![capture d’écran du diagramme d’architecture de whoiam](media/partner-whoiam/whoiam-architecture-diagram.png)

|Étape | Description |
|:-----| :-----------|
| 1. | L’utilisateur accède à une page de connexion. L’utilisateur lance une demande d’inscription ou de connexion à une application qui utilise Azure AD B2C comme fournisseur d'identité.
| 2. | Dans le cadre de l’authentification, l’utilisateur demande à vérifier la propriété de son adresse de messagerie ou de son téléphone, ou à utiliser sa voix comme facteur de vérification biométrique.  
| 3. | Azure AD B2C appelle le service d’API du système BRIMS en transmettant l’adresse e-mail de l’utilisateur, son numéro de téléphone et l’enregistrement de sa voix.
| 4. | Le système BRIMS utilise des configurations prédéfinies telles que des modèles d’adresses e-mail et de SMS entièrement personnalisables pour interagir avec l’utilisateur dans sa langue respective, en toute cohérence avec l’aspect de l’application.
| 5. | Une fois que la vérification de l’identité d’un utilisateur est terminée, le système renvoie un jeton indiquant le résultat de la vérification à Azure AD B2C. Azure AD B2C permet alors à l’utilisateur d’accéder à l’application ou fait échouer sa tentative d’authentification.  

## <a name="onboard-with-whoiam"></a>Intégrer avec WhoIAM

1. Contactez [WhoIAM](https://www.whoiam.ai/contact-us/) et créez un compte BRIMS.

2. Une fois qu’un compte est créé, utilisez les instructions d’intégration mises à votre disposition et configurez les services Azure suivants :

    - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) : utilisé pour le stockage sécurisé de mots de passe tels que les mots de passe de services de messagerie.

    - [Azure App Service](https://azure.microsoft.com/services/app-service/) : utilisé pour héberger les services de l’API BRIMS et du portail d’administration

    - [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) : utilisé pour authentifier les utilisateurs administratifs pour le portail d’administration

    - [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) : utilisé pour stocker et récupérer des paramètres

    - [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview#:~:text=Application%20Insights%2C%20a%20feature%20of%20Azure%20Monitor%2C%20is,professionals.%20Use%20it%20to%20monitor%20your%20live%20applications.) (facultatif) : utilisé pour se connecter à l’API et au portail d’administration

3. Déployez l’API du système BRIMS et le portail d’administration de BRIMS dans votre environnement Azure.

4. Les modèles de stratégie personnalisée Azure AD B2C sont disponibles dans votre documentation d’intégration du système BRIMS. Suivez les instructions données dans le document pour configurer votre application et utilisez la plateforme du système BRIMS pour vérifier l’identité de l’utilisateur.  

Pour plus d’informations sur le système BRIMS de WhoIAM, consultez la [documentation produit](https://www.whoiam.ai/brims/)

## <a name="test-the-user-flow"></a>Tester le flux utilisateur

1. Ouvrez le locataire Azure AD B2C locataire, puis, sous Stratégies, sélectionnez **Identity Experience Framework**.

2. Sélectionnez votre **SignUpSignIn** créé précédemment.

3. Sélectionnez **Exécuter le flux d’utilisateurs**, puis sélectionnez les paramètres :

   a. **Application** : sélectionnez l’application inscrite (JWT dans l’exemple).

   b. **URL de réponse** : sélectionnez l’**URL de redirection**.

   c. Sélectionnez **Exécuter le flux utilisateur**.

4. Suivez le processus d’inscription et créez un compte

5. Le service BRIMS sera appelé pendant le flux, après la création de l’attribut d’utilisateur. Si le flux est incomplet, vérifiez que l’utilisateur n’est pas enregistré dans le répertoire.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants :

- [Stratégies personnalisées dans Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Bien démarrer avec les stratégies personnalisées dans Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
