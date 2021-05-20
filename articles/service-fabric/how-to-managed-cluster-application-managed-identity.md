---
title: Configurer et utiliser l’identité managée de l’application sur des nœuds de cluster managé Service Fabric
description: Découvrez comment configurer et utiliser une identité managée d’application sur un cluster managé Azure Service Fabric déployé à partir d’un modèle ARM.
ms.topic: how-to
ms.date: 5/10/2021
ms.openlocfilehash: f3c76ae6418ddf2ca9da0ed620941c10b7061603
ms.sourcegitcommit: b35c7f3e7f0e30d337db382abb7c11a69723997e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109689192"
---
# <a name="deploy-a-service-fabric-application-with-managed-identity"></a>Déployer une application Service Fabric avec une identité managée

Pour déployer une application Service Fabric avec une identité managée, l’application doit être déployée via Azure Resource Manager, généralement avec un modèle Azure Resource Manager. Pour plus d’informations sur le déploiement d’une application Service Fabric via Azure Resource Manager, consultez [Gérer des applications et services en tant que ressources Azure Resource Manager](service-fabric-application-arm-resource.md).

> [!NOTE] 
> 
> Les applications qui ne sont pas déployées en tant que ressource Azure **ne peuvent pas avoir** d’identités managées. 
>
> Le déploiement d’applications Service Fabric avec une identité managée est pris en charge avec la version d’API `"2021-05-01"` sur les clusters managés.

Des exemples de modèles de cluster managé sont disponibles ici : [Service Fabric managed cluster templates](https://github.com/Azure-Samples/service-fabric-cluster-templates).

## <a name="managed-identity-support-in-service-fabric-managed-cluster"></a>Prise en charge des identités managées dans un cluster managé Service Fabric

Quand une application Service Fabric est configurée avec des [identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md) et déployée sur le cluster, cela déclenche la configuration automatique du *service de jetons d’identité managée* sur le cluster managé Service Fabric. Ce service est responsable de l’authentification des applications Service Fabric à l’aide de leurs identités managées et de l’obtention de jetons d’accès en leur nom. Une fois le service activé, vous pouvez le voir dans Service Fabric Explorer sous la section **Système** dans le volet de gauche, exécuté sous le nom **fabric:/System/ManagedIdentityTokenService**.

>[!NOTE]
>La première fois qu’une application est déployée avec des identités managées, vous devez vous attendre à un déploiement ponctuel plus long en raison de la modification automatique de la configuration du cluster. Cela peut prendre de 15 minutes pour un cluster zonal à 45 minutes pour un cluster couvrant plusieurs zones. Si d’autres déploiements sont en cours, la configuration MITS attendra que ceux-ci se terminent en premier.

Les ressources d’application prennent en charge l’attribution SystemAssigned ou UserAssigned, et l’attribution peut être effectuée comme indiqué dans l’extrait de code ci-dessous.

```json
{
  "type": "Microsoft.ServiceFabric/managedclusters/applications",
  "apiVersion": "2021-05-01",
  "identity": {
    "type": "SystemAssigned",
    "userAssignedIdentities": {}
  },
}

```
[Référence JSON complète](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2021-05-01/managedclusters/applications?tabs=json)

## <a name="user-assigned-identity"></a>Identité attribuée par l’utilisateur

Pour activer l’application avec une identité attribuée par l’utilisateur, ajoutez tout d’abord la propriété **identity** à la ressource d’application avec le type **userAssigned** et les identités attribuées par l’utilisateur référencées. Ajoutez ensuite une section **managedIdentities** dans la section **properties** de la ressource **application** qui contient une liste de noms conviviaux pour le mappage de principalId de chacune des identités attribuées par l’utilisateur. Pour plus d’informations sur les identités attribuées par l’utilisateur, consultez [Créer, lister ou supprimer une identité managée attribuée par l’utilisateur](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md).

### <a name="application-template"></a>Modèle d’application

Pour activer l’application avec l’identité attribuée par l’utilisateur, ajoutez tout d’abord la propriété **identity** à la ressource d’application avec le type **userAssigned** et les identités attribuées par l’utilisateur référencées, puis ajoutez un objet **managedIdentities** dans la section **properties** qui contient une liste de noms conviviaux pour le mappage de principalId de chacune des identités attribuées par l’utilisateur.

```json
{
  "apiVersion": "2021-05-01",
  "type": "Microsoft.ServiceFabric/managedclusters/applications",
  "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[parameters('applicationVersion')]",
    "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
  ],
  "identity": {
    "type" : "userAssigned",
    "userAssignedIdentities": {
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]": {}
    }
  },
  "properties": {
    "version": "[parameters('applicationVersion')]",
    "parameters": {
    },
    "managedIdentities": [
      {
        "name" : "[parameters('userAssignedIdentityName')]",
        "principalId" : "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName')), '2018-11-30').principalId]"
      }
    ]
  }
}
```

Dans l’exemple ci-dessus, le nom de ressource de l’identité attribuée par l’utilisateur est utilisé comme nom convivial de l’identité managée pour l’application. Les exemples suivants supposent que le nom convivial réel est « AdminUser ».

### <a name="application-package"></a>Package d’application

1. Pour chaque identité définie dans la section `managedIdentities` du modèle Azure Resource Manager, ajoutez une balise `<ManagedIdentity>` dans le manifeste de l’application sous la section **Principals**. L’attribut `Name` doit correspondre à la propriété `name` définie dans la section `managedIdentities`.

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. Dans la section **ServiceManifestImport**, ajoutez une **IdentityBindingPolicy** pour le service qui utilise l’identité managée. Cette stratégie mappe l’identité `AdminUser` à un nom d’identité spécifique au service qui doit être ajouté ultérieurement dans le manifeste de service.

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Mettez à jour le manifeste de service pour ajouter une **ManagedIdentity** à l’intérieur de la section **Ressources** avec le nom correspondant à `ServiceIdentityRef` dans `IdentityBindingPolicy` du manifeste de l’application :

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="system-assigned-managed-identity"></a>Identité managée affectée par le système

### <a name="application-template"></a>Modèle d’application

Pour activer l’application avec une identité managée attribuée par le système, ajoutez la propriété **identity** à la ressource d’application, avec le type **systemAssigned** comme indiqué dans l’exemple ci-dessous :

```json
    {
      "apiVersion": "2021-05-01",
      "type": "Microsoft.ServiceFabric/managedclusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
      ],
      "identity": {
        "type" : "systemAssigned"
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        }
      }
    }
```
Cette propriété déclare, pour Azure Resource Manager, et l’identité managée et les fournisseurs de ressources Service Fabric, respectivement, que cette ressource doit avoir une identité managée (`system assigned`) implicite.

### <a name="application-and-service-package"></a>Package d’application et de service

1. Mettez à jour le manifeste de l’application pour ajouter un élément **ManagedIdentity** dans la section **Principaux**, contenant une seule entrée comme indiqué ci-dessous :

    **ApplicationManifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    Cela mappe l’identité assignée à l’application en tant que ressource avec un nom convivial, pour une attribution ultérieure aux services qui composent l’application. 

2. Dans la section **ServiceManifestImport** correspondant au service auquel est affectée l’identité managée, ajoutez un élément **IdentityBindingPolicy**, comme indiqué ci-dessous :

    **ApplicationManifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    Cet élément assigne l’identité de l’application au service; sans cette attribution, le service ne sera pas en mesure d’accéder à l’identité de l’application. Dans l’extrait de code ci-dessus, l’identité `SystemAssigned` (qui est un mot clé réservé) est mappée à la définition du service sous le nom convivial `WebAdmin`.

3. Mettez à jour le manifeste de service pour ajouter un élément **ManagedIdentity** à l’intérieur de la section **Ressources** avec le nom correspondant à la valeur du paramètre `ServiceIdentityRef` de la définition `IdentityBindingPolicy` dans le manifeste de l’application :

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    Il s’agit du mappage équivalent d’une identité à un service, comme décrit ci-dessus, mais du point de vue de la définition de service. L’identité est référencée ici par son nom convivial (`WebAdmin`), comme déclaré dans le manifeste de l’application.

## <a name="next-steps"></a>Étapes suivantes

* [Tirer parti de l’identité managée d’une application Service Fabric à partir du code de service](./how-to-managed-identity-service-fabric-app-code.md)
* [Accorder à une application Azure Service Fabric l’accès à d’autres ressources Azure](./how-to-grant-access-other-resources.md)
