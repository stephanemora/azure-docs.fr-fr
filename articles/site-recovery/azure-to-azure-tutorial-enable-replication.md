---
title: Configurer la reprise d’activité après sinistre des machines virtuelles Azure à l’aide d’Azure Site Recovery
description: Découvrez comment configurer la récupération d’urgence pour des machines virtuelles Azure vers une autre région Azure avec le service Azure Site Recovery.
ms.topic: tutorial
ms.date: 1/24/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 50bf1ec7f21ccbc3a3fa8feaea02e45bd08a158a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87421414"
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
> Cet article fournit des instructions pour déployer la reprise d’activité après sinistre avec les paramètres les plus simples. Si vous souhaitez en savoir plus sur les paramètres personnalisés, consultez les articles listés dans la [section Procédures](azure-to-azure-how-to-enable-replication.md).

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel :

- Examinez [l’architecture et les composants du scénario](./azure-to-azure-architecture.md).
- Avant de commencer, consultez les [conditions de prise en charge](./azure-to-azure-support-matrix.md).

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

Créez le coffre dans n’importe quelle région, à l’exception de la région source.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans le menu du Portail Azure ou dans la page **Accueil**, sélectionnez **Créer une ressource**. Ensuite, sélectionnez **Informatique et outils de gestion** > **Sauvegarde et Site Recovery**.
1. Dans **Nom**, indiquez un nom convivial permettant d’identifier le coffre. Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié.
1. Créez un groupe de ressources ou sélectionnez-en un. Spécifiez une région Azure. Pour découvrir les régions prises en charge, référez-vous à la disponibilité géographique de la page [Tarification de Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
1. Pour accéder au coffre à partir du tableau de bord, sélectionnez **Épingler au tableau de bord**, puis **Créer**.

   ![Nouveau coffre](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

Le nouveau coffre est ajouté à la zone **Tableau de bord** dans **Toutes les ressources** et dans la page principale **Coffres Recovery Services**.

## <a name="verify-target-resource-settings"></a>Vérifier les paramètres des ressources cibles

Vérifiez la prise en charge de la région cible dans votre abonnement Azure.

- Vérifiez que votre abonnement Azure vous permet de créer des machines virtuelles dans la région cible. Contactez le support pour activer le quota requis.
- Vérifiez que votre abonnement dispose de suffisamment de ressources pour prendre en charge la taille de vos machines virtuelles sources. Site Recovery choisit la même taille, ou la taille la plus proche possible, pour la machine virtuelle cible.

## <a name="set-up-outbound-network-connectivity-for-vms"></a>Configurer la connectivité réseau sortante pour machines virtuelles

Pour que Site Recovery fonctionne comme prévu, vous devez modifier la connectivité réseau sortante des machines virtuelles à répliquer.

> [!NOTE]
> Site Recovery ne prend pas en charge l’utilisation d’un proxy d’authentification pour contrôler la connectivité réseau.

### <a name="outbound-connectivity-for-urls"></a>Connectivité sortante pour les URL

Si vous utilisez un proxy de pare-feu basé sur des URL pour contrôler la connectivité sortante, autorisez l’accès à ces URL :

| **Nom**                  | **Commerciale**                               | **Secteur public**                                 | **Description** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Stockage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`              | Permet d’écrire les données dans le compte de stockage de cache dans la région source à partir de la machine virtuelle. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Fournit l’autorisation et l’authentification aux URL du service Site Recovery. |
| Réplication               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Permet à la machine virtuelle de communiquer avec le service Site Recovery. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Permet à la machine virtuelle d’écrire des données de surveillance et de diagnostic Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Connectivité sortante pour les plages d’adresses IP

Si vous utilisez un groupe de sécurité réseau, créez des règles de groupe de sécurité réseau basées sur des balises de service pour l’accès à Stockage Azure, à Azure Active Directory, au service Site Recovery et à la supervision Site Recovery. [Plus d’informations](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)

## <a name="verify-azure-vm-certificates"></a>Vérifier les certificats des machines virtuelles Azure

Vérifiez que les machines virtuelles que vous souhaitez répliquer ont les derniers certificats racines. Si ce n’est pas le cas, les machines virtuelles ne pourront pas être inscrites auprès du service Site Recovery en raison de contraintes de sécurité.

- Pour les machines virtuelles Windows, installez-y toutes les mises à jour de Windows les plus récentes afin que tous les certificats racines approuvés s’y trouvent. Dans un environnement déconnecté, suivez les processus Windows Update et de mise à jour de certificat standard en vigueur pour votre organisation.
- Pour les machines virtuelles Linux, suivez les instructions fournies par votre distributeur Linux pour obtenir les certificats racines approuvés les plus récents et la dernière liste de révocation de certificats sur la machine virtuelle.

## <a name="set-permissions-on-the-account"></a>Définir les autorisations sur le compte

Azure Site Recovery fournit trois rôles intégrés pour contrôler les opérations de gestion de Site Recovery.

- **Site Recovery Contributor** - Ce rôle dispose de toutes les autorisations requises pour gérer les opérations d’Azure Site Recovery dans un coffre Recovery Services. Toutefois, un utilisateur disposant de ce rôle ne peut pas créer ou supprimer un coffre Recovery Services, ni affecter des droits d’accès à d’autres utilisateurs. Ce rôle est tout indiqué pour les administrateurs de récupération d’urgence, qui peuvent activer et gérer la récupération d’urgence pour des applications ou organisations.

- **Site Recovery Operator** - Ce rôle dispose des autorisations d’exécution et de gestion des opérations de basculement et de restauration automatique. Un utilisateur disposant de ce rôle ne peut pas activer ou désactiver la réplication, créer ou supprimer des coffres, enregistrer une nouvelle infrastructure ou affecter des droits d’accès à d’autres utilisateurs. Ce rôle est tout indiqué pour un opérateur de récupération d’urgence, qui peut basculer des machines virtuelles ou des applications quand il en reçoit la demande des propriétaires et administrateurs informatiques. Une fois l’incident résolu, l’opérateur de reprise d’activité peut reprotéger et restaurer automatiquement les machines virtuelles.

- **Site Recovery Reader** : Ce rôle dispose des autorisations pour afficher toutes les opérations de gestion de Site Recovery. Ce rôle est tout indiqué pour un responsable de suivi informatique, qui peut surveiller l’état de protection actuel et envoyer des billets d’assistance.

Découvrez plus d’informations sur les [rôles intégrés Azure](../role-based-access-control/built-in-roles.md).

## <a name="enable-replication-for-a-vm"></a>Activer la réplication pour une machine virtuelle

Les sections suivantes décrivent comment activer la réplication.

### <a name="select-the-source"></a>Sélectionner la source

Pour commencer la configuration de la réplication, choisissez la source où vos machines virtuelles Azure s’exécutent.

1. Accédez à **Coffres Recovery Services**, sélectionnez le nom du coffre, puis **+ Répliquer**.
1. Dans **Source**, sélectionnez **Azure**.
1. Dans **Emplacement source**, sélectionnez la région Azure source où vos machines virtuelles s’exécutent actuellement.
1. Sélectionnez l’**abonnement source** à partir duquel les machines virtuelles sont exécutées. Il peut s’agir de n’importe quel abonnement au sein du même locataire Azure Active Directory où se trouve votre coffre Recovery services.
1. Sélectionnez le **groupe de ressources Source**, puis sélectionnez **OK** pour enregistrer les paramètres.

   ![Configurer la source](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>Sélectionner les machines virtuelles

Site Recovery récupère une liste des machines virtuelles associées à l’abonnement et au groupe de ressources/service cloud.

1. Dans **Machines virtuelles**, sélectionnez les machines virtuelles que vous souhaitez répliquer.
1. Sélectionnez **OK**.

### <a name="configure-replication-settings"></a>Configurer les paramètres de réplication

Site Recovery crée les paramètres par défaut et la stratégie de réplication pour la région cible. Vous pouvez modifier ces paramètres selon vos besoins.

1. Sélectionnez **Paramètres** pour afficher les paramètres de cible et de réplication.

1. Pour remplacer les paramètres de cible par défaut, sélectionnez **Personnaliser** en regard de **Groupe de ressources, Réseau, Stockage et Disponibilité**.

   ![Configurer les paramètres](./media/azure-to-azure-tutorial-enable-replication/settings.png)

1. Personnalisez les paramètres cibles comme récapitulé dans le tableau.

   | **Paramètre** | **Détails** |
   | --- | --- |
   | **Abonnement cible** | Par défaut, l’abonnement cible est identique à l’abonnement source. Sélectionnez **Personnaliser** pour sélectionner un abonnement cible différent au sein du même locataire Azure Active Directory. |
   | **Emplacement cible** | région cible utilisée pour la récupération d’urgence.<br/><br/> Il est recommandé que l’emplacement cible corresponde à l’emplacement du coffre Site Recovery. |
   | **Groupe de ressources cible** | groupe de ressources dans la région cible qui héberge les machines virtuelles Azure après le basculement.<br/><br/> Par défaut, Site Recovery crée un groupe de ressources dans la région cible avec un suffixe `asr`. L’emplacement du groupe de ressources cible peut être n’importe quelle région Azure à l’exception de la région dans laquelle vos machines virtuelles sources sont hébergées. |
   | **Réseau virtuel cible** | réseau dans la région cible où se trouvent les machines virtuelles après le basculement.<br/><br/> Par défaut, Site Recovery crée un réseau virtuel (et des sous-réseaux) dans la région cible avec un suffixe `asr`. |
   | **Comptes Stockage de cache** | Site Recovery utilise un compte de stockage dans la région source. Les modifications apportées aux machines virtuelles sources sont envoyées sur ce compte avant la réplication vers l’emplacement cible.<br/><br/> Si vous utilisez un compte de stockage de cache autorisé par le pare-feu, veillez à activer l’option **Autoriser les services Microsoft approuvés**. [Plus d’informations](../storage/common/storage-network-security.md#exceptions) Vérifiez également que vous autorisez l’accès à au moins un sous-réseau du réseau virtuel source. |
   | **Comptes de stockage cibles (si la machine virtuelle source utilise des disques non managés)** | par défaut, Site Recovery crée un compte de stockage dans la région cible qui reflète le compte de stockage de la machine virtuelle source.<br/><br/> Activez **Autoriser les services Microsoft approuvés** si vous utilisez un compte de stockage de cache autorisé par le pare-feu. |
   | **Disques managés de réplica (si la machine virtuelle source utilise des disques managés)** | Par défaut, Site Recovery crée des disques managés de réplica dans la région cible pour mettre en miroir les disques managés de la machine virtuelle source avec le même type de stockage (Standard ou Premium) que celui du disque managé de la machine virtuelle source. Seul le type de disque peut être personnalisé. |
   | **Groupes à haute disponibilité cibles** | Par défaut, Azure Site Recovery crée un groupe à haute disponibilité dans la région cible avec un nom comportant le suffixe `asr`, pour les machines virtuelles qui font partie d’un groupe à haute disponibilité dans la région source. Si le groupe à haute disponibilité créé par Azure Site Recovery existe déjà, il est réutilisé. |
   | **Zones de disponibilité cibles** | par défaut, Site Recovery affecte à la région cible le même nombre de zones que la région source, si la région cible prend en charge les zones de disponibilité.<br/><br/> Si la région cible ne prend pas en charge les zones de disponibilité, les machines virtuelles cibles sont configurées comme des instances uniques par défaut.<br/><br/> Sélectionnez **Personnaliser** pour configurer des machines virtuelles d’un groupe à haute disponibilité dans la région cible.<br/><br/> Vous ne pouvez plus changer le type de disponibilité (instance unique, groupe à haute disponibilité ou zone de disponibilité) une fois que vous avez activé la réplication. Pour changer le type de disponibilité, vous devez désactiver puis réactiver la réplication. |

1. Pour personnaliser les paramètres de stratégie de réplication, sélectionnez **Personnaliser** en regard de **Stratégie de réplication**, puis modifiez les paramètres en fonction de vos besoins.

   | **Paramètre** | **Détails** |
   | --- | --- |
   | **Nom de la stratégie de réplication** | nom de la stratégie. |
   | **Conservation des points de récupération** | par défaut, Site Recovery conserve les points de récupération pendant 24 heures. Vous pouvez configurer une valeur comprise entre 1 et 72 heures. |
   | **Fréquence des instantanés de cohérence des applications** | par défaut, Site Recovery prend un instantané de cohérence des applications toutes les 4 heures. Vous pouvez configurer une valeur comprise entre 1 et 12 heures.<br/><br/> Une capture instantanée de cohérence des applications est un instantané à un instant T des données des applications à l’intérieur de la machine virtuelle. Le service VSS (Volume Shadow Copy Service) s’assure que les applications sur la machine virtuelle sont dans un état cohérent au moment de la prise des captures instantanées. |
   | **Groupe de réplication** | si votre application a besoin d’une cohérence multimachine virtuelle sur les machines virtuelles, vous pouvez créer un groupe de réplication pour ces machines virtuelles. Par défaut, les machines virtuelles sélectionnés ne font pas partie d’un groupe de réplication. |

1. Si vous souhaitez ajouter des machines virtuelles à un groupe de réplication nouveau ou existant, dans **Personnaliser**, sélectionnez **Oui** pour activer la cohérence multimachine virtuelle Sélectionnez ensuite **OK**.

   > [!NOTE]
   > - Toutes les machines d’un groupe de réplication ont des points de récupération partagés cohérents après incident et cohérents avec les applications lorsqu’elles basculent.
   > - L’activation de la cohérence multimachine virtuelle peut affecter les performances de la charge de travail (elle utilise intensivement le processeur). Il convient de l’utiliser uniquement si les machines exécutent la même charge de travail et si vous avez besoin de cohérence sur plusieurs machines virtuelles.
   > - Vous pouvez avoir un maximum de 16 machines virtuelles dans un groupe de réplication.
   > - Si vous activez la cohérence multimachine virtuelle, les machines du groupe de réplication communiquent entre elles sur le port 20004. Assurez-vous qu’aucun pare-feu ne bloque la communication interne entre les machines virtuelles sur ce port.
   > - Pour des machines virtuelles Linux dans un groupe de réplication, assurez-vous que le trafic sortant sur le port 20004 a été ouvert manuellement conformément aux instructions relatives à la version Linux.

### <a name="configure-encryption-settings"></a>Configurer les paramètres de chiffrement

Si Azure Disk Encryption est activé sur machine virtuelle source, vérifiez les paramètres.

1. Vérifiez les paramètres :
   1. **Coffres de clés de chiffrement de disque** : Par défaut, Site Recovery crée un coffre de clés basé sur les clés de chiffrement de disque de machine virtuelle sources, avec un suffixe `asr`. Si le coffre de clés existe déjà, il est réutilisé.
   1. **Coffres de clés de chiffrement de clé** : Par défaut, Site Recovery crée un coffre de clés dans la région cible. Le nom comporte un suffixe `asr` et est basé sur les clés de chiffrement principales de machine virtuelle sources. Si le coffre de clés créé par Azure Site Recovery existe déjà, il est réutilisé.
1. Pour sélectionner des coffres de clés personnalisés, sélectionnez **Personnaliser**.

>[!NOTE]
> Site Recovery prend actuellement en charge ADE, avec et sans Azure Active Directory (AAD) pour les machines virtuelles sur Windows. Dans le cas des systèmes d’exploitation Linux, nous ne gérons ADE que sans AAD. Par ailleurs, les machines virtuelles exécutant ADE 1.1 (sans AAD) doivent utiliser des disques managés. Les machines virtuelles avec des disques non managés ne sont pas prises en charge. Si vous passez de ADE 0.1 (avec AAD) à 1.1, vous devez désactiver la réplication et activer la réplication pour une machine virtuelle après avoir activé 1.1.

### <a name="track-replication-status"></a>Suivre l’état de la réplication

Une fois la réplication activée, vous pouvez suivre l’état du travail.

1. Dans **Paramètres**, sélectionnez **Actualiser** pour obtenir l’état le plus récent.
1. Pour suivre la progression et l’état :
   1. Suivez la progression du travail **Activer la protection** dans **Paramètres** > **Travaux** > **Travaux Site Recovery**.
   1. Dans **Paramètres** > **Éléments répliqués**, vous pouvez afficher l’état des machines virtuelles et la progression de la réplication initiale. Sélectionnez la machine virtuelle pour explorer ses paramètres.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez configuré la récupération d’urgence pour une machine virtuelle Azure. Vous pouvez maintenant effectuer une simulation de reprise d’activité après sinistre afin de vérifier que le basculement fonctionne comme prévu.

> [!div class="nextstepaction"]
> [Effectuer un test de récupération d’urgence](azure-to-azure-tutorial-dr-drill.md)
