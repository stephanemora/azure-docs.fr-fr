---
title: Créer une porte d’entrée avec redirection de HTTP vers HTTPS à l’aide du portail Microsoft Azure
description: Apprenez à créer une porte d’entrée avec redirection du trafic de HTTP vers HTTPS à l’aide du portail Microsoft Azure.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: how-to
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 05699c827af251b890de4c6f195df5872bfbe364
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84743607"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Créer une porte d’entrée avec redirection de HTTP vers HTTPS à l’aide du portail Microsoft Azure

Vous pouvez utiliser le portail Azure pour créer une [porte d’entrée](front-door-overview.md) avec un certificat pour la terminaison TLS. Une règle d’acheminement est utilisée pour rediriger le trafic HTTP vers HTTPS.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer une porte d’entrée avec une ressource d’application web existante
> * Ajouter un domaine personnalisé à l’aide d’un certificat TLS/SSL 
> * Configurer la redirection HTTPS sur le domaine personnalisé

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Créer une porte d’entrée avec une ressource d’application web existante

1. Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).
2. Cliquez sur  **Créer une ressource** dans le coin supérieur gauche du portail Azure.
3. Recherchez la **porte d’entrée** à l’aide de la barre de recherche. Après avoir trouvé le type de ressource, cliquez sur **Créer**.
4. Choisissez un abonnement, puis utilisez un groupe de ressources existant ou créez-en un. Notez que l’emplacement demandé dans l’interface utilisateur est pour le groupe de ressources uniquement. Votre configuration de porte d’entrée va être déployée sur l’ensemble des [emplacements POP d’Azure Front Door](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door).

    ![Configurer les paramètres de base pour la nouvelle porte d’entrée](./media/front-door-url-redirect/front-door-create-basics.png)

5. Cliquez sur **Suivant** pour accéder à l’onglet de configuration. La configuration de la porte d’entrée se déroule en trois étapes : l’ajout d’un hôte frontend par défaut, l’ajout de back ends dans un pool de back ends et la création de règles d’acheminement pour mapper le comportement d’acheminement à l’hôte frontend. 

     ![Concepteur de configuration de porte d’entrée](./media/front-door-url-redirect/front-door-designer.png)

6. Cliquez sur l’icône « **+** » sur les _hôtes frontend_ pour créer un hôte frontend. Entrez un nom global unique pour l’hôte frontend par défaut de votre porte d’entrée (`\<**name**\>.azurefd.net`). Cliquez sur **Ajouter** pour passer à l’étape suivante.

     ![Ajouter un hôte frontend](./media/front-door-url-redirect/front-door-create-fehost.png)

7. Cliquez sur l’icône « **+** » sur les _pools de back ends_ pour créer un pool de back ends. Fournissez un nom pour le pool de back ends, puis cliquez sur « **Ajouter un back end** ».
8. Sélectionnez le type d’hôte back end en tant que _service d’application_. Sélectionnez l’abonnement dans lequel votre application web est hébergée, puis sélectionnez l’application web spécifique dans la liste déroulante pour **Nom de l’hôte back end**.
9. Cliquez sur **Ajouter** pour enregistrer le back end et cliquez de nouveau sur **Ajouter** pour enregistrer la configuration du pool de back ends.   ![Ajouter un back end dans un pool de back ends](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. Cliquez sur l’icône « **+** » sur les _règles d’acheminement_ pour créer un itinéraire. Fournissez un nom pour l’itinéraire, par exemple « HttpToHttpsRedirect », puis définissez le champ _Accepted Protocols_ (Protocoles acceptés) sur **« HTTP uniquement »** . Vérifiez que _l’hôte frontend_ approprié est sélectionné.  
11. Dans la section _Détails de la route_, définissez le _type de route_ sur **Rediriger** ; vérifiez que le _type de redirection_ est défini sur **Trouvé (302)** et que _Protocole de redirection_ est défini sur **HTTPS uniquement**. 
12. Cliquez sur Ajouter pour enregistrer la règle d’acheminement pour la redirection HTTP vers HTTPS.
     ![Ajouter un itinéraire de redirection HTTP vers HTTPS](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. Ajoutez une autre règle d’acheminement pour gérer le trafic HTTPS. Cliquez sur le signe « **+** » sur les _règles de routage_ et fournissez un nom pour l’itinéraire, par exemple « DefaultForwardingRoute », puis définissez le champ _Accepted Protocols_ (Protocoles acceptés) sur **« HTTPS uniquement »** . Vérifiez que _l’hôte frontend_ approprié est sélectionné.
14. Dans la section Détails de la route, définissez le _type de route_ sur **Transférer** ; vérifiez que le pool de back ends approprié est sélectionné et que le _protocole de transfert_ est défini sur **HTTPS uniquement**. 
15. Cliquez sur Ajouter pour enregistrer la règle d’acheminement pour le transfert de requêtes.
     ![Ajouter un itinéraire de transfert pour le trafic HTTPS](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. Cliquez sur **Vérifier + créer**, puis sur **Créer** pour créer votre profil de porte d’entrée. Accédez à la ressource après sa création.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Ajouter un domaine personnalisé à votre porte d’entrée et activer HTTPS dessus
Les étapes suivantes illustrent comment vous pouvez ajouter un domaine personnalisé sur une ressource de porte d’entrée existante, puis activer la redirection HTTP vers HTTPS sur celle-ci. 

### <a name="add-a-custom-domain"></a>Ajouter un domaine personnalisé

Dans cet exemple, vous ajoutez un enregistrement CNAME pour le sous-domaine `www` (`www.contosonews.com`, par exemple).

#### <a name="create-the-cname-record"></a>Créer un enregistrement CNAME

Ajoutez un enregistrement CNAME pour mapper un sous-domaine à votre hôte frontend de porte d’entrée par défaut (`<name>.azurefd.net`, où `<name>` est le nom du profil de votre porte d’entrée).

Pour l’exemple de domaine `www.contoso.com`, ajoutez un enregistrement CNAME qui mappe le nom `www` à `<name>.azurefd.net`.

Après avoir ajouté l’enregistrement CNAME, la page d’enregistrements DNS ressemble à l’exemple suivant :

![Domaine personnalisé CNAME sur la porte d’entrée](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>Intégrer le domaine personnalisé à votre Front Door

1. Dans l’onglet du concepteur de Front Door, cliquez sur l’icône « + » dans la section des hôtes frontend pour ajouter un nouveau domaine personnalisé. 
2. Entrez le nom DNS personnalisé complet dans le champ de nom d’hôte personnalisé, par exemple `www.contosonews.com`. 
3. Une fois le mappage CNAME du domaine à votre Front Door validé, cliquez sur **Ajouter** pour ajouter le domaine personnalisé.
4. Cliquez sur **Enregistrer** pour envoyer les modifications.

![Menu Domaines personnalisés](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>Activer HTTPS sur votre domaine personnalisé

1. Cliquez sur le domaine personnalisé qui a été ajouté puis, sous la section **HTTPS sur un domaine personnalisé**, modifiez l’état sur **Activé**.
2. Vous pouvez laisser le **type de gestion de certificats** défini sur _Porte d’entrée managée_ pour le certificat gratuit conservé, géré et avec rotation automatique par la porte d’entrée. Vous pouvez également choisir d’utiliser votre propre certificat TLS/SSL personnalisé stocké avec Azure Key Vault. Ce didacticiel part du principe que le certificat géré de porte d’entrée est utilisé.
![Activation de HTTPS pour un domaine personnalisé](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. Cliquez sur **Mettre à jour** pour enregistrer la sélection, puis cliquez sur **Enregistrer**.
4. Après quelques minutes, cliquez sur **Actualiser**, puis cliquez une nouvelle fois sur le domaine personnalisé pour voir la progression de l’approvisionnement de certificats. 

> [!WARNING]
> L’activation de HTTPS pour un domaine personnalisé peut prendre plusieurs minutes, et dépend également de la validation de la propriété de domaine si l’enregistrement CNAME n’est pas mappé directement à votre hôte de porte d’entrée `<name>.azurefd.net`. Découvrez plus en détail [comment activer le protocole HTTPS pour un domaine personnalisé](./front-door-custom-domain-https.md).

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>Configurer les règles d’acheminement pour le domaine personnalisé

1. Cliquez sur la règle d’acheminement de redirection créée précédemment.
2. Cliquez sur la liste déroulante des hôtes frontend et sélectionnez votre domaine personnalisé pour appliquer également cet itinéraire à votre domaine.
3. Cliquez sur **Update**.
4. Effectuez la même opération pour l’autre règle d’acheminement, autrement dit, pour votre itinéraire de transfert pour ajouter le domaine personnalisé.
5. Cliquez sur **Enregistrer** pour envoyer les modifications.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).
- Découvrez [comment fonctionne Front Door](front-door-routing-architecture.md).
- Découvrez plus en détail la [redirection d’URL sur la porte d’entrée](front-door-url-redirect.md).
