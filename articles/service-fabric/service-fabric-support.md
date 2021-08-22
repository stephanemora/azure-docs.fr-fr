---
title: Découvrez les options de support d’Azure Service Fabric
description: Versions de clusters Azure Service Fabric prises en charge et liens pour soumettre des tickets de support
author: erikadoyle
ms.topic: troubleshooting
ms.date: 5/17/2021
ms.author: edoyle
ms.custom: support-help-page
ms.openlocfilehash: 3b84f85a01ebaff9c3901672d31a135586280e13
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122633540"
---
# <a name="azure-service-fabric-support-options"></a>Options de support d’Azure Service Fabric

Nous avons créé un certain nombre d’options de demande de support pour répondre aux besoins de gestion de vos clusters Service Fabric et de vos charges de travail d’application, en fonction de l’urgence du support requis et de la gravité du problème.

## <a name="self-help-troubleshooting"></a>Résolution des problèmes autonome
<div class='icon is-large'>
    <img alt='Self help content' src='./media/logos/doc-logo.png'>
</div>

Documentation référencée par les services de support technique lors de la création d’un ticket, par les ingénieurs de fiabilité de site Service Fabric qui répondent à un incident, et par les utilisateurs lors de la découverte de solutions à des problèmes système actifs.

Pour obtenir la liste complète du contenu de résolution des problèmes autonome, consultez les [guides de résolution des problèmes de Service Fabric](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides).

## <a name="create-an-azure-support-request"></a>Créer une demande de support Azure
<div class='icon is-large'>
    <img alt='Azure support' src='./media/logos/azure-logo.png'>
</div>

Pour signaler des problèmes relatifs à votre cluster Service Fabric s’exécutant sur Azure, ouvrez un ticket de support [sur le portail Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) ou le [portail de support Microsoft](https://support.microsoft.com/oas/default.aspx?prid=16146).

Pour en savoir plus :

- [Options de support Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Support Premier Microsoft](https://support.microsoft.com/premier).

> [!Note]
> Les clusters s’exécutant sur un niveau de fiabilité bronze ou un cluster à nœud unique vous permettent uniquement d’exécuter des charges de travail de test. Si vous rencontrez des problèmes avec un cluster en cours d’exécution sur la fiabilité bronze ou un cluster à nœud unique, l’équipe du support technique de Microsoft vous aidera à atténuer le problème, mais elle n’effectuera pas d’analyse de la cause racine. Pour plus d’informations, reportez-vous à [Caractéristiques de fiabilité du cluster](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster).
>
> Pour de plus amples renseignements sur les conditions exigées lors de la mise en service d’un cluster en production, reportez-vous à la [check-list de préparation à la production](./service-fabric-production-readiness-checklist.md).

<a id="getlivesitesupportonprem"></a>

## <a name="create-a-support-request-for-standalone-service-fabric-clusters"></a>Créer une demande de support pour les clusters Service Fabric autonomes
<div class='icon is-large'>
    <img alt='Azure support' src='./media/logos/azure-logo.png'>
</div>

Pour signaler des problèmes relatifs à votre cluster Service Fabric s’exécutant localement ou sur d’autres clouds, vous pouvez ouvrir un ticket de support professionnel sur le [portail de support Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Pour en savoir plus :

- [Support professionnel Microsoft local](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Support Premier Microsoft](https://support.microsoft.com/en-us/premier).

## <a name="post-a-question-to-microsoft-qa"></a>Publier une question sur Microsoft Q&A
<div class='icon is-large'>
    <img alt='Microsoft Q&A' src='./media/logos/microsoft-logo.png'>
</div>   

Recevez les réponses à vos questions concernant Service Fabric directement de la part des ingénieurs Microsoft, des professionnels Azure MVP (Most Valuable Professionals) et des membres de notre communauté d’experts.

[Microsoft Q&A](/answers/topics/azure-service-fabric.html) est la source recommandée d’Azure pour le support de la communauté.

Si vous ne trouvez pas de solution à votre problème en effectuant une recherche dans Microsoft Q&A, envoyez une nouvelle question. Veillez à publier votre question en utilisant l’étiquette [**azure-service-fabric**](/answers/topics/azure-service-fabric.html). Voici quelques conseils de Microsoft Q&A pour rédiger des [questions de qualité](/answers/articles/24951/how-to-write-a-quality-question.html).

## <a name="open-a-github-issue"></a>Ouvrir un problème GitHub
<div class='icon is-large'>
    <img alt='GitHub-image' src='./media/logos/github-logo.png'>
</div>

Signalez les problèmes concernant Azure Service Fabric sur la [page Service Fabric de GitHub](https://github.com/microsoft/service-fabric/issues). Ce référentiel est destiné au signalement et au suivi des problèmes ainsi qu’aux demandes de petites fonctionnalités en relation avec Azure Service Fabric. **N’utilisez pas ce moyen pour signaler des problèmes de site en ligne**.

## <a name="check-the-stackoverflow-forum"></a>Consulter le forum StackOverflow
<div class='icon is-large'>
    <img alt='Stack Overflow' src='./media/logos/stack-overflow-logo.png'>
</div>

L’étiquette `azure-service-fabric` sur [StackOverflow][stackoverflow] est utilisée pour poser des questions générales sur le fonctionnement de la plateforme et sur la manière dont vous pouvez l’utiliser pour accomplir certaines tâches.

## <a name="stay-informed-of-updates-and-new-releases"></a>Rester informé des mises à jour et des nouvelles versions

<div class='icon is-large'>
    <img alt='Stay informed' src='./media/logos/updates-logo.png'>
</div>

Découvrez les mises à jour de produits, la feuille de route et les annonces importantes dans [Mises à jour Azure](https://azure.microsoft.com/updates/?product=service-fabric).

Pour connaître les mises en production et mises à jour les plus récentes du runtime et des kits de développement logiciel (SDK) de Service Fabric, consultez la page sur les [versions de Service Fabric](release-notes.md).



## <a name="next-steps"></a>Étapes suivantes

[Versions de Service Fabric prises en charge](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: /answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: ./index.yml
[sample-repos]: /samples/browse/?products=azure