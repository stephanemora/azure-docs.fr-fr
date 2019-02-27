---
title: TDE - Intégration d’Azure Key Vault ou Bring Your Own Key (BYOK) - Base de données SQL Azure | Microsoft Docs
description: Bring Your Own Key (BYOK) prend en charge le chiffrement transparent des données (TDE, Transparent Data Encryption) avec Azure Key Vault pour SQL Database et Data Warehouse. Vue d'ensemble de TDE avec BYOK, avantages, fonctionnement, considérations et recommandations.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 02/20/2019
ms.openlocfilehash: c8c2c58250b6f806b48241ad1bb2a85202b9b67a
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56456875"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-keys-in-azure-key-vault-bring-your-own-key-support"></a>Azure SQL Transparent Data Encryption avec des clés managées dans Azure Key Vault : support Bring Your Own Key

[Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) avec l’intégration Azure Key Vault vous permet de chiffrer la clé de chiffrement de la base de données (DEK) avec une clé asymétrique managée par le client et appelée protecteur TDE. Cela s’appelle aussi généralement la prise en charge Bring Your Own Key (BYOK) pour Transparent Data Encryption.  Dans le scénario BYOK, le protecteur TDE est stocké dans un coffre géré appartenant au client, [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), le système Azure de gestion de clés externes sur le cloud. Le protecteur TDE peut être [généré](https://docs.microsoft.com/en-us/azure/key-vault/about-keys-secrets-and-certificates) par le coffre de clés ou [transféré](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) vers le coffre de clés à partir d’un dispositif HSM local. La DEK de TDE, stockée dans la page de démarrage d’une base de données, est chiffrée et déchiffrée par le protecteur TDE, qui est stocké dans Azure Key Vault et ne le quitte jamais.  La base de données SQL doit être autorisée à déchiffrer et chiffrer la DEK dans le coffre de clés appartenant au client. Si des autorisations d’accès du serveur SQL logique au coffre de clés sont supprimées, une base de données devient inaccessible alors que toutes les données sont chiffrées. Pour Azure SQL Database, le protecteur TDE est défini au niveau du serveur SQL logique et il est hérité par toutes les bases de données associées à ce serveur. Pour [Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance), le protecteur TDE est défini au niveau de l’instance et il est hérité par toutes les bases de données *chiffrées* sur cette instance. Le terme *serveur* fait référence à la fois au serveur et à l’instance tout au long de ce document, sauf indication contraire.

Avec l’intégration de TDE à Azure Key Vault, les utilisateurs peuvent contrôler les tâches de gestion de clés, y compris les rotations de clés, les autorisations de coffre de clés, les sauvegardes de clés, ainsi que l’audit et le rapport sur tous les protecteurs TDE à l’aide de la fonctionnalité Azure Key Vault. Key Vault centralise la gestion centrale des clés, utilise des modules de sécurité matériels étroitement surveillés (HSM) et permet la séparation des responsabilités entre la gestion de clés et des données pour aider à répondre aux exigences des stratégies de sécurité.  

TDE avec l’intégration Azure Key Vault offre les avantages suivants :

- Augmentation de la transparence et du contrôle granulaire avec la possibilité de gérer soi-même le protecteur TDE
- Possibilité de révoquer les autorisations à tout moment pour supprimer les accès à la base de données
- Gestion centralisée des protecteurs TDE (avec d’autres clés et les secrets utilisés dans d’autres services Azure) en les hébergeant dans Key Vault
- Séparation des responsabilités de gestion des clés et des données au sein de l’organisation, pour prendre en charge la séparation des tâches
- Confiance accrue de vos clients, car Key Vault a été conçu de sorte que Microsoft ne puisse pas voir ni extraire des clés de chiffrement.
- Prise en charge de la rotation des clés

> [!IMPORTANT]
> Pour ceux qui utilisent TDE géré par le service et souhaitant commencer à utiliser Key Vault, TDE reste activé pendant le processus de basculement vers un protecteur TDE dans Key Vault. Il n’existe pas de temps d’arrêt ni de rechiffrement des fichiers de base de données. Le passage d’une clé gérée par un service à une clé Key Vault nécessite uniquement de rechiffrement de la clé de chiffrement de base de données (DEK), une opération rapide et en ligne.

## <a name="how-does-tde-with-azure-key-vault-integration-support-work"></a>Comment fonctionne le support de TDE avec l’intégration d’Azure Key Vault ?

![Authentification du serveur auprès de Key Vault](./media/transparent-data-encryption-byok-azure-sql/tde-byok-server-authentication-flow.PNG)

Quand TDE est tout d’abord configuré pour utiliser un protecteur TDE de Key Vault, le serveur envoie la clé DEK de chaque base de données prenant en charge TDE à Key Vault pour une requête de clé encapsulée. Key Vault retourne la clé de chiffrement de base de données chiffrée, qui est stockée dans la base de données utilisateur.  

> [!IMPORTANT]
> Il est important de noter **qu’une fois qu’un protecteur TDE est stocké dans Azure Key Vault, il ne quitte jamais ce dernier**. Le serveur peut uniquement envoyer des requêtes d’opérations de clé pour le matériel de clé du protecteur TDE au sein de Key Vault. **Il n’accède jamais au protecteur TDE et ne le met jamais en cache**. L’administrateur de Key Vault peut révoquer des autorisations de Key Vault du serveur à tout moment, auquel cas toutes les connexions au serveur sont interrompues.

## <a name="guidelines-for-configuring-tde-with-azure-key-vault"></a>Instructions de configuration de TDE avec Azure Key Vault

### <a name="general-guidelines"></a>Instructions générales

- Assurez-vous qu’Azure Key Vault et Azure SQL Database/Managed Instance soient dans le même tenant.  Les interactions entre un serveur et un coffre de clés inter-locataires **ne sont pas prises en charge**.
- Déterminez les abonnements qui vont être utilisés pour les ressources requises : le déplacement ultérieur du serveur entre des abonnements exige une nouvelle configuration de TDE avec des BYOK. En savoir plus sur le [déplacement des ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- Lors de la configuration de TDE avec Azure Key Vault, il est important de prendre en compte la charge placée sur le coffre de clés par des opérations répétées de chiffrement/déchiffrement. Par exemple, étant donné que toutes les bases de données associées à un serveur SQL Database utilisent le même protecteur TDE, un basculement de ce serveur déclenchera autant d’opérations de clés sur le coffre qu’il y a de bases de données dans le serveur. Selon notre expérience et les [limites de service de coffre de clés](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits) documentées, nous vous recommandons d’associer au maximum 500 bases de données Standard / Usage général ou 200 bases de données Premium / Critiques pour l'entreprise avec un Azure Key Vault dans un seul abonnement, pour vous assurer une disponibilité toujours élevée lors de l’accès au protecteur TDE dans le coffre.
- Recommandé : conservez une copie du protecteur TDE en local.  Cela nécessite un appareil HSM pour créer un protecteur TDE localement et un système de dépôt de clé pour stocker une copie locale du protecteur TDE.  En savoir plus sur [comment transférer une clé depuis un module HSM local vers Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys).

### <a name="guidelines-for-configuring-azure-key-vault"></a>Instructions de configuration de Azure Key Vault

- Créez un coffre de clés en activant la [suppression réversible](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) pour vous protéger des pertes de données engendrées par la suppression accidentelle d’une clé ou d’un coffre de clés.  Vous devez utiliser [PowerShell pour activer la propriété « soft-delete »](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) sur le coffre de clés (cette option n’est pas encore disponible sur le portail AKV, mais elle est exigée par Azure SQL) :  
  - Les ressources ayant fait l’objet d’une suppression réversible sont conservées pendant une durée fixée à 90 jours, à moins qu’elles ne soient récupérées ou vidées.
  - Les actions **recover** et **purge** ont leurs propres autorisations associées dans une stratégie d’accès au coffre de clés.
- Définissez un verrou de ressource sur le coffre de clés pour contrôler les utilisateurs pouvant supprimer cette ressource critique et pour empêcher toute suppression accidentelle ou non autorisée.  [En savoir plus sur les verrous de ressource](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)

- Accordez l’accès du serveur SQL Database au coffre de clés à l’aide de son identité Azure Active Directory (Azure AD).  Lorsque vous utilisez l’interface utilisateur du portail, l’identité Azure AD est automatiquement créée et les autorisations d’accès de coffre de clés sont accordées au serveur.  En utilisant PowerShell pour configurer TDE avec BYOK, l’identité Azure AD doit être créée, et sa création vérifiée une fois terminée. Consultez [Configurer TDE avec BYOK](transparent-data-encryption-byok-azure-sql-configure.md) et [Configure TDE with BYOK for Managed Instance](http://aka.ms/sqlmibyoktdepowershell) (Configurer TDE avec BYOK pour Managed Instance) pour obtenir des instructions détaillées lors de l’utilisation de PowerShell.

  > [!NOTE]
  > Si l’identité Azure AD **est accidentellement supprimée ou si les autorisations du serveur sont révoquées** à l’aide de la stratégie d’accès du coffre de clés, le serveur perd l’accès au coffre de clés et les bases de données chiffrées avec TDE sont inaccessibles dans les 24 heures.

- Lorsque vous utilisez des pare-feu et des réseaux virtuels avec Azure Key Vault, vous devez configurer les éléments suivants : - Autoriser les services Microsoft approuvés pour contourner ce pare-feu- choisissez OUI

 > [!NOTE]
 > Si les bases de données SQL chiffrées avec TDE perdent l’accès au coffre de clés parce qu’elles ne peuvent pas contourner le pare-feu, elles sont inaccessibles dans les 24 heures.

- Activer l’audit et la création de rapports sur toutes les clés de chiffrement : Key Vault fournit des journaux faciles à injecter dans d’autres outils de gestion d’événements et d’informations de sécurité (SIEM). Operations Management Suite (OMS) [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) est un exemple de service déjà intégré.
- Pour garantir la haute disponibilité des bases de données chiffrées, configurez chaque serveur SQL Database avec deux instances Azure Key Vault résidant dans des régions différentes.

### <a name="guidelines-for-configuring-the-tde-protector-asymmetric-key"></a>Lignes directrices en matière de configuration du protecteur TDE (clé asymétrique)

- Créez votre clé de chiffrement localement sur un appareil HSM local. Assurez-vous qu’il s’agit d’une clé RSA 2048 bits asymétrique afin qu’elle puisse être stockée dans Azure Key Vault.
- Déposez la clé dans un système de dépôt de clé.  
- Importez le fichier de clé de chiffrement (.pfx, .byok ou .backup) dans Azure Key Vault.

   > [!NOTE]
   > À des fins de test, il est possible de créer une clé avec Azure Key Vault. Toutefois, elle ne peut pas être déposée, car la clé privée ne peut jamais quitter le coffre de clés.  Sauvegardez et déposez toujours des clés utilisées pour chiffrer les données de production, car la perte de la clé (suppression accidentelle dans le coffre de clés, expiration, etc.) entraîne la perte définitive des données.

- Utilisez une clé sans date d’expiration et ne définissez pas de date d’expiration sur une clé en cours d’utilisation : **une fois la clé expirée, les bases de données chiffrées perdent l’accès à leur protecteur TDE et sont inaccessibles dans les 24 heures**.
- Vérifiez que la clé est activée et qu’elle dispose des autorisations pour effectuer les opérations *get*, *wrap key*, et *unwrap key*.
- Créez une sauvegarde de la clé Azure Key Vault avant d’utiliser la clé dans Azure Key Vault pour la première fois. En savoir plus sur la commande [Backup-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1).
- Créez une nouvelle sauvegarde à chaque modification de la clé (par exemple, ajout d’ACL, ajout de balises, ajout d’attributs de clé).
- **Conservez les versions précédentes** de la clé dans le coffre de clés lors de la rotation des clés, de sorte que les anciennes sauvegardes de base de données puissent être restaurées. Lorsque le protecteur TDE est modifié pour une base de données, les anciennes sauvegardes de la base de données **ne sont pas mises à jour** pour l’utiliser.  La restauration d’une sauvegarde nécessite le protecteur TDE présent lorsque celle-ci a été créée. Les rotations de clés peuvent être effectuées en suivant les instructions de l’article [Rotation du protecteur Transparent Data Encryption à l’aide de PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Conservez toutes les clés déjà utilisées dans Azure Key Vault une fois revenu aux clés gérées par le service.  Cela garantit la restauration possible des sauvegardes de base de données avec les protecteurs TDE stockés dans Azure Key Vault.  Les protecteurs TDE créés avec Azure Key Vault doivent être conservés jusqu'à ce que toutes les sauvegardes stockées aient été créées avec des clés gérées par un service.  
- Effectuez des copies de sauvegarde récupérables de ces clés à l’aide de [Backup-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1).
- Pour supprimer une clé potentiellement compromise pendant un incident de sécurité sans risque de perte de données, suivez les étapes indiquées dans [Supprimer une clé potentiellement compromise](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).

## <a name="high-availability-geo-replication-and-backup--restore"></a>Haute disponibilité, Géoréplication et Sauvegarde/restauration

### <a name="high-availability-and-disaster-recovery"></a>Haute disponibilité et récupération d’urgence

La façon de configurer la haute disponibilité avec Azure Key Vault dépend de la configuration de votre base de données et du serveur SQL Database, et voici les configurations recommandées pour les deux cas distincts.  Le premier cas est une base de données ou un serveur SQL Database autonome sans géoredondance configurée.  Le deuxième cas est une base de données ou un serveur SQL Database configuré avec des groupes de basculement ou une géoredondance, où il est nécessaire de s’assurer que chaque copie géoredondante possède une instance Azure Key Vault locale au sein du groupe de basculement pour que les géobasculements fonctionnent.

Dans le premier cas, si vous avez besoin de la haute disponibilité d’une base de données et d’un serveur SQL Database sans géoredondance configurée, il est fortement recommandé de configurer le serveur pour utiliser deux coffres de clés différents dans deux régions différentes, avec le même matériel de clé. Cela est possible en créant un protecteur TDE utilisant le coffre de clés principal situé dans la même région que le serveur SQL Database et en clonant la clé dans un coffre de clés situé dans une autre région Azure, afin que le serveur puisse accéder à un second coffre de clés si le premier venait à tomber en panne pendant que la base de données fonctionne. Utilisez l’applet de commande Backup-AzureKeyVaultKey pour récupérer la clé en format chiffré depuis le coffre de clés principal puis utilisez l’applet de commande Restore-AzureKeyVaultKey et spécifiez un coffre de clés dans la deuxième région.

![Serveur unique à haute disponibilité et aucune géo-reprise](./media/transparent-data-encryption-byok-azure-sql/SingleServer_HA_Config.PNG)

## <a name="how-to-configure-geo-dr-with-azure-key-vault"></a>Comment configurer la géo-reprise avec Azure Key Vault

Pour maintenir une haute disponibilité des protecteurs TDE pour les bases de données chiffrées, il est nécessaire de configurer des Azure Key Vault redondants basés sur les groupes de basculement de base de données SQL existants ou souhaités ou bien des instances de géoréplication active.  Chaque serveur géorépliqué nécessite un coffre de clés distinct, devant être localisé dans la même région Azure que le serveur. Si une panne rend une base de données primaire inaccessible dans une région et qu’un basculement est déclenché, la base de données secondaire est en mesure de prendre le relais à l’aide du coffre de clés secondaire.

Pour les bases de données SQL Azure géorépliquées, la configuration suivante de Azure Key Vault est requise :

- Une base de données primaire avec un coffre de clés dans une région et une base de données secondaire avec un coffre de clés dans une autre région.
- Au moins une base de données secondaire est nécessaire et jusqu'à quatre bases de données secondaires sont prises en charge.
- Les bases de données secondaires de bases de données secondaires (chaînage) ne sont pas prises en charge.

La section suivante aborde plus en détails les étapes d’installation et de configuration.

### <a name="azure-key-vault-configuration-steps"></a>Étapes de configuration de Azure Key Vault

- Installer [PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azurermps-5.6.0)
- Créez deux Azure Key Vault dans deux régions différentes en utilisant [PowerShell pour activer la propriété « soft-delete »](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) sur le coffre de clés (cette option n’est pas encore disponible à partir du portail AKV, mais elle est exigée par SQL).
- Les deux coffres Azure Key Vault doivent se trouver dans les deux régions disponibles au sein de la même zone géographique Azure pour que la sauvegarde et restauration des clés fonctionnent.  Si vous avez besoin que les deux coffres de clés soient situés dans des zones géographiques différentes pour répondre aux exigences de géo-récupération d’urgence de SQL, suivez le [processus BYOK](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) qui permet l’importation des clés à partir d’un module HSM local.
- Créer une nouvelle clé dans le premier coffre de clés :  
  - Clé RSA/RSA-HSA de 2048 bits
  - Aucune date d'expiration
  - La clé est activée et elle dispose des autorisations pour effectuer les opérations get, wrap key, et unwrap key
- Sauvegardez la clé primaire et restaurez la clé dans le deuxième coffre de clés.  Consultez [BackupAzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1) et [Restore-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-5.5.0).

### <a name="azure-sql-database-configuration-steps"></a>Étapes de configuration de Microsoft Azure SQL Database

Les étapes de configuration suivantes ne sont pas les mêmes si vous démarrez avec un nouveau déploiement SQL ou bien si vous utilisez un déploiement de géo-reprise SQL déjà existant.  Nous commençons par décrire les étapes de configuration d’un nouveau déploiement, puis nous expliquons comment attribuer des protecteurs TDE stockés dans Azure Key Vault à un déploiement existant disposant déjà d’un lien de géo-reprise.

**Étapes pour un nouveau déploiement** :

- Créez les deux serveurs SQL Database dans les deux mêmes régions que les coffres de clés créés précédemment.
- Sélectionnez le volet TDE des serveurs SQL Database, puis, pour chaque serveur SQL Database :  
  - Sélectionnez le AKV dans la même région
  - Sélectionnez la clé à utiliser comme protecteur TDE : chaque serveur utilisera la copie locale du protecteur TDE.
  - Cette opération dans le portail crée un [AppID](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) pour le serveur SQL Database, utilisé pour affecter les autorisations du serveur SQL Database pour l’accès au coffre de clés ; ne supprimez pas cette identité. L’accès peut être révoqué en supprimant les autorisations dans Azure Key Vault à la place du serveur SQL Database, qui est utilisé pour affecter les autorisations du serveur SQL Database pour l’accès au coffre de clés.
- Créez la base de données primaire.
- Suivez les [conseils de géoréplication active](sql-database-geo-replication-overview.md) pour réaliser le scénario, cette étape permet de créer la base de données secondaire.

![Groupes de basculement et géo-reprise](./media/transparent-data-encryption-byok-azure-sql/Geo_DR_Config.PNG)

> [!NOTE]
> Il est important de s’assurer que les mêmes protecteurs TDE sont présents dans les deux coffres de clés avant d’établir le lien de géoréplication entre les bases de données.

**Étapes pour une base de données SQL existante avec le déploiement de géo-récupération d’urgence** :

Étant donné que les serveurs SQL Database existent déjà et que les bases de données primaire et secondaire sont déjà affectées, les étapes de configuration d’Azure Key Vault doivent être effectuées dans l’ordre suivant :

- Démarrez par le serveur SQL Database qui héberge la base de données secondaire :
  - Affecter le coffre de clés situé dans la même région
  - Affecter le protecteur TDE
- Accédez au serveur SQL Database qui héberge la base de données primaire :
  - Sélectionnez le protecteur TDE déjà utilisé pour la base de données secondaire

![Groupes de basculement et géo-reprise](./media/transparent-data-encryption-byok-azure-sql/geo_DR_ex_config.PNG)

>[!NOTE]
>Quand vous affectez le coffre de clés au serveur, il est important de commencer par le serveur secondaire.  Dans la deuxième étape, affectez le coffre de clés au serveur principal et mettez à jour le protecteur TDE, le lien de géo-reprise continuera de fonctionner, car à ce stade le protecteur TDE utilisé par la base de données répliquée est disponible pour les deux serveurs.

Avant l’activation de TDE avec les clés gérées par le client dans Azure Key Vault pour un scénario de géo-reprise de base de données SQL, il est important de créer et de gérer deux coffres Azure Key Vault avec un contenu identique dans les mêmes régions que celles qui seront utilisées pour la géo-réplication de base de données SQL.  « Contenu identique » signifie spécifiquement que les deux coffres de clés doivent contenir des copies du/des même(s) protecteur(s) TDE afin que les deux serveurs aient accès aux protecteurs TDE utilisés par toutes les bases de données.  À l’avenir, il est nécessaire de maintenir la synchronisation des deux coffres de clés, ils doivent contenir les mêmes copies des protecteurs TDE après la rotation des clés, maintenir les vieilles versions de clés utilisées pour les fichiers journaux ou les sauvegardes, les protecteurs TDE doivent conserver les mêmes propriétés de clé et les coffres de clés doivent conserver les mêmes autorisations d’accès pour SQL.  

Suivez les étapes de l’article [Vue d’ensemble de la géo-réplication active](sql-database-geo-replication-overview.md) pour tester et déclencher un basculement, à effectuer régulièrement pour vérifier le maintien des autorisations d’accès pour SQL pour les deux coffres de clés.

### <a name="backup-and-restore"></a>Sauvegarde et restauration

Une fois qu’une base de données est chiffrée avec TDE à l’aide d’une clé du coffre de clés, toutes les sauvegardes effectuées sont également chiffrées avec le même protecteur TDE.

Pour restaurer une sauvegarde chiffrée avec un protecteur TDE du coffre de clés, assurez-vous que le matériel de clé est toujours dans le coffre d’origine sous le nom de clé d’origine. Lorsque le protecteur TDE est modifié pour une base de données, les anciennes sauvegardes de la base de données **ne sont pas mises à jour** pour utiliser le nouveau protecteur TDE. Par conséquent, nous vous recommandons de conserver toutes les anciennes versions du protecteur TDE dans le coffre de clés, afin que toutes les sauvegardes de base de données puissent être restaurées.

Si une clé susceptible d’être nécessaire pour la restauration d’une sauvegarde ne se trouve plus dans le coffre de clés d’origine, le message d’erreur suivant est renvoyé : « Le serveur cible `<Servername>` n’a pas accès à tous les URI AKV créés entre <Timestamp #1> et <Timestamp #2>. Veuillez réessayer l’opération après avoir restauré tous les URI AKV. »

Pour contrer cela, exécutez l’applet de commande [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) pour retourner la liste des clés du coffre de clés ajoutées au serveur (sauf si elles ont été supprimées par un utilisateur). Pour garantir la restauration possible de toutes les sauvegardes, vérifiez que le serveur cible pour la sauvegarde dispose d’un accès à toutes ces clés.

```powershell
Get-AzureRmSqlServerKeyVaultKey `
  -ServerName <LogicalServerName> `
  -ResourceGroup <SQLDatabaseResourceGroupName>
```

Pour en savoir plus sur la récupération d’une sauvegarde de base de données SQL, consultez [Récupérer une base de données SQL Azure](sql-database-recovery-using-backups.md). Pour en savoir plus sur la récupération d’une sauvegarde pour SQL Data Warehouse, consultez [Récupérer une Azure SQL Data Warehouse](../sql-data-warehouse/backup-and-restore.md).

Attention particulière pour les journaux sauvegardés : les journaux sauvegardés restent chiffrés avec le chiffreur TDE d’origine, même si le protecteur TDE a subi une permutation et si la base de données utilise maintenant un nouveau protecteur TDE.  Lors d’une restauration, les deux clés seront nécessaires pour restaurer la base de données.  Si le fichier journal utilise un protecteur TDE stocké dans Azure Key Vault, cette clé sera nécessaire lors de la restauration, même si entretemps la base de données est passée à un TDE géré par un service.
