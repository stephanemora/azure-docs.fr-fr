---
title: 'Azure Service Fabric : configurer un cluster Azure Service Fabric existant pour activer le support des identités managées'
description: Cet article explique comment configurer un cluster Azure Service Fabric existant pour activer le support des identités managées
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 13b8b38a206b0dae0877263a5cda56a134d4788d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75351603"
---
# <a name="configure-an-existing-azure-service-fabric-cluster-to-enable-managed-identity-support-preview"></a>Configurer un cluster Azure Service Fabric existant pour activer la prise en charge d’identités managées (préversion)
Afin d’accéder à la fonctionnalité des identités managées pour les applications Azure Service Fabric, vous devez d’abord activer le **Service de jetons des identités managées** sur le cluster. Ce service est responsable de l’authentification des applications Service Fabric à l’aide de leurs identités managées et de l’obtention de jetons d’accès en leur nom. Une fois le service activé, vous pouvez le voir dans Service Fabric Explorer sous la section **Système** dans le volet de gauche, exécuté sous le nom **fabric:/System/ManagedIdentityTokenService**.

> [!NOTE]
> Le runtime Service Fabric version 6.5.658.9590 ou une version ultérieure est requis pour activer le **Service de jetons des identités managées**.  
> 
> Vous pouvez rechercher la version Service Fabric d’un cluster à partir du Portail Azure en ouvrant la ressource de cluster et en vérifiant la propriété **version Fabric version** dans la section **Essentials**.
> 
> Si le cluster est en mode de mise à niveau **Manuelle**, vous devez d’abord le mettre à niveau vers 6.5.658.9590 ou une version ultérieure.


## <a name="enable-the-managed-identity-token-service-in-an-existing-cluster"></a>Activer le Service de jetons des identités managées dans un cluster existant
Pour activer le Service de jetons des identités managées dans un cluster existant, vous devez lancer une mise à niveau du cluster en spécifiant deux modifications : activation du Service de jetons des identités managées et requête de redémarrage de chaque nœud. Pour ce faire, ajoutez les deux extraits de code suivants dans le modèle Azure Resource Manager :

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

Pour que les modifications prennent effet, vous devez également modifier la stratégie de mise à niveau pour spécifier un redémarrage forcé du runtime Service Fabric sur chaque nœud à mesure que la mise à niveau progresse au sein du cluster. Ce redémarrage garantit que le service système qui vient d’être activé est démarré et en cours d’exécution sur chaque nœud. Dans l’extrait de code ci-dessous, `forceRestart` est le paramètre essentiel ; utilisez vos valeurs existantes pour les autres paramètres.  

```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```

> [!NOTE]
> À la fin de la mise à niveau, n’oubliez pas de restaurer le paramètre `forceRestart` pour réduire l’impact des mises à niveau suivantes. 

## <a name="errors-and-troubleshooting"></a>Erreurs et résolution des problèmes

Si le déploiement échoue avec le message suivant, cela signifie que le cluster ne s’exécute pas sur une version de Service Fabric suffisamment élevée :

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Étapes suivantes
* [Déployer une application Azure Service Fabric avec une identité managée attribuée par le système](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Déployer une application Azure Service Fabric avec une identité managée attribuée par l’utilisateur](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Tirer parti de l’identité managée d’une application Service Fabric à partir du code de service](./how-to-managed-identity-service-fabric-app-code.md)
* [Accorder à une application Azure Service Fabric l’accès à d’autres ressources Azure](./how-to-grant-access-other-resources.md)