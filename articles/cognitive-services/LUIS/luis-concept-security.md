---
title: Comprendre l’accès aux applications LUIS - Azure | Microsoft Docs
description: Découvrez comment accéder à la création dans LUIS.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 44380e12e6d095e8d40675af0b6b2fddc5e4c4e9
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264265"
---
# <a name="authoring-and-endpoint-user-access"></a>Accès utilisateur à la création et au point de terminaison
L’accès à la création est disponible pour les propriétaires et les collaborateurs. Pour une application privée, l’accès au point de terminaison est disponible pour les propriétaires et les collaborateurs. Pour une application publique, l’accès au point de terminaison est disponible à tous les utilisateurs qui possèdent leur propre compte LUIS et l’ID de l’application publique. 

## <a name="access-to-authoring"></a>Accès à la création
L’accès à l’application à partir du site web [LUIS][LUIS] ou des [API de création](https://aka.ms/luis-authoring-apis) est contrôlé par le propriétaire de l’application. 

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
|Passez en revue les énoncés de point de terminaison pour un [apprentissage actif](label-suggested-utterances.md)|
|Former|

## <a name="access-to-endpoint"></a>Accès au point de terminaison
L’accès au point de terminaison pour interroger LUIS est contrôlé par le paramètre **Public** de l’application sur la page **Paramètres**. La requête du point de terminaison d’une application privée est vérifiée pour voir s’il existe une clé autorisée avec accès au quota restant. La requête du point de terminaison d’une application publique doit également fournir une clé de point de terminaison (à partir de la personne qui effectue la requête) qui est également vérifiée pour l’accès au quota restant. 

La clé de point de terminaison est passée dans la chaîne de requête de la requête GET ou l’en-tête de la requête POST.

![Définir l’application comme publique](./media/luis-concept-security/set-application-as-public.png)

### <a name="private-app-endpoint-security"></a>Sécurité du point de terminaison d’application privée
Le point de terminaison d’une application privée est disponible uniquement pour ce qui suit :

|Clé et utilisateur|Explication|
|--|--|--|
|Clé de création du propriétaire| Jusqu’à 1 000 accès au point de terminaison|
|Clés de création des collaborateurs| Jusqu’à 1 000 accès au point de terminaison|
|Clés de point de terminaison ajoutées à partir de la page **[Publier](publishapp.md)**|Le propriétaire et les collaborateurs peuvent ajouter des clés de point de terminaison|

Les autres clés de création ou de point de terminaison n’ont **aucun** accès.

### <a name="public-app-endpoint-access"></a>Accès au point de terminaison d’application publique
Configurez l’application en tant que **publique** sur la page **Paramètres** de l’application. Une fois qu’une application est configurée comme étant publique, _toute_ clé de création LUIS valide ou clé de point de terminaison LUIS peut interroger votre application, tant que la clé n’a pas épuisé le quota de point de terminaison.

Un utilisateur qui n’est pas propriétaire ou collaborateur peut uniquement accéder à une application publique s’il dispose de l’ID d’application. LUIS ne dispose pas d’un _marché_ public, ni d’un autre moyen de rechercher une application publique.  

## <a name="microsoft-user-accounts"></a>Comptes utilisateur Microsoft
Les créateurs et les collaborateurs peuvent ajouter des clés à LUIS sur la page Publier. Le compte utilisateur Microsoft qui crée la clé LUIS dans le portail Azure doit être le propriétaire de l’application ou un collaborateur de l’application. 

<!--
### Individual consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then you can provide individual consent as part of the login process. 

### Administrator consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then the administrator can give individual consent via the method discussed in this [blog](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/). 
-->
## <a name="securing-the-endpoint"></a>Sécurisation du point de terminaison 
Vous pouvez contrôler qui peut voir votre clé de point de terminaison LUIS en l’appelant dans un environnement serveur à serveur. Si vous utilisez LUIS à partir d’un bot, la connexion entre le bot et LUIS est déjà sécurisée. Si vous appelez le point de terminaison LUIS directement, vous devez créer une API côté serveur (comme une [fonction](https://azure.microsoft.com/services/functions/) Azure) avec un accès contrôlé (comme [AAD](https://azure.microsoft.com/services/active-directory/)). Lorsque l’API côté serveur est appelée et que l’authentification et l’autorisation sont vérifiées, passez l’appel à LUIS. Bien que cette stratégie n’empêche pas les attaques de type interception (man-in-the-middle), elle masque votre point de terminaison à vos utilisateurs, vous permet d’effectuer le suivi de l’accès et vous permet d’ajouter la journalisation de la réponse du point de terminaison (comme [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="security-compliance"></a>Conformité de la sécurité
LUIS a passé avec succès l’audit ISO 27001:2013 et l’audit ISO 27018:2014 avec ZÉRO non conformités (résultats) dans le rapport d’audit. En outre, LUIS a également obtenu la Certification CSA STAR avec le Gold Award le plus élevé en termes d’évaluation de la fonctionnalité de la maturité. Azure est le seul fournisseur principal de service cloud public ayant obtenu cette certification. Pour plus d’informations, vous trouverez le service LUIS inclus dans la déclaration d’étendue mise à jour dans le document principal sur la [présentation de la conformité](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) d’Azure référencé sur les pages ISO de [Trust Center](https://www.microsoft.com/en-us/trustcenter/compliance/iso-iec-27001).  

## <a name="next-steps"></a>Étapes suivantes

Consultez les [Meilleures pratiques](luis-concept-best-practices.md) pour découvrir comment utiliser les intentions et les entités pour obtenir les meilleures prédictions.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website