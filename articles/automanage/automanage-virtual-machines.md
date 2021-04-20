---
title: Azure Automanage pour machines virtuelles
description: Découvrez Azure Automanage pour machines virtuelles.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 514f1af2a1b120254840986fc5ceb803dfc24345
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363374"
---
# <a name="azure-automanage-for-virtual-machines"></a>Azure Automanage pour machines virtuelles

Cet article contient des informations sur le service Azure Automanage pour machines virtuelles, qui offre les avantages suivants :

- intègre intelligemment des machines virtuelles afin de sélectionner les meilleures pratiques pour les services Azure ;
- configure automatiquement chaque service conformément aux meilleures pratiques Azure ;
- surveille la dérive et la corrige en cas de détection ;
- offre une interface simple (pointer, cliquer, définir, oublier).


## <a name="overview"></a>Vue d’ensemble

Azure Automanage pour machines virtuelles est un service qui élimine la nécessité de découvrir, de savoir comment intégrer et de configurer certains services Azure susceptibles de tirer parti de votre machine virtuelle. Ces services sont considérés comme des services liés aux bonnes pratiques Azure. Ils contribuent à améliorer la fiabilité, la sécurité et la gestion des machines virtuelles. Voici des exemples de services : [Azure Update Management](../automation/update-management/overview.md) et [Sauvegarde Azure](../backup/backup-overview.md).

Une fois vos machines virtuelles intégrées avec Azure Automanage, chaque service lié aux bonnes pratiques est configuré selon ses paramètres recommandés. Les meilleures pratiques sont distinctes pour chaque service. Par exemple, dans Sauvegarde Azure, la meilleure pratique peut être de sauvegarder la machine virtuelle quotidiennement et de respecter une période de rétention de six mois.

Le service Azure Automanage surveille également automatiquement la dérive et la corrige en cas de détection. Cela signifie que, si votre machine virtuelle est intégrée au service Azure Automanage, nous allons non seulement la configurer conformément aux meilleures pratiques Azure, mais aussi la surveiller en veillant à ce qu’elle reste conforme à ces meilleures pratiques tout au long de son cycle de vie. Si votre machine virtuelle dévie ou s’écarte de ces bonnes pratiques (par exemple, si un service est débarqué), nous le corrigeons et nous ramenons votre machine à l’état souhaité.

## <a name="prerequisites"></a>Prérequis

Il existe plusieurs conditions préalables à prendre en compte avant d’essayer d’activer le service Azure Automanage sur vos machines virtuelles.

- [Versions de Windows Server](automanage-windows-server.md#supported-windows-server-versions) et [Distributions Linux](automanage-linux.md#supported-linux-distributions-and-versions) prises en charge
- Les machines virtuelles doivent se trouver dans une région prise en charge (voir ci-dessous)
- L’utilisateur doit disposer des autorisations appropriées (voir ci-dessous)
- Automanage ne prend pas en charge les abonnements sandbox à ce stade

### <a name="supported-regions"></a>Régions prises en charge
Automanage prend uniquement en charge les machines virtuelles situées dans les régions suivantes :
* Europe Ouest
* Europe Nord
* USA Centre
* USA Est
* USA Est 2
* USA Ouest
* USA Ouest 2
* Centre du Canada
* Centre-USA Ouest
* États-Unis - partie centrale méridionale
* Japon Est
* Sud du Royaume-Uni
* Australie Est
* Australie Sud-Est
* Asie Sud-Est

### <a name="required-rbac-permissions"></a>Autorisations RBAC nécessaires
Votre compte nécessite des rôles RBAC légèrement différents selon que vous activez ou non Automanage avec un nouveau compte Automanage.

Si vous activez Automanage avec un nouveau compte Automanage :
* Rôle **Propriétaire** pour les abonnements contenant vos machines virtuelles, _**ou**_
* Rôles **Contributeur** et **Administrateur de l’accès utilisateur** pour les abonnements contenant vos machines virtuelles

Si vous activez Automanage avec un compte Automanage existant :
* Rôle **Contributeur** pour le groupe de ressources contenant vos machines virtuelles

Le compte Automanage disposera des autorisations **Contributeur** et **Contributeur de la stratégie de ressource** pour effectuer des actions sur les machines autogérées.

> [!NOTE]
> Si vous souhaitez utiliser Automanage sur une machine virtuelle qui est connectée à un espace de travail dans un autre abonnement, vous devez disposer des autorisations décrites ci-dessus pour chaque abonnement.

## <a name="participating-services"></a>Services participant

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services-1.png" alt-text="Services intégrés de manière intelligente.":::

Pour obtenir la liste complète des services Azure concernés ainsi que leur environnement pris en charge, consultez :
- [Automanage pour Linux](automanage-linux.md)
- [Automanage pour Windows Server](automanage-windows-server.md)

 Nous vous intégrerons automatiquement à ces services participant. Ils sont essentiels pour notre livre blanc sur les meilleures pratiques, que vous pouvez trouver dans notre [Cloud Adoption Framework](/azure/cloud-adoption-framework/manage/azure-server-management).

Pour tous ces services, nous les intégrons et les configurons automatiquement, surveillons leur dérive et corrigeons toute dérive détectée.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Activation du service Automanage pour machines virtuelles dans le portail Azure

Dans le portail Azure, vous pouvez activer le service Automanage sur une machine virtuelle existante ou lorsque vous créez une machine virtuelle. Pour les étapes concises de ce processus, consultez le [Démarrage rapide du service Automanage pour machines virtuelles](quick-create-virtual-machines-portal.md).

Si c’est la première fois que vous activez le service Automanage pour votre machine virtuelle, vous pouvez rechercher dans le portail Azure **Automanage – Meilleures pratiques pour les machines virtuelles Azure**. Cliquez sur **Activer sur une machine virtuelle existante**, sélectionnez les machines virtuelles que vous souhaitez intégrer, puis cliquez sur **Sélectionner** et **Activer**.

La seule situation dans laquelle vous pourriez être amené à interagir avec cette machine virtuelle pour gérer ces services serait si nous tentions de corriger votre machine virtuelle sans succès. Quand nous parvenons à corriger correctement votre machine virtuelle, nous rétablissons sa conformité sans même vous en informer. Pour plus d’informations, consultez [État des machines virtuelles](#status-of-vms).

## <a name="enabling-automanage-for-vms-using-azure-policy"></a>Activation du service Automanage pour machines virtuelles à l’aide d’Azure Policy
Vous pouvez également activer le service Automanage sur les machines virtuelles à grande échelle à l’aide de la stratégie intégrée Azure Policy. La stratégie a un effet DeployIfNotExists, ce qui signifie que toutes les machines virtuelles éligibles situées dans l’étendue de la stratégie seront automatiquement intégrées à Automanage - Bonnes pratiques pour les machines virtuelles.

Vous trouverez [ici](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F270610db-8c04-438a-a739-e8e6745b22d3) un lien direct vers la stratégie.

### <a name="how-to-apply-the-policy"></a>Comment appliquer la stratégie
1. Cliquez sur le bouton **Attribuer** quand la définition de stratégie est affichée
1. Sélectionnez l’étendue à laquelle vous souhaitez appliquer la stratégie (il peut s’agir d’un groupe d’administration, d’un abonnement ou d’un groupe de ressources)
1. Sous **Paramètres**, spécifiez les paramètres pour le compte Automanage, le profil de configuration et l’effet (l’effet doit généralement être DeployIfNotExists)
    1. Si vous n’avez pas de compte Automanage, vous devez en [créer un](./automanage-account.md).
1. Sous **Correction**, cochez la case « Cliquer sur une tâche de correction ». L’intégration au service Automanage est alors effectuée.
1. Cliquez sur **Vérifier + créer** et vérifiez que tous les paramètres sont corrects.
1. Cliquez sur **Créer**.

## <a name="environment-configuration"></a>Configuration de l’environnement

Quand vous activez Automanage pour votre machine virtuelle, un environnement est nécessaire. Les environnements constituent le fondement de ce service. Ils définissent les services que nous intégrons sur vos machines et, dans une certaine mesure, la configuration de ces services.

### <a name="default-environments"></a>Environnements par défaut

Deux environnements sont disponibles.

- L’environnement **Dev/Test** est conçu pour les machines de dev/test.
- L’environnement **Production** est destiné à la production.

La raison de cette différentiation est que certains services sont recommandés en fonction de la charge de travail en cours d’exécution. Par exemple, sur une machine de Production, nous vous intégrons automatiquement au service Sauvegarde Azure. En revanche, pour une machine de Dev/Test, un service de sauvegarde occasionnerait un coût inutile, car les machines de Dev/Test ont généralement une moindre incidence sur l’activité.

### <a name="customizing-an-environment-using-preferences"></a>Personnalisation d’un environnement à l’aide des préférences

En plus des services standard auxquels nous vous intégrons, nous vous permettons de configurer un sous-ensemble de préférences. Ces préférences sont autorisées au sein d’un éventail d’options de configuration. Par exemple, dans le cas du service Sauvegarde Azure, nous vous autorisons à définir la fréquence de la sauvegarde et le jour de la semaine où elle se produit.

> [!NOTE]
> Dans l’environnement Dev/Test, nous ne sauvegardons pas du tout la machine virtuelle.

Vous pouvez modifier les paramètres d’un environnement par défaut via les préférences. Découvrez comment créer une préférence [ici](virtual-machines-custom-preferences.md).

> [!NOTE]
> Vous ne pouvez pas changer la configuration de l’environnement sur votre machine virtuelle quand Automanage est activé. Vous devez désactiver Automanage pour cette machine virtuelle, puis réactiver Automanage avec l’environnement et les préférences souhaités.

Pour obtenir la liste complète des services Azure concernés et pour savoir s’ils prennent en charge les préférences, consultez :
- [Automanage pour Linux](automanage-windows-server.md)
- [Automanage pour Windows Server](automanage-windows-server.md)


## <a name="automanage-account"></a>Compte Automanage

Le Compte Automanage est le contexte de sécurité ou l’identité sous laquelle les opérations automatisées se produisent. En règle générale, il est inutile de sélectionner l’option Compte Automanage, mais s’il existe un scénario de délégation dans lequel vous souhaitez diviser la gestion automatisée de vos ressources (peut-être entre deux administrateurs système), l’option Compte Automanage dans le flux d’activation vous permet de définir une identité Azure pour chacun de ces administrateurs.

Pour en savoir plus sur le compte Automanage et la procédure à suivre pour en créer un, visitez le [document Compte Automanage](./automanage-account.md).

## <a name="status-of-vms"></a>État des machines virtuelles

Dans le portail Azure, accédez à la page **Automanage – Meilleurs pratiques pour les machines virtuelles Azure**, qui répertorie toutes vos machines virtuelles gérées automatiquement. Vous verrez ici l’état global de chaque machine virtuelle.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="Liste des machines virtuelles configurées.":::

Pour chaque machine virtuelle listée, les détails suivants sont affichés : nom, environnement, préférence de configuration, état, système d’exploitation, compte, abonnement et groupe de ressources.

La colonne **État** peut afficher les états suivants :
- *En cours* : la machine virtuelle vient d’être activée et est en cours de configuration.
- *Configurée* : la machine virtuelle est configurée et aucune dérive n’est détectée.
- *Échec*  : la machine virtuelle a dérivé et nous n’avons pas pu la corriger.
- *En attente* : la machine virtuelle n’est pas en cours d’exécution et Automanage tentera d’intégrer ou de corriger la machine virtuelle lors de sa prochaine exécution.

Si vous voyez l’**État** *Échec*, vous pouvez résoudre des problèmes de déploiement via le groupe de ressources dans lequel se trouve votre machine virtuelle. Accédez à **Groupes de ressources**, sélectionnez votre groupe de ressources, cliquez sur **Déploiements**, puis consultez l’état *Échec* contenant tous les détails de l’erreur.


## <a name="disabling-automanage-for-vms"></a>Désactivation du service Automanage pour machines virtuelles

Vous pouvez décider un jour de désactiver le service Automanage sur certaines machines virtuelles. Par exemple, si votre machine exécute une charge de travail sécurisée extrêmement sensible, et si vous devez la verrouiller encore plus que ne le ferait Azure naturellement, vous devez configurer la machine en dehors des meilleures pratiques Azure.

Pour ce faire, dans le portail Azure, accédez à la page **Automanage – Meilleures pratiques pour les machines virtuelles Azure** qui répertorie toutes vos machines virtuelles gérées automatiquement. Activez la case à cocher en regard de la machine virtuelle que vous souhaitez désactiver dans le service Automanage, puis cliquez sur le bouton **Désactiver la gestion automatique**.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Désactivation du service Automanage sur une machine virtuelle.":::

Lisez attentivement le message dans la fenêtre contextuelle qui s’affiche avant d’accepter de **Désactiver**.

> [!NOTE]
> La désactivation de l’autogestion dans une machine virtuelle entraîne le comportement suivant :
>
> - La configuration de la machine virtuelle et des services qu’elle contient ne change pas.
> - Tous les frais associés à ces services resteront facturables et seront accumulés.
> - La surveillance de la dérive d’Automanage s’arrête immédiatement.


Tout d’abord, nous n’annulons l’intégration de la machine virtuelle à l’un des services auxquels nous l’avons intégrée et sur lesquels nous l’avons configurée. Par conséquent, les frais associés à ces services resteront facturables. Vous devez donc annuler l’intégration si nécessaire. Dans ce cas, tout comportement du service Automanage s’arrête immédiatement. Par exemple, nous cessons surveiller la dérive de la machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a expliqué que le service Automanage pour machines virtuelles vous évite d’avoir à connaître, intégrer et configurer les meilleures pratiques pour les services Azure. De plus, si une machine que vous avez intégrée à Automanage pour machines virtuelles dévie de la configuration de l’environnement, nous la remettons automatiquement en conformité.

Essayez d’activer le service Automanage pour machines virtuelles dans le portail Azure.

> [!div class="nextstepaction"]
> [Activer le service Automanage pour machines virtuelles dans le portail Azure](quick-create-virtual-machines-portal.md)