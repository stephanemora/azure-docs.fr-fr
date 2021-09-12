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
ms.date: 08/16/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c88954434c38a04d0d1263e96639e6cca03c3a1b
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122534725"
---
# <a name="enable-custom-domains-for-azure-active-directory-b2c"></a>Activer des domaines personnalisés pour Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Cet article décrit comment activer des domaines personnalisés dans vos URL de redirection pour Azure Active Directory B2C (Azure AD B2C). L’utilisation d’un domaine personnalisé avec votre application offre une expérience utilisateur plus transparente. Du point de vue de l’utilisateur, les URL restent dans votre domaine au cours du processus de connexion au lieu de rediriger vers le domaine par défaut Azure AD B2C *&lt;<nom du locataire&gt;.b2clogin.com*.

![La capture d’écran illustre une expérience utilisateur de domaine personnalisé Azure AD B2C.](./media/custom-domain/custom-domain-user-experience.png)

## <a name="custom-domain-overview"></a>Vue d’ensemble des domaines personnalisés

Vous pouvez activer des domaines personnalisés pour Azure AD B2C à l’aide d’[Azure Front Door](https://azure.microsoft.com/services/frontdoor/). Azure Front Door est un point d’entrée mondial qui utilise le réseau de périmètre mondial de Microsoft pour créer des applications web rapides, sécurisées et très évolutives. Vous pouvez afficher le contenu Azure AD B2C derrière Azure Front Door, puis configurer une option dans Azure Front Door pour fournir le contenu via un domaine personnalisé dans l’URL de votre application.

Le diagramme suivant illustre l’intégration d’Azure Front Door :

1. À partir d’une application, un utilisateur sélectionne le bouton de connexion qui l’amène à la page de connexion d’Azure AD B2C. Cette page spécifie un nom de domaine personnalisé.
1. Le navigateur web résout le nom de domaine personnalisé en adresse IP Azure Front Door. Pendant la résolution DNS, un enregistrement de nom canonique (CNAME) avec un nom de domaine personnalisé pointe vers l’hôte frontal par défaut de Front Door (par exemple, `contoso.azurefd.net`). 
1. Le trafic adressé au domaine personnalisé (par exemple, `login.contoso.com`) est acheminé vers l’hôte frontal par défaut de Front Door spécifié (`contoso.azurefd.net`).
1. Front Door appelle le contenu Azure AD B2C à l’aide du domaine `<tenant-name>.b2clogin.com` Azure AD B2C par défaut. La demande adressée au point de terminaison Azure AD B2C inclut le nom de domaine personnalisé d’origine.
1. Azure AD B2C répond à la requête en affichant le contenu approprié et le domaine personnalisé d’origine.

![Le diagramme illustre le flux de mise en réseau du domaine personnalisé.](./media/custom-domain/custom-domain-network-flow.png)

> [!IMPORTANT]
> La connexion entre le navigateur et Azure Front Door doit toujours utiliser IPv4 au lieu d’IPv6.

Lorsque vous utilisez des domaines personnalisés, tenez compte des éléments suivants :

- Vous pouvez configurer plusieurs domaines personnalisés. Pour connaître le nombre maximal de domaines personnalisés pris en charge, consultez [Restrictions et limites du service Azure AD B2C](../active-directory/enterprise-users/directory-service-limits-restrictions.md) pour Azure AD B2C et [Abonnement Azure et limites, quotas et contraintes de service](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits) pour Azure Front Door.
- Azure Front Door étant un service Azure distinct, des frais supplémentaires seront donc facturés. Pour plus d’informations, consultez [Tarification de Front Door](https://azure.microsoft.com/pricing/details/frontdoor).
- Pour utiliser le [pare-feu d’applications web](../web-application-firewall/afds/afds-overview.md) Azure Front Door, vous devez vérifier que la configuration et les règles de votre pare-feu fonctionnent correctement avec vos flux d’utilisateurs Azure AD B2C.
- Une fois que vous avez configuré des domaines personnalisés, les utilisateurs peuvent toujours accéder au nom de domaine par défaut Azure AD B2C *&lt;<nom de locataire&gt;.b2clogin.com* (sauf si vous utilisez une stratégie personnalisée et [bloquez l’accès](#block-access-to-the-default-domain-name).
- Si vous avez plusieurs applications, migrez-les toutes vers le domaine personnalisé, car le navigateur stocke la session de Azure AD B2C sous le nom de domaine en cours d’utilisation.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="step-1-add-a-custom-domain-name-to-your-azure-ad-b2c-tenant"></a>Étape 1. Ajouter un nom de domaine personnalisé à votre locataire Azure AD B2C

Chaque nouveau locataire Azure AD B2C est fourni avec un nom de domaine initial, au format &lt;nom_de_domaine&gt;.onmicrosoft.com. Vous ne pouvez ni modifier ni supprimer le nom de domaine initial, mais vous pouvez ajouter un domaine personnalisé. 

Pour ajouter un domaine personnalisé à votre locataire Azure AD B2C, procédez comme suit :

1. [Ajoutez votre nom de domaine personnalisé à Azure AD](../active-directory/fundamentals/add-custom-domain.md#add-your-custom-domain-name-to-azure-ad).

    > [!IMPORTANT]
    > Pour ces étapes, veillez à vous connecter à votre locataire **Azure AD B2C** et à sélectionner le service **Azure Active Directory**.

1. [Ajoutez vos informations DNS au bureau d’enregistrement de domaines](../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar). Après avoir ajouté votre nom de domaine personnalisé à Azure AD, créez un DNS `TXT` ou un enregistrement `MX` pour votre domaine. La création de cet enregistrement DNS pour votre domaine entraîne la vérification de la propriété de votre nom de domaine.

    Les exemples suivants illustrent les enregistrements TXT pour *login.contoso.com* et *account.contoso.com* :

    |Nom (nom d’hôte)  |Type  |Données  |
    |---------|---------|---------|
    |login   | TXT  | MS=ms12345678  |
    |account | TXT  | MS=ms87654321  |
    
    L’enregistrement TXT doit être associé au sous-domaine ou au nom d’hôte du domaine. Par exemple, la partie *login* du domaine *contoso.com*. Si le nom d’hôte est vide ou est `@`, Azure AD n’est pas en mesure de vérifier le domaine personnalisé que vous avez ajouté. Dans les exemples suivants, les deux enregistrements sont mal configurés.
    
    |Nom (nom d’hôte)  |Type  |Données  |
    |---------|---------|---------|
    | | TXT  | MS=ms12345678  |
    | @ | TXT  | MS=ms12345678  | 
    
    > [!TIP]
    > Vous pouvez gérer votre domaine personnalisé avec n’importe quel service DNS disponible publiquement, tel que GoDaddy. Si vous n’avez pas de serveur DNS, vous pouvez utiliser une [zone Azure DNS](../dns/dns-getstarted-portal.md) ou des [domaines App Service](../app-service/manage-custom-dns-buy-domain.md).

1. [Vérifiez votre nom de domaine personnalisé](../active-directory/fundamentals/add-custom-domain.md#verify-your-custom-domain-name). Vérifiez chaque sous-domaine, ou nom d’hôte, que vous envisagez d’utiliser. Par exemple, pour pouvoir vous connecter avec *login.contoso.com* et *account.contoso.com*, vous devez vérifier les deux sous-domaines et pas le domaine de niveau supérieur *contoso.com*. 

    Une fois le domaine vérifié, **supprimez** l’enregistrement TXT DNS que vous avez créé.

    
## <a name="step-2-create-a-new-azure-front-door-instance"></a>Étape 2. Créer une nouvelle instance Azure Front Door

Suivez les étapes ci-dessous pour créer une Front Door pour votre locataire Azure AD B2C. Pour plus d’informations, consultez [création d’une Front Door pour votre application](../frontdoor/quickstart-create-front-door.md#create-a-front-door-for-your-application).
  

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez **Répertoire + abonnement**, puis choisissez le répertoire qui contient l’abonnement Azure que vous souhaitez utiliser pour Azure Front Door. Le répertoire ne doit *pas* être le répertoire contenant votre locataire Azure AD B2C.
1. Dans la page d’accueil ou le menu Azure, sélectionnez **Créer une ressource**. Sélectionnez **Réseau** > **Afficher tout** > **Porte d’entrée**.
1. Sous l’onglet **Général** de la page **Créer une porte d’entrée**, entrez ou sélectionnez les informations suivantes, puis sélectionnez **Suivant : Configuration**.

    | Paramètre | Valeur |
    | --- | --- |
    | **Abonnement** | Sélectionnez votre abonnement Azure. |    
    | **Groupe de ressources** | Sélectionnez un groupe de ressources existant ou **Créer** pour en créer un.|
    | **Emplacement du groupe de ressources** | sélectionnez l’emplacement du groupe de ressources. Par exemple, **USA Centre**. |

### <a name="21-add-frontend-host"></a>2.1 Ajouter un hôte frontal

L’hôte frontal est le nom de domaine que votre application utilise. Lorsque vous créez une Front Door, l’hôte frontal par défaut est un sous-domaine de `azurefd.net`.

Par commodité, Azure Front Door permet d’associer un domaine personnalisé à l’hôte frontal. Avec cette option, vous associez l’interface utilisateur Azure AD B2C à un domaine personnalisé dans votre URL au lieu d’un nom de domaine appartenant à une Front Door. Par exemple : https://login.contoso.com.

Pour ajouter un hôte frontal, procédez comme suit :

1. Dans **Front-ends/domaines**, sélectionnez **+** pour ouvrir **Ajouter l’hôte front-end**.
1. Pour **Nom d’hôte**, entrez un nom d’hôte global unique. Le nom d’hôte n’est pas votre domaine personnalisé. Cet exemple utilise *contoso-frontend*. Sélectionnez **Ajouter**.

    ![La capture d’écran montre comment ajouter un hôte frontal.](./media/custom-domain/add-frontend-host-azure-front-door.png)

### <a name="22-add-backend-and-backend-pool"></a>2.2 Ajouter un principal et un pool principal

Un principal fait référence à votre [nom de locataire Azure AD B2C](tenant-management.md#get-your-tenant-name), `tenant-name.b2clogin.com`. Pour ajouter un pool principal, procédez comme suit :

1. Toujours dans **Créer une porte d’entrée**, dans **Pools principaux**, sélectionnez **+** pour ouvrir **Ajouter un pool principal**.

1. Saisissez un **Nom**. Par exemple, *MyBackEndPool*. Sélectionnez **Ajouter un backend**.
    
    La capture d’écran suivante montre comment créer un pool principal :
    
    ![La capture d’écran montre comment ajouter un pool principal frontal.](./media/custom-domain/front-door-add-backend-pool.png)

1. Dans le panneau **Ajouter un principal**, sélectionnez les informations suivantes, puis sélectionnez **Ajouter**.

    | Paramètre | Valeur |
    | --- | --- |
    | **Type de l’hôte backend**| Sélectionnez un **hôte personnalisé**.| 
    | **Nom de l’hôte backend**| Sélectionnez le nom de votre [Azure AD B2C](tenant-management.md#get-your-tenant-name), `<tenant-name>.b2clogin.com`. Exemple : contoso.b2clogin.com.|
    | **En-tête d’hôte principal**| Sélectionnez la valeur que vous avez sélectionnée pour **Nom d’hôte principal**.|
    
    **Laissez tous les autres champs sur leurs valeurs par défaut.*
    
    La capture d’écran suivante montre comment créer un principal d’hôte personnalisé associé à un locataire Azure AD B2C :
    
    ![La capture d’écran montre comment ajouter un principal d’hôte personnalisé.](./media/custom-domain/add-a-backend.png)

1. Pour achever la configuration du pool principal, sur le panneau **Ajouter un pool principal**, sélectionnez **Ajouter**.

1. Après avoir ajouté le **serveur principal** au **pool principal**, désactivez les **sondes d’intégrité**.

    ![La capture d’écran montre comment ajouter un pool principal et désactiver les sondes d’intégrité.](./media/custom-domain/add-a-backend-pool.png)

### <a name="23-add-a-routing-rule"></a>2.3 Ajouter une règle d’acheminement

Enfin, ajoutez une règle de routage. Une règle d’acheminement mappe votre hôte frontal au pool principal. La règle transfère une demande adressée à l’[hôte frontal](#21-add-frontend-host) au [principal](#22-add-backend-and-backend-pool) Azure AD B2C. Pour ajouter une règle d’acheminement, procédez comme suit :

1. Dans **Ajouter une règle**, pour **Nom**, entrez *LocationRule*. Acceptez toutes les valeurs par défaut, puis sélectionnez **Ajouter** pour ajouter la règle de routage.
1. Sélectionnez **Vérifier + créer**, puis **Créer**.
    
     ![La capture d’écran montre comment créer une Azure Front Door.](./media/custom-domain/configuration-azure-front-door.png)


## <a name="step-3-set-up-your-custom-domain-on-azure-front-door"></a>Étape 3. Configurer votre domaine personnalisé sur Azure Front Door

Au cours de cette étape, vous allez ajouter le domaine personnalisé que vous avez inscrit à votre Front Door à l’[étape 1](#step-1-add-a-custom-domain-name-to-your-azure-ad-b2c-tenant). 

### <a name="31-create-a-cname-dns-record"></a>3.1 Créer un enregistrement DNS CNAME

Avant de pouvoir utiliser un domaine personnalisé avec votre Front Door, vous devez d’abord créer un enregistrement de nom canonique (CNAME) auprès de votre fournisseur de domaine pour pointer vers l’hôte frontend par défaut du Front Door (par exemple, contoso.azurefd.net).

Un enregistrement CNAME est un type d’enregistrement DNS qui mappe un nom de domaine source à un nom de domaine de destination (alias). Pour Azure Front Door, le nom de domaine source correspond à votre nom de domaine personnalisé, et le nom de domaine de destination correspond au nom d’hôte par défaut Front Door que vous avez configuré à l’[étape 2.1](#21-add-frontend-host). 

Une fois que Front Door a vérifié l’enregistrement CNAME que vous avez créé, le trafic adressé au domaine personnalisé source (par exemple, login.contoso.com) est acheminé vers l’hôte frontal par défaut du Front Door de destination spécifié (par exemple `contoso.azurefd.net`). Pour plus d’informations, consultez [Ajoutez un domaine personnalisé à votre Front Door](../frontdoor/front-door-custom-domain.md). 

Pour créer un enregistrement CNAME pour votre domaine personnalisé :

1. Connectez-vous au site web du fournisseur de domaine pour votre domaine personnalisé.

1. Recherchez la page permettant de gérer les enregistrements DNS en consultant la documentation du fournisseur ou en recherchant les sections du site web intitulées **Nom de domaine**, **DNS** ou **Gestion des noms de serveur**. 

1. Créez une entrée d’enregistrement CNAME pour votre domaine personnalisé et renseignez les champs comme indiqué dans le tableau suivant (les noms de champs peuvent varier) :

    | Source          | Type  | Destination           |
    |-----------------|-------|-----------------------|
    | `<login.contoso.com>` | CNAME | `contoso.azurefd.net` |

   - Source : entrez votre nom de domaine personnalisé (par exemple, login.contoso.com).

   - Tapez : entrez *CNAME*.

   - Destination : entrez l’hôte frontal Front Door par défaut que vous avez créé à l’[étape 2.1](#21-add-frontend-host). Il doit être au format suivant : _&lt;nom_hôte&gt;_ .azurefd.net. Par exemple : `contoso.azurefd.net`.

1. Enregistrez vos modifications.

### <a name="32-associate-the-custom-domain-with-your-front-door"></a>3.2 Associer le domaine personnalisé avec votre Front Door

Après avoir inscrit votre domaine personnalisé, vous pouvez l’ajouter à votre Front Door.
    
1. Dans la page **Concepteur Front Door**, sous **Frontends/domains**, sélectionnez **+** pour ajouter un domaine personnalisé.

    ![Capture d’écran montrant comment ajouter un domaine personnalisé.](./media/custom-domain/azure-front-door-add-custom-domain.png) 
    
1. Pour **Hôte frontend**, l’hôte frontend à utiliser en tant que domaine de destination de votre enregistrement CNAME est prérempli et est dérivé de votre Front Door : *&lt;nom d’hôte par défaut&gt;* .azurefd.net. Cette valeur n’est pas modifiable.

1. Pour **Nom d’hôte personnalisé**, entrez votre domaine personnalisé, y compris le sous-domaine, à utiliser comme domaine source de votre enregistrement CNAME. Par exemple, login.contoso.com.

    ![La capture d’écran montre comment vérifier un domaine personnalisé.](./media/custom-domain/azure-front-door-add-custom-domain-verification.png)

    Azure vérifie que l’enregistrement CNAME existe pour le nom de domaine personnalisé que vous avez entré. Si l'enregistrement CNAME est correct, votre domaine personnalisé sera validé.

    
1. Une fois le nom de domaine personnalisé vérifié, sous **Nom de domaine personnalisé HTTPS**, sélectionnez **Activé**. 
    
    ![Capture d’écran montrant comment activer HTTPS à l’aide d’un certificat Azure Front Door.](./media/custom-domain/azure-front-door-add-custom-domain-https-settings.png)

1. Pour le **Type de gestion de certificats**, sélectionnez [Gestion Front Door](../frontdoor/front-door-custom-domain-https.md#option-1-default-use-a-certificate-managed-by-front-door) ou [Utiliser mon propre certificat](../frontdoor/front-door-custom-domain-https.md#option-2-use-your-own-certificate). Si vous choisissez l’option *géré par Front Door*, attendez que le certificat soit entièrement approvisionné.

1. Sélectionnez **Ajouter**.

### <a name="33-update-the-routing-rule"></a>3.3 Mettre à jour de la règle d’acheminement

1. Dans **Règles d’acheminement**, sélectionnez la règle d’acheminement que vous avez créée à l’[étape 2.3](#23-add-a-routing-rule).

    ![Capture d’écran montrant comment sélectionner une règle d’acheminement.](./media/custom-domain/select-routing-rule.png)

1. Sous **Serveurs frontaux/domaines**, sélectionnez votre nom de domaine personnalisé.
    
    ![Capture d’écran montrant comment mettre à jour la règle d’acheminement Azure Front Door.](./media/custom-domain/update-routing-rule.png)

1. Sélectionnez **Mettre à jour**.
1. Dans la fenêtre principale, sélectionnez **Enregistrer**.

## <a name="step-4-configure-cors"></a>Étape 4. Configuration de CORS

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

## <a name="test-your-custom-domain"></a>Tester votre domaine personnalisé

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Stratégies**, sélectionnez **Flux utilisateur (stratégies)** .
1. Sélectionnez un flux d’utilisateur, puis sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *webapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Copiez l’URL sous **Exécuter le point de terminaison du flux d’utilisateur**.

    ![Capture d’écran montrant comment copier l’URI de demande d’autorisation.](./media/custom-domain/user-flow-run-now.png)

1. Pour simuler une connexion avec votre domaine personnalisé, ouvrez un navigateur web et utilisez l’URL que vous avez copiée. Remplacez le domaine Azure AD B2C ( _&lt;nom de locataire&gt;_ .b2clogin.com) par votre domaine personnalisé.   

    Par exemple, à la place de :

    ```http
    https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login
    ```

    utiliser :

    ```http
    https://login.contoso.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login    
    ```

1. Vérifiez que l’Azure AD B2C est chargé correctement. Ensuite, connectez-vous à l’aide d’un compte local.
1. Répétez le test avec vos stratégies restantes.

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

- Remplacez **&lt;nom de domaine personnalisé&gt;** par le nom de votre domaine personnalisé.
- Remplacez **&lt;nom de locataire&gt;** par le nom de votre locataire ou votre ID de locataire.

L’exemple suivant montre un URI de redirection OAuth valide :

```http
https://login.contoso.com/contoso.onmicrosoft.com/oauth2/authresp
```

Si vous choisissez d’utiliser l’[ID de locataire](#optional-use-tenant-id), un URI de redirection OAuth valide ressemble à l’exemple suivant :

```http
https://login.contoso.com/11111111-1111-1111-1111-111111111111/oauth2/authresp
```

Les métadonnées des [fournisseurs d’identité SAML](saml-identity-provider-technical-profile.md) se présentent comme l’exemple suivant :

```http
https://<custom-domain-name>.b2clogin.com/<tenant-name>/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

## <a name="configure-your-application"></a>Configuration de votre application 

Après avoir configuré et testé le domaine personnalisé, vous pouvez mettre à jour vos applications pour charger l’URL qui spécifie votre domaine personnalisé comme nom d’hôte au lieu du domaine Azure AD B2C. 

L’intégration de domaine personnalisé s’applique aux points de terminaison d’authentification qui utilisent des stratégies Azure AD B2C (flux d’utilisateur ou stratégies personnalisées) pour authentifier les utilisateurs. Ces points de terminaison peuvent ressembler à l’exemple suivant :

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/v2.0/.well-known/openid-configuration</code>

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://\<custom-domain\>/<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

Remplacez :
- **custom-domain** par votre domaine personnalisé
- **tenant-name** par le nom ou l’ID de votre locataire
- **your-policy** par le nom de votre stratégie. [En savoir plus sur les stratégies Azure AD B2C](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 


Les métadonnées du [fournisseur de services SAML](./saml-service-provider.md) peuvent ressembler à l’exemple suivant : 

```html
https://custom-domain-name/tenant-name/policy-name/Samlp/metadata
```

### <a name="optional-use-tenant-id"></a>(Facultatif) Utiliser l’ID de locataire

Vous pouvez remplacer le nom de votre locataire B2C dans l’URL par le GUID d’ID de votre locataire pour supprimer toutes les références à « b2c » dans l’URL. Vous trouverez le GUID d’ID de votre locataire dans la page Vue d’ensemble de B2C de le portail Azure.
Par exemple, remplacez `https://account.contosobank.co.uk/contosobank.onmicrosoft.com/` par `https://account.contosobank.co.uk/<tenant ID GUID>/`

Si vous choisissez d’utiliser l’ID de locataire au lieu du nom de locataire, veillez à mettre à jour les **URI de redirection OAuth** du fournisseur d’identité en conséquence. Pour plus d’informations, consultez [Configurer votre fournisseur d'identité](#configure-your-identity-provider).

### <a name="token-issuance"></a>Émission du jeton

Le nom de l’émetteur de jeton (ISS) demande des modifications en fonction du domaine personnalisé utilisé. Par exemple :

```http
https://<domain-name>/11111111-1111-1111-1111-111111111111/v2.0/
```

::: zone pivot="b2c-custom-policy"

## <a name="block-access-to-the-default-domain-name"></a>Bloquer l’accès au nom de domaine par défaut

Une fois que vous avez ajouté le domaine personnalisé et configuré votre application, les utilisateurs peuvent toujours accéder au domaine &lt;nom de locataire&gt;. b2clogin.com. Pour empêcher l’accès, vous pouvez configurer la stratégie pour vérifier la demande d’autorisation « nom d’hôte » par rapport à une liste de domaines autorisés. Le nom d’hôte est le nom de domaine qui apparaît dans l’URL. Le nom d’hôte est disponible via des `{Context:HostName}` [programmes de résolution de revendications](claim-resolver-overview.md). Vous pouvez ensuite présenter un message d’erreur personnalisé. 

1. Obtenez l’exemple d’une stratégie d’accès conditionnel qui vérifie le nom d’hôte à partir de [GitHub](https://github.com/azure-ad-b2c/samples/blob/master/policies/check-host-name).
1. Dans chaque fichier, remplacez la chaîne `yourtenant` par le nom de votre locataire Azure AD B2C. Par exemple, si le nom de votre locataire B2C est *contosob2c*, toutes les instances de `yourtenant.onmicrosoft.com` deviennent `contosob2c.onmicrosoft.com`.
1. Téléchargez les fichiers de stratégie dans l'ordre suivant : `B2C_1A_TrustFrameworkExtensions_HostName.xml` puis `B2C_1A_signup_signin_HostName.xml`.

::: zone-end

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="azure-ad-b2c-returns-a-page-not-found-error"></a>Azure AD B2C renvoie une erreur de page introuvable

- **Symptôme** : après avoir configuré un domaine personnalisé, lorsque vous essayez de vous connecter avec le domaine personnalisé, vous recevez un message d’erreur HTTP 404.
- **Causes possibles** : ce problème peut être lié à la configuration DNS ou à la configuration du serveur principal Azure Front Door. 
- **Résolution** :  
    1. Assurez-vous que le domaine personnalisé est [inscrit et vérifié avec succès](#step-1-add-a-custom-domain-name-to-your-azure-ad-b2c-tenant) dans votre locataire Azure AD B2C.
    1. Assurez-vous que le [domaine personnalisé](../frontdoor/front-door-custom-domain.md) est correctement configuré. L'enregistrement `CNAME` de votre domaine personnalisé doit pointer vers l’hôte frontal par défaut d’Azure Front Door (par exemple, contoso.azurefd.net).
    1. Assurez-vous que la [configuration du pool principal Azure Front Door](#22-add-backend-and-backend-pool) pointe vers le locataire dans lequel vous configurez le nom de domaine personnalisé, ainsi que l’emplacement de stockage de votre flux d’utilisateur ou de vos stratégies personnalisées.


### <a name="azure-ad-b2c-returns-the-resource-you-are-looking-for-has-been-removed-had-its-name-changed-or-is-temporarily-unavailable"></a>Azure AD B2C renvoie un message indiquant que la ressource que vous recherchez a été supprimée, que son nom a changé ou qu’elle est temporairement indisponible.

- **Symptôme** – Après avoir configuré un domaine personnalisé, lorsque vous essayez de vous connecter avec le domaine personnalisé, vous recevez le message d'erreur *La ressource que vous recherchez a été supprimée, son nom a été modifié ou elle est temporairement indisponible*.
- **Causes possibles** – Ce problème peut être lié à la vérification de domaine personnalisé Azure AD. 
- **Résolution** – Assurez-vous que le domaine personnalisé est [inscrit et **vérifié avec succès**](#step-1-add-a-custom-domain-name-to-your-azure-ad-b2c-tenant) dans votre locataire Azure AD B2C.

### <a name="identify-provider-returns-an-error"></a>Le fournisseur d’identité retourne une erreur

- **Symptôme** : après avoir configuré un domaine personnalisé, vous êtes en mesure de vous connecter avec des comptes locaux. Toutefois, lorsque vous vous connectez avec des informations d’identification de [fournisseurs d’identité sociale ou d’entreprise](add-identity-provider.md) externes, le fournisseur d’identité présente un message d’erreur.
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

Pour utiliser votre propre pare-feu d’applications web devant Azure Front Door, vous devez configurer et vérifier que tout fonctionne correctement avec vos flux d’utilisateurs Azure AD B2C ou stratégies personnalisées.  

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [requêtes d’autorisation OAuth](protocols-overview.md).
