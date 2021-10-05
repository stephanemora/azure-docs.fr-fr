---
title: Installer VMware HCX dans Azure VMware Solution
description: Installez VMware HCX dans votre cloud privé Azure VMware Solution.
ms.topic: how-to
ms.date: 09/16/2021
ms.openlocfilehash: e3db000661ea310c35d32d988db5cd2001290004
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600814"
---
# <a name="install-and-activate-vmware-hcx-in-azure-vmware-solution"></a>Installer et activer VMware HCX dans Azure VMware Solution

VMware HCX Advanced et son gestionnaire de cloud associé ne sont plus prédéployés dans Azure VMware Solution. Au lieu de cela, vous l'installerez via le portail Azure en tant que module complémentaire. Vous allez toujours télécharger le fichier OVA du connecteur HCX et déployer l’appliance virtuelle sur votre vCenter local. 

Toute édition de VMware HCX prend en charge 25 paires de sites (sur site vers le cloud ou de cloud à cloud).  La valeur par défaut est HCX Advanced, mais vous pouvez ouvrir une [demande d'assistance](https://rc.portal.azure.com/#create/Microsoft.Support) pour activer HCX Enterprise Edition, qui est actuellement en phase d'essai public. Une fois le service mis à la disposition générale, vous disposerez de 30 jours pour décider de vos prochaines étapes. Vous pouvez également désactiver ou refuser le service HCX Enterprise Edition mais conserver HCX Advanced car il fait partie du coût du nœud.

La rétrogradation de HCX Édition Entreprise vers HCX Advanced est possible sans redéploiement. Tout d’abord, assurez-vous que vous avez rétabli un état de configuration HCX Advanced et que vous n’utilisez pas les fonctionnalités Enterprise. Si vous prévoyez de passer à une version inférieure, assurez-vous qu'aucune migration n'est prévue, que des fonctions telles que RAV et [HCX Mobility Optimized Networking (MON)](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-0E254D74-60A9-479C-825D-F373C41F40BC.html) ne sont pas utilisées et que les paires de sites sont au nombre de trois ou moins.

>[!TIP]
>Vous pouvez également [désinstaller HCX Advanced](#uninstall-hcx-advanced) via le portail. Lorsque vous désinstallez HCX Advanced, assurez-vous que vous n’avez pas de migrations actives en cours. La suppression de HCX Advanced renvoie les ressources vers votre cloud privé occupé par les appliances virtuelles HCX.

Dans ce guide pratique, vous allez :

* Installer HCX Advanced via le Portail Azure
* Télécharger et déployer le fichier OVA du connecteur VMware HCX
* Activer HCX Advanced avec une clé de licence


Une fois que vous avez terminé, suivez les étapes suivantes recommandées à la fin pour continuer avec les étapes de ce guide de démarrage.

## <a name="prerequisites"></a>Configuration requise

- [Préparer les installations HCX](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-A631101E-8564-4173-8442-1D294B731CEB.html)

- Si vous envisagez d’utiliser VMware HCX Enterprise, vérifiez que vous avez activé le module complémentaire [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) par le biais d’une [demande de support](https://portal.azure.com/#create/Microsoft.Support). Il s'agit d'un essai gratuit de 12 mois dans Azure VMware Solution.

- [Série de blogs VMware – Migration vers le cloud](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html)


## <a name="install-vmware-hcx-advanced"></a>Installer VMware HCX Advanced

1. Dans votre cloud privé Azure VMware Solution, sélectionnez **Gérer** > **Modules complémentaires**.

1. Sélectionnez **Démarrage** pour **Mobilité des charges de travail HCX**.

   :::image type="content" source="media/tutorial-vmware-hcx/deployed-hcx-migration-get-started.png" alt-text="Capture d’écran montrant le bouton de démarrage pour la mobilité des charges de travail HCX.":::

1. Cochez la case **J’accepte les conditions générales ci-dessus**, puis sélectionnez **Installer**.

   L’installation de HCX Advanced et la configuration du gestionnaire cloud prennent environ 35 minutes. Une fois installé, l’URL HCX Manager et les clés HCX requises pour le jumelage de sites de connecteur locaux HCX s’affichent dans l’onglet **Migration à l’aide de HCX**.

   :::image type="content" source="media/tutorial-vmware-hcx/deployed-hcx-migration-using-hcx-tab.png" alt-text="Capture d’écran montrant la migration à l’aide de l’onglet HCX sous Connectivité.":::


## <a name="download-and-deploy-the-vmware-hcx-connector-ova"></a>Télécharger et déployer le fichier OVA du connecteur VMware HCX 

Au cours de cette étape, vous allez télécharger le fichier OVA du connecteur VMware HCX, puis vous allez déployer le connecteur VMware HCX sur votre vCenter local.

1. Ouvrez une fenêtre de navigateur, connectez-vous au Gestionnaire HCX d’Azure VMware Solution sur le port `https://x.x.x.9` 443, avec les informations d’identification de l’utilisateur **cloudadmin\@vsphere.local**.

1. Sous **Administration** > **Mises à jour système**, sélectionnez **Demander le lien de téléchargement**. Si la zone est grisée, attendez quelques secondes qu’elle génère un lien.

1. Téléchargez ou recevez un lien pour le fichier OVA du connecteur VMware HCX que vous déployez sur votre vCenter local.

1. Dans votre vCenter local, sélectionnez un [modèle OVF](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) pour déployer le connecteur VMware HCX sur votre vCenter local.

1. Accédez au fichier OVA que vous avez téléchargé, sélectionnez-le, puis sélectionnez **Ouvrir**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Capture d’écran de navigation vers un modèle OVF." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::

1. Sélectionnez un nom et un emplacement, puis sélectionnez une ressource ou un cluster où déployer le connecteur VMware HCX. Ensuite, consultez les détails et les ressources nécessaires, puis sélectionnez **Next** (Suivant).

1. Passez en revue les termes du contrat de licence, sélectionnez le stockage et le réseau requis, puis sélectionnez **Suivant**.

1. Sélectionnez le [segment de réseau de gestion VMware HCX](plan-private-cloud-deployment.md#define-vmware-hcx-network-segments) que vous avez défini lors de l’état de planification. Sélectionnez ensuite **Suivant**.  

1. Dans **Customize template** (Personnaliser le modèle), entrez toutes les informations demandées, puis sélectionnez **Next**.

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Capture d’écran des zones de personnalisation d’un modèle." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Vérifiez, puis sélectionnez **Finish** (Terminer) pour déployer le fichier OVA du connecteur VMware HCX.

   >[!IMPORTANT]
   >Vous devrez allumer manuellement l’appliance virtuelle.  Après la mise sous tension, patientez 10 à 15 minutes avant de passer à l’étape suivante.


## <a name="activate-vmware-hcx"></a>Activer VMware HCX

Une fois que vous avez déployé le fichier OVA du connecteur VMware HCX en local et que vous avez démarré l’appliance, vous êtes prêt à l’activer. Tout d’abord, vous devez obtenir une clé de licence à partir du portail Azure VMware Solution, puis vous allez l’activer dans VMware HCX Manager. Enfin, vous aurez besoin d’une clé pour chaque connecteur HCX local déployé.

1. Dans votre cloud privé Azure VMware Solution, sélectionnez **Gérer** > **Modules complémentaires** > **Migration au moyen de HCX**. Copiez ensuite la **clé d’activation**.

   :::image type="content" source="media/tutorial-vmware-hcx/hcx-activation-key.png" alt-text="Capture d’écran montrant la clé d’activation.":::   

1. Connectez-vous à VMware HCX Manager en local sur `https://HCXManagerIP:9443` avec les informations d’identification de l’**administrateur**.  Veillez à inclure le numéro de port `9443` avec l’adresse IP de VMware HCX Manager.

   >[!TIP]
   >Vous avez défini le mot de passe de l’utilisateur **administrateur** lors du déploiement du fichier OVA du Gestionnaire VMware HCX.

1. Dans **Licensing** (Gestion des licences), entrez votre clé pour **HCX Advanced Key** (Clé HCX Advanced) et sélectionnez **Activate**.

    >[!IMPORTANT]
    >VMware HCX Manager doit avoir un accès Internet ouvert ou un proxy configuré.

1. Dans **Datacenter Location** (Emplacement du centre de données), indiquez l’emplacement le plus proche pour l’installation du Gestionnaire de clouds VMware HCX en local. Sélectionnez **Continuer**.

1. Dans **System Name** (Nom du système), modifiez ou acceptez le nom par défaut et sélectionnez **Continue**.

1. Sélectionnez **Yes, Continue** (Oui, continuer).

1. Dans **Connecter votre vCenter**, fournissez le nom de domaine complet ou l’adresse IP de votre serveur vCenter, ainsi que les informations d’identification appropriées, puis sélectionnez **Continuer**.

   >[!TIP]
   >Le serveur vCenter est l’emplacement où vous avez déployé le connecteur VMware HCX dans votre centre de données.

1. Dans **Configurer SSO/PSC**, indiquez le nom de domaine complet ou l’adresse IP de votre instance Platform Services Controller (PSC), et sélectionnez **Continuer**.

   >[!NOTE]
   >En règle générale, il s’agit du nom de domaine complet ou de l’adresse IP de votre vCenter.

1. Vérifiez que les informations indiquées sont correctes, puis sélectionnez **Restart** (Redémarrer).

   >[!NOTE]
   >Un certain délai s’écoulera après le redémarrage avant que vous soyez invité à passer à l’étape suivante.

Après le redémarrage des services, vous devez voir l’état de vCenter apparaître en vert dans l’écran qui s’affiche. VCenter et SSO doivent être dotés des paramètres de configuration appropriés : ceux-ci doivent être identiques à ceux de l’écran précédent.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Capture d’écran du tableau de bord avec l’état vCenter vert." lightbox="media/tutorial-vmware-hcx/activation-done.png":::


## <a name="uninstall-hcx-advanced"></a>Désinstaller HCX Advanced

Vous pouvez désinstaller HCX Advanced via le portail, ce qui supprime le jumelage et le logiciel existants. 

>[!NOTE]
>Le retour des ressources dans votre cloud privé occupé par les appliances virtuelles HCX peut prendre environ 30 minutes. 

1. Assurez-vous que vous n’avez pas de migrations actives en cours.

1. Assurez-vous que les extensions L2 ne sont plus nécessaires ou que les réseaux ont été « non étendus » vers la destination. 

1. Pour les charges de travail qui utilisent MON, vérifiez que vous avez supprimé les passerelles par défaut. Dans le cas contraire, les charges de travail peuvent ne pas être en mesure de communiquer ou de fonctionner.  

1. Dans votre cloud privé Azure VMware Solution, sélectionnez **Gérer** > **Modules complémentaires**.

1. Sélectionnez **Démarrage** pour **Mobilité des charges de travail HCX**, puis sélectionnez **Désinstaller**.
   
1. Entrez **Oui** pour confirmer la désinstallation.

À ce stade, HCX Advanced n’a plus le plug-in vCenter et, si nécessaire, vous pouvez le réinstaller à tout moment.


## <a name="next-steps"></a>Étapes suivantes

Passez au didacticiel suivant pour configurer le connecteur VMware HCX.  Une fois que vous avez configuré le connecteur VMware HCX, vous disposez d’un environnement prêt pour la production, pour la création de machines virtuelles et pour la migration. 


>[!div class="nextstepaction"]
>[Configurer VMware HCX dans Azure VMware Solution](configure-vmware-hcx.md)
