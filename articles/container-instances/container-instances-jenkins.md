---
title: Utiliser Azure Container Instances en tant qu’agent de build Jenkins
description: Découvrez comment utiliser Azure Container Instances en tant qu’agent de build Jenkins.
services: container-instances
author: neilpeterson
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/20/2018
ms.author: nepeters
ms.openlocfilehash: dbe418db8fb3d73739a30b60703f15b3dc47b291
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2018
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Utiliser Azure Container Instances en tant qu’agent de build Jenkins

Les instances de conteneur Azure offrent un environnement à la demande, modulable et isolé pour l’exécution de charges de travail en conteneur. En raison de ces attributs, les instances de conteneur Azure constituent une plateforme idéale pour l’exécution des travaux de build Jenkins à grande échelle. Ce document décrit les procédures complètes de déploiement et d’utilisation d’un serveur Jenkins préconfiguré avec Azure Container Instances (ACI) en tant que cible de build.

Pour plus d’informations sur Azure Container Instances, consultez l’article [À propos d’Azure Container Instances][about-aci].

## <a name="deploy-jenkins-server"></a>Déployer un serveur Jenkins

Dans le Portail Azure, sélectionnez **Créer une ressource** et recherchez **Jenkins**. Sélectionnez l’offre Jenkins avec un serveur de publication **Microsoft**, puis sélectionnez **Créer**.

Entrez les informations ci-après sur le formulaire de base, puis cliquez sur **OK** lorsque vous avez terminé.

- **Nom** : nom du déploiement Jenkins.
- **Nom d’utilisateur** : ce nom d’utilisateur fait office d’utilisateur administrateur de la machine virtuelle Jenkins.
- **Type d’authentification** : l’authentification par clé publique SSH est recommandée. Si vous sélectionnez cette méthode, copiez une clé publique SSH à utiliser lors de la connexion à la machine virtuelle Jenkins.
- **Abonnement** : sélectionnez un abonnement Azure.
- **Groupe de ressources** : créez un groupe de ressources ou sélectionnez un groupe existant.
- **Emplacement** : sélectionnez un emplacement pour le serveur Jenkins.

![Paramètres de base de déploiement du portail Jenkins](./media/container-instances-jenkins/jenkins-portal-01.png)

Sur le formulaire de paramètres supplémentaires, renseignez les champs suivants :

- **Size (Taille)** : sélectionnez l’option de dimensionnement appropriée pour votre machine virtuelle Jenkins.
- **Type de disque de machine virtuelle** : spécifiez le type de disque HDD (lecteur de disque dur) ou SSD (Solid-State Drive) pour le serveur Jenkins.
- **Réseau virtuel** : (facultatif) sélectionnez Réseau virtuel pour modifier les paramètres par défaut.
- **Sous-réseaux** : sélectionnez Sous-réseaux, vérifiez les informations, puis sélectionnez **OK**.
- **Adresse IP publique** : la sélection de l’adresse IP publique vous permet de lui donner un nom personnalisé et de configurer la référence SKU ainsi que la méthode d’affectation.
- **Étiquette du nom de domaine** : spécifiez une valeur pour créer l’URL complète d’accès à la machine virtuelle Jenkins.
- **Jenkins release type** (Type de version Jenkins) : sélectionnez le type de version souhaité parmi les options suivantes : LTS (LTS), Weekly build (Build hebdomadaire) ou Azure Verified (Vérifié par Azure).

![Paramètres supplémentaires de déploiement du portail Jenkins](./media/container-instances-jenkins/jenkins-portal-02.png)

Dans le champ Service Principal Integration (Intégration de principal de service), sélectionnez **Auto(MSI)** (Automatique [MSI]) pour que la fonctionnalité [Azure Managed Service Identity][managed-service-identity] crée automatiquement une identité d’authentification pour l’instance Jenkins. Si vous préférez fournir vos propres informations d’identification de principal de service, sélectionnez Manuel.

Les agents cloud configurent une plateforme cloud pour les travaux de build Jenkins. Pour les besoins de ce document, sélectionnez ACI (ACI). Avec l’agent cloud ACI, chaque travail de build Jenkins est exécuté dans une instance de conteneur Azure.

![Paramètres d’intégration cloud de déploiement du portail Jenkins](./media/container-instances-jenkins/jenkins-portal-03.png)

Lorsque vous avez terminé de définir les paramètres d’intégration, cliquez sur **OK**, puis de nouveau sur **OK** dans le résumé de validation. Dans le résumé Terms of use (Conditions d’utilisation), cliquez sur **Créer**. Le déploiement du serveur Jenkins ne nécessite que quelques minutes.

## <a name="configure-jenkins"></a>Configurer Jenkins

Dans le Portail Azure, accédez au groupe de ressources Jenkins, sélectionnez la machine virtuelle Jenkins, puis notez la valeur du nom DNS.

![Instructions de connexion Jenkins](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

Accédez au nom DNS de la machine virtuelle Jenkins, puis copiez la chaîne SSH renvoyée.

![Instructions de connexion Jenkins](./media/container-instances-jenkins/jenkins-portal-04.png)

Ouvrez une session de terminal sur votre système de développement, puis collez la chaîne SSH copiée à l’étape précédente. Remplacez « username » par le nom d’utilisateur spécifié lors du déploiement du serveur Jenkins.

Une fois connecté, exécutez la commande ci-après pour récupérer le mot de passe d’administrateur initial.

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Laissez la session SSH et le tunnel s’exécuter, puis accédez à http://localhost:8080 dans un navigateur. Collez le mot de passe d’administrateur initial dans le champ, comme illustré ci-après. Lorsque vous avez terminé, sélectionnez **Continuer**.

![Déverrouillage de Jenkins](./media/container-instances-jenkins/jenkins-portal-05.png)

Sélectionnez **Install suggested plugins** (Installer les plug-ins suggérés) pour installer tous les plug-ins Jenkins recommandés.

![Installation des plug-ins Jenkins](./media/container-instances-jenkins/jenkins-portal-06.png)

Créez un compte d’utilisateur administrateur. Ce compte est utilisé pour la connexion à votre instance Jenkins et pour l’utilisation de cette dernière.

![Création d’un utilisateur Jenkins](./media/container-instances-jenkins/jenkins-portal-07.png)

Lorsque vous avez terminé, sélectionnez **Save and Finish** (Enregistrer et terminer), puis **Start using Jenkins** (Commencer à utiliser Jenkins) pour achever la configuration.

Jenkins est à présent configuré et prêt à générer et déployer le code. Cet exemple utilise une application Java simple pour illustrer un build Jenkins sur Azure Container Instances.

## <a name="create-build-job"></a>Créer un travail de build

Lorsque vous utilisez une image conteneur comme cible de build Jenkins, vous devez spécifier une image incluant tous les outils nécessaires pour un build réussi. Pour spécifier l’image, sélectionnez **Manage Jenkins (Gérer Jenkins)** > **Configure System** (Configurer le système), puis accédez à la section **Cloud**. Pour cet exemple, redéfinissez la valeur d’image Docker sur `microsoft/java-on-azure-jenkins-slave`.

Après avoir terminé, cliquez sur **Enregistrer** pour revenir au tableau de bord Jenkins.

![Configuration du cloud Jenkins](./media/container-instances-jenkins/jenkins-aci-image.png)

À présent, créez un travail de build Jenkins. Sélectionnez **Nouvel élément**, attribuez au projet de build un nom tel que `aci-java-demo`, sélectionnez **Freestyle project** (Projet libre), puis cliquez sur **OK**.

![Créer une tâche Jenkins](./media/container-instances-jenkins/jenkins-new-job.png)

Sous **Général**, vérifiez que l’option **Restrict where this project can be run** (Limiter où ce projet peut être exécuté). Dans le champ Label Expression (Expression d’étiquette), entrez `linux`. Cette configuration garantit l’exécution de ce travail de build sur le cloud ACI.

![Créer une tâche Jenkins](./media/container-instances-jenkins/jenkins-job-01.png)

Sous Gestion du code source, sélectionnez `Git`, puis entrez `https://github.com/spring-projects/spring-petclinic.git` dans le champ d’URL du référentiel. Ce référentiel GitHub contient l’exemple de code d’application.

![Ajout du code source au travail Jenkins](./media/container-instances-jenkins/jenkins-job-02.png)

Sous Build, sélectionnez **Ajouter une étape de build**, puis sélectionnez `Invoke top-level Maven targets`. Entrez `package` comme objectif d’étape de build.

![Ajout d’une étape de build Jenkins](./media/container-instances-jenkins/jenkins-job-03.png)

Sélectionnez **Enregistrer** lorsque vous avez terminé.

## <a name="run-the-build-job"></a>Exécuter le travail de build

Pour tester le travail de build et observer Azure Container Instances en tant que plateforme de build, démarrez un build manuellement.

Sélectionnez **Build Now** (Générer maintenant) pour démarrer un travail de build. Le démarrage du travail nécessite quelques minutes ; lors de l’exécution du processus, l’état du travail doit apparaître comme illustré ci-après.

![Ajout d’une étape de build Jenkins](./media/container-instances-jenkins/jenkins-job-status.png)

Pendant l’exécution du travail, ouvrez le Portail Azure et examinez le groupe de ressources Jenkins. Une instance de conteneur Azure devrait avoir été créée. Le travail Jenkins s’exécute à l’intérieur de cette instance.

![Builds Jenkins dans ACI](./media/container-instances-jenkins/jenkins-aci.png)

Étant donné que Jenkins exécute davantage de travaux que le nombre configuré d’Exécuteurs (valeur par défaut de 2), plusieurs instances de conteneur Azure sont créées.

![Builds Jenkins dans ACI](./media/container-instances-jenkins/jenkins-aci-multi.png)

Une fois tous les travaux de build terminés, les instances de conteneur Azure sont supprimées.

![Builds Jenkins dans ACI](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de Jenkins sur Azure, consultez l’article [Azure et Jenkins][jenkins-azure].

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-service-identity]: ../active-directory/managed-service-identity/overview.md