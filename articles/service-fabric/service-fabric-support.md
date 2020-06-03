---
title: Découvrez les options de support d’Azure Service Fabric
description: Versions de clusters Azure Service Fabric prises en charge et liens pour soumettre des tickets de support
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: ee602b3aa6c701eb10730f4c5c387175bb070ef3
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834592"
---
# <a name="azure-service-fabric-support-options"></a>Options de support d’Azure Service Fabric

Pour assurer un support adapté des clusters Service Fabric sur lesquels vous exécutez vos charges de travail d’applications, nous avons mis différentes options à votre disposition. Vous pourrez choisir les options appropriées selon le niveau de support nécessaire et la gravité du problème. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Signaler des problèmes de production ou demander un support payant pour Azure

Pour signaler des problèmes sur votre cluster Service Fabric déployé sur Azure, ouvrez un ticket de support [sur le portail Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) ou le [portail de support Microsoft](https://support.microsoft.com/oas/default.aspx?prid=16146).

Pour en savoir plus :
 
- [Support Microsoft pour Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Support Premier Microsoft](https://support.microsoft.com/en-us/premier).

> [!Note]
> Les clusters s’exécutant sur un niveau de fiabilité bronze ou un cluster à nœud unique vous permettent uniquement d’exécuter des charges de travail de test. Si vous rencontrez des problèmes avec un cluster en cours d’exécution sur la fiabilité bronze ou un cluster à nœud unique, l’équipe du support technique de Microsoft vous aidera à atténuer le problème, mais elle n’effectuera pas d’analyse de la cause racine. Pour plus d’informations, reportez-vous à [Caractéristiques de fiabilité du cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster).
>
> Pour de plus amples renseignements sur les conditions exigées lors de la mise en service d’un cluster en production, reportez-vous à la [check-list de préparation à la production](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Signaler des problèmes de production ou demander un support payant pour des clusters Service Fabric autonomes

Pour signaler des problèmes sur votre cluster Service Fabric déployé en local ou sur d’autres clouds, ouvrez un ticket de support professionnel sur le [portail de support Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Pour en savoir plus :

- [Support professionnel Microsoft local](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Support Premier Microsoft](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Signaler des problèmes avec Azure Service Fabric

Nous avons défini un référentiel GitHub pour signaler les problèmes de Service Fabric.  De plus, nous surveillons activement les forums suivants.

### <a name="github-repo"></a>Référentiel GitHub 

Signaler des problèmes avec Azure Service Fabric sur le [Référentiel Git des problèmes de Service Fabric](https://github.com/Azure/service-fabric-issues). Ce référentiel est destiné au signalement et au suivi des problèmes rencontrés avec Azure Service Fabric, ainsi qu’aux demandes de petites fonctionnalités. **Ne pas l’utiliser pour signaler des problèmes de site en ligne**.

### <a name="stackoverflow-and-msdn-forums"></a>Forums StackOverflow et MSDN

La balise [Service Fabric sur StackOverflow][stackoverflow] et le [Service Fabric forum on MSDN][msdn-forum] sont recommandés pour poser des questions sur le fonctionnement de la plateforme et la réalisation de certaines tâches par son intermédiaire.

### <a name="azure-feedback-forum"></a>Forum de commentaires Azure

Le [Forum de commentaires Azure pour Service Fabric][uservoice-forum] est le meilleur endroit pour envoyer vos idées de fonctionnalités majeures pour le produit. Nous examinons en effet les requêtes les plus populaires dans le cadre de notre planification à moyen ou long terme. Nous vous encourageons à chercher l’appui de la communauté pour vos suggestions.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Préversions de Service Fabric – non prises en charge en production

De temps à autre, nous publions des versions dotées de fonctionnalités significatives, sur lesquelles nous souhaitons avoir un retour et qui sont publiées sous forme de version préliminaire. Ces versions préliminaires doivent uniquement être utilisées à des fins de test. Votre cluster de production doit toujours exécuter une version de Service Fabric prise en charge et stable. Les versions préliminaires commencent toujours par un numéro de version majeure et mineure : 255. Par exemple, la version 255.255.5703.949 de Service Fabric ne doit être utilisée que dans les clusters de test. Il s’agit d’une version préliminaire. Ces versions préliminaires sont également annoncées sur le [blog de l’équipe Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric) et s’accompagnent d’informations sur les fonctionnalités incluses.
Il n’existe aucune option de support technique payant pour ces versions préliminaires. Utilisez l’une des options répertoriées dans la rubrique [Signaler des problèmes Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) pour poser des questions ou laisser un commentaire.

## <a name="next-steps"></a>Étapes suivantes

[Versions de Service Fabric prises en charge](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: https://docs.microsoft.com/answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples
