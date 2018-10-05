---
title: Comprendre l’accès aux applications LUIS
titleSuffix: Azure Cognitive Services
description: L’accès à la création est disponible pour les propriétaires et les collaborateurs. Pour une application privée, l’accès au point de terminaison est disponible pour les propriétaires et les collaborateurs. Pour une application publique, l’accès au point de terminaison est disponible à tous les utilisateurs qui possèdent leur propre compte LUIS et l’ID de l’application publique.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 8a7ecac3776d767160b07f550251c54793926056
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033190"
---
# <a name="authoring-and-endpoint-user-access"></a>Accès utilisateur à la création et au point de terminaison
L’accès à la création est disponible pour les propriétaires et les collaborateurs. Pour une application privée, l’accès au point de terminaison est disponible pour les propriétaires et les collaborateurs. Pour une application publique, l’accès au point de terminaison est disponible à tous les utilisateurs qui possèdent leur propre compte LUIS et l’ID de l’application publique. 

## <a name="access-to-authoring"></a>Accès à la création
L’accès à l’application à partir du site web [LUIS](luis-reference-regions.md#luis-website) ou des [API de création](https://aka.ms/luis-authoring-apis) est contrôlé par le propriétaire de l’application. 

Le propriétaire et tous les collaborateurs disposent de l’accès requis pour créer l’application. 

|L’accès à la création inclut|Notes|
|--|--|
|Ajouter ou supprimer des clés de point de terminaison||
|Exporter la version||
|Exporter les journaux du point de terminaison||
|Importer la version||
|Rendre une application publique|Lorsqu’une application est publique, toute personne disposant d’une clé de création ou de point de terminaison peut interroger l’application.|
|Modifier le modèle|
|Publish|
|Passez en revue les énoncés de point de terminaison pour un [apprentissage actif](luis-how-to-review-endoint-utt.md)|
|Former|

## <a name="access-to-endpoint"></a>Accès au point de terminaison
L’accès pour interroger le point de terminaison est contrôlé par un paramètre dans la page **Informations sur l’application** dans la section **Gérer**. 

![Définir l’application comme publique](./media/luis-concept-security/set-application-as-public.png)

|[Point de terminaison privé](#private-app-endpoint-security)|[Point de terminaison public](#public-app-endpoint-access)|
|:--|:--|
|Accessible au propriétaire et aux collaborateurs|Accessible au propriétaire, aux collaborateurs et à toute personne qui connaît l’ID d’application|

### <a name="private-app-endpoint-security"></a>Sécurité du point de terminaison d’application privée
Le point de terminaison d’une application privée est disponible uniquement pour ce qui suit :

|Clé et utilisateur|Explication|
|--|--|--|
|Clé de création du propriétaire| Jusqu’à 1 000 accès au point de terminaison|
|Clés de création des collaborateurs| Jusqu’à 1 000 accès au point de terminaison|
|N’importe quelle clé affectée à LUIS par un auteur ou un collaborateur|Dépend du niveau d’utilisation de la clé|

#### <a name="microsoft-user-accounts"></a>Comptes utilisateur Microsoft
Les auteurs et collaborateurs peuvent affecter des clés à une application LUIS privée. Le compte d’utilisateur Microsoft qui crée la clé LUIS dans le portail Azure doit être celui du propriétaire de l’application ou d’un collaborateur de l’application. Vous ne pouvez pas affecter une clé à une application privée à partir d’un autre compte Azure.

Pour plus d’informations sur les comptes d’utilisateur Active Directory, voir [Utilisateur du locataire Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-tenant-user). 

### <a name="public-app-endpoint-access"></a>Accès au point de terminaison d’application publique
Une fois qu’une application est configurée comme étant publique, _toute_ clé de création LUIS valide ou clé de point de terminaison LUIS peut interroger votre application, tant que la clé n’a pas épuisé le quota de point de terminaison.

Un utilisateur qui n’est pas propriétaire ou collaborateur peut uniquement accéder à une application publique s’il dispose de l’ID d’application. LUIS ne dispose pas d’un _marché_ public, ni d’un autre moyen de rechercher une application publique.  

Une application publique est publiée dans toutes les régions. Ainsi, un utilisateur ayant une clé de ressource LUIS basée sur une région peut accéder à l’application dans toute région associée à la clé de ressource.

## <a name="microsoft-user-accounts"></a>Comptes utilisateur Microsoft
Les créateurs et les collaborateurs peuvent ajouter des clés à LUIS sur la page Publier. Le compte d’utilisateur Microsoft qui crée la clé LUIS dans le portail Azure doit être celui du propriétaire de l’application ou d’un collaborateur de l’application. 

Pour plus d’informations sur les comptes d’utilisateur Active Directory, voir [Utilisateur du locataire Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-tenant-user). 

<!--
### Individual consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then you can provide individual consent as part of the login process. 

### Administrator consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then the administrator can give individual consent via the method discussed in this [blog](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/). 
-->

## <a name="securing-the-endpoint"></a>Sécurisation du point de terminaison 
Vous pouvez contrôler qui peut voir votre clé de point de terminaison LUIS en l’appelant dans un environnement serveur à serveur. Si vous utilisez LUIS à partir d’un bot, la connexion entre le bot et LUIS est déjà sécurisée. Si vous appelez le point de terminaison LUIS directement, vous devez créer une API côté serveur (comme une [fonction](https://azure.microsoft.com/services/functions/) Azure) avec un accès contrôlé (comme [AAD](https://azure.microsoft.com/services/active-directory/)). Lorsque l’API côté serveur est appelée et que l’authentification et l’autorisation sont vérifiées, passez l’appel à LUIS. Bien que cette stratégie n’empêche pas les attaques de type interception (man-in-the-middle), elle masque votre point de terminaison à vos utilisateurs, vous permet d’effectuer le suivi de l’accès et vous permet d’ajouter la journalisation de la réponse du point de terminaison (comme [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="security-compliance"></a>Conformité de la sécurité
 
[!include[LUIS Free account](../../../includes/cognitive-services-luis-security-compliance.md)]

## <a name="next-steps"></a>Étapes suivantes

Consultez les [Meilleures pratiques](luis-concept-best-practices.md) pour découvrir comment utiliser les intentions et les entités pour obtenir les meilleures prédictions.
