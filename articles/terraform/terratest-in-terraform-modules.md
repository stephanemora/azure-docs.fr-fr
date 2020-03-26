---
title: Tutoriel - Tester des modules Terraform dans Azure à l’aide de Terratest
description: Découvrez comment utiliser Terratest pour tester vos modules Terraform.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 687a793af2b9b75efe463b042d121c32f18974d6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79370795"
---
# <a name="tutorial-test-terraform-modules-in-azure-using-terratest"></a>Tutoriel : Tester des modules Terraform dans Azure à l’aide de Terratest

> [!NOTE]
> L’exemple de code dans cet article ne fonctionne pas avec la version 0.12 (et supérieure).

Vous pouvez utiliser les modules Azure Terraform pour créer des composants réutilisables, composables et testables. Les modules Terraform comprennent l’encapsulation qui permet d’implémenter l’infrastructure comme des processus de code.

Lorsque vous créez des modules Terraform, il est important d’implémenter l’assurance qualité. Malheureusement, il existe peu de documentation expliquant comment créer des tests unitaires et des tests d’intégration dans les modules Terraform. Ce tutoriel présente une infrastructure de test ainsi que les bonnes pratiques que nous avons adoptées pour la création de nos [modules Azure Terraform](https://registry.terraform.io/browse?provider=azurerm).

Nous avons étudié toutes les infrastructures de test les plus populaires et nous avons choisi [Terratest](https://github.com/gruntwork-io/terratest) pour tester nos modules Terraform. Terratest est implémenté sous forme de bibliothèque Go. Terratest fournit une collection de fonctions et de modèles d’assistance pour les tâches de test d’infrastructure courantes, telles que les requêtes HTTP et l’utilisation d’une connexion SSH pour accéder à une machine virtuelle. Le tableau suivant liste certains des principaux avantages que présente l’utilisation de Terratest :

- **Il fournit des outils d’assistance pratiques pour contrôler l’infrastructure.** Cette fonctionnalité est utile lorsque vous souhaitez vérifier l’infrastructure réelle dans l’environnement réel.
- **La structure de dossiers est clairement organisée.** Vos cas de test sont clairement organisés et suivront la [structure de dossiers standard des modules Terraform](https://www.terraform.io/docs/modules/create.html#standard-module-structure).
- **Tous les cas de test sont écrits en Go.** La plupart des développeurs qui utilisent Terraform sont des développeurs Go. Si vous êtes un développeur Go, vous n’êtes pas obligé d’apprendre un autre langage de programmation pour utiliser Terratest. En outre, les seules dépendances nécessaires pour exécuter des cas de test dans Terratest sont Go et Terraform.
- **Cette infrastructure est très extensible.** Vous pouvez facilement étendre des fonctions supplémentaires sur Terratest, telles que les fonctionnalités spécifiques à Azure.

## <a name="prerequisites"></a>Prérequis

Ce guide pratique peut être utilisé avec n’importe quelle plateforme. Vous pouvez exécuter ses exemples de code sur Windows, Linux ou MacOS. 

Avant de commencer, installez les logiciels suivants :

- **Langage de programmation Go** : les cas de test Terraform sont écrits dans le langage [Go](https://golang.org/dl/).
- **dep** : [dep](https://github.com/golang/dep#installation) est un outil de gestion de dépendance pour Go.
- **Azure CLI** : [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) est un outil en ligne de commande pour la gestion des ressources Azure. (Terraform prend en charge l’authentification auprès d’Azure via un principal de service ou [via l’interface Azure CLI](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html)).
- **Mage** : nous utilisons l’[exécutable Mage](https://github.com/magefile/mage/releases) pour vous montrer comment simplifier l’exécution des cas Terratest. 

## <a name="create-a-static-webpage-module"></a>Créer un module de page Web statique

Dans ce tutoriel, vous allez créer un module Terraform qui provisionne une page web statique en chargeant un fichier HTML unique dans Azure Storage Blob. Ce module permet aux utilisateurs du monde entier d’accéder à la page web via une URL qui retourne le module.

> [!NOTE]
> Créez tous les fichiers qui sont décrits dans cette section, à l’emplacement [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH).

Tout d’abord, créez un dossier nommé `staticwebpage` dans le dossier `src` de GoPath. La structure générale des dossiers de ce tutoriel est illustrée dans l’exemple suivant. Les fichiers marqués par un astérisque `(*)` sont d’un intérêt majeur pour cette section.

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

Le module de page web statique accepte trois entrées. Les entrées sont déclarées dans `./variables.tf` :

```hcl
variable "location" {
  description = "The Azure region in which to create all resources."
}

variable "website_name" {
  description = "The website name to use to create related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static home page HTML in your local file system."
  default     = "index.html"
}
```

Comme nous l’avons mentionné précédemment dans cet article, ce module génère également une URL qui est déclarée dans `./outputs.tf` :

```hcl
output "homepage_url" {
  value = azurerm_storage_blob.homepage.url
}
```

La logique principale du module provisionne quatre ressources :
- **Groupe de ressources** : le nom du groupe de ressources correspond à l’entrée `website_name` à laquelle est ajouté `-staging-rg`.
- **Compte de stockage** : le nom du compte de stockage correspond à l’entrée `website_name` à laquelle est ajouté `data001`. Afin de respecter les limites de longueur pour le nom du compte de stockage, le module supprime tous les caractères spéciaux et utilise des lettres minuscules dans l’intégralité du nom.
- **Conteneur de nom fixe** : le conteneur est nommé `wwwroot` et est créé dans le compte de stockage.
- **Fichier HTML** : le fichier HTML est lu dans l’entrée `html_path`, puis chargé dans `wwwroot/index.html`.

La logique de module de page Web statique est implémentée dans `./main.tf` :

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = var.location
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = azurerm_resource_group.main.name
  location                 = azurerm_resource_group.main.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = azurerm_resource_group.main.name
  storage_account_name  = azurerm_storage_account.main.name
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = azurerm_resource_group.main.name
  storage_account_name   = azurerm_storage_account.main.name
  storage_container_name = azurerm_storage_container.main.name
  source                 = var.html_path
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>Test unitaire

Terratest est conçu pour les tests d’intégration. À cette fin, Terratest provisionne des ressources réelles dans un environnement réel. Parfois, ces travaux de test d’intégration peuvent devenir particulièrement volumineux, notamment si vous avez un grand nombre de ressources à provisionner. La logique qui convertit les noms de compte de stockage dans la section précédente en est un bon exemple. 

Toutefois, nous n’avons pas réellement besoin de provisionner des ressources. Nous voulons seulement être sûrs que la logique de conversion des noms est correcte. Grâce à la flexibilité de Terratest, nous pouvons utiliser des tests unitaires. Les tests unitaires sont des cas de test exécutés localement (même s’ils nécessitent un accès Internet). Les cas de test unitaire exécutent les commandes `terraform init` et `terraform plan` pour analyser la sortie de `terraform plan` et rechercher les valeurs d’attributs à comparer.

Le reste de cette section décrit comment utiliser Terratest pour implémenter un test unitaire et s’assurer que la logique utilisée pour la conversion des noms de comptes de stockage est correcte. Nous nous intéresserons uniquement aux fichiers marqués d’un astérisque `(*)`.

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

Tout d’abord, nous utilisons un fichier HTML vide nommé `./test/fixtures/storage-account-name/empty.html` comme espace réservé.

Le fichier `./test/fixtures/storage-account-name/main.tf` correspond à la structure du cas de test. Il accepte une entrée (`website_name`) qui est également l’entrée des tests unitaires. La logique est la suivante :

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = var.website_name
  html_path    = "empty.html"
}
```

Le composant principal est l’implémentation des tests unitaires dans `./test/storage_account_name_unit_test.go`.

Si vous êtes un développeur Go, vous remarquerez que le test unitaire correspond à la signature d’une fonction de test classique Go, du fait qu’elle accepte un argument de type `*testing.T`.

Dans le corps du test unitaire, nous disposons de cinq cas définis dans la variable `testCases` (`key` comme entrée, et `value` comme sortie attendue). Pour chaque cas de test unitaire, nous allons d’abord exécuter `terraform init` en ciblant le dossier de fixture de test (`./test/fixtures/storage-account-name/`). 

Ensuite, une commande `terraform plan` avec une entrée de cas de test spécifique (regardez la définition de `website_name` dans `tfOptions`) enregistre le résultat dans `./test/fixtures/storage-account-name/terraform.tfplan` (non listé dans la structure générale des dossiers).

Ensuite, ce fichier de résultat est analysé dans une structure lisible par code à l’aide de l’analyseur de plan officiel de Terraform.

Nous allons maintenant rechercher les attributs qui nous intéressent (dans ce cas, le `name` du `azurerm_storage_account`), puis comparer les résultats à la sortie attendue :

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
        // Specify the test case folder and "-var" options
        tfOptions := &terraform.Options{
            TerraformDir: "./fixtures/storage-account-name",
            Vars: map[string]interface{}{
                "website_name": input,
            },
        }

        // Terraform init and plan only
        tfPlanOutput := "terraform.tfplan"
        terraform.Init(t, tfOptions)
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions, "plan", "-out="+tfPlanOutput)...)

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

Pour exécuter les tests unitaires, effectuez les étapes suivantes sur la ligne de commande :

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in test cases
cd test
go fmt
go test -run TestUT_StorageAccountName
```

En général, les résultats de test Go sont retournés au bout d’une minute environ.

### <a name="integration-test"></a>Test d’intégration

Contrairement aux tests unitaires, les tests d’intégration doivent provisionner des ressources dans un environnement réel, pour une perspective de bout en bout. Terratest est très efficace pour ce type de tâches. 

Parmi les bonnes pratiques à employer pour les modules Terraform figure l’installation du dossier `examples`. Le dossier `examples` contient des exemples de bout en bout. Pour ne pas avoir à utiliser des données réelles, nous vous conseillons d’utiliser ces exemples pour vos tests d’intégration. Dans cette section, nous allons nous concentrer sur les trois fichiers qui sont signalés par un astérisque `(*)` dans la structure de dossiers suivante :

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

Commençons par les exemples. Un exemple de dossier appelé `hello-world/` est créé dans le dossier `./examples/`. Nous fournissons une page HTML simple à charger : `./examples/hello-world/index.html`.

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demonstrate Terratest.</p>
</body>
</html>
```

L’exemple Terraform `./examples/hello-world/main.tf` est similaire à celui illustré dans le test unitaire. Il existe cependant une différence importante : l’exemple affiche également l’URL du code HTML chargé sous la forme d’une page web nommée `homepage`.

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = var.website_name
}

output "homepage" {
  value = module.staticwebpage.homepage_url
}
```

Une nouvelle fois, nous utilisons Terratest et les fonctions de test Go classiques dans le fichier de test d’intégration `./test/hello_world_example_test.go`.

Contrairement aux tests unitaires, les tests d’intégration créent des ressources réelles dans Azure. Vous devez donc veiller à éviter les conflits de noms (faites particulièrement attention à certains noms uniques, comme le nom de compte de stockage). Par conséquent, la première étape de la logique de test consiste à générer un `websiteName` aléatoire à l’aide de la fonction `UniqueId()` fournie par Terratest. Cette fonction génère un nom aléatoire constitué de lettres minuscules, de lettres majuscules ou de nombres. `tfOptions` crée toutes les commandes Terraform qui ciblent le dossier `./examples/hello-world/`. Il garantit également que `website_name` est défini sur le `websiteName` aléatoire.

Ensuite, `terraform init`, `terraform apply`, et `terraform output` sont exécutées, l’un après l’autre. Nous utilisons une autre fonction d’assistance (`HttpGetWithCustomValidation()`) qui est fournie par Terratest. Nous utilisons la fonction d’assistance pour garantir que le code HTML sera chargé dans l’URL de sortie `homepage` qui est retournée par `terraform output`. Nous comparons le code d’état HTTP GET à `200` et recherchons des mots clés dans le contenu HTML. Enfin, `terraform destroy` est « promis » d’être exécuté en tirant parti de la fonctionnalité `defer` de Go.

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

    // Generate a random website name to prevent a naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify the test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output, and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demonstrate Terratest.")
    })
}
```

Pour exécuter les tests d’intégration, effectuez les étapes suivantes sur la ligne de commande :

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in test cases
cd test
go fmt
go test -run TestIT_HelloWorldExample
```

En général, les résultats de test Go sont retournés au bout de deux minutes environ. Vous pouvez également exécuter à la fois les tests unitaires et les tests d’intégration en exécutant ces commandes :

```shell
go fmt
go test
```

Les tests d’intégration prennent beaucoup plus de temps que les tests unitaires (deux minutes pour un cas d’intégration comparé à une minute pour cinq cas unitaires). Toutefois, c’est vous qui décidez d’utiliser des tests unitaires ou des tests d’intégration dans votre scénario. En règle générale, nous préférons utiliser des tests unitaires pour les logiques complexes, à l’aide des fonctions HCL de Terraform. Nous utilisons généralement les tests d’intégration pour la perspective de bout en bout d’un utilisateur.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>Utilisez mage pour simplifier l’exécution des cas Terratest 
L’exécution de cas de test dans Azure Cloud Shell exige d’exécuter différentes commandes dans différents répertoires. Pour rendre ce processus plus efficace, nous présentons le système de génération dans notre projet. Dans cette section, nous allons utiliser un système de génération Go (Mage) pour effectuer le travail.

Mage ne nécessite qu’une seule chose : `magefile.go`, qui se trouve dans le répertoire racine de votre projet (indiqué par `(+)` dans l’exemple suivant) :

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

Voici un exemple de `./magefile.go`. Dans ce script de génération, écrit en Go, nous avons implémenté cinq étapes de génération :
- `Clean`: cette étape supprime tous les fichiers générés et les fichiers temporaires générés au cours des tests.
- `Format`: cette étape exécute `terraform fmt` et `go fmt` afin de formater votre base de code.
- `Unit`: cette étape exécute tous les tests unitaires (en utilisant la convention de nommage des fonctions `TestUT_*`) sous le dossier `./test/`.
- `Integration`: cette étape est similaire à `Unit`, sauf qu’au lieu d’exécuter des tests unitaires, elle exécute des tests d’intégration (`TestIT_*`).
- `Full`: cette étape exécute `Clean`, `Format`, `Unit` et `Integration` dans cet ordre.

```go
// +build mage

// Build a script to format and run tests of a Terraform module project
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// The default target when the command executes `mage` in Cloud Shell
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

// A build step that removes temporary build and test files
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

Vous pouvez utiliser les commandes suivantes pour exécuter une suite de tests complète. Le code est similaire à celui des étapes que nous avons utilisées dans la section précédente. 

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in magefile or test cases
go fmt      # Only required when you change the magefile
mage
```

Vous pouvez remplacer la dernière ligne de commande par les étapes Mage supplémentaires. Par exemple, vous pouvez utiliser `mage unit` ou `mage clean`. Il est recommandé d’incorporer les commandes `dep` et `az login` dans le fichier magefile. Ce code n’est pas montré ici. 

Avec Mage, vous pouvez également partager les étapes en utilisant le système de package Go. Dans ce cas, vous pouvez simplifier les fichiers mage sur l’ensemble de vos modules en référençant une implémentation commune et en déclarant des dépendances (`mg.Deps()`).

**Facultatif : définissez des variables d’environnement de principal de service pour exécuter des tests d’acceptation**
 
Au lieu d’exécuter `az login` avant les tests, vous pouvez procéder à l’authentification Azure en définissant les variables d’environnement du principal de service. Terraform publie la [liste des noms de variables d’environnement](https://www.terraform.io/docs/providers/azurerm/index.html#testing). (Seuls les quatre premières variables d’environnement sont obligatoires.) Terraform publie également des instructions détaillées expliquant comment [obtenir la valeur de ces variables d’environnement](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Page GitHub Terratest](https://github.com/gruntwork-io/terratest).