---
title: Résoudre les erreurs courantes liées à l’intégration d’Azure Automanage
description: Erreurs courantes d’intégration à Automanage et résolution
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alsin
ms.openlocfilehash: 6ee0164dd8243d30cf691350352757f2503e34c8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862972"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>Résoudre les erreurs courantes liées à l’intégration d’Automanage
Automanage peut échouer à intégrer un ordinateur sur le service. Ce document explique comment résoudre les échecs de déploiement, partage quelques raisons courantes pour lesquelles les déploiements peuvent échouer et décrit les étapes suivantes possibles d’atténuation.

## <a name="troubleshooting-deployment-failures"></a>Résoudre les problèmes de déploiement
L’intégration d’une machine à Automanage entraînera la création d’un déploiement d’Azure Resource Manager. Vérifiez le déploiement pour plus d’informations sur la raison de l’échec. Vous trouverez des liens vers les déploiements dans le menu volant détaillant les échecs, illustré ci-dessous.

:::image type="content" source="media\common-errors\failure-flyout.png" alt-text="Menu volant détaillant les échecs d’Automanage.":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-vm"></a>Vérifier les déploiements pour le groupe de ressources contenant la machine virtuelle ayant échoué
Le menu volant d’échec contiendra un lien vers les déploiements dans le groupe de ressources contenant la machine dont l’intégration a échoué. Le menu volant contient également un nom de préfixe que vous pouvez utiliser pour filtrer les déploiements. Cliquez sur le lien de déploiement pour accéder au panneau des déploiements, où vous pouvez ensuite filtrer les déploiements pour voir les déploiements Automanage sur votre ordinateur. Si vous effectuez un déploiement dans plusieurs régions, veillez à cliquer sur le déploiement dans la bonne région.

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-vm"></a>Vérifier les déploiements pour l’abonnement contenant la machine virtuelle ayant échoué
Si vous ne constatez aucun échec dans le déploiement du groupe de ressources, l’étape suivante consiste à examiner les déploiements de votre abonnement contenant la machine virtuelle dont l’intégration a échoué. Cliquez sur le lien **Déploiements pour l’abonnement** dans le menu volant des échecs et filtrez les déploiements à l’aide du filtre **Automanage-DefaultResourceGroup**. Utilisez le nom du groupe de ressources du panneau des échecs pour filtrer les déploiements. Le nom du déploiement sera suivi d’un nom de région. Si vous effectuez un déploiement dans plusieurs régions, veillez à cliquer sur le déploiement dans la bonne région.

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Vérifier les déploiements dans un abonnement lié à un espace de travail Log Analytics
Si vous ne voyez aucun échec de déploiement dans le groupe de ressources ou l’abonnement contenant votre machine virtuelle ayant échoué, et si votre machine virtuelle ayant échoué est connectée à un espace de travail Log Analytics dans un autre abonnement, accédez à l’abonnement lié à votre espace de travail Log Analytics et vérifiez les échecs de déploiement.

## <a name="common-deployment-errors"></a>Erreurs de déploiement courantes

Error |  Limitation des risques
:-----|:-------------|
Erreur d’autorisations insuffisantes pour le compte Automanage | Cette erreur peut se produire si vous avez récemment déplacé un abonnement contenant un nouveau compte Automanage dans un nouveau locataire. Les étapes de résolution de cette erreur sont accessibles [ici](./repair-automanage-account.md).
La région de l’espace de travail ne correspond pas aux spécifications du mappage des régions | Automanage n’a pas pu intégrer votre machine, car l’espace de travail Log Analytics auquel la machine est actuellement liée n’est pas mappé à une région Automation prise en charge. Assurez-vous que votre compte Automation et votre espace de travail Log Analytics existants se trouvent dans un [mappage de régions pris en charge](../automation/how-to/region-mappings.md).
« Access denied because of the deny assignment with name 'System deny assignment created by managed application' » (« Accès refusé en raison de l’affectation de refus avec le nom 'Affectation de refus du système créée par l’application managée.' ») | Un [denyAssignment](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments) a été créé sur votre ressource, ce qui empêche l’accès à votre ressource par Automanage. Ce denyAssignment peut être dû à un [blueprint](https://docs.microsoft.com/azure/governance/blueprints/concepts/resource-locking) ou à une [application managée](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/overview).
"OS Information: Name='(null)', ver='(null)', agent status='Not Ready'." | Assurez-vous que vous exécutez une [version minimale de l’agent prise en charge](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version), que l’agent est en cours d’exécution ([Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) et [Windows](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)) et que l’agent est à jour ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) et [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)).
« Impossible de déterminer le système d’exploitation pour la machine virtuelle. Nom du système d’exploitation :, ver. Vérifiez que l’agent de machine virtuelle est en cours d’exécution et que l’état actuel est Prêt. » | Assurez-vous que vous exécutez une [version minimale de l’agent prise en charge](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version), que l’agent est en cours d’exécution ([Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) et [Windows](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)) et que l’agent est à jour ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) et [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)).
« La machine virtuelle a signalé un échec lors du traitement de l’extension 'IaaSAntimalware' » | Vérifiez que vous n’avez pas d’autre offre de logiciel anti-programme malveillantou d’antivirus installée sur votre machine virtuelle. En cas d’échec, contactez le support.
Espace de travail ASC : Automanage ne prend pas actuellement en charge le service Log Analytics dans _emplacement_. | Vérifiez que votre machine virtuelle se trouve dans une [région prise en charge](./automanage-virtual-machines.md#supported-regions).
Le déploiement du modèle a échoué à cause de la violation de stratégie. Pour plus d’informations, consultez les détails. | Une stratégie empêche l’intégration d’Automanage à votre machine virtuelle. Vérifiez les stratégies qui sont appliquées à votre abonnement ou groupe de ressources contenant la machine virtuelle que vous souhaitez intégrer à Automanage.
« L’attribution a échoué ; aucune information supplémentaire n’est disponible. » | Ouvrez un cas auprès du support Microsoft Azure.

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur Azure Automanage](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [Activer le service Automanage pour machines virtuelles dans le portail Azure](quick-create-virtual-machines-portal.md)