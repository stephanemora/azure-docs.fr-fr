---
title: Collaboration
titleSuffix: Language Understanding - Azure Cognitive Services
description: Les applications LUIS exigent un seul propriétaire et des collaborateurs facultatifs afin que plusieurs personnes puissent créer une même application.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 34ae47723762215fdea99362419c3875dc2e5942
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/12/2019
ms.locfileid: "59522743"
---
# <a name="collaborating-with-other-authors"></a>Collaboration avec d’autres auteurs

Les applications LUIS exigent un seul propriétaire et des collaborateurs facultatifs afin que plusieurs personnes puissent créer une même application.

## <a name="luis-account"></a>Compte LUIS
Un compte de LUIS est associé à un seul compte [Microsoft Live](https://login.live.com/). Chaque compte LUIS reçoit une [clé de création](luis-concept-keys.md#authoring-key) gratuite à utiliser pour la création de toutes les applications LUIS auxquelles le compte a accès. 

Un compte LUIS peut contenir de nombreuses applications LUIS.

Pour plus d’informations sur les comptes d’utilisateur Active Directory, voir [Utilisateur du locataire Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-tenant-user). 

## <a name="luis-app-owner"></a>Propriétaire d’application LUIS

Le compte qui crée une application est le propriétaire, et chaque application dispose d’un seul propriétaire. Le propriétaire est répertorié dans l’application **[paramètres](luis-how-to-collaborate.md)** page. Le propriétaire peut supprimer l’application de réception e-mail lorsque le quota de point de terminaison atteint 75 % de la limite mensuelle. 

## <a name="authorization-roles"></a>Rôles d’autorisation
LUIS ne prend pas en charge des rôles différents pour les propriétaires et collaborateurs, à une exception près. Le propriétaire est le seul compte qui peut supprimer l’application.

Si vous souhaitez pouvoir contrôler l’accès au modèle, découpez-le en applications LUIS plus petites, où chaque petite application a un ensemble plus limité de collaborateurs. Utilisez [Dispatch](https://aka.ms/dispatch-tool) pour autoriser une application LUIS parente à gérer la coordination entre les applications parentes et enfants.

## <a name="transfer-ownership"></a>Transférer la propriété
LUIS ne propose pas le transfert de propriété, mais un collaborateur peut exporter l’application, puis créer une application en l’important. Notez que la nouvelle application a un ID d’application différent. La nouvelle application doit être formée, publiée et le nouveau point de terminaison utilisé.

## <a name="luis-app-collaborators"></a>Collaborateurs pour les applications LUIS
Un propriétaire d’application peut ajouter des collaborateurs à une application. Le propriétaire doit ajouter l’adresse e-mail du collaborateur dans les **[Paramètres](luis-how-to-collaborate.md)** de l’application. Le collaborateur a un accès complet à l’application. Si le collaborateur supprime l’application, celle-ci est supprimée du compte du collaborateur, mais elle reste dans le compte du propriétaire. 

Si vous souhaitez partager plusieurs applications avec des collaborateurs, l’adresse e-mail des collaborateurs doit être ajoutée dans chaque application. 

## <a name="managing-multiple-authors"></a>Gérer plusieurs auteurs
Le site web [LUIS](luis-reference-regions.md#luis-website) n’offre actuellement pas la création au niveau des transactions. Vous pouvez autoriser les auteurs à travailler sur des versions indépendantes à partir d’une version de base. Deux méthodes différentes sont décrites dans les sections suivantes.

## <a name="manage-multiple-versions-inside-the-same-app"></a>Gérer plusieurs versions dans la même application
Commencez par le [clonage](luis-how-to-manage-versions.md#clone-a-version), à partir d’une version de base, pour chaque auteur. 

Chaque auteur apporte des modifications à sa propre version de l’application. Une fois que chaque auteur est satisfait du modèle, exportez les nouvelles versions sous forme de fichiers JSON.  

Les applications exportées sont des fichiers au format JSON, qui peuvent être utilisés pour comparer les modifications. Combinez les fichiers pour créer un seul fichier JSON de la nouvelle version. Modifiez la propriété **versionId** dans le fichier JSON pour indiquer la nouvelle version fusionnée. Importez cette version dans l’application d’origine. 

Cette méthode vous permet d’avoir une version active, une version intermédiaire et une version publiée. Vous pouvez comparer les résultats dans le volet de test interactif dans les trois versions.

## <a name="manage-multiple-versions-as-apps"></a>Gérer plusieurs versions en tant qu’applications
[Exportez](luis-how-to-manage-versions.md#export-version) la version de base. Chaque auteur importe la version. La personne qui importe l’application est le propriétaire de la version. Une fois la modification de l’application terminée, exportez la version. 

Les applications exportées sont des fichiers au format JSON, qui peuvent être utilisés pour comparer les modifications avec l’exportation de base. Combinez les fichiers pour créer un seul fichier JSON de la nouvelle version. Modifiez la propriété **versionId** dans le fichier JSON pour indiquer la nouvelle version fusionnée. Importez cette version dans l’application d’origine.

## <a name="collaborator-roles-vs-entity-roles"></a>Rôles d’entité collaborateur rôles vs

[Rôles de l’entité](luis-concept-roles.md) s’appliquent au modèle de données de l’application LUIS. Rôles de collaborateur s’appliquent aux niveaux d’accès de création. 

## <a name="next-steps"></a>Étapes suivantes

Comprendre les concepts du [contrôle de version](luis-concept-version.md). 

Consultez les [Paramètres de l’application](luis-how-to-collaborate.md) pour savoir comment gérer les collaborateurs dans votre application LUIS.

Consultez [Ajouter une adresse e-mail à la liste d’accès](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58fcccdd5aca2f08a4104342) avec les API de création.
