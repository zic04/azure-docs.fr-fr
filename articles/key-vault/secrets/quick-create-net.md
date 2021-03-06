---
title: Démarrage rapide - Bibliothèque de client Azure Key Vault pour .NET (v4)
description: Découvrez comment créer, récupérer et supprimer des secrets d’un coffre de clés Azure à l’aide de la bibliothèque cliente .NET (v4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 20974367b9d4b75bb9746cd065bc7490011f37ad
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92786154"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-net-sdk-v4"></a>Démarrage rapide : Bibliothèque de client de secrets Azure Key Vault pour .NET (SDK v4)

Bien démarrer avec la bibliothèque de client de secrets Azure Key Vault pour .NET. Suivez les étapes ci-dessous pour installer le package et tester un exemple de code relatif à des tâches de base.

[Documentation de référence sur l’API](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet&preserve-view=true) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [Package (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/dotnet)
* [SDK .NET Core 3.1 ou ultérieur](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure CLI](/cli/azure/install-azure-cli)

Ce guide de démarrage rapide utilise `dotnet` et Azure CLI

## <a name="setup"></a>Programme d’installation

Ce guide de démarrage rapide utilise la bibliothèque Azure Identity avec Azure CLI pour authentifier l’utilisateur auprès des services Azure. Les développeurs peuvent également utiliser Visual Studio ou Visual Studio Code pour authentifier leurs appels. Pour plus d’informations, consultez [Authentifier le client avec la bibliothèque de client Azure Identity](/dotnet/api/overview/azure/identity-readme#authenticate-the-client&preserve-view=true).

### <a name="sign-in-to-azure"></a>Connexion à Azure

1. Exécutez la commande `login`.

    ```azurecli-interactive
    az login
    ```

    Si l’interface CLI peut ouvrir votre navigateur par défaut, elle le fait et charge une page de connexion Azure par la même occasion.

    Sinon, ouvrez une page de navigateur à l’adresse [https://aka.ms/devicelogin](https://aka.ms/devicelogin) et entrez le code d’autorisation affiché dans votre terminal.

2. Dans le navigateur, connectez-vous avec les informations d’identification de votre compte.


### <a name="create-new-net-console-app"></a>Créer une application console .NET

1. Dans un shell de commandes, exécutez la commande suivante pour créer un projet nommé `key-vault-console-app` :

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. Accédez au répertoire *key-vault-console-app* nouvellement créé, puis exécutez la commande suivante pour générer le projet :

    ```dotnetcli
    dotnet build
    ```

    La sortie de génération ne doit contenir aucun avertissement ni erreur.
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>Installer les packages

Depuis le shell de commandes, installez la bibliothèque de client de secrets Azure Key Vault pour .NET :

```dotnetcli
dotnet add package Azure.Security.KeyVault.Secrets
```

Pour ce guide de démarrage rapide, vous devez également installer la bibliothèque de client du SDK Azure pour Azure Identity :

```dotnetcli
dotnet add package Azure.Identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Créer un groupe de ressources et un coffre de clés

[!INCLUDE[Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>Accorder l’accès à votre coffre de clés

Créer une stratégie d’accès pour votre coffre de clés qui accorde une autorisation de secret à votre compte d’utilisateur

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

#### <a name="set-environment-variables"></a>Définir des variables d’environnement

Cette application utilise le nom de coffre de clés en tant que variable d’environnement appelée `KEY_VAULT_NAME`.

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS ou Linux
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Modèle objet

La bibliothèque de client de secrets Azure Key Vault pour .NET vous permet de gérer des secrets. La section [Exemples de code](#code-examples) montre comment créer un client, et définir, récupérer et supprimer un secret.

## <a name="code-examples"></a>Exemples de code

### <a name="add-directives"></a>Ajouter des directives

Ajoutez les directives suivantes en haut de *Program.cs*  :

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Authentifier et créer un client

Dans ce guide de démarrage rapide, l’utilisateur connecté est utilisé pour l’authentification auprès du coffre de clés, qui est la méthode recommandée pour le développement local. Pour les applications déployées sur Azure, l’identité managée doit être affectée à App Service ou à une machine virtuelle. Pour plus d’informations, consultez [Vue d’ensemble des identités managées](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Dans l’exemple ci-dessous, le nom de votre coffre de clés est étendu à l’URI du coffre de clés, au format « https://\<your-key-vault-name\>.vault.azure.net ». Cet exemple utilise la classe [« DefaultAzureCredential() »](/dotnet/api/azure.identity.defaultazurecredential), qui permet d’utiliser le même code dans différents environnements avec des options différentes pour fournir une identité. Pour plus d’informations, consultez [Authentification des informations d’identification Azure par défaut](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme?#defaultazurecredential). 

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Enregistrer un secret

Maintenant que l’application console est authentifiée, ajoutez un secret au coffre de clés. Pour cette tâche, utilisez la méthode [SetSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.setsecretasync). Le premier paramètre de la méthode accepte un nom pour le secret, qui est « mySecret » dans cet exemple.

```csharp
await client.SetSecretAsync(secretName, secretValue);
``````

Vous pouvez vérifier que le secret a été défini à l’aide de la commande [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show&preserve-view=true) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValue | ConvertFrom-SecureString -AsPlainText
```

### <a name="retrieve-a-secret"></a>Récupérer un secret

Vous pouvez désormais récupérer la valeur définie avec la méthode [GetSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.getsecretasync).

```csharp
var secret = await client.GetSecretAsync(secretName);
``````

Votre secret est désormais enregistré en tant que `secret.Value`.

### <a name="delete-a-secret"></a>supprimer un secret

Enfin, nous allons supprimer le secret de votre coffre de clés avec la méthode [StartDeleteSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.startdeletesecretasync).

```csharp
await client.StartDeleteSecretAsync(secretName);
``````

Vous pouvez vérifier que le secret a été effacé à l’aide de la commande [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show&preserve-view=true) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValue | ConvertFrom-SecureString -AsPlainText
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Une fois que vous n’en avez plus besoin, vous pouvez supprimer votre coffre de clés et le groupe de ressources correspondant en utilisant Azure CLI ou Azure PowerShell.

### <a name="delete-a-key-vault"></a>Supprimer un coffre de clés

```azurecli
az keyvault delete --name <your-unique-keyvault-name>
```

```azurepowershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name>
```

### <a name="purge-a-key-vault"></a>Vider un coffre de clés

```azurecli
az keyvault purge --location eastus --name <your-unique-keyvault-name>
```

```azurepowershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name> -InRemovedState -Location eastus
```

### <a name="delete-a-resource-group"></a>Supprimer un groupe de ressources

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Exemple de code

Modifiez l’application console .NET Core pour interagir avec le coffre de clés en effectuant les étapes suivantes :

1. Remplacez le code dans *Program.cs* par le code suivant :

    ```csharp
    using System;
    using Azure.Identity;
    using Azure.Security.KeyVault.Secrets;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string secretName = "mySecret";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write("Input the value of your secret > ");
                var secretValue = Console.ReadLine();
    
                Console.Write($"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...");
                await client.SetSecretAsync(secretName, secretValue);
                Console.WriteLine(" done.");
    
                Console.WriteLine("Forgetting your secret.");
                secretValue = string.Empty;
                Console.WriteLine($"Your secret is '{secretValue}'.");
    
                Console.WriteLine($"Retrieving your secret from {keyVaultName}.");
                var secret = await client.GetSecretAsync(secretName);
                Console.WriteLine($"Your secret is '{secret.Value}'.");
    
                Console.Write($"Deleting your secret from {keyVaultName} ...");
                DeleteSecretOperation operation = await client.StartDeleteSecretAsync(secretName);
                // You only need to wait for completion if you want to purge or recover the secret.
                await operation.WaitForCompletionAsync();

                await client.PurgeDeletedSecret(secretName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```

1. Exécutez la commande suivante pour exécuter l’application.

    ```dotnetcli
    dotnet run
    ```

1. Quand vous y êtes invité, entrez une valeur de secret. Par exemple, mySecretPassword.

    Une variante de la sortie suivante apparaît :

    ```console
    Input the value of your secret > mySecretPassword
    Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
    Forgetting your secret.
    Your secret is ''.
    Retrieving your secret from <your-unique-keyvault-name>.
    Your secret is 'mySecretPassword'.
    Deleting your secret from <your-unique-keyvault-name> ... done.    
    ```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un coffre de clés, stocké un secret et récupéré ce secret. Accédez à l’[application console complète dans GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app).

Pour plus d’informations sur Key Vault et comment l’intégrer à vos applications, consultez les articles suivants :

- Lire la [vue d’ensemble Azure Key Vault](../general/overview.md)
- Consultez un [Tutoriel sur l’accès à Key Vault depuis une application App Service](../general/tutorial-net-create-vault-azure-web-app.md)
- Consultez un [Tutoriel sur l’accès à Key Vault depuis une machine virtuelle](../general/tutorial-net-virtual-machine.md)
- Consulter le [Guide du développeur Azure Key Vault](../general/developers-guide.md)
- Passer en revue les [bonnes pratiques relatives à Azure Key Vault](../general/best-practices.md)