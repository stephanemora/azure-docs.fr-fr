---
title: Créer une topologie de réseau de maillage avec Azure Virtual Network Manager (préversion)
description: Découvrez comment créer une topologie de réseau de maillage avec Azure Virtual Network Manager.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: fea577a1aa3d2a26d218a7d7c97140d5f58661f2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096171"
---
# <a name="create-a-mesh-network-topology-with-azure-virtual-network-manager-preview"></a>Créer une topologie de réseau de maillage avec Azure Virtual Network Manager (préversion)

Dans cet article, vous apprendrez comment créer une topologie de réseau de maillage avec Azure Virtual Network Manager. Avec cette configuration, tous les réseaux virtuels présent dans la même région du même groupe réseau peuvent communiquer les uns avec les autres. Vous pouvez activer la connectivité inter-régions en activant le paramètre de maillage global dans la configuration de la connectivité.

> [!IMPORTANT]
> *Azure Virtual Network Manager* est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [**Conditions supplémentaires relatives à l’utilisation des préversions de Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* En savoir plus sur la topologie du réseau de [maillage](concept-connectivity-configuration.md#mesh-network-topology).
* Créé une [instance Azure Virtual Network Manager](create-virtual-network-manager-portal.md#create-virtual-network-manager).
* Identifiez les réseaux virtuels que vous souhaitez utiliser dans la configuration de maillage ou créez de nouveaux [réseaux virtuels](../virtual-network/quick-create-portal.md).

## <a name="create-a-network-group"></a>Créer un groupe réseau

Cette section vous aidera à créer un groupe réseau contenant les réseaux virtuels que vous utiliserez pour la topologie de réseau hub-and-spoke.

1. Accéder à votre instance Azure Virtual Network Manager. Ce guide pratique suppose que vous avez déjà créé une instance à l’aide du Guide de [démarrage rapide](create-virtual-network-manager-portal.md).

1. Sélectionnez **Connexions** sous *Paramètres*, puis sélectionnez **Ajouter** pour créer une nouvelle connexion.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-network-group.png" alt-text="Capture d’écran du bouton Ajouter un groupe réseau.":::

1. Sous l’onglet *De base*, entrez un **Nom** et une **Description** pour le groupe réseau.

    :::image type="content" source="./media/how-to-create-hub-and-spoke/basics.png" alt-text="Capture d’écran de l’onglet De base pour l’ajout d’un groupe réseau.":::

1. Pour ajouter un réseau virtuel manuellement, sélectionnez l’onglet **Membres du groupe statique**. Pour plus d’informations, consultez [Membres statiques](concept-network-groups.md#static-membership).

    :::image type="content" source="./media/how-to-create-hub-and-spoke/static-group.png" alt-text="Capture d’écran de l’onglet Membres du groupe statique.":::

1. Pour ajouter des réseaux virtuels de manière dynamique, sélectionnez l’onglet **Instructions conditionnelles**. Pour plus d’informations, consultez [Appartenance dynamique](concept-network-groups.md#dynamic-membership).

    :::image type="content" source="./media/how-to-create-hub-and-spoke/conditional-statements.png" alt-text="Capture d’écran de l’onglet Instructions conditionnelles.":::

1. Une fois que vous êtes satisfait des réseaux virtuels choisis pour le groupe réseau, sélectionnez **Examiner + créer**. Sélectionnez **Créer** après avoir passé la validation.

## <a name="create-a-mesh-connectivity-configuration"></a>Créer une configuration de connectivité de maillage

Cette section vous guide tout au long de la création d’une configuration de maillage avec le groupe réseau que vous avez créé dans la section précédente.

1. Sous *Paramètres*, sélectionnez **Configuration**, puis choisissez **+ Ajouter une configuration**.

    :::image type="content" source="./media/how-to-create-hub-and-spoke/configuration-list.png" alt-text="Capture d’écran de la liste des configurations.":::

1. Dans le menu déroulant, sélectionnez **Connectivité**.

    :::image type="content" source="./media/create-virtual-network-manager-portal/configuration-menu.png" alt-text="Capture d’écran du menu déroulant de configuration.":::

1. Sur la page *Ajouter une configuration de connectivité*, entrez ou sélectionnez les informations suivantes :

    :::image type="content" source="./media/how-to-create-mesh-network/connectivity-configuration.png" alt-text="Capture d’écran de la page Ajouter une configuration de connectivité.":::

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez un *nom* pour cette configuration. |
    | Description | *Facultatif* Entrez une description de ce que fait cette configuration de connectivité. |
    | Topologie | Sélectionnez la topologie de **maillage**. |
    | Maillage global | Sélectionnez cette option pour activer la connectivité inter-régions entre les réseaux virtuels dans le même groupe réseau. |

1. Ensuite, sélectionnez **+ Ajouter des groupes réseau**. 

1. Dans la page *Ajouter des groupes réseau*, sélectionnez les groupes réseau que vous souhaitez ajouter à cette configuration. Sélectionnez ensuite **Ajouter** pour sauvegarder.

1. Puis sélectionnez **Ajouter** à nouveau pour créer la configuration de connectivité.

## <a name="deploy-the-mesh-configuration"></a>Déployer la configuration de maillage

Pour que cette configuration soit effective dans votre environnement, vous devez déployer la configuration dans les régions où vos réseaux virtuels sélectionnés sont créés.

1. Sélectionnez **Déploiement** sous *Paramètres*, puis cliquez sur **Déployer une configuration**.

1. Dans la section *Déployer une configuration*, sélectionnez les paramètres suivants :

    :::image type="content" source="./media/how-to-create-hub-and-spoke/deploy.png" alt-text="Capture d’écran de la page Déployer une configuration.":::

    | Paramètre | Value |
    | ------- | ----- |
    | Type de configuration | Sélectionnez **Connectivité**. |
    | Configurations | Sélectionnez le nom de la configuration que vous avez créée dans la section précédente. |
    | Régions cibles | Sélectionnez toutes les régions qui s’appliquent aux réseaux virtuels que vous sélectionnez pour la configuration. |

1. Sélectionnez **Déployer**, puis cliquez sur **OK** pour valider la configuration dans les régions sélectionnées.

1. Le déploiement de la configuration peut prendre jusqu’à 15-20 minutes, sélectionner le bouton **Actualiser** pour vérifier l’état du déploiement.

## <a name="confirm-deployment"></a>Confirmer le déploiement

1. Consultez [Afficher les configurations appliquées](how-to-view-applied-configurations.md).

1. Pour tester la connectivité entre les réseaux virtuels, déployez une machine virtuelle de test dans chaque réseau virtuel et démarrez une requête ICMP entre elles.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [Règles de sécurité administrateur](concept-security-admins.md)
- Découvrez comment bloquer le trafic réseau avec une [Configuration de l’administration de la sécurité](how-to-block-network-traffic-portal.md).
