---
title: Tester des modules Terraform dans Azure à l’aide de Terratest
description: Découvrez comment utiliser Terratest pour tester vos modules Terraform.
services: terraform
ms.service: terraform
keywords: terraform, devops, storage account, azure, terratest, unit test, integration test
author: JunyiYi
manager: jeconnoc
ms.author: junyi
ms.topic: tutorial
ms.date: 10/19/2018
ms.openlocfilehash: 7feee063c7b311934f7d157a9dff62d803a041b0
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638705"
---
# <a name="test-terraform-modules-in-azure-using-terratest"></a>Tester des modules Terraform dans Azure à l’aide de Terratest

Les modules Terraform servent à créer des composants réutilisables, composables et testables. Ils représentent l’encapsulation dans le monde de l’« infrastructure en tant que code ».

Comme avec d’autres composants logiciels, l’assurance qualité joue un rôle important dans les modules Terraform. Malheureusement, il existe peu de documentation expliquant comment créer des tests unitaires et des tests d’intégration dans les modules Terraform. Ce didacticiel présente une infrastructure de test et les meilleures pratiques que nous avons adoptées lors de la génération de nos [modules Azure Terraform](https://registry.terraform.io/browse?provider=azurerm).

Après avoir étudié les infrastructures de tests les plus populaires, nous avons choisi d’utiliser [Terratest](https://github.com/gruntwork-io/terratest). Terratest est implémenté sous forme de bibliothèque Go. Il fournit une collection de fonctions et de modèles d’assistance pour les tâches de test des infrastructures communes, telles que les requêtes HTTP et la connexion SSH à une machine virtuelle spécifique. Parmi les principaux avantages de Terratest, vous trouverez :

- **Elle fournit des outils d’assistance pratiques pour contrôler l’infrastructure.** Cette fonctionnalité est utile lorsque vous souhaitez vérifier l’infrastructure réelle dans l’environnement réel.
- **La structure de dossiers est clairement organisée.** Vos cas de test sont clairement organisés et suivront la [structure de dossier standard des modules Terraform](https://www.terraform.io/docs/modules/create.html#standard-module-structure).
- **Tous les cas de test sont écrits en Go.** Étant donné que les développeurs de Terraform sont déjà des développeurs Go, utiliser Terratest évite de devoir apprendre un autre langage de programmation. En outre, les seules dépendances nécessaires pour exécuter des cas de test dans Terratest sont Go et Terraform.
- **Cette infrastructure est très extensible.** Il n’est pas difficile d’étendre des fonctions supplémentaires sur Terratest, telles que les fonctionnalités spécifiques à Azure.

## <a name="prerequisites"></a>Prérequis

Ce guide pratique peut être exécuté sur Windows, Linux ou MacOS, et indépendamment de la plateforme. Avant de continuer, installez les logiciels suivants :

- **Le langage de programmation Go** : les cas de test Terraform sont écrits en [Go](https://golang.org/dl/).
- **dep** : [dep](https://github.com/golang/dep#installation) est un outil de gestion de dépendance pour Go.
- **Azure CLI** : [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) est un outil en ligne de commande pour la gestion des ressources Azure. (Terraform prend en charge l’authentification auprès d’Azure via un principal de service ou [l’interface Azure CLI](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html).)
- **mage** : nous allons utiliser l’[exécutable mage](https://github.com/magefile/mage/releases) pour apprendre comment simplifier l’exécution de vos cas Terratest. 

## <a name="create-a-static-webpage-module"></a>Créer un module de page Web statique

Dans ce didacticiel, vous allez créer un module Terraform qui configure une page Web statique en chargeant un fichier HTML unique sur un objet blob de stockage Azure. Ce module permet aux utilisateurs du monde entier d’accéder à cette page Web via une URL renvoyée par le module.

> [!NOTE]
> Tous les fichiers décrits dans cette section doivent être créés sous votre emplacement [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH).

Tout d’abord, créez un dossier nommé `staticwebpage` dans le dossier `src` de GoPath. La structure générale des dossiers de ce didacticiel est illustrée ci-dessous. (Les fichiers marqués par un astérisque `(*)` sont d’intérêt majeur pour cette section.)

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf      (*)
   ├ 📄 outputs.tf   (*)
   └ 📄 variables.tf (*)
```

Le module de page Web statique accepte trois entrées, qui sont déclarées dans `./variables.tf` :

```hcl
variable "location" {
  description = "The Azure region in which all resources will be created."
}

variable "website_name" {
  description = "The website name which will be used to create a bunch of related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static homepage HTML in your local filesystem."
  default     = "index.html"
}
```

Comme mentionné précédemment, ce module génère également une URL déclarée dans `./outputs.tf` :

```hcl
output "homepage_url" {
  value = "${azurerm_storage_blob.homepage.url}"
}
```

Ce qui nous amène à la logique principale de ce module. Au total, ce dernier approvisionne quatre ressources :
- Un groupe de ressources dont le nom est l’entrée `website_name` suivie de `-staging-rg`.
- Un compte de stockage dont le nom est l’entrée `website_name` suivie de `data001`. Mais afin de respecter les limitations de nom de compte de stockage, le module supprime tous les caractères spéciaux et bascule le nom complet en minuscules.
- Un conteneur au nom fixe, `wwwroot`, créé dans le compte de stockage cité ci-dessus.
- Un fichier HTML lu à partir de l’entrée `html_path` et chargé vers `wwwroot/index.html`.

La logique de module de page Web statique est implémentée dans `./main.tf` :

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = "${var.location}"
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = "${azurerm_resource_group.main.name}"
  location                 = "${azurerm_resource_group.main.location}"
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = "${azurerm_resource_group.main.name}"
  storage_account_name  = "${azurerm_storage_account.main.name}"
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = "${azurerm_resource_group.main.name}"
  storage_account_name   = "${azurerm_storage_account.main.name}"
  storage_container_name = "${azurerm_storage_container.main.name}"
  source                 = "${var.html_path}"
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>Test unitaire

Terratest est un outil conçu pour les tests d’intégration, ce qui signifie que ce dernier approvisionne des ressources réelles dans un environnement réel. Parfois, ces travaux peuvent devenir particulièrement volumineux, notamment si vous avez un grand nombre de ressources à configurer. La logique de conversion de nommage du compte de stockage décrite dans la section précédente est un bon exemple : nous n’avons pas réellement besoin de configurer des ressources ; nous souhaitons simplement nous assurer que la logique de conversion de nommage est correcte.

La flexibilité de Terratest permet de le faire facilement en utilisant des tests unitaires. Il s’agit de cas de test exécutés en local (bien qu’un accès à internet soit toujours requis) en exécutant simplement des commandes `terraform init` et `terraform plan`. Les cas de test unitaire analyseront la sortie de `terraform plan` et rechercheront les valeurs d’attribut à comparer.

Le reste de cette section décrit comment utiliser Terratest pour implémenter un test unitaire et s’assurer que la logique de conversion de nommage du compte de stockage est correcte. Nous nous intéresserons uniquement aux fichiers marqués d’un astérisque `(*)`.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html                (*)
   │   │       └ 📄 main.tf                   (*)
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go (*)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Tout d’abord, le fichier HTML vide `./test/fixtures/storage-account-name/empty.html` est simplement un espace réservé.

Le fichier `./test/fixtures/storage-account-name/main.tf` est la structure du cas de test. Il accepte une entrée `website_name`, qui est également l’entrée des tests unitaires. Voici sa logique :

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = "${var.website_name}"
  html_path    = "empty.html"
}
```

Enfin, le composant principal est l’implémentation des tests unitaires : `./test/storage_account_name_unit_test.go`

Si vous êtes un développeur de Go, vous reconnaîtrez qu’elle correspond à la signature d’une fonction de test classique Go du fait qu’elle accepte un argument de type `*testing.T`.

Dans le corps du test unitaire, nous disposons de cinq cas au total définis dans la variable `testCases` (clé en entrée, valeur en sortie attendue). Pour chaque cas de test unitaire, nous allons tout d’abord exécuter `terraform init` en ciblant le dossier de fixture de test (`./test/fixtures/storage-account-name/`). 

Ensuite, une commande `terraform plan` avec une entrée de cas de test spécifique (consultez la définition de `website_name` dans `tfOptions`) enregistre le résultat dans `./test/fixtures/storage-account-name/terraform.tfplan` (non répertorié dans la structure générale des dossiers).

Ensuite, ce fichier de résultat sera analysé sur une structure lisible par code à l’aide de l’Analyseur de plan Terraform officiel.

Nous allons maintenant rechercher les attributs qui nous intéressent (dans ce cas le `name` du `azurerm_storage_account`) et les comparer avec la sortie attendue.

```go
package test

import (
    "os"
    "path"
    "testing"

    "github.com/gruntwork-io/terratest/modules/terraform"
    terraformCore "github.com/hashicorp/terraform/terraform"
)

func TestUT_StorageAccountName(t *testing.T) {
    t.Parallel()

    // Test cases for storage account name conversion logic
    testCases := map[string]string{
        "TestWebsiteName": "testwebsitenamedata001",
        "ALLCAPS":         "allcapsdata001",
        "S_p-e(c)i.a_l":   "specialdata001",
        "A1phaNum321":     "a1phanum321data001",
        "E5e-y7h_ng":      "e5ey7hngdata001",
    }

    for input, expected := range testCases {
        // Specify test case folder and "-var" options
        tfOptions := &terraform.Options{
            TerraformDir: "./fixtures/storage-account-name",
            Vars: map[string]interface{}{
                "website_name": input,
            },
        }

        // Terraform init and plan only
        tfPlanOutput := "terraform.tfplan"
        terraform.Init(t, tfOptions)
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions.Vars, "plan", "-out="+tfPlanOutput)...)

        // Read and parse the plan output
        f, err := os.Open(path.Join(tfOptions.TerraformDir, tfPlanOutput))
        if err != nil {
            t.Fatal(err)
        }
        defer f.Close()
        plan, err := terraformCore.ReadPlan(f)
        if err != nil {
            t.Fatal(err)
        }

        // Validate the test result
        for _, mod := range plan.Diff.Modules {
            if len(mod.Path) == 2 && mod.Path[0] == "root" && mod.Path[1] == "staticwebpage" {
                actual := mod.Resources["azurerm_storage_account.main"].Attributes["name"].New
                if actual != expected {
                    t.Fatalf("Expect %v, but found %v", expected, actual)
                }
            }
        }
    }
}
```

Pour exécuter les tests unitaires, vous devez procéder aux étapes suivantes dans la ligne de commande.

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage/test$ go test -run TestUT_StorageAccountName
```

Vous verrez le résultat de test traditionnel de Go après environ une minute.

### <a name="integration-test"></a>Test d’intégration

Contrairement aux tests unitaires, les tests d’intégration sont requis pour une perspective de bout en bout sur l’approvisionnement des ressources dans un environnement réel. Terratest est parfaitement adapté à ce genre de cas. Étant donné que les meilleures pratiques concernant les modules Terraform recommandent également que le dossier `examples` contienne quelques exemples de bout en bout, pourquoi ne pas simplement tester ces exemples sous forme de tests d’intégration ? Dans cette section, nous allons nous concentrer sur trois fichiers, marqués d’un astérisque `(*)`.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html              (*)
   │       └ 📄 main.tf                 (*)
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go (*)
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Commençons tout d’abord avec les exemples. Un dossier exemple appelé `hello-world/` est créé dans le dossier `./examples/`. Nous fournissons une page HTML simple à charger `./examples/hello-world/index.html` :

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demostrate Terratest.</p>
</body>
</html>
```

L’exemple Terraform `./examples/hello-world/main.tf` est similaire à l’exemple proposé pour le test unitaire, avec une seule différence majeure : elle affiche également l’URL du contenu HTML chargé appelé `homepage`.

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = "${var.website_name}"
}

output "homepage" {
  value = "${module.staticwebpage.homepage_url}"
}
```

Terratest et la fonction de test Go classique apparaissent également dans le fichier de test d’intégration `./test/hello_world_example_test.go`.

Contrairement aux tests unitaires, les tests d’intégration créeront des ressources réelles dans Azure, c’est pourquoi vous devez veiller à éviter les conflits de noms. (Faites particulièrement attention à certains noms uniques, comme le nom de compte de stockage). Par conséquent, la première étape de la logique de test consiste à générer un `websiteName` aléatoire à l’aide de la fonction `UniqueId()` fournie par TerraTest. Cette fonction génère un nom aléatoire contenant des lettres minuscules, des lettres majuscules ou des nombres. `tfOptions` fait en sorte que toutes les commandes de Terraform ciblent le dossier `./examples/hello-world/` et garantit également que le `website_name` soit paramétré en fonction du `websiteName` aléatoire.

Ensuite, `terraform init`, `terraform apply`, et `terraform output` sont exécutées, l’un après l’autre. Nous avons utilisé une autre fonction d’assistance `HttpGetWithCustomValidation()` fournie par Terratest pour assurer que HTML est chargé dans l’URL de sortie `homepage` retournée par `terraform output`, en comparant le code d’état HTTP Get avec `200` et en recherchant certains mots clés dans le contenu HTML. Enfin, `terraform destroy` est « promis » d’être exécuté en tirant parti de la fonctionnalité `defer` de Go.

```go
package test

import (
    "fmt"
    "strings"
    "testing"

    "github.com/gruntwork-io/terratest/modules/http-helper"
    "github.com/gruntwork-io/terratest/modules/random"
    "github.com/gruntwork-io/terratest/modules/terraform"
)

func TestIT_HelloWorldExample(t *testing.T) {
    t.Parallel()

    // Generate a random website name to prevent naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demostrate Terratest.")
    })
}
```

Pour exécuter les tests unitaires, vous devez procéder aux étapes suivantes dans la ligne de commande.

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage/test$ go test -run TestIT_HelloWorldExample
```

Vous verrez le résultat de test traditionnel de Go après environ deux minutes. Bien sûr, vous pouvez également exécuter les deux tests unitaires ainsi que les tests d’intégration en exécutant :

```shell
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ go test
```

Comme vous pouvez le voir, les tests d’intégration prennent beaucoup plus de temps que les tests unitaires (deux minutes pour un cas d’intégration comparé à une minute pour cinq cas unitaires). Mais il vous revient toujours de décider quand utiliser les tests unitaires et quand tirer parti des tests d’intégration. En règle générale, nous préférons utiliser des tests unitaires pour les logiques complexes utilisant les fonctions de Terraform HCL, tandis que nous utiliserons les tests d’intégration pour tester la perspective de bout en bout du point de vue utilisateur.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>Utilisez mage pour simplifier l’exécution des cas Terratest 

Comme vous l’avez vu, l’exécution de cas de test dans le shell n’est pas une tâche facile, car vous avez besoin d’accéder à des répertoires différents et d’exécuter des commandes différentes. C’est pour cette raison que nous intégrons le système de build dans notre projet. Dans cette section, nous allons utiliser un mage de système de build Go pour effectuer le travail.

mage requiert une seule chose : un fichier `magefile.go` dans le répertoire racine de votre projet (indiqué par `(+)` dans la figure suivante).

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 magefile.go (+)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Voici un exemple de fichier `./magefile.go`. Dans ce script de build, écrit en Go, nous avons implémenté les cinq étapes de génération :
- `Clean` : cette étape supprime tous les fichiers générés/temporaires lors des exécutions de test.
- `Format` : cette étape exécute `terraform fmt` et `go fmt` afin de formater votre base de code.
- `Unit` : cette étape exécute tous les tests unitaires (à l’aide de la convention de nommage de la fonction `TestUT_*`) sous le dossier `./test/`.
- `Integration` : similaire à `Unit`, mais exécute les tests d’intégration (`TestIT_*`) au lieu des tests unitaires.
- `Full` : cette étape exécute `Clean`, `Format`, `Unit', and `Integration`, dans cet ordre.

```go
// +build mage

// Build script to format and run tests of a Terraform module project.
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// Default target when execute `mage` in shell
var Default = Full

// A build step that runs Clean, Format, Unit and Integration in sequence
func Full() {
    mg.Deps(Unit)
    mg.Deps(Integration)
}

// A build step that runs unit tests
func Unit() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running unit tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestUT_", "-v")
}

// A build step that runs integration tests
func Integration() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running integration tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestIT_", "-v")
}

// A build step that formats both Terraform code and Go code
func Format() error {
    fmt.Println("Formatting...")
    if err := sh.RunV("terraform", "fmt", "."); err != nil {
        return err
    }
    return sh.RunV("go", "fmt", "./test/")
}

// A build step that removes temporary build/test files
func Clean() error {
    fmt.Println("Cleaning...")
    return filepath.Walk(".", func(path string, info os.FileInfo, err error) error {
        if err != nil {
            return err
        }
        if info.IsDir() && info.Name() == "vendor" {
            return filepath.SkipDir
        }
        if info.IsDir() && info.Name() == ".terraform" {
            os.RemoveAll(path)
            fmt.Printf("Removed \"%v\"\n", path)
            return filepath.SkipDir
        }
        if !info.IsDir() && (info.Name() == "terraform.tfstate" ||
            info.Name() == "terraform.tfplan" ||
            info.Name() == "terraform.tfstate.backup") {
            os.Remove(path)
            fmt.Printf("Removed \"%v\"\n", path)
        }
        return nil
    })
}
```

De manière similaire aux étapes d’exécution antérieures, vous pouvez utiliser les commandes suivantes pour exécuter une suite de tests complète :

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in magefile or test cases
GoPath/src/staticwebpage$ go fmt      # Only requied when you change the magefile
GoPath/src/staticwebpage$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage$ mage
```

N’hésitez pas à remplacer la dernière ligne de commande avec les étapes mage, par exemple `mage unit` ou `mage clean`. Vous pouvez penser qu’il y a toujours de nombreuses lignes de commande, et que c’est une bonne idée d’incorporer des commandes `dep` et `az login` dans le fichier mage. Mais nous n’expliquerons pas le code correspondant ici. Il est possible d’utiliser mage de façon plus poussée en partageant les étapes à l’aide du système de package Go, de façon à simplifier les fichiers mage sur tous vos modules en faisant référence à une implémentation commune et en déclarant les dépendances (`mg.Deps()`).

> [!NOTE]
> **Option : Définition des variables d’environnement de principal de service pour exécuter des tests d’acceptation**
> 
> Au lieu d’exécuter `az login` avant les tests, vous pouvez procéder à l’authentification Azure en définissant les variables d’environnement du principal de service. Terraform publie [une liste des noms de variables d’environnement](https://www.terraform.io/docs/providers/azurerm/index.html#testing). (Seuls les quatre premières variables d’environnement sont obligatoires.) Terraform publie également des instructions détaillées expliquant comment [obtenir la valeur de ces variables d’environnement.](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Terratest, consultez [sa page GitHub](https://github.com/gruntwork-io/terratest). Vous pouvez trouver des informations utiles sur mage sur [sa page GitHub](https://github.com/magefile/mage) et [sa page d’accueil](https://magefile.org/).
