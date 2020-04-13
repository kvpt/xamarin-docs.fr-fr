---
title: Comment installer manuellement les bibliothèques de prise en charge Android requises par les packages Xamarin.Android.Support ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A9CB8CA8-8A6D-405E-B84C-A16CE452C0F7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 99571e0b62592597bb1fffdc8d3ed8336fe050b2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73026929"
---
# <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>Comment installer manuellement les bibliothèques de prise en charge Android requises par les packages Xamarin.Android.Support ?

## <a name="example-steps-for-xamarinandroidsupportv4"></a>Exemple d’étapes pour Xamarin.Android.Support.v4 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Téléchargez le forfait NuGet Xamarin.Android.Support souhaité (par exemple en l’installant avec le gestionnaire de paquets NuGet).

Utilisez-le `ildasm` pour vérifier quelle version de **android_m2repository.zip** le paquet NuGet a besoin:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr SourceUrl
```

Exemple de sortie :

```cmd
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
```

Télécharger **\_android m2repository.zip** de Google en utilisant l’URL retournée de **ildasm**. Alternativement, vous pouvez vérifier quelle version du _référentiel_ de support Android que vous avez actuellement installé dans le gestionnaire Android SDK:

!["Android SDK Manager montrant Android Support Repository version 32 installé"](install-android-support-library-images/sdk-extras.png)

Si la version correspond à celle dont vous avez besoin pour le paquet NuGet, alors vous n’avez pas à télécharger quelque chose de nouveau. Vous pouvez plutôt re-zip le répertoire **m2repository** existant qui est situé sous **les\\extras android** dans le chemin _SDK_ (comme indiqué le haut de la fenêtre Android SDK Manager).

Calculez le hachage MD5 de l’URL retournée de **l’ildasme**. Formatez la chaîne résultante pour utiliser toutes les lettres majuscules et pas d’espaces. Par exemple, `$url` ajustez la variable au besoin, puis exécutez les 2 lignes suivantes (basées sur [le code Cmd original de Xamarin.Android](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)) dans PowerShell :

```powershell
$url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"
(([System.Security.Cryptography.MD5]::Create()).ComputeHash([System.Text.Encoding]::UTF8.GetBytes($url)) | %{ $_.ToString("X02") }) -join ""
```

Exemple de sortie :

```powershell
F16A3455987DBAE5783F058F19F7FCDF
```

**Copiez\_android m2repository.zip** dans le **dossier de fermetures\\\\\\ éclairS de 000 $ LOCALAPPDATA % Xamarin.** Renommer le fichier pour utiliser le hachage MD5 de l’étape précédente de calcul du hachage MD5. Par exemple :

**%LOCALAPPDATA%\\Xamarin\\\\zips F16A3455987DBAE5783F058F19F7FCDF.zip**

(Facultatif) Dézipez le fichier en **%LOCALAPPDATA%\\\\Xamarin Xamarin.Android.Support.v4\\23.4.0.0\\contenu\\ ** (création **d’un contenu\\m2repository** sous-direction). Si vous sautez cette étape, alors la première construction qui utilise la bibliothèque prendra un peu plus longtemps, car il devra terminer cette étape.
Le numéro de version de la sous-direction **(23.4.0.0** dans cet exemple) n’est pas tout à fait le même que la version du paquet NuGet. Vous pouvez `ildasm` utiliser pour trouver le numéro de version correct:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr /C:"string 'Version'"
```

Exemple de sortie :

```cmd
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
```

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Téléchargez le forfait NuGet Xamarin.Android.Support souhaité (par exemple en l’installant avec le gestionnaire de paquets NuGet).

Double-cliquez sur l’assemblage _Xamarin.Android.Support.v4_ sous la section _Références_ du projet Android dans Visual Studio pour Mac pour ouvrir l’assemblage dans le navigateur d’assemblage. Assurez-vous que _l’abandon de la langue_ est réglé sur _C et_ sélectionnez l’assemblage _Xamarin.Android.Support.v4_ de haut niveau de l’arbre de navigation Assembly Browser. Localiser `SourceUrl` la propriété sous `IncludeAndroidResourcesFrom` `JavaLibraryReference` l’un des attributs ou les attributs :

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

Télécharger **\_android m2repository.zip** de `SourceUrl` Google en utilisant le retour de **l’ildasm**. Alternativement, vous pouvez vérifier quelle version du _référentiel_ de support Android que vous avez actuellement installé dans le gestionnaire Android SDK:

!["Android SDK Manager montrant Android Support Repository version 32 installé"](install-android-support-library-images/sdk-extras.png)

Si la version correspond à celle dont vous avez besoin pour le paquet NuGet, alors vous n’avez pas à télécharger quelque chose de nouveau. Vous pouvez plutôt re-zip le répertoire **m2repository** existant qui est situé sous **extras / androïde** dans le _chemin SDK_ (comme indiqué le haut de la fenêtre Android SDK Manager).

Calculez le hachage MD5 de l’URL retournée de **l’ildasme**. Formatez la chaîne résultante pour utiliser toutes les lettres majuscules et pas d’espaces. Par exemple, ajustez la chaîne d’URL au besoin, puis exécutez la commande suivante dans une invite de commande **Terminal.app** :

```bash
echo -n "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip" | md5 | tr '[:lower:]' '[:upper:]'
```

Une autre option `csharp` est d’utiliser l’interprète pour exécuter [le même code C que Xamarin.Android lui-même utilise](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208).
Pour ce faire, `url` ajustez la variable au besoin, puis exécutez la commande suivante dans une invite de commande **Terminal.app** :

```bash
csharp -e 'var url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"; string.Concat((System.Security.Cryptography.MD5.Create().ComputeHash(System.Text.Encoding.UTF8.GetBytes(url))).Select(b => b.ToString("X02")))'
```

Exemple de sortie :

```bash
F16A3455987DBAE5783F058F19F7FCDF
```

**Copiez\_android m2repository.zip** à la **$HOME/.local/share/Xamarin/zips/folder.** Renommer le fichier pour utiliser le hachage MD5 de l’étape précédente de calcul du hachage MD5. Par exemple :

**$HOME/.local/share/Xamarin/zips/F16A3455987DBAE5783F058F19F7FCDF.zip**

(Facultatif) Décompressez le fichier dans : 

**$HOME/.local/share/Xamarin/Xamarin.Android.Support.v4/23.4.0.0/content/**

(création d’une sous-direction du **contenu/m2repository).** Si vous sautez cette étape, alors la première construction qui utilise la bibliothèque prendra un peu plus longtemps, car il devra terminer cette étape.

Le numéro de version de la sous-direction **(23.4.0.0** dans cet exemple) n’est pas tout à fait le même que la version du paquet NuGet. Comme dans **l’étape ildasm** plus tôt, vous pouvez utiliser le navigateur d’assemblage dans Visual Studio pour Mac pour trouver le numéro de version correct. Recherchez `Version` la propriété sous `IncludeAndroidResourcesFrom` `JavaLibraryReference` l’un des attributs ou des attributs :

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

-----

## <a name="additional-references"></a>Références supplémentaires

- [Bug 43245](https://bugzilla.xamarin.com/show_bug.cgi?id=43245) - Inexact "Télécharger échoué. S’il vous plaît télécharger {0} et le mettre à l’annuaire. {1} et "S’il{0}vous plaît installer paquet: ' ' disponible dans l’installateur SDK" messages d’erreur liés à Xamarin.Android.Support paquets

### <a name="next-steps"></a>Étapes suivantes

Ce document traite du comportement actuel en août 2016. La technique décrite dans ce document ne fait pas partie de la suite d’essai stable pour Xamarin, de sorte qu’il pourrait se briser à l’avenir.

Pour plus d’aide, pour nous contacter, ou si ce problème reste même après avoir utilisé les informations ci-dessus, s’il vous plaît voir [quelles options de soutien sont disponibles pour Xamarin?](~/cross-platform/troubleshooting/support-options.md) pour des informations sur les options de contact, suggestions, ainsi que la façon de déposer un nouveau bogue si nécessaire.
