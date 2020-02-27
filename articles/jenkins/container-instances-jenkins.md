---
title: Build Jenkins sur une instance de conteneur
description: Découvrez comment configurer un serveur Jenkins pour exécuter des tâches de build à la demande sur Azure Container Instances
ms.topic: article
ms.date: 08/31/2018
ms.openlocfilehash: 8bb84895fb581053248fbad326ea7b2c8d1873a2
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617741"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Utiliser Azure Container Instances en tant qu’agent de build Jenkins

Le service Azure Container Instances (ACI) offre un environnement à la demande, modulable et isolé pour l’exécution de charges de travail en conteneur. En raison de ces attributs, ACI constitue une plateforme idéale pour l’exécution des travaux de build Jenkins à grande échelle. Cet article décrit les procédures complètes de déploiement et d’utilisation d’un serveur Jenkins préconfiguré avec ACI en tant que cible de build.

Pour plus d’informations sur Azure Container Instances, consultez l’article [À propos d’Azure Container Instances](../container-instances/container-instances-overview.md).

## <a name="deploy-a-jenkins-server"></a>Déployer un serveur Jenkins

1. Dans le Portail Azure, sélectionnez **Créer une ressource** et recherchez **Jenkins**. Sélectionnez l’offre Jenkins avec un serveur de publication **Microsoft**, puis sélectionnez **Créer**.

2. Entrez les informations ci-après sur le formulaire **De base**, puis sélectionnez **OK**.

   - **Name** : Entrez un nom pour le déploiement Jenkins.
   - **Nom d’utilisateur** : Entrez un nom pour l’utilisateur administrateur de la machine virtuelle Jenkins.
   - **Type d'authentification** : Nous vous recommandons d’utiliser une clé publique SSH pour l’authentification. Si vous sélectionnez cette option, collez une clé publique SSH à utiliser pour la connexion à la machine virtuelle Jenkins.
   - **Abonnement**: Sélectionnez un abonnement Azure.
   - **Groupe de ressources** : Créez un groupe de ressources ou sélectionnez-en un.
   - **Emplacement** : Sélectionnez un emplacement pour le serveur Jenkins.

   ![Paramètres de base relatifs au déploiement du portail Jenkins](./media/container-instances-jenkins/jenkins-portal-01.png)

3. Sur le formulaire **Paramètres supplémentaires**, renseignez les champs suivants :

   - **Size** : Sélectionnez l’option de dimensionnement appropriée pour votre machine virtuelle Jenkins.
   - **Type de disque de machine virtuelle** : Spécifiez le type de disque **HDD** (lecteur de disque dur) ou **SSD** (solid-state drive) pour le serveur Jenkins.
   - **Réseau virtuel** : Sélectionnez la flèche si vous souhaitez modifier les paramètres par défaut.
   - **Sous-réseaux** : Sélectionnez la flèche, vérifiez les informations, puis sélectionnez **OK**.
   - **Adresse IP publique** : Sélectionnez la flèche pour attribuer un nom personnalisé à l’adresse IP publique, configurer la référence SKU et définir la méthode d’affectation.
   - **Étiquette du nom de domaine** : Spécifiez une valeur pour créer l’URL complète d’accès à la machine virtuelle Jenkins.
   - **Jenkins release type** (Type de version Jenkins) : Sélectionnez le type de version souhaité parmi les options : **LTS**, **Weekly build** (Build hebdomadaire) ou **Azure Verified** (Vérifié par Azure).

   ![Paramètres supplémentaires relatifs au déploiement du portail Jenkins](./media/container-instances-jenkins/jenkins-portal-02.png)

4. Pour l’intégration de principal du service, sélectionnez **Auto(MSI)** (Automatique (MSI)) afin que les [identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md) créent automatiquement une identité d’authentification pour l’instance Jenkins. Si vous préférez fournir vos propres informations d’identification de principal de service, sélectionnez **Manuel**.

5. Les agents cloud configurent une plateforme cloud pour les travaux de build Jenkins. Pour les besoins de cet article, sélectionnez **ACI** (ACI). Avec l’agent cloud ACI, chaque travail de build Jenkins est exécuté dans une instance de conteneur.

   ![Paramètres d’intégration cloud relatifs au déploiement du portail Jenkins](./media/container-instances-jenkins/jenkins-portal-03.png)

6. Lorsque vous avez terminé de définir les paramètres d’intégration, sélectionnez **OK**, puis de nouveau **OK** dans le résumé de validation. Dans le résumé **Conditions d’utilisation**, cliquez sur **Créer**. Le déploiement du serveur Jenkins ne nécessite que quelques minutes.

## <a name="configure-jenkins"></a>Configurer Jenkins

1. Dans le Portail Azure, accédez au groupe de ressources Jenkins, sélectionnez la machine virtuelle Jenkins, puis notez la valeur du nom DNS.

   ![Nom DNS dans les détails concernant la machine virtuelle Jenkins](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Accédez au nom DNS de la machine virtuelle Jenkins, puis copiez la chaîne SSH renvoyée.

   ![Instructions de connexion Jenkins avec la chaîne SSH](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Ouvrez une session de terminal sur votre système de développement, puis collez la chaîne SSH copiée à l’étape précédente. Remplacez `username` par le nom d’utilisateur que vous avez spécifié en déployant le serveur Jenkins.

4. Une fois la session connectée, exécutez la commande ci-après pour récupérer le mot de passe d’administrateur initial :

   ```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. Laissez la session SSH et le tunnel s’exécuter, puis accédez à l’adresse `http://localhost:8080` dans un navigateur. Collez le mot de passe d’administrateur initial dans la zone, puis sélectionnez **Continuer**.

   ![Écran de déverrouillage de Jenkins présentant la zone de mot de passe d’administrateur](./media/container-instances-jenkins/jenkins-portal-05.png)

6. Sélectionnez **Install suggested plugins** (Installer les plug-ins suggérés) pour installer tous les plug-ins Jenkins recommandés.

   ![Écran de personnalisation de Jenkins avec sélection de l’option d’installation des plug-ins suggérés](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Créez un compte d’utilisateur administrateur. Ce compte est utilisé pour la connexion à votre instance Jenkins et pour l’utilisation de cette dernière.

   ![Écran de création du premier utilisateur administrateur renseigné avec les informations d’identification](./media/container-instances-jenkins/jenkins-portal-07.png)

8. Sélectionnez **Save and Finish** (Enregistrer et terminer), puis **Start using Jenkins** (Commencer à utiliser Jenkins) pour achever la configuration.

Jenkins est à présent configuré et prêt à générer et déployer le code. Cet exemple utilise une application Java simple pour illustrer un build Jenkins sur Azure Container Instances.

## <a name="create-a-build-job"></a>Créer un travail de build

À présent, un travail de build Jenkins est créé pour montrer les builds Jenkins sur une instance de conteneur Azure.

1. Sélectionnez **Nouvel élément**, attribuez au projet de build un nom comme **aci-demo**, sélectionnez **Freestyle project** (Projet libre), puis sélectionnez **OK**.

   ![Zone du nom de travail de build et liste des types de projets](./media/container-instances-jenkins/jenkins-new-job.png)

2. Sous **Général**, vérifiez que l’option **Restrict where this project can be run** (Limiter où ce projet peut être exécuté). Dans la zone d’expression d’étiquette, entrez **linux**. Cette configuration garantit l’exécution de ce travail de build sur le cloud ACI.

   ![Onglet « Général » contenant les détails de configuration](./media/container-instances-jenkins/jenkins-job-01.png)

3. Sous **Build**, sélectionnez **Ajouter une étape de build**, puis sélectionnez **Exécuter le shell**. Entrez la commande `echo "aci-demo"`.

   ![Onglet « Build » contenant les sélections de l’étape de build](./media/container-instances-jenkins/jenkins-job-02.png)

5. Sélectionnez **Enregistrer**.

## <a name="run-the-build-job"></a>Exécuter le travail de build

Pour tester le travail de build et observer Azure Container Instances en tant que plateforme de build, démarrez un build manuellement.

1. Sélectionnez **Build Now** (Générer maintenant) pour démarrer un travail de build. Le démarrage du travail nécessite quelques minutes. Une fenêtre d’état semblable à celle illustrée ci-dessous doit s’afficher :

   ![Informations d’historique de build présentant l’état du travail](./media/container-instances-jenkins/jenkins-job-status.png)

2. Pendant l’exécution du travail, ouvrez le Portail Azure et examinez le groupe de ressources Jenkins. Une instance de conteneur doit avoir été créée. Le travail Jenkins s’exécute à l’intérieur de cette instance.

   ![Instance de conteneur dans le groupe de ressources](./media/container-instances-jenkins/jenkins-aci.png)

3. Étant donné que Jenkins exécute davantage de travaux que le nombre configuré d’Exécuteurs Jenkins (valeur par défaut de 2), plusieurs instances de conteneur sont créées.

   ![Instances de conteneur nouvellement créées](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. Lorsque tous les travaux de build sont terminés, les instances de conteneur sont supprimées.

   ![Groupe de ressources avec instances de conteneur supprimées](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Dépannage du plug-in Jenkins

Si vous constatez des bogues dans les plug-ins Jenkins, enregistrez un problème dans le [Jenkins JIRA](https://issues.jenkins-ci.org/) du composant en question.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [CI/CD vers Azure App Service](java-deploy-webapp-tutorial.md)