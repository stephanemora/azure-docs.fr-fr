---
title: Connecter des instances BareMetal Infrastructure dans Azure
description: Découvrez comment identifier et interagir avec des instances BareMetal dans le portail Azure ou Azure CLI.
ms.topic: how-to
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: 8f28579a83c45692e5d1eade2e4632ff8c1c8e42
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108139612"
---
# <a name="connect-baremetal-infrastructure-instances-in-azure"></a>Connecter des instances BareMetal Infrastructure dans Azure

Cet article montre la façon dont le [portail Azure](https://portal.azure.com/) affiche les [instances BareMetal](concepts-baremetal-infrastructure-overview.md). Cet article vous montre également ce que vous pouvez faire dans le portail Azure avec vos instances BareMetal Infrastructure déployées. 
 
## <a name="register-the-resource-provider"></a>Inscrire le fournisseur de ressources
Un fournisseur de ressources Azure pour les instances BareMetal fournit la visibilité des instances dans le portail Azure. Par défaut, l’abonnement Azure que vous utilisez pour les déploiements d’instances BareMetal inscrit le fournisseur de ressources *BareMetalInfrastructure*. Si vous ne voyez pas vos instances BareMetal déployées, vous devez inscrire le fournisseur de ressources auprès de votre abonnement. 

Vous pouvez inscrire le fournisseur de ressources d’instance BareMetal à l’aide du portail Azure ou d’Azure CLI.

### <a name="portal"></a>[Portail](#tab/azure-portal)
 
Vous devrez répertorier vos abonnements dans le portail Azure, puis double-cliquer sur l’abonnement utilisé pour déployer vos instances BareMetal.
 
1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans le menu du portail Azure, sélectionnez **Tous les services**.

1. Dans la zone **Tous les services**, entrez **Abonnement**, puis sélectionnez **Abonnements**.

1. Sélectionnez l’abonnement dans la liste.

1. Sélectionnez **Fournisseurs de ressources** et entrez  **BareMetalInfrastructure** dans la zone de recherche. Le fournisseur de ressources doit être **inscrit**, comme le montre l’image.
 
>[!NOTE]
>Si le fournisseur de ressources n’est pas inscrit, sélectionnez **Inscrire**.
 
:::image type="content" source="media/connect-baremetal-infrastructure/register-resource-provider-azure-portal.png" alt-text="Capture d’écran montrant les instances BareMetal inscrites.":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour commencer avec Azure CLI :

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Connectez-vous à l’abonnement Azure que vous utilisez pour le déploiement d’instances BareMetal via Azure CLI. Inscrivez le fournisseur de ressources `BareMetalInfrastructure` à l’aide de la commande [az provider register](/cli/azure/provider#az_provider_register) :

```azurecli
az provider register --namespace Microsoft.BareMetalInfrastructure
```

Vous pouvez utiliser la commande [az provider list](/cli/azure/provider#az_provider_list) pour afficher tous les fournisseurs disponibles.

---

Pour plus d’informations sur les fournisseurs de ressources, consultez [Fournisseurs et types de ressources Azure](../azure-resource-manager/management/resource-providers-and-types.md).  

## <a name="baremetal-instances-in-the-azure-portal"></a>Instances BareMetal dans le portail Azure
 
Lorsque vous envoyez une demande de déploiement d’instance BareMetal, vous spécifiez l’abonnement Azure que vous connectez aux instances BareMetal. Utilisez le même abonnement que celui utilisé pour déployer la couche d’application qui fonctionne sur les instances BareMetal.
 
Lors du déploiement de vos instances BareMetal, un nouveau [groupe de ressources Azure](../azure-resource-manager/management/manage-resources-portal.md) est créé dans l’abonnement Azure que vous avez utilisé dans la requête de déploiement. Ce nouveau groupe de ressources répertorie toutes les instances BareMetal que vous avez déployées dans cet abonnement.

### <a name="portal"></a>[Portail](#tab/azure-portal)

1. Dans l’abonnement BareMetal, dans le portail Azure, sélectionnez **Groupes de ressources**.
 
   :::image type="content" source="media/connect-baremetal-infrastructure/view-baremetal-instances-azure-portal.png" alt-text="Capture d’écran montrant la liste des groupes de ressources.":::

1. Dans la liste, localisez le nouveau groupe de ressources.
 
   :::image type="content" source="media/connect-baremetal-infrastructure/filter-resource-groups.png" alt-text="Capture d’écran montrant l’instance BareMetal dans une liste de groupes de ressources filtrée." lightbox="media/connect-baremetal-infrastructure/filter-resource-groups.png":::
   
   >[!TIP]
   >Vous pouvez filtrer sur l’abonnement que vous avez utilisé pour déployer l’instance BareMetal. Une fois que vous avez filtré l’abonnement approprié, vous pourriez avoir une longue liste de groupes de ressources. Recherchez celui ayant un suffixe **-Txxx** où « xxx » représente trois chiffres ; par exemple, **-T250**.

1. Sélectionnez le nouveau groupe de ressources pour en afficher les détails. L’image montre une instance BareMetal déployée.
   
   >[!NOTE]
   >Si vous avez déployé plusieurs locataires d’instance BareMetal sous le même abonnement Azure, vous verrez plusieurs groupes de ressources Azure.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour voir toutes vos instances BareMetal, exécutez la commande [az baremetalinstance list](/cli/azure/baremetalinstance#az_baremetalinstance_list) pour votre groupe de ressources :

```azurecli
az baremetalinstance list --resource-group DSM05A-T550 –output table
```

> [!TIP]
> Le paramètre `--output` est un paramètre global, disponible pour toutes les commandes. La valeur **table** présente la sortie dans un format convivial. Pour plus d’informations, consultez [Formats de sortie pour les commandes Azure CLI](/cli/azure/format-output-azure-cli).

---

## <a name="view-the-attributes-of-a-single-instance"></a>Afficher les attributs d’une instance unique

Vous pouvez afficher les détails d’une seule instance.

### <a name="portal"></a>[Portail](#tab/azure-portal)

Dans la liste des instances BareMetal, sélectionnez la seule instance que vous souhaitez afficher.
 
:::image type="content" source="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png" alt-text="Capture d’écran montrant les attributs d’instance BareMetal d’une seule instance." lightbox="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png":::
 
Les attributs de l’image ne sont pas très différents des attributs des machines virtuelles Azure. Sur la gauche, vous pouvez voir le groupe de ressources, la région Azure et le nom et l’ID de l’abonnement. Si vous avez attribué des étiquettes, vous les verrez ici également. Par défaut, les instances BareMetal n’ont pas d’étiquettes assignées.
 
À droite, vous verrez le nom de l’instance BareMetal, le système d’exploitation, l’adresse IP et la référence SKU indiquant la mémoire et le nombre de threads de l’UC. Vous verrez également l’état d’alimentation et la version du matériel (révision du tampon d’instance BareMetal). L’état d’alimentation indique si l’unité matérielle est sous tension ou hors tension. Toutefois, les détails du système d’exploitation n’indiquent pas s’il est en cours d’exécution.
 
Les révisions matérielles possibles sont les suivantes :

* Révision 3 (Rév. 3)

* Révision 4 (Rév. 4)
 
* Révision 4.2 (Rév. 4.2)
 
>[!NOTE]
>Rev 4.2 est la dernière infrastructure BareMetal rebaptisée qui utilise l’architecture Rev 4 existante. Rev 4 assure une plus grande proximité des hôtes de machine virtuelle Azure. Elle apporte des améliorations significatives à la latence du réseau entre les machines virtuelles Azure et les instances SAP HANA. Vous pouvez accéder à vos instances BareMetal et les gérer via le portail Azure. Pour plus d’informations, consultez l’article [Infrastructure BareMetal sur Azure](concepts-baremetal-infrastructure-overview.md).

 
En outre, sur le côté droit, vous trouverez le nom du [groupe de placement de proximité Azure](../virtual-machines/co-location.md), qui est créé automatiquement pour chaque instance BareMetal déployée. Référencez le groupe de placement de proximité lorsque vous déployez les machines virtuelles Azure qui hébergent la couche d’application. Lorsque vous utilisez le groupe de placement de proximité associé à l’instance BareMetal, vous vous assurez que les machines virtuelles Azure sont déployées près de l’instance BareMetal.
 
>[!TIP]
>Pour trouver la couche application dans le même centre de données Azure que Révision 4.x, consultez [Groupes de placement de proximité Azure pour une latence réseau optimale](../virtual-machines/workloads/sap/sap-proximity-placement-scenarios.md).

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour afficher les détails d’une instance BareMetal, exécutez la commande [az baremetalinstance show](/cli/azure/baremetalinstance#az_baremetalinstance_show) :

```azurecli
az baremetalinstance show --resource-group DSM05A-T550 --instance-name orcllabdsm01
```

Si vous n’êtes pas sûr du nom de l’instance, exécutez la commande `az baremetalinstance list` décrite ci-dessus.

---
 
## <a name="check-activities-of-a-single-instance"></a>Vérifier les activités d’une instance unique
 
Vous pouvez vérifier les activités d’une seule instance BareMetal. L’une des principales activités enregistrées est le redémarrage de l’instance. Les données indiquées incluent l’état de l’activité, le timestamp de l’activité déclenchée, l’ID d’abonnement et l’utilisateur Azure qui a déclenché l’activité.
 
:::image type="content" source="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png" alt-text="Capture d’écran montrant les activités de l’instance BareMetal." lightbox="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png":::
 
Les modifications apportées aux métadonnées de l’instance dans Azure sont également enregistrées dans le journal d’activité. En plus du redémarrage lancé, vous pouvez voir l’activité **Write BareMetallnstances**. Cette activité n’apporte aucune modification à l’instance BareMetal elle-même, mais documente les modifications apportées aux métadonnées de l’unité dans Azure.
 
Une autre activité qui est enregistrée est lorsque vous ajoutez ou supprimez une [étiquette](../azure-resource-manager/management/tag-resources.md) à une instance.
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>Ajouter et supprimer une étiquette Azure dans une instance

### <a name="portal"></a>[Portail](#tab/azure-portal)
 
Vous pouvez ajouter des étiquettes Azure à une instance BareMetal ou les supprimer. Les étiquettes sont attribuées de la même façon que lors de l’attribution d’étiquettes aux machines virtuelles. Comme pour les machines virtuelles, les étiquettes existent dans les métadonnées Azure. Les étiquettes ont les mêmes restrictions pour les instances BareMetal que pour les machines virtuelles.
 
La suppression des étiquettes fonctionne également de la même manière que pour les machines virtuelles. L’application et la suppression d’une étiquette sont répertoriées dans le journal d’activité de l’instance BareMetal.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

L’attribution d’étiquettes à des instances BareMetal fonctionne de la même façon que l’attribution d’étiquettes à des machines virtuelles. Comme pour les machines virtuelles, les étiquettes existent dans les métadonnées Azure. Les étiquettes ont les mêmes restrictions pour les instances BareMetal que pour les machines virtuelles.

Pour ajouter des étiquettes à une instance BareMetal, exécutez la commande [az baremetalinstance update](/cli/azure/baremetalinstance#az_baremetalinstance_update) :

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
 
Vous verrez également un élément d’information critique sur l’adresse IP du stockage NFS. Il isole votre stockage dans votre **locataire** dans la pile d’instances BareMetal. Vous utilisez également cette adresse IP quand vous modifiez le [fichier de configuration pour les sauvegardes de captures instantanées de stockage](../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots).
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-properties.png" alt-text="Capture d’écran montrant les paramètres de propriété de l’instance BareMetal." lightbox="media/connect-baremetal-infrastructure/baremetal-instance-properties.png":::
 
## <a name="restart-a-baremetal-instance-through-the-azure-portal"></a>Redémarrer une instance BareMetal via le portail Azure

Il existe plusieurs situations où le système d’exploitation ne termine pas un redémarrage, ce qui nécessite un redémarrage physique de l’instance BareMetal.

### <a name="portal"></a>[Portail](#tab/azure-portal)

Vous pouvez effectuer un redémarrage physique de l’instance directement à partir du portail Azure :
 
Sélectionnez **Redémarrer**, puis **Oui** pour confirmer le redémarrage.
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-restart.png" alt-text="Capture d’écran montrant comment redémarrer l’instance BareMetal.":::
 
Lorsque vous redémarrez une instance BareMetal, vous remarquerez un délai. Pendant ce laps de temps, l’état d’alimentation passe de **Démarrage en cours** à **Démarré**, ce qui signifie que le système d’exploitation a démarré complètement. Par conséquent, après un redémarrage, vous pouvez vous connecter à l’unité uniquement lorsque l’état passe à **Démarré**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour redémarrer une instance BareMetal, utilisez la commande [az baremetalinstance restart](/cli/azure/baremetalinstance#az_baremetalinstance_restart) :

```azurecli
az baremetalinstance restart --resource-group DSM05a-T550 --instance-name orcllabdsm01
```

---

>[!IMPORTANT]
>En fonction de la quantité de mémoire présente dans votre instance BareMetal, un redémarrage du matériel et du système d’exploitation peut prendre jusqu’à une heure.
 
## <a name="open-a-support-request-for-baremetal-instances"></a>Ouvrir une demande de support pour les instances BareMetal
 
Vous pouvez envoyer des demandes de support spécifiquement pour les instances BareMetal.
1. Dans le portail Azure, sous **Aide + Support**, créez une **[Nouvelle demande de support](https://rc.portal.azure.com/#create/Microsoft.Support)** et fournissez les informations suivantes pour le ticket :
 
   - **Type de problème :** Sélectionnez un type de problème.
 
   - **Abonnement :** Sélectionnez votre abonnement.
 
   - **Service :** BareMetal Infrastructure
 
   - **Ressource :** Indiquez le nom de l’instance.
 
   - **Résumé :** Fournissez un résumé de votre demande.
 
   - **Type de problème :** Sélectionnez un type de problème.
 
   - **Sous-type de problème :** Sélectionnez un sous-type pour le problème.

1. Sélectionnez l’onglet **Solutions** pour trouver une solution à votre problème. Si vous ne trouvez pas de solution, passez à l’étape suivante.

1. Sélectionnez l’onglet **Détails** et indiquez si le problème est lié à des machines virtuelles ou à des instances BareMetal. Ces informations permettent d’adresser la demande de support aux spécialistes appropriés.

1. Indiquez à quel moment le problème a commencé et sélectionnez la région de l’instance.

1. Fournissez plus de détails sur la demande et chargez un fichier si nécessaire.

1. Sélectionnez **Vérifier + Créer** pour envoyer la demande.
 
Il faut compter jusqu’à cinq jours ouvrés pour qu’un représentant du support technique confirme votre demande.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les charges de travail :

- [Qu’est-ce que SAP HANA sur Azure (grandes instances) ?](../virtual-machines/workloads/sap/hana-overview-architecture.md)