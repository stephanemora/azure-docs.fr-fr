---
title: Guide pratique pour créer une demande de support Azure pour un problème lié au Contrat Entreprise
description: Les clients Contrat Entreprise qui ont besoin d’aide peuvent utiliser le portail Azure afin de trouver des solutions en libre-service, et pour créer et gérer des demandes de support.
ms.topic: troubleshooting
ms.date: 10/07/2021
ms.author: banders
author: bandersmsft
ms.reviewer: sapnakeshari
ms.service: cost-management-billing
ms.subservice: billing
ms.openlocfilehash: 7826b2c19a77ef3762f39ade9a80f26615f704bd
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129660188"
---
# <a name="create-an-azure-support-request-for-an-enterprise-agreement-issue"></a>Créer une demande de support Azure pour un problème lié au Contrat Entreprise

Azure vous permet de créer et gérer des demandes de support, également appelées tickets de support, pour les Contrats Entreprise. Vous pouvez créer et gérer des demandes dans le [portail Azure](https://portal.azure.com), comme décrit dans cet article. Vous pouvez également créer et gérer des demandes par programmation, en utilisant l’[API REST de ticket de support Azure](/rest/api/support) ou [Azure CLI](/cli/azure/azure-cli-support-request).

> [!NOTE]
> L’URL du portail Azure est spécifique au cloud Azure sur lequel votre organisation est déployée.
>
>- Portail Azure pour une utilisation commerciale : [https://portal.azure.com](https://portal.azure.com)
>- Portail Azure pour l’Allemagne : [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>- Portail Azure pour le gouvernement des États-Unis : [https://portal.azure.us](https://portal.azure.us)

Azure offre une prise en charge illimitée de la gestion des abonnements, ce qui inclut la facturation, le réglage des quotas et les transferts de compte. Vous avez besoin d’un plan de support pour le support technique. Pour plus d’informations, consultez [Comparer les plans de support](https://azure.microsoft.com/support/plans).

## <a name="getting-started"></a>Prise en main

Vous pouvez accéder à **Aide + support** dans le portail Azure. Cette fonctionnalité est disponible dans le menu du portail Azure, dans l’en-tête global ou dans le menu des ressources d’un service. Avant de pouvoir faire une demande de support, vous devez disposer des autorisations appropriées.

### <a name="azure-role-based-access-control"></a>Contrôle d'accès en fonction du rôle Azure

Afin de créer une demande de support pour un Contrat Entreprise, vous devez être administrateur d’entreprise ou administrateur de partenaire associé à une inscription d’entreprise. 

### <a name="go-to-help--support-from-the-global-header"></a>Accédez à Aide + support dans l’en-tête global

Pour démarrer une demande de support à partir de n’importe où dans le portail Azure :

1. Sélectionnez le symbole de point d’interrogation dans l’en-tête général, puis sélectionnez **Aide et support**.

   :::image type="content" source="media/how-to-create-azure-support-request-ea/help-support-new-lower.png" alt-text="Capture d’écran du menu Aide dans le portail Azure.":::

1. Sélectionnez **Créer une demande de support**. Suivez les invites pour fournir des informations sur votre problème. Nous vous suggérerons des solutions possibles, collecterons des détails sur le problème et vous aiderons à soumettre et à suivre la demande de support.

   :::image type="content" source="media/how-to-create-azure-support-request-ea/new-support-request-2-lower.png" alt-text="Capture d’écran de la page Aide + support avec le lien Créer une demande de support.":::

### <a name="go-to-help--support-from-a-resource-menu"></a>Accéder à Aide + support dans le menu d’une ressource

Pour démarrer une demande de support :

1. Dans le menu de la ressource, dans la section **Support + dépannage**, sélectionnez **Nouvelle demande de support**.

   :::image type="content" source="media/how-to-create-azure-support-request-ea/in-context-2-lower.png" alt-text="Capture d’écran de l’option Nouvelle demande de support dans le volet des ressources.":::

1. Suivez les invites pour nous fournir des informations sur le problème que vous rencontrez. Quand vous démarrez le processus de demande de support à partir de la ressource, certaines options sont présélectionnées pour vous.

## <a name="create-a-support-request"></a>Création d’une demande de support

Nous allons vous guider tout au long des étapes à suivre pour recueillir des informations sur votre problème et vous aider à le résoudre. Chaque étape est décrite dans les sections suivantes.

### <a name="problem-description"></a>Description du problème

1. Tapez un récapitulatif de votre problème, puis sélectionnez le **Type de problème**. 
1. Dans la liste **Type de problème**, sélectionnez **Administration d’inscription** pour les problèmes liés au portail EA.  
    :::image type="content" source="./media/how-to-create-azure-support-request-ea/select-issue-type-enrollment-administration.png" alt-text="Capture d’écran montrant la sélection d’Administration d’inscription." lightbox="./media/how-to-create-azure-support-request-ea/select-issue-type-enrollment-administration.png" :::
1. Pour le **Numéro d’inscription**, sélectionnez le numéro d’inscription. 
    :::image type="content" source="./media/how-to-create-azure-support-request-ea/select-enrollment.png" alt-text="Capture d’écran montrant la sélection du numéro d’inscription." :::
1. Pour le **Type de problème**, sélectionnez la catégorie de problème qui décrit le mieux le type de problème que vous rencontrez.  
    :::image type="content" source="./media/how-to-create-azure-support-request-ea/select-problem-type.png" alt-text="Capture d’écran montrant la sélection d’un type de problème." :::
1. Pour **Sous-type de problème**, sélectionnez une sous-catégorie de problème. 

Une fois que vous avez fourni tous ces détails, sélectionnez **Suivant : Solutions**.

### <a name="recommended-solution"></a>Solution recommandée

En fonction des informations que vous avez fournies, nous vous présentons les solutions recommandées que vous pouvez utiliser pour essayer de résoudre le problème. Dans certains cas, nous pouvons même exécuter un diagnostic rapide. Écrites par des ingénieurs Azure, les solutions résolvent les problèmes les plus courants.

Si vous ne parvenez toujours pas à résoudre le problème, continuez à créer votre demande de support en sélectionnant **Suivant : Détails**.

### <a name="additional-details"></a>Informations supplémentaires

Nous collectons ensuite plus de détails sur le problème. En fournissant des informations complètes et détaillées à cette étape, vous nous aidez à acheminer votre demande de support vers l’ingénieur approprié.

1. Sous l’onglet Détails, indiquez les **Détails du problème** pour nous fournir plus d’informations sur votre problème. Si possible, indiquez-nous quand le problème a commencé et les étapes permettant de le reproduire. Vous pouvez charger un fichier, tel qu’un fichier journal ou le résultat d’un diagnostic. Pour plus d’informations sur les chargements de fichiers, consultez [Instructions de chargement de fichier](../../azure-portal/supportability/how-to-manage-azure-support-request.md#file-upload-guidelines).

1. Dans la section **Partager les informations de diagnostic**, sélectionnez **Oui** ou **Non**. La sélection de **Oui** permet au support Azure de collecter des [informations de diagnostic](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) à partir de vos ressources Azure. Si vous préférez ne pas partager ces informations, sélectionnez **Non**. Dans certains cas, vous avez d’autres options possibles.

1. Dans la section **Méthode de support**, sélectionnez la gravité du problème. Le niveau de gravité maximale dépend de votre [plan de support](https://azure.microsoft.com/support/plans).

1. Indiquez votre méthode de contact préférée, votre disponibilité et votre langue de support préférée.

1. Ensuite, complétez la section **Informations de contact** afin que nous sachions comment vous contacter.  
    :::image type="content" source="./media/how-to-create-azure-support-request-ea/details-tab.png" alt-text="Capture d’écran montrant l’onglet Détails." lightbox="./media/how-to-create-azure-support-request-ea/details-tab.png" :::

Dès que vous avez renseigné toutes les informations nécessaires, sélectionnez **Suivant : Vérifier + créer**.

### <a name="review--create"></a>Vérifier + créer

Avant de créer votre demande, passez en revue tous les détails que vous enverrez au support technique. Vous pouvez sélectionner **Précédent** pour revenir à n’importe quel onglet si vous devez apporter des modifications. Une fois que la demande de support est complète, sélectionnez **Créer**.

Un ingénieur de support vous contactera en utilisant la méthode que vous avez indiquée. Pour plus d’informations sur le temps de réponse initial, consultez [Étendue du support et réactivité](https://azure.microsoft.com/support/plans/response/).

## <a name="cant-create-request-with-microsoft-account"></a>Impossible de créer une demande avec un compte Microsoft

Si vous avez un compte Microsoft (MSA), vous ne pouvez pas créer de ticket de support Azure. Les comptes Microsoft sont créés pour des services comme Outlook, Windows Live et Hotmail.

Pour créer un ticket de support Azure, un *compte professionnel* doit avoir le rôle Administrateur EA ou Administrateur de partenaire.

Si vous avez un MSA, demandez à un administrateur de créer un compte professionnel pour vous. Un administrateur d’entreprise ou un administrateur de partenaire doit ensuite ajouter votre compte professionnel comme administrateur d’entreprise ou administrateur de partenaire. Vous pouvez alors utiliser votre compte professionnel pour effectuer une demande de support.

- Pour ajouter un administrateur d’entreprise, consultez [Créer un autre administrateur d’entreprise](ea-portal-administration.md#create-another-enterprise-administrator).
- Pour ajouter un administrateur de partenaire, consultez [Gérer les administrateurs de partenaire](ea-partner-portal-administration.md#manage-partner-administrators).

## <a name="next-steps"></a>Étapes suivantes

Suivez ces liens pour en savoir plus :

* [Guide pratique pour gérer une demande de support Azure]()../../azure-portal/supportability/how-to-manage-azure-support-request.md)
* [API REST de ticket de support Azure](/rest/api/support)
* Nous contacter sur [Twitter](https://twitter.com/azuresupport)
* Obtenez l’aide de vos pairs sur la [page de questions Microsoft Q&A](/answers/products/azure)
* En découvrir plus dans le [Forum aux questions du support Azure](https://azure.microsoft.com/support/faq)