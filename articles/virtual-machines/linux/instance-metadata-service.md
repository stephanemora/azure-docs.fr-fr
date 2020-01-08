---
title: Service de métadonnées d’instance Azure
description: Une interface RESTful permettant d’obtenir des informations sur le calcul, le réseau et les événements de maintenance à venir d’une machine virtuelle Linux.
services: virtual-machines-linux
documentationcenter: ''
author: KumariSupriya
manager: harijayms
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: 6ca8dd08f3b6c1a7bc9a0b8c7ba853adb46fd30c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355974"
---
# <a name="azure-instance-metadata-service"></a>Service de métadonnées d’instance Azure

Le service de métadonnées d’instance Azure fournit des informations sur les instances de machine virtuelle en cours d’exécution qui peuvent être utilisées pour gérer et configurer vos machines virtuelles.
Cela inclut des informations telles que la référence (SKU), la configuration réseau et les événements de maintenance à venir. Pour plus d’informations sur le type d’informations disponibles, consultez [API de métadonnées](#metadata-apis).

Le service de métadonnées d’instance d’Azure est un point de terminaison REST accessible à toutes les machines virtuelles IaaS créées via [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/).
Le point de terminaison est disponible à une adresse IP non routable bien connue (`169.254.169.254`) accessible uniquement à partir de la machine virtuelle.

> [!IMPORTANT]
> Ce service est **mis à la disposition générale** dans toutes les régions Azure.  Il reçoit régulièrement des mises à jour pour exposer de nouvelles informations sur les instances de machine virtuelle. Cette page reflète les [API de métadonnées](#metadata-apis) à jour disponibles.

## <a name="service-availability"></a>Disponibilité du service

Le service est disponible dans toutes les régions Azure mises à la disposition générale. Toutes les versions d’API ne sont peut-être pas disponibles dans toutes les régions Azure.

Régions                                        | Disponibilité ?                                 | Versions prises en charge
-----------------------------------------------|-----------------------------------------------|-----------------
[Toutes les régions Azure globales généralement disponibles](https://azure.microsoft.com/regions/)     | Mise à la disposition générale | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Mise à la disposition générale | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30
[Azure Chine](https://www.azure.cn/)                                                     | Mise à la disposition générale | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30
[Azure Allemagne](https://azure.microsoft.com/overview/clouds/germany/)                    | Mise à la disposition générale | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30

Ce tableau est mis à jour lors des mises à jour du service et lorsque de nouvelles versions prises en charge sont disponibles.

Pour tester le service de métadonnées d’instance, créez une machine virtuelle à partir d’[Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) ou du [portail Azure](https://portal.azure.com) dans les régions ci-dessus, puis suivez les exemples ci-dessous.

## <a name="usage"></a>Usage

### <a name="versioning"></a>Contrôle de version

La version de Instance Metadata Service est gérée et la spécification de la version de l’API dans la requête HTTP est obligatoire.

Vous pouvez voir les versions les plus récentes dans ce [tableau de disponibilité](#service-availability).

Au fur et à mesure que des versions plus récentes sont ajoutées, les versions antérieures sont toujours accessibles pour des questions de compatibilité si vos scripts ont des dépendances sur des formats de données spécifiques.

Si aucune version n’est spécifiée, une erreur est retournée avec une liste des versions plus récentes prises en charge.

> [!NOTE]
> La réponse est une chaîne JSON. L’exemple de réponse suivant est imprimé avec soin par souci de lisibilité.

**Requête**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance"
```

**Réponse**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2018-10-01",
        "2018-04-02",
        "2018-02-01"
    ]
}
```

### <a name="using-headers"></a>Utilisation d’en-têtes

Quand vous interrogez le service de métadonnées d’instance, vous devez fournir l’en-tête `Metadata: true` pour garantir que la demande n’a pas été redirigée involontairement.

### <a name="retrieving-metadata"></a>Récupération des métadonnées

Les métadonnées Instance sont disponibles pour l’exécution de machines virtuelles créées/gérées à l’aide [d’Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Accédez à toutes les catégories de données pour une instance de machine virtuelle à l’aide de la demande suivante :

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE]
> Toutes les requêtes de métadonnées d’instance respectent la casse.

### <a name="data-output"></a>Sortie des données

Par défaut, le service de métadonnées d’instance retourne des données au format JSON (`Content-Type: application/json`). Toutefois, différentes API retournent des données dans des formats différents si nécessaire.
Le tableau suivant répertorie les autres formats de données que les API peuvent prendre en charge.

API | Format de données par défaut | Autres formats
--------|---------------------|--------------
/instance | json | text
/scheduledevents | json | Aucun
/attested | json | Aucun

Pour accéder à un format de réponse autre que le format par défaut, spécifiez le format demandé en tant que paramètre de chaîne de requête dans la demande. Par exemple :

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> Pour les nœuds terminaux, `format=json` ne fonctionne pas. Pour ces requêtes, `format=text` doit être spécifié explicitement si le format par défaut est json.

### <a name="security"></a>Sécurité

Le point de terminaison du service de métadonnées d’instance est accessible uniquement à partir de l’instance de machine virtuelle active sur une adresse IP non routable. En outre, toute demande contenant un en-tête `X-Forwarded-For` est rejetée par le service.
Toute demande doit contenir un en-tête `Metadata: true` garantissant que la demande est réellement intentionnelle et ne fait pas partie d’une redirection involontaire.

### <a name="error"></a>Error

S’il existe un élément de données introuvable ou une requête mal formée, le service de métadonnées d’instance retourne des erreurs HTTP standard. Par exemple :

Code d’état HTTP | Motif
----------------|-------
200 OK |
400 Demande incorrecte | En-tête `Metadata: true` manquant ou format manquant lors de l’interrogation d’un nœud terminal
404 Introuvable | L’élément demandé n’existe pas
405 Méthode non autorisée | Seules les demandes `GET` et `POST` sont prises en charge
429 Trop de demandes | L’API prend actuellement en charge un maximum de 5 requêtes par seconde
500 Erreur de service     | Recommencez l’opération plus tard

### <a name="examples"></a>Exemples

> [!NOTE]
> Toutes les réponses de l’API sont des chaînes JSON. Tous les exemples de réponses suivants sont imprimés avec soin par souci de lisibilité.

#### <a name="retrieving-network-information"></a>Récupération des informations réseau

**Requête**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Réponse**

> [!NOTE]
> La réponse est une chaîne JSON. L’exemple de réponse suivant est imprimé avec soin par souci de lisibilité.

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="retrieving-public-ip-address"></a>Récupération de l’adresse IP publique

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Récupération de toutes les métadonnées d’une instance

**Requête**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2019-03-11"
```

**Réponse**

> [!NOTE]
> La réponse est une chaîne JSON. L’exemple de réponse suivant est imprimé avec soin par souci de lisibilité.

```json
{
  "compute": {
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Prod;Role:WorkerRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Récupération de métadonnées dans une machine virtuelle Windows

**Requête**

Les métadonnées d’instance peuvent être récupérées dans Windows via l’utilitaire Powershell `curl` : 

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-03-11 | select -ExpandProperty Content
```

Ou via l’applet de commande `Invoke-RestMethod` :

```powershell

Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2019-03-11 -Method get 
```

**Réponse**

> [!NOTE]
> La réponse est une chaîne JSON. L’exemple de réponse suivant est imprimé avec soin par souci de lisibilité.

```json
{
  "compute": {
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="metadata-apis"></a>API de métadonnées

#### <a name="the-following-apis-are-available-through-the-metadata-endpoint"></a>Les API suivantes sont disponibles via le point de terminaison de métadonnées :

Données | Description | Version introduite
-----|-------------|-----------------------
attested | Voir [Données attestées](#attested-data) | 2018-10-01
identité | Identités managées pour les ressources Azure. Consulter [Obtenir un jeton d’accès](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
instance | Consulter [API Instance](#instance-api) | 2017-04-02
scheduledevents | Consultez [Événements planifiés](scheduled-events.md). | 2017-08-01

#### <a name="instance-api"></a>API Instance
##### <a name="the-following-compute-categories-are-available-through-the-instance-api"></a>Les catégories de calcul suivantes sont disponibles via l’API Instance :

> [!NOTE]
> Grâce au point de terminaison de métadonnées, les catégories suivantes sont accessibles via l’instance/le calcul

Données | Description | Version introduite
-----|-------------|-----------------------
azEnvironment | Environnement Azure dans lequel s’exécute la machine virtuelle | 2018-10-01
customData | Consulter [Données personnalisées](#custom-data) | 2019-02-01
location | Région Azure dans laquelle la machine virtuelle est en cours d’exécution | 2017-04-02
name | Nom de la machine virtuelle | 2017-04-02
offer | Fournit des informations pour l’image de machine virtuelle et ne sont présentes que pour les images déployées à partir de la galerie d’images Azure | 2017-04-02
osType | Linux ou Windows | 2017-04-02
placementGroupId | [Groupe de placement](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) de votre groupe de machines virtuelles identiques | 2017-08-01
Plan | [Plan](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) contenant le nom, le produit et l’éditeur d’une machine virtuelle s’il s’agit d’une image de la Place de marché Azure | 2018-04-02
platformUpdateDomain |  [Domaine de mise à jour](manage-availability.md) dans lequel la machine virtuelle est en cours d’exécution | 2017-04-02
platformFaultDomain | [Domaine par défaut](manage-availability.md) dans lequel la machine virtuelle est en cours d’exécution | 2017-04-02
provider | Fournisseur de la machine virtuelle | 2018-10-01
publicKeys | [Collection de clés publiques](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) affectée à la machine virtuelle et aux chemins | 2018-04-02
publisher | Éditeur de l’image de machine virtuelle | 2017-04-02
resourceGroupName | [Groupe de ressources](../../azure-resource-manager/management/overview.md) de votre machine virtuelle | 2017-08-01
resourceId | L’ID [complet](https://docs.microsoft.com/rest/api/resources/resources/getbyid) de la ressource | 2019-03-11
sku | Référence (SKU) spécifique pour l’image de machine virtuelle | 2017-04-02
subscriptionId | Abonnement Azure pour la machine virtuelle | 2017-08-01
tags | [Étiquettes](../../azure-resource-manager/resource-group-using-tags.md) de votre machine virtuelle  | 2017-08-01
tagsList | Balises mises en forme en tant que tableau JSON pour faciliter l’analyse programmatique  | 2019-06-04
version | Version de l’image de machine virtuelle | 2017-04-02
vmId | [Identificateur unique](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) de la machine virtuelle | 2017-04-02
vmScaleSetName | [Nom du groupe identique de machine virtuelle](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) de votre groupe de machines virtuelles identiques | 2017-12-01
vmSize | [Taille de la machine virtuelle](sizes.md) | 2017-04-02
zone | [Zone de disponibilité](../../availability-zones/az-overview.md) de votre machine virtuelle | 2017-12-01

##### <a name="the-following-network-categories-are-available-through-the-instance-api"></a>Les catégories de réseau suivantes sont disponibles via l’API Instance :

> [!NOTE]
> Grâce au point de terminaison de métadonnées, les catégories suivantes sont accessibles via l’instance/le réseau/l’interface

Données | Description | Version introduite
-----|-------------|-----------------------
ipv4/privateIpAddress | Adresse IPv4 locale de la machine virtuelle | 2017-04-02
ipv4/publicIpAddress | Adresse IPv4 publique de la machine virtuelle | 2017-04-02
subnet/address | Adresse de sous-réseau de la machine virtuelle | 2017-04-02
subnet/prefix | Préfixe de sous-réseau, par exemple 24 | 2017-04-02
ipv6/ipAddress | Adresse IPv6 locale de la machine virtuelle | 2017-04-02
macAddress | Adresse MAC de la machine virtuelle | 2017-04-02

## <a name="attested-data"></a>Données attestées

Les métadonnées d’instance répondent au point de terminaison http 169.254.169.254. Une partie du scénario pris en charge par Instance Metadata Service garantit que les données de réponse proviennent d’Azure. Nous signons une partie de ces informations afin qu’une image de la Place de marché puisse déterminer que c’est l’une de ses instances qui s’exécute sur Azure.

### <a name="example-attested-data"></a>Exemple de données attestées

> [!NOTE]
> Toutes les réponses de l’API sont des chaînes JSON. Les exemples de réponses suivants sont imprimés avec soin par souci de lisibilité.

 **Requête**

 ```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

La version de l’API est un champ obligatoire. Reportez-vous à la [section disponibilité du service](#service-availability) pour les versions d’API prises en charge.
Nonce est une chaîne facultative de 10 chiffres fournie. Nonce permet de suivre la requête et, si elle n’est pas fournie, l’horodatage UTC actuel est renvoyé dans la chaîne codée en réponse.

 **Réponse**

> [!NOTE]
> La réponse est une chaîne JSON. L’exemple de réponse suivant est imprimé avec soin par souci de lisibilité.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> L’objet blob de signature est une version signée [pkcs7](https://aka.ms/pkcs7) du document. Il contient le certificat utilisé pour la signature ainsi que les détails de machine virtuelle, comme vmId, nonce, subscriptionId, timeStamp pour la création et l’expiration du document et les informations du plan sur l’image. Les informations du plan ne sont fournies que pour les images Azure Marketplace. Le certificat peut être extrait de la réponse et utilisé pour valider que la réponse est correcte et provient d’Azure.

#### <a name="retrieving-attested-metadata-in-windows-virtual-machine"></a>Récupération de métadonnées attestées dans une machine virtuelle Windows

 **Requête**

Les métadonnées d’instance peuvent être récupérées dans Windows via l’utilitaire Powershell `curl` :

 ```bash
curl -H @{'Metadata'='true'} "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" | select -ExpandProperty Content
```

 Ou via l’applet de commande `Invoke-RestMethod` :

 ```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" -Method get
```

La version de l’API est un champ obligatoire. Reportez-vous à la section disponibilité du service pour les versions d’API prises en charge.
Nonce est une chaîne facultative de 10 chiffres fournie. Nonce permet de suivre la requête et, si elle n’est pas fournie, l’horodatage UTC actuel est renvoyé dans la chaîne codée en réponse.

 **Réponse**

> [!NOTE]
> La réponse est une chaîne JSON. L’exemple de réponse suivant est imprimé avec soin par souci de lisibilité.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> L’objet blob de signature est une version signée [pkcs7](https://aka.ms/pkcs7) du document. Il contient le certificat utilisé pour la signature ainsi que les détails de machine virtuelle, comme vmId, nonce, subscriptionId, timeStamp pour la création et l’expiration du document et les informations du plan sur l’image. Les informations du plan ne sont fournies que pour les images Azure Marketplace. Le certificat peut être extrait de la réponse et utilisé pour valider que la réponse est correcte et provient d’Azure.


## <a name="example-scenarios-for-usage"></a>Exemples de scénarios d’utilisation  

### <a name="tracking-vm-running-on-azure"></a>Suivi de la machine virtuelle s’exécutant sur Azure

En tant que fournisseur de service, vous aurez peut-être besoin de suivre le nombre de machines virtuelles s’exécutant sur votre logiciel ou de disposer d’agents effectuant le suivi de l’unicité de la machine virtuelle. Pour pouvoir obtenir un ID unique pour une machine virtuelle, utilisez le champ `vmId` du service de métadonnées d’instance.

**Requête**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Réponse**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Positionnement du domaine d’erreur/de mise à jour basé sur des conteneurs et des partitions de données

Pour certains scénarios, le positionnement des différents réplicas de données est de première importance. Par exemple, pour le [positionnement du réplica HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) ou pour le positionnement des conteneurs via un [orchestrateur](https://kubernetes.io/docs/user-guide/node-selection/), vous devez connaître les domaines `platformFaultDomain` et `platformUpdateDomain` sur lesquels la machine virtuelle s’exécute.
Vous pouvez également utiliser les [Zones de disponibilité](../../availability-zones/az-overview.md) pour les instances pour prendre ces décisions.
Vous pouvez interroger ces données directement via le service de métadonnées d’instance.

**Requête**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Réponse**

```text
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Obtenir plus d’informations sur la machine virtuelle pour le dossier de support

En tant que fournisseur de services, vous recevrez peut-être un appel du support pour lequel vous devrez connaître les informations de la machine virtuelle. Demandez au client de partager les métadonnées de calcul afin que l’équipe du support dispose des informations de base concernant le type de machine virtuelle sur Azure.

**Requête**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-08-01"
```

**Réponse**

> [!NOTE]
> La réponse est une chaîne JSON. L’exemple de réponse suivant est imprimé avec soin par souci de lisibilité.

```json
{
  "compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  }
}
```

### <a name="getting-azure-environment-where-the-vm-is-running"></a>Obtenir l’environnement Azure là où s’exécute la machine virtuelle

Azure dispose de plusieurs clouds souverains comme [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Vous avez parfois besoin de l’environnement Azure pour prendre certaines décisions liées au runtime. L’exemple suivant vous montre comment obtenir ce comportement.

**Requête**
```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Réponse**
```bash
AzurePublicCloud
```
Le cloud et les valeurs de l’environnement Azure sont répertoriés ci-dessous.

 Cloud   | Environnement Azure
---------|-----------------
[Toutes les régions Azure globales généralement disponibles](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure Chine](https://azure.microsoft.com/global-infrastructure/china/)                  | AzureChinaCloud
[Azure Allemagne](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

### <a name="getting-the-tags-for-the-vm"></a>Obtenir les balises de la machine virtuelle

Des balises peuvent être appliqués à votre machine virtuelle Azure pour les organiser de manière logique dans une taxonomie. Les balises affectées à une machine virtuelle peuvent être récupérées à l’aide de la requête ci-dessous.

**Requête**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Réponse**

```text
Department:IT;Environment:Test;Role:WebRole
```

Le champ `tags` est une chaîne dont les balises sont délimitées par des points-virgules. Cela peut constituer un problème si des points-virgules sont utilisés dans les balises elles-mêmes. Si un analyseur est écrit pour extraire par programme les balises, vous devez vous appuyer sur le champ `tagsList` qui est un tableau JSON sans délimiteur et, par conséquent, plus facile à analyser.

**Requête**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04&format=text"
```

**Réponse**

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "Environment",
    "value": "Test"
  },
  {
    "name": "Role",
    "value": "WebRole"
  }
]
```

### <a name="validating-that-the-vm-is-running-in-azure"></a>Avoir la garantie que la machine virtuelle s’exécute dans Azure

Les fournisseurs de la Place de marché souhaitent avoir la certitude que la licence de leur logiciel prévoit une exécution sur Azure exclusivement. Si quelqu’un copie le disque dur virtuel localement, ils doivent avoir la possibilité de le détecter. En faisant appel à Instance Metadata Service, les fournisseurs de la Place de marché peuvent obtenir des données signées qui garantissent que la réponse provient d’Azure uniquement.

> [!NOTE]
> Nécessite jq pour être installé.

**Requête**

 ```bash
  # Get the signature
   curl  --silent -H Metadata:True http://169.254.169.254/metadata/attested/document?api-version=2019-04-30 | jq -r '.["signature"]' > signature
  # Decode the signature
  base64 -d signature > decodedsignature
  #Get PKCS7 format
  openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
  # Get Public key out of pkc7
  openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
  #Get the intermediate certificate
  wget -q -O intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
  openssl x509 -inform der -in intermediate.cer -out intermediate.pem
  #Verify the contents
  openssl smime -verify -in sign.pk7 -inform pem -noverify
 ```

 **Réponse**

```json
Verification successful
{"nonce":"20181128-001617",
  "plan":
    {
     "name":"",
     "product":"",
     "publisher":""
    },
"timeStamp":
  {
    "createdOn":"11/28/18 00:16:17 -0000",
    "expiresOn":"11/28/18 06:16:17 -0000"
  },
"vmId":"d3e0e374-fda6-4649-bbc9-7f20dc379f34",
"subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx"
}
```

Données | Description
-----|------------
nonce | Chaîne facultative fournie par l’utilisateur avec la requête. Si aucune valeur nonce n’a été fournie dans la requête, l’horodatage UTC actuel est renvoyé.
Plan | [Plan](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) pour une machine virtuelle d’une image de la Place de marché Microsoft Azure, contient le nom, le produit et l’éditeur
timestamp/createdOn | Horodatage de création du premier document signé
timestamp/expiresOn | Horodatage d’expiration du document signé
vmId |  [Identificateur unique](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) de la machine virtuelle
subscriptionId | Abonnement Azure pour la machine virtuelle, inauguré dans `2019-04-30`

#### <a name="verifying-the-signature"></a>Vérification de la signature

Après avoir obtenu la signature ci-dessus, vous pouvez vérifier qu’elle émane de Microsoft. Vous pouvez également vérifier le certificat intermédiaire et la chaîne d'approbation. Enfin, vous pouvez vérifier que l’ID d’abonnement est correct.

> [!NOTE]
> Le certificat est différent pour le cloud public et le cloud souverain.

 Cloud | Certificat
---------|-----------------
[Toutes les régions Azure globales généralement disponibles](https://azure.microsoft.com/regions/)     | metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | metadata.azure.us
[Azure Chine](https://azure.microsoft.com/global-infrastructure/china/)                  | metadata.azure.cn
[Azure Allemagne](https://azure.microsoft.com/overview/clouds/germany/)                    | metadata.microsoftazure.de

```bash

# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

Lorsque le certificat intermédiaire ne peut pas être téléchargé en raison de contraintes réseau pendant la validation, le certificat intermédiaire peut être épinglé. Azure remplacera toutefois les certificats conformément à la pratique PKI standard. Les certificats épinglés doivent être mis à jour en cas de remplacement. Si une mise à jour du certificat intermédiaire est planifiée, le blog Azure sera mis à jour et les clients Azure en seront informés. Vous pouvez trouver les certificats intermédiaires [ici](https://www.microsoft.com/pki/mscorp/cps/default.htm). Les certificats intermédiaires de chaque région peuvent être différents.

### <a name="failover-clustering-in-windows-server"></a>Clustering de basculement dans Windows Server

Pour certains scénarios, pendant l’interrogation du service Instance Metadata Service avec le clustering de basculement, vous devez ajouter une route à la table de routage.

1. Ouvrez une invite de commandes avec des privilèges d’administrateur.

2. Exécutez la commande suivante et notez l’adresse de l’interface pour la destination réseau (`0.0.0.0`) dans la table de routage IPv4.

```bat
route print
```

> [!NOTE] 
> L’exemple de sortie suivant provenant d’une machine virtuelle Windows Server avec un cluster de basculement activé contient uniquement la table de routage IPv4 par souci de simplicité.

```bat
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
         10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
        10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
      169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
    169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
  169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         10.0.1.10    266
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

1. Exécutez la commande suivante et notez l’adresse de l’interface pour la destination réseau (`0.0.0.0`) qui est (`10.0.1.10`) dans cet exemple.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

### <a name="custom-data"></a>Données personnalisées
Instance Metadata Service permet à la machine virtuelle d’accéder à ses données personnalisées. Les données binaires doivent être inférieures à 64 Ko et sont fournies à la machine virtuelle sous forme encodée en base64.

Des données personnalisées Azure peuvent être insérées sur la machine virtuelle via des API REST, des applets de commande PowerShell, l’interface de ligne de commande (CLI) Azure ou un modèle ARM.

Pour obtenir un exemple d’interface de ligne de commande Azure, consultez [Données personnalisées et Cloud-Init sur Microsoft Azure](https://azure.microsoft.com/blog/custom-data-and-cloud-init-on-windows-azure/).

Pour obtenir un exemple de modèle ARM, consultez [Déployer une machine virtuelle avec CustomData](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).

Les données personnalisées sont disponibles pour tous les processus en cours d’exécution dans la machine virtuelle. Il est recommandé aux clients de ne pas insérer d’informations confidentielles dans des données personnalisées.

La disponibilité des données personnalisées est actuellement garantie pendant le démarrage d’une machine virtuelle. Si des mises à jour sont apportées à la machine virtuelle, comme l’ajout de disques ou le redimensionnement de la machine virtuelle, Instance Metadata Service ne fournira pas de données personnalisées. La fourniture de données personnalisées en continu via Instance Metadata Service est actuellement en cours de développement.

#### <a name="retrieving-custom-data-in-virtual-machine"></a>Récupération de données personnalisées dans la machine virtuelle
Instance Metadata Service fournit des données personnalisées à la machine virtuelle sous forme encodée en base64. L’exemple suivant décode la chaîne encodée en base64.

> [!NOTE]
> Les données personnalisées dans cet exemple sont interprétées comme une chaîne ASCII qui indique « Mes données personnalisées. ».

**Requête**

```bash
curl -H "Metadata:true" "http://169.254.169.254/metadata/instance/compute/customData?api-version=2019-02-01&&format=text" | base64 --decode
```

**Réponse**

```text
My custom data.
```

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Exemples d’appels du service de métadonnées utilisant différents langages à l’intérieur de la machine virtuelle

Langage | Exemple
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Go  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
Puppet | https://github.com/keirans/azuremetadata

## <a name="faq"></a>Questions fréquentes (FAQ)

1. J’obtiens l’erreur `400 Bad Request, Required metadata header not specified`. Qu’est-ce que cela signifie ?
   * Le service de métadonnées d’instance requiert que l’en-tête `Metadata: true` soit transmis dans cette demande. La transmission de cet en-tête dans l’appel REST permet d’accéder au service de métadonnées d’instance.
2. Pourquoi je n’obtiens pas les informations de calcul pour ma machine virtuelle ?
   * Actuellement, le service de métadonnées d’instance prend uniquement en charge les instances créées avec Azure Resource Manager. À l’avenir, il se peut que la prise en charge des machines virtuelles du service cloud soit ajoutée.
3. J’ai créé une machine virtuelle via Azure Resource Manager il y quelque temps déjà. Pourquoi ne puis-je pas voir les informations de métadonnées de calcul ?
   * Pour les machines virtuelles créées après septembre 2016, ajoutez une [balise](../../azure-resource-manager/resource-group-using-tags.md) pour commencer à voir les métadonnées de calcul. Pour une machine virtuelle plus ancienne (créée avant septembre 2016), ajoutez/supprimez des extensions ou des disques de données à la machine virtuelle pour actualiser les métadonnées.
4. Je ne vois pas toutes les données renseignées pour la nouvelle version
   * Pour les machines virtuelles créées après septembre 2016, ajoutez une [balise](../../azure-resource-manager/resource-group-using-tags.md) pour commencer à voir les métadonnées de calcul. Pour une machine virtuelle plus ancienne (créée avant septembre 2016), ajoutez/supprimez des extensions ou des disques de données à la machine virtuelle pour actualiser les métadonnées.
5. Pourquoi l’erreur `500 Internal Server Error` ?
   * Renouvelez votre demande en fonction du système d’interruption exponentiel. Si le problème persiste, contactez le support Azure.
6. Où partager des commentaires/questions supplémentaires ?
   * Envoyez vos commentaires sur https://feedback.azure.com.
7. Cela fonctionne-t-il pour l’instance de groupe de machines virtuelles identiques ?
   * Oui, le service de métadonnées est disponible pour les instances de groupe identique.
8. Comment obtenir un support technique pour le service ?
   * Pour obtenir un support technique pour le service, créez un problème de support dans le portail Azure pour la machine virtuelle sur laquelle vous ne pouvez pas obtenir de réponse de métadonnées après plusieurs tentatives longues.
9. Quand j’appelle le service, un message m’informe que ma demande a expiré.
   * Les appels de métadonnées doivent être effectués à partir de l’adresse IP principale assignée à la carte réseau de la machine virtuelle ; en outre, si vous avez modifié vos itinéraires, un itinéraire doit exister pour l’adresse 169.254.0.0/16 en dehors de votre carte réseau.
10. J’ai mis à jour les balises d’un groupe de machines virtuelles identiques, mais elles ne s’affichent pas dans les instances, contrairement aux machines virtuelles.
    * Les balises de groupe de machines virtuelles identiques s’affichent uniquement dans la machine virtuelle lors d’un redémarrage, d’une réinitialisation ou d’une modification de disque pour l’instance.

    ![Support des métadonnées d’instance](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [événements planifiés](scheduled-events.md)
