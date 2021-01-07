---
title: 'Xamarin.Essentials: Sélecteur de fichiers'
description: La classe FilePicker dans Xamarin.Essentials permet à un utilisateur de sélectionner un ou plusieurs fichiers à partir de l’appareil.
ms.assetid: 00bdbd57-56b1-47ca-8abe-cebe1b01f61a
author: jamesmontemagno
ms.author: jamont
ms.date: 01/04/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 797f7e80ac389776f921021ef3b1a9ab16b3f53b
ms.sourcegitcommit: 995ee23d93e08dceb8754cc6c682cd2f4594345b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972329"
---
# <a name="no-locxamarinessentials-file-picker"></a>Xamarin.Essentials: Sélecteur de fichiers

La classe **FilePicker** permet à un utilisateur de sélectionner un ou plusieurs fichiers à partir de l’appareil.

## <a name="get-started"></a>Prendre en main

[!include[](~/essentials/includes/get-started.md)]

Pour accéder à la fonctionnalité **FilePicker** , la configuration spécifique à la plateforme suivante est requise.

# <a name="android"></a>[Android](#tab/android)

L’autorisation `ReadExternalStorage` est obligatoire, et doit être configurée dans le projet Android. Vous pouvez l’ajouter des façons suivantes :

Ouvrez le fichier **AssemblyInfo.cs** sous le dossier **Propriétés** et ajoutez :

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.ReadExternalStorage)]
```

OU mettez à jour le manifeste Android :

Ouvrez le fichier **AndroidManifest.xml** sous le dossier **Propriétés** , puis ajoutez le code suivant dans le nœud **manifeste** .

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

Vous pouvez également cliquer avec le bouton droit sur le projet Android, et ouvrir les propriétés du projet. Sous le **manifeste Android** , recherchez la zone **autorisations requises :** , puis cochez cette autorisation. Cela entraîne la mise à jour automatique du fichier **AndroidManifest.xml**.

# <a name="ios"></a>[iOS](#tab/ios)

Aucune configuration supplémentaire n’est requise.

# <a name="uwp"></a>[UWP](#tab/uwp)

Aucune configuration supplémentaire n’est requise.

-----

> [!TIP]
> Toutes les méthodes doivent être appelées sur le thread d’interface utilisateur, car les vérifications et les demandes d’autorisations sont gérées automatiquement par Xamarin.Essentials .

## <a name="pick-file"></a>Sélectionner un fichier

`FilePicker.PickAsync()` la méthode permet à votre utilisateur de choisir un fichier à partir de l’appareil. Vous pouvez `PickOptions` spécifier un autre nom spécifique lors de l’appel de la méthode, ce qui vous permet de spécifier le titre à afficher et les types de fichiers que l’utilisateur est autorisé à sélectionner. Par défaut 

```csharp
async Task<FileResult> PickAndShow(PickOptions options)
{
    try
    {
        var result = await FilePicker.PickAsync();
        if (result != null)
        {
            Text = $"File Name: {result.FileName}";
            if (result.FileName.EndsWith("jpg", StringComparison.OrdinalIgnoreCase) ||
                result.FileName.EndsWith("png", StringComparison.OrdinalIgnoreCase))
            {
                var stream = await result.OpenReadAsync();
                Image = ImageSource.FromStream(() => stream);
            }
        }
    }
    catch (Exception ex)
    {
        // The user canceled or something went wrong
    }
}
```

Les types de fichiers par défaut sont fournis avec `FilePickerFileType.Images` , `FilePickerFileType.Png` et `FilePickerFilerType.Videos` . Vous pouvez spécifier des types de fichiers personnalisés lors de la création du `PickOptions` et ils peuvent être personnalisés par plateforme. Par exemple, voici comment spécifier des types de fichiers Comic spécifiques :

```csharp
var customFileType =
    new FilePickerFileType(new Dictionary<DevicePlatform, IEnumerable<string>>
    {
        { DevicePlatform.iOS, new[] { "public.my.comic.extension" } }, // or general UTType values
        { DevicePlatform.Android, new[] { "application/comics" } },
        { DevicePlatform.UWP, new[] { ".cbr", ".cbz" } },
        { DevicePlatform.Tizen, new[] { "*/*" } },
        { DevicePlatform.macOS, new[] { "cbr", "cbz" } }, // or general UTType values
    });
var options = new PickOptions
{
    PickerTitle = "Please select a comic file",
    FileTypes = customFileType,
};
```

## <a name="pick-multiple-files"></a>Sélectionner plusieurs fichiers

Si vous souhaitez que votre utilisateur sélectionne plusieurs fichiers, vous pouvez appeler la `FilePicker.PickMultipleAsync()` méthode. Elle prend également `PickOptions` comme paramètre pour spécifier des informations supplémentaires. Les résultats sont les mêmes que `PickAsync` , mais au lieu d’un unique, `FileResult` un `IEnumerable<FileResult>` est retourné qui peut être itéré.


## <a name="platform-differences"></a>Différences entre les plateformes

# <a name="android"></a>[Android](#tab/android)

- L’URI du fichier résultant peut ne pas être conservé entre les redémarrages.

# <a name="ios"></a>[iOS](#tab/ios)

Aucune différence entre les plateformes.

# <a name="uwp"></a>[UWP](#tab/uwp)

Aucune différence entre les plateformes.

## <a name="api"></a>API

- [Code source FilePicker](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/FilePicker)
- [Documentation de l’API FilePicker](xref:Xamarin.Essentials.FilePicker)
