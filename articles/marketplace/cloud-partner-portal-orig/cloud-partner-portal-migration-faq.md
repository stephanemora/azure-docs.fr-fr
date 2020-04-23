---
title: Questions fréquentes (FAQ) sur la migration vers l’Espace partenaires | Place de marché Azure
description: Cet article répond aux questions fréquemment posées sur la migration des offres du portail Cloud Partner vers l’Espace partenaires.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mingshen
ms.openlocfilehash: 672153eba4aa2b739b67694f939c4796b39ac4c6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81275818"
---
# <a name="frequently-asked-questions-for-migrating-from-the-cloud-partner-portal-to-partner-center"></a>Questions fréquentes (FAQ) sur la migration du portail Cloud Partner vers l’Espace partenaires

Cet article répond aux questions fréquemment posées sur la migration des offres du portail Cloud Partner vers l’Espace partenaires.

## <a name="what-does-offer-migration-mean"></a>Que signifie « migration d’offre » ?

Nous transférons vos données d’offres du portail Cloud Partner vers l’Espace partenaires, ce qui implique certains changements en termes d’expérience de publication et de gestion des offres.

| Domaine  | Changements  |
|-------|----------|
| **Expérience de publication et de gestion des offres** | Vous bénéficierez d’une expérience utilisateur améliorée avec une interface intuitive dans l’Espace partenaires. Pour plus d’informations, consultez [Quelles sont les différences entre l’Espace partenaires et le portail Cloud Partner ?](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal) |
| **Disponibilité de vos offres sur la Place de marché** | Aucun changement. Si votre offre est active sur la Place de marché, elle continue de l’être pendant et après la migration. |
| **Nouveaux achats et déploiements** | Aucun changement. Vos clients peuvent toujours acheter et déployer vos offres sans interruption. |
| **Paiements** | Les achats et les déploiements qui ont lieu pendant ou après la migration continuent à être payés normalement. |
|**Intégrations d’API avec des [API du portail Cloud Partner existantes](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)** | Les API du portail Cloud Partner existantes continuent d’être prises en charge après la migration, et vos intégrations existantes continuent de fonctionner. Pour plus d’informations, consultez [Les API REST du portail Cloud Partner seront-elles prises en charge après la migration ?](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration) |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>Puis-je encore accéder au portail Cloud Partner et gérer mes offres pendant la migration ?

Les offres sont migrées vers l’Espace partenaires à partir du 13 avril, et tout au long du mois de mai. Pendant cette période, vous pouvez accéder au portail Cloud Partner comme d’habitude, sauf pendant la période durant laquelle la migration de vos offres est planifiée.
Pendant que vos offres seront migrées, elles présenteront l’état « Verrouillé - en déplacement vers l’Espace partenaires », comme indiqué dans la capture d’écran suivante. Cette période de migration devrait être inférieure à 24 heures. Pendant ce temps, vous ne pourrez pas apporter de mises à jour à vos offres. Une fois la migration de vos offres terminée, vous recevrez une notification par e-mail.

:::image type="content" source="media/migration-faq/all-offers-1.png" alt-text="Illustre l’état des offres migrées.":::

Une fois vos offres migrées, elles seront en mode lecture seule **pendant une période limitée** dans le portail Cloud Partner. Leur état indiquera « Déplacé vers l’Espace partenaires » et inclura un lien vers votre offre dans l’Espace partenaires, comme illustré dans les captures d’écran suivantes. À partir de là, vous gérerez les mises à jour de toutes vos offres ou créerez des offres exclusivement par le biais de l’Espace partenaires.

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="Illustre le message fourni pour les offres qui ont été migrées vers l’Espace partenaires":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="Illustre la page Portail Cloud Partner pour une offre migrée.":::

## <a name="how-will-i-create-new-offers"></a>Comment créer des offres ?

À partir du portail Cloud Partner, vous serez redirigé vers la création de nouvelles offres dans l’Espace partenaires.

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="Illustre le menu permettant de créer une offre dans le portail Cloud Partner":::

Une fois que vous aurez sélectionné une nouvelle offre, un message tel que le suivant s’affichera.

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="Illustre le message reçu lors de la création d’une offre dans CPP":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>Dois-je créer un nouveau compte pour gérer les offres dans l’Espace partenaires ?

Non. Votre compte sous-jacent est conservé, et vous devez déjà le gérer dans l’Espace partenaires. Cela signifie que si vous êtes un partenaire existant, vous pouvez utiliser vos informations d’identification de compte Portail Cloud Partner existantes pour vous connecter à l’Espace partenaires après la migration. Seules les offres et l’expérience de gestion associée sont transférées du portail Cloud Partner vers l’Espace partenaires. Nous vous demandons de ne pas créer de nouveau compte, car vos offres ne seront pas associées au nouveau compte.

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>Un message dans le portail Cloud Partner m’invite à activer mon compte. Qu’est-ce que cela signifie ?

Nous avons besoin de quelques informations supplémentaires pour pouvoir migrer correctement votre compte vers l’Espace partenaires et vous permettre de gérer vos offres dans l’Espace partenaires une fois la migration de l’offre terminée. Pour plus d’informations sur l’activation de compte, consultez [Migration de compte du portail Cloud Partner vers l’Espace partenaires](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc).

Les étapes nécessaires pour terminer l’activation du compte varient en fonction du rôle de votre compte.

| Rôle de compte | Étapes d’activation |
|--------------|----------------|
|Propriétaire | Accédez à la page [Profil de l’éditeur](https://cloudpartner.azure.com/#profile) dans le portail Cloud Partner, puis cliquez sur le lien dans la bannière pour l’activer. Vous serez redirigé vers l’Espace partenaires pour terminer l’activation du compte. |
| Contributeur | Seul un utilisateur du compte disposant d’un rôle de propriétaire peut activer le compte. Contactez les propriétaires de votre compte pour terminer l’activation du compte. Les propriétaires de votre compte doivent être listés dans le message de bannière. |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>Je ne parviens pas à me connecter à mon compte et à ouvrir un ticket de support

Si vous ne pouvez pas vous connecter à votre compte, vous pouvez [ouvrir un ticket de support](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>Où trouver de la documentation sur la nouvelle expérience de publication de l’Espace partenaires ?

Accédez à la [documentation sur la Place de marché commerciale](https://docs.microsoft.com/azure/marketplace/). Développez ensuite **Place de marché commercial dans l’Espace partenaires**  > **Créer une offre** pour voir les rubriques d’aide à la création de chaque type d’offre.

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="Illustre les rubriques d’aide relatives à l’Espace partenaires":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>Quelles sont les différences entre l’Espace partenaires et le portail Cloud Partner ?

Vous pouvez remarquer les différences suivantes entre le portail Cloud Partner et l’Espace partenaires.

### <a name="modular-publishing-capabilities"></a>Fonctionnalités de publication modulaires

L’Espace partenaires fournit une option de publication modulaire qui vous permet de sélectionner les modifications que vous souhaitez publier au lieu de toujours publier toutes les mises à jour à la fois. Par exemple, la capture d’écran suivante montre que les seules modifications sélectionnées à publier sont les modifications apportées à **Propriétés** et **Référencement de l’offre**.

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="Illustre la page Réviser et publier":::

Les mises à jour que vous ne publiez pas sont enregistrées en tant que brouillons. Continuez à utiliser la préversion de votre offre afin de la vérifier avant de la rendre accessible au public.

### <a name="rich-text-format"></a>Format de texte enrichi

Améliorez la description de votre offre et de votre plan à l’aide de l’Éditeur de texte enrichi dans les pages Référencement de l’offre et Référencement du plan.

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="Illustre l’éditeur de texte enrichi":::

### <a name="enhanced-preview-options"></a>Options d’aperçu améliorées

L’Espace partenaires comprend une [fonctionnalité de comparaison](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers) avec des options de filtrage améliorées. Cela vous permet d’effectuer des comparaisons entre la préversion et la version active de l’offre.

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="Illustre la fonctionnalité de comparaison":::

### <a name="branding-and-navigation-changes"></a>Changements dans la personnalisation et la navigation

Vous remarquerez certaines modifications de personnalisation. Par exemple, les « références SKU » sont appelées « plans » dans l’Espace partenaires.

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="Illustre la vue d’ensemble de plan":::

De plus, les informations que vous fournissiez dans les pages **Place de marché** ou **Détails de la vitrine** (Service de conseil, application Power BI) du portail Cloud Partner sont recueillies dans la page **Référencement de l’offre** dans l’Espace partenaires.

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="Illustre la page Référencement de l’offre.":::

Les informations que vous fournissiez pour les références SKU dans une seule page du portail Cloud Partner peuvent maintenant être recueillies dans plusieurs pages de l’Espace partenaires :

* Page de configuration du plan
* Page de référencement du plan
* Page de disponibilité du plan
* Page de configuration technique du plan, comme illustré dans cette capture d’écran.

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="Illustre la page de configuration technique du plan.":::

Votre ID d’offre est maintenant affiché dans la barre de navigation gauche de l’offre.

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="Illustre le menu de navigation gauche avec l’ID de l’offre.":::

### <a name="stop-selling-an-offer"></a>Arrêter la vente d’une offre

Vous pouvez demander à [arrêter la vente d’une offre](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan) sur la Place de marché directement à partir du portail de l’Espace partenaires. L’option est disponible dans la page **Vue d’ensemble de l’offre** de votre offre.

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="Illustre la page Vue d’ensemble de l’offre avec l’option Arrêter la vente.":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Quelles pages de l’Espace partenaires correspondent aux pages que j’utilisais dans le portail Cloud Partner ?

Le tableau suivant présente les liens correspondants entre les deux portails.

| Page | Lien du portail Cloud Partner | Lien de l’Espace partenaire |
|------|---------------------------|---------------------|
| **Page regroupant toutes les offres** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **Page regroupant tous les éditeurs** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **Profil d’éditeur** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **Page des utilisateurs** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **Page Historique** | https://cloudpartner.azure.com/#history | La fonctionnalité d’historique n’est pas encore prise en charge dans l’Espace partenaires. |
| **Tableau de bord Insights** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **Rapport de paiements** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>Les API REST du portail Cloud Partner seront-elles prises en charge après la migration ?

Les API du portail Cloud Partner sont intégrées à l’Espace partenaires et continueront à fonctionner après la migration de vos offres vers l’Espace partenaires. L’intégration apporte de légères modifications. Passez en revue les modifications listées dans le tableau suivant pour vous assurer que votre code continue à fonctionner après la migration vers l’Espace partenaires.

| **API** | **Description de la modification** | **Impact** |
| ------- | ---------------------- | ---------- |
| POST Publish, GoLive, Cancel | Pour les offres migrées, l’en-tête de réponse aura un format différent, mais continuera à fonctionner de la même façon, indiquant un chemin relatif pour récupérer l’état de l’opération. | Lors de l’envoi d’une des requêtes de publication correspondantes pour une offre, l’en-tête d’emplacement aura l’un des deux formats suivants en fonction de l’état de migration de l’offre :<ul><li>Offres non migrées<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Offres migrées<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| Opération GET | Pour les offres qui prenaient auparavant en charge le champ « notification-email » dans la réponse, ce champ est déprécié et n’est plus retourné pour les offres migrées. | Pour les offres migrées, nous n’enverrons plus de notifications à la liste des e-mails spécifiés dans les requêtes. Au lieu de cela, le service d’API s’alignera sur le processus de notification par e-mail dans l’Espace partenaires pour envoyer des e-mails. Plus précisément, les notifications seront envoyées à l’adresse e-mail définie dans la section Coordonnées du vendeur de vos paramètres de compte dans l’Espace partenaires, pour vous informer de la progression de l’opération.<br><br>Vérifiez l’adresse e-mail définie dans la section Coordonnées du vendeur de [Paramètres du compte](https://partner.microsoft.com/dashboard/account/management) dans l’Espace partenaires afin de vous assurer que l’adresse e-mail correcte est fournie pour les notifications.  |
| | | |
