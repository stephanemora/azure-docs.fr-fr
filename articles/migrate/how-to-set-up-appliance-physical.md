---
title: Configurer une appliance Azure Migrate pour les serveurs physiques
description: Découvrez comment configurer une appliance Azure Migrate pour l’évaluation de serveurs physiques.
ms.service: azure-migrate
ms.topic: article
ms.date: 04/15/2020
ms.openlocfilehash: 6d9cc071ad5d81a09a14b12fe2acdf564c2ea6c8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84331778"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Configurer une appliance pour les serveurs physiques

Cet article explique comment configurer l’appliance Azure Migrate si vous évaluez des serveurs physiques avec l’outil Azure Migrate : Server Assessment.

L’appliance Azure Migrate est une appliance légère utilisée par Azure Migrate Server Assessment pour effectuer les opérations suivantes :

- Découvrir les serveurs locaux.
- Envoyer des métadonnées et des données de performances concernant les serveurs découverts à Azure Migrate Server Assessment.

[En savoir plus](migrate-appliance.md) sur les appliances d’Azure Migrate.


## <a name="appliance-deployment-steps"></a>Étapes de déploiement d’appliance

Pour configurer l’appliance, vous devez :
- Télécharger un fichier compressé avec le script du programme d’installation Azure Migrate à partir du portail Azure.
- Extraire le contenu du fichier compressé. Lancer la console PowerShell avec des privilèges administratifs.
- Exécuter le script PowerShell pour lancer l’application web de l’appliance.
- Configurez l’appliance pour la première fois, puis inscrivez-la auprès du projet Azure Migrate.

## <a name="download-the-installer-script"></a>Télécharger le script du programme d’installation

Téléchargez le fichier compressé pour l’appliance.

1. Dans **Objectifs de migration** > **Serveurs** > **Azure Migrate : Server Assessment**, cliquez sur **Découvrir**.
2. Dans **Découvrir des machines** > **Vos machines sont-elles virtualisées ?** , cliquez sur **Non virtualisé/autre**.
3. Cliquez sur **Télécharger** pour télécharger le fichier compressé.

    ![Télécharger la machine virtuelle](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Vérifier la sécurité

Vérifiez que le fichier compressé est sécurisé avant de le déployer.

1. Sur l’ordinateur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier compressé :
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple d’utilisation du cloud public : ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
    - Exemple d’utilisation du cloud du secteur public : ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Vérifiez la version la plus récente de l’appliance et les valeurs de hachage :
 
    - Pour le cloud public :

        **Scénario** | **Télécharger*** | **Valeur de hachage**
        --- | --- | ---
        Physique (63,1 Mo) | [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2105112) | 0a27adf13cc5755e4b23df0c05732c6ac08d1fe8850567cb57c9906fbc3b85a0

    - Pour Azure Government :

        **Scénario** | **Télécharger*** | **Valeur de hachage**
        --- | --- | ---
        Physique (63,1 Mo) | [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2120100&clcid=0x409) | 93dfef131026e70acdfad2769cd208ff745ab96a96f013cdf3f9e1e61c9b37e1


## <a name="run-the-azure-migrate-installer-script"></a>Exécuter le script du programme d’installation Azure Migrate
Le script du programme d’installation effectue les opérations suivantes :

- Installe des agents et une application web pour la découverte et l’évaluation des serveurs physiques.
- Installe des rôles Windows, notamment le service d’activation Windows, IIS et PowerShell ISE.
- Télécharge et installe un module réinscriptible IIS. [Plus d’informations](https://www.microsoft.com/download/details.aspx?id=7435)
- Met à jour une clé de Registre (HKLM) avec les détails de paramètres persistants pour Azure Migrate.
- Crée les fichiers suivants sous le chemin :
    - **Fichiers de configuration** : %Programdata%\Microsoft Azure\Config
    - **Fichiers journaux** : %Programdata%\Microsoft Azure\Logs

Exécutez le script comme suit :

1. Extrayez le fichier compressé dans un dossier sur le serveur qui hébergera l’appliance.  Veillez à ne pas exécuter le script sur un ordinateur d’une appliance Azure Migrate existante.
2. Lancez PowerShell sur le serveur ci-dessus avec un privilège administratif (élevé).
3. Remplacez le répertoire PowerShell par le dossier dans lequel le contenu a été extrait du fichier compressé téléchargé.
4. Exécutez le script nommé **AzureMigrateInstaller.ps1** via la commande suivante :

    - Pour le cloud public : ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 ```
    - Pour Azure Government : ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```

    Une fois son exécution terminé, le script lance l’application web de l’appliance.

Si vous rencontrez des problèmes, vous pouvez accéder aux journaux de script dans C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>horodatage</em>.log pour les résoudre.



### <a name="verify-appliance-access-to-azure"></a>Vérifier l’accès de l’appliance à Azure

Vérifiez que la machine virtuelle de l’appliance peut se connecter aux URL Azure pour les clouds [publics](migrate-appliance.md#public-cloud-urls) et du [secteur public](migrate-appliance.md#government-cloud-urls).

## <a name="configure-the-appliance"></a>Configurer l’appliance

Configurez l’appliance pour la première fois.

1. Ouvrez un navigateur sur une machine qui peut se connecter à la machine virtuelle, puis ouvrez l’URL de l’application web de l’appliance : **https://*nom ou adresse IP de l’appliance* : 44368**.

   Vous pouvez aussi ouvrir l’application à partir du bureau en cliquant sur le raccourci de l’application.
2. Dans l’application web > **Configurer les prérequis**, procédez comme suit :
    - **Licence** : Acceptez les termes de licence et lisez les informations relatives aux tiers.
    - **Connectivité** : L’application vérifie que la machine virtuelle a accès à Internet. Si la machine virtuelle utilise un proxy :
        - Cliquez sur **Paramètres du proxy** et spécifiez l’adresse du proxy et le port d’écoute, sous la forme http://ProxyIPAddress ou http://ProxyFQDN.
        - Spécifiez les informations d’identification si le proxy nécessite une authentification.
        - Seuls les proxys HTTP sont pris en charge.
    - **Synchronisation de l’heure** : L’heure est vérifiée. L’heure de l’appliance doit être synchronisée avec l’heure Internet pour que la découverte des machines virtuelles fonctionne correctement.
    - **Installer les mises à jour** : Azure Migrate Server Assessment vérifie que les dernières mises à jour sont installées sur l’appliance.

### <a name="register-the-appliance-with-azure-migrate"></a>Inscrire l’appliance auprès d’Azure Migrate

1. Cliquez sur **Se connecter**. S’il n’apparaît pas, vérifiez que vous avez désactivé le bloqueur de fenêtres publicitaires dans le navigateur.
2. Sous le nouvel onglet, connectez-vous avec vos informations d’identification Azure.
    - Connectez-vous avec votre nom d’utilisateur et votre mot de passe.
    - La connexion avec un code PIN n’est pas prise en charge.
3. Une fois la connexion réussie, revenez à l’application web.
4. Sélectionnez l’abonnement où le projet Azure Migrate a été créé. Sélectionnez ensuite le projet.
5. Spécifiez un nom pour l’appliance. Le nom doit être alphanumérique et comporter 14 caractères au maximum.
6. Cliquez sur **S'inscrire**.


## <a name="start-continuous-discovery"></a>Démarrer la découverte en continu

Connectez-vous à partir de l’appliance aux serveurs physiques, puis démarrez la découverte.

1. Cliquez sur **Ajouter des informations d’identification** pour spécifier les informations d’identification du compte que l’appliance utilisera pour découvrir les serveurs.  
2. Spécifiez le **système d’exploitation**, un nom convivial pour les informations d’identification ainsi que le nom d’utilisateur et le mot de passe. Cliquez ensuite sur **Ajouter**.
Vous pouvez ajouter un ensemble d’informations d’identification pour les serveurs Windows et Linux.
4. Cliquez sur **Ajouter un serveur**, puis spécifiez les détails du serveur - nom de domaine complet (FQDN)/adresse IP et nom convivial des informations d’identification (une entrée par ligne) - pour vous connecter au serveur.
3. Cliquez sur **Valider**. Après validation, la liste des serveurs qui peuvent être découverts s’affiche.
    - Si la validation échoue pour un serveur, passez en revue l’erreur en pointant sur l’icône dans la colonne **État**. Corrigez les problèmes et recommencez la validation.
    - Pour supprimer un serveur, sélectionnez > **Supprimer**.
4. Après la validation, cliquez sur **Enregistrer et démarrer la découverte** pour démarrer le processus de découverte.

Ceci démarre la découverte. Il faut environ 15 minutes pour que les métadonnées des machines virtuelles découvertes apparaissent dans le portail Azure.

## <a name="verify-servers-in-the-portal"></a>Vérifier les serveurs dans le portail

Une fois la découverte terminée, vous pouvez vérifier que les serveurs apparaissent dans le portail.

1. Ouvrez le tableau de bord Azure Migrate.
2. Dans la page **Azure Migrate - Serveurs** > **Azure Migrate : Server Assessment**, cliquez sur l’icône qui affiche le nombre de **Serveurs découverts**.


## <a name="next-steps"></a>Étapes suivantes

Essayez l’[évaluation des serveurs physiques](tutorial-assess-physical.md) avec Azure Migrate Server Assessment.
