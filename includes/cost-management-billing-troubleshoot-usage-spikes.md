---
title: Fichier Include
description: inclure fichier
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: include
ms.date: 02/09/2021
ms.author: banders
ms.reviewer: isvargas
ms.custom: include file
ms.openlocfilehash: efd08ec98533bd78d6a7cc606e11cbde1b4e33e3
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/10/2021
ms.locfileid: "122523965"
---
## <a name="troubleshoot-usage-spikes"></a>Résoudre les pics d’utilisation

Cette section vous aide à comprendre comment un pic d’utilisation s’affiche dans votre fichier d’utilisation Azure, comment empêcher les pics d’utilisation, surveiller vos ressources et quand contacter le support Azure. Elle est destinée aux clients disposant d’un Contrat Entreprise (EA) ou d’un contrat de client Microsoft. Ils doivent avoir des rôles Administrateur EA ou administrateur de facturation. Pour plus d’informations sur les autorisations, consultez [Télécharger ou consulter votre facture Azure et vos données d’utilisation quotidienne](../articles/cost-management-billing/manage/download-azure-invoice-daily-usage-date.md).

Un pic dans votre utilisation d’Azure ou des frais inattendus pour un service ou une ressource spécifique est souvent causé par une utilisation involontaire ou par un incident.

Dans les deux cas, vous devez limiter les services et les ressources affectés avant de contacter le support afin de pouvoir choisir la zone de support appropriée.

Il est important de comprendre qu’il est peu probable que Microsoft puisse déterminer la cause principale de l’utilisation accrue et des frais associés. Ainsi, les clients peuvent télécharger leurs propres données d’utilisation détaillées dans le Portail Azure.

Microsoft n’analyse pas vos ressources Azure déployées, telles que les machines virtuelles, les réseaux ou les transferts de données, en raison de problèmes de sécurité et de la confidentialité des clients. Toutefois, Microsoft tente de vous informer sur la façon dont vous pouvez surveiller votre utilisation d’Azure. Enfin, il vous incombe de surveiller votre propre utilisation.

### <a name="what-a-spike-looks-like-in-the-usage-file"></a>À quoi ressemble un pic dans le fichier d’utilisation

Une fois que vous avez appliqué les filtres décrits dans les sections précédentes, vous pouvez rechercher le terme anormal. Par exemple, vous pouvez dépanner un pic pour la Catégorie de mesure de la **Bande passante**).

Placez l’ID de **Produit** et d'**Instance** (ID de ressource pour un contrat de client Microsoft) dans la section lignes de l’outil de tableau croisé dynamique. Ajoutez ensuite **Coût** dans Valeurs, **ID d’abonnement** dans les filtres et **Date** dans les colonnes. Filtrez ensuite pour afficher uniquement les données d’un ID d’abonnement. Par exemple : `111111111111-1111-1111-111111111111`.

L’illustration suivante montre à quoi ressemble un pic de Bande passante (Transferts de données).

:::image type="content" source="./media/cost-management-billing-troubleshoot-usage-spikes/data-usage-spike.png" alt-text="Capture d’écran d’Excel montrant un pic d’utilisation." lightbox="./media/cost-management-billing-troubleshoot-usage-spikes/data-usage-spike.png" :::

Le pic est destiné à une ressource particulière. Dans ce cas, la ligne 7 du fichier Excel affiche les valeurs de coût pour le compte de stockage **storageaccountnameazurefile1**. Le 1er octobre 2020, le coût a une valeur proche de zéro (0) USD (2.23043 E-06, qui est égal à 0.000002230431449). Vous pouvez voir un pic important le 2 octobre 2020 et le 3 octobre 2020, lorsque le coût passe à 10 000 et à 28 000 USD. Les coûts sont retournés à la normale le 4 octobre 2020 (9.29 E-07).

Dans cet exemple, vous avez identifié la ressource qui a généré un grand nombre de frais de bande passante, les dates de survenue et le produit spécifique (inter-région – Transfert de données out – Europe). Déterminez si le pic résulte d’un transfert de données volumineux. Utilisez les informations des sections précédentes pour vérifier votre ressource affectée.

Si vous déterminez qu’il n’y avait aucun transfert de la ressource aux dates indiquées, contactez une équipe technique Azure. L’équipe peut aider à déterminer s’il existe un bogue ou un incident à l’origine du problème. Dans cet exemple, la ressource affectée est un compte Stockage. Vous devez donc contacter l’équipe technique stockage Azure. De même, si le pic a affecté un ordinateur virtuel, vous devez contacter l’équipe technique Azure Virtual Machines pour déterminer si un incident en cours affecte le service d’ordinateur virtuel.

S’il existe un incident en cours, l’équipe technique Azure coordonne avec l’équipe de facturation Azure pour passer en revue une demande de remboursement.

### <a name="tools-to-monitor-azure-usage"></a>Outils de surveillance de l’utilisation d’Azure

Vous pouvez toujours gérer vos coûts avec Azure Cost Management et créer des budgets. Pour plus d'informations, consultez les pages suivantes :

- [Gérer les coûts avec Azure Budgets](../articles/cost-management-billing/manage/cost-management-budget-scenario.md)
- [Tutoriel : Créer et gérer des budgets Azure](../articles/cost-management-billing/costs/tutorial-acm-create-budgets.md)

Pour une utilisation Stockage, nous vous recommandons d’utiliser l’outil Storage Analytics. Elle vous permet d’utiliser la journalisation par transaction. Les journaux sont détaillés, mais vous pouvez effectuer un suivi et un débogage complets par vous-même. Pour plus d'informations, consultez les pages suivantes :

- [Analyse du stockage](../articles/storage/common/storage-analytics.md)
- [Format de journal Storage Analytics](/rest/api/storageservices/Storage-Analytics-Log-Format)
- [Activer et gérer les journaux Azure Storage Analytics (classique)](../articles/storage/common/manage-storage-analytics-logs.md)

Pour l’utilisation liée au réseau, vous pouvez utiliser des outils de capture réseau comme [Moniteur réseau](https://www.microsoft.com/download/details.aspx?id=4865)ou Fiddler.

Pour les problèmes liés aux machines virtuelles avec une image du système d’exploitation Windows, vous pouvez utiliser le [Journal des événements Windows](/windows/win32/wes/windows-event-log).

Pour les déploiements de plateforme en tant que service (PaaS), [activez les diagnostics Azure](../articles/cloud-services/cloud-services-dotnet-diagnostics.md) dans l’application.

Pour les déploiements IaaS (infrastructure as a service), activez [le suivi Windows Communication Foundation](/dotnet/framework/wcf/diagnostics/tracing/configuring-tracing).

Activez la [Journalisation améliorée pour IIS 8.5](/iis/get-started/whats-new-in-iis-85/enhanced-logging-for-iis85).

[Activez la journalisation des diagnostics pour les applications Web dans Azure App Service](../articles/app-service/troubleshoot-diagnostic-logs.md).

Pour obtenir plus d’informations et des conseils pour votre situation, contactez votre responsable de compte de réussite des clients Microsoft pour demander de l’aide auprès d’un architecte de solution Cloud.