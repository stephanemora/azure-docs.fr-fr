---
title: Gérer les serveurs VMWare vCenter pour la récupération après sinistre des machines virtuelles VMware sur Azure avec Azure Site Recovery | Microsoft Docs
description: Cet article décrit comment ajouter et gérer VMware vCenter pour la récupération après sinistre des machines virtuelles VMware sur Azure avec Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ramamill
ms.openlocfilehash: 59088d8351bf89c859312774e3e9e396be8dd532
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69904251"
---
# <a name="manage-vmware-vcenter-server"></a>Gérer un serveur VMware vCenter

Cet article présente les différentes opérations Site Recovery qui peuvent être effectuées sur un serveur VMware vCenter. Vérifiez les [conditions préalables](vmware-physical-azure-support-matrix.md#replicated-machines) avant de commencer.


## <a name="set-up-an-account-for-automatic-discovery"></a>Configurer un compte pour la détection automatique

Site Recovery a besoin d’accéder à VMware pour que le serveur de processus découvre automatiquement des machines virtuelles ainsi que pour le basculement et la restauration automatique des machines virtuelles. Créez un compte d’accès comme suit :

1. Connectez-vous à la machine serveur de configuration.
2. Ouvrez l’exécutable cspsconfigtool.exe à l’aide du raccourci Bureau.
3. Cliquez sur **Ajouter un compte** dans l’onglet **Gérer les comptes**.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Fournissez les détails du compte, puis cliquez sur **OK** pour l’ajouter.  Le compte doit disposer des privilèges résumés dans le tableau suivant. 

La synchronisation des informations du compte avec le service Site Recovery dure environ 15 minutes.

### <a name="account-permissions"></a>Autorisations du compte

|**Tâche** | **Compte** | **autorisations** | **Détails**|
|--- | --- | --- | ---|
|**Détection automatique/migrer (sans restauration automatique)** | Vous devez disposer d’au moins un utilisateur en lecture seule | Objet de centre de données -> Propager vers l’objet enfant, rôle = lecture seule | L’utilisateur est affecté au niveau du centre de données et a accès à tous les objets dans le centre de données.<br/><br/> Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’objet **Propager vers enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).|
|**Réplication/basculement** | Vous devez disposer d’au moins un utilisateur en lecture seule| Objet de centre de données -> Propager vers l’objet enfant, rôle = lecture seule | L’utilisateur est affecté au niveau du centre de données et a accès à tous les objets dans le centre de données.<br/><br/> Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’objet **Propager vers enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).<br/><br/> Utile à des fins de migration, mais pas pour la réplication complète, le basculement et la restauration automatique.|
|**Réplication/basculement/restauration automatique** | Nous vous suggérons de créer un rôle (AzureSiteRecoveryRole) avec les autorisations nécessaires, puis d’attribuer le rôle à un utilisateur ou à un groupe d’utilisateurs VMware | Objet de centre de données -> Propager vers l’objet enfant, rôle = AzureSiteRecoveryRole<br/><br/> Banque de données -> Allouer de l’espace, parcourir la banque de données, opérations de fichier de bas niveau, supprimer le fichier, mettre à jour les fichiers de machine virtuelle<br/><br/> Réseau -> Attribution de réseau<br/><br/> Ressource -> Affecter les machines virtuelles au pool de ressources, migrer des machines virtuelles hors tension, migrer des machines virtuelles sous tension<br/><br/> Tâches -> Créer une tâche, Mettre à jour une tâche<br/><br/> Machine virtuelle -> Configuration<br/><br/> Machine virtuelle -> Interagir -> répondre à la question, connexion d’appareil, configurer un support de CD, configurer une disquette, mettre hors tension, mettre sous tension, installation des outils VMware<br/><br/> Machine virtuelle -> Stock -> Créer, inscrire, désinscrire<br/><br/> Machine virtuelle -> Approvisionnement -> Autoriser le téléchargement de machines virtuelles, autoriser le chargement de fichiers de machine virtuelle<br/><br/> Machine virtuelle -> Instantanés -> Supprimer les instantanés | L’utilisateur est affecté au niveau du centre de données et a accès à tous les objets dans le centre de données.<br/><br/> Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’objet **Propager vers enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).|


## <a name="add-vmware-server-to-the-vault"></a>Ajouter un serveur VMware dans le coffre

1. Sur le portail Azure, ouvrez votre coffre > **Site Recovery Infrastructure** (Infrastructure Site Recovery) > **Configuration Severs** (Serveurs de configuration) et ouvrez le serveur de configuration.
2. Sur la page **Détails**, cliquez sur **+vCenter**.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Modifier les informations d’identification

Modifiez les informations d’identification utilisées pour se connecter au serveur vCenter ou à l’hôte vSphere ESXi comme suit :

1. Connectez-vous au serveur de configuration et exécutez le fichier cspconfigtool.exe depuis le Bureau.
2. Cliquez sur **Ajouter un compte** dans l’onglet **Gérer les comptes**.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Fournissez les détails du nouveau compte, puis cliquez sur **OK** pour l’ajouter. Le compte doit disposer des privilèges répertoriés [ci-dessus](#account-permissions).
4. Sur le portail Azure, ouvrez le coffre > **Site Recovery Infrastructure** (Infrastructure Site Recovery) > **Configuration Severs** (Serveurs de configuration) et ouvrez le serveur de configuration.
5. Sur la page **Détails**, cliquez sur **Actualiser le serveur**.
6. Une fois l’actualisation du serveur terminée, sélectionnez le serveur vCenter pour ouvrir la page **Résumé** vCenter.
7. Sélectionnez le compte qui vient d’être ajouté dans le champ **vCenter server/vSphere host account** (Compte d’hôte vSphere/de serveur vCenter), puis cliquez sur le bouton **Enregistrer**.

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Supprimer un serveur vCenter

1. Sur le portail Azure, ouvrez votre coffre > **Site Recovery Infrastructure** (Infrastructure Site Recovery) > **Configuration Severs** (Serveurs de configuration) et ouvrez le serveur de configuration.
2. Sur la page **Détails**, sélectionnez le serveur vCenter.
3. Cliquez sur le bouton **Supprimer**.

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-vcenter-ip-address-and-port"></a>Modifier le port et l’adresse IP de vCenter

1. Connectez-vous au portail Azure.
2. Accédez à **Coffre Recovery Services** > **Infrastructure Site Recovery** > **Serveurs de Configuration**.
3. Cliquez sur le serveur de configuration auquel vCenter est affecté.
4. Dans la section **Serveurs vCenter**, cliquez sur le serveur vCenter que vous souhaitez modifier.
5. Dans la page récapitulative du serveur vCenter, mettez à jour l’adresse IP et le port dans les champs respectifs, puis enregistrez vos modifications.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

6. Afin que les modifications soient prises en compte, attendez 15 minutes ou [actualisez le serveur de configuration](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-protected-virtual-machines-to-a-new-vcenter"></a>Migrer toutes les machines virtuelles protégées vers un nouveau serveur vCenter

Pour migrer toutes les machines virtuelles sur le nouveau serveur vCenter, n’ajoutez pas d’autre compte vCenter. Des entrées en double peuvent en découler. Contentez-vous de mettre à jour l’adresse IP du nouveau serveur vCenter :

1. Connectez-vous au portail Azure.
2. Accédez à **Coffre Recovery Services** > **Infrastructure Site Recovery** > **Serveurs de Configuration**.
3. Cliquez sur le serveur de configuration auquel l’ancien serveur vCenter est affecté.
4. Dans la section **Serveurs vCenter**, cliquez sur le serveur vCenter à partir duquel vous prévoyez de procéder à la migration.
5. Dans la page récapitulative du serveur vCenter, mettez à jour l’adresse IP du nouveau serveur vCenter dans le champ **serveur vCenter/nom d’hôte ou adresse IP de vSphere**. Enregistrez vos modifications.

Aussitôt l’adresse IP mise à jour, les composants Site Recovery commencent à recevoir les informations de découverte des machines virtuelles du nouveau serveur vCenter. Les activités de réplication en cours ne s’en trouvent pas affectées.

## <a name="migrate-few-protected-virtual-machines-to-a-new-vcenter"></a>Migrer plusieurs machines virtuelles protégées vers un nouveau serveur vCenter

> [!NOTE]
> Cette section s’applique uniquement lorsque vous effectuez la migration de quelques machines virtuelles protégées vers un nouveau serveur vCenter. Si vous souhaitez protéger un nouvel ensemble de machines virtuelles à partir d’un nouveau serveur vCenter, [ajoutez des détails vCenter supplémentaires au serveur de configuration](#add-vmware-server-to-the-vault) et commencez par **[activer la protection](vmware-azure-tutorial.md#enable-replication)** .

Pour déplacer plusieurs machines virtuelles vers un nouveau serveur vCenter :

1. [Ajoutez les nouveaux détails vCenter au serveur de configuration](#add-vmware-server-to-the-vault).
2. [Désactivez la réplication des machines virtuelles](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) que vous prévoyez de migrer.
3. Effectuez la migration des machines virtuelles sélectionnées vers le nouveau serveur vCenter.
4. Protégez maintenant les machines virtuelles migrées en [ sélectionnant le nouveau serveur vCenter lorsque vous activez la protection](vmware-azure-tutorial.md#enable-replication).

> [!TIP]
> Si le nombre de machines virtuelles en cours de migration est **supérieur** au nombre de machines virtuelles conservées dans l’ancien vCenter, mettez à jour l’adresse IP du nouveau serveur vCenter en suivant les instructions fournies ici. Pour les quelques machines virtuelles qui sont conservées sur l’ancien serveur vCenter, [désactivez la réplication](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure), [ajoutez de nouveaux détails vCenter au serveur de configuration](#add-vmware-server-to-the-vault) et démarrez **[Activer la protection](vmware-azure-tutorial.md#enable-replication)** .

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

1. Y a-t-il une incidence sur la réplication si les machines virtuelles protégées sont déplacées entre des hôtes ESXi ?

    Non, cette opération n’affecte pas la réplication en cours. Toutefois, [prenez soin de déployer le serveur cible maître avec des privilèges suffisants](vmware-azure-reprotect.md#deploy-a-separate-master-target-server).

2. Quels sont les numéros de port utilisés pour la communication entre le serveur vCenter et d’autres composants Site Recovery ?

    Par défaut, il s’agit du port 443. Le serveur de configuration accède aux informations de l’hôte vCenter/vSphere via ce port. Si vous souhaitez mettre à jour ces informations, cliquez [ici](#modify-the-vcenter-ip-address-and-port).
