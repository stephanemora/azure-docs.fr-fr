---
title: Déployer et configurer le Pare-feu Azure Premium - Préversion
description: Apprenez à déployer et configurer le Pare-feu Azure Premium.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: fa106fac683619706f4be330ad1c4bff7b56f2dd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721784"
---
# <a name="deploy-and-configure-azure-firewall-premium-preview"></a>Déployer et configurer le Pare-feu Azure Premium - Préversion

> [!IMPORTANT]
> Pare-feu Azure Premium est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Le Pare-feu Azure Premium - Préversion est un pare-feu de nouvelle génération avec des fonctionnalités qui sont nécessaires pour les environnements hautement sensibles et réglementés. Microsoft System Center ASP.NET 2.0 2007 offre les fonctionnalités suivantes :

- **Inspection TLS** : déchiffre le trafic sortant, traite les données, puis les chiffre et les transmet à la destination.
- **IDPS** : un système IDPS (Intrusion Detection and Prevention System) vous permet de surveiller les activités malveillantes, de consigner des informations sur ces activités, de les signaler, voire de les bloquer.
- **Filtrage d’URL** : étend la fonctionnalité de filtrage de nom de domaine complet du Pare-feu Azure pour prendre en compte une URL entière. Par exemple, `www.contoso.com/a/c` plutôt que `www.contoso.com`.
- **Catégories web** : permettent aux administrateurs d’autoriser ou de refuser aux utilisateurs l’accès aux catégories de sites web telles que les sites web de jeux d’argent, les sites web de réseaux sociaux, etc.

Pour plus d’informations, consultez [Fonctionnalités du Pare-feu Azure Premium](premium-features.md).

Vous utiliserez un modèle pour déployer un environnement de test doté d’un réseau virtuel central (10.0.0.0/16) avec trois sous-réseaux :
- sous-réseau worker (10.0.10.0/24)
- sous-réseau Azure Bastion (10.0.20.0/24)
- sous-réseau de pare-feu (10.0.100.0/24)

Pour des raisons de simplicité, un réseau virtuel central unique est utilisé dans cet environnement de test. À des fins de production, une [topologie hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) avec réseaux virtuels appairés est plus courante.

:::image type="content" source="media/premium-deploy/premium-topology.png" alt-text="Topologie de réseau virtuel central":::

La machine virtuelle worker est un client qui envoie des requêtes HTTP/S via le pare-feu.

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="deploy-the-infrastructure"></a>Déployer l’infrastructure

Le modèle déploie un environnement de test complet pour le Pare-feu Azure Premium compatible avec le système IDPS, l’inspection TLS, le filtrage d’URL et les catégories web :

- nouveau Pare-feu Premium et une nouvelle stratégie de pare-feu avec paramètres prédéfinis pour faciliter la validation de ses fonctionnalités principales (système IDPS, inspection TLS, filtrage d’URL et catégories web)
- déploie toutes les dépendances, y compris Key Vault et une identité managée. Dans un environnement de production, ces ressources peuvent être déjà créées et ne pas s’avérer nécessaires dans le même modèle.
- génère une autorité de certification racine auto-signée et la déploie sur le Key Vault généré
-  génère une autorité de certification intermédiaire dérivée et la déploie sur une machine virtuelle de test Windows (WorkerVM)
- un hôte bastion (BastionHost) est également déployé et peut être utilisé pour se connecter à la machine de test Windows (WorkerVM)



[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-premium%2Fazuredeploy.json)

## <a name="test-the-firewall"></a>Tester le pare-feu

Vous pouvez maintenant tester le système IDPS, l’inspection TLS, le filtrage web et les catégories web.

### <a name="add-firewall-diagnostics-settings"></a>Ajouter des paramètres de diagnostic de pare-feu

Pour collecter les journaux du pare-feu, vous devez ajouter des paramètres de diagnostic.

1. Sélectionnez **DemoFirewall** et sous **Surveillance**, sélectionnez **Paramètres de diagnostic**.
2. Sélectionnez **Ajouter le paramètre de diagnostic**.
3. Pour **Nom du paramètre de diagnostic**, entrez *fw-diag*.
4. Sous **Journal**, sélectionnez **AzureFirewallApplicationRule** et **AzureFirewallNetworkRule**.
5. Sous **Détails de la destination**, sélectionnez **Envoyer à l’espace de travail Log Analytics**.
6. Sélectionnez **Enregistrer**.

### <a name="idps-tests"></a>Tests IDPS

Pour tester le système IDPS, vous devez déployer votre propre serveur web interne avec un certificat de serveur approprié. Pour plus d’informations sur les conditions requises pour les certificats du Pare-feu Azure Premium - Préversion, consultez [Certificats du Pare-feu Azure Premium - Préversion](premium-certificates.md).

Vous pouvez utiliser `curl` pour contrôler différents en-têtes HTTP et simuler le trafic malveillant.

#### <a name="to-test-idps-for-http-traffic"></a>Pour tester le système IDPS pour le trafic HTTP :

1. Sur la machine virtuelle WorkerVM, ouvrez une fenêtre d’invite de commandes d’administrateur.
2. Entrez la commande suivante à l'invite de commandes :

   `curl -A "BlackSun" <your web server address>`
3. Vous voyez la réponse de votre serveur web.
4. Accédez aux journaux des règles de réseau du pare-feu sur le portail Azure pour trouver une alerte similaire au message suivant :

   :::image type="content" source="media/premium-deploy/alert-message.png" alt-text="Message d’alerte":::

   > [!NOTE]
   > L’affichage des données dans les journaux peut prendre un certain temps. Comptez au moins 20 minutes pour que les journaux commencent à afficher les données.
5. Ajouter une règle de signature pour la signature 2008983 :

   1. Sélectionnez **DemoFirewallPolicy** et sous **Paramètres**, sélectionnez **IDPS (préversion)** .
   1. Sélectionnez l’onglet **Règles de signature**.
   1. Sous **ID de signature**, dans la zone de texte, entrez *2008983*.
   1. Sous **Mode**, sélectionnez **Refuser**.
   1. Sélectionnez **Enregistrer**.
   1. Attendez la fin du déploiement avant de poursuivre.



6. Sur WorkerVM, réexécutez la commande `curl` :

   `curl -A "BlackSun" <your web server address>`

   La requête HTTP étant maintenant bloquée par le pare-feu, la sortie suivante s’affiche après l’expiration du délai de connexion :

   `read tcp 10.0.100.5:55734->10.0.20.10:80: read: connection reset by peer`

7. Accédez aux journaux Monitor dans le portail Azure et recherchez le message de la requête bloquée.
<!---8. Now you can bypass the IDPS function using the **Bypass list**.

   1. On the **IDPS (preview)** page, select the **Bypass list** tab.
   2. Edit **MyRule** and set **Destination** to *10.0.20.10, which is the ServerVM private IP address.
   3. Select **Save**.
1. Run the test again: `curl -A "BlackSun" http://server.2020-private-preview.com` and now you should get the `Hello World` response and no log alert. --->

#### <a name="to-test-idps-for-https-traffic"></a>Pour tester le système IDPS pour le trafic HTTPS

Répétez ces tests curl à l’aide du protocole HTTPS plutôt que HTTP. Par exemple :

`curl --ssl-no-revoke -A "BlackSun" <your web server address>`

Vous devez voir les mêmes résultats que ceux des tests HTTP.

### <a name="tls-inspection-with-url-filtering"></a>Inspection TLS avec filtrage d’URL

Pour tester l’inspection TLS avec le filtrage d’URL, procédez comme suit.

1. Modifiez les règles d’application de la stratégie de pare-feu et ajoutez une nouvelle règle appelée `AllowURL` à la collection de règles `AllowWeb`. Configurez l’URL cible `www.nytimes.com/section/world`, l’adresse IP source **\* *_, le type de destination _* URL (préversion)** , sélectionnez **Inspection TLS (préversion)** et les protocoles **http, https**.

3. Une fois le déploiement terminé, ouvrez un navigateur sur WorkerVM et accédez à `https://www.nytimes.com/section/world`, puis vérifiez que la réponse HTML s’affiche comme prévu dans le navigateur.
4. Dans le portail Azure, vous pouvez afficher l’URL complète dans les journaux de surveillance des règles d’application :

      :::image type="content" source="media/premium-deploy/alert-message-url.png" alt-text="Message d’alerte affichant l’URL":::

Certaines pages HTML peuvent paraître incomplètes, car elles font référence à d’autres URL refusées. Pour y remédier, vous pouvez adopter l’approche suivante :

- Si la page HTML contient des liens vers d’autres domaines, vous pouvez ajouter ces domaines à une nouvelle règle d’application avec autorisation d’accès à ces noms de domaine complets.
- Si la page HTML contient des liens vers des URL secondaires, vous pouvez modifier la règle et ajouter un astérisque à l’URL. Par exemple : `targetURLs=www.nytimes.com/section/world*`

   Vous pouvez également ajouter une nouvelle URL à la règle. Par exemple : 

   `www.nytimes.com/section/world, www.nytimes.com/section/world/*`

### <a name="web-categories-testing"></a>Test des catégories web

Nous allons créer une règle d’application pour autoriser l’accès aux sites web sportifs.
1. À partir du portail, ouvrez votre groupe de ressources et sélectionnez **DemoFirewallPolicy**.
2. Sélectionnez **Règles d’application**, puis **Ajouter une collection de règles**.
3. Pour **Nom**, entrez *GeneralWeb*, **Priorité** *103*, **Groupe de collection de règles**, sélectionnez **DefaultApplicationRuleCollectionGroup**.
4. Sous **Règles** pour **Nom**, entrez *AllowSports*, **Source** *\** , **Protocole** *http, https*, sélectionnez **Inspection TLS**, **Type de destination** sélectionnez *Catégories web (préversion)* , **Destination** sélectionnez *Sports*.
5. Sélectionnez **Ajouter**.

      :::image type="content" source="media/premium-deploy/web-categories.png" alt-text="Catégorie web Sports":::
6. Une fois le déploiement terminé, accédez à **WorkerVM**, ouvrez un navigateur web et accédez à `https://www.nfl.com`.

   Vous devriez voir la page web NFL, et le journal des règles d’application indique qu’une règle **Catégorie web : Sports** a été mise en correspondance et que la demande a été autorisée.

## <a name="next-steps"></a>Étapes suivantes

- [Pare-feu Azure Premium - Préversion dans le portail Azure](premium-portal.md)