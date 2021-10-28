---
title: Architecture et meilleures pratiques pour les comptes Purview
description: Cet article fournit des exemples d’architectures des comptes Azure Purview et décrit les meilleures pratiques.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 10/12/2021
ms.openlocfilehash: c061fcd27cbafae20732f7ab1436b3a55a5a8dc0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130214811"
---
# <a name="azure-purview-accounts-architectures-and-best-practices"></a>Architecture et meilleures pratiques des comptes Purview  

Azure Purview est une solution de gouvernance des données unifiée. Vous déployez un compte Azure Purview pour gérer de manière centralisée la gouvernance des données sur l’ensemble du patrimoine de données, comprenant à la fois les environnements cloud et les environnements locaux. Pour utiliser Azure Purview comme solution centralisée de gouvernance des données, vous devez déployer un ou plusieurs comptes Purview dans votre abonnement Azure. Toutefois, nous vous recommandons de conserver le nombre d’instances de Purview au minimum, mais dans certains cas, plusieurs instances de Purview sont nécessaires pour répondre aux exigences de conformité et de sécurité de l’entreprise.

## <a name="intended-audience"></a>Public concerné

- Équipe en charge de l’architecture des données
- Équipes en charge de la gestion et de la gouvernance des données
- Équipe de sécurité des données

## <a name="single-purview-account"></a>Compte Purview unique

Vous pouvez déployer un nombre minimal de comptes Purview pour l’ensemble de l’organisation. Cette approche tire le meilleur parti des « effets réseau » où la valeur de la plateforme augmente de façon exponentielle en fonction des données qui résident à l’intérieur de celle-ci. 

Utilisez la [hiérarchie de collections Azure Purview](./concept-best-practices-collections.md) pour mettre en place la structure de gestion des données de votre organisation à l’intérieur d’un seul compte Purview. Dans ce scénario, un compte Purview est déployé dans un abonnement Azure. Les sources de données d’un ou de plusieurs abonnements Azure peuvent être inscrites et analysées dans Azure Purview. Vous pouvez également inscrire et analyser des sources de données à partir de vos environnements locaux ou multiclouds.

:::image type="content" source="media/concept-best-practices/accounts-single-account.png" alt-text="Capture d’écran montrant l’unique compte Azure Purview."lightbox="media/concept-best-practices/accounts-single-account.png":::

## <a name="multiple-purview-accounts"></a>Plusieurs comptes Purview

Certaines organisations peuvent nécessiter la configuration de plusieurs comptes Azure Purview. Passez en revue les exemples de scénarios suivants lors de la définition de votre architecture de comptes Azure Purview :  

### <a name="testing-new-features"></a>Test de nouvelles fonctionnalités 

Il est recommandé de créer une nouvelle instance de compte Purview lors du test des configurations d’analyse ou des classifications dans des environnements isolés. Pour certains scénarios, il existe une fonctionnalité de « contrôle de version » dans certains domaines de la plateforme, par exemple un glossaire, mais il serait plus facile d’avoir une instance « jetable » de Purview pour tester librement la fonctionnalité attendue, puis planifier le déploiement de la fonctionnalité dans l’instance de production.  

En outre, vous pouvez utiliser un compte Purview test lorsque vous ne pouvez pas effectuer une restauration. Par exemple, actuellement, vous ne pouvez pas supprimer un attribut de terme de glossaire d’une instance de Purview une fois qu’il a été ajouté à votre compte Purview. Dans ce cas, il est recommandé d’utiliser d’abord un compte Purview test.
 
### <a name="isolating-productionand-non-production-environments"></a>Isolement des environnements de production et de non-production 

Vous pouvez déployer des instances distinctes de comptes Purview pour les environnements de développement, de test et de production, en particulier si vous possédez des instances de données distinctes pour chaque environnement.  

Dans ce scénario, les sources de données de production et de non-production peuvent être inscrites et analysées dans les instances de Purview correspondantes.

Vous pouvez également inscrire une source de données dans plusieurs instances de Purview, si nécessaire.

:::image type="content" source="media/concept-best-practices/accounts-multiple-accounts.png" alt-text="Capture d’écran montrant plusieurs comptes Azure Purview basés sur des environnements."lightbox="media/concept-best-practices/accounts-multiple-accounts.png":::

### <a name="fulfilling-compliance-requirements"></a>Respecter les exigences de conformité  

Lorsque vous analysez des sources de données dans Azure Purview, les informations relatives à vos métadonnées sont ingérées et stockées à l’intérieur de votre mappage de données Azure Purview, dans la région Azure où votre compte Purview est déployé. Vous pouvez déployer des instances distinctes d’Azure Purview si vous avez des exigences réglementaires et de conformité spécifiques qui nécessitent de placer les métadonnées dans un emplacement géographique spécifique.  

Si votre organisation possède des données dans plusieurs zones géographiques et que vous devez conserver les métadonnées dans la même région que les données réelles, vous devez déployer plusieurs instances de Purview, une pour chaque zone géographique. Dans ce cas, les sources de données de chaque région doivent être inscrites et analysées dans le compte Purview qui correspond à la région de la source de données ou à la géographie.

:::image type="content" source="media/concept-best-practices/accounts-multiple-regions.png" alt-text="Capture d’écran montrant plusieurs comptes Azure Purview basés sur les exigences de conformité."lightbox="media/concept-best-practices/accounts-multiple-regions.png":::

### <a name="having-data-sources-distributed-across-multiple-tenants"></a>Distribution de sources de données sur plusieurs locataires  

Actuellement, Purview ne prend pas en charge l’architecture mutualisée. Si vous avez des sources de données Azure distribuées sur plusieurs abonnements Azure sous différents locataires Azure Active Directory, il est recommandé de déployer des comptes Azure Purview distincts sous chaque locataire. 

Une exception s’applique aux sources de données basées sur les machines virtuelles et Power BI locataires. Pour plus d’informations sur l’analyse et l’inscription d’un multilocataire Power BI dans un compte Purview unique, voir [Inscrire et analyser un multilocataire Power BI](/azure/purview/register-scan-power-bi-tenant#register-and-scan-a-cross-tenant-power-bi). 

:::image type="content" source="media/concept-best-practices/accounts-multiple-tenants.png" alt-text="Capture d’écran montrant plusieurs comptes Azure Purview basés sur des exigences d’architecture mutualisée."lightbox="media/concept-best-practices/accounts-multiple-tenants.png"::: 

### <a name="billing-model"></a>Modèle de facturation 

Passez en revue le [modèle de tarification Azure Purview](https://azure.microsoft.com/pricing/details/azure-purview) lors de la définition du modèle de budgétisation et de la conception de l’architecture Azure Purview pour votre organisation. Une facturation est générée pour un seul compte Purview dans l’abonnement dans lequel est déployé le compte Purview. Ce modèle s’applique également à d’autres coûts Purview tels que l’analyse et la classification des métadonnées dans le mappage de données Purview.

Certaines organisations comptent souvent de nombreuses unités commerciales qui fonctionnent séparément et, dans certains cas, ne partagent même pas la facturation. Dans ce cas, l’organisation finit par créer une instance Purview pour chaque unité commerciale. Ce modèle n’est pas idéal, mais peut être nécessaire, en particulier parce que, souvent, les unités commerciales ne souhaitent pas partager la facturation Azure. 

Pour plus d’informations sur le modèle de coût cloud computing dans les modèles Chargeback et showBack, consultez [Qu’est-ce que Cloud Accounting ?](/azure/cloud-adoption-framework/strategy/cloud-accounting).  

## <a name="additional-considerations-and-recommendations"></a>Considérations et recommandations supplémentaires 

- Conservez un faible nombre de comptes Purview pour réduire la charge administrative. Si vous prévoyez de créer plusieurs comptes Purview, vous devrez peut-être créer et gérer des analyses, un modèle de contrôle d’accès, des informations d’identification et des runtimes supplémentaires sur vos comptes Purview. En outre, vous devrez peut-être gérer des classifications et des termes de Glossaire pour chaque compte Purview.

- Passez en revue vos besoins budgétaires et financiers. Si possible, utilisez le modèle de rétrofacturation ou de facturation interne lors de l’utilisation des services Azure, et divisez le coût d’Azure Purview dans toute l’organisation pour conserver le nombre minimal de comptes Purview. 

- Utilisez [Collections Azure Purview](concept-best-practices-collections.md) pour définir le contrôle d’accès aux métadonnées dans le mappage de données Azure Purview pour les utilisateurs professionnels, les équipes de gestion et de gouvernance des données de votre organisation. Pour plus d’informations, consultez [Contrôle d’accès dans Azure Purview](./catalog-permissions.md).

- Passez en revue les [limites d’Azure Purview](./how-to-manage-quotas.md#azure-purview-limits) avant de déployer de nouveaux comptes Purview. Actuellement, la limite par défaut de comptes Purview par région, par locataire (tous les abonnements combinés) est de 3. Vous devrez peut-être contacter le support technique Microsoft pour augmenter cette limite dans votre abonnement ou locataire avant de déployer des instances supplémentaires d’Azure Purview.  

- Passez en revue les [conditions préalables Azure Purview](./create-catalog-portal.md#prerequisites) avant de déployer de nouveaux comptes Purview dans votre environnement.
  
## <a name="next-steps"></a>Étapes suivantes
-  [Créer un compte Purview](./create-catalog-portal.md)
