---
title: Proxy REST Apache Kafka - Azure HDInsight
description: Découvrez comment effectuer des opérations Apache Kafka à l’aide d’un proxy REST Kafka sur Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: a64d03ebe7c8bbb4cfa9c7bd63a678892250373d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75475871"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interagir avec des clusters Apache Kafka dans Azure HDInsight à l’aide d’un proxy REST

Le proxy REST Kafka vous permet d’interagir avec votre cluster Kafka via une API REST sur HTTP. Cela signifie que vos clients Kafka peuvent se trouver en dehors du réseau virtuel. En outre, les clients peuvent effectuer des appels HTTP simples pour envoyer et recevoir des messages au/du cluster Kafka, au lieu de s’appuyer sur des bibliothèques Kafka.  

## <a name="background"></a>Arrière-plan

### <a name="architecture"></a>Architecture

Sans proxy REST, les clients Kafka doivent se trouver dans le même réseau virtuel que le cluster Kafka ou dans un réseau virtuel homologué. Le proxy REST vous permet de connecter des producteurs ou consommateurs de données où qu’ils se trouvent. Le déploiement du proxy REST crée un nouveau point de terminaison public pour votre cluster, que vous pouvez retrouver dans les paramètres de votre portail.

Pour obtenir la spécification complète des opérations prises en charge par l’API, consultez l’[API proxy REST Apache Kafka](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="security"></a>Sécurité

L’accès au proxy REST Kafka est géré avec des groupes de sécurité Azure Active Directory. Consultez [Gérer l’accès aux applications et aux ressources à l’aide de groupes Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups) pour plus d’informations.

Lors de la création du cluster Kafka avec le proxy REST activé, vous indiquez le groupe de sécurité AAD qui doit avoir accès au point de terminaison REST. Les clients (applications) Kafka qui ont besoin d’accéder au proxy REST doivent être inscrits auprès de ce groupe par le propriétaire du groupe. Le propriétaire du groupe peut le faire via le portail ou PowerShell.

Avant d’envoyer des requêtes au point de terminaison de proxy REST, l’application cliente doit obtenir un jeton OAuth pour vérifier l’appartenance au groupe de sécurité approprié. Pour plus d’informations sur le fonctionnement des jetons OAuth, consultez [Autoriser l’accès aux applications web Azure Active Directory à l’aide du flux d’octroi de code OAuth 2.0](../../active-directory/develop/v1-protocols-oauth-code.md). Pour obtenir un exemple de récupération (fetch) d’un jeton OAuth dans Python, consultez [Exemple d’application cliente](#client-application-sample)

Une fois que l’application cliente dispose du jeton OAuth, elle doit passer ce jeton dans la requête HTTP envoyée au proxy REST.

## <a name="prerequisites"></a>Conditions préalables requises

1. inscrivez une application auprès d’Azure AD ; Les applications clientes que vous écrivez pour interagir avec le proxy REST Kafka utilisent l’identificateur et le secret de cette application pour s’authentifier auprès d’Azure.
1. Créez un groupe de sécurité Azure AD et ajoutez l’application que vous avez inscrite auprès d’Azure AD au groupe de sécurité. Ce groupe de sécurité sera utilisé pour contrôler les applications qui sont autorisées à interagir avec le proxy REST. Pour plus d’informations sur la création de groupes Azure AD, consultez [Créer un groupe de base et ajouter des membres avec Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Créer un cluster Kafka avec le proxy REST activé

1. Pendant le flux de travail de création du cluster Kafka, sous l’onglet « Security + networking » (Sécurité et mise en réseau), activez l’option « Enable Kafka REST proxy » (Activer le proxy REST Kafka).

     ![Activer le proxy REST Kafka et sélectionner le groupe de sécurité](./media/apache-kafka-rest-proxy/apache-kafka-rest-proxy-enable.png)

1. Cliquez sur **Sélectionner un groupe de sécurité**. Dans la liste des groupes de sécurité, sélectionnez le groupe de sécurité qui doit avoir accès au proxy REST. Vous pouvez utiliser la zone de recherche pour retrouver le groupe de sécurité approprié. Cliquez sur le bouton **Sélectionner** en bas.

     ![Activer le proxy REST Kafka et sélectionner le groupe de sécurité](./media/apache-kafka-rest-proxy/apache-kafka-rest-proxy-select-security-group.png)

1. Effectuez les étapes restantes pour créer votre cluster, comme décrit dans [Créer un cluster Apache Kafka dans Azure HDInsight à l’aide du portail Azure](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. Une fois le cluster créé, accédez aux propriétés du cluster pour enregistrer l’URL du proxy REST Kafka.

     ![afficher l’URL du proxy REST](./media/apache-kafka-rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Exemple d’application cliente

Vous pouvez utiliser le code Python ci-dessous pour interagir avec le proxy REST sur votre cluster Kafka. Ce code effectue les actions suivantes :

1. Récupère (fetch) un jeton OAuth d’Azure AD
1. Crée la rubrique spécifiée
1. Envoie des messages à cette rubrique
1. Utilise les messages de cette rubrique

Pour plus d’informations sur l’obtention de jetons OAuth dans Python, consultez [Classe AuthenticationContext Python](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Vous pouvez constater un délai d’affichage, des rubriques ne semblent alors pas créées ou supprimées via le proxy REST Kafka. Ce délai est dû à l’actualisation du cache.

```python
#Required python packages
#pip3 install adal
#pip install msrestazure

import adal
from msrestazure.azure_active_directory import AdalAuthentication
from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
import requests

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

#getting token
login_endpoint = AZURE_PUBLIC_CLOUD.endpoints.active_directory
resource = "https://hib.azurehdinsight.net"
context = adal.AuthenticationContext(login_endpoint + '/' + tenant_id)

token = context.acquire_token_with_client_credentials(
    resource,
    client_id,
    client_secret)

accessToken = 'Bearer ' + token['accessToken']

print(accessToken)

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

Pour exécuter l’exemple de code, procédez comme suit :

1. Enregistrez l’exemple de code sur un ordinateur sur lequel Python est installé.
1. Installez les dépendances Python requises en exécutant `pip3 install adal` et `pip install msrestazure`.
1. Modifiez le code et mettez à jour les propriétés suivantes pour votre environnement :
    1.  *ID de locataire* : locataire Azure où se trouve votre abonnement.
    1.  *ID client* : ID de l’application que vous avez inscrite dans le groupe de sécurité.
    1.  *Clé secrète client* : secret de l’application que vous avez inscrite dans le groupe de sécurité.
    1.  *Kafkarest_endpoint* : récupérez cette valeur dans l’onglet « Propriétés » de la vue d’ensemble du cluster, comme décrit dans la [section de déploiement](#create-a-kafka-cluster-with-rest-proxy-enabled). Elle doit respecter le format suivant : `https://<clustername>-kafkarest.azurehdinsight.net`
3. Depuis la ligne de commande, exécutez le fichier Python en exécutant `python <filename.py>`

## <a name="next-steps"></a>Étapes suivantes

* [Documents d’informations de référence de l’API Proxy REST Kafka](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
* [Tutoriel : Utiliser les API de producteur et de consommateur Apache Kafka](apache-kafka-producer-consumer-api.md)