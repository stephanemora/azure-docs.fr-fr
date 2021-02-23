---
title: Unités d’instance BareMetal dans Azure
description: Découvrez comment identifier et interagir avec les unités d’instance BareMetal par le biais du portail Azure.
ms.topic: how-to
ms.date: 02/17/2021
ms.openlocfilehash: 076e84473a7d067712625dd12a2d5cae42bfa91a
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100548163"
---
# <a name="manage-baremetal-instances-through-the-azure-portal"></a>Gérer des instances nues via le portail Azure
 
Cet article montre comment le [portail Azure](https://portal.azure.com/) affiche les [instances BareMetal](baremetal-overview-architecture.md). Cet article vous montre également les activités que vous pouvez effectuer dans le portail Azure avec les unités d’instance BareMetal déployées. 
 
## <a name="register-the-resource-provider"></a>Inscrire le fournisseur de ressources
Un fournisseur de ressources Azure pour les instances BareMetal fournit la visibilité des instances dans le portail Azure, actuellement en version préliminaire publique. Par défaut, l’abonnement Azure que vous utilisez pour les déploiements d’instance BareMetal inscrit le fournisseur de ressources *BareMetalInfrastructure*. Si vous ne voyez pas vos unités d’instance BareMetal déployées, vous devez inscrire le fournisseur de ressources auprès de votre abonnement. 

Vous pouvez inscrire le fournisseur de ressources d’instance BareMetal à l’aide du portail Azure ou d’Azure CLI.

### <a name="portal"></a>[Portail](#tab/azure-portal)
 
Vous devez afficher, dans le portail Azure, votre abonnement qui a été utilisé pour déployer votre ou vos unités d’instance BareMetal, puis double-cliquer dessus.
 
1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans le menu du portail Azure, sélectionnez **Tous les services**.

1. Dans la zone **Tous les services**, entrez **Abonnement**, puis sélectionnez **Abonnements**.

1. Sélectionnez l'abonnement dans la liste.

1. Sélectionnez **Fournisseurs de ressources** et entrez  **BareMetalInfrastructure** dans la zone de recherche. Le fournisseur de ressources doit être **inscrit**, comme le montre l’image.
 
>[!NOTE]
>Si le fournisseur de ressources n’est pas inscrit, sélectionnez **Inscrire**.
 
:::image type="content" source="media/baremetal-infrastructure-portal/register-resource-provider-azure-portal.png" alt-text="Capture d’écran montrant l’unité d’instance BareMetal inscrite":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour commencer à utiliser Azure CLI :

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Connectez-vous à l’abonnement Azure que vous utilisez pour le déploiement de l’instance BareMetal via Azure CLI. Inscrivez le fournisseur de ressources `BareMetalInfrastructure` à l’aide de la commande [az provider register](/cli/azure/provider#az_provider_register) :

```azurecli
az provider register --namespace Microsoft.BareMetalInfrastructure
```

Vous pouvez utiliser la commande [az provider list](/cli/azure/provider#az_provider_list) pour afficher tous les fournisseurs disponibles.

---

Pour plus d’informations sur les fournisseurs de ressources, consultez [Fournisseurs et types de ressources Azure](../../../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="baremetal-instance-units-in-the-azure-portal"></a>Unités d’instance BareMetal dans le portail Azure
 
Lorsque vous envoyez une demande de déploiement d’instance BareMetal, vous spécifiez l’abonnement Azure auquel vous vous connectez aux instances BareMetal. Utilisez le même abonnement que celui utilisé pour déployer la couche d’application qui fonctionne sur les unités d’instance BareMetal.
 
Lors du déploiement de vos instances BareMetal, un nouveau [groupe de ressources Azure](../../../azure-resource-manager/management/manage-resources-portal.md) est créé dans l’abonnement Azure que vous avez utilisé dans la requête de déploiement. Ce nouveau groupe de ressources répertorie toutes les unités d’instance BareMetal que vous avez déployées dans l’abonnement spécifique.

### <a name="portal"></a>[Portail](#tab/azure-portal)

1. Dans l’abonnement BareMetal, dans le portail Azure, sélectionnez **Groupes de ressources**.
 
   :::image type="content" source="media/baremetal-infrastructure-portal/view-baremetal-instance-units-azure-portal.png" alt-text="Capture d’écran montrant la liste de groupes de ressources":::

1. Dans la liste, localisez le nouveau groupe de ressources.
 
   :::image type="content" source="media/baremetal-infrastructure-portal/filter-resource-groups.png" alt-text="Capture d’écran montrant l’unité d’instance BareMetal dans une liste de groupes de ressources filtrée" lightbox="media/baremetal-infrastructure-portal/filter-resource-groups.png":::
   
   >[!TIP]
   >Vous pouvez filtrer sur l’abonnement que vous avez utilisé pour déployer l’instance BareMetal. Une fois que vous avez filtré l’abonnement approprié, vous pourriez avoir une longue liste de groupes de ressources. Recherchez celui ayant un suffixe **-Txxx** où « xxx » représente trois chiffres ; par exemple, **-T250**.

1. Sélectionnez le nouveau groupe de ressources pour en afficher les détails. L’image affiche une unité d’instance BareMetal déployée.
   
   >[!NOTE]
   >Si vous avez déployé plusieurs locataires d’instance BareMetal dans le même abonnement Azure, plusieurs groupes de ressources Azure devraient être affichés.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour afficher toutes vos instances BareMetal, exécutez la commande [az baremetalinstance list](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_list) pour votre groupe de ressources :

```azurecli
az baremetalinstance list --resource-group DSM05A-T550 –output table
```

> [!TIP]
> Le paramètre `--output` est un paramètre global, disponible pour toutes les commandes. La valeur **table** présente la sortie dans un format convivial. Pour plus d’informations, consultez [Formats de sortie pour les commandes Azure CLI](/cli/azure/format-output-azure-cli).

---

## <a name="view-the-attributes-of-a-single-instance"></a>Afficher les attributs d’une instance unique

Vous pouvez afficher les détails d’une seule unité.

### <a name="portal"></a>[Portail](#tab/azure-portal)

Dans la liste d’instances BareMetal, sélectionnez l’instance unique que vous souhaitez afficher.
 
:::image type="content" source="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png" alt-text="Capture d’écran montrant les attributs d’unité d’instance BareMetal d’une instance unique" lightbox="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png":::
 
Les attributs de l’image ne sont pas très différents des attributs des machines virtuelles Azure. Sur la gauche, vous pouvez voir le groupe de ressources, la région Azure et le nom et l’ID de l’abonnement. Si vous avez attribué des étiquettes, vous les verrez ici également. Par défaut, les unités d’instance BareMetal n’ont pas d’étiquettes affectées.
 
À droite, vous verrez le nom de l’unité, le système d’exploitation, l’adresse IP et la référence SKU indiquant la mémoire et le nombre de threads de l’UC. Vous verrez également l’état d’alimentation et la version du matériel (révision du tampon d’instance BareMetal). L’état d’alimentation indique si l’unité matérielle est sous tension ou hors tension. Toutefois, les détails du système d’exploitation n’indiquent pas s’il est en cours d’exécution.
 
Les révisions matérielles possibles sont les suivantes :

* Révision 3 (Rév. 3)

* Révision 4 (Rév. 4)
 
* Révision 4.2 (Rév. 4.2)
 
>[!NOTE]
>Rev 4.2 est la dernière infrastructure BareMetal rebaptisée qui utilise l’architecture Rev 4 existante. Rev 4 assure une plus grande proximité des hôtes de machine virtuelle Azure. Elle présente des améliorations significatives de latence réseau entre les machines virtuelles Azure et les unités d’instance BareMetal déployées dans les tampons ou lignes Rev 4. Vous pouvez accéder à vos instances BareMetal et les gérer via le portail Azure. Pour plus d’informations, consultez l’article [Infrastructure BareMetal sur Azure](baremetal-overview-architecture.md).
 
En outre, sur le côté droit, vous trouverez le [Nom du groupe de placement de proximité Azure](../../../virtual-machines/co-location.md), qui est créé automatiquement pour chaque unité d’instance BareMetal déployée. Référencez le groupe de placement de proximité lorsque vous déployez les machines virtuelles Azure qui hébergent la couche application. Lorsque vous utilisez le groupe de placement de proximité associé à l’unité d’instance BareMetal, vous vous assurez que les machines virtuelles Azure sont déployées près de l’unité d’instance BareMetal.
 
>[!TIP]
>Pour trouver la couche application dans le même centre de données Azure que Révision 4.x, consultez [Groupes de placement de proximité Azure pour une latence réseau optimale](../../../virtual-machines/workloads/sap/sap-proximity-placement-scenarios.md).

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour afficher les détails d’une instance BareMetal, exécutez la commande [az baremetalinstance show](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_show) :

```azurecli
az baremetalinstance show --resource-group DSM05A-T550 --instance-name orcllabdsm01
```

Si vous n’êtes pas sûr du nom de l’instance, exécutez la commande `az baremetalinstance list` décrite ci-dessus.

---
 
## <a name="check-activities-of-a-single-instance"></a>Vérifier les activités d’une instance unique
 
Vous pouvez vérifier les activités d’une seule unité. L’une des principales activités enregistrées est le redémarrage de l’unité. Les données indiquées incluent l’état de l’activité, le timestamp de l’activité déclenchée, l’ID d’abonnement et l’utilisateur Azure qui a déclenché l’activité.
 
:::image type="content" source="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png" alt-text="Capture d’écran montrant les activités d’unité d’instance BareMetal" lightbox="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png":::
 
Les modifications apportées aux métadonnées de l’unité dans Azure sont également enregistrées dans le journal d’activité. En plus du redémarrage lancé, vous pouvez voir l’activité **Write BareMetallnstances**. Cette activité n’apporte aucune modification à l’unité d’instance BareMetal elle-même, mais documente les modifications apportées aux métadonnées de l’unité dans Azure.
 
Une autre activité qui est enregistrée est lorsque vous ajoutez ou supprimez une [étiquette](../../../azure-resource-manager/management/tag-resources.md) à une instance.
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>Ajouter et supprimer une étiquette Azure dans une instance

### <a name="portal"></a>[Portail](#tab/azure-portal)
 
Vous pouvez ajouter des étiquettes Azure à une unité d’instance BareMetal ou les supprimer. La façon dont les étiquettes sont attribuées ne diffère pas de l’attribution d’étiquettes aux machines virtuelles. Comme pour les machines virtuelles, les étiquettes existent dans les métadonnées Azure et, pour les instances BareMetal, ont les mêmes restrictions que les étiquettes pour les machines virtuelles.
 
La suppression d’étiquettes fonctionne de la même façon qu’avec les machines virtuelles. L’application et la suppression d’une étiquette sont répertoriées dans le journal d’activité de l’unité d’instance BareMetal.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

L’affectation de balises à des instances BareMetal fonctionne de la même façon que pour les machines virtuelles. Les balises existent dans les métadonnées Azure et, pour les instances BareMetal, ont les mêmes restrictions que les balises pour les machines virtuelles.

Pour ajouter des balises à une unité d’instance BareMetal, exécutez la commande [az baremetalinstance update](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_update) :

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --set tags.Dept=Finance tags.Status=Normal
```

Utilisez la même commande pour supprimer une balise :

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --remove tags.Dept
```

---

## <a name="check-properties-of-an-instance"></a>Vérifier les propriétés d’une instance
 
Lorsque vous acquérez les instances, vous pouvez accéder à la section Propriétés pour afficher les données collectées sur les instances. Les données collectées incluent la connectivité Azure, le serveur principal de stockage, l’ID de circuit ExpressRoute, l’ID de ressource unique et l’ID d’abonnement. Vous utiliserez ces informations dans les demandes de support ou lors de la configuration d’une capture instantanée de stockage.
 
Vous verrez également un élément d’information critique sur l’adresse IP du stockage NFS. Il isole votre stockage dans votre **locataire** dans la pile d’instances BareMetal. Vous utilisez également cette adresse IP quand vous modifiez le [fichier de configuration pour les sauvegardes de captures instantanées de stockage](../../../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots).
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-properties.png" alt-text="Capture d’écran montrant les paramètres de propriété d’instance BareMetal" lightbox="media/baremetal-infrastructure-portal/baremetal-instance-properties.png":::
 
## <a name="restart-a-unit-through-the-azure-portal"></a>Redémarrage d’une unité par le biais du portail Azure

Il existe plusieurs situations où le système d’exploitation ne termine pas un redémarrage, ce qui nécessite un redémarrage physique de l’unité d’instance BareMetal.

### <a name="portal"></a>[Portail](#tab/azure-portal)

Vous pouvez effectuer un redémarrage physique de l’unité directement à partir du portail Azure :
 
Sélectionnez **Redémarrer**, puis **Oui** pour confirmer le redémarrage de l’unité.
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-restart.png" alt-text="Capture d’écran montrant comment redémarrer l’unité d’instance BareMetal":::
 
Lorsque vous redémarrez une unité d’instance BareMetal, vous remarquerez un délai. Pendant ce laps de temps, l’état d’alimentation passe de **Démarrage en cours** à **Démarré**, ce qui signifie que le système d’exploitation a démarré complètement. Par conséquent, après un redémarrage, vous ne pouvez pas vous connecter à l’unité dès que l’état passe à **Démarré**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour redémarrer une unité d’instance BareMetal, utilisez la commande [az baremetalinstance restart](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_restart) :

```azurecli
az baremetalinstance restart --resource-group DSM05a-T550 --instance-name orcllabdsm01
```

---

>[!IMPORTANT]
>En fonction de la quantité de mémoire présente dans votre unité d’instance BareMetal, un redémarrage du matériel et du système d’exploitation peut prendre jusqu’à une heure.
 
## <a name="open-a-support-request-for-baremetal-instances"></a>Ouvrir une demande de support pour les instances BareMetal
 
Vous pouvez envoyer des demandes de support spécifiquement pour une unité d’instance BareMetal.
1. Dans le portail Azure, sous **Aide + Support**, créez une **[Nouvelle demande de support](https://rc.portal.azure.com/#create/Microsoft.Support)** et fournissez les informations suivantes pour le ticket :
 
   - **Type de problème :** Sélectionner un type de problème
 
   - **Abonnement :** Sélectionnez votre abonnement
 
   - **Service :** BareMetal Infrastructure
 
   - **Ressource :** Fournir le nom de l’instance
 
   - **Résumé :** Fournir un résumé de votre demande
 
   - **Type de problème :** Sélectionner un type de problème
 
   - **Sous-type de problème :** Sélectionner un sous-type pour le problème

1. Sélectionnez l’onglet **Solutions** pour trouver une solution à votre problème. Si vous ne trouvez pas de solution, passez à l’étape suivante.

1. Sélectionnez l’onglet **Détails** et indiquez si le problème est lié à des machines virtuelles ou à des unités d’instance BareMetal. Ces informations permettent d’adresser la demande de support aux spécialistes appropriés.

1. Indiquez à quel moment le problème a commencé et sélectionnez la région de l’instance.

1. Fournissez plus de détails sur la demande et chargez un fichier si nécessaire.

1. Sélectionnez **Vérifier + Créer** pour envoyer la demande.
 
Il faut compter jusqu’à cinq jours ouvrés pour qu’un représentant du support technique confirme votre demande.

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez en savoir plus sur les charges de travail, consultez [Types de charges de travail BareMetal](../../../virtual-machines/workloads/sap/get-started.md).