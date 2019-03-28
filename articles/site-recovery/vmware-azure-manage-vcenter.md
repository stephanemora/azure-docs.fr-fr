---
title: Gérer les serveurs VMWare vCenter pour la récupération après sinistre des machines virtuelles VMware sur Azure avec Azure Site Recovery | Microsoft Docs
description: Cet article décrit comment ajouter et gérer VMware vCenter pour la récupération après sinistre des machines virtuelles VMware sur Azure avec Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ramamill
ms.openlocfilehash: 9694c682f171ab715812b05fed2064c9bbcd36b3
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518080"
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

## <a name="modify-the-vcenter-ip-address-and-port"></a>Modifier l’adresse IP de vCenter et le port

1. Connectez-vous au portail Azure.
2. Accédez à **coffre Recovery Services** > **Infrastructure Site Recovery** > **serveurs de Configuration**.
3. Cliquez sur le serveur de configuration le vCenter est assigné à.
4. Dans le **serveurs vCenter** section, cliquez sur le serveur vCenter que vous souhaitez modifier.
5. Dans la page de résumé vCenter, mettre à jour l’adresse IP et le port du vCenter dans les champs respectifs, puis enregistrez vos modifications.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

6. Modifications entrent en vigueur, attendez 15 minutes ou [actualiser le serveur de configuration](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-protected-virtual-machines-to-a-new-vcenter"></a>Migrer des machines virtuelles protégées tout vers un nouveau serveur vCenter

Pour migrer tous les ordinateurs virtuels vers le nouveau serveur vCenter, n’ajoutez pas d’un autre compte vCenter. Cela peut entraîner d’entrées en double. Simplement mettre à jour l’adresse IP du nouveau vCenter :

1. Connectez-vous au portail Azure.
2. Accédez à **coffre Recovery Services** > **Infrastructure Site Recovery** > **serveurs de Configuration**.
3. Cliquez sur le serveur de configuration anciennes vCenter est assigné à.
4. Dans le **serveurs vCenter** section, cliquez sur le serveur vCenter que vous envisagez de migrer à partir de.
5. Dans la page de résumé vCenter, mettre à jour l’adresse IP du nouveau serveur vCenter dans le champ **vCenter server/vSphere hostname ou l’adresse IP**. Enregistrez vos modifications.

Dès que l’adresse IP est mise à jour, les composants de Site Recovery démarre recevoir des informations de découverte des machines virtuelles à partir du nouveau serveur vCenter. Cela n’affectera pas les activités de réplication en cours.

## <a name="migrate-few-protected-virtual-machines-to-a-new-vcenter"></a>Migrer plusieurs machines virtuelles protégées vers un nouveau serveur vCenter

> [!NOTE]
> Cette section s’applique uniquement lorsque vous effectuez une migration quelques-uns de vos machines virtuelles protégées à un nouveau serveur vCenter. Si vous souhaitez protéger un nouvel ensemble de machines virtuelles à partir d’un nouveau serveur vCenter, [ajouter de nouveaux détails de vCenter au serveur de configuration](#add-vmware-server-to-the-vault) et commencer par  **[activer la protection](vmware-azure-tutorial.md#enable-replication)**.

Pour déplacer plusieurs machines virtuelles vers un nouveau serveur vCenter :

1. [Ajouter les nouveaux détails de vCenter au serveur de configuration](#add-vmware-server-to-the-vault).
2. [Désactiver la réplication des machines virtuelles](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) vous envisagez de migrer.
3. Effectuer la migration de machines virtuelles sélectionnées vers le nouveau serveur vCenter.
4. À présent, protéger les ordinateurs virtuels migrés par [en sélectionnant le nouveau serveur vCenter lorsque vous activez la protection](vmware-azure-tutorial.md#enable-replication).

> [!TIP]
> Si le nombre de machines virtuelles en cours de migration est **supérieur** que le nombre de machines virtuelles conservées dans l’ancien vCenter, mettre à jour l’adresse IP du nouveau vCenter en suivant les instructions fournies ici. Pour les quelques ordinateurs virtuels qui sont conservés sur le serveur vCenter ancien, [désactiver la réplication](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure); [ajouter de nouveaux détails de vCenter au serveur de configuration](#add-vmware-server-to-the-vault)et démarrez  **[activer la protection](vmware-azure-tutorial.md#enable-replication)**.

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

1. Si les machines virtuelles protégées sont déplacés à partir d’un hôte ESXi à l’autre, il n’affectera la réplication ?

    Non, cela n’affectera pas la réplication en cours. Toutefois, [prenez soin de déployer le serveur cible maître avec des privilèges suffisants](vmware-azure-reprotect.md#deploy-a-separate-master-target-server)

2. Quels sont les numéros de port utilisé pour la communication entre le serveur vCenter et autres Site Recovery composants ?

    Le port par défaut est 443. Serveur de configuration accède aux informations de l’hôte vCenter/vSphere via ce port. Si vous souhaitez mettre à jour ces informations, cliquez sur [ici](#modify-the-vcenter-ip-address-and-port).
