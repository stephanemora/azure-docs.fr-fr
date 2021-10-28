---
title: À propos de la sauvegarde d’Azure Database pour PostgreSQL
description: Vue d’ensemble de la sauvegarde Azure Database pour PostgreSQL (version préliminaire)
ms.topic: conceptual
ms.date: 09/28/2021
ms.custom: references_regions
ms.openlocfilehash: b868af4c96691c9496a0c5382d9416e784d3eb8c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130219391"
---
# <a name="about-azure-database-for-postgresql-backup-preview"></a>À propos de la sauvegarde d’Azure Database pour PostgreSQL (version préliminaire)

Sauvegarde Azure et les services de base de données Azure constituent ensemble une solution de sauvegarde d’entreprise pour les serveurs Azure Database pour PostgreSQL qui conserve les sauvegardes jusqu’à 10 années. Outre la rétention à long terme, la solution offre les fonctionnalités suivantes :

- Sauvegarde planifiée et demande contrôlée par le client au niveau de la base de données individuelle.
- Restaurations au niveau de la base de données sur un serveur Postgres ou directement dans le stockage d’objets blob.
- Surveillance centralisée de toutes les opérations et de tous les travaux.
- Les sauvegardes sont stockées dans des domaines de sécurité et d’erreur distincts. Si le serveur ou l’abonnement source est compromis dans toutes les circonstances, les sauvegardes restent sécurisées dans le[coffre de sauvegarde](./backup-vault-overview.md) (dans les comptes de stockage géré de sauvegarde Azure).
- L’utilisation de **pg_dump** permet une plus grande flexibilité dans les restaurations. Cela vous permet de restaurer les différentes versions de base de données 

Vous pouvez utiliser cette solution indépendamment ou en plus de la [solution de sauvegarde native offerte par Azure PostgreSQL](../postgresql/concepts-backup.md), qui permet une conservation jusqu’à 35 jours. La solution native est adaptée aux récupérations d’exploitation, par exemple quand vous voulez récupérer à partir des sauvegardes les plus récentes. La solution Sauvegarde Azure vous aide à répondre à vos besoins en matière de conformité, et offre une sauvegarde et une restauration plus flexibles et plus précises.

## <a name="support-matrix"></a>Matrice de prise en charge

|Support  |Détails  |
|---------|---------|
|Déploiements pris en charge   |  [Azure Database pour PostgreSQL - Serveur unique](../postgresql/overview.md#azure-database-for-postgresql---single-server)     |
|Régions Azure prises en charge |  USA Est, USA Est 2, USA Centre, USA Centre Sud, USA Ouest, USA Ouest 2, USA Centre Ouest, Brésil Sud, Canada Centre, Europe Nord, Europe Ouest, Royaume-Uni Sud, Royaume-Uni Ouest, Allemagne Centre-Ouest, Suisse Nord, Suisse Ouest, Asie Est, Asie Sud-Est, Japon Est, Japon Ouest, Corée Centre, Corée Sud, Inde Centre, Australie Est, Australie Centre, Australie Centre 2, Émirats arabes unis Nord  |
|Versions d’Azure PostgreSQL prises en charge    |   9.5, 9.6, 10, 11      |

## <a name="feature-considerations-and-limitations"></a>Considérations et limitations relatives aux fonctionnalités

- Toutes les opérations sont prises en charge seulement depuis le portail Azure. 
- La limite recommandée pour la taille maximale de la base de données est de 400 Go.
- La sauvegarde inter-régions n’est pas prise en charge. Cela signifie que vous ne pouvez pas sauvegarder un serveur Azure PostgreSQL dans un coffre qui se trouve dans une autre région. De même, vous pouvez restaurer une sauvegarde seulement sur un serveur situé dans la même région que le coffre. Toutefois, nous prenons en charge la sauvegarde et la restauration entre les abonnements. 
- Seules les données sont récupérées pendant la restauration ; les « rôles » ne sont pas restaurés.
- Avec la version préliminaire, nous vous recommandons d’exécuter la solution uniquement sur votre environnement de test.

## <a name="backup-process"></a>Processus de sauvegarde

1. En tant qu’administrateur de sauvegarde, vous pouvez spécifier les bases de données Azure PostgreSQL que vous souhaitez sauvegarder. En outre, vous pouvez également spécifier les détails du coffre de clés Azure qui stocke les informations d’identification nécessaires pour se connecter aux bases de données spécifiées. Ces informations d’identification sont amorcées en toute sécurité par l’administrateur de la base de données dans le coffre de clés Azure.
1. Le service de sauvegarde valide ensuite s’il dispose des [autorisations appropriées pour s’authentifier](#azure-backup-authentication-with-the-postgresql-server) auprès du serveur PostgreSQL spécifié et pour sauvegarder ses bases de données.
1. Azure Backup fait tourner un rôle de travail (machine virtuelle) avec une extension de sauvegarde installée pour communiquer avec le serveur PostgreSQL protégé. Cette extension se compose d’un coordinateur et d’un plug-in PostgreSQL. Le coordinateur déclenche des workflows pour diverses opérations, telles que la sauvegarde et la restauration, et le plug-in gère le flux de données réel.
1. Au moment planifié, le coordinateur communique avec le plug-in et démarre le streaming des données de sauvegarde depuis le serveur PostgreSQL en utilisant **pg_dump (personnalisé)** .
1. Le plug-in envoie les données directement aux comptes de stockage managés de sauvegarde Azure (masqués par le coffre de sauvegarde), ce qui élimine la nécessité d’un emplacement intermédiaire. Les données sont chiffrées avec des clés gérées par Microsoft et stockées par le service Sauvegarde Azure dans des comptes de stockage.

 :::image type="content" source="./media/backup-azure-database-postgresql-overview/backup-process.png" alt-text="Diagramme montrant le processus de sauvegarde.":::

## <a name="azure-backup-authentication-with-the-postgresql-server"></a>Authentification de la sauvegarde Azure avec le serveur PostgreSQL

Azure Backup respecte les instructions de sécurité strictes définies par Azure. les autorisations sur la ressource à sauvegarder ne sont pas supposées et doivent être explicitement fournies par l’utilisateur. 

### <a name="key-vault-based-authentication-model"></a>Modèle d’authentification basé sur le coffre de clés

Le service sauvegarde Azure doit se connecter à Azure PostgreSQL tout en effectuant chaque sauvegarde. Alors que « nom d’utilisateur + mot de passe » (ou chaîne de connexion), correspondant à la base de données, sont utilisés pour établir cette connexion, ces informations d’identification ne sont pas stockées avec la sauvegarde Azure. Au lieu de cela, ces informations d'identification doivent être stockées de manière sécurisée par l'administrateur de la base de données dans le [coffre-fort Azure en tant que secret](../key-vault/secrets/about-secrets.md). L’administrateur de charge de travail est responsable de la gestion et de la rotation des informations d’identification. Azure Backup appelle pour obtenir les informations les plus récentes sur les secrets à partir du coffre de clés pour effectuer la sauvegarde.
 
:::image type="content" source="./media/backup-azure-database-postgresql-overview/key-vault-based-authentication-model.png" alt-text="Diagramme montrant la charge de travail ou le déroulement de la base de données.":::

#### <a name="set-of-permissions-needed-for-azure-postgresql-database-backup"></a>Ensemble d’autorisations nécessaires pour la sauvegarde de base de données Azure PostgreSQL

1. Accordez les autorisations d’accès suivantes à l’identité MSI de l’archivage de sauvegarde :

   - Accès _lecteur_ sur le serveur Azure PostgreSQL.
   - _Accès utilisateur_ (ou obtenir, lister les secrets) sur le coffre-fort de clés Azure.

1. Accès à la ligne de vue réseau sur :

   - L’indicateur **Autoriser l'accès aux services Azure** du serveur Azure PostgreSQL doit être défini sur **Oui**.
   - Le coffre-fort - **Autoriser les services Microsoft de confiance** doit être réglé sur **Oui**.

1. Privilèges de sauvegarde de l’utilisateur de base de données sur la base de données

>[!Note]
>Vous pouvez accorder ces autorisations dans le cadre de la configuration de la [sauvegarde](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases) en un seul clic si vous (l’administrateur de sauvegarde) avez un accès en écriture sur les ressources prévues, ou utilisez un modèle ARM si vous n’avez pas les autorisations requises (lorsque plusieurs personnes sont impliquées). 

#### <a name="set-of-permissions-needed-for-azure-postgresql-database-restore"></a>Ensemble d’autorisations nécessaires pour la restauration des bases de données Azure PostgreSQL

Les autorisations de restauration sont similaires à celles nécessaires à la sauvegarde et vous devez accorder les autorisations sur le serveur PostgreSQL cible et le coffre de clés correspondant. Contrairement à la configuration du workflow de sauvegarde, l’expérience d’octroi de ces autorisations en ligne n’est pas disponible actuellement. Par conséquent, vous devez [accorder manuellement l’accès sur le serveur Postgres et le coffre de clés correspondant](#grant-access-on-the-azure-postgresql-server-and-key-vault-manually).

En outre, assurez-vous que l’utilisateur de la base de données (correspondant aux informations d’identification stockées dans le coffre de clés) dispose des privilèges de restauration suivants sur la base de données :

- ALTER USER username CREATEDB ;
- Attribuez le rôle _azure_pg_admin_ à l’utilisateur de base de données.

### <a name="azure-active-directory-based-authentication-model"></a>Ajout de la prise en charge pour l’authentification basée sur Azure Active Directory

Nous avions précédemment lancé un modèle d’authentification différent entièrement basé sur Azure Active Directory (Azure AD). Toutefois, nous fournissons maintenant le nouveau modèle d’authentification basé sur le coffre de clés (comme expliqué ci-dessus) comme alternative, ce qui facilite le processus de configuration. 

[Téléchargez ce document](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) pour obtenir un script automatisé et les instructions associées pour utiliser ce modèle d’authentification. Il va accorder un ensemble d’autorisations approprié à un serveur Azure PostgreSQL pour la sauvegarde et la restauration.

>[!Note]
>Toutes les nouvelles protections de la configuration s’effectuent avec le nouveau modèle d’authentification du coffre de clés uniquement. Toutefois, toutes les instances de sauvegarde existantes configurées pour la protection avec l’authentification Azure AD continuent d’exister et des sauvegardes régulières sont effectuées. Pour restaurer ces sauvegardes, vous devez suivre l’authentification Azure AD.

## <a name="grant-access-on-the-azure-postgresql-server-and-key-vault-manually"></a>Accorder manuellement l’accès sur le serveur Azure PostgreSQL et le coffre de clés

Pour accorder toutes les autorisations d’accès requises par sauvegarde Azure, reportez-vous aux sections suivantes :

### <a name="access-permissions-on-the-azure-postgresql-server"></a>Autorisations d’accès sur le serveur Azure PostgreSQL

1. Configurer l'accès au **lecteur** MSI du coffre de sauvegarde sur le serveur Azure PostgreSQL.

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/set-reader-access-on-azure-postgresql-server-inline.png" alt-text="Capture d’écran montrant l’option permettant de définir l’accès au lecteur MSI de l’archivage de sauvegarde sur le serveur Azure PostgreSQL." lightbox="./media/backup-azure-database-postgresql-overview/set-reader-access-on-azure-postgresql-server-expanded.png":::

1. Accès à la ligne de vue réseau sur le serveur Azure PostgreSQL : définissez l’indicateur « autoriser l’accès aux services Azure » sur « Oui ».

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-azure-postgresql-server-inline.png" alt-text="Capture d’écran montrant l’option permettant de définir l’accès de la ligne d’horizon du réseau sur le serveur Azure PostgreSQL." lightbox="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-azure-postgresql-server-expanded.png":::

### <a name="access-permissions-on-the-azure-key-vault-associated-with-the-postgresql-server"></a>Autorisations d’accès sur le coffre de clés Azure (associé au serveur PostgreSQL)

1. Définissez les secrets du **coffre de clés MSI** sur l'accès utilisateur (ou **obtenir**, **lister les secrets**) au coffre de clés Azure. Pour affecter des autorisations, vous pouvez utiliser des attributions de rôles ou des stratégies d’accès. Il n’est pas nécessaire d’ajouter l’autorisation à l’aide des deux options, car cela n’est pas utile.

   - À l’aide de l’autorisation Azure RBAC (contrôle d’accès en fonction du rôle) (autrement dit, le modèle d’autorisation est défini sur contrôle d’accès en fonction du rôle Azure) :

     - Sous Contrôle d'accès, accordez à _l'utilisateur des secrets du coffre de clés_ MSI l'accès au coffre de clés. Les porteurs de ce rôle pourront lire les secrets.
     - [Accorder à des applications l’autorisation d’accéder à un coffre de clés Azure en utilisant le RBAC Azure](../key-vault/general/rbac-guide.md?tabs=azure-cli).

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/key-vault-secrets-user-access-inline.png" alt-text="Capture d’écran montrant l’option permettant de fournir un accès utilisateur secret." lightbox="./media/backup-azure-database-postgresql-overview/key-vault-secrets-user-access-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/grant-permission-to-applications-azure-rbac-inline.png" alt-text="Capture d’écran montrant l’option permettant d’accorder à l’utilisateur MSI du coffre de sauvegarde un accès utilisateur Key Vault secrets du coffre de clés." lightbox="./media/backup-azure-database-postgresql-overview/grant-permission-to-applications-azure-rbac-expanded.png":::  

   - À l’aide de stratégies d’accès (autrement dit, le modèle d’autorisation est défini sur Stratégie d’accès de coffre) :

     - Définissez les autorisations d’extraction et de liste sur les secrets.
     - En savoir plus sur [Attribuer une stratégie d’accès Azure Key Vault](../key-vault/general/assign-access-policy.md?tabs=azure-portal)

     :::image type="content" source="./media/backup-azure-database-postgresql-overview/permission-model-is-set-to-vault-access-policy-inline.png" alt-text="Capture d’écran montrant l’option d’octroi d’autorisation à l’aide du modèle d’autorisation est définie sur le modèle de stratégie d’accès de coffre." lightbox="./media/backup-azure-database-postgresql-overview/permission-model-is-set-to-vault-access-policy-expanded.png":::  
 

1. Accès à la ligne d’horizon du réseau sur le coffre de clés : définissez l’indicateur **Autoriser les services Microsoft approuvés** sur **Oui**.

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-key-vault-inline.png" alt-text="Capture d’écran montrant comment définir l’indicateur autoriser les services Microsoft approuvés sur oui pour un accès réseau de la ligne de vue sur le coffre de clés." lightbox="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-key-vault-expanded.png":::

### <a name="database-users-backup-privileges-on-the-database"></a>Privilèges de sauvegarde de l’utilisateur de base de données sur la base de données

Exécutez la requête suivante dans l’outil d'[administration PG](#using-the-pg-admin-tool) (remplacez _username_ par l’ID d’utilisateur de base de données) :

```
DO $do$
DECLARE
sch text;
BEGIN
EXECUTE format('grant connect on database %I to %I', current_database(), 'username');
FOR sch IN select nspname from pg_catalog.pg_namespace
LOOP
EXECUTE format($$ GRANT USAGE ON SCHEMA %I TO username $$, sch);
EXECUTE format($$ GRANT SELECT ON ALL TABLES IN SCHEMA %I TO username $$, sch);
EXECUTE format($$ ALTER DEFAULT PRIVILEGES IN SCHEMA %I GRANT SELECT ON TABLES TO username $$, sch);
EXECUTE format($$ GRANT SELECT ON ALL SEQUENCES IN SCHEMA %I TO username $$, sch);
EXECUTE format($$ ALTER DEFAULT PRIVILEGES IN SCHEMA %I GRANT SELECT ON SEQUENCES TO username $$, sch);
END LOOP;
END;
```

## <a name="using-the-pg-admin-tool"></a>Utilisation de l’outil d’administration PG

[ Téléchargez l'outil d'administration PG](https://www.pgadmin.org/download/) si vous ne l'avez pas encore. Vous pouvez vous connecter au serveur Azure PostgreSQL à l’aide de cet outil. En outre, vous pouvez ajouter des bases de données et de nouveaux utilisateurs à ce serveur.

:::image type="content" source="./media/backup-azure-database-postgresql-overview/connect-to-azure-postgresql-server-using-pg-admin-tool-inline.png" alt-text="Capture d’écran montrant le processus de connexion au serveur Azure PostgreSQL à l’aide de l’outil d’administration PG." lightbox="./media/backup-azure-database-postgresql-overview/connect-to-azure-postgresql-server-using-pg-admin-tool-expanded.png":::

Créez un nouveau serveur avec le nom de votre choix. Entrez le nom d’hôte/adresse identique au nom du **serveur** affiché dans l’affichage des ressources Azure PostgreSQL dans le portail Azure.

:::image type="content" source="./media/backup-azure-database-postgresql-overview/create-new-server-using-pg-admin-tool-inline.png" alt-text="Capture d’écran montrant l’option de création d’un nouveau serveur à l’aide de l’outil d’administration PG." lightbox="./media/backup-azure-database-postgresql-overview/create-new-server-using-pg-admin-tool-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql-overview/enter-host-name-or-address-name-same-as--server-name-inline.png" alt-text="Capture d’écran montrant l’option permettant d’entrer le nom d’hôte ou le nom d’adresse identique au nom du serveur." lightbox="./media/backup-azure-database-postgresql-overview/enter-host-name-or-address-name-same-as--server-name-expanded.png":::

Veillez à ajouter l' _adresse de l’ID client actuel_ aux règles de pare-feu pour la connexion.

:::image type="content" source="./media/backup-azure-database-postgresql-overview/add-current-client-id-address-to-firewall-rules-inline.png" alt-text="Capture d’écran montrant le processus d’ajout de l’adresse d’ID client actuelle aux règles de pare-feu." lightbox="./media/backup-azure-database-postgresql-overview/add-current-client-id-address-to-firewall-rules-expanded.png":::

Vous pouvez ajouter de nouvelles bases de données et des utilisateurs de base de données au serveur. Pour les utilisateurs de la base de données, ajoutez un nouveau **Login/Group Roles**. Vérifiez que **Connexion possible** est définie sur **Oui**.

:::image type="content" source="./media/backup-azure-database-postgresql-overview/add-new-databases-and-database-users-to-server-inline.png" alt-text="Capture d’écran montrant le processus d’ajout de nouvelles bases de données et d’utilisateurs de base de données au serveur." lightbox="./media/backup-azure-database-postgresql-overview/add-new-databases-and-database-users-to-server-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql-overview/add-new-login-group-roles-inline.png" alt-text="Capture d’écran montrant le processus d’ajout d’un nouveau rôle de connexion ou de groupe pour les utilisateurs de base de données." lightbox="./media/backup-azure-database-postgresql-overview/add-new-login-group-roles-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql-overview/set-can-login-to-yes-inline.png" alt-text="La capture d’écran montrant la vérification de l’option de connexion peut être définie sur Oui." lightbox="./media/backup-azure-database-postgresql-overview/set-can-login-to-yes-expanded.png":::

## <a name="next-steps"></a>Étapes suivantes

[Sauvegarde d’Azure Database pour PostgreSQL](backup-azure-database-postgresql.md)