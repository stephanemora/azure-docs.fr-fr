---
title: Activer automatiquement la sauvegarde lors de la création de machines virtuelles avec Azure Policy
description: Article décrivant comment utiliser Azure Policy pour activer automatiquement la sauvegarde de toutes les machines virtuelles créées dans une étendue donnée
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: dfa4364eeaa9f5b60af3f5d6a19aaeb188d4f65e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101707300"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Activer automatiquement la sauvegarde lors de la création de machines virtuelles avec Azure Policy

L’une des principales responsabilités d’un administrateur de sauvegarde ou de conformité dans une organisation est de s’assurer que toutes les machines vitales pour l’entreprise sont sauvegardées avec la rétention appropriée.

Aujourd’hui, Sauvegarde Azure fournit un ensemble de stratégies intégrées (utilisant [Azure Policy](../governance/policy/overview.md)) pour vous aider à vous assurer automatiquement que vos machines virtuelles Azure sont configurées pour la sauvegarde. En fonction de l’organisation de vos ressources et équipes de sauvegarde, vous pouvez utiliser l’une des stratégies suivantes :

## <a name="policy-1---configure-backup-on-vms-without-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>Stratégie 1 – Configurer une sauvegarde sur des machines virtuelles sans étiquette spécifique vers un coffre Recovery Services existant au même emplacement

Si votre organisation dispose d’une équipe de sauvegarde centrale qui gère les sauvegardes entre les équipes d’applications, vous pouvez utiliser cette stratégie pour configurer la sauvegarde vers un coffre Recovery Services central existant dans le même abonnement et le même emplacement que les machines virtuelles gouvernées. Vous pouvez choisir d’**exclure** de l’étendue de cette stratégie les machines virtuelles qui contiennent une étiquette spécifique.

## <a name="policy-2---preview-configure-backup-on-vms-with-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>Stratégie 2 – [Préversion] Configurer une sauvegarde sur des machines virtuelles avec une étiquette donnée vers un coffre Recovery Services existant au même emplacement
Cette stratégie fonctionne de la même façon que la stratégie 1 ci-dessus, la seule différence étant que vous pouvez utiliser cette stratégie pour **inclure** dans l’étendue de cette stratégie les machines virtuelles qui contiennent une étiquette spécifique. 

## <a name="policy-3---preview-configure-backup-on-vms-without-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>Stratégie 3 – [Préversion] Configurer une sauvegarde sur des machines virtuelles sans étiquette donnée vers un nouveau coffre Recovery Services avec une stratégie par défaut
Si vous organisez des applications dans des groupes de ressources dédiés et que vous souhaitez qu’elles soient sauvegardées par le même coffre, cette stratégie vous permet de gérer cette action automatiquement. Vous pouvez choisir d’**exclure** de l’étendue de cette stratégie les machines virtuelles qui contiennent une étiquette spécifique.

## <a name="policy-4---preview-configure-backup-on-vms-with-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>Stratégie 4 – [Préversion] Configurer une sauvegarde sur des machines virtuelles avec une étiquette donnée vers un nouveau coffre Recovery Services avec une stratégie par défaut
Cette stratégie fonctionne de la même façon que la stratégie 3 ci-dessus, à la seule différence que vous pouvez utiliser cette stratégie pour **inclure** dans l’étendue de cette stratégie les machines virtuelles qui contiennent une étiquette spécifique. 

Outre les stratégies précédentes, Sauvegarde Azure fournit également une stratégie [Auditer uniquement](../governance/policy/concepts/effects.md#audit) – **La Sauvegarde Azure doit être activée pour les machines virtuelles**. Cette stratégie identifie les machines virtuelles pour lesquelles la sauvegarde n’est pas activée, mais elle ne configure pas automatiquement les sauvegardes pour ces machines virtuelles. Cela s’avère utile quand vous cherchez uniquement à évaluer la compatibilité générale des machines virtuelles sans envisager d’agir immédiatement.

## <a name="supported-scenarios"></a>Scénarios pris en charge

* La stratégie intégrée est actuellement prise en charge uniquement pour les machines virtuelles Azure. Les utilisateurs doivent veiller à ce que la stratégie de rétention spécifiée lors de l’attribution soit une stratégie de rétention de machine virtuelle. Reportez-vous à [ce document](./backup-azure-policy-supported-skus.md) pour voir toutes les références SKU de machines virtuelles prises en charge par cette stratégie.

* Les stratégies 1 et 2 peuvent être attribuées à un seul emplacement et à un seul abonnement à la fois. Pour activer la sauvegarde des machines virtuelles sur plusieurs emplacements et abonnements, plusieurs instances de l’attribution de stratégie doivent être créées, une pour chaque combinaison d’emplacement et d’abonnement.

* Pour les stratégies 1 et 2, l’étendue d’un groupe d’administration n’est pas prise en charge pour le moment.

* Pour les stratégies 1 et 2, le coffre spécifié et les machines virtuelles configurées pour la sauvegarde peuvent se trouver dans des groupes de ressources différents.

* Les stratégies 1, 2, 3 et 4 ne sont actuellement pas prises en charge dans les clouds nationaux.

* Les stratégies 3 et 4 peuvent être attribuées à un seul abonnement à la fois (ou à un groupe de ressources au sein d’un abonnement).

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="using-the-built-in-policies"></a>Utilisation des stratégies intégrées

Les étapes ci-dessous décrivent le processus de bout en bout pour attribuer la stratégie 1 : **Configurer une sauvegarde sur des machines virtuelles sans étiquette spécifique dans un coffre Recovery Services existant au même emplacement** à une étendue donnée. Des instructions similaires s’appliquent aux autres stratégies. Après l’attribution, toute nouvelle machine virtuelle créée dans l’étendue est automatiquement configurée pour la sauvegarde.

1. Connectez-vous au Portail Azure et accédez au tableau de bord **Stratégie**.
2. Sélectionnez **Définitions** dans le menu de gauche pour obtenir la liste de toutes les stratégies intégrées dans les ressources Azure.
3. Filtrez la liste en sélectionnant **Catégorie=Sauvegarde** et sélectionnez la stratégie nommée « Configurer la sauvegarde sur les machines virtuelles d’un emplacement dans un coffre central existant au même emplacement ».
![Tableau de bord Stratégie](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Sélectionnez le nom de la stratégie. Vous serez redirigé vers sa définition détaillée.
![Policy Definition pane](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. Sélectionnez le bouton **Attribuer** en haut du volet. Cela vous redirige vers le volet **Attribuer une stratégie**.
6. Sous **Fonctions de base**, sélectionnez le bouton de sélection à côté du champ **Étendue**. Cela ouvre un volet contextuel à droite dans lequel vous pouvez sélectionner l’abonnement de la stratégie à appliquer. Si vous le souhaitez, vous pouvez également sélectionner un groupe de ressources, de façon à ce que la stratégie soit appliquée uniquement pour les machines virtuelles d’un groupe de ressources particulier.
![Concepts de base de l’attribution d’une stratégie](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. Dans l’onglet **Paramètres**, choisissez un emplacement dans la liste déroulante, puis sélectionnez le coffre et la stratégie de sauvegarde auxquels les machines virtuelles de l’étendue doivent être associées. Vous pouvez également choisir de spécifier des valeurs de nom d’étiquette et de tableau d’étiquette. Une machine virtuelle qui contient l’une des valeurs spécifiées pour l’étiquette spécifique sera exclue de l’étendue d’attribution de la stratégie.
![Paramètres d’attribution de stratégie](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Assurez-vous que la valeur **Effet** est configurée sur deployIfNotExists.
9. Accédez à **Vérifier+créer**, puis sélectionnez **Créer**.

> [!NOTE]
>
> Azure Policy peut également être utilisé sur des machines virtuelles existantes, à l’aide de la [correction](../governance/policy/how-to/remediate-resources.md).

> [!NOTE]
>
> Nous vous recommandons de ne pas attribuer cette stratégie à plus de 200 machines virtuelles à la fois. Si la stratégie est attribuée à plus de 200 machines virtuelles, ce nombre peut entraîner le déclenchement de la sauvegarde quelques heures plus tard que celle spécifiée par la planification.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur Azure Policy](../governance/policy/overview.md)