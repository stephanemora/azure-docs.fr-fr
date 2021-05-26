---
title: Concepts - Identité et accès
description: Découvrir les concepts d’identité et d’accès de Azure VMware Solution
ms.topic: conceptual
ms.date: 05/13/2021
ms.openlocfilehash: d5d4b7f09df86315553e4716ef38cdbb67a883f4
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110089575"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Concepts d’identité d’Azure VMware Solution

Les clouds privés Azure VMware Solution sont approvisionnés avec vCenter Server et NSX-T Manager. Vous utilisez vCenter pour gérer les charges de travail des machines virtuelles, et NSX-T Manager pour gérer et étendre le cloud privé. La gestion des accès et des identités utilise le rôle CloudAdmin pour vCenter et des droits d’administrateur restreints pour NSX-T Manager. 

## <a name="vcenter-access-and-identity"></a>Identité et accès vCenter

Dans Azure VMware Solution, vCenter a un utilisateur local intégré appelé cloudadmin et affecté au rôle CloudAdmin. L’utilisateur local cloudadmin est utilisé pour configurer des utilisateurs dans Active Directory (AD). En général, le rôle CloudAdmin crée et gère les charges de travail dans votre cloud privé. Toutefois, dans Azure VMware Solution, le rôle CloudAdmin dispose de privilèges vCenter qui diffèrent d’autres solutions cloud VMware.     

- Dans un déploiement local vCenter et ESXi, l’administrateur a accès au compte administrateur vCenter\@vsphere.local. Il peut également avoir d’autres utilisateurs et groupes AD attribués. 

- Dans un déploiement Azure VMware Solution, l’administrateur n’a pas accès au compte d’utilisateur administrateur. Il peut cependant attribuer des utilisateurs et groupes AD au rôle CloudAdmin sur vCenter.  

L’utilisateur de cloud privé n’a pas accès et ne peut pas configurer de composants de gestion spécifiques pris en charge et gérés par Microsoft. Par exemple, les clusters, les hôtes, les magasins de données et les commutateurs virtuels distribués.

> [!IMPORTANT]
> Azure VMware Solution propose des rôles personnalisés sur vCenter, mais pas sur le portail Azure VMware Solution. Pour plus d’informations, consultez la section [Créer des rôles personnalisés sur vCenter](#create-custom-roles-on-vcenter) plus loin dans cet article. 

### <a name="view-the-vcenter-privileges"></a>Afficher les privilèges vCenter

Vous pouvez afficher les privilèges accordés au rôle CloudAdmin d’Azure VMware Solution sur votre instance vCenter de cloud privé Azure VMware Solution.

1. Connectez-vous au client vSphere et accédez à **Menu** > **Administration**.

1. Sous **Access Control**, sélectionnez **Rôles**.

1. Dans la liste des rôles, sélectionnez **CloudAdmin**, puis **Privilèges**. 

   :::image type="content" source="media/role-based-access-control-cloudadmin-privileges.png" alt-text="Comment afficher les privilèges du rôle CloudAdmin dans le client vSphere":::

Le rôle CloudAdmin dans Azure VMware Solution dispose des privilèges suivants sur vCenter. Pour plus d’informations, consultez la [documentation produit de VMware](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html).

| Privilège | Description |
| --------- | ----------- |
| **Alarmes** | Confirmer la réception de l’alerte<br />Créer une alerte<br />Désactiver l’action d’alarme<br />Modifier l’alarme<br />Supprimer l’alarme<br />Définir l’état de l’alarme |
| **Bibliothèque de contenu** | Ajouter un élément de bibliothèque<br />Créer un abonnement pour une bibliothèque publiée<br />Créer une bibliothèque locale<br />Créer une bibliothèque abonnée<br />Supprimer un élément de bibliothèque<br />Supprimer une bibliothèque locale<br />Supprimer une bibliothèque abonnée<br />Supprimer un abonnement pour une bibliothèque publiée<br />Télécharger les fichiers<br />Expulser des éléments de bibliothèque<br />Expulser une bibliothèque abonnée<br />Importer un stockage<br />Informations d’abonnement de probe<br />Publier un élément de bibliothèque à ses abonnés<br />Publier une bibliothèque à ses abonnés<br />Lire le stockage<br />Synchroniser un élément de bibliothèque<br />Synchroniser une bibliothèque abonnée<br />Introspection de type<br />Mettre à jour les paramètres de configuration du système<br />Mettre à jour les fichiers<br />Mettre à jour la bibliothèque<br />Mettre à jour un élément de bibliothèque<br />Mettre à jour une bibliothèque locale<br />Mettre à jour une bibliothèque abonnée<br />Mettre à jour l’abonnement d’une bibliothèque publiée<br />Afficher les paramètres de configuration |
| **Opérations de chiffrement** | Accès direct |
| **Magasins de données** | Allouer de l’espace<br />Parcourir les magasins de données<br />Configurer le magasin de données<br />Opérations de fichiers de bas niveau<br />Supprimer les fichiers<br />Mettre à jour les métadonnées de machine virtuelle |
| **Folder** | Créer un dossier<br />Supprimer un dossier<br />Déplacer le dossier<br />Renommer le dossier |
| **Global** | Annuler la tâche<br />Balise globale<br />Intégrité<br />Journaliser les événements<br />Gérer des attributs personnalisés<br />Gestionnaires d’appareils<br />Définir un attribut personnalisé<br />Balise système |
| **Hôte** | Réplication vSphere<br />&#160;&#160;&#160;&#160;Gérer la réplication |
| **Réseau** | Attribuer un réseau |
| **autorisations** | Modifier les autorisations<br />Modifier le rôle |
| **Profil** | Vue du stockage basé sur le profil |
| **Ressource** | Appliquer une recommandation<br />Attribuer vApp à une liste de ressources partagées<br />Attribuer une machine virtuelle à une liste de ressources partagées<br />Créer une liste de ressources partagées<br />Migrer une machine virtuelle hors tension<br />Migrer une machine virtuelle sous tension<br />Modifier la liste de ressources partagées<br />Déplacer la liste de ressources partagées<br />Interroger vMotion<br />Supprimer la liste de ressources partagées<br />Renommer la liste de ressources partagées |
| **Tâche planifiée** | Créer une tâche<br />Modifier la tâche<br />Supprimer la tâche<br />Exécuter la tâche |
| **Sessions** | Message<br />Valider la session |
| **Vue du stockage** | Affichage |
| **vApp** | Ajouter une machine virtuelle<br />Attribuer une liste de ressources partagées<br />Attribuer vApp<br />Clone<br />Créer<br />DELETE<br />Exporter<br />Importer<br />Déplacer<br />Mise hors tension<br />Mise sous tension<br />Renommer<br />Interrompre<br />Unregister<br />Afficher l’environnement OVF<br />Configuration de l’application vApp<br />Configuration de l'instance vApp<br />Configuration de vApp managedBy<br />Configuration de ressource vApp |
| **Machine virtuelle** | Changer la configuration<br />&#160;&#160;&#160;&#160;Acquérir le bail du disque<br />&#160;&#160;&#160;&#160;Ajouter un disque existant<br />&#160;&#160;&#160;&#160;Ajouter un nouveau disque<br />&#160;&#160;&#160;&#160;Ajouter ou supprimer un appareil<br />&#160;&#160;&#160;&#160;Configuration avancée<br />&#160;&#160;&#160;&#160;Modifier le nombre d’UC<br />&#160;&#160;&#160;&#160;Modifier la mémoire<br />&#160;&#160;&#160;&#160;Modifier les paramètres<br />&#160;&#160;&#160;&#160;Modifier le positionnement du fichier d’échange<br />&#160;&#160;&#160;&#160;Modifier la ressource<br />&#160;&#160;&#160;&#160;Configurer un périphérique USB hôte<br />&#160;&#160;&#160;&#160;Configurer un périphérique brut<br />&#160;&#160;&#160;&#160;Configurer managedBy<br />&#160;&#160;&#160;&#160;Afficher les paramètres de connexion<br />&#160;&#160;&#160;&#160;Étendre le disque virtuel<br />&#160;&#160;&#160;&#160;Modifier les paramètres de l’appareil<br />&#160;&#160;&#160;&#160;Interroger la compatibilité de la tolérance de pannes<br />&#160;&#160;&#160;&#160;Interroger les fichiers sans propriétaire<br />&#160;&#160;&#160;&#160;Recharger à partir de chemins d’accès<br />&#160;&#160;&#160;&#160;Retirer un disque<br />&#160;&#160;&#160;&#160;Renommer<br />&#160;&#160;&#160;&#160;Réinitialiser les informations de l’invité<br />&#160;&#160;&#160;&#160;Définir l’annotation<br />&#160;&#160;&#160;&#160;Basculer le suivi des modifications du disque<br />&#160;&#160;&#160;&#160;Basculer le parent fourche<br />&#160;&#160;&#160;&#160;Mettre à niveau la compatibilité de la machine virtuelle<br />Modifier l’inventaire<br />&#160;&#160;&#160;&#160;Créer à partir de ce qui existe<br />&#160;&#160;&#160;&#160;Créer<br />&#160;&#160;&#160;&#160;Déplacer<br />&#160;&#160;&#160;&#160;Inscrire<br />&#160;&#160;&#160;&#160;Supprimer<br />&#160;&#160;&#160;&#160;Annuler l’inscription<br />Opérations de l’invité<br />&#160;&#160;&#160;&#160;Modification des alias d’opération invité<br />&#160;&#160;&#160;&#160;Requête d’alias d'opération invité<br />&#160;&#160;&#160;&#160;Modification d’opération invité<br />&#160;&#160;&#160;&#160;Exécution du programme d’opération invité<br />&#160;&#160;&#160;&#160;Requêtes d’opération invité<br />Interaction<br />&#160;&#160;&#160;&#160;Répondre à la question<br />&#160;&#160;&#160;&#160;Opération de sauvegarde de machines virtuelles<br />&#160;&#160;&#160;&#160;Configurer un média CD<br />&#160;&#160;&#160;&#160;Configurer une disquette<br />&#160;&#160;&#160;&#160;Connecter des appareils<br />&#160;&#160;&#160;&#160;Interaction de la console<br />&#160;&#160;&#160;&#160;Créer une capture d’écran<br />&#160;&#160;&#160;&#160;Défragmenter tous les disques<br />&#160;&#160;&#160;&#160;Glisser-déposer<br />&#160;&#160;&#160;&#160;Gestion du système d’exploitation invité par API VIX<br />&#160;&#160;&#160;&#160;Injecter les codes d’analyse USB HID<br />&#160;&#160;&#160;&#160;Installer les outils VMware<br />&#160;&#160;&#160;&#160;Suspendre ou annuler la suspension<br />&#160;&#160;&#160;&#160;Opérations de réinitialisation ou de réduction<br />&#160;&#160;&#160;&#160;Mise hors tension<br />&#160;&#160;&#160;&#160;Allumer<br />&#160;&#160;&#160;&#160;Enregistrer la session sur la machine virtuelle<br />&#160;&#160;&#160;&#160;Relire la session sur la machine virtuelle<br />&#160;&#160;&#160;&#160;Suspendre<br />&#160;&#160;&#160;&#160;Suspendre la tolérance de panne<br />&#160;&#160;&#160;&#160;Tester le basculement<br />&#160;&#160;&#160;&#160;Redémarrage du test de la machine virtuelle secondaire<br />&#160;&#160;&#160;&#160;Désactiver la tolérance de panne<br />&#160;&#160;&#160;&#160;Activer la tolérance de panne<br />Approvisionnement<br />&#160;&#160;&#160;&#160;Autoriser l’accès au disque<br />&#160;&#160;&#160;&#160;Autoriser l’accès au fichier<br />&#160;&#160;&#160;&#160;Autoriser l’accès au disque en lecture seule<br />&#160;&#160;&#160;&#160;Autoriser le téléchargement de machines virtuelles<br />&#160;&#160;&#160;&#160;Cloner le modèle<br />&#160;&#160;&#160;&#160;Cloner la machine virtuelle<br />&#160;&#160;&#160;&#160;Créer un modèle à partir de la machine virtuelle<br />&#160;&#160;&#160;&#160;Personnaliser l’invité<br />&#160;&#160;&#160;&#160;Déployer un modèle<br />&#160;&#160;&#160;&#160;Marquer comme modèle<br />&#160;&#160;&#160;&#160;Modifier la spécification de personnalisation<br />&#160;&#160;&#160;&#160;Promouvoir les disques<br />&#160;&#160;&#160;&#160;Lire les spécifications de personnalisation<br />Configuration de service<br />&#160;&#160;&#160;&#160;Autoriser les notifications<br />&#160;&#160;&#160;&#160;Autoriser l’interrogation des notifications d’événements globales<br />&#160;&#160;&#160;&#160;Gérer la configuration du service<br />&#160;&#160;&#160;&#160;Modifier la configuration du service<br />&#160;&#160;&#160;&#160;Interroger les configurations de service<br />&#160;&#160;&#160;&#160;Lire la configuration du service<br />Gestion des instantanés<br />&#160;&#160;&#160;&#160;Créer une capture instantanée<br />&#160;&#160;&#160;&#160;Supprimer une capture instantanée<br />&#160;&#160;&#160;&#160;Renommer une capture instantanée<br />&#160;&#160;&#160;&#160;Rétablir une capture instantanée<br />Réplication vSphere<br />&#160;&#160;&#160;&#160;Configurer la réplication<br />&#160;&#160;&#160;&#160;Gérer la réplication<br />&#160;&#160;&#160;&#160;Surveiller la réplication |
| **vService** | Créer une dépendance<br />Détruire la dépendance<br />Reconfigurer la configuration de la dépendance<br />Mettre à jour la dépendance |
| **Balisage vSphere** | Attribuer ou annuler l’attribution d’une balise vSphere<br />Créer une balise vSphere<br />Créer une catégorie de balise vSphere<br />Supprimer une balise vSphere<br />Supprimer une catégorie de balise vSphere<br />Modifier une balise vSphere<br />Modifier une catégorie de balise vSphere<br />Modifier le champ UsedBy pour la catégorie<br />Modifier le champ UsedBy pour la balise |

### <a name="create-custom-roles-on-vcenter"></a>Créer des rôles personnalisés sur vCenter

Azure VMware Solution prend en charge l’utilisation de rôles personnalisés avec des privilèges égaux ou inférieurs à ceux du rôle CloudAdmin. 

Vous allez utiliser le rôle CloudAdmin pour créer, modifier ou supprimer des rôles personnalisés avec des privilèges inférieurs ou égaux à leur rôle actuel. Vous pouvez créer des rôles avec des privilèges supérieurs à CloudAdmin, mais vous ne pouvez pas attribuer le rôle à des utilisateurs ou à des groupes, ni le supprimer.

Pour empêcher la création de rôles qui ne peuvent pas être attribués ou supprimés, clonez le rôle CloudAdmin comme base pour la création d’autres rôles personnalisés.

#### <a name="create-a-custom-role"></a>Créer un rôle personnalisé
1. Connectez-vous à vCenter avec cloudadmin\@vsphere.local ou un utilisateur avec le rôle CloudAdmin.

1. Accédez à la section de configuration **Rôles** et sélectionnez **Menu** > **Administration** > **Contrôle d’accès** > **Rôles**.

1. Sélectionnez le rôle **CloudAdmin** et l’icône d’action **Cloner le rôle**.

   >[!NOTE] 
   >Ne clonez pas le rôle **Administrateur**, car vous ne pouvez pas l’utiliser. En outre, le rôle personnalisé créé ne peut pas être supprimé par cloudadmin\@vsphere.local.

1. Indiquez le nom de votre choix pour le rôle cloné.

1. Ajoutez ou supprimez des privilèges pour le rôle, puis sélectionnez **OK**. Le rôle cloné est visible dans la liste **Rôles**.


#### <a name="apply-a-custom-role"></a>Appliquer un rôle personnalisé

1. Accédez à l’objet qui requiert l’autorisation ajoutée. Par exemple, pour appliquer l’autorisation à un dossier, accédez à **Menu** > **Ordinateurs virtuels et modèles** > **Nom du dossier**.

1. Cliquez avec le bouton droit sur l’objet et sélectionnez **Ajouter une autorisation**.

1. Dans la fenêtre **Ajouter une autorisation**, sélectionnez la source d’identité dans la liste déroulante **Utilisateur** où se trouve le groupe ou l’utilisateur.

1. Recherchez l’utilisateur ou le groupe après avoir sélectionné la source d’identité dans la section **Utilisateur**. 

1. Sélectionnez le rôle qui sera appliqué à l’utilisateur ou au groupe.

1. Activez la case à cocher **Se propager aux enfants** si nécessaire, puis sélectionnez **OK**. L’autorisation ajoutée s’affiche dans la section **Autorisations**.

## <a name="nsx-t-manager-access-and-identity"></a>Identité et accès du gestionnaire NSX-T

>[!NOTE]
>NSX-T 2.5 est actuellement pris en charge pour tous les nouveaux clouds privés.

Utilisez le compte *Administrateur* pour accéder à NSX-T Manager. Ce compte dispose de privilèges complets et vous permet de créer et de gérer des passerelles de niveau 1 (T1), des segments (commutateurs logiques) et tous les services. Les privilèges vous permettent d’accéder à la passerelle de niveau 0 (T0) de NSX-T. Une modification apportée à la passerelle T0 peut entraîner une dégradation des performances réseau ou une perte de l’accès à un cloud privé. Ouvrez une demande de support dans le portail Azure pour demander toute modification de votre passerelle NSX-T T0.

 
## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez abordé les concepts d’accès et d’identité d’Azure VMware Solution, vous pouvez en apprendre davantage sur les sujets suivants :

- [Comment activer la ressource Azure VMware Solution](deploy-azure-vmware-solution.md#step-1-register-the-microsoftavs-resource-provider)
- [Détails de chaque privilège](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html)
- [Surveillance et réparation des clouds privés Azure VMware Solution](/azure/azure-vmware/concepts-private-clouds-clusters#host-monitoring-and-remediation)



<!-- LINKS - external-->
[VMware product documentation]: https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-private-clouds-clusters#host-maintenance-and-lifecycle-management
