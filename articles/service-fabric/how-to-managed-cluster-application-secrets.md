---
title: Utiliser des secrets d’application dans des clusters managés Service Fabric
description: En savoir plus sur les secrets d’application Azure Service Fabric et sur la façon de collecter les informations nécessaires pour une utilisation dans des clusters managés
ms.topic: how-to
ms.date: 5/10/2021
ms.openlocfilehash: 820fb2a116ba5343a2f2126950a7f5d5896ddee3
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111950129"
---
# <a name="use-application-secrets-in-service-fabric-managed-clusters"></a>Utiliser des secrets d’application dans des clusters managés Service Fabric

Les secrets peuvent être des informations sensibles quelconques, notamment des chaînes de connexion de stockage, des mots de passe ou d’autres valeurs qui ne doivent pas être traitées en texte brut. Cet article utilise Azure Key Vault pour gérer les clés et les secrets, comme c’est le cas pour les clusters managés Service Fabric. Toutefois, *l’utilisation de* secrets dans une application cloud est indépendante de la plateforme et permet ainsi un déploiement d’applications dans un cluster hébergé à n’importe quel endroit.

La méthode recommandée pour gérer les paramètres de configuration de service consiste à utiliser des [packages de configuration de service][config-package]. Les versions des packages de configuration sont gérées et peuvent être mises à jour par le biais de mises à niveau propagées gérées avec validation de l’intégrité et la restauration automatique. Cette option est préférable à une configuration globale, car elle réduit le risque d’interruption de service globale. Les secrets chiffrés ne font pas exception. Service Fabric dispose de fonctionnalités intégrées pour chiffrer et déchiffrer des valeurs dans un fichier de package de configuration Settings.xml à l’aide du cryptage de certificat.

Le diagramme suivant illustre le flux de base pour la gestion des secrets dans une application Service Fabric :

![vue d’ensemble de la gestion des secrets][overview]

Ce flux comprend quatre étapes principales :

1. Obtenez un certificat de chiffrement de données.
2. Installez le certificat dans votre cluster.
3. Chiffrez les valeurs secrètes lors du déploiement d’une application avec le certificat et injectez-les dans le fichier de configuration Settings.xml d’un service.
4. Lisez les valeurs chiffrées dans Settings.xml en les déchiffrant avec le même certificat de chiffrement. 

[Azure Key Vault][key-vault-get-started] est utilisé ici comme un emplacement de stockage sécurisé pour les certificats et comme un moyen d’installer des certificats sur les nœuds de cluster managé Service Fabric dans Azure.

Pour obtenir un exemple de la façon d’implémenter des secrets d’application, consultez [Gérer les secrets d’application](service-fabric-application-secret-management.md).

Nous prenons également prendre en charge [KeyVaultReference](service-fabric-keyvault-references.md). La prise en charge de KeyVaultReference de Service Fabric facilite le déploiement de secrets dans vos applications en référençant simplement l’URL du secret qui est stocké dans Key Vault

## <a name="create-a-data-encipherment-certificate"></a>Créer un certificat de chiffrement de données
Pour créer votre propre coffre de clés et configurer des certificats, suivez les instructions d’Azure Key Vault avec [Azure CLI, PowerShell, le portail et plus encore][key-vault-certs].

>[!NOTE]
> Le coffre de clés doit être [activé pour le déploiement de modèle](../key-vault/general/manage-with-cli2.md#bkmk_KVperCLI) afin d’autoriser le fournisseur de ressources de calcul à obtenir des certificats à partir de ce coffre de clés et à les installer sur des nœuds de cluster.

## <a name="install-the-certificate-in-your-cluster"></a>Installation du certificat dans votre cluster
Ce certificat doit être installé sur chaque nœud du cluster et les clusters managés Service Fabric facilitent cette opération. Le service de cluster managé peut envoyer des secrets spécifiques à la version aux nœuds pour vous permettre d’installer des secrets qui ne changeront pas souvent, comme l’installation d’une autorité de certification racine privée sur les nœuds. Pour la plupart des charges de travail de production, nous vous suggérons d’utiliser l’[extension KeyVault][key-vault-windows]. L’extension de machine virtuelle Key Vault assure l’actualisation automatique des certificats stockés dans un coffre de clés Azure par rapport à une version statique.

Pour les clusters managés, vous avez besoin de trois valeurs : deux pour Azure Key Vault et une pour le nom de magasin local sur les nœuds.

Paramètres : 
* Coffre source : Il s’agit de cet 
    * exemple : /subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1
* URL de certificat : Il s’agit de l’identificateur d’objet complet. Elle n’est pas sensible à la casse et est immuable
    * https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}
* Magasin de certificats : Il s’agit du magasin de certificats local sur les nœuds où le certificat sera placé
    * nom du magasin de certificats sur les nœuds ; par exemple : « MY »

Les clusters managés Service Fabric prennent en charge deux méthodes pour ajouter des secrets spécifiques à la version à vos nœuds.

1. Le portail uniquement lors de la création initiale du cluster, en insérant les valeurs ci-dessus dans cette zone :

![entrée de secrets à partir du portail][sfmc-secrets]

2. Azure Resource Manager lors de la création ou à tout moment

```json
{
    "apiVersion": "2021-05-01",
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "properties": {
        "vmSecrets": [
          {
            "sourceVault": {
              "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
            },
            "vaultCertificates": [
              {
                "certificateStore": "MY",
                "certificateUrl": "https://mykeyvault1.vault.azure.net/certificates/{certificatename}/{secret-version}"
              }
            ]
          }
        ]
    }    
}
```


<!-- Links -->
[key-vault-get-started]:../key-vault/general/overview.md
[key-vault-certs]: ../key-vault/certificates/quick-create-cli.md
[config-package]: service-fabric-application-and-service-manifests.md
[key-vault-windows]: ../virtual-machines/extensions/key-vault-windows.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png
[sfmc-secrets]:./media/how-to-managed-cluster-application-secrets/sfmc-secrets.png