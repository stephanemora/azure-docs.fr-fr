---
title: Mise à jour automatique du service Mobilité dans Azure Site Recovery
description: Vue d’ensemble de la mise à jour automatique du service Mobilité lors de la réplication de machines virtuelles Azure à l’aide d’Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: rajanaki
ms.openlocfilehash: b57ce89979225015dc87bbfb17f9603897ef6d6b
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135842"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Mise à jour automatique du service Mobilité dans la réplication interne à Azure

Azure Site Recovery utilise une cadence de publication mensuelle pour résoudre les problèmes et améliorer les fonctionnalités existantes ou en ajouter de nouvelles. Pour rester à jour avec le service, vous devez planifier le déploiement de correctifs chaque mois. Pour éviter la surcharge associée à chaque mise à niveau, vous pouvez autoriser Site Recovery à gérer les mises à jour de composant.

Comme mentionné dans l’[architecture de récupération d’urgence interne à Azure](azure-to-azure-architecture.md), le service Mobility est installé sur toutes les machines virtuelles pour lesquelles la réplication est activée, d’une région Azure à une autre. Lorsque vous utilisez des mises à jour automatiques, chaque nouvelle version met à jour l’extension du service Mobilité.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Fonctionnement des mises à jour automatiques

Lorsque vous utilisez Site Recovery pour gérer les mises à jour, il déploie un runbook global (utilisé par les services Azure) via un compte Automation créé dans le même abonnement que le coffre. Chaque coffre utilise un seul compte Automation. Pour chaque machine virtuelle présente dans un coffre, le runbook vérifie les mises à jour automatiques actives. Si une version plus récente de l’extension du service Mobility est disponible, la mise à jour est installée.

La planification du runbook par défaut intervient tous les jours à 12h00 dans le fuseau horaire de la zone géographique de la machine virtuelle répliquée. Vous pouvez également modifier la planification du runbook via le compte Automation.

> [!NOTE]
> À partir du [correctif cumulatif 35](site-recovery-whats-new.md#updates-march-2019), vous pouvez choisir un compte Automation existant à utiliser pour les mises à jour. Avant le correctif cumulatif 35, Site Recovery créait le compte Automation par défaut. Vous pouvez uniquement sélectionner cette option lorsque vous activez la réplication pour une machine virtuelle. Elle n’est pas disponible pour une machine virtuelle pour laquelle la réplication est déjà activée. Le paramètre que vous sélectionnez s’applique à toutes les machines virtuelles Azure protégées dans le même coffre.

L’activation des mises à jour automatiques ne nécessite pas un redémarrage de vos machines virtuelles Azure ni n’affecte la réplication en cours.

La facturation du travail dans le compte Automation est basée sur le nombre de minutes d’exécution de travail réellement utilisées au cours d’un mois. L’exécution du travail prend quelques secondes à une minute environ chaque jour et elle est couverte en tant qu’unités gratuites. Par défaut, 500 minutes sont incluses en tant qu’unités gratuites pour un compte Automation, comme indiqué dans le tableau suivant :

| Unités gratuites incluses (chaque mois) | Price |
|---|---|
| Exécution de travail de 500 minutes | 0,14 ₹/minute

## <a name="enable-automatic-updates"></a>Activation des mises à jour automatiques

Site Recovery peut gérer les mises à jour d’extension de différentes manières :

- [Gérer dans le cadre de l’étape d’activation de la réplication](#manage-as-part-of-the-enable-replication-step)
- [Activer/désactiver les paramètres de mise à jour de l’extension dans le coffre](#toggle-the-extension-update-settings-inside-the-vault)
- [Gérer les mises à jour manuellement](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>Gérer dans le cadre de l’étape d’activation de la réplication

Lorsque vous activez la réplication pour une machine virtuelle [à partir de la vue de la machine virtuelle](azure-to-azure-quickstart.md) ou [à partir du coffre Recovery Services](azure-to-azure-how-to-enable-replication.md), vous pouvez autoriser Site Recovery à gérer les mises à jour de l’extension Site Recovery ou les gérer manuellement.

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="Paramètres d’extension":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Activer/désactiver les paramètres de mise à jour de l’extension dans le coffre

1. À partir du coffre Recovery Services, accédez à **Gérer** > **Infrastructure Site Recovery**.
1. Sous **Pour les machines virtuelles Azure** > **Paramètres de mise à jour de l’extension** > **Autoriser Site Recovery à gérer**, sélectionnez **Activer**.

   Pour gérer l’extension manuellement, sélectionnez **Désactiver**.

1. Sélectionnez **Enregistrer**.

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="Paramètres de mise à jour de l’extension":::

> [!IMPORTANT]
> Lorsque vous sélectionnez **Autoriser Site Recovery à gérer**, le paramètre est appliqué à toutes les machines virtuelles présentes dans le coffre.

> [!NOTE]
> Chaque option vous informe du compte Automation utilisé pour la gestion des mises à jour. Si vous activez cette fonctionnalité dans un coffre pour la première fois, un nouveau compte Automation est créé par défaut. Vous pouvez également personnaliser le paramètre et choisir un compte Automation existant. Toutes les tâches suivantes visant à activer la réplication dans le même coffre utilisent le compte Automation précédemment créé. Actuellement, le menu déroulant répertorie uniquement les comptes Automation qui se trouvent dans le même groupe de ressources que le coffre.

### <a name="manage-updates-manually"></a>Gérer les mises à jour manuellement

1. Si de nouvelles mises à jour pour le service Mobilité sont installées sur vos machines virtuelles, vous verrez la notification suivante : **Une nouvelle mise à jour de l’agent de réplication Site Recovery est disponible. Cliquez pour installer.**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="Fenêtre Éléments répliqués":::

1. Sélectionnez la notification pour ouvrir la page de sélection de machine virtuelle.
1. Choisissez les machines virtuelles que vous voulez mettre à niveau, puis sélectionnez **OK**. Le service de mise à jour de la Mobilité démarre pour chaque machine virtuelle sélectionnée.

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="Éléments répliqués - Liste des machines virtuelles":::

## <a name="common-issues-and-troubleshooting"></a>Problèmes courants et résolutions

En cas de problème lié aux mises à jour automatiques, vous voyez une notification d’erreur sous **Problèmes de configuration** dans le tableau de bord du coffre.

Si vous ne pouvez pas activer les mises à jour automatiques, consultez les erreurs courantes et actions recommandées suivantes :

- **Erreur** : Vous n’avez pas les autorisations nécessaires pour créer un compte d’identification Azure (principal du service) et octroyer le rôle Contributeur au principal du service.

  **Action recommandée** : Vérifiez que le compte de connexion est affecté en tant que Contributeur et réessayez. Pour plus d’informations sur l’affectation des autorisations, consultez la section relative aux autorisations requises de [Procédure : Utilisez le portail pour créer une application Azure AD et un principal du service pouvant accéder aux ressources](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

  Pour résoudre la plupart des problèmes après avoir activé les mises à jour automatiques, sélectionnez **Réparer**. Si le bouton de réparation n’est pas disponible, consultez le message d’erreur affiché dans le volet des paramètres de mise à jour de l’extension.

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="Bouton de réparation du service Site Recovery dans les paramètres de mise à jour de l’extension":::

- **Erreur** : le compte d’identification n’a pas l’autorisation d’accéder à la ressource Recovery Services.

  **Action recommandée** : Supprimez puis [recréez le compte d’identification](../automation/manage-runas-account.md). Ou vérifiez que le compte d’identification Automation de l’application Azure Active Directory peut accéder à la ressource Recovery Services.

- **Erreur** : le compte d’identification est introuvable. Un de ces éléments a été supprimé ou n’a pas été créé : Application Azure Active Directory, Principal du service, Rôle, Ressource de certificat Automation, Ressource de connexion Automation, ou l’empreinte numérique n’est pas identique entre le certificat et la connexion.

  **Action recommandée** : Supprimez puis [recréez le compte d’identification](../automation/manage-runas-account.md).

- **Erreur** : Le certificat d’identification d’Azure utilisé par le compte Automation va bientôt expirer.

  Le certificat auto-signé créé pour le compte d’identification expire un an après la date de création. Vous pouvez le renouveler à tout moment avant qu’il n’expire. Si vous êtes inscrit aux notifications par e-mail, vous recevrez également des e-mails lorsqu’une action de votre part est requise. Cette erreur s’affiche deux mois avant la date d’expiration et devient une erreur critique si le certificat a expiré. Une fois que le certificat a expiré, la mise à jour automatique ne fonctionne plus tant que vous ne le renouvelez pas.

  **Action recommandée** : Pour résoudre ce problème, sélectionnez **Réparer**, puis **Renouveler le certificat**.

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="renew-cert":::

  > [!NOTE]
  > Une fois le certificat renouvelé, actualisez la page pour afficher l’état actuel.
