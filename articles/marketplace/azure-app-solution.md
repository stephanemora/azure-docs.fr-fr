---
title: Configurer un plan de modèle de solution
description: Configurez un plan de modèle de solution pour votre offre d’application Azure dans l’Espace partenaires (Place de marché Azure).
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 22e5bc0aa411fd4561094fc41d5d3e55a5dcdeb8
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111542472"
---
# <a name="configure-a-solution-template-plan"></a>Configurer un plan de modèle de solution

Cet article s’applique uniquement aux plans de modèle de solution pour une offre d’application Azure. Si vous configurez un plan d’application managée, reportez-vous à [Configurer un plan d’application managée](azure-app-managed.md).

## <a name="re-use-technical-configuration-optional"></a>Réutiliser la configuration technique (facultatif)

Si vous avez créé plusieurs plans du même type au sein de cette offre et que la configuration technique est identique, vous pouvez réutiliser la configuration technique d’un autre plan. Ce paramètre n’est plus modifiable une fois le plan publié.

Pour réutiliser la configuration technique :

1. Activez la case à cocher **Ce plan réutilise la configuration technique d’un autre plan du même type**.
1. Dans la liste qui s’affiche, sélectionnez le plan de base souhaité.

> [!NOTE]
> Si un plan est en cours de réutilisation ou a réutilisé la configuration technique d’un autre plan du même type, accédez à ce plan de base pour afficher l’historique des packages précédemment publiés.

## <a name="choose-who-can-see-your-plan"></a>Choisir qui peut voir votre plan

Vous pouvez configurer chaque plan de sorte qu’il soit visible de tous ou d’un public spécifique. Vous accordez l’accès à un public privé à l’aide d’ID d’abonnement Azure, avec la possibilité d’inclure une description de chaque ID d’abonnement que vous attribuez. Vous pouvez ajouter jusqu’à 10 ID d’abonnement manuellement ou jusqu’à 10 000 ID d’abonnement à l’aide d’un fichier .CSV. Les ID d’abonnements Azure sont représentés par des GUID, où les lettres doivent être en minuscules.

> [!NOTE]
> Si vous publiez un plan privé, vous pouvez modifier sa visibilité sur public ultérieurement. Toutefois, une fois que vous avez publié un plan public, vous ne pouvez pas changer sa visibilité sur privé.

Sous l’onglet **Accessibilité**, sous **Visibilité du plan**, effectuez l’une des opérations suivantes :

- Pour rendre le plan public, sélectionnez la case d’option (ou _bouton d’option_) **Public**.
- Pour rendre le plan privé, sélectionnez la case d’option **Privé**, puis ajoutez les ID d’abonnement Azure manuellement ou à l’aide d’un fichier .CSV.

    > [!NOTE]
    > Un public privé ou restreint est différent du public de préversion que vous avez défini sous l’onglet **Préversion**. Un public de préversion peut accéder à votre offre avant sa publication sur la place de marché. Tandis que la sélection d’un public privé ne s’applique qu’à un plan spécifique, le public de préversion peut voir tous les plans (privés ou non) dans le cadre de la validation.

### <a name="manually-add-azure-subscription-ids-for-a-private-plan"></a>Ajouter manuellement des ID d’abonnement Azure pour un plan privé

1. Sous **Visibilité du plan**, sélectionnez la case d’option **Privé**.
1. Dans la zone **ID d’abonnement Azure** qui s’affiche, entrez l’ID d’abonnement Azure du public auquel vous souhaitez accorder l’accès à ce plan privé. Au moins un ID d’abonnement est requis.
1. (Facultatif) Entrez une description de ce public dans la zone **Description**.
1. Pour ajouter un ID d’abonnement, sélectionnez le lien **Ajouter ID (max 10)** , puis répétez les étapes 2 et 3.

## <a name="use-a-csv-file-to-add-azure-subscription-ids-for-a-private-plan"></a>Utiliser un fichier .CSV pour ajouter des ID d’abonnement Azure pour un plan privé

1. Sous **Visibilité du plan**, sélectionnez la case d’option **Privé**.
1. Sélectionnez le lien **Exporter le public (CSV)** .
1. Ouvrez le fichier .CSV et ajoutez les ID d’abonnement Azure auxquels vous souhaitez accorder l’accès à l’offre privée dans la colonne **ID**.
1. Si vous le souhaitez, entrez une description pour chaque public dans la colonne **Description**.
1. Ajoutez « SubscriptionId » dans la colonne **Type** pour chaque ligne contenant un ID d’abonnement.
1. Enregistrez le fichier .CSV.
1. Sous l’onglet **Disponibilité**, sous **Visibilité du plan**, sélectionnez le lien **Importer le public (CSV)** .
1. Dans la boîte de dialogue qui s’affiche, sélectionnez **Oui**.
1. Sélectionnez le fichier .CSV, puis **Ouvrir**. Un message s’affiche, indiquant que le fichier .CSV a été correctement importé.

### <a name="hide-your-plan"></a>Masquer votre plan

Si vous comptez déployer votre modèle de solution uniquement de manière indirecte quand il est référencé par un autre modèle de solution ou une application managée, cochez la case sous **Masquer le plan** pour publier votre modèle de solution, tout en le masquant pour les clients qui essaient de le rechercher et d’y accéder directement.

Avant de passer à la section suivante, sélectionnez **Enregistrer le brouillon** : Définissez la configuration technique.

## <a name="define-the-technical-configuration"></a>Définir la configuration technique

Sous l’onglet **Configuration technique**, vous allez charger le package de déploiement permettant aux clients de déployer votre plan, et fournir un numéro de version pour le package.

### <a name="assign-a-version-number-for-the-package"></a>Affecter un numéro de version pour le package

Dans la zone **Version**, fournissez la version actuelle de la configuration technique. Incrémentez cette version chaque fois que vous publiez une modification apportée à cette page. Le numéro de version doit être au format : entier.entier.entier. Par exemple : `1.0.2`.

### <a name="upload-a-package-file"></a>Charger un fichier de package

Sous **Fichier de package (.zip)** , faites glisser votre fichier de package vers la zone grise, ou sélectionnez le lien pour **parcourir vos fichiers**.

> [!NOTE]
> Si vous rencontrez un problème de téléchargement de fichiers, assurez-vous que votre réseau local ne bloque pas le service `https://upload.xboxlive.com` utilisé par l’espace partenaires.

### <a name="previously-published-packages"></a>Packages précédemment publiés

Une fois que vous avez publié votre offre en direct, le sous-onglet **Packages précédemment publiés** s’affiche sur la page **Configuration technique**. Cet onglet répertorie toutes les versions publiées précédemment de votre configuration technique.

## <a name="view-your-plans"></a>Afficher vos plans

- Sélectionnez **Enregistrer le brouillon**, puis, en haut à gauche de la page, sélectionnez **Vue d’ensemble du plan** pour revenir à l’onglet **Vue d’ensemble du plan**.

Une fois que vous avez créé un ou plusieurs plans, sous l’onglet **Vue d’ensemble du plan**, vous pouvez voir le nom de votre plan, son ID, son type, sa disponibilité (public ou privé), son état de publication actuel et toutes les actions disponibles.

Les actions disponibles dans la colonne **Action** de l’onglet **Vue d’ensemble du plan** varient en fonction de l’état de votre plan et peuvent inclure les éléments suivants :

- Si l’état du plan est **Brouillon**, le lien figurant dans la colonne **Action** indique **Supprimer le brouillon**.
- Si l’état du plan est **Disponible**, le lien dans la colonne **Action** est **Arrêter la vente du plan** ou **Synchroniser le public privé**. Le lien **Synchroniser l’audience privé** publiera uniquement les modifications apportées à votre audience privée, sans publier d’autres mises à jour éventuellement apportées à l’offre.
- Pour créer un autre plan pour cette offre, en haut de l’onglet **Vue d’ensemble du plan**, sélectionnez **+ Créer un plan**. Répétez les étapes décrites dans [Comment créer des plans pour votre offre d’application Azure](azure-app-plans.md). Sinon, si vous avez terminé la création de plans, passez à la section suivante : Étapes suivantes.

## <a name="next-steps"></a>Étapes suivantes

- [Tester et publier cette offre](azure-app-test-publish.md)
- [Vendre cette offre](azure-app-marketing.md) par le biais des programmes **Co-vente avec Microsoft** et/ou **Revente via des fournisseurs de solutions cloud**
