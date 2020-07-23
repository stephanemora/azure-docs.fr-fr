---
title: Comment importer et exporter des paramètres de configuration Azure AD Connect
description: Ce document décrit les questions fréquemment posées sur le provisionnement cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a13236294f74bbe4bdaf8c1a30408afad09d9796
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86224938"
---
# <a name="importing-and-exporting-azure-ad-connect-configuration-settings-public-preview"></a>Importer et exporter des paramètres de configuration Azure AD Connect (Préversion publique) 

Les déploiements Azure AD Connect varient d’une installation de forêt unique en mode Express à des déploiements complexes qui se synchronisent sur plusieurs forêts à l’aide de règles de synchronisation personnalisées. En raison du grand nombre d’options et de mécanismes de configuration, il apparaît essentiel de comprendre les paramètres actifs et de pouvoir déployer rapidement un serveur avec une configuration identique. Cette fonctionnalité introduit la possibilité de cataloguer la configuration d’un serveur de synchronisation donné et d’importer les paramètres dans un nouveau déploiement. Différents instantanés de paramètres de synchronisation peuvent être comparés afin de visualiser facilement les différences entre deux serveurs ou le même serveur au fil du temps. 

Chaque fois que la configuration est modifiée à partir de l’Assistant Azure AD Connect, un nouveau fichier de paramètres JSON horodaté est automatiquement exporté vers  **%ProgramData%\AADConnect**. Le nom de fichier des paramètres se présente sous la forme **Applied-SynchronizationPolicy-*.JSON** où la dernière partie de ce nom correspond à un timestamp. 

>[!IMPORTANT]
> Seules les modifications apportées par Azure AD Connect sont automatiquement exportées. Toutes les modifications apportées à l’aide de PowerShell, de Synchronization Service Manager ou de l’éditeur de règles de synchronisation doivent être exportées à la demande, si nécessaire, pour conserver une copie à jour. L’exportation à la demande peut également être utilisée pour placer une copie des paramètres dans un emplacement sécurisé à des fins de récupération d’urgence. 

## <a name="exporting-azure-ad-connect-settings"></a>Exporter les paramètres Azure AD Connect 

Pour afficher un résumé de vos paramètres de configuration, ouvrez l’outil Azure AD Connect et sélectionnez la tâche supplémentaire nommée : Afficher ou exporter la configuration actuelle. Un résumé rapide de vos paramètres s’affiche, de même que la possibilité d’exporter la configuration complète de votre serveur. 

Par défaut, les paramètres sont exportés vers **%ProgramData%\AADConnect**. Vous pouvez cependant choisir d’enregistrer les paramètres dans un emplacement protégé afin d’en garantir la disponibilité en cas de sinistre. Les paramètres sont exportés à l’aide du format de fichier JSON et ne doivent pas être créés ou modifiés à des fins de cohérence logique. L’importation d’un fichier créé à la main ou modifié n’est pas prise en charge et peut entraîner des résultats inattendus. 

## <a name="importing-azure-ad-connect-settings"></a>Importer les paramètres Azure AD Connect 

Pour importer des paramètres précédemment exportés, procédez comme suit :
 
1. Installez **Azure AD Connect** sur un nouveau serveur. 
2. Sélectionnez l’option **Personnaliser** après la page d’**accueil**. 
3. Cliquez sur **Importer les paramètres de synchronisation**. Recherchez le fichier de paramètres JSON précédemment exporté.  
4. Cliquez sur **Installer**.

![Installer les composants](media/how-to-connect-import-export-config/import1.png)

> [!NOTE]
> Remplacez les paramètres de cette page, comme l’utilisation de SQL Server plutôt que la base de données locale ou l’utilisation d’un compte de service existant plutôt que l’attribut VSA par défaut, etc. Ces paramètres ne sont pas importés à partir du fichier des paramètres de configuration, mais fournis à titre d’information et de comparaison.

### <a name="import-installation-experience"></a>Expérience d’importation de l’installation 

L’expérience d’importation de l’installation est intentionnellement simple avec des entrées minimales de l’utilisateur afin de facilement reproduire un serveur existant.  

Voici les seules modifications qui peuvent être apportées lors de l’expérience d’installation. Toutes les autres modifications peuvent être apportées après l’installation à partir de l’Assistant Azure AD Connect : 
- **Informations d’identification Azure Active Directory**  : le nom de compte de l’administrateur général Azure utilisé pour configurer le serveur d’origine est suggéré par défaut, et  **DOIT**  être modifié si vous souhaitez synchroniser les informations dans un nouveau répertoire.
- **Connexion utilisateur**  : les options de connexion configurées pour votre serveur d’origine sont sélectionnées par défaut et demandent automatiquement les informations d’identification ou d’autres informations requises lors de la configuration. Dans de rares cas, il peut s’avérer nécessaire de configurer un serveur avec différentes options pour ne pas modifier le comportement du serveur actif. Sinon, appuyez simplement sur Suivant pour utiliser les mêmes paramètres. 
- **Informations d’identification du répertoire local**  : pour chaque répertoire local inclus dans vos paramètres de synchronisation, vous devez fournir des informations d’identification afin de créer un compte de synchronisation ou fournir un compte de synchronisation personnalisé créé préalablement. Cette procédure est identique à l’expérience de nouvelle installation à cette exception près : vous ne pouvez pas ajouter ou supprimer de répertoires. 
- **Options de configuration**  : comme pour une nouvelle installation, vous pouvez choisir de configurer les paramètres initiaux pour déterminer s’il faut démarrer la synchronisation automatique ou activer le mode de préproduction. La principale différence réside dans le fait que le mode de préproduction est intentionnellement activé par défaut pour permettre la comparaison des résultats de configuration et de synchronisation avant d’exporter activement les résultats vers Azure. 

![Connecter des répertoires](media/how-to-connect-import-export-config/import2.png)

>[!NOTE]
>Un seul serveur de synchronisation peut remplir le rôle principal et exporter activement les modifications de configuration vers Azure. Tous les autres serveurs doivent être placés en mode de préproduction. 

## <a name="migrating-settings-from-an-existing-server"></a>Migrer les paramètres à partir d'un serveur existant 

Si un serveur existant ne prend pas en charge la gestion des paramètres, vous pouvez choisir de mettre à niveau le serveur sur place ou de migrer les paramètres à utiliser vers un nouveau serveur de préproduction.  

La migration requiert l’exécution d’un script PowerShell qui extrait les paramètres existants à utiliser dans une nouvelle installation.  Cette méthode est recommandée pour cataloguer les paramètres de votre serveur existant, puis les appliquer à un serveur de préproduction nouvellement installé.  La comparaison des paramètres du serveur d’origine avec le serveur nouvellement créé permet de visualiser rapidement les modifications entre les serveurs.  Comme toujours, suivez le processus de certification de votre organisation pour vous assurer qu’aucune configuration supplémentaire ne s’impose.  

### <a name="migration-process"></a>Processus de migration 
Pour migrer les paramètres, procédez comme suit :

1. Ouvrez cmd en tant qu’administrateur sur le nouveau serveur de préproduction.
2. Extrayez **AzureADConnect.msi** en exécutant la commande suivante : `msiexec /a msifile/qb TARGETDIR=targetpath` où **msifile** correspond à l’adresse du fichier msi et **targetpath** au répertoire dans lequel vous souhaitez extraire les fichiers.
   
   Exemple : `msiexec /a "C:\Holding\AzureADConnect.msi" TARGETDIR="C:\extractedfiles"`
3. Copiez **MigrateSettings.ps1** à partir du répertoire Microsoft Azure AD Connect\Tools vers un emplacement du serveur existant.  Par exemple, C:\setup.  Où setup correspond à un répertoire créé sur le serveur existant. 
![Connecter des répertoires](media/how-to-connect-import-export-config/migrate1.png)

4. Exécutez le script comme indiqué ci-dessous et enregistrez l’intégralité du répertoire de configuration de serveur de niveau inférieur. Copiez ce répertoire sur le nouveau serveur de préproduction. Notez que vous devez copier l’intégralité du dossier **Exported-ServerConfiguration-** * sur le nouveau serveur. 
 ![Connecter des répertoires](media/how-to-connect-import-export-config/migrate2.png)

 ![Connecter des répertoires](media/how-to-connect-import-export-config/migrate3.png)

5. Lancez **Azure AD Connect** en double-cliquant sur l’icône du bureau. Acceptez le CLUF, puis sur la page suivante, cliquez sur le bouton **Personnaliser**. 
6. Activez la case à cocher **Importer les paramètres de synchronisation**, cliquez sur le bouton **Parcourir** pour parcourir le dossier copié dans le dossier Exported-ServerConfiguration-*, puis sélectionnez MigratedPolicy.json pour importer les paramètres migrés.
 ![Connecter des répertoires](media/how-to-connect-import-export-config/migrate4.png)

7. Pour comparer les paramètres migrés avec les paramètres appliqués, recherchez les derniers fichiers **Migrated-SynchronizationPolicy-*.JSON** et **Applied-SynchronizationPolicy-*.JSON** (* correspond au timestamp) sous **%ProgramData%\AADConnect**. Utilisez votre outil de comparaison de fichiers favori pour comparer la parité. 

## <a name="post-installation-verification"></a>Vérification après installation 

La comparaison du fichier de paramètres initialement importés avec le fichier de paramètres exportés du serveur nouvellement déployé est une étape essentielle pour comprendre les différences entre le déploiement prévu et le déploiement obtenu. L’utilisation de votre application de comparaison de texte favorite côte à côte offre une visualisation instantanée qui met rapidement en évidence les modifications souhaitées ou accidentelles. Si de nombreuses étapes de configuration manuelles sont désormais évitées, vous devez systématiquement suivre le processus de certification de votre organisation pour vous assurer qu’aucune configuration supplémentaire ne s’impose. Une telle configuration peut intervenir si vous utilisez des paramètres avancés non capturés dans la préversion publique de gestion des paramètres. 

Les limitations connues sont notamment : 
- **Règles de synchronisation**  : la priorité d’une règle personnalisée doit être comprise dans la plage réservée 0-99 pour éviter les conflits avec les règles standard de Microsoft. Le placement d’une règle personnalisée en dehors de la plage réservée peut se traduire par un décalage de votre règle personnalisée au fur et à mesure que des règles standard sont ajoutées à la configuration. Un problème similaire se produit si votre configuration contient des règles standard modifiées. La modification d’une règle standard est vivement déconseillée et l’emplacement de la règle est susceptible d’être incorrect. Réécriture d'appareil : ces paramètres sont catalogués mais ne sont actuellement pas appliqués lors de la configuration. Si la réécriture d'appareil a été activée pour votre serveur d’origine, vous devez configurer manuellement cette fonctionnalité sur le serveur nouvellement déployé. 
- **Types d’objets synchronisés**  : bien qu’il soit possible de limiter la liste des types d’objets synchronisés (utilisateurs, contacts, groupes, etc.) à l’aide de Synchronization Service Manager, cette fonctionnalité n’est actuellement pas prise en charge via les paramètres de synchronisation. Une fois l’installation terminée, vous devez réappliquer manuellement la configuration avancée. 
- **Profils d’exécution personnalisés**  : bien qu’il soit possible de modifier l’ensemble des profils d’exécution par défaut à l’aide de Synchronization Service Manager, cette fonctionnalité n’est actuellement pas prise en charge via les paramètres de synchronisation. Une fois l’installation terminée, vous devez réappliquer manuellement la configuration avancée. 
- **Configuration de la hiérarchie d’approvisionnement**  : cette fonctionnalité avancée de Synchronization Service Manager n’est pas prise en charge via les paramètres de synchronisation et doit être reconfigurée manuellement au terme du déploiement initial. 
- **Authentification AD FS et PingFederate**  : les méthodes de connexion associées à ces fonctionnalités d’authentification sont automatiquement présélectionnées, mais vous devez fournir de manière interactive tous les autres paramètres de configuration requis. 

 ## <a name="next-steps"></a>Étapes suivantes

- [Matériel et conditions préalables](how-to-connect-install-prerequisites.md) 
- [Paramètres Express](how-to-connect-install-express.md)
- [Paramètres personnalisés](how-to-connect-install-custom.md)
- [Installation des agents Azure AD Connect Health](how-to-connect-health-agent-install.md) 
