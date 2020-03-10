---
title: Effectuer la migration d’une ressource Azure pour la création
titleSuffix: Azure Cognitive Services
description: Effectuez une migration vers une clé de ressource de création Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: ec6f9592a4c149be382fab66cca27d929644d988
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78194507"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Effectuer une migration vers une clé de ressource de création Azure

Pour la création LUIS (Language Understanding), l’authentification ne se fait plus avec un compte e-mail, mais avec une ressource Azure. Bien que cela ne soit pas le cas pour le moment, le passage à une ressource Azure sera obligatoire à l’avenir.

## <a name="why-migrate"></a>Pourquoi migrer ?

L’utilisation d’une ressource Azure pour la création vous permet, en tant que propriétaire de la ressource, de contrôler l’accès à la fonctionnalité de création. Vous pouvez créer et nommer des ressources de création pour gérer différents groupes de créateurs.

Par exemple, vous êtes le propriétaire de 2 applications LUIS et avez différents membres qui sont des collaborateurs sur chaque application. Vous pouvez créer deux ressources de création différentes et attribuer chaque application à chaque ressource distincte. Attribuez ensuite chaque membre en tant que contributeur à la ressource de création appropriée en fonction de l’application sur laquelle il collabore. La ressource de création Azure contrôle l’autorisation.

> [!Note]
> Avant la migration, les cocréateurs sont appelés _collaborateurs_ au niveau de l’application LUIS. Après la migration, le rôle Azure de _contributeur_ est utilisé pour la même fonctionnalité, mais au niveau de la ressource Azure.

## <a name="what-is-migrating"></a>Qu’est-ce qu’une migration ?

La migration comprend les éléments suivants :

* Tous les utilisateurs de LUIS, propriétaires et contributeurs.
* **Toutes** les applications.
* Une **migration unidirectionnelle**.

Le propriétaire ne peut pas effectuer la migration d’un sous-ensemble d’applications et ce processus n’est pas réversible.

La migration n’est pas :

* Un processus qui collecte des contributeurs et déplace ou ajoute automatiquement des éléments dans la ressource de création Azure. En tant que propriétaire de l’application, vous devez effectuer cette étape. Cette étape nécessite des autorisations pour la ressource concernée.
* Un processus permettant de créer et d’affecter une ressource de runtime de prédiction. Si vous avez besoin d’une ressource de runtime de prédiction, le processus impliqué est le même, toutefois, il [doit être effectué séparément](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal).

## <a name="how-are-the-apps-migrating"></a>Comment se passe la migration des applications ?

Le [portail LUIS](https://www.luis.ai) fournit le processus de migration.

Vous serez invité à effectuer une migration dans les cas suivants :

* Vous avez des applications à créer sur le système d’authentification par e-mail.
* Et vous êtes le propriétaire de l’application.

Vous pouvez remettre à plus tard le processus de migration en fermant la fenêtre. Vous serez régulièrement invité à effectuer une migration tant que celle-ci n’aura pas été faite et que la date d’échéance n’aura pas été atteinte. Vous pouvez démarrer le processus de migration à partir de l’icône de verrou de la barre de navigation supérieure.

## <a name="migration-for-the-app-owner"></a>Migration côté propriétaire de l’application

### <a name="before-you-migrate"></a>Avant de migrer

* **Obligatoire** : Vous devez disposer d’un [abonnement Azure](https://azure.microsoft.com/free/). Une partie du processus d’abonnement nécessite que vous fournissiez des informations de facturation. Toutefois, vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) quand vous utilisez LUIS.
* **Facultatif** : Sauvegardez les applications qui figurent dans la liste des applications du portail LUIS en exportant chaque application ou en utilisant l’[API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) d’exportation.
* **Facultatif** : Enregistrez la liste de chaque contributeur de l’application. Cette liste d’e-mails est fournie dans le cadre du processus de migration.


**La création de votre application LUIS est gratuite**, indiquée par le niveau `F0`. Découvrez [plus d’informations sur les niveaux tarifaires](luis-boundaries.md#key-limits).

Si vous n’avez pas d’abonnement Azure, [créez-en un](https://azure.microsoft.com/free/).

### <a name="migration-steps"></a>Étapes de la migration

Suivez [ces étapes de migration](luis-migration-authoring-steps.md).

### <a name="after-you-migrate"></a>Après la migration

Après le processus de migration, toutes vos applications LUIS sont affectées à une seule ressource de création LUIS.

Vous pouvez créer d’autres ressources de création et les affecter à partir de la page **Gérer -> Ressources Azure** dans le _portail LUIS_.

Vous pouvez ajouter des contributeurs à la ressource de création à partir du _portail Azure_, dans la page **Contrôle d’accès (IAM)** de cette ressource. Pour plus d’informations, voir [Ajouter un accès contributeur](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource).

|Portail|Objectif|
|--|--|
|[Microsoft Azure](https://azure.microsoft.com/free/)|* Créer des ressources de prédiction et de création<br>* Affecter des contributeurs|
|[LUIS](https://www.luis.ai)|* Effectuer une migration vers de nouvelles ressources de création<br>* Affecter des ressources de prédiction et de création à des applications (ou annuler leur affectation) à partir de la page **Gérer -> Ressources Azure**|

## <a name="migration-for-the-app-contributor"></a>Migration côté contributeur de l’application

Chaque utilisateur de LUIS doit migrer, y compris les collaborateurs/contributeurs. Un collaborateur doit migrer pour avoir accès à l’application.

> [!Note]
> Si le propriétaire de l’application LUIS a migré et ajouté le collaborateur en tant que contributeur sur la ressource Azure, le collaborateur n’aura toujours pas accès à l’application, sauf s’il migre également.

### <a name="before-the-app-is-migrated"></a>Avant la migration de l’application

Vous pouvez choisir d’exporter une application à laquelle vous contribuez, puis de réimporter l’application dans LUIS. Le processus d’importation crée une application (avec un nouvel ID d’application) dont vous êtes le propriétaire.

### <a name="after-the-app-is-migrated"></a>Après la migration de l’application

Le propriétaire de l’application doit [ajouter votre e-mail à la ressource de création Azure en tant que collaborateur](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource).

Après le processus de migration, toutes les applications que vous possédez sont disponibles sur la page **Mes applications** du portail LUIS.

## <a name="troubleshooting-the-migration-process-for-luis-authoring"></a>Résolution de problèmes liés au processus de migration pour la création LUIS

* Les clés de création LUIS ne sont visibles dans le portail LUIS qu’à l’issue du processus de migration. Si vous créez les clés de création, par exemple à l’aide de l’interface CLI LUIS, l’utilisateur doit toujours effectuer le processus de migration dans le portail LUIS.
* Si un utilisateur migré ajoute un utilisateur non migré en tant que contributeur sur sa ressource Azure, l’utilisateur non migré n’aura pas accès aux applications, sauf s’il migre.
* Si un utilisateur non migré n’est pas propriétaire d’applications, mais est un collaborateur pour d’autres applications appartenant à d’autres personnes et que les propriétaires ont entrepris le processus de migration, cet utilisateur devra migrer pour avoir accès aux applications.
* Si un utilisateur non migré a ajouté un autre utilisateur migré en tant que collaborateur à son application, une erreur se produit, car vous ne pouvez pas ajouter un utilisateur migré en tant que collaborateur à une application. L’utilisateur non migré devra ensuite passer par le processus de migration et créer une ressource Azure, puis ajouter l’utilisateur migré en tant que contributeur à cette ressource.

Vous recevez une erreur pendant le processus de migration dans les cas suivants :
* Votre abonnement n’autorise pas la création de ressources Cognitive Services.
* Votre migration a un impact négatif sur le runtime d’autres applications. Lors de la migration, tous les collaborateurs sont supprimés de vos applications, et vous êtes supprimé en tant que collaborateur d’autres applications. Ce processus signifie que les clés que vous avez attribuées sont également supprimées. La migration est bloquée si vous avez attribué des clés dans d’autres applications. Supprimez la clé que vous avez attribuée en toute sécurité avant d’effectuer la migration. Si vous savez que la clé que vous avez attribuée n’est pas utilisée dans le runtime, vous devez la supprimer pour pouvoir progresser dans la migration.

Accédez à la liste des ressources Azure de votre application en utilisant le format d’URL suivant :

`https://www.luis.ai/applications/REPLACE-WITH-YOUR-APP-ID/versions/REPLACE-WITH-YOUR-VERSION-ID/manage/resources`

## <a name="next-steps"></a>Étapes suivantes

* [Comment effectuer la migration de votre application vers une ressource de création](luis-migration-authoring-steps.md)
