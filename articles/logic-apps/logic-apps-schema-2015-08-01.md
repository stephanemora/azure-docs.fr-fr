---
title: Mises à jour de schéma pour la préversion du 1er août 2015
description: Mise à jour de schéma version 2015-08-01-preview pour les définitions d’application logique dans Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/31/2016
ms.openlocfilehash: b6746baaede777eb8c2afcae9eb3fe80b669c468
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792844"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Mises à jour de schéma pour Azure Logic Apps - Version préliminaire du 1er août 2015

Cette version de schéma et d’API pour Azure Logic Apps intègre différentes améliorations clés destinées à accroître la fiabilité et la simplicité d’utilisation des applications logiques :

* Le type d’action **APIApp** s’appelle désormais [**APIConnection**](#api-connections).
* L’action **Repeat** s’appelle désormais [**Foreach**](#foreach).
* L’application API de [**l’Écouteur HTTP**](#http-listener)n’est plus requise.
* L’appel des flux de travail enfants utilise un [nouveau schéma](#child-workflows).

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>Déplacement vers les connexions d’API

La plus importante modification est que vous n’avez plus besoin de déployer API Apps dans votre abonnement Azure pour pouvoir utiliser des API. Voici les manières dont vous pouvez utiliser les API :

* API gérées
* Vos API web personnalisées

Chacune de ces méthodes fait l’objet d’un traitement légèrement différent, car leurs modèles de gestion et d’hébergement sont différents. Avec ce modèle, vous n’êtes plus limité aux ressources déployées dans votre groupe de ressources Azure. 

### <a name="managed-apis"></a>API gérées

Microsoft gère certaines API à votre place, comme Office 365, Salesforce, Twitter et FTP. Vous pouvez utiliser des API gérées (par exemple, Bing Translate), tandis que d’autres nécessitent une configuration, ou *connexion*.

Par exemple, quand vous utilisez Office 365, vous devez créer une connexion qui contient votre jeton de connexion Office 365. Ce jeton est stocké et actualisé de manière sécurisée afin que votre application logique puisse toujours appeler l’API Office 365. Si vous souhaitez vous connecter à votre serveur SQL ou FTP, vous devez créer une connexion présentant la chaîne de connexion. 

Dans la définition, ces actions sont appelées `APIConnection`. Voici un exemple de connexion appelant l’API Office 365 pour envoyer un courrier électronique :

``` json
{
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "host": {
               "api": {
                  "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
               },
               "connection": {
                  "name": "@parameters('$connections')['shared_office365']['connectionId']"
               }
            },
            "method": "POST",
            "body": {
               "Subject": "Reminder",
               "Body": "Don't forget!",
               "To": "me@contoso.com"
            },
            "path": "/Mail"
         }
      }
   }
}
```

L’objet `host` est une partie des entrées spécifique aux connexions d’API et contient deux parties : `api` et `connection`. L’objet `api` indique l’URL du runtime pour l’emplacement d’hébergement de cette API gérée. Vous pouvez voir l’ensemble des API managées disponibles en appelant cette méthode :

```text
GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/locations/<location>/managedApis?api-version=2015-08-01-preview
```

Quand vous utilisez une API, elle peut présenter ou non des *paramètres de connexion* définis. Par conséquent, si l’API ne définit pas ces paramètres, aucune connexion n’est requise. Si l’API définit ces paramètres, vous devez créer une connexion avec un nom spécifié.  
Vous référencez ensuite ce nom dans l’objet `connection` à l’intérieur de l’objet `host`. Pour créer une connexion dans un groupe de ressources, appelez la méthode suivante :

```text
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/<name>?api-version=2015-08-01-preview
```

Avec le corps suivant :

``` json
{
   "properties": {
      "api": {
         "id": "/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/managedApis/azureblob"
      },
      "parameterValues": {
         "accountName": "<Azure-storage-account-name-with-different-parameters-for-each-API>"
      }
   },
   "location": "<logic-app-location>"
}
```

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Déploiement d’API gérées dans un modèle Azure Resource Manager

Quand une connexion interactive n’est pas nécessaire, vous pouvez créer une application complète avec un modèle Azure Resource Manager.
Si une connexion est nécessaire, vous pouvez néanmoins utiliser un modèle Azure Resource Manager, mais vous devez autoriser les connexions via le portail Azure. 

``` json
"resources": [ {
   "apiVersion": "2015-08-01-preview",
   "name": "azureblob",
   "type": "Microsoft.Web/connections",
   "location": "[resourceGroup().location]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
      },
      "parameterValues": {
         "accountName": "[parameters('storageAccountName')]",
         "accessKey": "[parameters('storageAccountKey')]"
      }
    },
},
{
   "type": "Microsoft.Logic/workflows",
   "apiVersion": "2015-08-01-preview",
   "name": "[parameters('logicAppName')]",
   "location": "[resourceGroup().location]",
   "dependsOn": ["[resourceId('Microsoft.Web/connections', 'azureblob')]"],
   "properties": {
      "sku": {
         "name": "[parameters('sku')]",
         "plan": {
            "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('svcPlanName'))]"
         }
      },
      "parameters": {
         "$connections": {
             "value": {
                  "azureblob": {
                     "connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
                     "connectionName": "azureblob",
                     "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
                  }
             }
         }
      },
      "definition": {
         "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "type": "Object",
            "$connections": {
               "defaultValue": {},
 
            }
         },
         "triggers": {
            "Recurrence": {
               "type": "Recurrence",
               "recurrence": {
                  "frequency": "Day",
                  "interval": 1
               }
            }
         },
         "actions": {
            "Create_file": {
               "type": "ApiConnection",
               "inputs": {
                  "host": {
                     "api": {
                        "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                     },
                     "connection": {
                       "name": "@parameters('$connections')['azureblob']['connectionId']"
                     }
                  },
                  "method": "POST",
                  "queries": {
                     "folderPath": "[concat('/', parameters('containerName'))]",
                     "name": "helloworld.txt"
                  },
                  "body": "@decodeDataUri('data:, Hello+world!')",
                  "path": "/datasets/default/files"
               },
               "conditions": []
            }
         },
         "outputs": {}
      }
   }
} ]
```

Vous pouvez constater dans cet exemple que les connexions sont des ressources hébergées dans votre groupe de ressources. Elles référencent les API gérées disponibles dans votre abonnement.

### <a name="your-custom-web-apis"></a>Vos API web personnalisées

Si vous utilisez vos propres API au lieu des API managées par Microsoft, utilisez l’action **HTTP** intégrée pour les appeler. Dans l’idéal, vous devez fournir un point de terminaison Swagger pour votre API. Ce point de terminaison permet au concepteur d’application logique de montrer les entrées et les sorties de vos API. Sans point de terminaison Swagger, le concepteur peut seulement montrer les entrées et les sorties en tant qu’objets JSON opaques.

Voici un exemple illustrant la nouvelle propriété `metadata.apiDefinitionUrl` :

``` json
"actions": {
   "mycustomAPI": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://mysite.azurewebsites.net/api/apidef/"  
      },
      "inputs": {
         "uri": "https://mysite.azurewebsites.net/api/getsomedata",
         "method": "GET"
      }
   }
}
```

Si vous hébergez votre API web sur Azure App Service, votre API web s’affiche automatiquement dans la liste des actions disponibles dans le concepteur. Si ce n’est pas le cas, vous devrez coller le contenu directement dans l’URL. Le point de terminaison Swagger doit être non authentifié pour être utilisé dans le concepteur des applications logiques ; vous pouvez cependant sécuriser l’API à l’aide des méthodes prises en charge dans Swagger.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Appel d’applications API déployées avec 2015-08-01-preview

Si vous avez déjà déployé une application API, vous pouvez l’appeler à l’aide de l’action **HTTP**.
Par exemple, si vous utilisez Dropbox pour répertorier les fichiers, vous pouvez disposer du contenu suivant dans votre définition de version de schéma **2014-12-01-preview** :

``` json
"definition": {
   "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
         "defaultValue": "eyJ0eX...wCn90",
         "type": "String",
         "metadata": {
            "token": {
               "name": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
            }
         }
      }
    },
    "actions": {
       "dropboxconnector": {
          "type": "ApiApp",
          "inputs": {
             "apiVersion": "2015-01-14",
             "host": {
                "id": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                "gateway": "https://avdemo.azurewebsites.net"
             },
             "operation": "ListFiles",
             "parameters": {
                "FolderPath": "/myfolder"
             },
             "authentication": {
                "type": "Raw",
                "scheme": "Zumo",
                "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
             }
          }
       }
    }
}
```

Vous pouvez maintenant créer une action HTTP similaire et laisser inchangée la section `parameters` de la définition d’application logique, par exemple :

``` json
"actions": {
   "dropboxconnector": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
      },
      "inputs": {
         "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
         "method": "POST",
         "body": {
            "FolderPath": "/myfolder"
         },
         "authentication": {
            "type": "Raw",
            "scheme": "Zumo",
            "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
         }
      }
   }
}
```

Présentation de ces propriétés une par une :

| Propriété d’action | Description |
| --- | --- |
| `type` | `Http` au lieu de `APIapp` |
| `metadata.apiDefinitionUrl` | Pour utiliser cette action dans le concepteur d’applications logiques, vous devez inclure le point de terminaison des métadonnées, créé depuis : `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Construit depuis : `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Toujours `POST` |
| `inputs.body` | Identique aux paramètres de l’application API |
| `inputs.authentication` | Identique à l’authentification de l’application API |

Cette approche doit fonctionner avec l’ensemble des actions d’application API. N’oubliez pas cependant que ces applications API antérieures ne sont plus prises en charge. Vous devez donc opter pour l’une des deux autres options précédentes, à savoir une API gérée ou l’hébergement de votre API web personnalisée.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>« Repeat » renommée en « Foreach »

Les clients ayant utilisé la version de schéma précédente se sont beaucoup plaints du caractère déroutant du nom de l’action **Repeat** et n’ont pas vraiment compris que **Repeat** était réellement une boucle foreach. Par conséquent, nous avons renommé `repeat` en `foreach`. Précédemment, vous auriez écrit cette action comme dans l’exemple suivant :

``` json
"actions": {
   "pingBing": {
      "type": "Http",
      "repeat": "@range(0,2)",
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com/search?q=@{repeatItem()}"
      }
   }
}
```

Maintenant, vous écririez plutôt cette version :

``` json
"actions": {
   "pingBing": {
      "type": "Http",
      "foreach": "@range(0,2)",
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com/search?q=@{item()}"
      }
   }
}
```

En outre, la fonction `repeatItem()`, utilisée pour référencer l’élément traité par la boucle au cours de l’itération actuelle, a été renommée `item()`. 

### <a name="reference-outputs-from-foreach"></a>Référencement des sorties de 'foreach'

Dans un souci de simplification, les sorties des actions `foreach` ne sont plus encapsulées dans un objet appelé `repeatItems`. En outre, ces modifications entraînent la disparition des fonctions `repeatItem()`, `repeatBody()` et `repeatOutputs()`.

Par conséquent, avec l’exemple `repeat` précédent, vous obtenez les sorties suivantes :

``` json
"repeatItems": [ {
   "name": "pingBing",
   "inputs": {
      "uri": "https://www.bing.com/search?q=0",
      "method": "GET"
   },
   "outputs": {
      "headers": { },
      "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"https://schemas.live.com/Web/\">...</html>"
   },
   "status": "Succeeded"
} ]
```

Vous obtenez maintenant ces sorties à la place :

``` json
[ {
   "name": "pingBing",
      "inputs": {
         "uri": "https://www.bing.com/search?q=0",
         "method": "GET"
      },
      "outputs": {
         "headers": { },
         "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"https://schemas.live.com/Web/\">...</html>"
      },
      "status": "Succeeded"
} ]
```

Quand vous référenciez ces sorties, pour obtenir l’élément `body` de l’action, vous deviez auparavant procéder ainsi :

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "repeat": "@outputs('pingBing').repeatItems",
      "inputs": {
         "method": "POST",
         "uri": "https://www.example.com",
         "body": "@repeatItem().outputs.body"
      }
   }
}
```

Maintenant, vous pouvez utiliser plutôt cette version :

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "foreach": "@outputs('pingBing')",
      "inputs": {
         "method": "POST",
         "uri": "https://www.example.com",
         "body": "@item().outputs.body"
      }
   }
}
```

<a name="http-listener"></a>

## <a name="native-http-listener"></a>Écouteur HTTP natif

Les fonctionnalités de l’écouteur HTTP étant désormais intégrées, vous n’avez plus à déployer une application API d’écouteur HTTP. Pour plus d’informations, découvrez comment [rendre appelable le point de terminaison de votre application logique](../logic-apps/logic-apps-http-endpoint.md). 

Avec ces modifications, Logic Apps remplace la fonction `@accessKeys()` par la fonction `@listCallbackURL()`, qui obtient le point de terminaison quand c’est nécessaire. Vous devez aussi définir maintenant au moins un déclencheur dans votre application logique. Si vous voulez appliquer l’action `/run` au workflow, vous devez utiliser un de ces déclencheurs : `Manual`, `ApiConnectionWebhook` ou `HttpWebhook`.

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Appel de flux de travail enfants

Auparavant, quiconque souhaitait appeler des flux de travail enfants devait y accéder, récupérer le jeton d’accès, puis coller ce dernier dans la définition de l’application logique destinée à effectuer l’appel. Avec ce schéma, le moteur Logic Apps génère automatiquement une signature d’accès partagé lors de l’exécution pour le workflow enfant, ce qui évite de devoir coller des secrets dans la définition. Voici un exemple :

``` json
"myNestedWorkflow": {
   "type": "Workflow",
   "inputs": {
      "host": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/myWorkflow001",
         "triggerName": "myEndpointTrigger"
      },
      "queries": {
         "extrafield": "specialValue"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "Content-type": "application/json"
      },
      "body": {
         "contentFieldOne": "value100",
         "anotherField": 10.001
      }
   },
   "conditions": []
}
```

De plus, les workflows enfants obtiennent un accès complet à la demande entrante. Vous pouvez donc passer des paramètres dans la section `queries` et dans l’objet `headers`. Vous pouvez aussi définir entièrement la section `body`.

Enfin, les workflows enfants ont ces modifications nécessaires. Avant, vous pouviez appeler directement un workflow enfant. Maintenant, il vous faut définir un point de terminaison déclencheur dans le workflow pour le parent à appeler. Généralement, vous devrez ajouter un déclencheur de type `Manual`, que vous utiliserez dans la définition du parent. La propriété `host` a un élément `triggerName`, car vous devez toujours spécifier le déclencheur que vous appelez.

## <a name="other-changes"></a>Autres modifications

### <a name="new-queries-property"></a>Nouvelle propriété « queries »

Tous les types d’action prennent désormais en charge une nouvelle entrée appelée `queries`. Cette entrée peut être un objet structuré, qui ne nécessite aucun assemblage manuel de chaîne.

### <a name="renamed-parse-function-to-json"></a>Fonction 'parse()' renommée 'json()'

La fonction `parse()` a été renommée `json()` pour les types de contenu futurs.

## <a name="enterprise-integration-apis"></a>API Intégration Entreprise

Ce schéma ne prend pas encore en charge les versions managées pour les API Intégration Entreprise, comme AS2. Vous pouvez cependant utiliser vos API BizTalk déployées existantes via l’action HTTP. Pour plus d’informations, consultez « Utilisation de vos applications API déjà déployées » dans le [calendrier d’intégration](https://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 
