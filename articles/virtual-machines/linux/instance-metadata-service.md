---
title: Azure Instance Metadata Service
description: Interface RESTful permettant d’obtenir des informations sur le calcul, le réseau et les événements de maintenance à venir des machines virtuelles.
services: virtual-machines
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/29/2020
ms.author: sukumari
ms.reviewer: azmetadatadev
ms.openlocfilehash: 0d31d982e7788970cbf7aad7dd64db9e6d4b9b10
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86502195"
---
# <a name="azure-instance-metadata-service-imds"></a>Azure Instance Metadata Service (IMDS)

Le service Instance Metadata Service fournit des informations sur les instances de machine virtuelle actuellement en cours d’exécution, et peut être utilisé pour gérer et configurer vos machines virtuelles.
Ces information comprennent la référence SKU, le stockage, les configurations réseau et les événements de maintenance à venir. Pour obtenir la liste complète des données disponibles, consultez [API de métadonnées](#metadata-apis).
Instance Metadata Service est disponible à la fois pour les instances de machine virtuelle et de groupe de machines virtuelles identiques. Le service est uniquement disponible pour l’exécution de machines virtuelles créées/gérées à l’aide [d’Azure Resource Manager](/rest/api/resources/).

Le service IMDS d’Azure est un point de terminaison REST disponible sur une adresse IP non routable bien connue (`169.254.169.254`) ; il est accessible uniquement à partir de la machine virtuelle. La communication entre la machine virtuelle et IMDS ne quitte jamais l’hôte.
Nous vous recommandons de faire en sorte que vos clients HTTP contournent les proxys web au sein de la machine virtuelle lors de l’interrogation d’IMDS et traitent `169.254.169.254` de la même façon que [`168.63.129.16`](../../virtual-network/what-is-ip-address-168-63-129-16.md).

## <a name="security"></a>Sécurité

Le point de terminaison du service de métadonnées d’instance est accessible uniquement à partir de l’instance de machine virtuelle active sur une adresse IP non routable. En outre, toute demande contenant un en-tête `X-Forwarded-For` est rejetée par le service.
Toute demande doit contenir un en-tête `Metadata: true` garantissant que la demande est réellement intentionnelle et ne fait pas partie d’une redirection involontaire.

> [!IMPORTANT]
> Instance Metadata Service n’est pas un canal pour les données sensibles. Le point de terminaison est ouvert à tous les processus sur la machine virtuelle. Les informations exposées par le biais de ce service doivent être considérées comme des informations partagées pour toutes les applications exécutées dans la machine virtuelle.

## <a name="usage"></a>Usage

### <a name="accessing-azure-instance-metadata-service"></a>Accéder à Azure Instance Metadata Service

Pour accéder à Instance Metadata Service, créez une machine virtuelle à partir d’[Azure Resource Manager](/rest/api/resources/) ou du [Portail Azure](https://portal.azure.com), puis suivez les exemples ci-dessous.
Vous trouverez d’avantage d’exemples sur la façon d’interroger IMDS dans [Exemples Azure Instance Metadata](https://github.com/microsoft/azureimds).

Vous trouverez l’exemple de code permettant de récupérer toutes les métadonnées d’une instance ci-dessous, pour accéder à une source de données spécifique, consultez la section [API de métadonnées](#metadata-apis). 

**Requête**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2019-06-01"
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
    "storageProfile": {
        "dataDisks": [
          {
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
          }
        ],
        "imageReference": {
          "id": "",
          "offer": "UbuntuServer",
          "publisher": "Canonical",
          "sku": "16.04.0-LTS",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": "30",
          "diffDiskSettings": {
            "option": "Local"
          },
          "encryptionSettings": {
            "enabled": "false"
          },
          "image": {
            "uri": ""
          },
          "managedDisk": {
            "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
            "storageAccountType": "Standard_LRS"
          },
          "name": "exampleosdiskname",
          "osType": "Linux",
          "vhd": {
            "uri": ""
          },
          "writeAcceleratorEnabled": "false"
        }
    },
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

### <a name="data-output"></a>Sortie des données

Par défaut, le service de métadonnées d’instance retourne des données au format JSON (`Content-Type: application/json`). Toutefois, certaines API sont capables de retourner des données dans différents formats, le cas échéant.
Le tableau suivant répertorie les autres formats de données que les API peuvent prendre en charge.

API | Format de données par défaut | Autres formats
--------|---------------------|--------------
/attested | json | Aucun
/identity | json | Aucun
/instance | json | text
/scheduledevents | json | Aucun

Pour accéder à un format de réponse autre que le format par défaut, spécifiez le format demandé en tant que paramètre de chaîne de requête dans la demande. Par exemple :

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> Pour les nœuds terminaux dans/metadata/instance, `format=json` ne fonctionne pas. Pour ces requêtes, `format=text` doit être spécifié explicitement si le format par défaut est json.

### <a name="versioning"></a>Contrôle de version

La version de Instance Metadata Service est gérée et la spécification de la version de l’API dans la requête HTTP est obligatoire.

Les versions de service prises en charge sont les suivantes : 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15.

Remarque : Lorsqu’une nouvelle version est publiée, son déploiement dans toutes les régions prend du temps. Actuellement, la version 2019-11-01 est toujours en cours de déploiement et n’est peut-être pas disponible dans toutes les régions.

Au fur et à mesure que des versions plus récentes sont ajoutées, les versions antérieures sont toujours accessibles pour des questions de compatibilité si vos scripts ont des dépendances sur des formats de données spécifiques.

Si aucune version n’est spécifiée, une erreur est retournée avec une liste des versions plus récentes prises en charge.

> [!NOTE]
> La réponse est une chaîne JSON. L’exemple suivant indique la condition d’erreur lorsque la version n’est pas spécifiée, la réponse est imprimée pour une meilleure lisibilité.

**Requête**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance"
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

## <a name="metadata-apis"></a>API de métadonnées

Metadata Service contient plusieurs API représentant des sources de données différentes.

API | Description | Version introduite
----|-------------|-----------------------
/attested | Voir [Données attestées](#attested-data) | 2018-10-01
/identity | Consulter [Obtenir un jeton d’accès](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
/instance | Consulter [API Instance](#instance-api) | 2017-04-02
/scheduledevents | Consultez [Événements planifiés](scheduled-events.md). | 2017-08-01

## <a name="instance-api"></a>API Instance

L’API d’instance expose les métadonnées importantes pour les instances de machine virtuelle, notamment la machine virtuelle, le réseau et le stockage. Les catégories suivantes sont accessibles via l’instance/le calcul :

Données | Description | Version introduite
-----|-------------|-----------------------
azEnvironment | Environnement Azure dans lequel s’exécute la machine virtuelle | 2018-10-01
customData | Cette fonctionnalité est actuellement désactivée. Nous mettrons à jour cette documentation dès qu’elle sera disponible. | 2019-02-01
location | Région Azure dans laquelle la machine virtuelle est en cours d’exécution | 2017-04-02
name | Nom de la machine virtuelle | 2017-04-02
offer | Fournit des informations pour l’image de machine virtuelle et ne sont présentes que pour les images déployées à partir de la galerie d’images Azure | 2017-04-02
osType | Linux ou Windows | 2017-04-02
placementGroupId | [Groupe de placement](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) de votre groupe de machines virtuelles identiques | 2017-08-01
Plan | [Plan](/rest/api/compute/virtualmachines/createorupdate#plan) contenant le nom, le produit et l’éditeur d’une machine virtuelle s’il s’agit d’une image de la Place de marché Azure | 2018-04-02
platformUpdateDomain |  [Domaine de mise à jour](manage-availability.md) dans lequel la machine virtuelle est en cours d’exécution | 2017-04-02
platformFaultDomain | [Domaine par défaut](manage-availability.md) dans lequel la machine virtuelle est en cours d’exécution | 2017-04-02
provider | Fournisseur de la machine virtuelle | 2018-10-01
publicKeys | [Collection de clés publiques](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) affectée à la machine virtuelle et aux chemins | 2018-04-02
publisher | Éditeur de l’image de machine virtuelle | 2017-04-02
resourceGroupName | [Groupe de ressources](../../azure-resource-manager/management/overview.md) de votre machine virtuelle | 2017-08-01
resourceId | L’ID [complet](/rest/api/resources/resources/getbyid) de la ressource | 2019-03-11
sku | Référence (SKU) spécifique pour l’image de machine virtuelle | 2017-04-02
storageProfile | Consultez [Profil de stockage](#storage-metadata) | 2019-06-01
subscriptionId | Abonnement Azure pour la machine virtuelle | 2017-08-01
tags | [Étiquettes](../../azure-resource-manager/management/tag-resources.md) de votre machine virtuelle  | 2017-08-01
tagsList | Balises mises en forme en tant que tableau JSON pour faciliter l’analyse programmatique  | 2019-06-04
version | Version de l’image de machine virtuelle | 2017-04-02
vmId | [Identificateur unique](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) de la machine virtuelle | 2017-04-02
vmScaleSetName | [Nom du groupe de machines virtuelles identiques](../../virtual-machine-scale-sets/overview.md) de votre groupe de machines virtuelles identiques | 2017-12-01
vmSize | [Taille de la machine virtuelle](sizes.md) | 2017-04-02
zone | [Zone de disponibilité](../../availability-zones/az-overview.md) de votre machine virtuelle | 2017-12-01

### <a name="sample-1-tracking-vm-running-on-azure"></a>Exemple 1 : Suivi de la machine virtuelle s’exécutant sur Azure

En tant que fournisseur de service, vous aurez peut-être besoin de suivre le nombre de machines virtuelles s’exécutant sur votre logiciel ou de disposer d’agents effectuant le suivi de l’unicité de la machine virtuelle. Pour pouvoir obtenir un ID unique pour une machine virtuelle, utilisez le champ `vmId` du service de métadonnées d’instance.

**Requête**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Réponse**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-containers-data-partitions-based-faultupdate-domain"></a>Exemple 2 : Positionnement du domaine d’erreur/de mise à jour basé sur des conteneurs et des partitions de données

Pour certains scénarios, le positionnement des différents réplicas de données est de première importance. Par exemple, pour le [positionnement du réplica HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) ou pour le positionnement des conteneurs via un [orchestrateur](https://kubernetes.io/docs/user-guide/node-selection/), vous devez connaître les domaines `platformFaultDomain` et `platformUpdateDomain` sur lesquels la machine virtuelle s’exécute.
Vous pouvez également utiliser les [Zones de disponibilité](../../availability-zones/az-overview.md) pour les instances pour prendre ces décisions.
Vous pouvez interroger ces données directement via le service de métadonnées d’instance.

**Requête**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Réponse**

```text
0
```

### <a name="sample-3-getting-more-information-about-the-vm-during-support-case"></a>Exemple 3 : Obtenir plus d’informations sur la machine virtuelle pour le dossier de support

En tant que fournisseur de services, vous recevrez peut-être un appel du support pour lequel vous devrez connaître les informations de la machine virtuelle. Demandez au client de partager les métadonnées de calcul afin que l’équipe du support dispose des informations de base concernant le type de machine virtuelle sur Azure.

**Requête**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2019-06-01"
```

**Réponse**

> [!NOTE]
> La réponse est une chaîne JSON. L’exemple de réponse suivant est imprimé avec soin par souci de lisibilité.

```json
{
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
    "storageProfile": {
        "dataDisks": [
          {
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
          }
        ],
        "imageReference": {
          "id": "",
          "offer": "UbuntuServer",
          "publisher": "Canonical",
          "sku": "16.04.0-LTS",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": "30",
          "diffDiskSettings": {
            "option": "Local"
          },
          "encryptionSettings": {
            "enabled": "false"
          },
          "image": {
            "uri": ""
          },
          "managedDisk": {
            "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
            "storageAccountType": "Standard_LRS"
          },
          "name": "exampleosdiskname",
          "osType": "Linux",
          "vhd": {
            "uri": ""
          },
          "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
}
```

### <a name="sample-4-getting-azure-environment-where-the-vm-is-running"></a>Exemple 4 : Obtenir l’environnement Azure là où s’exécute la machine virtuelle

Azure dispose de plusieurs clouds souverains comme [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Vous avez parfois besoin de l’environnement Azure pour prendre certaines décisions liées au runtime. L’exemple suivant vous montre comment obtenir ce comportement.

**Requête**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Réponse**

```text
AzurePublicCloud
```

Le cloud et les valeurs de l’environnement Azure sont répertoriés ci-dessous.

 Cloud   | Environnement Azure
---------|-----------------
[Toutes les régions Azure globales généralement disponibles](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure Allemagne](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>Métadonnées du réseau 

Les métadonnées du réseau font partie de l’API d’instance. Les catégories de réseau suivantes sont disponibles via le point de terminaison d’instance ou de réseau :

Données | Description | Version introduite
-----|-------------|-----------------------
ipv4/privateIpAddress | Adresse IPv4 locale de la machine virtuelle | 2017-04-02
ipv4/publicIpAddress | Adresse IPv4 publique de la machine virtuelle | 2017-04-02
subnet/address | Adresse de sous-réseau de la machine virtuelle | 2017-04-02
subnet/prefix | Préfixe de sous-réseau, par exemple 24 | 2017-04-02
ipv6/ipAddress | Adresse IPv6 locale de la machine virtuelle | 2017-04-02
macAddress | Adresse MAC de la machine virtuelle | 2017-04-02

> [!NOTE]
> Toutes les réponses de l’API sont des chaînes JSON. Tous les exemples de réponses suivants sont imprimés avec soin par souci de lisibilité.

#### <a name="sample-1-retrieving-network-information"></a>Exemple 1 : Récupération des informations réseau

**Requête**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
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

#### <a name="sample-2-retrieving-public-ip-address"></a>Exemple 2 : Récupération de l’adresse IP publique

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

## <a name="storage-metadata"></a>Métadonnées de stockage

Les métadonnées de stockage font partie de l’API d’instance sous le point de terminaison instance/compute/storageProfile.
Elles peuvent fournir des détails sur les disques de stockage associés à la machine virtuelle. 

Le profil de stockage d’une machine virtuelle est divisé en trois catégories : référence de l’image, disque du système d’exploitation et disques de données.

L’objet de référence d’image contient les informations suivantes sur l’image du système d’exploitation :

Données    | Description
--------|-----------------
id      | ID de ressource
offer   | Offre de la plateforme ou de l’image de la Place de marché
publisher | Éditeur de l’image
sku     | Référence SKU de l’image
version | Version de la plateforme ou de l’image de la Place de marché

L’objet de disque du système d’exploitation contient les informations suivantes sur le disque du système d’exploitation utilisé par la machine virtuelle :

Données    | Description
--------|-----------------
caching | Exigences de la mise en cache
createOption | Informations sur la façon dont la machine virtuelle a été créée
diffDiskSettings | Paramètres du disque éphémère
diskSizeGB | Taille du disque en Go
image   | Disque dur virtuel de l’image utilisateur source
lun     | Numéro d’unité logique du disque
managedDisk | Paramètres des disques managés
name    | Nom du disque
vhd     | Disque dur virtuel
writeAcceleratorEnabled | Indique si writeAccelerator est activé ou non sur le disque

Le tableau des disques de données contient la liste des disques de données attachés à la machine virtuelle. Chaque objet de disque de données contient les informations suivantes :

Données    | Description
--------|-----------------
caching | Exigences de la mise en cache
createOption | Informations sur la façon dont la machine virtuelle a été créée
diffDiskSettings | Paramètres du disque éphémère
diskSizeGB | Taille du disque en Go
encryptionSettings | Paramètres de chiffrement du disque
image   | Disque dur virtuel de l’image utilisateur source
managedDisk | Paramètres des disques managés
name    | Nom du disque
osType  | Type de système d’exploitation inclus dans le disque
vhd     | Disque dur virtuel
writeAcceleratorEnabled | Indique si writeAccelerator est activé ou non sur le disque

L’exemple suivant montre comment interroger les informations de stockage de la machine virtuelle.

**Requête**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
```

**Réponse**

> [!NOTE]
> La réponse est une chaîne JSON. L’exemple de réponse suivant est imprimé avec soin par souci de lisibilité.

```json
{
    "dataDisks": [
      {
        "caching": "None",
        "createOption": "Empty",
        "diskSizeGB": "1024",
        "image": {
          "uri": ""
        },
        "lun": "0",
        "managedDisk": {
          "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
          "storageAccountType": "Standard_LRS"
        },
        "name": "exampledatadiskname",
        "vhd": {
          "uri": ""
        },
        "writeAcceleratorEnabled": "false"
      }
    ],
    "imageReference": {
      "id": "",
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04.0-LTS",
      "version": "latest"
    },
    "osDisk": {
      "caching": "ReadWrite",
      "createOption": "FromImage",
      "diskSizeGB": "30",
      "diffDiskSettings": {
        "option": "Local"
      },
      "encryptionSettings": {
        "enabled": "false"
      },
      "image": {
        "uri": ""
      },
      "managedDisk": {
        "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
        "storageAccountType": "Standard_LRS"
      },
      "name": "exampleosdiskname",
      "osType": "Linux",
      "vhd": {
        "uri": ""
      },
      "writeAcceleratorEnabled": "false"
    }
}
```

## <a name="vm-tags"></a>Balises de machine virtuelle

Les balises de machine virtuelle sont incluses dans l’API d’instance sous le point de terminaison instance/compute/tags.
Des balises peuvent être appliqués à votre machine virtuelle Azure pour les organiser de manière logique dans une taxonomie. Les balises affectées à une machine virtuelle peuvent être récupérées à l’aide de la requête ci-dessous.

**Requête**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Réponse**

```text
Department:IT;Environment:Test;Role:WebRole
```

Le champ `tags` est une chaîne dont les balises sont délimitées par des points-virgules. Cette sortie peut constituer un problème si des points-virgules sont utilisés dans les balises proprement dites. Si un analyseur est écrit pour extraire les balises par programmation, vous devez vous appuyer sur le champ `tagsList`. Le champ `tagsList` est un tableau JSON sans délimiteur et, par conséquent plus facile à analyser.

**Requête**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04"
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

## <a name="attested-data"></a>Données attestées

Une partie du scénario pris en charge par Instance Metadata Service est de garantir que les données fournies proviennent d’Azure. Nous signons une partie de ces informations afin qu’une image de la Place de marché puisse déterminer que c’est l’une de ses instances qui s’exécute sur Azure.

### <a name="sample-1-getting-attested-data"></a>Exemple 1 : Obtenir des données attestées

> [!NOTE]
> Toutes les réponses de l’API sont des chaînes JSON. Les exemples de réponses suivants sont imprimés avec soin par souci de lisibilité.

**Requête**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

La version de l’API est un champ obligatoire. Reportez-vous à la [section utilisation](#usage) pour obtenir les versions d’API prises en charge.
Nonce est une chaîne facultative de 10 chiffres. S’il n’est pas fourni, IMDS retourne à la place l’horodatage UTC actuel.

> [!NOTE]
> En raison du mécanisme de mise en cache d’IMDS, une valeur nonce précédemment mise en cache peut être retournée.

**Réponse**

> [!NOTE]
> La réponse est une chaîne JSON. L’exemple de réponse suivant est imprimé avec soin par souci de lisibilité.

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

L’objet blob de signature est une version signée [pkcs7](https://aka.ms/pkcs7) du document. Il contient le certificat utilisé pour la signature ainsi que les détails de machine virtuelle, comme vmId, sku, nonce, subscriptionId, timeStamp pour la création et l’expiration du document, et les informations du plan sur l’image. Les informations du plan ne sont fournies que pour les images de la Place de marché Azure. Le certificat peut être extrait de la réponse et utilisé pour valider que la réponse est correcte et provient d’Azure.
Le document contient les champs suivants :

Données | Description
-----|------------
nonce | Chaîne qui peut être éventuellement fournie avec la requête. Si aucune valeur nonce n’a été fournie, l’horodatage UTC actuel est utilisé.
plan | [Plan d’image de la Place de marché Azure](/rest/api/compute/virtualmachines/createorupdate#plan). Contient l’ID de plan (name), l’image de produit ou l’offre (product) et l’ID d’éditeur (publisher).
timestamp/createdOn | Horodatage UTC de création du document signé.
timestamp/expiresOn | Horodatage UTC d’expiration du document signé.
vmId |  [Identificateur unique](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) de la machine virtuelle
subscriptionId | Abonnement Azure pour la machine virtuelle, inauguré dans `2019-04-30`
sku | Référence SKU spécifique pour l’image de machine virtuelle, introduite dans `2019-11-01`

### <a name="sample-2-validating-that-the-vm-is-running-in-azure"></a>Exemple 2 : Avoir la garantie que la machine virtuelle s’exécute dans Azure

Les fournisseurs de la Place de marché souhaitent avoir la certitude que la licence de leur logiciel prévoit une exécution sur Azure exclusivement. Si quelqu’un copie le disque dur virtuel localement, ils doivent avoir la possibilité de le détecter. En faisant appel à Instance Metadata Service, les fournisseurs de la Place de marché peuvent obtenir des données signées qui garantissent que la réponse provient d’Azure uniquement.

> [!NOTE]
> Nécessite jq pour être installé.

**Requête**

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2019-04-30" | jq -r '.["signature"]' > signature
# Decode the signature
base64 -d signature > decodedsignature
# Get PKCS7 format
openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
# Get Public key out of pkc7
openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
# Get the intermediate certificate
curl -s -o intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
openssl x509 -inform der -in intermediate.cer -out intermediate.pem
# Verify the contents
openssl smime -verify -in sign.pk7 -inform pem -noverify
```

**Réponse**

```json
Verification successful
{
  "nonce": "20181128-001617",
  "plan":
    {
      "name": "",
      "product": "",
      "publisher": ""
    },
  "timeStamp":
    {
      "createdOn": "11/28/18 00:16:17 -0000",
      "expiresOn": "11/28/18 06:16:17 -0000"
    },
  "vmId": "d3e0e374-fda6-4649-bbc9-7f20dc379f34",
  "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
  "sku": "RS3-Pro"
}
```

Vérifiez que la signature provient de Microsoft Azure, et recherchez les erreurs dans la chaîne de certificats.

```bash
# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

> [!NOTE]
> En raison du mécanisme de mise en cache d’IMDS, une valeur nonce précédemment mise en cache peut être retournée.

La valeur nonce dans le document signé peut être comparée si vous avez fourni un paramètre nonce dans la requête initiale.

> [!NOTE]
> Le certificat est différent pour le cloud public et le cloud souverain.

Cloud | Certificat
------|------------
[Toutes les régions Azure globales généralement disponibles](https://azure.microsoft.com/regions/) | *.metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)          | *.metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)     | *.metadata.azure.cn
[Azure Allemagne](https://azure.microsoft.com/overview/clouds/germany/)                | *.metadata.microsoftazure.de

> [!NOTE]
> Il existe un problème connu au niveau du certificat utilisé pour la signature. Les certificats peuvent ne pas avoir la correspondance exacte `metadata.azure.com` pour le cloud public. Par conséquent, la validation de la certification doit autoriser un nom commun de tout sous-domaine `.metadata.azure.com`.

Lorsque le certificat intermédiaire ne peut pas être téléchargé en raison de contraintes réseau pendant la validation, le certificat intermédiaire peut être épinglé. Azure remplacera toutefois les certificats conformément à la pratique PKI standard. Les certificats épinglés doivent être mis à jour en cas de remplacement. Si une mise à jour du certificat intermédiaire est planifiée, le blog Azure sera mis à jour et les clients Azure en seront informés. Vous pouvez trouver les certificats intermédiaires [ici](https://www.microsoft.com/pki/mscorp/cps/default.htm). Les certificats intermédiaires de chaque région peuvent être différents.

> [!NOTE]
> Le certificat intermédiaire pour Azure Chine 21Vianet sera issu de l’autorité de certification racine globale DigiCert au lieu de Baltimore.
De même, si vous avez épinglé les certificats intermédiaires pour Azure Chine dans le cadre du changement de l’autorité de chaîne racine, les certificats intermédiaires doivent être mis à jour.

## <a name="managed-identity-via-metadata-service"></a>Identité gérée via Metadata Service

Une identité managée affectée par le système peut être activée sur la machine virtuelle, ou une ou plusieurs identités managées affectées par l’utilisateur peuvent être attribuées à la machine virtuelle.
Les jetons pour les identités managées peuvent ensuite être demandés à Instance Metadata Service. Ces jetons peuvent être utilisés pour s’authentifier auprès d’autres services Azure, tels qu’Azure Key Vault.

Pour plus d’informations sur la procédure d’activation de cette fonctionnalité, consultez [Obtenir un jeton d’accès](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="scheduled-events-via-metadata-service"></a>Scheduled Events via Metadata Service
Vous pouvez obtenir l’état des événements planifiés via le service de métadonnées, puis l’utilisateur peut spécifier un ensemble d’actions à exécuter sur ces événements.  Pour plus d’informations, consultez [Scheduled Events](scheduled-events.md). 

## <a name="regional-availability"></a>Disponibilité régionale

Le service est **en disponibilité générale** dans tous les clouds Azure.

## <a name="sample-code-in-different-languages"></a>Exemple de code dans différents langages

Exemples d’appels du service de métadonnées utilisant différents langages à l’intérieur de la machine virtuelle :

Langage      | Exemple
--------------|----------------
Bash          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
C#            | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Go            | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Java          | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
NodeJS        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Perl          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
PowerShell    | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Puppet        | https://github.com/keirans/azuremetadata
Python        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
Ruby          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="error-and-debugging"></a>Erreur et débogage

S’il existe un élément de données introuvable ou une requête mal formée, le service de métadonnées d’instance retourne des erreurs HTTP standard. Par exemple :

Code d’état HTTP | Motif
----------------|-------
200 OK |
400 Demande incorrecte | En-tête `Metadata: true` manquant ou paramètre `format=json` manquant lors de l’interrogation d’un nœud terminal
404 Introuvable | L’élément demandé n’existe pas
405 Méthode non autorisée | Seules les requêtes `GET` sont prises en charge
410 Supprimé | Recommencez l’opération dans un délai maximum de 70 secondes
429 Trop de demandes | L’API prend actuellement en charge un maximum de 5 requêtes par seconde
500 Erreur de service     | Recommencez l’opération plus tard

### <a name="known-issues-and-faq"></a>Problèmes connus et FAQ

1. J’obtiens l’erreur `400 Bad Request, Required metadata header not specified`. Qu’est-ce que cela signifie ?
   * Le service de métadonnées d’instance requiert que l’en-tête `Metadata: true` soit transmis dans cette demande. La transmission de cet en-tête dans l’appel REST permet d’accéder au service de métadonnées d’instance.
1. Pourquoi je n’obtiens pas les informations de calcul pour ma machine virtuelle ?
   * Actuellement, le service de métadonnées d’instance prend uniquement en charge les instances créées avec Azure Resource Manager. À l’avenir, il se peut que la prise en charge des machines virtuelles du service cloud soit ajoutée.
1. J’ai créé une machine virtuelle via Azure Resource Manager il y quelque temps déjà. Pourquoi ne puis-je pas voir les informations de métadonnées de calcul ?
   * Pour les machines virtuelles créées après septembre 2016, ajoutez une [balise](../../azure-resource-manager/management/tag-resources.md) pour commencer à voir les métadonnées de calcul. Pour une machine virtuelle plus ancienne (créée avant septembre 2016), ajoutez/supprimez des extensions ou des disques de données aux instances de machine virtuelle pour actualiser les métadonnées.
1. Je ne vois pas toutes les données renseignées pour la nouvelle version
   * Pour les machines virtuelles créées après septembre 2016, ajoutez une [balise](../../azure-resource-manager/management/tag-resources.md) pour commencer à voir les métadonnées de calcul. Pour une machine virtuelle plus ancienne (créée avant septembre 2016), ajoutez/supprimez des extensions ou des disques de données aux instances de machine virtuelle pour actualiser les métadonnées.
1. Pourquoi l’erreur `500 Internal Server Error` ou `410 Resource Gone` s’affiche-t-elle ?
   * Réessayez votre requête en fonction du système d’interruption exponentielle ou d’autres méthodes décrites dans [Gestion des erreurs temporaires](/azure/architecture/best-practices/transient-faults). Si le problème persiste, créez un ticket de support dans le portail Azure pour la machine virtuelle.
1. Cela fonctionne-t-il pour des instances de groupes de machines virtuelles identiques ?
   * Oui, le service de métadonnées est disponible pour les instances de groupes identiques.
1. J’ai mis à jour les balises des groupes de machines virtuelles identiques, mais elles ne s’affichent pas dans les instances, contrairement aux machines virtuelles à instance unique.
   * Actuellement, les balises de groupes de machines virtuelles identiques s’affichent uniquement sur la machine virtuelle lors d’un redémarrage, d’une réinitialisation ou d’une modification de disque pour l’instance.
1. Quand j’appelle le service, un message m’informe que ma demande a expiré.
   * Les appels de métadonnées doivent être effectués à partir de l’adresse IP principale affectée à la carte réseau principale de la machine virtuelle. En outre, dans le cas où vous avez modifié vos routes, il doit y avoir une route pour l’adresse 169.254.169.254/32 dans la table de routage locale de votre machine virtuelle.
   * <details>
        <summary>Vérification de votre table de routage</summary>

        1. Videz votre table de routage locale à l’aide d’une commande telle que `netstat -r` et recherchez l’entrée IMDS (p. ex.) :
            ```console
            ~$ netstat -r
            Kernel IP routing table
            Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
            default         _gateway        0.0.0.0         UG        0 0          0 eth0
            168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
            169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
            172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
            ```
        1. Vérifiez qu’il existe une route pour `169.254.169.254` et notez l’interface réseau correspondante (par exemple `eth0`).
        1. Videz la configuration d’interface pour l’interface correspondante dans la table de routage (notez que le nom exact du fichier de configuration peut varier).
            ```console
            ~$ cat /etc/netplan/50-cloud-init.yaml
            network:
            ethernets:
                eth0:
                    dhcp4: true
                    dhcp4-overrides:
                        route-metric: 100
                    dhcp6: false
                    match:
                        macaddress: 00:0d:3a:e4:c7:2e
                    set-name: eth0
            version: 2
            ```
        1. Si vous utilisez une adresse IP dynamique, notez l’adresse MAC. Si vous utilisez une adresse IP statique, vous pouvez noter la ou les adresses IP répertoriées et/ou l’adresse MAC.
        1. Vérifiez que l’interface correspond à la carte réseau principale et à l’adresse IP principale de la machine virtuelle. Vous pouvez trouver la carte réseau/adresse IP principale en examinant la configuration du réseau dans le portail Azure ou en la recherchant [avec Azure CLI](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-show). Notez les adresses IP publiques et privées (et l’adresse MAC si vous utilisez l’interface CLI). Exemple CLI PowerShell :
            ```powershell
            $ResourceGroup = '<Resource_Group>'
            $VmName = '<VM_Name>'
            $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
            foreach($NicName in $NicNames)
            {
                $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
                Write-Host $NicName, $Nic.primary, $Nic.macAddress
            }
            # Output: ipexample606 True 00-0D-3A-E4-C7-2E
            ```
        1. Si elles ne correspondent pas, mettez à jour la table de routage afin que la carte réseau/adresse IP principale soit ciblée.
    </details>

## <a name="support-and-feedback"></a>Support et commentaires

Envoyez vos commentaires sur https://feedback.azure.com.

Pour obtenir un support technique pour le service, créez un problème de support dans le portail Azure pour la machine virtuelle sur laquelle vous ne pouvez pas obtenir de réponse de métadonnées après plusieurs tentatives longues.
Sélectionnez `Management` comme type de problème et `Instance Metadata Service` comme catégorie.

![Support Instance Metadata](./media/instance-metadata-service/InstanceMetadata-support.png "Capture d’écran : Ouverture d’une demande de support en cas de problème avec Instance Metadata Service")

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus :
1. [Acquérir un jeton d’accès pour la machine virtuelle](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).
1. [Scheduled Events](scheduled-events.md)
