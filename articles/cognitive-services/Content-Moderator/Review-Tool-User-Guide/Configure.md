---
title: 'Configurer les paramètres d’outil de révision : Content Moderator'
titlesuffix: Azure Cognitive Services
description: Utilisez l’outil de vérification pour configurer ou récupérer votre équipe, les balises, les connecteurs, les workflows et les informations d’identification de modérateur de contenu.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: f88ccbabc925b651abbc06f571a9d4220ed8aeb2
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756517"
---
# <a name="configure-the-review-tool"></a>Configurer l’outil de vérification

Le [outil de révision](https://contentmoderator.cognitive.microsoft.com) comporte plusieurs fonctionnalités importantes qui vous pouvez accéder via le **paramètres** menu du tableau de bord.

![Révision de modérateur de contenu trop menu Paramètres](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Gérer une équipe et sous-équipes

Le **équipe** onglet vous permet de gérer votre équipe et les sous-équipes&mdash;groupes d’utilisateurs qui peuvent être notifiés lorsque certains [révisions humaines](../review-api.md#reviews) sont démarrés. Vous ne pouvez avoir qu’une équipe (que vous créez lorsque vous vous inscrivez avec l’outil de vérification), mais vous pouvez créer plusieurs sous-équipes. L’administrateur d’équipe permettre inviter des membres, définir leurs autorisations et affectez-les à des sous-équipes différents.

![Passez en revue les paramètres d’équipe outil](images/settings-2-team.png)

Les sous-équipes permettent de créer des équipes d’escalade ou des équipes dédiées à la révision de catégories de contenu spécifiques. Par exemple, vous pouvez envoyer le contenu pour adultes à une équipe distincte pour examiner les étapes antérieures.

Cette section explique comment créer des sous-équipes et affecter rapidement des révisions à la volée. Toutefois, vous pouvez utiliser les [flux de travail](workflows.md) pour affecter les révisions en fonction de critères spécifiques.

### <a name="create-a-subteam"></a>Créer une sous-équipe

Accédez à la **sous-équipes** section et cliquez sur **sous-équipe ajouter**. Entrez votre nom de sous-équipe dans la boîte de dialogue et cliquez sur **enregistrer**.

![Nom de la sous-équipe](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Inviter des collègues

Vous ne pouvez pas assigner un utilisateur à un sous-groupe si elles ne sont pas déjà un membre de l’équipe par défaut, vous devez d’abord ajouter des réviseurs à l’équipe par défaut. Cliquez sur **inviter** sur le **Team** onglet.

![Inviter des utilisateurs](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Affecter des collègues à subteam

Cliquez sur le **Add Member** bouton pour attribuer des membres à partir de votre équipe par défaut pour un ou plusieurs des sous-équipes. Vous pouvez uniquement ajouter des utilisateurs existants à une sous-équipe. Pour ajouter des utilisateurs qui ne font pas encore partie de l’outil de révision, vous devez les inviter à l’aide du bouton Inviter sur la page Paramètres de l’équipe.

![Assigner des membres à la sous-équipe](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Affecter des révisions à sous-équipes

Une fois que vous avez créé votre sous-équipes et attribuer des membres, vous pouvez démarrer assignation du contenu [révisions](../review-api.md#reviews) à ces sous-équipes. Cette opération est effectuée à partir de la **révision** onglet du site.
Pour affecter le contenu à un sous-groupe, cliquez sur le bouton de sélection dans le coin supérieur droit, sélectionnez **déplacer vers**, puis sélectionnez un sous-groupe.

![Assigner la révision d’une image à une sous-équipe](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Basculer entre les sous-équipes

Si vous êtes un membre de plusieurs sous-équipe, vous pouvez basculer entre ces sous-équipes pour modifier les révisions de contenu sont affichées pour vous. Dans le **révision** , sélectionnez le menu déroulant intitulé **par défaut** et sélectionnez **sous-équipe choisissez**. Vous pouvez afficher les révisions de contenu pour des sous-équipes différents, mais uniquement ceux dont votre êtes membre.

![Basculer entre les sous-équipes](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Balises

Le **balises** onglet vous permet de définir des balises de modération personnalisées en plus les balises de modération par deux défaut&mdash;**isadult** (**un**) et **isracy**  (**r**). Lorsque vous créez une balise personnalisée, elle devient disponible dans les révisions en même temps que les balises par défaut. Vous pouvez modifier les balises s’affichent dans les révisions par leurs paramètres de visibilité de commutation.

![Afficher les balises, y compris les cases à cocher « Est visible »](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Créer des balises personnalisées

Pour créer une nouvelle balise, vous devez entrer un code court, un nom et une description dans les champs respectifs.

- **Short code**: Entrez un code à deux lettres pour votre balise. Exemple : **cb**
- **Nom** : Entrez un nom court et descriptif de balise en minuscules, sans espaces. Exemple : **isbullying**.
- **Description**: (facultatif) Entrez une description du type de contenu que votre cible de la balise. Exemple : **Si vous voulez ou les instances de cyber harcèlement**.

Cliquez sur **ajouter** pour ajouter une balise, puis cliquez sur **enregistrer** lorsque vous avez terminé la création de balises.

![Outil de vérification de créer la nouvelle boîte de dialogue de balise](images/settings-3-tags.png)

### <a name="delete-tags"></a>Supprimer des étiquettes

Vous pouvez supprimer des balises personnalisées en sélectionnant l’icône de Corbeille en regard de leurs entrées dans la liste de balises, mais vous ne pouvez pas supprimer les balises par défaut.

## <a name="connectors"></a>Connecteurs

Le **connecteurs** onglet vous permet de gérer vos connecteurs, qui sont des plug-ins spécifiques au service qui peuvent traiter le contenu de différentes façons en tant que partie du contenu [des flux de travail](../review-api.md#workflows).

Le connecteur par défaut lorsque vous créez un flux de travail est le connecteur de modérateur de contenu, ce qui peut marquer le contenu en tant que **adultes** ou **osé**, recherchez les obscénités et ainsi de suite. Toutefois, vous pouvez utiliser les autres connecteurs, répertoriés ici, tant que vous disposez des informations d’identification pour leurs services respectifs (pour utiliser le connecteur de l’API visage, par exemple, vous devez obtenir un [API visage](https://docs.microsoft.com/azure/cognitive-services/face/overview) clé d’abonnement).

Le [outil de révision](./human-in-the-loop.md) inclut les connecteurs suivants :

- API Émotion
- API Visage
- Service cloud PhotoDNA
- API Analyse de texte

### <a name="add-a-connector"></a>Ajouter un connecteur

Pour ajouter un connecteur (et le rendre disponible pour une utilisation dans le contenu [workflows](../review-api.md#workflows)), sélectionnez l’option appropriée **Connect** bouton. Dans la boîte de dialogue suivante, entrez votre clé d’abonnement pour ce service. Lorsque vous avez terminé, votre nouveau connecteur doit apparaître en haut de la page.

![Paramètres des connecteurs Content Moderator](images/settings-4-connectors.png)

## <a name="workflows"></a>Flux de travail

Le **workflows** onglet vous permet de gérer votre [des flux de travail](../review-api.md#workflows). Flux de travail est des filtres de cloud pour le contenu, et ils fonctionnent avec des connecteurs pour trier le contenu de différentes façons et prendre les mesures appropriées. Ici, vous pouvez définir, modifier et tester vos flux de travail. Consultez [définir et utiliser des flux de travail](Workflows.md) pour obtenir des conseils sur la procédure à suivre.

![Paramètres du flux de travail Content Moderator](images/settings-5-workflows.png)

## <a name="credentials"></a>Informations d'identification

Le **informations d’identification** onglet fournit un accès rapide à votre clé d’abonnement de Content Moderator, vous devez accéder à tous les services de modération à partir d’un appel REST ou le logiciel (SDK).

![Informations d’identification Content Moderator](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>Utilisez les informations d’identification externes pour les workflows

Le [outil de révision](https://contentmoderator.cognitive.microsoft.com) génère une clé de version d’évaluation gratuite pour les services Azure Content Moderator lorsque vous vous inscrivez, mais vous pouvez également le configurer pour utiliser un existant de clé à partir de votre compte Azure. Cela est recommandé pour les scénarios à grande échelle, comme les clés de version d’évaluation gratuites ont des limites d’utilisation strict ([tarification et limites](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)).

Si vous avez créé un [ressource de Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) dans Azure, accédez à celui-ci dans le portail Azure et sélectionnez le **clés** panneau. Copiez une de vos clés.

![Clés Content Moderator du portail Azure](images/credentials-azure-portal-keys.PNG)

Dans le [outil de révision](https://contentmoderator.cognitive.microsoft.com)de **informations d’identification** onglet, accédez à la **les paramètres de flux de travail** volet, sélectionnez **modifier**et collez votre clé dans le **Ocp-Apim-Subscription-Key** champ. Désormais, les flux de travail qui appellent les API de modération utilisera vos informations d’identification Azure.

> [!NOTE]
> Les deux autres champs dans le **les paramètres de flux de travail** volet sont pour les listes de terme et l’image personnalisées. Consultez le [termes du contrat personnalisé](../try-terms-list-api.md) ou [images personnalisées](../try-image-list-api.md) guides pour en savoir plus sur ces éléments.

### <a name="use-your-azure-account-with-the-review-apis"></a>Utiliser votre compte Azure avec les API de révision

Pour utiliser votre clé Azure avec les API de révision, vous devez récupérer votre ID de ressource. Accédez à votre ressource de modérateur de contenu dans le portail Azure et sélectionnez le **propriétés** panneau. Copiez la valeur d’ID de ressource et collez-la dans la **ID de ressource dans la liste verte** champ de l’outil de vérification **informations d’identification** onglet.

![ID de ressource Content Moderator dans le portail Azure](images/credentials-azure-portal-resourceid.PNG)

Si vous avez entré votre clé d’abonnement aux deux endroits, la clé d’évaluation qui est fourni avec votre compte d’outil de vérification ne sera pas utilisée, mais reste disponible.

## <a name="next-steps"></a>Étapes suivantes

Suivez le [démarrage rapide d’outil de révision](../quick-start.md) pour commencer à utiliser l’outil de vérification dans les scénarios de modération du contenu.