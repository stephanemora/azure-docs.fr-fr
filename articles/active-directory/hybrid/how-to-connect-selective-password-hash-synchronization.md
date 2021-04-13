---
title: Synchronisation sélective du hachage de mot de passe pour Azure AD Connect
description: Cet article explique comment installer et configurer la synchronisation sélective du hachage de mot de passe à utiliser avec Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a73f4eba9581965470b95111e6dda1d8014e4cb
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167496"
---
# <a name="selective-password-hash-synchronization-configuration-for-azure-ad-connect"></a>Configuration de la synchronisation sélective du hachage de mot de passe pour Azure AD Connect

La [synchronisation sélective du hachage de mot de passe](whatis-phs.md) est l’une des méthodes de connexion utilisées pour accomplir l’identité hybride. Azure AD Connect synchronise le hachage du mot de passe d’un utilisateur entre une instance Active Directory locale et une instance Azure AD basée sur le cloud.  Par défaut, une fois qu’elle a été configurée, la synchronisation du hachage de mot de passe aura lieu sur tous les utilisateurs que vous synchronisez.

Si vous souhaitez exclure un sous-ensemble d’utilisateurs de la synchronisation du hachage de leurs mots de passe sur Azure AD, vous pouvez configurer la synchronisation sélective du hachage de mot de passe à l’aide des étapes guidées fournies dans cet article.

>[!Important]
> Microsoft ne prend pas en charge la modification ou l’utilisation de la synchronisation Azure AD Connect en dehors des configurations ou des actions documentées de façon formelle. Ces configurations ou actions peuvent entraîner un état de synchronisation Azure AD Connect incohérent ou non pris en charge. Par conséquent, Microsoft ne peut pas garantir que l’équipe du support technique sera en mesure de fournir un support efficace pour de tels déploiements. 


## <a name="consider-your-implementation"></a>Réfléchir à votre implémentation  
Pour réduire l’effort administratif de la configuration, vous devez d’abord prendre en compte le nombre d’objets utilisateur que vous souhaitez exclure de la synchronisation du hachage de mot de passe. Vérifiez lequel des scénarios ci-dessous, qui s’excluent mutuellement, correspond à vos besoins afin de sélectionner l’option de configuration qui vous convient.
- Si le nombre d’utilisateurs à **exclure** est **inférieur** au nombre d’utilisateurs à **inclure**, suivez les étapes de cette [section](#excluded-users-is-smaller-than-included-users).
- Si le nombre d’utilisateurs à **exclure** est **supérieur** au nombre d’utilisateurs à **inclure**, suivez les étapes de cette [section](#excluded-users-is-larger-than-included-users).

> [!Important]
> Quelle que soit l’option de configuration choisie, une synchronisation initiale obligatoire (synchronisation complète) pour appliquer les modifications est effectuée automatiquement lors du cycle de synchronisation suivant.

> [!Important]
> La configuration de la synchronisation sélective du hachage de mot de passe influence directement la réécriture du mot de passe. Les modifications de mot de passe ou les réinitialisations de mot de passe lancées dans Azure Active Directory sont réécrites dans Active Directory local uniquement si l’utilisateur est dans l’étendue de la synchronisation de hachage de mot de passe. 

### <a name="the-admindescription-attribute"></a>Attribut adminDescription
Les deux scénarios s’appuient sur la définition de l’attribut adminDescription des utilisateurs sur une valeur spécifique.  Cela permet d’appliquer les règles et c’est ce qui permet à la synchronisation sélective du hachage de mot de passe de fonctionner.

|Scénario|Valeur adminDescription|
|-----|-----|
|Moins d’utilisateurs exclus que d’utilisateurs inclus|PHSFiltered|
|Plus d’utilisateurs exclus que d’utilisateurs inclus|PHSIncluded|

Cet attribut peut être défini :

- à l’aide de l’interface utilisateur Utilisateurs et ordinateurs Active Directory
- à l’aide de la cmdlet PowerShell `Set-ADUser`  Pour plus d’informations, consultez [Set-ADUser](https://docs.microsoft.com/powershell/module/addsadministration/set-aduser).

 


### <a name="disable-the-synchronization-scheduler"></a>Désactiver le planificateur de synchronisation
Avant de commencer l’un ou l’autre scénario, vous devez désactiver le planificateur de synchronisation tout en modifiant les règles de synchronisation.
 1. Démarrez Windows PowerShell et entrez la commande suivante :

     ```Set-ADSyncScheduler -SyncCycleEnabled $false``` 
 
2.  Vérifiez que le planificateur est désactivé en exécutant la cmdlet suivante :
     
    ```Get-ADSyncScheduler```

Pour plus d’informations sur le planificateur, consultez [Planificateur de synchronisation Azure AD Connect](how-to-connect-sync-feature-scheduler.md).




## <a name="excluded-users-is-smaller-than-included-users"></a>Moins d’utilisateurs exclus que d’utilisateurs inclus
La section suivante décrit comment activer la synchronisation sélective du hachage de mot de passe lorsque le nombre d’utilisateurs à **exclure** **est inférieur** au nombre d’utilisateurs à **inclure**.

>[!Important]
> Avant de continuer, vérifiez que le planificateur de synchronisation est désactivé comme indiqué ci-dessus.

- Créez une copie modifiable de l’entrée **Entrant depuis AD – Utilisateur AccountEnabled** avec l’option **Activer la synchronisation de hachage du mot de passe** désélectionnée et définissez son filtre d’étendue. 
- Créez une autre copie modifiable de la valeur par défaut **Entrant depuis AD – Utilisateur AccountEnabled** avec l’option **Activer la synchronisation de hachage du mot de passe** sélectionnée et définissez son filtre d’étendue. 
- Réactivez le planificateur de synchronisation. 
- Dans Active Directory, définissez la valeur de l’attribut qui a été défini comme attribut d’étendue sur les utilisateurs que vous souhaitez autoriser dans la synchronisation du hachage de mot de passe. 

>[!Important]
>Les étapes fournies pour configurer la synchronisation sélective du hachage de mot de passe ne concernent que les objets utilisateur dont l’attribut **adminDescription** est renseigné dans Active Directory avec la valeur **PHSFiltered**.
Si cet attribut n’est pas renseigné ou si la valeur est différente de **PHSFiltered**, ces règles ne seront pas appliquées aux objets utilisateur.


### <a name="configure-the-necessary-synchronization-rules"></a>Configurer les règles de synchronisation nécessaires :

 1. Démarrez l’éditeur de règles de synchronisation et définissez les filtres **Synchronisation des mots de passe** sur **Activé** et **Type de règle** sur **Standard**.
     ![Démarrer l’éditeur de règles de synchronisation](media/how-to-connect-selective-password-hash-synchronization/exclude-1.png)
 2. Sélectionnez la règle **Entrant depuis AD – Utilisateur AccountEnabled** pour le connecteur de forêt Active Directory sur lequel vous souhaitez configurer la synchronisation sélective du hachage de mot de passe, puis cliquez sur **Modifier**. Sélectionnez **Oui** dans la boîte de dialogue suivante pour créer une copie modifiable de la règle d’origine.
     ![Sélectionner une règle](media/how-to-connect-selective-password-hash-synchronization/exclude-2.png)
 3. La première règle désactivera la synchronisation du hachage de mot de passe. Donnez le nom suivant à la nouvelle règle personnalisée : **Entrant depuis AD – Utilisateur AccountEnabled – Filtrer les utilisateurs par PHS**.
 Remplacez la valeur de précédence par un nombre inférieur à 100 (par exemple **90** ou la valeur la plus basse disponible dans votre environnement).
 Assurez-vous que les cases à cocher **Activer la synchronisation de mot de passe** et **Désactivé** sont décochées.
 Cliquez sur **Suivant**.
  ![Modifier une règle de trafic entrant](media/how-to-connect-selective-password-hash-synchronization/exclude-3.png)
 4. Dans **Filtre d’étendue**, cliquez sur **Ajouter une clause**.
 Sélectionnez **adminDescription** dans la colonne d’attribut et **EQUAL** dans la colonne Opérateur, puis entrez **PHSFiltered** comme valeur.
     ![Filtre d’étendue](media/how-to-connect-selective-password-hash-synchronization/exclude-4.png)
 5. Aucune autre modification n’est nécessaire. Les **règles de jointure** et les **transformations** doivent être laissées avec les paramètres copiés par défaut ; vous pouvez donc maintenant cliquer sur **Enregistrer**.
 Cliquez sur **OK** dans la boîte de dialogue d’avertissement indiquant qu’une synchronisation complète sera exécutée lors du prochain cycle de synchronisation du connecteur.
     ![Enregistrer la règle](media/how-to-connect-selective-password-hash-synchronization/exclude-5.png)
 6. Ensuite, créez une autre règle personnalisée avec la synchronisation du hachage de mot de passe activée. Sélectionnez de nouveau la règle par défaut **Entrant depuis AD – Utilisateur AccountEnabled** pour la forêt Active Directory sur laquelle vous souhaitez configurer la synchronisation sélective du hachage de mot de passe, puis cliquez sur **Modifier**. Sélectionnez **Oui** dans la boîte de dialogue suivante pour créer une copie modifiable de la règle d’origine.
     ![Règle personnalisée](media/how-to-connect-selective-password-hash-synchronization/exclude-6.png)
 7. Donnez le nom suivant à la nouvelle règle personnalisée : **Entrant depuis AD – Utilisateur AccountEnabled – Utilisateurs inclus pour la PHS**.
 Remplacez la valeur de précédence par un nombre inférieur à celui de la règle créée précédemment (dans cet exemple, ce sera **89**).
 Vérifiez que la case **Activer la synchronisation de mot de passe** est cochée et que celle pour **Désactivé** est décochée.
 Cliquez sur **Suivant**.  
     ![Modifier une nouvelle règle](media/how-to-connect-selective-password-hash-synchronization/exclude-7.png)
 8. Dans **Filtre d’étendue**, cliquez sur **Ajouter une clause**.
 Sélectionnez **adminDescription** dans la colonne d’attribut et **NOTEQUAL** dans la colonne Opérateur, puis entrez **PHSFiltered** comme valeur.
     ![Règle d’étendue](media/how-to-connect-selective-password-hash-synchronization/exclude-8.png)
 9. Aucune autre modification n’est nécessaire. Les **règles de jointure** et les **transformations** doivent être laissées avec les paramètres copiés par défaut ; vous pouvez donc maintenant cliquer sur **Enregistrer**.
 Cliquez sur **OK** dans la boîte de dialogue d’avertissement indiquant qu’une synchronisation complète sera exécutée lors du prochain cycle de synchronisation du connecteur.
     ![Règles de jointure](media/how-to-connect-selective-password-hash-synchronization/exclude-9.png)
 10. Confirmez la création des règles. Supprimez les filtres **Synchronisation du mot de passe :** **Activée** et **Type de règle** : **Standard**. Vous devriez voir les deux nouvelles règles que vous venez de créer.
     ![Confirmer les règles](media/how-to-connect-selective-password-hash-synchronization/exclude-10.png) 


### <a name="re-enable-synchronization-scheduler"></a>Réactiver le planificateur de synchronisation :  
Une fois que vous avez terminé les étapes de configuration des règles de synchronisation nécessaires, réactivez le planificateur de synchronisation en procédant comme suit :
 1. Dans Windows PowerShell, exécutez :

     ```Set-ADSyncScheduler -SyncCycleEnabled $true```
 2. Vérifiez ensuite qu’il a été correctement activé en exécutant :

     ```Get-ADSyncScheduler```

Pour plus d’informations sur le planificateur, consultez [Planificateur de synchronisation Azure AD Connect](how-to-connect-sync-feature-scheduler.md).

### <a name="edit-users-admindescription-attribute"></a>Modifier l’attribut **adminDescription** des utilisateurs :
Une fois toutes les configurations terminées, vous devez modifier l’attribut **adminDescription** de tous les utilisateurs que vous souhaitez **exclure** de la synchronisation du hachage de mot de passe dans Active Directory et ajouter la chaîne utilisée dans le filtre d’étendue : **PHSFiltered**.
   
  ![Modifier l’attribut](media/how-to-connect-selective-password-hash-synchronization/exclude-11.png)

Vous pouvez également utiliser la commande PowerShell suivante pour modifier l’attribut **adminDescription** d’un utilisateur :

```Set-ADUser myuser -Replace @{adminDescription="PHSFiltered"}```

## <a name="excluded-users-is-larger-than-included-users"></a>Plus d’utilisateurs exclus que d’utilisateurs inclus
La section suivante décrit comment activer la synchronisation sélective du hachage de mot de passe lorsque le nombre d’utilisateurs à **exclure** **est supérieur** au nombre d’utilisateurs à **inclure**.

>[!Important]
> Avant de continuer, vérifiez que le planificateur de synchronisation est désactivé comme indiqué ci-dessus.

Voici un résumé des actions qui seront effectuées dans les étapes ci-dessous :

- Créez une copie modifiable de l’entrée **Entrant depuis AD – Utilisateur AccountEnabled** avec l’option **Activer la synchronisation de hachage du mot de passe** désélectionnée et définissez son filtre d’étendue. 
- Créez une autre copie modifiable de la valeur par défaut **Entrant depuis AD – Utilisateur AccountEnabled** avec l’option **Activer la synchronisation de hachage du mot de passe** sélectionnée et définissez son filtre d’étendue. 
- Réactivez le planificateur de synchronisation. 
- Dans Active Directory, définissez la valeur de l’attribut qui a été défini comme attribut d’étendue sur les utilisateurs que vous souhaitez autoriser dans la synchronisation du hachage de mot de passe. 

>[!Important]
>Les étapes fournies pour configurer la synchronisation sélective du hachage de mot de passe ne concernent que les objets utilisateur dont l’attribut **adminDescription** est renseigné dans Active Directory avec la valeur **PHSIncluded**.
Si cet attribut n’est pas renseigné ou si la valeur est différente de **PHSIncluded**, ces règles ne seront pas appliquées aux objets utilisateur.


### <a name="configure-the-necessary-synchronization-rules"></a>Configurer les règles de synchronisation nécessaires :

 1. Démarrez l’éditeur de règles de synchronisation et définissez les filtres **Synchronisation des mots de passe** sur **Activé** et **Type de règle** sur **Standard**.
     ![Type de règle](media/how-to-connect-selective-password-hash-synchronization/include-1.png)
 2. Sélectionnez la règle **Entrant depuis AD – Utilisateur AccountEnabled** pour la forêt Active Directory sur laquelle vous souhaitez configurer la synchronisation sélective du hachage de mot de passe, puis cliquez sur **Modifier**. Sélectionnez **Oui** dans la boîte de dialogue suivante pour créer une copie modifiable de la règle d’origine.
     ![Entrant à partir d’AD](media/how-to-connect-selective-password-hash-synchronization/include-2.png)
 3. La première règle désactivera la synchronisation du hachage de mot de passe. Donnez le nom suivant à la nouvelle règle personnalisée : **Entrant depuis AD – Utilisateur AccountEnabled – Filtrer les utilisateurs par PHS**.
 Remplacez la valeur de précédence par un nombre inférieur à 100 (par exemple **90** ou la valeur la plus basse disponible dans votre environnement).
 Assurez-vous que les cases à cocher **Activer la synchronisation de mot de passe** et **Désactivé** sont décochées.
 Cliquez sur **Suivant**.
  ![Définir la priorité](media/how-to-connect-selective-password-hash-synchronization/include-3.png)
 4. Dans **Filtre d’étendue**, cliquez sur **Ajouter une clause**.
Sélectionnez **adminDescription** dans la colonne d’attribut et **NOTEQUAL** dans la colonne Opérateur, puis entrez **PHSIncluded** comme valeur.
     ![Ajouter une clause](media/how-to-connect-selective-password-hash-synchronization/include-4.png)
 5. Aucune autre modification n’est nécessaire. Les **règles de jointure** et les **transformations** doivent être laissées avec les paramètres copiés par défaut ; vous pouvez donc maintenant cliquer sur **Enregistrer**.
 Cliquez sur **OK** dans la boîte de dialogue d’avertissement indiquant qu’une synchronisation complète sera exécutée lors du prochain cycle de synchronisation du connecteur.
     ![Transformation](media/how-to-connect-selective-password-hash-synchronization/include-5.png)
 6. Ensuite, créez une autre règle personnalisée avec la synchronisation du hachage de mot de passe activée. Sélectionnez de nouveau la règle par défaut **Entrant depuis AD – Utilisateur AccountEnabled** pour la forêt Active Directory sur laquelle vous souhaitez configurer la synchronisation sélective du hachage de mot de passe, puis cliquez sur **Modifier**. Sélectionnez **Oui** dans la boîte de dialogue suivante pour créer une copie modifiable de la règle d’origine.
     ![Utilisateur AccountEnabled](media/how-to-connect-selective-password-hash-synchronization/include-6.png)
 7. Donnez le nom suivant à la nouvelle règle personnalisée : **Entrant depuis AD – Utilisateur AccountEnabled – Utilisateurs inclus pour la PHS**.
 Remplacez la valeur de précédence par un nombre inférieur à celui de la règle créée précédemment (dans cet exemple, ce sera **89**).
 Vérifiez que la case **Activer la synchronisation de mot de passe** est cochée et que celle pour **Désactivé** est décochée.
 Cliquez sur **Suivant**.
     ![Activer la synchronisation de mot de passe](media/how-to-connect-selective-password-hash-synchronization/include-7.png)
 8. Dans **Filtre d’étendue**, cliquez sur **Ajouter une clause**.
 Sélectionnez **adminDescription** dans la colonne d’attribut et **EQUAL** dans la colonne Opérateur, puis entrez **PHSIncluded** comme valeur.
     ![PHSIncluded](media/how-to-connect-selective-password-hash-synchronization/include-8.png)
 9. Aucune autre modification n’est nécessaire. Les **règles de jointure** et les **transformations** doivent être laissées avec les paramètres copiés par défaut ; vous pouvez donc maintenant cliquer sur **Enregistrer**.
 Cliquez sur **OK** dans la boîte de dialogue d’avertissement indiquant qu’une synchronisation complète sera exécutée lors du prochain cycle de synchronisation du connecteur.
     ![Enregistrer maintenant](media/how-to-connect-selective-password-hash-synchronization/include-9.png)
 10.    Confirmez la création des règles. Supprimez les filtres **Synchronisation du mot de passe :** **Activée** et **Type de règle** : **Standard**. Vous devriez voir les deux nouvelles règles que vous venez de créer.
     ![Synchronisation activée](media/how-to-connect-selective-password-hash-synchronization/include-10.png)

### <a name="re-enable-synchronization-scheduler"></a>Réactiver le planificateur de synchronisation :  
Une fois que vous avez terminé les étapes de configuration des règles de synchronisation nécessaires, réactivez le planificateur de synchronisation en procédant comme suit :
 1. Dans Windows PowerShell, exécutez :

     ```Set-ADSyncScheduler -SyncCycleEnabled $true```
 2. Vérifiez ensuite qu’il a été correctement activé en exécutant :

     ```Get-ADSyncScheduler```

Pour plus d’informations sur le planificateur, consultez [Planificateur de synchronisation Azure AD Connect](how-to-connect-sync-feature-scheduler.md).

### <a name="edit-users-admindescription-attribute"></a>Modifier l’attribut **adminDescription** des utilisateurs :
Une fois toutes les configurations terminées, vous devez modifier l’attribut **adminDescription** pour tous les utilisateurs que vous souhaitez **inclure** pour la synchronisation du hachage de mot de passe dans Active Directory et ajouter la chaîne utilisée dans le filtre d’étendue : **PHSIncluded**.

  ![Modifier les attributs](media/how-to-connect-selective-password-hash-synchronization/include-11.png)
 
 Vous pouvez également utiliser la commande PowerShell suivante pour modifier l’attribut **adminDescription** d’un utilisateur :

 ```Set-ADUser myuser -Replace @{adminDescription="PHSIncluded"}``` 

## <a name="next-steps"></a>Étapes suivantes
- [Qu’est-ce que la synchronisation de hachage de mot de passe ?](whatis-phs.md)
- [Fonctionnement de la synchronisation de hachage de mot de passe](how-to-connect-password-hash-synchronization.md)
