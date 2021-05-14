---
title: Découvrez les options de support d’Azure Service Fabric
description: Versions de clusters Azure Service Fabric prises en charge et liens pour soumettre des tickets de support
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: 9ba2f25665620524cd55a6f81c796aa024362d9d
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108123030"
---
# <a name="azure-service-fabric-support-options"></a>Options de support d’Azure Service Fabric

Nous avons créé un certain nombre d’options de demande de support pour répondre aux besoins de gestion de vos clusters Service Fabric et de vos charges de travail d’application, en fonction de l’urgence du support requis et de la gravité du problème.

## <a name="create-an-azure-support-request"></a>Créer une demande de support Azure

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

Pour signaler des problèmes relatifs à votre cluster Service Fabric s’exécutant localement ou sur d’autres clouds, vous pouvez ouvrir un ticket de support professionnel sur le [portail de support Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Pour en savoir plus :

- [Support professionnel Microsoft local](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Support Premier Microsoft](https://support.microsoft.com/en-us/premier).

## <a name="post-a-question-to-microsoft-qa"></a>Publier une question sur Microsoft Q&A

Recevez les réponses à vos questions concernant Service Fabric directement de la part des ingénieurs Microsoft, des professionnels Azure MVP (Most Valuable Professionals) et des membres de notre communauté d’experts.

[Microsoft Q&A](https://docs.microsoft.com/answers/products/) est la source recommandée d’Azure pour le support de la communauté.

Si vous ne trouvez pas de solution à votre problème en effectuant une recherche dans Microsoft Q&A, envoyez une nouvelle question. Veillez à publier votre question en utilisant l’étiquette **azure-service-fabric**. Voici quelques conseils de Microsoft Q&A pour rédiger des [questions de qualité](https://docs.microsoft.com/answers/articles/24951/how-to-write-a-quality-question.html).

## <a name="open-a-github-issue"></a>Ouvrir un problème GitHub

Signalez les problèmes concernant Azure Service Fabric sur la [page Service Fabric de GitHub](https://github.com/microsoft/service-fabric/issues). Ce référentiel est destiné au signalement et au suivi des problèmes ainsi qu’aux demandes de petites fonctionnalités en relation avec Azure Service Fabric. **N’utilisez pas ce moyen pour signaler des problèmes de site en ligne**.

## <a name="check-the-stackoverflow-forum"></a>Consulter le forum StackOverflow

L’étiquette `azure-service-fabric` sur [StackOverflow][stackoverflow] est utilisée pour poser des questions générales sur le fonctionnement de la plateforme et sur la manière dont vous pouvez l’utiliser pour accomplir certaines tâches.

## <a name="submit-feedback-on-azure-feedback"></a>Envoyer des commentaires sur Azure Feedback

Le [forum de commentaires Azure pour Service Fabric][uservoice-forum] est le meilleur endroit pour soumettre des idées de fonctionnalités de produit importantes. Nous examinons les demandes les plus populaires et les prenons en compte dans notre planification à moyen et à long terme. Nous vous encourageons à chercher l’appui de la communauté pour vos suggestions.


> [!Note]
> **Les préversions de Service Fabric ne sont pas prises en charge pour une utilisation en production.** Nous créons parfois des versions préliminaires spéciales contenant des modifications significatives de fonctionnalités pour lesquelles nous aimerions avoir un retour d’information rapide. Vous devez utiliser ces versions préliminaires uniquement dans des environnements de test isolés qui ne traitent pas de charges de travail de production. Votre cluster de production doit toujours exécuter une version de Service Fabric prise en charge et stable. Nous n’offrons aucune option de support technique payant pour ces versions préliminaires.
>
> Les versions préliminaires commencent toujours par un numéro de version majeure et mineure : 255. Par exemple, la version 255.255.5703.949 de Service Fabric est disponible en préversion et ne doit être utilisée que dans les clusters de test. Ces versions préliminaires sont également annoncées sur le [blog de l’équipe Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) et s’accompagnent d’informations sur les fonctionnalités incluses. Utilisez l’une des options ci-dessus pour fournir des commentaires.

## <a name="next-steps"></a>Étapes suivantes

[Versions de Service Fabric prises en charge](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: /answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: ./index.yml
[sample-repos]: /samples/browse/?products=azure