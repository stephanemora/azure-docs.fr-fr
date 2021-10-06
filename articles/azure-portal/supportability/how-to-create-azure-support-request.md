---
title: Création d’une demande de support Azure
description: Les clients qui ont besoin d’aide peuvent utiliser le portail Azure pour trouver des solutions en libre-service et pour créer et gérer des demandes de support.
ms.topic: how-to
ms.custom: support-help-page
ms.date: 09/01/2021
ms.openlocfilehash: 768c778926024288b0d331b5cb6b60c8e9c16a57
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123429458"
---
# <a name="create-an-azure-support-request"></a>Créer une demande de support Azure

Azure vous permet de créer et gérer les demandes de support, également appelées tickets de support. Vous pouvez créer et gérer des demandes dans le [portail Azure](https://portal.azure.com), comme décrit dans cet article. Vous pouvez également créer et gérer des demandes par programmation, en utilisant l’[API REST de ticket de support Azure](/rest/api/support) ou [Azure CLI](/cli/azure/azure-cli-support-request).

> [!NOTE]
> L’URL du portail Azure est spécifique au cloud Azure sur lequel votre organisation est déployée.
>
>* Portail Azure pour une utilisation commerciale : [https://portal.azure.com](https://portal.azure.com)
>* Portail Azure pour l’Allemagne : [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* Portail Azure pour le gouvernement des États-Unis : [https://portal.azure.us](https://portal.azure.us)

Azure offre une prise en charge illimitée de la gestion des abonnements, ce qui inclut la facturation, le réglage des quotas et les transferts de compte. Pour bénéficier du support, vous devez disposer d’un plan de support. Pour plus d’informations, consultez [Comparer les plans de support](https://azure.microsoft.com/support/plans).

## <a name="getting-started"></a>Prise en main

Vous pouvez accéder à **Aide + support** dans le portail Azure. Cette fonctionnalité est disponible dans le menu du portail Azure, dans l’en-tête global ou dans le menu des ressources d’un service. Avant de pouvoir faire une demande de support, vous devez disposer des autorisations appropriées.

### <a name="azure-role-based-access-control"></a>Contrôle d'accès en fonction du rôle Azure

Pour créer une demande de support, vous devez être [propriétaire](../../role-based-access-control/built-in-roles.md#owner) ou [contributeur](../../role-based-access-control/built-in-roles.md#contributor) ou le rôle [Contributeur de demande de support](../../role-based-access-control/built-in-roles.md#support-request-contributor) doit vous avoir été attribuer au niveau de l’abonnement. Pour créer une demande de support sans abonnement, par exemple dans un scénario Azure Active Directory, vous devez être un [administrateur](../../active-directory/roles/permissions-reference.md).

### <a name="go-to-help--support-from-the-global-header"></a>Accédez à Aide + support dans l’en-tête global

Pour démarrer une demande de support à partir de n’importe où dans le portail Azure :

1. Sélectionnez **?** dans l’en-tête global, puis sélectionnez **Aide + support**.

   :::image type="content" source="media/how-to-create-azure-support-request/helpandsupportnewlower.png" alt-text="Capture d’écran du menu Aide dans le portail Azure.":::

1. Sélectionnez **Créer une demande de support**. Suivez les invites pour fournir des informations sur votre problème. Nous vous suggérerons des solutions possibles, collecterons des détails sur le problème et vous aiderons à soumettre et à suivre la demande de support.

   :::image type="content" source="media/how-to-create-azure-support-request/newsupportrequest2lower.png" alt-text="Capture d’écran de la page Aide + support avec le lien Créer une demande de support.":::

### <a name="go-to-help--support-from-a-resource-menu"></a>Accéder à Aide + support dans le menu d’une ressource

Pour démarrer une demande de support dans le contexte de la ressource en cours d’utilisation :

1. Dans le menu de la ressource, dans la section **Support + dépannage**, sélectionnez **Nouvelle demande de support**.

   :::image type="content" source="media/how-to-create-azure-support-request/incontext2lower.png" alt-text="Capture d’écran de l’option Nouvelle demande de support dans le volet des ressources.":::

1. Suivez les invites pour nous fournir des informations sur le problème que vous rencontrez. Quand vous démarrez le processus de demande de support à partir de la ressource, certaines options sont présélectionnées pour vous.

## <a name="create-a-support-request"></a>Création d’une demande de support

Nous allons vous guider tout au long des étapes à suivre pour recueillir des informations sur votre problème et vous aider à le résoudre. Chaque étape est décrite dans les sections suivantes.

### <a name="problem-description"></a>Description du problème

La première étape du processus de demande de support consiste à sélectionner un type de problème. Vous serez ensuite invité à fournir des informations supplémentaires, qui peuvent varier en fonction du type de problème que vous avez sélectionné. Dans la plupart des cas, vous devrez spécifier un abonnement, décrire brièvement votre problème et sélectionner un type de problème. Si vous sélectionnez **Technique**, vous devrez spécifier le service auquel votre problème est lié. En fonction du service, vous verrez des options supplémentaires pour le **Type de problème** et le **Sous-type de problème**.

:::image type="content" source="media/how-to-create-azure-support-request/basics2lower.png" alt-text="Capture d’écran de l’étape de Description du problème du processus de demande de support.":::

Une fois que vous avez fourni tous ces détails, sélectionnez **Suivant**.

### <a name="recommended-solution"></a>Solution recommandée

En fonction des informations que vous avez fournies, nous vous présenterons les solutions recommandées que vous pouvez utiliser pour essayer de résoudre le problème. Dans certains cas, nous pouvons même exécuter un diagnostic rapide. Écrites par des ingénieurs Azure, les solutions résolvent les problèmes les plus courants.

Si vous ne parvenez toujours pas à résoudre le problème, continuez à créer votre demande de support en sélectionnant **Suivant**.

### <a name="additional-details"></a>Informations supplémentaires

Nous recueillons ensuite des détails supplémentaires sur le problème. En fournissant des informations complètes et détaillées à cette étape, vous nous aidez à acheminer votre demande de support vers l’ingénieur approprié.

1. Renseignez les **détails du problème** pour nous fournir plus d’informations sur votre problème. Si possible, indiquez-nous quand le problème a commencé et les étapes permettant de le reproduire. Vous pouvez charger un fichier, tel qu’un fichier journal ou le résultat d’un diagnostic. Pour plus d’informations sur les chargements de fichiers, consultez [Instructions de chargement de fichier](how-to-manage-azure-support-request.md#file-upload-guidelines).

1. Dans la section **Partager les informations de diagnostic**, sélectionnez **Oui** ou **Non**. La sélection de **Oui** permet au support Azure de collecter des [informations de diagnostic](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) à partir de vos ressources Azure. Si vous préférez ne pas partager ces informations, sélectionnez **Non**. Dans certains cas, d’autres options sont disponibles, comme celle d’autoriser l’accès à la mémoire d’une machine virtuelle.

1. Dans la section **Méthode de support**, sélectionnez la gravité de l’impact. Le niveau de gravité maximale dépend de votre [plan de support](https://azure.microsoft.com/support/plans).

1. Indiquez votre méthode de contact préférée, votre disponibilité et votre langue de support préférée.

1. Ensuite, complétez la section **Informations de contact** afin que nous sachions comment vous contacter.

Lorsque vous avez renseigné toutes les informations nécessaires, sélectionnez **Suivant** .

### <a name="review--create"></a>Vérifier + créer

Avant de créer votre demande, passez en revue tous les détails que vous enverrez au support technique. Vous pouvez sélectionner **Précédent** pour revenir à n’importe quel onglet si vous devez apporter des modifications. Une fois que la demande de support est complète, sélectionnez **Créer**.

Un ingénieur de support vous contactera en utilisant la méthode que vous avez indiquée. Pour plus d’informations sur le temps de réponse initial, consultez [Étendue du support et réactivité](https://azure.microsoft.com/support/plans/response/).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les options de support en libre-service dans Azure, regardez cette vidéo :

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

Suivez ces liens pour en savoir plus :

* [Comment gérer une demande de support Azure](how-to-manage-azure-support-request.md)
* [API REST de ticket de support Azure](/rest/api/support)
* Nous contacter sur [Twitter](https://twitter.com/azuresupport)
* Obtenez l’aide de vos pairs sur la [page de questions Microsoft Q&A](/answers/products/azure)
* En découvrir plus dans le [Forum aux questions du support Azure](https://azure.microsoft.com/support/faq)