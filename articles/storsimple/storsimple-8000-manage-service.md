---
title: Déployer le service StorSimple Device Manager dans Azure | Microsoft Docs
description: En savoir plus sur les étapes requises pour la création, la suppression, la migration du service et la gestion de la clé d’inscription au service.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 85d66b5e4f7e95d114cbf0b4c681d73eebb93a67
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076562"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>Déployer le service StorSimple Device Manager pour les appareils de la gamme StorSimple 8000

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Vue d’ensemble

Le service StorSimple Device Manager s’exécute dans Microsoft Azure et se connecte à plusieurs appareils StorSimple. Après avoir créé le service, vous pouvez l’utiliser pour gérer tous les appareils qui y sont connectés à partir d’un emplacement central unique, ce qui réduit la charge administrative.

Ce didacticiel décrit les étapes requises pour la création, la suppression, la migration du service et la gestion de la clé d’inscription du service. Les informations contenues dans cet article s’appliquent uniquement aux appareils de la gamme StorSimple 8000. Pour plus d’informations sur StorSimple Virtual Arrays, accédez à [Déployer le service StorSimple Device Manager pour StorSimple Virtual Array](storsimple-virtual-array-manage-service.md).

> [!NOTE]
> -  Le portail Azure prend en charge les appareils exécutant Update 5.0 ou version ultérieure. Si votre appareil n’est pas à jour, installez la mise à jour Update 5.0 immédiatement. Pour plus d’informations, accédez à [Installer Update 5](storsimple-8000-install-update-5.md). 
> - Si vous utilisez une appliance StorSimple Cloud Appliance (8010/8020), vous ne pouvez pas mettre à jour une appliance cloud. Utilisez la version la plus récente du logiciel pour créer une appliance cloud avec Update 5.0 et basculez ensuite vers la nouvelle appliance cloud créée. 
> - Tous les appareils exécutant la mise à jour Update 4.0 ou une version antérieure auront des fonctionnalités de gestion réduites. 

## <a name="create-a-service"></a>Créer un service
Pour créer un service StorSimple Device Manager, vous avez besoin des éléments suivants :

* Un abonnement avec un contrat Entreprise
* Un compte de stockage Microsoft Azure actif
* Les informations de facturation utilisées pour la gestion des accès

Seuls les abonnements avec un contrat Entreprise sont autorisés. Vous pouvez également choisir de générer un compte de stockage par défaut lorsque vous créez le service.

Un seul service peut gérer plusieurs appareils. Cependant, un appareil ne peut pas couvrir plusieurs services. Une grande entreprise peut avoir plusieurs instances de service pour utiliser différents abonnements, organisations ou même emplacements de déploiement. 

> [!NOTE]
> Vous devez créer des instances distinctes du service StorSimple Device Manager pour gérer les appareils de la gamme StorSimple 8000 et les baies StorSimple Virtual Array.

Procédez comme suit pour créer un service.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Pour chaque service StorSimple Device Manager, les attributs suivants existent :

* **Nom** : le nom assigné à votre service StorSimple Device Manager lors de sa création. **Impossible de modifier le nom du service une fois que le service a été créé. Cela vaut également pour les autres entités telles que les appareils, les volumes, les conteneurs de volumes et les stratégies de sauvegarde qui ne peuvent pas être renommés dans le portail Azure.**
* **État** : l’état du service, qui peut être **Actif**, **Création en cours** ou **En ligne**.
* **Emplacement** : l’emplacement géographique sur lequel l’appareil StorSimple sera déployé.
* **Abonnement** : l’abonnement de facturation associé à votre service.

## <a name="delete-a-service"></a>Supprimer un service

Avant de supprimer un service, assurez-vous qu’aucun appareil connecté ne l’utilise. Si le service est en cours d’utilisation, désactivez les appareils connectés. L’opération de désactivation rompt la connexion entre l’appareil et le service, mais conserve les données de l’appareil dans le cloud.

> [!IMPORTANT]
> Après qu’un service a été supprimé, l’opération ne peut pas être annulée. Un appareil qui utilisait le service doit être réinitialisé aux paramètres d’usine par défaut avant de pouvoir être utilisé avec un autre service. Dans ce scénario, les données locales de l’appareil, ainsi que la configuration, sont perdues.

Pour supprimer un service, procédez comme suit.

### <a name="to-delete-a-service"></a>Pour supprimer un service

1. Recherchez le service que vous souhaitez supprimer. Cliquez sur l’icône **Ressources** et entrez les termes appropriés à rechercher. Dans les résultats de la recherche, cliquez sur le service que vous souhaitez supprimer.

    ![Rechercher le service à supprimer](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Cela vous renvoie vers le panneau du service StorSimple Device Manager. Cliquez sur **Supprimer**.

    ![Suppression du service](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. Cliquez sur **Oui** dans la notification de confirmation. La suppression du service peut nécessiter quelques minutes.

    ![Confirmer la suppression](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Obtenir la clé d’inscription du service

Une fois que vous avez créé un service, vous devez inscrire votre appareil StorSimple auprès du service. Pour inscrire votre premier appareil StorSimple, vous avez besoin de la clé d’inscription du service. Pour inscrire des appareils supplémentaires auprès d’un service StorSimple existant, vous avez besoin de la clé d’inscription et de la clé de chiffrement des données de service (générée sur le premier appareil lors de l’inscription). Pour plus d’informations sur la clé de chiffrement des données du service, consultez la rubrique [Sécurité StorSimple](storsimple-8000-security.md). Vous pouvez obtenir la clé d’inscription en accédant à **Clés** sur le panneau de votre service StorSimple Device Manager.

Procédez comme suit pour obtenir la clé d’inscription du service.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

Conservez la clé d’inscription du service dans un emplacement sûr. Vous aurez besoin de cette clé, ainsi que de la clé de chiffrement des données du service, pour enregistrer des appareils supplémentaires auprès du service. Après avoir obtenu la clé d’inscription du service, vous devez configurer votre appareil via l’interface Windows PowerShell pour StorSimple.

Pour plus d’informations sur la façon d’utiliser cette clé d’inscription, consultez l’[étape 3 : Configuration et inscription de l’appareil via Windows PowerShell pour StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Régénérer la clé d’inscription du service
Vous devez régénérer une clé d’inscription du service si vous êtes amené à effectuer une rotation des clés ou si la liste des administrateurs de services fédérés a changé. Lorsque vous régénérez la clé, la nouvelle clé est utilisée uniquement pour l’enregistrement des appareils suivants. Les appareils déjà enregistrés ne sont pas affectés par ce processus.

Procédez comme suit pour régénérer une clé d’inscription du service.

### <a name="to-regenerate-the-service-registration-key"></a>Pour régénérer la clé d’inscription du service
1. Dans le panneau **StorSimple Device Manager**, accédez à **Gestion &gt;** **Clés**.
    
    ![Accédez au panneau Clés](./media/storsimple-8000-manage-service/regenregkey2.png)

2. Dans le panneau **Clés**, cliquez sur **Régénérer**.

    ![Cliquer sur Régénérer](./media/storsimple-8000-manage-service/regenregkey3.png)
3. Dans le panneau **Régénérer la clé d’inscription du service**, vérifiez l’action requise lors de la régénération des clés. Tous les appareils qui seront inscrits auprès de ce service par la suite utilisent la nouvelle clé d’inscription. Cliquez sur **Régénérer** pour confirmer l’opération. Une fois la régénération terminée, vous en êtes informé.

    ![Confirmer la régénération](./media/storsimple-8000-manage-service/regenregkey4.png)

4. Une nouvelle clé d’inscription du service s’affiche.

5. Copiez cette clé et sauvegardez-la pour enregistrer tout nouvel appareil auprès de ce service.



## <a name="change-the-service-data-encryption-key"></a>Modifier la clé de chiffrement des données de service
[!INCLUDE [storage-files-migration-generate-key](../../includes/storage-files-migration-generate-key.md)]

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Opérations prises en charge sur les appareils exécutant des versions antérieures à Update 5.0
Seuls les appareils StorSimple exécutant Update 5.0 et des versions ultérieures sont pris en charge dans le portail Azure. La prise en charge des appareils exécutant des versions antérieures est limitée. Une fois que vous avez effectué la migration vers le portail Azure, utilisez le tableau suivant pour connaître les opérations prises en charge sur les appareils exécutant des versions antérieures à Update 5.0.

| Opération                                                                                                                       | Prise en charge      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Inscrire un appareil                                                                                                               | Oui            |
| Configurer les paramètres d’un appareil, tels que les paramètres généraux, réseau et de sécurité                                                                | Oui            |
| Analyser, télécharger et installer des mises à jour                                                                                             | Oui            |
| Désactiver un appareil                                                                                                               | Oui            |
| Supprimer un appareil                                                                                                                   | Oui            |
| Créer, modifier et supprimer un conteneur de volumes                                                                                   | Non             |
| Créer, modifier et supprimer un volume                                                                                             | Non             |
| Créer, modifier et supprimer une stratégie de sauvegarde                                                                                      | Non             |
| Exécuter une sauvegarde manuelle                                                                                                            | Non             |
| Exécuter une sauvegarde planifiée                                                                                                         | Non applicable |
| Restaurer à partir d’un jeu de sauvegarde                                                                                                        | Non             |
| Cloner vers un appareil exécutant Update 3.0 et versions ultérieures <br> L’appareil source exécute une version antérieure à Update 3.0.                                | Oui            |
| Cloner vers un appareil exécutant des versions antérieures à Update 3.0                                                                          | Non             |
| Basculer en tant qu’appareil source <br> (à partir d’un appareil exécutant une version antérieure à Update 3.0 vers un appareil exécutant Update 3.0 et versions ultérieures)                                                               | Oui            |
| Basculer en tant qu’appareil cible <br> (vers un appareil exécutant une version logicielle antérieure à Update 3.0)                                                                                   | Non             |
| Supprimer une alerte                                                                                                                  | Oui            |
| Afficher les stratégies de sauvegarde, le catalogue de sauvegarde, les volumes, les conteneurs de volumes, les graphiques de surveillance, les travaux et les alertes créés dans le portail classique | Oui            |
| Activer et désactiver des contrôleurs d’appareils                                                                                              | Oui            |


## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur le [processus de déploiement StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* En savoir plus sur la [gestion de votre compte de stockage StorSimple](storsimple-8000-manage-storage-accounts.md).
* En savoir plus sur [l’utilisation du service StorSimple Device Manager pour gérer votre appareil StorSimple](storsimple-8000-manager-service-administration.md).
