---
title: Configurer la reprise d’activité après sinistre des machines virtuelles Azure à l’aide d’Azure Site Recovery
description: Découvrez comment configurer la récupération d’urgence pour des machines virtuelles Azure vers une autre région Azure avec le service Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 1/8/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: a7d25dfad20d8eff25020070d0bb32d5777fdb62
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754594"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Configurer la récupération d’urgence pour les machines virtuelles Azure

Le service [Azure Site Recovery](site-recovery-overview.md) contribue à votre stratégie de reprise d’activité après sinistre en gérant et en coordonnant la réplication, le basculement et la restauration automatique des machines locales et des machines virtuelles Azure.

Ce didacticiel vous montre comment configurer la récupération d’urgence pour des machines virtuelles Azure en les répliquant d’une région Azure vers un autre. Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un coffre Recovery Services
> * Vérifier les paramètres des ressources cibles
> * Configurer la connectivité réseau sortante pour machines virtuelles
> * Activer la réplication pour une machine virtuelle

> [!NOTE]
> Cet article fournit des instructions pour déployer la reprise d’activité après sinistre avec les paramètres les plus simples. Si vous souhaitez en savoir plus sur les paramètres personnalisés, consultez les articles listés sous la [section Procédures](azure-to-azure-how-to-enable-replication.md).

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre ce tutoriel :

- Examinez [l’architecture et les composants du scénario](concepts-azure-to-azure-architecture.md).
- Avant de commencer, consultez les [conditions de prise en charge](site-recovery-support-matrix-azure-to-azure.md).

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

Créez le coffre dans n’importe quelle région, à l’exception de la région source.

1. Connectez-vous au [portail Azure](https://portal.azure.com) > **Recovery Services**.
2. Dans le menu du Portail Azure ou dans la page **Accueil**, sélectionnez **Créer une ressource**. Ensuite, sélectionnez **Outils de gestion** > **Sauvegarde et Site Recovery**.
3. Dans **Nom**, indiquez un nom convivial permettant d’identifier le coffre. Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié.
4. Créez un groupe de ressources ou sélectionnez-en un. Spécifiez une région Azure. Pour découvrir les régions prises en charge, référez-vous à la disponibilité géographique de la page [Tarification de Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
5. Pour accéder rapidement au coffre à partir du tableau de bord, cliquez sur **Épingler au tableau de bord**, puis sur **Créer**.

   ![Nouveau coffre](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   Le nouveau coffre est ajouté à la zone **Tableau de bord** dans **Toutes les ressources** et dans la page principale **Coffres Recovery Services**.

## <a name="verify-target-resource-settings"></a>Vérifier les paramètres des ressources cibles

1. Vérifiez que votre abonnement Azure vous permet de créer des machines virtuelles dans la région cible. Contactez le support pour activer le quota requis.
2. Vérifiez que votre abonnement dispose de suffisamment de ressources pour prendre en charge la taille de vos machines virtuelles sources. Site Recovery choisit la même taille, ou la taille la plus proche possible, pour la machine virtuelle cible.

## <a name="set-up-outbound-network-connectivity-for-vms"></a>Configurer la connectivité réseau sortante pour machines virtuelles

Pour que Site Recovery fonctionne comme prévu, vous devez modifier la connectivité réseau sortante des machines virtuelles à répliquer.

> [!NOTE]
> Site Recovery ne prend pas en charge l’utilisation d’un proxy d’authentification pour contrôler la connectivité réseau.


### <a name="outbound-connectivity-for-urls"></a>Connectivité sortante pour les URL

Si vous utilisez un proxy de pare-feu basé sur des URL pour contrôler la connectivité sortante, autorisez l’accès à ces URL.

| **URL** | **Détails** |
| ------- | ----------- |
| *.blob.core.windows.net | Permet d’écrire les données dans le compte de stockage de cache dans la région source à partir de la machine virtuelle. |
| login.microsoftonline.com | Fournit l’autorisation et l’authentification aux URL du service Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Permet à la machine virtuelle de communiquer avec le service Site Recovery. |
| *.servicebus.windows.net | Permet à la machine virtuelle d’écrire des données de surveillance et de diagnostic Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Connectivité sortante pour les plages d’adresses IP

Si vous utilisez un groupe de sécurité réseau, créez des règles de groupe de sécurité réseau basées sur des étiquettes de service pour l’accès à Stockage Azure, à Azure Active Directory, au service Site Recovery et à la supervision Site Recovery. [Plus d’informations](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)

Si vous souhaitez contrôler la connectivité sortante à l’aide d’adresses IP plutôt que des règles de groupe de sécurité réseau, autorisez ces adresses pour les règles de pare-feu, de proxy et de groupe de sécurité réseau basées sur les adresses IP.

>[!NOTE]
>Nous vous recommandons de toujours configurer des règles de groupe de sécurité réseau avec des étiquettes de service pour l’accès sortant.

  - [Plages IP de centres de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653)
  - [Plages IP de centres de données Microsoft Azure en Allemagne](https://www.microsoft.com/download/details.aspx?id=54770)
  - [Plages IP de centres de données Microsoft Azure en Chine](https://www.microsoft.com/download/details.aspx?id=42064)
  - [URL et plages d’adresses IP Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [Adresses IP de points de terminaison du service Site Recovery](https://aka.ms/site-recovery-public-ips)

## <a name="verify-azure-vm-certificates"></a>Vérifier les certificats des machines virtuelles Azure

Vérifiez que les machines virtuelles que vous souhaitez répliquer ont les derniers certificats racines. Si ce n’est pas le cas, les machines virtuelles ne pourront pas être inscrites auprès du service Site Recovery en raison de contraintes de sécurité.

- Pour les machines virtuelles Windows, installez-y toutes les mises à jour de Windows les plus récentes afin que tous les certificats racines approuvés s’y trouvent. Dans un environnement déconnecté, suivez les processus Windows Update et de mise à jour de certificat standard en vigueur pour votre organisation.
- Pour les machines virtuelles Linux, suivez les instructions fournies par votre distributeur Linux pour obtenir les certificats racines approuvés les plus récents et la dernière liste de révocation de certificats sur la machine virtuelle.

## <a name="set-permissions-on-the-account"></a>Définir les autorisations sur le compte

Azure Site Recovery fournit trois rôles intégrés pour contrôler les opérations de gestion de Site Recovery.

- **Site Recovery Contributor** - Ce rôle dispose de toutes les autorisations requises pour gérer les opérations d’Azure Site Recovery dans un coffre Recovery Services. Toutefois, un utilisateur disposant de ce rôle ne peut pas créer ou supprimer un coffre Recovery Services, ni affecter des droits d’accès à d’autres utilisateurs. Ce rôle est tout indiqué pour les administrateurs de récupération d’urgence, qui peuvent activer et gérer la récupération d’urgence pour des applications ou organisations.

- **Site Recovery Operator** - Ce rôle dispose des autorisations d’exécution et de gestion des opérations de basculement et de restauration automatique. Un utilisateur disposant de ce rôle ne peut pas activer ou désactiver la réplication, créer ou supprimer des coffres, enregistrer une nouvelle infrastructure ou affecter des droits d’accès à d’autres utilisateurs. Ce rôle est tout indiqué pour un opérateur de récupération d’urgence, qui peut basculer des machines virtuelles ou des applications quand il en reçoit la demande des propriétaires et administrateurs informatiques. Une fois l’incident résolu, l’opérateur de récupération d’urgence peut reprotéger et restaurer automatiquement les machines virtuelles.

- **Site Recovery Reader** : Ce rôle dispose des autorisations pour afficher toutes les opérations de gestion de Site Recovery. Ce rôle est tout indiqué pour un responsable de suivi informatique, qui peut surveiller l’état de protection actuel et envoyer des billets d’assistance.

Apprenez-en davantage sur les [rôles intégrés RBAC Azure](../role-based-access-control/built-in-roles.md).

## <a name="enable-replication-for-a-vm"></a>Activer la réplication pour une machine virtuelle

### <a name="select-the-source"></a>Sélectionner la source

1. Dans Coffres Recovery Services, cliquez sur le nom du coffre > **+Répliquer**.
2. Dans **Source**, sélectionnez **Azure**.
3. Dans **Emplacement source**, sélectionnez la région Azure source où vos machines virtuelles s’exécutent actuellement.
4. Sélectionnez l’**abonnement source** à partir duquel les machines virtuelles sont exécutées. Il peut s’agir de n’importe quel abonnement au sein du même locataire Azure Active Directory où se trouve votre coffre Recovery services.
5. Sélectionnez le **groupe de ressources Source**, puis cliquez sur **OK** pour enregistrer les paramètres.

    ![Configurer la source](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>Sélectionner les machines virtuelles

Site Recovery récupère une liste des machines virtuelles associées à l’abonnement et au groupe de ressources/service cloud.

1. Dans **Machines virtuelles**, sélectionnez les machines virtuelles que vous souhaitez répliquer.
2. Cliquez sur **OK**.

### <a name="configure-replication-settings"></a>Configurer les paramètres de réplication

Site Recovery crée les paramètres par défaut et la stratégie de réplication pour la région cible. Vous pouvez modifier les paramètres selon vos besoins.

1. Cliquez sur **Paramètres** pour afficher les paramètres de cible et de réplication.
2. Pour remplacer les paramètres de cible par défaut, cliquez sur **Personnaliser** en regard de **Resource group, Network, Storage and Availability** (Groupe de ressources, réseau, stockage et groupes à haute disponibilité).

   ![Configurer les paramètres](./media/azure-to-azure-tutorial-enable-replication/settings.png)


3. Personnalisez les paramètres cibles comme récapitulé dans le tableau.

    **Paramètre** | **Détails**
    --- | ---
    **Abonnement cible** | Par défaut, l’abonnement cible est identique à l’abonnement source. Cliquez sur « Personnaliser » pour sélectionner un abonnement cible différent au sein du même locataire Azure Active Directory.
    **Emplacement cible** | région cible utilisée pour la récupération d’urgence.<br/><br/> Il est recommandé que l’emplacement cible corresponde à l’emplacement du coffre Site Recovery.
    **Groupe de ressources cible** | groupe de ressources dans la région cible qui héberge les machines virtuelles Azure après le basculement.<br/><br/> Par défaut, Site Recovery crée un groupe de ressources dans la région cible avec un suffixe « asr ». L’emplacement du groupe de ressources cible peut être n’importe quelle région Azure à l’exception de la région dans laquelle vos machines virtuelles sources sont hébergées.
    **Réseau virtuel cible** | réseau dans la région cible où se trouvent les machines virtuelles après le basculement.<br/><br/> Par défaut, Site Recovery crée un réseau virtuel (et des sous-réseaux) dans la région cible avec un suffixe « asr ».
    **Comptes Stockage de cache** | Site Recovery utilise un compte de stockage dans la région source. Les modifications apportées aux machines virtuelles sources sont envoyées sur ce compte avant la réplication vers l’emplacement cible.<br/><br/> Si vous utilisez un compte de stockage de cache autorisé par le pare-feu, veillez à activer l’option **Autoriser les services Microsoft approuvés**. [En savoir plus.](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) Vérifiez également que vous autorisez l’accès à au moins un sous-réseau de réseau virtuel source.
    **Comptes de stockage cibles (si la machine virtuelle source utilise des disques non managés)** | par défaut, Site Recovery crée un compte de stockage dans la région cible qui reflète le compte de stockage de la machine virtuelle source.<br/><br/> Activez **Autoriser les services Microsoft approuvés** si vous utilisez un compte de stockage de cache autorisé par le pare-feu.
    **Disques managés de réplica (si la machine virtuelle source utilise des disques managés)** | par défaut, Site Recovery crée des disques managés de réplica dans la région cible pour mettre en miroir les disques managés de la machine virtuelle source avec le même type de stockage (Standard ou Premium) que celui du disque managé de la machine virtuelle source. Seul le type de disque peut être personnalisé. 
    **Groupes à haute disponibilité cibles** | par défaut, Azure Site Recovery crée un groupe à haute disponibilité dans la région cible avec un nom comportant le suffixe « asr », pour les machines virtuelles qui font partie d’un groupe à haute disponibilité dans la région source. Si le groupe à haute disponibilité créé par Azure Site Recovery existe déjà, il est réutilisé.
    **Zones de disponibilité cibles** | par défaut, Site Recovery affecte à la région cible le même nombre de zones que la région source, si la région cible prend en charge les zones de disponibilité.<br/><br/> Si la région cible ne prend pas en charge les zones de disponibilité, les machines virtuelles cibles sont configurées comme des instances uniques par défaut.<br/><br/> Cliquez sur **Personnaliser** pour configurer des machines virtuelles d’un groupe à haute disponibilité dans la région cible.<br/><br/> Vous ne pouvez plus modifier le type de disponibilité (instance unique, groupe à haute disponibilité ou zone de disponibilité) une fois que vous avez activé la réplication. Vous devez désactiver puis réactiver la réplication pour modifier le type de disponibilité.

4. Pour personnaliser les paramètres de stratégie de réplication, cliquez sur **Personnaliser** à côté de **Stratégie de réplication**, puis modifiez les paramètres selon vos besoins.

    **Paramètre** | **Détails**
    --- | ---
    **Nom de la stratégie de réplication** | nom de la stratégie.
    **Conservation des points de récupération** | par défaut, Site Recovery conserve les points de récupération pendant 24 heures. Vous pouvez configurer une valeur comprise entre 1 et 72 heures.
    **Fréquence des instantanés de cohérence des applications** | par défaut, Site Recovery prend un instantané de cohérence des applications toutes les 4 heures. Vous pouvez configurer une valeur comprise entre 1 et 12 heures.<br/><br/> Une capture instantanée de cohérence des applications est un instantané à un instant T des données des applications à l’intérieur de la machine virtuelle. Le service VSS (Volume Shadow Copy Service) s’assure que les applications sur la machine virtuelle sont dans un état cohérent au moment de la prise des captures instantanées.
    **Groupe de réplication** | si votre application a besoin d’une cohérence multimachine virtuelle sur les machines virtuelles, vous pouvez créer un groupe de réplication pour ces machines virtuelles. Par défaut, les machines virtuelles sélectionnés ne font pas partie d’un groupe de réplication.

5. Si vous souhaitez ajouter des machines virtuelles à un groupe de réplication nouveau ou existant, dans **Personnaliser**, sélectionnez **Oui** pour activer la cohérence multimachine virtuelle Cliquez ensuite sur **OK**. 

    >[!NOTE]
    >- Toutes les machines d’un groupe de réplication ont des points de récupération partagés cohérents après incident et cohérents avec les applications lorsqu’elles basculent.
    >- L’activation de la cohérence multimachine virtuelle peut affecter les performances de la charge de travail (elle utilise intensivement le processeur). Il convient de l’utiliser uniquement si les machines exécutent la même charge de travail et si vous avez besoin de cohérence sur plusieurs machines virtuelles.
    >- Vous pouvez avoir un maximum de 16 machines virtuelles dans un groupe de réplication.
    >- Si vous activez la cohérence multimachine virtuelle, les machines du groupe de réplication communiquent entre elles sur le port 20004. Assurez-vous qu’aucun pare-feu ne bloque la communication interne entre les machines virtuelles sur ce port.
    >- Pour des machines virtuelles Linux dans un groupe de réplication, assurez-vous que le trafic sortant sur le port 20004 a été ouvert manuellement conformément aux instructions relatives à la version Linux.



### <a name="configure-encryption-settings"></a>Configurer les paramètres de chiffrement

Si Azure Disk Encryption est activé sur machine virtuelle source, vérifiez les paramètres.

1. Vérifiez les paramètres :
    - **Coffres de clés de chiffrement de disque** : Par défaut, Site Recovery crée un coffre de clés basé sur les clés de chiffrement de disque de machine virtuelle sources, avec un suffixe « asr ». Si le coffre de clés existe déjà, il est réutilisé.
    - **Coffres de clés de chiffrement de clé** : Par défaut, Site Recovery crée un coffre de clés dans la région cible. Le nom comporte un suffixe « asr » et est basé sur les clés de chiffrement à clé de machine virtuelle sources. Si le coffre de clés créé par Azure Site Recovery existe déjà, il est réutilisé.

2. Pour sélectionner des coffres de clés personnalisés, cliquez sur **Personnaliser**.

>[!NOTE]
>Seules les machines virtuelles Azure exécutant un système d’exploitation Windows et [permettant le chiffrement avec l’application Azure AD](https://aka.ms/ade-aad-app) sont prises en charge par Azure Site Recovery.
>

### <a name="track-replication-status"></a>Suivre l’état de la réplication

1. Dans **Paramètres**, cliquez sur **Actualiser** pour obtenir l’état le plus récent.
2. Pour suivre la progression et l’état :
    - Suivez la progression du travail **Activer la protection** dans **Paramètres** > **Travaux** > **Travaux Site Recovery**.
    - Dans **Paramètres** > **Éléments répliqués**, vous pouvez afficher l’état des machines virtuelles et la progression de la réplication initiale. Cliquez sur la machine virtuelle pour explorer ses paramètres.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez configuré la récupération d’urgence pour une machine virtuelle Azure. Maintenant, vous pouvez effectuer un test de reprise d’activité pour vérifier que le basculement fonctionne comme prévu.

> [!div class="nextstepaction"]
> [Effectuer un test de récupération d’urgence](azure-to-azure-tutorial-dr-drill.md)
