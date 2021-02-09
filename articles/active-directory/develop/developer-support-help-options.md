---
title: Options d'aide et de support pour les développeurs d'applications Azure AD
description: Apprenez à vous procurer aide et support pour les questions liées au développement et les problèmes rencontrés lors de la création d'applications qui s'intègrent à des identités Microsoft (compte Azure Active Directory et Microsoft)
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: bce9479d063d091eb4fa68d2452d8a4218d45db9
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219941"
---
# <a name="support-and-help-options-for-developers"></a>Options d’aide et de support pour les développeurs

Que vous implémentiez une nouvelle fonctionnalité dans votre application ou que vous commenciez à vous familiariser avec Azure Active Directory (Azure AD), les identités Microsoft ou l'API Microsoft Graph, vous pouvez être amené à rechercher de l'aide auprès de la Communauté ou à vous renseigner sur les options de support qui sont à votre disposition en tant que développeur. Cet article vous présente ces options, notamment :

> [!div class="checklist"]
> * Effectuer une recherche pour savoir si votre question ou votre problème a déjà été traité par la Communauté, ou s'il existe déjà une documentation sur la fonctionnalité que vous essayez d'implémenter
> * Dans certains cas, vous pouvez simplement souhaiter utiliser nos outils de support afin de résoudre un problème particulier
> * Si vous ne trouvez pas la solution à votre problème, vous avez la possibilité de poser une question sur *Microsoft Q&A*
> * Si vous rencontrez des difficultés avec l’une de nos bibliothèques d’authentification, signalez un problème *GitHub*
> * Enfin, si vous voulez vous entretenir avec une personne, ouvrez une demande de support

## <a name="search"></a>Recherche

Si vous avez une question relative au développement, la réponse peut se trouver dans la documentation, dans les [exemples GitHub](https://github.com/azure-samples) ou dans les réponses aux questions posées sur [Microsoft Q&A](https://docs.microsoft.com/answers/products/).

### <a name="scoped-search"></a>Recherche étendue

Pour des résultats plus rapides, élargissez votre recherche à [Microsoft Q&A](https://docs.microsoft.com/answers/products/), à la documentation et aux exemples de code en utilisant la requête suivante dans votre moteur de recherche habituel :

```
{Your Search Terms} (site:http://www.docs.microsoft.com/answers/products/ OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Sachant que *{Your Search Terms}* correspond aux mots clés de votre recherche.

## <a name="use-the-development-support-tools"></a>Utiliser les outils de support de développement

| Outil  | Description  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Collez un ID ou un jeton d'accès pour décoder les noms et les valeurs des revendications. |
| [Afficheur Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)| Cet outil vous permet de soumettre des requêtes et de voir les réponses par rapport à l'API Microsoft Graph. |

## <a name="post-a-question-to-microsoft-qa"></a>Publier une question sur Microsoft Q&A

[Microsoft Q&A](https://docs.microsoft.com/answers/products/) est le canal de prédilection pour les questions de développement. Les membres de la communauté des développeurs et de l'équipe Microsoft y apportent directement leur contribution en vous aidant à résoudre vos problèmes.

Si vous ne trouvez pas de réponse à votre question par l’intermédiaire de la recherche, soumettez une nouvelle question sur [Microsoft Q&A](https://docs.microsoft.com/answers/products/). Utilisez l'une des balises suivantes lorsque vous posez une question pour permettre à la communauté d'identifier votre question et d'y répondre plus rapidement :

|Composant/zone  | Balises |
|---------|---------|
| Bibliothèque ADAL | [[adal]](https://docs.microsoft.com/answers/topics/azure-ad-adal-deprecation.html) |
| Bibliothèque MSAL     | [[msal]](https://docs.microsoft.com/answers/topics/azure-ad-msal.html) |
| Intergiciel (middleware) OWIN  | [[azure-active-directory]](https://docs.microsoft.com/answers/topics/azure-active-directory.html) |
| [Azure B2B](../external-identities/what-is-b2b.md)  | [[azure-ad-b2b]](https://docs.microsoft.com/answers/topics/azure-ad-b2b.html) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://docs.microsoft.com/answers/topics/azure-ad-b2c.html) |
| [API Microsoft Graph](https://developer.microsoft.com/graph/) | [[azure-ad-graph]](https://docs.microsoft.com/answers/topics/azure-ad-graph.html) |
| Toute autre zone liée à des sujets sur l’authentification ou les autorisations | [[azure-active-directory]](https://docs.microsoft.com/answers/topics/azure-active-directory.html) |

Les publications suivantes de [Microsoft Q&A](https://docs.microsoft.com/answers/products/) contiennent des conseils sur la formulation des questions et sur l’ajout de code source. Suivez ces recommandations pour que les membres de la communauté étudient votre question et y répondent rapidement :

* [Comment poser une bonne question](https://docs.microsoft.com/answers/articles/24951/how-to-write-a-quality-question.html)
* [Comment créer un exemple minimal, complet et vérifiable](https://docs.microsoft.com/answers/articles/24907/how-to-write-a-quality-answer.html)

## <a name="create-a-github-issue"></a>Créer un problème GitHub

Si vous découvrez un bogue ou un problème lié à nos bibliothèques, signalez-le dans nos référentiels GitHub. Nos bibliothèques étant open source, vous pouvez également soumettre une demande de tirage (pull request).

Pour obtenir la liste des bibliothèques et de leurs référentiels GitHub, consultez les informations suivantes :

* Bibliothèques [ADAL (Azure Active Directory Authentication Library)](../azuread-dev/active-directory-authentication-libraries.md) et référentiels GitHub
* Bibliothèques [MSAL (Microsoft Authentication Library)](reference-v2-libraries.md) et référentiels GitHub

## <a name="open-a-support-request"></a>Ouverture d’une demande de support

Si vous avez besoin de vous entretenir avec quelqu’un, vous pouvez ouvrir une demande de support. Si vous êtes un client Azure, plusieurs options de support s’offrent à vous. Pour comparer les formules, consultez [cette page](https://azure.microsoft.com/support/plans/). Le support technique des développeurs est également disponible pour les clients Azure. Pour obtenir plus d’informations sur l’achat de formules d’assistance Developer Support, consultez [cette page](https://azure.microsoft.com/support/plans/developer/).

* Si vous disposez déjà d’une formule d’assistance Azure, [ouvrez une demande de support ici](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Si vous n’êtes pas un client Azure, vous pouvez également ouvrir une demande de support auprès de Microsoft via [notre assistance commerciale](https://support.serviceshub.microsoft.com/supportforbusiness).

Vous avez aussi la possibilité de vous adresser à un [agent virtuel](https://support.microsoft.com/contactus/?ws=support) pour obtenir une assistance ou poser des questions.