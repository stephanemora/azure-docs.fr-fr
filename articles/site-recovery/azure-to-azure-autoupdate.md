---
title: Mise à jour automatique du service Mobilité vers la reprise d’activité après sinistre interne à Azure | Microsoft Docs
description: Fournit une vue d’ensemble de la mise à jour automatique du service Mobilité, lors de la réplication de machines virtuelles Azure à l’aide d’Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/16/2018
ms.author: rajanaki
ms.openlocfilehash: add80b17c76e7262f55e50cd07d4e9b053cfa1ff
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209829"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Mise à jour automatique du service Mobilité dans la réplication Azure à Azure

Azure Site Recovery a une cadence de publication mensuelle. À cette occasion, des améliorations sont apportées aux fonctionnalités existantes, de nouvelles fonctionnalités sont ajoutées et les éventuels problèmes connus sont résolus. Cela signifie que, pour que le service soit à jour, vous devez planifier le déploiement de ces correctifs sur une base mensuelle. Afin d’éviter la surcharge associée à la mise à niveau, les utilisateurs peuvent à la place autoriser Site Recovery à gérer les mises à jour des composants. Comme détaillé dans la [référence d’architecture](azure-to-azure-architecture.md) pour la reprise d’activité après sinistre interne à Azure, le service Mobilité est installé sur toutes les machines virtuelles pour lesquelles la réplication est activée lors de la réplication des machines virtuelles d’une région Azure à une autre. Lorsque vous activez la mise à jour automatique, l’extension du service Mobilité est mise à jour à chaque nouvelle version. Ce document fournit les informations suivantes :

- Fonctionnement de la mise à jour automatique
- Activation des mises à jour automatiques
- Problèmes courants et résolutions
 
## <a name="how-does-automatic-update-work"></a>Fonctionnement de la mise à jour automatique

Une fois que vous autorisez Site Recovery à gérer les mises à jour, un runbook global (qui est utilisé par les services Azure) est déployé via un compte Automation, qui est créé dans le même abonnement que le coffre. Un seul compte Automation est utilisé pour un coffre spécifique. Le runbook vérifie pour chaque machine virtuelle présente dans un coffre quelles sont les mises à jour automatiques activées et lance une mise à niveau de l’extension du service Mobilité si une version plus récente est disponible. La planification par défaut du runbook a lieu tous les jours à minuit selon le fuseau horaire de la zone géographique de la machine virtuelle répliquée. La planification du runbook peut également être modifiée via le compte Automation par l’utilisateur, si nécessaire. 

> [!NOTE]
> L’activation des mises à jour automatiques ne nécessite pas un redémarrage de vos machines virtuelles Azure et n’affecte pas la réplication en cours.

## <a name="enable-automatic-updates"></a>Activation des mises à jour automatiques

Vous pouvez choisir d’autoriser Site Recovery à gérer les mises à jour comme suit :

- [Dans le cadre de l’étape d’activation de la réplication](#as-part-of-the-enable-replication-step)
- [Activer/désactiver les paramètres de mise à jour de l’extension dans le coffre](#toggle-the-extension-update-settings-inside-the-vault)

### <a name="as-part-of-the-enable-replication-step"></a>Dans le cadre de l’étape d’activation de la réplication :

Lorsque vous activez la réplication pour une machine virtuelle [à partir de la vue de la machine virtuelle](azure-to-azure-quickstart.md) ou à partir [du coffre Recovery services](azure-to-azure-how-to-enable-replication.md), une option vous permet d’autoriser Site Recovery à gérer les mises à jour de l’extension Site Recovery ou à gérer cela manuellement.

![enable-replication-auto-update](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Activer/désactiver les paramètres de mise à jour de l’extension dans le coffre

1. Dans le coffre, accédez à **Gérer**-> **Infrastructure Site Recovery**.
2. Sous **For Azure virtual Machines (Pour des machines virtuelles Azure)**-> **Paramètres de mise à jour de l’extension**, cliquez sur le bouton bascule pour choisir si vous souhaitez autoriser *ASR à gérer les mises à jour* ou si vous souhaitez *les gérer manuellement*. Cliquez sur **Enregistrer**.

![vault-toggle-autuo-update](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important] 
> Lorsque vous choisissez *Autoriser ASR à gérer*, le paramètre est appliqué à toutes les machines virtuelles dans le coffre correspondant.


> [!Note] 
> Les deux options vous informent du compte Automation qui permet de gérer les mises à jour. Si vous activez cette fonctionnalité pour la première fois dans un coffre, un nouveau compte Automation est créé. Toutes les activations de réplications suivantes dans le même coffre utilisent celui créé précédemment.

## <a name="common-issues--troubleshooting"></a>Problèmes courants et résolutions

S’il existe un problème avec les mises à jour automatiques, vous en êtes averti sous Problèmes de configuration dans le tableau de bord du coffre. 

Si vous avez tenté d’activer les mises à jour automatiques et que l’opération a échoué, reportez-vous à la section ci-dessous relative à la résolution des problèmes.

**Erreur** : Vous ne disposez pas des autorisations nécessaires pour créer un compte d’identification Azure (principal du service) ni pour accorder le rôle de contributeur au principal du service. 
- Action recommandée : Vérifiez que le rôle Contributeur est attribué au compte connecté et recommencez l’opération.
 
Une fois que les mises à jour automatiques sont activées, la plupart des problèmes peuvent être corrigés par le service Site Recovery. Vous devez pour cela cliquer sur le bouton **Réparation**.

![repair-button](./media/azure-to-azure-autoupdate/repair.png)

Si le bouton de réparation n’est pas disponible, consultez le message d’erreur affiché sous le volet des paramètres d’extension.

 - **Erreur** : Le compte d’identification n’a pas l’autorisation d’accéder à la ressource Recovery Services.

    **Action recommandée** : supprimez puis [recréez le compte d’identification](https://docs.microsoft.com/en-us/azure/automation/automation-create-runas-account) ou vérifiez que le compte d’identification Automation de l’application Azure Active Directory a accès à la ressource Recovery Services.

- **Erreur** : Le compte d’identification est introuvable. Un de ces éléments a été supprimé ou n’a pas été créé : Application Azure Active Directory, Principal du service, Rôle, Ressource de certificat Automation, Ressource de connexion Automation, ou l’empreinte numérique n’est pas identique entre le certificat et la connexion. 

    **Action recommandée** : supprimez [puis recréez le compte d’identification](https://docs.microsoft.com/en-us/azure/automation/automation-create-runas-account).
