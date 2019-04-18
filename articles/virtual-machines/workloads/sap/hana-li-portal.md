---
title: Contrôler les grandes Instances HANA Azure via le portail Azure | Microsoft Docs
description: Décrit la façon dont vous pouvez identifier et interagir avec les grandes Instances Azure HANA via le portail
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/02/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8240308b3e0955b1d4d3ef2e82cad215daf95b00
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59009366"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Contrôle des grandes instances Azure HANA à l’aide du portail Azure
Ce document décrit la façon dont [grandes Instances HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) sont présentés dans [Azure portal](https://portal.azure.com) et quelles activités peuvent être effectuées via le portail Azure avec des unités de grande Instance HANA qui sont déployées automatiquement. Visibilité des grandes Instances HANA dans le portail Azure est fournie via un fournisseur de ressources Azure pour les grandes Instances HANA, qui est actuellement en version préliminaire publique

## <a name="register-hana-large-instance-resource-provider"></a>Inscrire le fournisseur de ressources de grande Instance HANA
Généralement, votre abonnement Azure que vous utilisiez pour les déploiements de grande Instance HANA est inscrit pour le fournisseur de ressources de l’Instance de grande taille HANA. Toutefois, si vous ne voyez pas déployé d’unités de grande Instance HANA, vous devez inscrire le fournisseur de ressources dans votre abonnement Azure. Il existe deux façons dans l’inscription du fournisseur de ressources d’Instance grande taille HANA

### <a name="register-through-cli-interface"></a>S’inscrire via l’interface CLI
Vous devez être connecté à votre abonnement Azure utilisé pour le déploiement de grande Instance HANA via l’interface Azure CLI. (Re-) inscrire le fournisseur d’Instance HANA volumineux avec cette commande, vous pouvez :
    
    az provider register --namespace Microsoft.HanaOnAzure

Pour plus d’informations, consultez l’article [les types et les fournisseurs de ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli)


### <a name="register-through-azure-portal"></a>S’inscrire via le portail Azure
Vous pouvez (re-) inscrire le fournisseur de ressources HANA grande Instance via le portail Azure. Vous devez répertorier votre abonnement dans le portail Azure, puis double-cliquez sur l’abonnement, ce qui a été utilisé pour déployer votre unité (s) de grande Instance HANA. Celle qui vous dans la page Vue d’ensemble de votre abonnement, sélectionnez « Fournisseurs de ressources » comme indiqué ci-dessous et que vous tapez « HANA » dans la fenêtre de recherche. 

![Inscrire HLI RP via le portail Azure](./media/hana-li-portal/portal-register-hli-rp.png)

Dans la capture d’écran, le fournisseur de ressources a déjà été enregistré. Dans le cas où le fournisseur de ressources n’est pas encore inscrit, appuyez sur « Re-register » ou « register ».

Pour plus d’informations, consultez l’article [les types et les fournisseurs de ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell)


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Affichage des unités de grande Instance HANA dans le portail Azure
Lorsque vous soumettez une demande de déploiement de grande Instance HANA, vous êtes invité à spécifier l’abonnement Azure que vous vous connectez à de grandes Instances HANA également. Il est recommandé, pour utiliser le même abonnement que vous utilisez pour déployer la couche application SAP qui fonctionne sur les unités de grande Instance HANA.
Comme votre première grandes Instances HANA sont déployés, un nouveau [groupe de ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) est créé dans l’abonnement Azure que vous avez envoyée dans la demande de déploiement pour votre ou les grandes instances HANA.  Nouveau groupe de ressources répertorie tous vos unités de grande Instance HANA que vous avez déployé dans l’abonnement spécifique.

Afin de trouver le groupe de ressources Azure, vous répertoriez le groupe de ressources dans votre abonnement en accédant via le volet de navigation gauche du portail Azure

![Volet de navigation dans le portail Azure](./media/hana-li-portal/portal-resource-group.png)

Dans la liste des groupes de ressources, vous êtes bien répertorié, vous devrez peut-être filtrer sur l’abonnement que vous avez utilisé pour avoir des grandes Instances HANA déployées

![Filtrer les groupes de ressources dans le portail Azure](./media/hana-li-portal/portal-filtering-subscription.png)

Après le filtrage à l’abonnement approprié, vous pouvez avez toujours une longue liste de groupes de ressources. Recherchez l’une avec un post-correctif de **- Txxx** où « xxx » est les trois chiffres, comme **-T050**. 

En tant que vous consultez le groupe de ressources, affiche les détails de celui-ci. La liste que vous avez reçu peut ressembler à :

![Liste HLI dans le portail Azure](./media/hana-li-portal/portal-hli-units-list.png)

Toutes les unités répertoriées représentent une seule unité de grande Instance HANA qui a été déployée dans votre abonnement. Dans ce cas, vous examinez les huit unités de grande Instance HANA différentes, qui ont été déployées dans votre abonnement.

Si vous avez déployé plusieurs clients de grande Instance HANA dans le même abonnement Azure, vous trouverez plusieurs groupes de ressources Azure 


## <a name="look-at-attributes-of-single-hli-unit"></a>Examinez les attributs d’unité HLI unique
Dans la liste des unités de grande Instance HANA, vous pouvez cliquer sur une seule unité et obtenir les détails de l’unité de grande Instance HANA unique. 

Dans l’écran vue d’ensemble, vous obtenez une présentation de l’unité, ce qui ressemble à :

![Afficher la vue d’ensemble d’une unité HLI](./media/hana-li-portal/portal-show-overview.png)

Ces attributs en examinant les différents attributs affichés, recherchez tout à fait différents de celui des attributs de la machine virtuelle Azure. Sur l’en-tête de la partie gauche, il affiche le groupe de ressources, une région Azure, nom de l’abonnement et ID ainsi que certaines balises que vous avez ajouté. Par défaut, les unités de grande Instance HANA n’ont aucun balise. Sur le côté droit de l’en-tête, le nom de l’unité est répertorié comme affecté lorsque le déploiement a été effectué. Le système d’exploitation est indiqué, ainsi que l’adresse IP. Comme avec les machines virtuelles tapez de l’unité HANA grande instance avec le nombre d’UC threads et la mémoire est également affichée. Plus d’informations sur les différentes unités de grande Instance HANA sont indiquées ici :

- [Références (SKU) disponibles pour HLI](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [Architecture de stockage SAP HANA (grandes Instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

Un champ supplémentaire dans la colonne de droite de l’en-tête informe de l’état d’alimentation de l’unité HANA grande instance.

> [!NOTE]
> L’état d’alimentation décrit si l’unité matérielle est sous tension ou hors tension. Il ne donne pas d’informations sur le système d’exploitation en cours haut et en cours d’exécution. Que vous redémarrez une unité de grande Instance HANA, vous rencontrerez un petit laps de temps où l’état de l’unité devient **démarrage** à déplacer dans l’état de **démarré**. L’état de **démarré** signifie que le système d’exploitation démarrage ou que le système d’exploitation a été complètement démarré. Par conséquent, après un redémarrage de l’unité, vous ne pouvez pas attendre pour vous connecter immédiatement à l’unité dès que l’état bascule vers **démarré**.
> 


## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Vérifier les activités d’une seule unité de grande Instance HANA 
Au-delà de ce qui donne une vue d’ensemble des unités de grande Instance HANA, vous pouvez vérifier les activités de l’unité particulière. Un journal d’activité peut ressembler à :

![Volet de navigation dans le portail Azure](./media/hana-li-portal/portal-activity-list.png)

Une des principales activités enregistrées sont le redémarrage d’une unité. Les données répertoriées incluent l’état de l’activité de l’horodatage de l’activité a été déclenchée, l’ID d’abonnement de l’activité a été déclenchée et l’utilisateur Azure qui a déclenché l’activité. 

Une autre activité qui est bien enregistrée sont des modifications de l’unité dans les métadonnées Azure. Outre le redémarrage lancé, vous pouvez voir l’activité de **HANAInstances écrire**. Ce type d’activité n’effectue aucune modification sur l’unité de grande Instance HANA lui-même, mais est documenter les modifications apportées aux métadonnées de l’unité dans Azure. Dans le cas répertorié, nous avons ajouté et supprimé une balise (voir la section suivante).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Ajouter et supprimer une balise Azure à une unité de grande Instance HANA
Une autre possibilité, vous avez consiste à ajouter un [balise](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) à une unité de grande Instance HANA. La façon dont les balises sont bien affectées ne diffère pas d’affectation d’étiquettes aux machines virtuelles. Comme avec les balises existent dans les métadonnées Azure et, pour les grandes Instances HANA, les machines virtuelles ont les mêmes restrictions sous forme de balises pour les machines virtuelles.

La suppression d’étiquettes fonctionne de la même façon avec les machines virtuelles. Les deux activités, l’application et la suppression d’une balise seront afficheront dans le journal d’activité de l’unité de grande Instance HANA particulier.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>Vérifiez les propriétés d’une unité de grande Instance HANA
La section **propriétés** inclut des informations importantes que vous obtenez lorsque les instances sont remis à vous. C’est une section dans laquelle vous obtenez toutes les informations que vous pourriez avoir besoin dans la prise en charge cas ou dont vous avez besoin lorsque vous configurez la configuration de capture instantanée de stockage. Par conséquent, cette section est une collection de données autour de votre instance, la connectivité de l’instance à Azure et le serveur principal de stockage. En haut de la section ressemble à :


![partie supérieure de propriétés HLI dans le portail Azure](./media/hana-li-portal/portal-properties-top.png)

Les premiers éléments quelques données, vous avez vu dans l’écran de présentation déjà. Mais une partie importante de données est l’ID du Circuit ExpressRoute, que vous avez obtenu comme les premières unités déployées ont été remises. Dans certains cas de prise en charge, vous pouvez obtenir l’invité pour ces données. Une entrée de données importantes est affichée en bas de la capture d’écran. Les données affichées sont l’adresse IP de la tête de stockage NFS qui isole votre stockage pour votre **locataire** dans la pile de grande Instance HANA. Cette adresse IP est également nécessaire lorsque vous modifiez le [fichier de configuration pour le stockage de sauvegardes de captures instantanées](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots). 

Lorsque vous faites défiler vers le bas dans le volet des propriétés, vous obtenez des données supplémentaires, comme un ID de ressource unique pour votre unité de grande Instance HANA ou l’ID d’abonnement qui a été affectée au déploiement.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Redémarrer une unité de grande Instance HANA via le portail Azure
Lancement d’un redémarrage du système d’exploitation Linux, il y avait des diverses situations où le système d’exploitation n’a pas pu redémarrer correctement. Pour forcer un redémarrage, vous deviez ouvrir une demande de service d’avoir des opérations de Microsoft à effectuer un redémarrage de l’alimentation de l’unité de grande Instance HANA. Les fonctionnalités d’un redémarrage de la puissance d’une unité de grande Instance HANA sont maintenant intégrée dans le portail Azure. Vous êtes dans la partie de la vue d’ensemble de l’unité de grande Instance HANA, vous voyez le bouton de redémarrage en haut de la section de données

![Redémarrez l’étape #1 dans le portail Azure](./media/hana-li-portal/portal-restart-first-step.png)

Comme vous appuyer sur le bouton de redémarrage, vous êtes invité si vous souhaitez vraiment redémarrer l’unité. Comme vous avez confirmé en appuyant sur le bouton « Oui », l’unité redémarre.

> [!NOTE]
> Dans le processus de redémarrage, vous rencontrerez un petit laps de temps où l’état de l’unité devient **démarrage** à déplacer dans l’état de **démarré**. L’état de **démarré** signifie que le système d’exploitation démarrage ou que le système d’exploitation a été complètement démarré. Par conséquent, après un redémarrage de l’unité, vous ne pouvez pas attendre pour vous connecter immédiatement à l’unité dès que l’état bascule vers **démarré**.

> [!IMPORTANT]
> Dépend de la quantité de mémoire dans votre unité de grande Instance HANA, un redémarrage et le redémarrage du matériel et le système d’exploitation peuvent prendre jusqu'à une heure


## <a name="open-a-support-request-for-hana-large-instances"></a>Ouvrez une demande de support pour les grandes Instances HANA
En dehors de l’affichage du portail Azure d’unités de grande Instance HANA, vous pouvez créer des demandes de support spécifiquement pour une HANA unité de grande Instance ainsi. Que vous suivez le lien **nouvelle demande de support** 

![lancer l’étape de requête de service #1 dans le portail Azure](./media/hana-li-portal/portal-initiate-support-request.png)

Afin d’obtenir le service des grandes Instances SAP HANA répertoriés dans l’écran suivant, vous devrez peut-être sélectionner « tous les Services » comme indiqué ci-dessous

![Sélectionnez tous les services dans le portail Azure](./media/hana-li-portal/portal-create-service-request.png)

Dans la liste des services, vous pouvez trouver le service **grande Instance HANA**. Lorsque vous choisissez ce service, vous pouvez sélectionner les types de problèmes spécifiques comme indiqué :


![Sélectionnez la classe de problème dans le portail Azure](./media/hana-li-portal/portal-select-problem-class.png)

Sous chacun des types de problème différent, vous avez une sélection des sous-types de problème que vous devez sélectionner pour caractériser votre problème. Après avoir sélectionné le sous-type, vous pouvez désormais nommer l’objet. Une fois que vous avez terminé avec le processus de sélection, vous pouvez déplacer à l’étape suivante de la création. Dans le **Solutions** section, vous êtes pointe vers la documentation autour de grandes Instances HANA, ce qui peut donner un pointeur à une solution de votre problème. Si vous ne pouvez pas trouver une solution à votre problème dans la documentation suggérée, vous accédez à l’étape suivante. Dans l’étape suivante, vous allez être invité si le problème est avec les machines virtuelles ou des unités de grande Instance HANA. Ces informations permettent de diriger la demande de support pour les spécialistes en corrects. 

![Détails des cas de prise en charge dans le portail Azure](./media/hana-li-portal/portal-support-request-details.png)

Lorsque vous répondu aux questions et fourni des détails supplémentaires, vous pouvez passer l’étape suivante pour passer en revue la demande de support et de l’envoi il.

## <a name="next-steps"></a>Étapes suivantes

- [La surveillance de SAP HANA (grandes instances) sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [Surveillance et dépannage à partir de HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)

