---
title: Contrôle d’accès pour la supervision à distance - Azure | Microsoft Docs
description: Cet article fournit des informations sur la façon dont vous pouvez configurer le contrôle d’accès en fonction du rôle (RBAC) dans l’accélérateur de solution de supervision à distance.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: 817bc7624bb3a6b69d56265e40681287b7fc09fa
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969585"
---
# <a name="configure-role-based-access-control-in-the-remote-monitoring-solution-accelerator"></a>Configurer le contrôle d’accès en fonction du rôle dans l’accélérateur de solution de supervision à distance

Cet article fournit des informations sur la façon de configurer le contrôle d’accès en fonction du rôle dans l’accélérateur de solution de supervision à distance. Le contrôle d’accès en fonction du rôle vous permet de limiter l’accès de certains utilisateurs à des fonctionnalités spécifiques de la solution.

## <a name="default-settings"></a>Paramètres par défaut

Quand vous déployez la solution de supervision à distance pour la première fois, il existe deux rôles : **Administrateur** et **Lecture seule**.

Tout utilisateur ayant le rôle **administrateur** bénéficie d’un accès total à la solution, avec les autorisations suivantes ci-dessous. Un utilisateur ayant le rôle **lecture seule** a uniquement accès pour afficher la solution.

| Autorisation            | Admin | Lecture seule |
|----------------       |-------|-----------|
| Afficher la solution         | Oui   | Oui       |
| Mettre à jour des alarmes         | Oui   | Non        |
| Supprimer des alarmes         | Oui   | Non        |
| Créer des appareils        | Oui   | Non        |
| Mettre à jour des appareils        | Oui   | Non        |
| Supprimer des appareils        | Oui   | Non        |
| Créer des groupes d’appareils  | Oui   | Non        |
| Mettre à jour des groupes d’appareils  | Oui   | Non        |
| Supprimer des groupes d’appareils  | Oui   | Non        |
| Création de règles          | Oui   | Non        |
| Mettre à jour des règles          | Oui   | Non        |
| Supprimer des règles          | Oui   | Non        |
| Créer des tâches           | Oui   | Non        |
| Mettre à jour la gestion SIM | Oui   | Non        |

Par défaut, l’utilisateur qui a déployé la solution se voit automatiquement assigner le rôle **administrateur** et est propriétaire de l’application Azure Active Directory. En tant que propriétaire de l’application, vous pouvez assigner des rôles à d’autres utilisateurs par le biais du portail Azure. Si vous souhaitez qu’un autre utilisateur assigne des rôles dans la solution, il doit également être défini en tant que propriétaire de l’application dans le Portail Azure.

> [!NOTE]
> L’utilisateur qui a déployé la solution est la **seule personne** qui peut la voir immédiatement après la création. Pour accorder à d’autres utilisateurs un accès pour afficher l’application dans le cadre d’un rôle lecture seule, administrateur ou personnalisé, consultez les instructions suivantes ci-dessous sur l’ajout ou la suppression d’utilisateurs.

## <a name="add-or-remove-users"></a>Ajouter ou supprimer des utilisateurs

En tant que propriétaire d’une application Azure Active Directory, vous pouvez utiliser le portail Azure pour ajouter ou supprimer un utilisateur dans un rôle à partir de la solution de supervision à distance. Dans les étapes suivantes, nous utilisons [l’application d’entreprise Azure Active Directory](../active-directory/manage-apps/view-applications-portal.md) créée quand vous avez déployé la solution de supervision à distance.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Vérifiez que [l’utilisateur se trouve dans le répertoire](../active-directory/fundamentals/add-users-azure-active-directory.md) que vous utilisez. Vous avez choisi le répertoire à utiliser lors de votre connexion au site [Accélérateurs de solution Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators). Le nom du répertoire est indiqué dans l’angle supérieur droit de la [page](https://www.azureiotsolutions.com/Accelerators).

1. Recherchez **l’application d’entreprise** pour votre solution dans le Portail Azure. Ensuite, filtrez la liste en définissant **Type d’application** sur **Toutes les applications**. Recherchez votre application par son nom. Le nom de l’application est celui de votre solution de supervision à distance. Dans la capture d’écran suivante, le nom d’affichage de la solution et de l’application est **contoso-rm4**.

    ![Application d’entreprise](media/iot-accelerators-remote-monitoring-rbac/appregistration.png)

1. Vérifiez que vous êtes propriétaire de l’application en cliquant sur l’application, puis sur **Propriétaires**. Dans la capture d’écran suivante, **Contoso admin** est propriétaire de l’application **contoso-rm4** :

    ![Capture d'écran sur laquelle l'option Gérer les propriétaires est sélectionnée et affichant les propriétaires de l'application contoso r m 4.](media/iot-accelerators-remote-monitoring-rbac/owners.png)

    Si vous n’êtes pas propriétaire, vous devez demander à un propriétaire existant de vous ajouter à la liste. Seuls les propriétaires peuvent assigner des rôles d’application tels que les rôles **administrateur** ou **lecture seule** à d’autres utilisateurs.

1. Pour afficher la liste des utilisateurs auxquels des rôles sont assignés dans l’application, cliquez sur **Users and groups** (Utilisateurs et groupes).

1. Pour ajouter un utilisateur, cliquez sur **+ Ajouter un utilisateur**, puis sur **Users and groups, None Selected** (Utilisateurs et groupes, Aucune sélection) pour sélectionner un utilisateur à partir du répertoire.

1. Pour assigner un rôle à l’utilisateur, cliquez sur **Sélectionner un rôle, Aucune sélection** et choisissez le rôle **Administrateur** ou **Read Only** (Lecture seule) pour l’utilisateur. Cliquez sur **Sélectionner**, puis sur **Attribuer**.

    ![Sélectionner un rôle](media/iot-accelerators-remote-monitoring-rbac/selectrole.png)

1. L’utilisateur peut désormais accéder à la solution de supervision à distance avec les autorisations définies par le rôle.

1. Vous pouvez supprimer des utilisateurs de l’application dans la page **Users and groups** (Utilisateurs et groupes) du portail.

## <a name="create-a-custom-role"></a>Créer un rôle personnalisé

La solution de supervision à distance inclut les rôles **administrateur** et **lecture seule** lors de son déploiement. Vous pouvez ajouter des rôles personnalisés avec différents ensembles d’autorisations. Pour définir un rôle personnalisé, vous devez :

- Ajouter un nouveau rôle à l’application dans le Portail Azure
- Définir une stratégie pour le nouveau rôle dans le microservice d’authentification et d’autorisation
- Mettre à jour l’interface utilisateur web de la solution

### <a name="define-a-custom-role-in-the-azure-portal"></a>Définir un rôle personnalisé dans le Portail Azure

Les étapes suivantes décrivent comment ajouter un rôle à une application dans Azure Active Directory. Dans cet exemple, vous créez un rôle qui permet aux membres de créer, mettre à jour et supprimer des appareils dans la solution de supervision à distance.

1. Recherchez **l’inscription d’application** pour votre solution dans le Portail Azure. Le nom de l’application est celui de votre solution de supervision à distance. Dans la capture d’écran suivante, le nom d’affichage de la solution et de l’application est **contoso-rm4**.

    ![Inscription d'application](media/iot-accelerators-remote-monitoring-rbac/app-registration-2.png)

1. Sélectionnez votre application, puis cliquez sur **Manifeste**. Vous pouvez voir les deux [rôles d’application](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) existants définis pour l’application :

    ![Affichage du manifeste](media/iot-accelerators-remote-monitoring-rbac/view-manifest.png)

1. Modifiez le manifeste pour ajouter un rôle appelé **ManageDevices** comme indiqué dans l’extrait de code suivant. Vous avez besoin d’une chaîne unique pour le nouvel ID de rôle, par exemple un GUID. Vous pouvez générer un nouveau GUID à l’aide d’un service tel que le [générateur de GUID en ligne](https://www.guidgenerator.com/) :

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Admin",
        "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "isEnabled": true,
        "description": "Administrator access to the application",
        "value": "Admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Read Only",
        "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "isEnabled": true,
        "description": "Read only access to device information",
        "value": "ReadOnly"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "ManageDevices",
        "id": "ADD A NEW GUID HERE",
        "isEnabled": true,
        "description": "A new role for the application.",
        "value": "ManageDevices"
      }
    ],
    ```

    Enregistrez les modifications.

### <a name="define-a-policy-for-the-new-role"></a>Définir une stratégie pour le nouveau rôle

Après avoir ajouté le rôle à l’application dans le Portail Azure, vous devez définir une stratégie dans [roles.json](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/auth/Services/data/policies/roles.json) pour le rôle, qui assigne les autorisations nécessaires à la gestion des appareils.

1. Clonez le référentiel [Microservices de supervision à distance](https://github.com/Azure/remote-monitoring-services-dotnet) de GitHub à votre ordinateur local.

1. Modifiez le fichier **auth/Services/data/policies/roles.json** afin d’ajouter la stratégie pour le rôle **ManageDevices** comme indiqué dans l’extrait de code suivant. Les valeurs **ID** et **Role** doivent correspondre à la définition de rôle du manifeste d’application de la section précédente. La liste des actions autorisées permet à un utilisateur ayant le rôle **ManageDevices** de créer, mettre à jour et supprimer des appareils connectés à la solution :

    ```json
    {
    "Items": [
      {
        "Id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "Role": "admin",
        "AllowedActions": [
          "UpdateAlarms",
          "DeleteAlarms",
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices",
          "CreateDeviceGroups",
          "UpdateDeviceGroups",
          "DeleteDeviceGroups",
          "CreateRules",
          "UpdateRules",
          "DeleteRules",
          "CreateJobs",
          "UpdateSimManagement"
        ]
      },
      {
        "Id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "Role": "readOnly",
        "AllowedActions": []
      },
      {
        "Id": "GUID FROM APP MANIFEST",
        "Role": "ManageDevices",
        "AllowedActions": [
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices"
        ]
      }
    ]
    }
    ```

1. Une fois le fichier **Services/data/policies/roles.json** modifié, régénérez et redéployez le microservice d’authentification et d’autorisation dans votre accélérateur de solution.

### <a name="how-the-web-ui-enforces-permissions"></a>Mode d’application des autorisations par l’interface utilisateur web

L’interface utilisateur web utilise le [microservice d’authentification et d’autorisation](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) pour déterminer les actions qu’un utilisateur est autorisé à effectuer et les contrôles visibles dans l’interface utilisateur. Par exemple, si votre solution se nomme **contoso-rm4**, l’interface utilisateur web récupère une liste d’actions autorisées pour l’utilisateur actuel en envoyant la requête suivante :

```http
http://contoso-rm4.azurewebsites.net/v1/users/current
headers:
X-Source: true
Authorization: Bearer <JWT Token from ADAL>
```

Pour un utilisateur appelé **Device Manager** ayant le rôle **ManageDevices**, la réponse inclut le code JSON suivant dans le corps :

```json
{
  "Id": "userIdExample",
  "Email": "devicemanager@contoso.com",
  "Name": "Device Manager",
  "AllowedActions": [
    "CreateDevices",
    "UpdateDevices",
    "DeleteDevices"
  ]
}
```

L’extrait de code suivant de [deviceDelete.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/pages/devices/flyouts/deviceDelete/deviceDelete.js) dans [l’interface utilisateur web](https://github.com/Azure/pcs-remote-monitoring-webui/) montre comment les autorisations sont appliquées de manière déclarative :

```json
<FlyoutContent>
  <Protected permission={permissions.deleteDevices}>
    <form className="device-delete-container" onSubmit={this.deleteDevices}>
      ...
    </form>
  </Protected>
</FlyoutContent>
```

Pour plus d’informations, consultez la rubrique [Protected Components](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/shared/protected/README.md) (Composants protégés). Vous pouvez définir des autorisations supplémentaires dans le fichier [authModel.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/services/models/authModels.js).

### <a name="how-the-microservices-enforce-permissions"></a>Mode d’application des autorisations par les microservices

Les microservices vérifient également les autorisations pour assurer la protection contre les requêtes d’API non autorisées. Lorsqu’un microservice reçoit une requête d’API, il décode et vérifie le jeton JWT pour obtenir l’ID et les autorisations de l’utilisateur associées à son rôle.

L’extrait de code suivant issu du fichier [DevicesController.cs](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/iothub-manager/WebService/v1/Controllers/DevicesController.cs) dans le [microservice Gestionnaire IoTHub](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) montre comment les autorisations sont appliquées :

```csharp
[HttpDelete("{id}")]
[Authorize("DeleteDevices")]
public async Task DeleteAsync(string id)
{
    await this.devices.DeleteAsync(id);
}
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris comment le contrôle d’accès en fonction du rôle est implémenté dans l’accélérateur de solution de supervision à distance.

Consultez [Configurer des contrôles d’accès pour l’explorateur Time Series Insights](iot-accelerators-remote-monitoring-rbac-tsi.md) pour en savoir plus sur la gestion des accès à l’explorateur Time Series Insights dans l’accélérateur de solution de supervision à distance.

Pour plus d’informations conceptuelles sur l’accélérateur de solution de supervision à distance, consultez [Architecture de la supervision à distance](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Pour plus d’informations sur la personnalisation de la solution de supervision à distance, consultez [Personnaliser et redéployer un microservice](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->