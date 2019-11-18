---
title: Références pour FarmBeats
description: ''
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 8e8e4524034f0a296045691309b065f8547bdaa0
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797234"
---
# <a name="references"></a>Références

Vous trouverez ci-dessous une collection de notes et d’instructions qui décrivent les API Azure FarmBeats.

## <a name="rest-api"></a>API REST

Les API Azure FarmBeats offrent aux entreprises agricoles une interface RESTful standardisée avec des réponses JSON. Cela vous aidera à tirer parti des fonctionnalités Azure FarmBeats :

- Des API pour obtenir des données de capteurs, caméras, drones, météo, satellites et des données au sol conservées.
- Normalisation/contextualization des données entre les fournisseurs de données courants.
- Fonctionnalités d’accès et de requête schématisées sur toutes les données ingérées.
- Génération automatique de métadonnées qui peuvent être interrogées, en fonction des fonctionnalités agronomiques.  
- Agrégats de séquence temporelle générés automatiquement pour la génération rapide de modèles.
- Moteur Azure Data Factory (ADF) intégré pour créer facilement des pipelines personnalisés de traitement de données.

## <a name="application-development"></a>Développement d’applications

Les API incluent une documentation technique Swagger. Pour plus d’informations sur toutes les API et leurs demandes/réponses correspondantes, consultez [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

Il s’agit d’un résumé de tous les objets/ressources dans le hub de données FarmBeats :

Farm | Farm correspond à un emplacement physique intéressant au sein du système FarmBeats. Chaque Farm a un nom Farm et un ID d’exploitation agricole unique.
--- | ---|
Appareil  | Device correspond à un appareil physique présent dans l’exploitation agricole. Chaque appareil a une identité d’appareil unique. Device est généralement approvisionné dans une exploitation agricole avec un ID d’exploitation agricole.
DeviceModel  | DeviceModel correspond aux métadonnées de l’appareil, telles que le fabricant et le type d’appareil (passerelle ou nœud).
Capteur  | Sensor correspond à un capteur physique qui enregistre des valeurs. Un capteur est généralement connecté à un appareil avec une identité d’appareil.
SensorModel  | SensorModel correspond aux métadonnées du capteur, telles que le fabricant, le type de capteur (analogique ou numérique), la mesure effectuée par le capteur (température ambiante, pression, etc.).
Télémétrie  | Telemetry offre la possibilité de lire des messages de télémétrie pour un capteur et un intervalle de temps particuliers.
Travail  | Job correspond à n’importe quel flux de travail des activités, qui sont exécutées dans le système FarmBeats pour obtenir la sortie souhaitée. Chaque travail est associé à un ID et type de travail.
JobType  | JobType correspond à différents types de travail pris en charge par le système. Cela comprend les types de travaux définis par le système et par l’utilisateur.
ExtendedType  | ExtendedType correspond à la liste des types système et définis par l’utilisateur dans le système. Cela permet de configurer un nouveau type Sensor, Scene ou Scenefile dans le système FarmBeats.
Partenaire  | Partner correspond au partenaire d’intégration de capteur/d’imagerie pour FarmBeats.
Scene  | Scene correspond à toute sortie générée dans le contexte d’une Farm. Chaque Scene est associée à un ID de scène, à une source de scène, à un type de scène et à un ID d’exploitation agricole. Chaque ID de scène peut être associé à plusieurs fichiers de scène.
SceneFile |SceneFile correspond à tous les fichiers qui sont générés pour une seule scène. Un ID de scène unique peut être associé à plusieurs ID SceneFile.
Règle  |Rule correspond à une condition pour que les données liées à l’exploitation agricole déclenchent une alerte. Chaque règle sera dans le contexte des données d’une exploitation agricole.
Alerte  | Alert correspond à une notification, qui est générée lorsqu’une condition de règle est remplie. Chaque alerte sera dans le contexte d’une règle.
RoleDefinition  | RoleDefinition définit des actions autorisées et interdites pour un rôle.
RoleAssignment  |RoleAssignment correspond à l’assignation d’un rôle à un utilisateur ou à un principal de service.

**Format de données**

JSON (JavaScript Object Notation) est un format de données courant, indépendant du langage, qui fournit une représentation textuelle simple de structures de données arbitraires. Pour plus d’informations, consultez json.org.

## <a name="authentication-and-authorization"></a>Authentification et autorisation

Les requêtes HTTP envoyées à l’API REST sont protégées par Azure Active Directory (Azure AD).
Pour effectuer une requête authentifiée auprès des API REST, le code client requiert une authentification à l’aide d’informations d’identification valides avant de pouvoir appeler l’API. L’authentification est coordonnée entre les différents intervenants par Azure AD et fournit à votre client un jeton d’accès comme preuve d’authentification. Le jeton est alors envoyé dans l’en-tête d’autorisation HTTP des requêtes de l’API REST. Pour en savoir plus sur l’authentification Azure AD, consultez [Azure Active Directory](https://portal.azure.com) pour les développeurs.

Le jeton d’accès doit être envoyé dans les requête d’API suivantes dans la section d’en-tête comme suit :

```
headers = {"Authorization": "Bearer " + **access_token**}
```

**En-têtes de requête HTTP**

Voici les en-têtes de requête les plus courants qui doivent être spécifiés lors d’un appel d’API au hub de données Azure FarmBeats :


**En-tête** | **Description et exemple**
--- | ---
Content-Type  | Le format de requête (Content-Type: application/<format>) pour l’API du hub de données Azure FarmBeats est JSON. Content-Type: application/json
Authorization  | Spécifie le jeton d’accès requis pour effectuer un appel d’API. **Authorization: Bearer <Access-Token>**
Acceptation | Format de la réponse. Pour les API du hub de données Azure FarmBeats, le format est JSON **Accept: application/json**

**Requêtes d’API**

Pour effectuer une requête d’API REST, vous devez associer la méthode HTTP (GET, POST, PUT ou DELETE), l’URL du service d’API, l’URI de la ressource à interroger, à laquelle envoyer les données, à mettre à jour ou à supprimer et un ou plusieurs en-têtes de requête HTTP.

L’URL du service d’API est l’URL de votre hub de données https://\<yourdatahub-website-name>.azurewebsites.net. Si vous le souhaitez, vous pouvez inclure des paramètres de requête sur les appels GET pour filtrer les données, en limiter la taille et les trier dans les réponses.

L’exemple de requête ci-dessous porte sur l’obtention de la liste des appareils :

```
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

La plupart des appels GET, POST et PUT nécessitent un corps de demande JSON.

L’exemple de requête ci-dessous porte sur la création d’un Device (qui comporte un code JSON d’entrée avec le corps de la demande).

```json
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

**Paramètres de requête**

Pour les appels **GET** REST, vous pouvez filtrer les données, en limiter la taille et les trier dans une réponse d’API en incluant un ou plusieurs paramètres de requête dans l’URI de requête. Pour les paramètres de requête, reportez-vous à la documentation de l’API et consultez la section sur les appels GET.
Par exemple, lors de l’interrogation de la liste des appareils (appel GET sur /Device), les paramètres de requête suivants peuvent être spécifiés :  

![Projet FarmBeats](./media/for-references/query-parameters-device-1.png)

**Gestion des erreurs**

L’API du hub de données Azure FarmBeats retourne les erreurs HTTP standard. Les codes d’erreur les plus courants sont les suivants :


 |Code d'erreur             | Description |
 |---                    | --- |
 |200                    | Succès |
 |201                    | Réussite de la création (Post) |
 |400                    | Demande incorrecte. Il existe une erreur dans la requête |
 |401                    | Non autorisé. L’appelant de l’API n’est pas autorisé à accéder à la ressource |
 |404                    | Ressource introuvable |
 |5XX                    | Erreur interne du serveur. Les codes d’erreur commençant par 5XX signifient qu’il y a une erreur sur le serveur. Pour plus d’informations, reportez-vous aux journaux du serveur et à la section suivante. |


En plus des erreurs HTTP standard, les API du hub de données Azure FarmBeats renvoient également des erreurs internes dans le format ci-dessous :

```
{
  "message": "<More information on the error>",
  "status": "<error code>”,
  "code": "<InternalErrorCode>",
  "moreInfo": "<Details of the error>"
}
```

Exemple : Lors de la création d’une Farm, un champ obligatoire « Name » n’a pas été spécifié dans la charge utile d’entrée. Le message d’erreur obtenu est le suivant :

```
{
  "message": "Model validation failed",
  "status": 400,
  "code": "ModelValidationFailed",
  "moreInfo": "[\"The Name field is required.\"]"
}
```

## <a name="adding-users-or-app-registrations-to-azure-active-directory"></a>Ajout d’utilisateurs ou d’inscriptions d’applications à Azure Active Directory

 Les API Azure FarmBeats sont accessibles à un utilisateur ou à une inscription d’application dans Azure Active Directory. Pour créer une inscription d’application sur votre Azure Active Directory, procédez comme suit :  

1. Accédez au [Portail Azure](https://portal.azure.com) puis à **Azure Active Directory, Inscriptions d’applications** > **Nouvelle inscription**. Vous pouvez également utiliser un compte existant.
2. Pour un nouveau compte, assurez-vous d’effectuer les opérations suivantes :

    - Entrez un nom
    - Sélectionnez **Comptes dans ce répertoire d’organisation uniquement (Locataire unique)**
    - Les valeurs par défaut dans les autres champs.
    - Sélectionnez **Inscrire**.

3. À partir de la nouvelle inscription de l’application nouvelle ou de celle existante, **Vue d’ensemble**, procédez comme suit :

    - Capturez l’**ID client** et l’**ID de locataire**.
    - Accédez à **Certificats et secrets** pour générer une nouvelle clé secrète client et capturer la **Client-Secret**.
    - Revenez à la vue d’ensemble, puis cliquez sur le lien à côté de **Gérer l’application dans le répertoire local**
    - Accédez à **Propriétés** pour capturer l’**ID d’objet**

4. Accédez à votre [Swagger de hub de données](https://<yourdatahub>.azurewebsites.net/swagger/index.html) et procédez comme suit :
    - Accédez à **API RoleAssignment**
    - Effectuez un appel **POST** pour créer un RoleAssignment pour l’**ID d’objet** que vous venez de créer. – Le JSON d’entrée est :

  > [!NOTE]
  > Pour plus d’informations sur l’ajout d’utilisateurs et l’inscription AD, consultez [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Une fois les étapes ci-dessus terminées, l’inscription de votre application (client) peut appeler les API Azure FarmBeats à l’aide d’un jeton d’accès via l’authentification du porteur.  

Utilisez le jeton d’accès pour l’envoyer dans les requête d’API suivantes dans la section d’en-tête comme suit :

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
