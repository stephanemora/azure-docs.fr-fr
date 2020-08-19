---
title: Comment utiliser l’Analyse de texte pour la santé
titleSuffix: Azure Cognitive Services
description: Découvrez comment extraire et étiqueter des informations médicales de texte clinique non structuré avec l’Analyse de texte pour la santé.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: aahi
ms.openlocfilehash: 4ba7aa530699ab0e06ac42e3701265254b617f73
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167689"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>Procédure : Utiliser l’Analyse de texte pour la santé (préversion)

> [!NOTE]
> L’Analyse de texte pour le conteneur d’intégrité a récemment été mis à jour. Pour plus d’informations sur les modifications récentes, consultez les [Nouveautés](../whats-new.md). N’oubliez pas d’extraire le conteneur le plus récent pour utiliser les mises à jour listées.

> [!IMPORTANT] 
> L’Analyse de texte pour l’intégrité est une fonctionnalité en version préliminaire fournie « en l’état » et « avec toutes les erreurs ». Par conséquent, **l’Analyse de texte pour l’intégrité (préversion) ne doit pas être implémentée ou déployée dans le cadre d’une utilisation en production.** L’Analyse de texte pour l’intégrité n’est pas destinée à être utilisée en tant que dispositif médical, support clinique, outil de diagnostic ou autre technologie destinée à être utilisée dans le diagnostic, la guérison, l’atténuation, le traitement ou la prévention de maladies ou d’autres conditions, et aucune licence ou droit n’est accordé par Microsoft pour utiliser cette fonctionnalité à ces fins. Cette fonctionnalité n’est pas conçue ou destinée à être mise en œuvre ou déployée en remplacement de conseils médicaux professionnels ou d’avis de santé, de diagnostic, de traitement ou de jugement clinique d’un professionnel de la santé, et ne doit pas être utilisé en tant que tel. Le client est seul responsable de l’utilisation de l’Analyse de texte pour l’intégrité. Microsoft ne garantit pas que l’Analyse de texte pour l’intégrité ou les ressources fournies dans le cadre de la fonctionnalité sont suffisantes pour des raisons médicales ou répondent aux exigences médicales de toute personne. 


L’Analyse de texte pour la santé est un service en conteneur qui extrait et étiquette les informations médicales pertinentes à partir de textes non structurés, tels que les notes du médecin, les bilans de sortie d’hospitalisation, les documents cliniques et les enregistrements d’intégrité électroniques.  

## <a name="features"></a>Fonctionnalités

Le conteneur d’Analyse de texte pour la santé effectue actuellement la Reconnaissance d’entité nommée (NER), l’extraction de relations, la négation d’entité et la liaison d’entités pour le texte en anglais, dans votre propre environnement de développement qui répond à vos exigences spécifiques en matière de sécurité et de gouvernance des données.

#### <a name="named-entity-recognition"></a>[Reconnaissance d’entité nommée](#tab/ner)

La Reconnaissance d’entité nommée détecte les mots et les expressions mentionnés dans du texte non structuré qui peuvent être associés à un ou plusieurs types sémantiques, tels que le diagnostic, le nom de médicaments, le symptôme/le signe ou l’âge.

> [!div class="mx-imgBorder"]
> ![NER d’intégrité](../media/ta-for-health/health-named-entity-recognition.png)

#### <a name="relation-extraction"></a>[Extraction de relations](#tab/relation-extraction)

L’extraction de relations identifie les connexions pertinentes entre les concepts mentionnés dans le texte. Par exemple, une relation « heure de maladie » est trouvée en associant un nom de pathologie à une indication temporelle. 

> [!div class="mx-imgBorder"]
> ![RE d’intégrité](../media/ta-for-health/health-relation-extraction.png)


#### <a name="entity-linking"></a>[Entity Linking](#tab/entity-linking)

La liaison d’entités distingue des entités distinctes en associant des entités nommées mentionnées dans du texte à des concepts trouvés dans une base de données de concepts prédéfinie. Par exemple, le système de langage médical unifié (UMLS).

> [!div class="mx-imgBorder"]
> ![EL d’intégrité](../media/ta-for-health/health-entity-linking.png)

L’Analyse de texte pour la santé prend en charge la liaison avec le vocabulaire médical et biomédical figurant dans la source de connaissances Metathesaurus de l’[UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html) (Unified Medical Language System).

#### <a name="negation-detection"></a>[Détection de négation](#tab/negation-detection) 

La signification du contenu médical est fortement affectée par les modificateurs, tels que la négation, qui peuvent avoir une incidence critique en cas de mauvais diagnostic. L’Analyse de texte pour la santé prend en charge la détection de négation pour les différentes entités mentionnées dans le texte. 

> [!div class="mx-imgBorder"]
> ![NEG d’intégrité](../media/ta-for-health/health-negation.png)

---

Consultez les [catégories d’entité](../named-entity-types.md?tabs=health) retournées par l’Analyse de texte pour la santé pour obtenir un liste complète des entités prises en charge.

## <a name="supported-languages"></a>Langues prises en charge

L’Analyse de texte pour la santé prend uniquement en charge les documents en langue anglaise.

## <a name="request-access-to-the-container-registry"></a>Demander l’accès au registre de conteneurs

Complétez et envoyez le [formulaire de demande de conteneurs Cognitive Services](https://aka.ms/cognitivegate) pour demander l’accès au conteneur. Actuellement, vous ne serez pas facturé pour l’utilisation de l’Analyse de texte pour la santé. 

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="install-the-container"></a>Installer le conteneur

Il existe plusieurs façons d’installer et d’exécuter le conteneur. 

- Utilisez le [Portail Azure](text-analytics-how-to-install-containers.md?tabs=healthcare) pour créer une ressource d’Analyse de texte, et utilisez Docker pour obtenir votre conteneur.
- Utilisez les scripts PowerShell et [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) suivants pour automatiser la configuration du conteneur de déploiement de ressources.

### <a name="install-the-container-using-azure-web-app-for-containers"></a>Installer le conteneur à l’aide d’Azure Web App pour conteneurs

Azure [Web App pour conteneurs](https://azure.microsoft.com/services/app-service/containers/) est une ressource Azure dédiée à l’exécution de conteneurs dans le Cloud. Il offre des fonctionnalités prêtes à l’emploi, telles que la mise à l’échelle automatique, la prise en charge des conteneurs Docker et la composition Docker, la prise en charge de HTTPS et bien plus encore.

> [!NOTE]
> À l’aide d’Azure Web App, vous obtiendrez automatiquement un domaine sous la forme `<appservice_name>.azurewebsites.net`

Exécutez ce script PowerShell au moyen de la Azure CLI pour créer un Web App pour conteneurs, à l’aide de votre abonnement et de l’image de conteneur sur HTTPS. Attendez la fin du script (environ 25 à 30 minutes) avant d’envoyer la première requête.

```bash
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME -s $DOCKER_REGISTRY_SERVER_USERNAME -w $DOCKER_REGISTRY_SERVER_PASSWORD
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>Installer le conteneur à l’aide d’Azure Container Instance

Vous pouvez également utiliser Azure Container Instance (ACI) pour faciliter le déploiement. ACI est une ressource qui vous permet d’exécuter des conteneurs Docker à la demande dans un environnement Azure serverless géré. 

Consultez [Comment utiliser Azure Container Instances](text-analytics-how-to-use-container-instances.md) pour connaître les étapes de déploiement d’une ressource ACI à l’aide du Portail Azure. Vous pouvez également utiliser le script PowerShell ci-dessous avec Azure CLI, ce qui crée une ACI sur votre abonnement à l’aide de l’image conteneur.  Attendez la fin du script (environ 25 à 30 minutes) avant d’envoyer la première requête.  En raison de la limite du nombre maximal de processeurs par ressource ACI, ne sélectionnez pas cette option si vous envisagez de soumettre plus de 5 documents volumineux (environ 5 000 caractères chacun) par requête.
Pour plus d’informations sur la disponibilité, consultez l’article [Support régional ACI](https://docs.microsoft.com/azure/container-instances/container-instances-region-availability). 

> [!NOTE] 
> Azure Container Instances n’inclue pas la prise en charge HTTPS pour les domaines intégrés. Si vous avez besoin de HTTPS, vous devez le configurer manuellement, y compris créer un certificat et l’inscription d’un domaine. Vous trouverez des instructions pour effectuer cette opération avec NGINX ci-dessous.

```bash
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_REGISTRY_LOGIN_SERVER = "containerpreview.azurecr.io"
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --registry-login-server $DOCKER_REGISTRY_LOGIN_SERVER --registry-username $DOCKER_REGISTRY_SERVER_USERNAME --registry-password $DOCKER_REGISTRY_SERVER_PASSWORD --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>Sécuriser la connectivité ACI

Par défaut, aucune sécurité n’est fournie lors de l’utilisation d’ACI avec l’API de conteneur. Cela est dû au fait que les conteneurs s’exécute généralement dans le cadre d’un pod qui est protégé de l’extérieur par un pont réseau. Vous pouvez toutefois modifier un conteneur avec un composant frontal, en gardant le point de terminaison de conteneur privé. Les exemples suivants utilisent [NGINX](https://www.nginx.com) en tant que passerelle d’entrée pour prendre en charge le protocole HTTPS/SSL et l’authentification client-certificat.

> [!NOTE]
> NGINX est un serveur et un proxy HTTP open source à hautes performances. Un conteneur NGINX peut être utilisé pour mettre fin à une connexion TLS pour un seul conteneur. Des solutions de terminaison TLS basées sur l’entrée NGINX plus complexes sont également possibles.

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>Configurer NGINX en tant que passerelle d’entrée

NGINX utilise des [fichiers config](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) pour activer les fonctionnalités lors du runtime. Pour activer la terminaison TLS pour un autre service, vous devez spécifier un certificat SSL pour mettre fin à la connexion TLS et `proxy_pass` pour spécifier une adresse pour le service. Un exemple est fourni ci-dessous.


> [!NOTE]
> `ssl_certificate` attend la spécification d’un chemin d’accès dans le système de fichiers local du conteneur NGINX. L’adresse spécifiée pour `proxy_pass` doit être disponible dans le réseau du conteneur NGINX.

Le conteneur NGINX chargera tous les fichiers dans `_.conf_` qui sont montés sous `/etc/nginx/conf.d/` dans le chemin de configuration HTTP.

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>Exemple de fichier Docker Compose

L’exemple ci-dessous montre comment créer un fichier [Docker Compose](https://docs.docker.com/compose/reference/overview) pour déployer les conteneurs NGINX et d’Analyse de texte pour la santé :

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

Pour initier ce fichier Docker Compose, exécutez la commande suivante à partir d’une console au niveau racine du fichier :

```bash
docker-compose up
```

Pour plus d’informations, consultez la documentation de NGINX sur la [terminaison SSL NGINX](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/).


## <a name="example-api-request"></a>Exemple de demande API
Le conteneur fournit des API de point de terminaison de prédiction de requête basées sur REST.

Utilisez l’exemple de requête cURL ci-dessous pour envoyer une requête au conteneur que vous avez déployé en remplaçant la variable `serverURL` par la valeur appropriée.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

Le code JSON suivant est un exemple de fichier JSON joint au corps POST de la requête API d’Analyse de texte pour la santé :

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

## <a name="api-response-body"></a>Corps de la réponse API

Le code JSON suivant est un exemple corps de réponse API de l’Analyse de texte pour la santé :

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 17,
                    "length": 11,
                    "text": "itchy sores",
                    "category": "SymptomOrSign",
                    "confidenceScore": 1.0,
                    "isNegated": false
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "id": "0",
                    "offset": 11,
                    "length": 4,
                    "text": "50mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 16,
                    "length": 8,
                    "text": "benadryl",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C0700899"
                        },
                        {
                            "dataSource": "CHV",
                            "id": "0000044903"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "899"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "D004155"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C300"
                        },
                        {
                            "dataSource": "NCI_DTP",
                            "id": "NSC0033299"
                        },
                        {
                            "dataSource": "PDQ",
                            "id": "CDR0000039163"
                        },
                        {
                            "dataSource": "PSY",
                            "id": "05760"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "203457"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 32,
                    "length": 11,
                    "text": "twice daily",
                    "category": "Frequency",
                    "confidenceScore": 1.0,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-07-24"
}
```

### <a name="negation-detection-output"></a>Sortie de détection de négation

Fans certains cas, en utilisant la détection de négation, un terme de négation unique peut traiter plusieurs termes à la fois. La négation d’une entité reconnue est représentée dans la sortie JSON par la valeur booléenne de l’indicateur `isNegated` :

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>Sortie d’extraction de relations

La sortie d’extraction de relations contient des références URI à la *source* de la relation, et sa *cible*. Les entités ayant le rôle de relation de `ENTITY` sont affectées au champ `target`. Les entités ayant le rôle de relation de `ATTRIBUTE` sont affectées au champ `source`. Les relations d’abréviation contiennent des champs `source` et `target` bidirectionnels, et `bidirectional` sera défini sur `true`. 

```json
"relations": [
  {
      "relationType": "DosageOfMedication",
      "score": 1.0,
      "bidirectional": false,
      "source": "#/documents/2/entities/0",
      "target": "#/documents/2/entities/1",
      "entities": [
          {
              "id": "0",
              "role": "ATTRIBUTE"
          },
          {
              "id": "1",
              "role": "ENTITY"
          }
      ]
  },
...
]
```

## <a name="see-also"></a>Voir aussi

* [Vue d’ensemble d’Analyse de texte](../overview.md)
* [Catégories d’entité nommée](../named-entity-types.md)
* [Nouveautés](../whats-new.md)
