---
title: Résoudre des problèmes d’intégrité de l’invité VM Insights (préversion)
description: Décrit les étapes de dépannage que vous pouvez suivre lorsque vous rencontrez des problèmes d’intégrité sur VM Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: d8b37569ebaa8e75be601a1efd65a23a61aeaa75
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051937"
---
# <a name="troubleshoot-vm-insights-guest-health-preview"></a>Résoudre des problèmes d’intégrité de l’invité VM Insights (préversion)
Cet article décrit les étapes de dépannage que vous pouvez suivre lorsque vous rencontrez des problèmes d’intégrité sur VM Insights.

## <a name="error-message-that-no-data-is-available"></a>Message d’erreur indiquant qu’aucune donnée n’est disponible 

![Pas de données](media/vminsights-health-troubleshoot/no-data.png)


### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>Vérifier que la machine virtuelle respecte la configuration requise

1. Vérifiez que la machine virtuelle est une machine virtuelle Azure. Azure Arc pour serveurs n’est pas pris en charge actuellement.
2. Vérifiez que la machine virtuelle exécute un [système d’exploitation pris en charge](vminsights-health-enable.md?current-limitations.md).
3. Vérifiez que la machine virtuelle est installée dans une [région prise en charge](vminsights-health-enable.md?current-limitations.md).
4. Vérifiez que l’espace de travail Log Analytics est installé dans une [région prise en charge](vminsights-health-enable.md?current-limitations.md).

### <a name="verify-that-the-vm-is-properly-onboarded"></a>Vérifier que la machine virtuelle est correctement intégrée
Vérifiez que l’extension de l’agent Azure Monitor et l’agent Intégrité de la machine virtuelle invitée sont configurés sur la machine virtuelle. Sélectionnez **Extensions** dans le menu de la machine virtuelle dans le Portail Azure et assurez-vous que les deux agents sont répertoriés.

![Extensions de machine virtuelle](media/vminsights-health-troubleshoot/extensions.png)

### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>Vérifier que l’identité affectée par le système est activée sur la machine virtuelle
Vérifiez que l’identité affectée par le système est activée sur la machine virtuelle. Sélectionnez **Identité** dans le menu de la machine virtuelle sur le portail Azure. Si l’identité managée par l’utilisateur est activée, quel que soit l’état de l’identité gérée par le système, l’agent Azure Monitor ne pourra pas communiquer avec le service de configuration, et l’extension d’intégrité de l’invité ne fonctionnera pas.

![Identité attribuée au système](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>Vérifier la règle de collecte des données
Vérifiez que la règle de collecte de données spécifiant l’extension d’intégrité comme source de données est associée à la machine virtuelle.

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>Message d’erreur pour une demande incorrecte en raison d’autorisations insuffisantes
Cette erreur indique que le fournisseur de ressources **Microsoft.WorkloadMonitor** n’a pas été inscrit dans l’abonnement. Pour plus d’informations sur l’inscription de ce fournisseur de ressources, consultez [Fournisseurs et types de ressources Azure](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider). 

![Demande incorrecte](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="next-steps"></a>Étapes suivantes

- [Découvrir une vue d’ensemble de la fonctionnalité d’intégrité des invités de VM Insights](vminsights-health-overview.md)