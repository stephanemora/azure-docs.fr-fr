---
title: Protéger vos applications Web et API Azure App Service
description: Cet article a pour but de vous aider à vous familiariser avec la protection de vos applications Web et API Azure App Service dans Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: be9331ccd548628bfc27172c4f6e625bdba1632c
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158925"
---
# <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Protéger vos applications Web et API Azure App Service

Azure App Service est une plateforme entièrement gérée pour la création et l’hébergement de vos applications Web et API sans vous soucier de la gestion de l’infrastructure. Elle fournit des informations sur la gestion, la surveillance et l’exploitation afin de répondre à des exigences en matière de performances, de sécurité et de conformité de classe entreprise. Pour en savoir plus, consultez [Azure App Service](https://azure.microsoft.com/services/app-service/).

Pour activer la protection avancée contre les menaces pour votre plan Azure App Service, vous devez :

* S’abonner au niveau tarifaire standard d’Azure Security Center
* Activez le plan App Service comme indiqué. Security Center étant intégré en mode natif avec App Service, aucun déploiement ou intégration ne sont nécessaires. L’intégration est transparente.
* Disposez d’un plan App Service associé aux ordinateurs dédiés. Les plans pris en charge sont : De base, Standard, Premium, Isolé ou Linux. Security Center ne prend pas en charge les plans Gratuit, Partagé ou Consommation. Pour plus d’informations, voir [Plans App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).

Une fois le plan App Service activé, Security Center évalue les ressources couvertes par votre plan App Service et génère des suggestions en matière de sécurité en fonction de ses résultats. Security Center protège l’instance de machine virtuelle dans laquelle votre App Service s’exécute et l’interface de gestion. Il surveille également les demandes et réponses échangées avec vos applications s’exécutant dans App Service.

Security Center tire parti de l’échelle du cloud et de la visibilité qu’Azure a en tant que fournisseur de cloud, pour surveiller les attaques d’applications Web courantes. Security Center peut découvrir les attaques dirigées contre vos applications et identifier les attaques émergentes, même lorsque les pirates sont en phase de reconnaissance et analysent les vulnérabilités de multiples applications hébergées sur Azure. En tant que service natif Azure, Security Center occupe également dans une position privilégiée pour proposer des analyses de sécurité basées sur l’hôte, couvrant des nœuds de calcul sous-jacents pour ce PaaS, ce qui permet à Security Center de détecter les attaques dirigées contre des applications web qui ont déjà été exploitées.


## <a name="enabling-monitoring-and-protection-of-app-service"></a>Activation de la surveillance et la protection d’App Service

1. Dans le Portail Azure, choisissez Security Center.
2. Accédez à **Tarifs et paramètres**, puis choisissez un abonnement.
3. Sous **Niveau tarifaire**, dans la ligne **App Service**, définissez votre plan sur **Activé**.

    [![Activation de services d’application dans votre abonnement de niveau de service standard](media/security-center-app-services/app-services-toggle.png)](media/security-center-app-services/app-services-toggle.png#lightbox)


>[!NOTE]
> Le nombre d’instances répertoriées pour votre **quantité de ressources** est le nombre total d’instances de calcul, dans tous les plans App Service sur cet abonnement, en cours d’exécution lorsque vous avez ouvert le panneau du niveau tarifaire.
>
> Azure App Service offre une variété de plans. Votre plan App Service définit un ensemble de ressources de calcul nécessaires à l’exécution d’une application web. Ils sont équivalents aux batteries de serveurs dans l’hébergement web conventionnel. Une ou plusieurs applications peuvent être configurées pour s’exécuter sur les mêmes ressources informatiques (ou dans le même plan App Service).
>
>Pour valider le nombre, reportez-vous à la section « Plans App Service » dans le portail Azure, où vous pouvez voir le nombre d’instances de calcul utilisées par chaque plan. 






Pour désactiver la surveillance et les suggestions pour votre App Service, répétez ce processus et définissez votre plan **App Service** sur **Désactivé**.



## <a name="see-also"></a>Voir aussi
Dans cet article, vous avez vu comment utiliser les fonctionnalités de surveillance d’Azure Security Center. Pour plus d’informations sur Azure Security Center, consultez les articles suivants :

* [Définition de stratégies de sécurité dans Azure Security Center](tutorial-security-policy.md) : découvrez comment configurer les paramètres de sécurité dans Azure Security Center.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [App Services](security-center-virtual-machine-protection.md#app-services) :  Consultez une liste de vos environnements App Service avec des résumés d’intégrité.
* [Supervision des solutions de partenaire avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaire.
* [FAQ sur Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](https://blogs.msdn.com/b/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.
