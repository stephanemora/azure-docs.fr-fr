---
title: Proxy REST Apache Kafka - Azure HDInsight
description: Découvrez comment effectuer des opérations Apache Kafka à l’aide d’un proxy REST Kafka sur Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 265e15713f8159e370ef22a197ffe931200a88f7
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759005"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interagir avec des clusters Apache Kafka dans Azure HDInsight à l’aide d’un proxy REST

Le proxy REST Kafka vous permet d’interagir avec votre cluster Kafka via une API REST sur HTTP. Cette action signifie que vos clients Kafka peuvent se trouver en dehors du réseau virtuel. Les clients peuvent effectuer des appels HTTP simples au cluster Kafka, au lieu de s’appuyer sur des bibliothèques Kafka. Cet article montre comment créer un cluster Kafka avec un proxy REST activé. Il contient également un exemple de code qui montre comment effectuer des appels au proxy REST.

## <a name="rest-api-reference"></a>Référence d’API REST

Pour plus d’informations sur les opérations prises en charge par l’API REST Kafka, consultez la [référence de l’API de proxy REST HDInsight Kafka](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Arrière-plan

![Conception du proxy REST Kafka](./media/rest-proxy/rest-proxy-architecture.png)

Pour obtenir la spécification complète des opérations prises en charge par l’API, consultez [API de proxy REST Apache Kafka](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>Point de terminaison de proxy REST

La création d’un cluster HDInsight Kafka avec un proxy REST entraîne la création d’un point de terminaison public pour votre cluster. Celui-ci apparaît dans les **Propriétés** de votre cluster HDInsight dans le portail Azure.

### <a name="security"></a>Sécurité

L’accès au proxy REST Kafka est géré avec des groupes de sécurité Azure Active Directory. Quand vous créez le cluster Kafka, spécifiez le groupe de sécurité Azure AD avec un accès au point de terminaison REST. Les clients Kafka qui ont besoin d’accéder au proxy REST doivent être inscrits auprès de ce groupe par le propriétaire du groupe, par le biais du portail ou de PowerShell.

Pour les requêtes de point de terminaison de proxy REST, les applications clientes doivent obtenir un jeton OAuth. Le jeton sert à vérifier l’appartenance au groupe de sécurité. Vous trouverez ci-dessous un [exemple d’application cliente](#client-application-sample) qui montre comment obtenir un jeton OAuth. L’application cliente transmet le jeton OAuth dans la requête HTTP au proxy REST.

> [!NOTE]  
> Consultez [Gérer l’accès aux applications et aux ressources à l’aide de groupes Azure Active Directory](../../active-directory/fundamentals/active-directory-manage-groups.md) pour en savoir plus sur les groupes de sécurité AAD. Pour plus d’informations sur le fonctionnement des jetons OAuth, consultez [Autoriser l’accès aux applications web Azure Active Directory à l’aide du flux d’octroi de code OAuth 2.0](../../active-directory/develop/v1-protocols-oauth-code.md).

## <a name="prerequisites"></a>Prérequis

1. inscrivez une application auprès d’Azure AD ; Les applications clientes que vous écrivez pour interagir avec le proxy REST Kafka utilisent l’identificateur et le secret de cette application pour s’authentifier auprès d’Azure.

1. Créez un groupe de sécurité Azure AD. Ajoutez l’application que vous avez inscrite auprès d’Azure AD au groupe de sécurité en tant que **membre** du groupe. Ce groupe de sécurité sera utilisé pour contrôler les applications qui sont autorisées à interagir avec le proxy REST. Pour plus d’informations sur la création de groupes Azure AD, consultez [Créer un groupe de base et ajouter des membres avec Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Confirmez que le groupe est de type **Sécurité**.
    ![Groupe de sécurité](./media/rest-proxy/rest-proxy-group.png)

    Confirmez que l’application est membre du groupe.
    ![Vérifier l’appartenance](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Créer un cluster Kafka avec le proxy REST activé

1. Pendant le workflow de création du cluster Kafka, sous l’onglet **Sécurité et réseau**, activez l’option **Activer le proxy REST Kafka**.

     ![Activer le proxy REST Kafka et sélectionner le groupe de sécurité](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Cliquez sur **Sélectionner un groupe de sécurité**. Dans la liste des groupes de sécurité, sélectionnez le groupe de sécurité qui doit avoir accès au proxy REST. Vous pouvez utiliser la zone de recherche pour retrouver le groupe de sécurité approprié. Cliquez sur le bouton **Sélectionner** en bas.

     ![Activer le proxy REST Kafka et sélectionner le groupe de sécurité](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Effectuez les étapes restantes pour créer votre cluster, comme décrit dans [Créer un cluster Apache Kafka dans Azure HDInsight à l’aide du portail Azure](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. Une fois le cluster créé, accédez aux propriétés du cluster pour enregistrer l’URL du proxy REST Kafka.

     ![afficher l’URL du proxy REST](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Exemple d’application cliente

Vous pouvez utiliser le code Python ci-dessous pour interagir avec le proxy REST sur votre cluster Kafka. Pour exécuter l’exemple de code, procédez comme suit :

1. Enregistrez l’exemple de code sur un ordinateur sur lequel Python est installé.
1. Installez les dépendances Python requises en exécutant `pip3 install msal`.
1. Modifiez la section de code **Configure these properties** et mettez à jour les propriétés suivantes pour votre environnement :

    |Propriété |Description |
    |---|---|
    |ID client|Locataire Azure où se trouve votre abonnement.|
    |ID client|ID de l’application que vous avez inscrite dans le groupe de sécurité.|
    |Clé secrète client|Secret de l’application que vous avez inscrite dans le groupe de sécurité.|
    |Kafkarest_endpoint|Récupérez cette valeur sous l’onglet **Propriétés** de la vue d’ensemble du cluster, comme décrit dans la [section de déploiement](#create-a-kafka-cluster-with-rest-proxy-enabled). Elle doit respecter le format suivant : `https://<clustername>-kafkarest.azurehdinsight.net`|

1. Depuis la ligne de commande, exécutez le fichier Python en exécutant `sudo python3 <filename.py>`

Ce code effectue l’action suivante :

1. Il récupère un jeton OAuth à partir d’Azure AD.
1. Il montre comment effectuer une requête auprès du proxy REST Kafka.

Pour plus d’informations sur l’obtention de jetons OAuth dans Python, consultez [Classe AuthenticationContext Python](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Vous pouvez constater un délai avant l’affichage ici des `topics` qui ne sont pas créées ou supprimées par le biais du proxy REST Kafka. Ce délai est dû à l’actualisation du cache.

```python
#Required python packages
#pip3 install msal

import msal

#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
    )

# The pattern to acquire a token looks like this.
result = None

result = app.acquire_token_for_client(scopes=[scope])

print(result)
accessToken = result['access_token']

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

Vous trouverez ci-dessous un autre exemple illustrant comment obtenir un jeton à partir d’Azure pour le proxy REST à l’aide d’une commande curl. **Notez que nous avons besoin du `scope=https://hib.azurehdinsight.net/.default` spécifié lors de l’obtention d’un jeton.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Étapes suivantes

* [Documents d’informations de référence de l’API Proxy REST Kafka](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
