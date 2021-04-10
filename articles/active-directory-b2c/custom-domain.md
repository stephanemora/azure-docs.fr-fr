---
title: Activer des domaines personnalisés Azure AD B2C
titleSuffix: Azure AD B2C
description: Découvrez comment activer des domaines personnalisés dans vos URL de redirection pour Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2de419885938b27ebce4a934db5ef966965b3dbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580162"
---
# <a name="enable-custom-domains-for-azure-active-directory-b2c"></a>Activer des domaines personnalisés pour Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Cet article décrit comment activer des domaines personnalisés dans vos URL de redirection pour Azure Active Directory B2C (Azure AD B2C). L’utilisation d’un domaine personnalisé avec votre application offre une expérience utilisateur plus transparente. Du point de vue de l’utilisateur, elles restent dans votre domaine au cours du processus de connexion au lieu de rediriger vers le domaine par défaut Azure AD B2C *<nom du locataire>.b2clogin.com*.

![Expérience utilisateur de domaine personnalisé](./media/custom-domain/custom-domain-user-experience.png)

## <a name="custom-domain-overview"></a>Vue d’ensemble des domaines personnalisés

Vous pouvez activer des domaines personnalisés pour Azure AD B2C à l’aide d’[Azure Front Door](https://azure.microsoft.com/services/frontdoor/). Azure Front Door est un point d’entrée mondial scalable qui utilise le réseau de périphérie mondial de Microsoft pour créer des applications web rapides, sécurisées et très scalables. Vous pouvez afficher le contenu Azure AD B2C derrière Azure Front Door, puis configurer une option dans Azure Front Door pour fournir le contenu via un domaine personnalisé dans l’URL de votre application.

Le diagramme suivant illustre l’intégration d’Azure Front Door :

1. À partir d’une application, un utilisateur clique sur le bouton de connexion, qui l’amène à la page de connexion d’Azure AD B2C. Cette page spécifie un nom de domaine personnalisé.
1. Le navigateur web résout le nom de domaine personnalisé en adresse IP Azure Front Door. Pendant la résolution DNS, un enregistrement de nom canonique (CNAME) avec un nom de domaine personnalisé pointe vers l’hôte frontal par défaut de Front Door (par exemple, `contoso.azurefd.net`). 
1. Le trafic adressé au domaine personnalisé (par exemple, `login.contoso.com`) est acheminé vers l’hôte frontal par défaut de Front Door spécifié (`contoso.azurefd.net`).
1. Front Door appelle le contenu Azure AD B2C à l’aide du domaine `<tenant-name>.b2clogin.com` Azure AD B2C par défaut. La requête adressée au point de terminaison Azure AD B2C inclut un en-tête HTTP personnalisé qui contient le nom de domaine personnalisé d’origine.
1. Azure AD B2C répond à la requête en affichant le contenu approprié et le domaine personnalisé d’origine.

![Diagramme de mise en réseau de domaine personnalisé](./media/custom-domain/custom-domain-network-flow.png)

> [!IMPORTANT]
> La connexion entre le navigateur et Azure Front Door doit toujours utiliser IPv4 au lieu d’IPv6.

Lorsque vous utilisez des domaines personnalisés, tenez compte des éléments suivants :

- Vous pouvez configurer plusieurs domaines personnalisés. Pour connaître le nombre maximal de domaines personnalisés pris en charge, consultez [Restrictions et limites du service Azure AD B2C](../active-directory/enterprise-users/directory-service-limits-restrictions.md) pour Azure AD B2C et [Abonnement Azure et limites, quotas et contraintes de service](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits) pour Azure Front Door.
- Azure Front Door est un service Azure distinct. Des frais supplémentaires seront donc facturés. Pour plus d’informations, consultez [Tarification de Front Door](https://azure.microsoft.com/pricing/details/frontdoor).
- Pour utiliser le [pare-feu d’applications web](../web-application-firewall/afds/afds-overview.md) Azure Front Door, vous devez vérifier que la configuration et les règles de votre pare-feu fonctionnent correctement avec vos flux d’utilisateurs Azure AD B2C.
- Une fois que vous avez configuré des domaines personnalisés, les utilisateurs peuvent toujours accéder au nom de domaine par défaut Azure AD B2C *<nom de locataire>.b2clogin.com* (sauf si vous utilisez une stratégie personnalisée et que vous [bloquez l’accès](#block-access-to-the-default-domain-name).
- Si vous avez plusieurs applications, migrez-les toutes vers le domaine personnalisé, car le navigateur stocke la session de Azure AD B2C sous le nom de domaine en cours d’utilisation.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="add-a-custom-domain-name-to-your-tenant"></a>Ajouter un nom de domaine personnalisé à votre locataire

Suivez les instructions d'[ajout et de validation de votre domaine personnalisé dans Azure AD](../active-directory/fundamentals/add-custom-domain.md). Une fois le domaine vérifié, supprimez l’enregistrement TXT DNS que vous avez créé.

> [!IMPORTANT]
> Pour ces étapes, veillez à vous connecter à votre locataire **Azure AD B2C** et à sélectionner le service **Azure Active Directory**.

Vérifiez chaque sous-domaine que vous envisagez d’utiliser. La vérification du domaine de niveau supérieur seulement ne suffit pas. Par exemple, pour pouvoir vous connecter avec *login.contoso.com* et *account.contoso.com*, vous devez vérifier les deux sous-domaines et pas seulement le domaine de niveau supérieur *contoso.com*.  

## <a name="create-a-new-azure-front-door-instance"></a>Créer une nouvelle instance Azure Front Door

Suivez les étapes de [création d’une Front Door pour votre application](../frontdoor/quickstart-create-front-door.md#create-a-front-door-for-your-application) en utilisant les paramètres par défaut pour l’hôte frontal et les règles d’acheminement. 

> [!IMPORTANT]
> Pour ces étapes, une fois que vous êtes connecté au portail Azure à l’étape 1, sélectionnez **Répertoire + abonnement** et choisissez le répertoire qui contient l’abonnement Azure que vous souhaitez utiliser pour Azure Front Door. Il ne doit *pas* s’agir du répertoire contenant votre locataire Azure AD B2C. 

À l’étape **Ajouter un serveur principal**, utilisez les paramètres suivants :

* Pour **Type d’hôte principal**, sélectionnez **Hôte personnalisé**.  
* Pour **Nom d’hôte principal**, sélectionnez le nom d’hôte de votre point de terminaison Azure AD B2C, <nom de locataire>.b2clogin.com. Exemple : contoso.b2clogin.com. 
* Pour **En-tête d’hôte principal**, sélectionnez la valeur que vous avez sélectionnée pour **Nom d’hôte principal**.

![Ajouter un serveur principal](./media/custom-domain/add-a-backend.png)

Après avoir ajouté le **serveur principal** au **pool principal**, désactivez les **sondes d’intégrité**.

![Ajouter un pool principal](./media/custom-domain/add-a-backend-pool.png)

## <a name="set-up-your-custom-domain-on-azure-front-door"></a>Configurer votre domaine personnalisé sur Azure Front Door

Suivez les étapes pour [ajouter un domaine personnalisé à votre Front Door](../frontdoor/front-door-custom-domain.md). Lors de la création de l’enregistrement `CNAME` de votre domaine personnalisé, utilisez le nom de domaine personnalisé que vous avez vérifié précédemment à l’étape [Ajouter un nom de domaine personnalisé à votre Azure AD](#add-a-custom-domain-name-to-your-tenant). 

Une fois le nom de domaine personnalisé vérifié, sélectionnez **Nom de domaine personnalisé HTTPS**. Ensuite, sous le **type de gestion des certificats**, sélectionnez [Gestion Front Door](../frontdoor/front-door-custom-domain-https.md#option-1-default-use-a-certificate-managed-by-front-door)ou [Utiliser mon propre certificat](../frontdoor/front-door-custom-domain-https.md#option-2-use-your-own-certificate). 

La capture d’écran suivante montre comment ajouter un domaine personnalisé et activer HTTPS à l’aide d’un certificat Azure Front Door.

![Configurer un domaine personnalisé Azure Front Door](./media/custom-domain/azure-front-door-add-custom-domain.png) 

## <a name="configure-cors"></a>Configuration de CORS

Si vous [personnalisez l’interface utilisateur Azure AD B2C](customize-ui-with-html.md) avec un modèle HTML, vous devez [configurer CORQ](customize-ui-with-html.md?pivots=b2c-user-flow.md#3-configure-cors) avec votre domaine personnalisé.

Configurez le stockage d’objets BLOB Azure pour le partage des ressources cross-origin en procédant comme suit :

1. Dans le [Portail Azure](https://portal.azure.com), accédez à votre compte de stockage.
1. Dans le menu, sélectionnez **CORS**.
1. Pour **Origines autorisées**, entrez `https://your-domain-name`. Remplacez `your-domain-name` par le nom de votre domaine. Par exemple : `https://login.contoso.com`. Utilisez des minuscules quand vous entrez le nom de votre locataire.
1. Pour **Méthodes autorisées**, sélectionnez `GET` et `OPTIONS`.
1. Pour **En-têtes autorisés**, saisissez un astérisque (*).
1. Pour **En-têtes exposés**, saisissez un astérisque (*).
1. Pour **Âge maximal**, tapez 200.
1. Sélectionnez **Enregistrer**.

## <a name="configure-your-identity-provider"></a>Configurer votre fournisseur d’identité

Lorsqu’un utilisateur choisit de se connecter avec un fournisseur d’identité sociale, Azure AD B2C lance une requête d’autorisation et dirige l’utilisateur vers le fournisseur d’identité sélectionné pour terminer le processus de connexion. La requête d’autorisation spécifie le `redirect_uri` avec le nom de domaine par défaut Azure AD B2C : 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>/oauth2/authresp
```

Si vous avez configuré votre stratégie pour autoriser la connexion avec un fournisseur d’identité externe, mettez à jour les URI de redirection OAuth avec le domaine personnalisé. La plupart des fournisseurs d’identité vous permettent d’inscrire plusieurs URI de redirection. Nous vous recommandons d’ajouter des URI de redirection plutôt que de les remplacer pour pouvoir tester votre stratégie personnalisée sans affecter les applications qui utilisent le nom de domaine par défaut Azure AD B2C. 

Dans l’URI de redirection suivant :

```http
https://<custom-domain-name>/<tenant-name>/oauth2/authresp
``` 

- Remplacez **<nom de domaine personnalisé>** par le nom de votre domaine personnalisé.
- Remplacez **<nom de locataire>** par le nom de votre locataire ou l’ID de votre locataire.

L’exemple suivant montre un URI de redirection OAuth valide :

```http
https://login.contoso.com/contoso.onmicrosoft.com/oauth2/authresp
```

Si vous choisissez d’utiliser l'[ID de locataire](#optional-use-tenant-id), un URI de redirection OAuth valide ressemble à ce qui suit :

```http
https://login.contoso.com/11111111-1111-1111-1111-111111111111/oauth2/authresp
```

Les métadonnées des [fournisseurs d’identité SAML](saml-identity-provider-technical-profile.md) se présentent comme suit :

```http
https://<custom-domain-name>.b2clogin.com/<tenant-name>/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

## <a name="test-your-custom-domain"></a>Tester votre domaine personnalisé

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Stratégies**, sélectionnez **Flux utilisateur (stratégies)** .
1. Sélectionnez un flux d’utilisateur, puis sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *webapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Cliquez sur **Copier dans le Presse-papiers**.

    ![Copier l’URI de requête d'autorisation](./media/custom-domain/user-flow-run-now.png)

1. Dans l’URL du point de terminaison **Exécuter le flux d’utilisateur**, remplacez le domaine Azure AD B2C (<nom de locataire>.b2clogin.com) par votre domaine personnalisé.  
    Par exemple, à la place de :

    ```http
    https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login
    ```

    utiliser :

    ```http
    https://login.contoso.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login    
    ```
1. Sélectionnez **Exécuter le flux utilisateur**. Votre stratégie Azure AD B2C doit être chargée.
1. Connectez-vous avec des comptes locaux et sociaux.
1. Répétez le test avec vos stratégies restantes.

## <a name="configure-your-application"></a>Configuration de votre application 

Après avoir configuré et testé le domaine personnalisé, vous pouvez mettre à jour vos applications pour charger l’URL qui spécifie votre domaine personnalisé comme nom d’hôte au lieu du domaine Azure AD B2C. 

L’intégration de domaine personnalisé s’applique aux points de terminaison d’authentification qui utilisent des stratégies Azure AD B2C (flux d’utilisateur ou stratégies personnalisées) pour authentifier les utilisateurs. Ces points de terminaison peuvent ressembler à ceci :

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/v2.0/.well-known/openid-configuration</code>

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://\<custom-domain\>/<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

Remplacez :
- **custom-domain** par votre domaine personnalisé
- **tenant-name** par le nom ou l’ID de votre locataire
- **your-policy** par le nom de votre stratégie. [En savoir plus sur les stratégies Azure AD B2C](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 


Les métadonnées du [fournisseur de services SAML](./saml-service-provider.md) peuvent se présenter comme ce qui suit : 

```html
https://custom-domain-name/tenant-name/policy-name/Samlp/metadata
```

### <a name="optional-use-tenant-id"></a>(Facultatif) Utiliser l’ID de locataire

Vous pouvez remplacer le nom de votre locataire B2C dans l’URL par le GUID d’ID de votre locataire pour supprimer toutes les références à « b2c » dans l’URL. Vous trouverez le GUID d’ID de votre locataire dans la page Vue d’ensemble de B2C de le portail Azure.
Par exemple, remplacez `https://account.contosobank.co.uk/contosobank.onmicrosoft.com/` par `https://account.contosobank.co.uk/<tenant ID GUID>/`

Si vous choisissez d’utiliser l’ID de locataire au lieu du nom de locataire, veillez à mettre à jour les **URI de redirection OAuth** du fournisseur d’identité en conséquence. Pour plus d’informations, consultez [Configurer votre fournisseur d'identité](#configure-your-identity-provider).

### <a name="token-issuance"></a>Émission du jeton

Le nom de l’émetteur de jeton (ISS) demande des modifications en fonction du domaine personnalisé utilisé. Exemple :

```http
https://<domain-name>/11111111-1111-1111-1111-111111111111/v2.0/
```

::: zone pivot="b2c-custom-policy"

## <a name="block-access-to-the-default-domain-name"></a>Bloquer l’accès au nom de domaine par défaut

Une fois que vous avez ajouté le domaine personnalisé et configuré votre application, les utilisateurs peuvent toujours accéder au domaine <nom de locataire> b2clogin.com. Pour empêcher l’accès, vous pouvez configurer la stratégie pour vérifier la demande d’autorisation « nom d’hôte » par rapport à une liste de domaines autorisés. Le nom d’hôte est le nom de domaine qui apparaît dans l’URL. Le nom d’hôte est disponible via des `{Context:HostName}` [programmes de résolution de revendications](claim-resolver-overview.md). Vous pouvez ensuite présenter un message d’erreur personnalisé. 

1. Obtenez l’exemple d’une stratégie d’accès conditionnel qui vérifie le nom d’hôte à partir de [GitHub](https://github.com/azure-ad-b2c/samples/blob/master/policies/check-host-name).
1. Dans chaque fichier, remplacez la chaîne `yourtenant` par le nom de votre locataire Azure AD B2C. Par exemple, si le nom de votre locataire B2C est *contosob2c*, toutes les instances de `yourtenant.onmicrosoft.com` deviennent `contosob2c.onmicrosoft.com`.
1. Téléchargez les fichiers de stratégie dans l'ordre suivant : `B2C_1A_TrustFrameworkExtensions_HostName.xml` puis `B2C_1A_signup_signin_HostName.xml`.

::: zone-end

## <a name="troubleshooting"></a>Dépannage

### <a name="azure-ad-b2c-returns-a-page-not-found-error"></a>Azure AD B2C renvoie une erreur de page introuvable

- **Symptôme** : après avoir configuré un domaine personnalisé, lorsque vous essayez de vous connecter avec le domaine personnalisé, vous recevez un message d’erreur HTTP 404.
- **Causes possibles** : ce problème peut être lié à la configuration DNS ou à la configuration du serveur principal Azure Front Door. 
- **Résolution** :  
    1. Assurez-vous que le domaine personnalisé est [inscrit et vérifié avec succès](#add-a-custom-domain-name-to-your-tenant) dans votre locataire Azure AD B2C.
    1. Assurez-vous que le [domaine personnalisé](../frontdoor/front-door-custom-domain.md) est correctement configuré. L'enregistrement `CNAME` de votre domaine personnalisé doit pointer vers l’hôte frontal par défaut d’Azure Front Door (par exemple, contoso.azurefd.net).
    1. Assurez-vous que la [configuration du pool principal Azure Front Door](#set-up-your-custom-domain-on-azure-front-door) pointe vers le locataire dans lequel vous configurez le nom de domaine personnalisé, ainsi que l’emplacement de stockage de votre flux d’utilisateur ou de vos stratégies personnalisées.

### <a name="identify-provider-returns-an-error"></a>Le fournisseur d’identité retourne une erreur

- **Symptôme** : après avoir configuré un domaine personnalisé, vous êtes en mesure de vous connecter avec des comptes locaux. Toutefois, lorsque vous vous connectez avec des informations d’identification provenant de [fournisseurs d’identité sociale ou d’entreprise](add-identity-provider.md) externes, les fournisseurs d’identité présentent un message d’erreur.
- **Causes possibles** : lorsqu’Azure AD B2C amène l’utilisateur à se connecter avec un fournisseur d’identité fédérée, il spécifie l’URI de redirection. L’URI de redirection est le point de terminaison vers l’emplacement où le fournisseur d’identité retourne le jeton. L’URI de redirection est le même domaine que celui utilisé par votre application avec la requête d’autorisation. Si l’URI de redirection n’est pas encore inscrit dans le fournisseur d’identité, il se peut qu’il n’approuve pas le nouvel URI de redirection, ce qui génère un message d’erreur. 
- **Résolution** : suivez les étapes décrites dans [Configurer votre fournisseur d’identité](#configure-your-identity-provider) pour ajouter le nouvel URI de redirection. 


## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="can-i-use-azure-front-door-advanced-configuration-such-as-web-application-firewall-rules"></a>Puis-je utiliser la configuration avancée d’Azure Front Door, telle que les *règles de pare-feu d’applications web* ? 
  
Bien que les paramètres de configuration avancée d’Azure Front Door ne soient pas officiellement pris en charge, vous pouvez les utiliser à vos propres risques. 

### <a name="when-i-use-run-now-to-try-to-run-my-policy-why-i-cant-see-the-custom-domain"></a>Quand j’utilise Exécuter maintenant pour essayer d’exécuter ma stratégie, pourquoi ne puis-je pas voir le domaine personnalisé ?

Copiez l’URL, modifiez le nom de domaine manuellement, puis collez-le dans votre navigateur.

### <a name="which-ip-address-is-presented-to-azure-ad-b2c-the-users-ip-address-or-the-azure-front-door-ip-address"></a>Quelle adresse IP est présentée à Azure AD B2C ? L’adresse IP de l’utilisateur ou l’adresse IP d’Azure Front Door ?

Azure Front Door transmet l’adresse IP d’origine de l’utilisateur. Il s’agit de l’adresse IP que vous verrez dans les rapports d’audit ou votre stratégie personnalisée.

### <a name="can-i-use-a-third-party-web-application-firewall-waf-with-b2c"></a>Puis-je utiliser un pare-feu d’applications web (WAF) tiers avec B2C ?

Pour utiliser votre propre pare-feu d’applications web devant Azure Front Door, vous devez configurer et vérifier que tout fonctionne correctement avec vos flux d’utilisateurs Azure AD B2C.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [requêtes d’autorisation OAuth](protocols-overview.md).