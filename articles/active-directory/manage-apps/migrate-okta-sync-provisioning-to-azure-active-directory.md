---
title: 'Didacticiel : Migrer le provisionnement de synchronisation Okta vers la synchronisation basée sur Azure AD Connect'
titleSuffix: Active Directory
description: Découvrez comment migrer le provisionnement de synchronisation Okta vers la synchronisation basée sur Azure AD Connect
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: d70d3f32093fc2e509d351e0ce194bac1d7490b8
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129084148"
---
# <a name="tutorial-migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization"></a>Didacticiel : Migrer le provisionnement de synchronisation Okta vers la synchronisation basée sur Azure Active Directory Connect

Cet article guide les organisations qui utilisent actuellement l’approvisionnement d’utilisateurs de Okta à Azure Active Directory (Azure AD), pour migrer la synchronisation utilisateur ou la synchronisation universelle avec Azure AD Connect. Cela permet d’effectuer un approvisionnement supplémentaire en Azure AD et Office 365.

La migration des plateformes de synchronisation n’est pas une modification minime. Chaque étape du processus mentionné dans cet article doit être validée par rapport à votre propre environnement avant de supprimer le Azure AD Connect du mode de préproduction ou d’activer l’agent d’approvisionnement cloud Azure AD.

## <a name="prerequisites"></a>Prérequis

Lors du passage de l’approvisionnement Okta à Azure AD, les clients ont deux possibilités : le serveur Azure AD Connect ou la configuration cloud Azure AD. Nous vous recommandons de lire l'[article de comparaison complet de Microsoft](../cloud-sync/what-is-cloud-sync.md#comparison-between-azure-ad-connect-and-cloud-sync) afin de comprendre les différences entre les deux produits.

L’approvisionnement Cloud Azure AD est le chemin de migration le plus familier pour les clients Okta utilisant Universal ou User Sync. Les agents d’approvisionnement Cloud sont légers et peuvent être installés sur des contrôleurs de domaine ou à proximité tels que les agents de synchronisation de référentiel Okta. Il n’est pas recommandé de les installer sur le même serveur.

Le serveur Azure AD Connect doit être choisi si votre organisation doit tirer parti des technologies suivantes lors de la synchronisation des utilisateurs.

- Synchronisation des appareils-joindre Azure AD Hybride ou Hello entreprise

- Authentification directe

- Prise en charge de plus de 150k objets

- Prise en charge de l’écriture différée

>[!NOTE]
>Toutes les conditions préalables doivent être prises en considération lors de l’installation d’Azure AD Connect ou de la configuration du cloud Azure AD. Pour en savoir plus, lisez cet [article](../hybrid/how-to-connect-install-prerequisites.md) avant l’installation.  

## <a name="step-1---confirm-immutableid-attribute-synchronized-by-okta"></a>Étape 1 : confirmer l’attribut ImmutableID synchronisé par Okta

ImmutableID est l’attribut principal utilisé pour lier les objets synchronisés à leurs équivalents locaux. Okta prend la valeur objectGUID Active Directory d’un objet local et la convertit en une chaîne encodée en Base64. Ensuite, marque par défaut cette chaîne dans le champ ImmutableID de Azure AD.

Vous pouvez vous connecter à Azure AD PowerShell et examiner la valeur ImmutableID actuelle. Si vous n’avez jamais utilisé le module Azure AD PowerShell, exécutez `Install-Module AzureAD` dans une session PowerShell d’administration avant d’exécuter les commandes suivantes.

```Powershell
Import-module AzureAD
Connect-AzureAD
```

Si vous disposez déjà du module, vous pouvez recevoir un avertissement pour effectuer une mise à jour vers la dernière version si elle est obsolète.

Une fois le module installé, importez-le et suivez les étapes ci-dessous pour vous connecter au service Azure AD :

1. Entrez vos informations d’identification d’administrateur général dans la fenêtre d’authentification moderne.

   ![l’image montre le module d’importation](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/import-module.png)

2. Après vous être connecté au client, vérifiez les éléments de votre ImmutableID. L’exemple illustré utilise les valeurs par défaut Okta de objectGUID à ImmutableID.

   ![l’image montre les valeurs par défaut Okta de objectGUID à ImmutableID](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/okta-default-objectid.png)

3. Il existe plusieurs façons de confirmer manuellement l’objectGUID à la conversion en Base64 en local. pour une validation individuelle, utilisez l’exemple suivant :

   ```PowerShell
   Get-ADUser onpremupn | fl objectguid
   $objectguid = 'your-guid-here-1010'

   [system.convert]::ToBase64String(([GUID]$objectGUID).ToByteArray())
   ```

   ![l’image montre comment changer manuellement l’objectGUID Okta en ImmutableID](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/manual-objectguid.png)

## <a name="step-2---mass-validation-methods-for-objectguid"></a>Étape 2 : méthodes de validation de masse pour objectGUID

Avant de passer à Azure AD Connect, il est essentiel de vérifier que les ImmutableID dans Azure AD vont exactement correspondre à leurs valeurs locales.

L’exemple récupère **tous** les utilisateurs AD locaux, puis exporte une liste de leurs objectGUID et ImmutableID déjà calculés dans un fichier CSV.

1. Exécutez les commandes suivantes dans PowerShell sur un contrôleur de domaine local. 

   ```PowerShell
   Get-ADUser -Filter * -Properties objectGUID | Select-Object
   UserPrincipalName, Name, objectGUID, @{Name = 'ImmutableID';
   Expression = {
   [system.convert\]::ToBase64String(([GUID\]\$_.objectGUID).ToByteArray())
   } } | export-csv C:\\Temp\\OnPremIDs.csv
   ```

   ![l’image affiche les commandes locales du contrôleur de domaine](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/domain-controller.png)

2. Exécutez les commandes suivantes dans une session PowerShell Azure AD pour recueillir les valeurs déjà synchronisées :

   ```powershell

   Get-AzureADUser -all $true | Where-Object {$_.dirsyncenabled -like
   "true"} | Select-Object UserPrincipalName, @{Name = 'objectGUID';
   Expression = {
   [GUID][System.Convert]::FromBase64String($_.ImmutableID) } },
   ImmutableID | export-csv C:\\temp\\AzureADSyncedIDS.csv
   ```

   ![l’image affiche la session azure ad powershell](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/azure-ad-powershell.png)

   Une fois que vous avez les deux exportations, vérifiez que le ImmutableID de chaque utilisateur correspond.

   >[!IMPORTANT]
   >Si vos ImmutableID dans le Cloud ne correspondent pas aux valeurs objectGUID, vous avez modifié les valeurs par défaut pour la synchronisation Okta. Vous avez probablement choisi un autre attribut pour déterminer ImmutableID. Avant de passer à la section suivante, il est essentiel d’identifier l’attribut source qui remplit ImmutableID.      Veillez à mettre à jour l’attribut Okta avant de désactiver la synchronisation Okta.

## <a name="step-3---install-azure-ad-connect-in-staging-mode"></a>Étape 3 : installer Azure AD Connect en mode intermédiaire

Une fois que vous avez préparé votre liste de cibles source et de destination, il est temps d’installer le serveur Azure AD Connect. Si vous avez choisi d’utiliser l’approvisionnement cloud d’Azure AD Connect, vous pouvez ignorer cette section.

1. Poursuivez le [téléchargement et l’installation de Azure AD Connect](../hybrid/how-to-connect-install-custom.md) sur le serveur que vous avez choisi. 

2. Sur la page **identification des utilisateurs** , sous **sélectionnez le mode d’identification des utilisateurs avec Azure AD** sélectionnez l’option radial pour **Choisir un attribut spécifique**. Sélectionnez ensuite **ms-DS-ConsistencyGUID** si vous n’avez pas modifié les valeurs par défaut Okta.

   >[!WARNING]
   >Il s’agit de l’étape la plus importante avant de sélectionner **suivant** sur cette page. Assurez-vous que l’attribut que vous sélectionnez pour le point d’ancrage source est celui qui remplit **actuellement** vos utilisateurs Azure AD existants. Si vous sélectionnez le mauvais attribut, vous devez désinstaller et réinstaller Azure AD Connect pour resélectionner cette option.

   ![l’image affiche le guid de cohérence](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/consistency-guid.png)

3. Dans la page **Configurer**, veillez à activer la case à cocher **Activer le mode de mise en lots**, puis sur **Installer**.

   ![l’image affiche l’activation du mode de mise en lots](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/enable-staging-mode.png)

4. Lorsque la configuration est terminée, sélectionnez **Quitter**.

Avant de quitter le mode de mise en lots, il est important de vérifier que le ImmutableID a été correctement mis en correspondance.

1. Ouvrez le Service de synchronisation en tant qu’**Administrateur**.

   ![l’image montre l’ouverture du service de synchronisation](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/open-sync-service.png)

2. Vérifiez tout d’abord que la Synchronisation complète de l’espace connecteur domain.onmicrosoft.com a des utilisateurs affichés sous l’onglet **Connecteurs avec des mises à jour de Flow** .

   ![l’image montre un connecteur avec mise à jour de Flow](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/connector-flow-update.png)

3. Vérifiez ensuite qu’il n’y a pas de suppressions en attente dans l’exportation. Sélectionnez l’onglet **Connecteurs**, puis mettez en surbrillance l’espace connecteur domain.onmicrosoft.com. Ensuite, sélectionnez **Rechercher l’espace de connecteur**.

   ![l’image montre l’espace de connecteur de recherche](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/search-connector-space.png)

4. Dans la recherche d’espace de connecteur, sélectionnez la liste déroulante Portée et sélectionnez **Exportation en attente**.

   ![l’image affiche l’exportation en attente](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/pending-export.png)

5. Sélectionnez **Supprimer**, puis **Rechercher** si tous les objets ont une correspondance correcte, il ne doit y avoir aucun enregistrement correspondant pour les suppressions. Enregistrez tous les objets en attente de suppression et leurs valeurs locales.

   ![l’image affiche les enregistrements correspondants supprimés](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/delete-matching-records.png)

6. Ensuite, désactivez la case à cocher **Supprimer**, puis sélectionnez **Ajouter et modifier**, suivi d’une recherche. Vous devez voir les fonctions de mise à jour pour tous les utilisateurs en cours de synchronisation sur Azure AD via Okta. Ajoutez tous les nouveaux objets que Okta n’est pas en train de synchroniser, mais qui existent dans la structure de l’unité d’organisation (UO) qui a été sélectionnée lors de l’installation de Azure AD Connect.

   ![l’image montre l’ajout d’un nouvel objet](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/add-new-object.png)

7. Double-cliquer sur les mises à jour indique ce que Azure AD Connect communique avec Azure AD.

8. S’il existe des fonctions d’ajout pour un utilisateur qui existe déjà dans Azure AD, leur compte local ne correspond pas à leur compte cloud et AD Connect a déterminé qu’il créera un nouvel objet, enregistrera les nouveaux ajouts qui sont inattendus. Veillez à corriger la valeur ImmutableID dans Azure AD avant de quitter le mode intermédiaire.

   Dans cet exemple, Okta avait marqué l’attribut mail sur le compte de l’utilisateur, même si la valeur locale n’a pas été correctement remplie. Lorsque Azure AD Connect prend le compte de John Smith, l’attribut Mail est supprimé de son objet.

   Vérifiez que vos mises à jour incluent toujours tous les attributs attendus dans Azure AD. Si plusieurs attributs sont en cours de suppression, vous devrez peut-être remplir manuellement ces valeurs AD locales avant de supprimer le mode intermédiaire.

   ![l’image montre le remplissage des valeurs ad locales](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/on-premises-ad-values.png)

   >[!NOTE]
   >Avant de passer à l’étape suivante, assurez-vous que tous les attributs utilisateur sont correctement synchronisés et qu’ils apparaissent dans l’onglet **Exportation en attente** comme prévu. S’ils sont supprimés, assurez-vous que la correspondance de leur ImmutableID et que l’utilisateur se trouve dans l’une des UO sélectionnées pour la synchronisation.

## <a name="step-4---install-azure-ad-cloud-sync-agents"></a>Étape 4 : installer les agents Azure AD Cloud

Une fois que vous avez préparé votre liste de cibles source et de destination, il est temps [d’installer et configurer les agents de synchronisation du cloud Azure AD](../cloud-sync/tutorial-single-forest.md). Si vous avez choisi d’utiliser le serveur Azure AD Connect, ignorez cette section.

## <a name="step-5---disable-okta-provisioning-to-azure-ad"></a>Étape 5 : désactivation de l’approvisionnement Okta pour Azure AD

Une fois que l’installation de Azure AD Connect a été vérifiée et que vos exportations en attente sont dans l’ordre, il est temps de désactiver l’approvisionnement Okta pour Azure AD.

1. Accédez à votre portail Okta, sélectionnez **Applications**, puis votre application Okta utilisée pour approvisionner les utilisateurs en Azure AD. Ouvrez l’onglet approvisionnement et la section **Intégration** .

   ![l’image affiche la section intégration dans Okta](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/integration-section.png)

2. Sélectionnez **Modifier**, décochez l’option **Activer l’intégration d’API** et **Enregistrez**.

   ![l’image montre l’intégration de l’api edit enable dans Okta](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/edit-api-integration.png)

   >[!NOTE]
   >Si vous avez plusieurs applications Office 365 qui gèrent l’approvisionnement pour Azure AD, assurez-vous qu’elles sont toutes désactivées.

## <a name="step-6---disable-staging-mode-in-azure-ad-connect"></a>Étape 6 : désactivation du mode intermédiaire dans Azure AD Connect

Après la désactivation de l’approvisionnement Okta, le serveur Azure AD Connect est prêt à commencer la synchronisation des objets. Si vous avez choisi d’utiliser les agents de synchronisation Cloud Azure AD, ignorez cette section.

1. Exécutez à nouveau l’Assistant Installation à partir du bureau, puis sélectionnez **Configurer**.

   ![l’image montre le serveur Azure AD Connect](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/azure-ad-connect-server.png)

2. Sélectionnez **Configurer le mode de mise en lots**, puis **Suivant** et entrez vos informations d’identification d’administrateur général.

   ![l’image montre la configuration du mode de mise en lots](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/configure-staging-mode.png)

3. Désactivez l’option **Activer le mode de mise en lots**, puis suivant.

   ![l’image montre l’option désactiver le mode de mise en lots](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/uncheck-enable-staging-mode.png)

4. Sélectionnez **Configurer** pour continuer.

   ![l’image montre lorsque la configuration est prête](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/ready-to-configure.png)

5. Une fois la configuration terminée, ouvrez le **Service de synchronisation** en tant qu’administrateur. Affichez l’exportation sur le connecteur domain.onmicrosoft.com. Vérifiez que les ajouts, les mises à jour et les suppressions s’effectuent comme prévu.

   ![l’image montre le service verify sync](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/verify-sync-service.png)

Vous avez maintenant effectué une migration vers l’approvisionnement basé sur le serveur Azure AD Connect. Les mises à jour et les expansions de l’ensemble de fonctionnalités de Azure AD Connect peuvent être effectuées en réexécutant l’assistant d’installation.

## <a name="step-7---enable-cloud-sync-agents"></a>Étape 7 : activer les agents de synchronisation Cloud

Après la désactivation de l’approvisionnement Okta, l’agent de synchronisation Cloud Azure AD est prêt à commencer la synchronisation des objets, revenez au [portail Azure AD](https://aad.portal.azure.com/).

1. Modifiez le **Profil de configuration** sur **Activé**.

2. Après l’activation, revenez au menu approvisionnement et sélectionnez **Journaux**.

3. Évaluer que le connecteur de provisionnement a été correctement mis à jour sur place. Les agents de synchronisation Cloud ne sont pas destructifs. Ils échouent à leurs mises à jour si une correspondance n’a pas été effectuée correctement.

4. Si un utilisateur n’est pas compatible, effectuez les mises à jour nécessaires pour lier les immutableID, puis redémarrez la synchronisation de l’approvisionnement Cloud.

## <a name="next-steps"></a>Étapes suivantes

- [Migrer des applications d’Okta vers Azure AD](migrate-applications-from-okta-to-azure-active-directory.md)

- [Migrer la fédération Okta vers l’authentification managée Azure AD](migrate-okta-federation-to-azure-active-directory.md)

- [Migrer les stratégies de connexion Okta vers l’accès conditionnel Azure AD](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)
