---
title: Paramètres de connectivité d’Azure Synapse
description: Un article qui vous montre comment configurer les paramètres de connectivité dans Azure Synapse Analytics
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 08/05/2021
author: ashinMSFT
ms.author: seshin
ms.reviewer: jrasnick, wiassaf
ms.openlocfilehash: 58086bcbe321b7d50d6f5d0e41a9723dfbe512ed
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121861628"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Paramètres de connectivité d’Azure Synapse Analytics

Cet article détaille les paramètres de connectivité dans Azure Synapse Analytics et comment les configurer le cas échéant.

## <a name="public-network-access"></a>Accès au réseau public 

Vous pouvez utiliser la fonctionnalité d’accès au réseau public pour autoriser la connectivité du réseau public entrant à votre espace de travail Azure Synapse. 

- Lorsque l’accès au réseau public est **désactivé**, vous pouvez vous connecter à votre espace de travail uniquement à l’aide des [points de terminaison privés](synapse-workspace-managed-private-endpoints.md). 
- Lorsque l’accès au réseau public est **activé**, vous pouvez vous connecter à votre espace de travail également depuis des réseaux publics. Vous pouvez gérer cette fonctionnalité pendant et après la création de votre espace de travail. 

> [!IMPORTANT]
> Cette fonctionnalité est disponible uniquement pour les espaces de travail Azure Synapse associés au [réseau virtuel managé d’Azure Synapse Analytics](synapse-workspace-managed-vnet.md). Toutefois, vous pouvez toujours ouvrir vos espaces de travail Synapse sur le réseau public, quelle que soit son association avec le réseau virtuel managé. 

La sélection de l’option **Désactiver** n’applique pas les règles de pare-feu que vous pouvez configurer. En outre, les règles de votre pare-feu apparaissent grisées dans le paramètre Réseau du portail Synapse. Vos configurations de pare-feu seront réappliquées lorsque vous réactiverez l’accès au réseau public. 

> [!TIP]
> Lorsque vous rétablissez l’activation, attendez un certain temps avant de modifier les règles de pare-feu.

### <a name="configure-public-network-access-when-you-create-your-workspace"></a>Configurer l’accès au réseau public lorsque vous créez votre espace de travail

1.    Sélectionnez l’onglet **Mise en réseau** lorsque vous créez votre espace de travail dans le [Portail Azure](https://aka.ms/azureportal).
2.    Sous Réseau virtuel managé, sélectionnez **Activer** pour associer votre espace de travail au réseau virtuel managé et autoriser l’accès au réseau public. 

       :::image type="content" source="./media/connectivity-settings/create-synapse-workspace-managed-virtual-network-1.png" alt-text="Créer un espace de travail Synapse, onglet Mise en réseau, paramètre Réseau virtuel managé" lightbox="media/connectivity-settings/create-synapse-workspace-managed-virtual-network-1.png":::

3. Sous **accès au réseau public**, sélectionnez **Désactiver** pour refuser l’accès public à votre espace de travail. Sélectionnez **Activer** si vous souhaitez autoriser l’accès public à votre espace de travail.

   :::image type="content" source="./media/connectivity-settings/create-synapse-workspace-public-network-access-2.png" alt-text="Créer un espace de travail Synapse, onglet Mise en réseau, paramètre Accès au réseau public" lightbox="media/connectivity-settings/create-synapse-workspace-public-network-access-2.png"::: 

4.    Complétez le reste du flux de création de l’espace de travail.

### <a name="configure-public-network-access-after-you-create-your-workspace"></a>Configurer l’accès au réseau public après avoir créé votre espace de travail

1.    Sélectionnez votre espace de travail Synapse dans le [Portail Azure](https://aka.ms/azureportal).
2.    Sélectionnez **Mise en réseau** dans le volet de navigation de gauche.
3.    Sélectionnez **Désactivé** pour refuser l’accès public à votre espace de travail. Sélectionnez **Activé** si vous souhaitez autoriser l’accès public à votre espace de travail.

       :::image type="content" source="./media/connectivity-settings/synapse-workspace-networking-public-network-access-3.png" alt-text="Dans un espace de travail Synapse existant, onglet Mise en réseau, paramètre Accès au réseau public est activé" lightbox="media/connectivity-settings/synapse-workspace-networking-public-network-access-3.png"::: 

4.    Lorsqu’elles sont désactivées, les **règles de pare-feu** sont grisées pour indiquer que les règles de pare-feu ne sont pas en vigueur. Les configurations de règles de pare-feu sont conservées. 

       :::image type="content" source="./media/connectivity-settings/synapse-workspace-networking-firewall-rules-4.png" alt-text="Dans un espace de travail Synapse existant, onglet Mise en réseau, paramètre Accès au réseau public est désactivé, en rapport aux règles de pare-feu" lightbox="media/connectivity-settings/synapse-workspace-networking-firewall-rules-4.png"::: 
 
5.    Sélectionnez **Enregistrer** pour enregistrer la modification. Une notification confirme que le paramètre réseau a été correctement enregistré.

## <a name="connection-policy"></a>Stratégie de connexion
La stratégie de connexion pour Synapse SQL dans Azure Synapse Analytics est définie sur *Par défaut*. Vous ne pouvez pas la modifier dans Azure Synapse Analytics. Vous pouvez en apprendre plus sur la façon dont cela affecte les connexions à Synapse SQL dans Azure Synapse Analytics [ici](../../azure-sql/database/connectivity-architecture.md#connection-policy). 

## <a name="minimal-tls-version"></a>Version TLS minimale
Synapse SQL dans Azure Synapse Analytics autorise les connexions avec toutes les versions TLS. Vous ne pouvez pas définir la version TLS minimale pour Synapse SQL dans Azure Synapse Analytics.

## <a name="next-steps"></a>Étapes suivantes

 - Créer un [espace de travail Azure Synapse](./synapse-workspace-ip-firewall.md).