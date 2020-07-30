---
title: Plateforme d’identités Microsoft et flux d’assertion du porteur SAML | Azure
description: Découvrez comment récupérer des données à partir de Microsoft Graph sans inviter l’utilisateur à entrer des informations d’identification à l’aide du flux d’assertion du porteur SAML.
services: active-directory
author: umeshbarapatre
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: kenwith
ms.reviewer: paulgarn
ms.custom: aaddev
ms.openlocfilehash: 2d745ddcf7b1d5bd7976a0ca8461c13d8ae3c849
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282379"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Plateforme d’identité Microsoft et flux d’assertion du porteur SAML OAuth 2.0
Le flux d’assertion du porteur SAML OAuth 2.0 vous permet de demander un jeton d’accès OAuth à l’aide d’une assertion SAML lorsqu’un client doit utiliser une relation d’approbation existante. La signature appliquée à l’assertion SAML fournit l’authentification de l’application autorisée. Une assertion SAML est un jeton de sécurité XML émis par un fournisseur d’identité et consommé par un fournisseur de services. Le fournisseur de services s’appuie sur son contenu pour identifier l’objet de l’assertion à des fins de sécurité.

L’assertion SAML est publiée sur le point de terminaison de jeton OAuth.  Le point de terminaison traite l’assertion et émet un jeton d’accès en fonction de l’approbation préalable de l’application. Le client n’est pas obligé d’avoir ou de stocker un jeton d’actualisation, et la clé secrète client ne doit pas être transmise au point de terminaison de jeton.

Le flux d’assertion du porteur SAML est utile lors de l’extraction de données à partir d’API Microsoft Graph (prenant uniquement en charge les autorisations déléguées) sans inviter l’utilisateur à entrer ses informations d’identification. Dans ce scénario, l’octroi des informations d’identification du client, qui est préféré pour les processus en arrière-plan, ne fonctionne pas.

Pour les applications qui effectuent une connexion interactive basée sur un navigateur pour obtenir une assertion SAML et voulant ensuite ajouter un accès à une API protégée OAuth (par exemple, Microsoft Graph), vous pouvez effectuer une requête OAuth pour obtenir un jeton d’accès pour l’API. Lorsque le navigateur est redirigé vers Azure AD pour authentifier l’utilisateur, le navigateur récupère la session à partir de la connexion SAML et l’utilisateur n’a pas besoin d’entrer ses informations d’identification.

Le flux d’assertion du porteur SAML OAuth est également pris en charge pour les utilisateurs qui s’authentifient auprès de fournisseurs d’identité tels que les services ADFS fédérés pour Azure Active Directory.  L’assertion SAML obtenue à partir d’ADFS peut être utilisée dans un processus OAuth pour authentifier l’utilisateur.

![Flux OAuth](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>Graphique des appels à l’aide de l’assertion du porteur SAML
Voyons à présent comment nous pouvons récupérer par programmation l’assertion SAML. Cette approche est testée avec ADFS. Toutefois, cela fonctionne avec n’importe quel fournisseur d’identité qui prend en charge le retour de l’assertion SAML par programmation. Le processus de base est le suivant: obtenir une assertion SAML, obtenir un jeton d’accès et accéder à Microsoft Graph.

### <a name="prerequisites"></a>Prérequis

Établissez une relation d’approbation entre le serveur/l’environnement d’autorisation (Microsoft 365) et le fournisseur d’identité, ou l’émetteur de l’assertion du porteur SAML 2,0 (ADFS). Pour configurer ADFS pour l’authentification unique et comme fournisseur d’identité, vous pouvez vous référer à [cet article](https://blogs.technet.microsoft.com/canitpro/2015/09/11/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365/).

Inscrivez l’application dans le [portail](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) :
1. Connectez-vous au [panneau d’inscription d’application du portail](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) (Veuillez noter que nous utilisons les points de terminaison v 2.0 pour l’API Graph et que vous devez donc inscrire l’application dans ce portail. Dans le cas contraire, nous aurions pu utiliser les inscriptions dans Azure Active Directory). 
1. Sélectionnez **Nouvelle inscription**.
1. Lorsque la page **Inscrire une application** s’affiche, saisissez les informations d’inscription de votre application : 
    1. **Nom** : saisissez un nom d’application cohérent qui s’affichera pour les utilisateurs de l’application.
    1. **Types de compte pris en charge** : sélectionnez les comptes à prendre en charge par l’application.
    1. **URI de redirection (facultatif)** : sélectionnez le type d’application que vous créez, Web ou Client public (mobile et bureau), puis entrez l’URI de redirection (ou URL de réponse).
    1. Lorsque vous avez terminé, sélectionnez **Inscrire**.
1. Notez l’ID du client d’application.
1. Dans le volet gauche, sélectionnez **Certificats et secrets**. Cliquez **Nouvelle clé secrète client** dans la section **Secrets client**. Copiez la nouvelle clé secrète client, vous ne pourrez pas la récupérer lorsque vous sortirez du panneau.
1. Dans le volet gauche, sélectionnez **Autorisations d’API**, puis **Ajouter une autorisation**. Sélectionnez **Microsoft Graph**, puis **Autorisations déléguées**, puis sélectionnez **Tasks.read** depuis que nous avons l’intention d’utiliser l’API Graph Outlook. 

Installez [Postman](https://www.getpostman.com/), un outil requis pour tester les exemples de demandes.  Plus tard, vous pouvez convertir les demandes en code.

### <a name="get-the-saml-assertion-from-adfs"></a>Obtenir l’assertion SAML à partir d’ADFS
Créez une requête POST sur le point de terminaison ADFS à l’aide d’une enveloppe SOAP pour extraire l’assertion SAML :

![Obtenir une assertion SAML](./media/v2-saml-bearer-assertion/2.png)

Valeurs d'en-tête :

![valeurs d'en-tête](./media/v2-saml-bearer-assertion/3.png)

corps de la requête ADFS :

![corps de la requête ADFS](./media/v2-saml-bearer-assertion/4.png)

Une fois cette requête publiée, vous devez recevoir une assertion SAML depuis ADFS. Seules les données de balise **Assertion SAML** sont requises, elles sont converties en encodage Base64 pour être utilisées dans les demandes ultérieures.

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>Obtenir le jeton OAuth2 à l’aide de l’assertion SAML 
Dans cette étape, récupérez un jeton OAuth2 à l’aide de la réponse d’assertion ADFS.

1. Créez une demande de publication, comme indiqué ci-dessous, avec les valeurs d’en-tête:

    ![requête POST](./media/v2-saml-bearer-assertion/5.png)
1. Dans le corps de la requête, remplacez **client_id**, **client_secret**et **assertion** (l’assertion SAML encodée en base64 a obtenu l’étape précédente) :

    ![Corps de la demande](./media/v2-saml-bearer-assertion/6.png)
1. Si la requête est réussie, vous recevrez un jeton d’accès à partir d’Azure Active Directory.

### <a name="get-the-data-with-the-oauth-token"></a>Récupérer les données avec le jeton OAuth

Après avoir reçu le jeton d’accès, appelez les API Graph (des tâches Outlook dans cet exemple). 

1. Créez une requête GET avec le jeton d’accès extrait à l’étape précédente :

    ![Requête GET](./media/v2-saml-bearer-assertion/7.png)

1. En cas de demande réussie, vous recevrez une réponse JSON.

## <a name="next-steps"></a>Étapes suivantes

Découvrez les différents [flux d’authentification et scénarios d’applications](authentication-flows-app-scenarios.md).