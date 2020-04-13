---
title: Localisation des cordes et de l’image dans Xamarin.Forms
description: Les applications Xamarin.Forms peuvent être localisées à l’aide de fichiers ressources .NET.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/01/2019
ms.openlocfilehash: bf99873d88a69a715cdf7969ad94afd66372b5e3
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "74135330"
---
# <a name="xamarinforms-string-and-image-localization"></a>Localisation des cordes et des images Xamarin.Forms

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization)

La localisation est le processus d’adaptation d’une application pour répondre aux exigences linguistiques ou culturelles spécifiques d’un marché cible. Pour parvenir à la localisation, le texte et les images d’une application peuvent devoir être traduits en plusieurs langues. Une application localisée affiche automatiquement le texte traduit en fonction des paramètres de culture de l’appareil mobile :

![Captures d’écran de l’application de localisation sur iOS et Android](text-images/localizationdemo-screenshots.png)

Le cadre .NET comprend un mécanisme intégré pour la localisation des applications à l’aide [de fichiers de ressources Resx](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps). Un fichier de ressources stocke le texte et d’autres contenus comme paires de noms/valeurs qui permettent à l’application de récupérer du contenu pour une clé fournie. Les fichiers de ressources permettent de séparer le contenu localisé du code d’application.

L’utilisation de fichiers de ressources pour localiser les applications Xamarin.Forms vous oblige à effectuer les étapes suivantes :

1. [Créez des fichiers Resx](#create-resx-files) contenant du texte traduit.
1. [Spécifier la culture par défaut](#specify-the-default-culture) dans le projet partagé.
1. [Localiser le texte dans Xamarin.Forms](#localize-text-in-xamarinforms).
1. [Localiser les images](#localize-images) en fonction des paramètres de culture de chaque plate-forme.
1. [Localiser le nom de l’application](#localize-the-application-name) sur chaque plate-forme.
1. [Testez la localisation](#test-localization) sur chaque plate-forme.

## <a name="create-resx-files"></a>Créer des fichiers Resx

Les fichiers de ressources sont des fichiers XML avec une extension **.resx** qui sont compilés dans les fichiers de ressources binaires (.ressources) pendant le processus de construction. Visual Studio 2019 génère une classe qui fournit une API utilisée pour récupérer des ressources. Une application localisée contient généralement un fichier de ressources par défaut avec toutes les chaînes utilisées dans l’application, ainsi que des fichiers de ressources pour chaque langue prise en charge. L’application d’échantillon a un dossier **Resx** dans le projet partagé qui contient les fichiers de ressources, et son fichier de ressources par défaut appelé **AppResources.resx**.

Les fichiers de ressources contiennent les informations suivantes pour chaque élément :

- **Le nom** spécifie la clé utilisée pour accéder au texte dans le code.
- **La valeur** spécifie le texte traduit.
- **Le commentaire** est un champ facultatif contenant des informations supplémentaires.

::: zone pivot="windows"

Un fichier de ressources est ajouté avec le dialogue **Add New Item** dans Visual Studio 2019 :

![Ajouter une nouvelle ressource dans Visual Studio 2019](text-images/pc-add-resource-file.png)

Une fois le fichier ajouté, des lignes peuvent être ajoutées pour chaque ressource de texte :

![Spécifier les ressources textuelles par défaut dans un fichier .resx](text-images/pc-default-strings.png)

Le paramètre De baisse **Access Modère** détermine comment Visual Studio génère la classe utilisée pour accéder aux ressources. Définir le modificateur d’accès au **public** ou **à l’interne** dans une classe générée avec le niveau d’accessibilité spécifié. La définition de la génération de code Access **Modérez sans code** ne génère pas de fichier de classe. Le fichier de ressources par défaut doit être configuré pour générer un fichier de classe, ce qui entraîne l’ajout d’un fichier avec l’extension **.designer.cs** au projet.

Une fois le fichier de ressources par défaut créé, des fichiers supplémentaires peuvent être créés pour chaque culture que l’application prend en charge. Chaque fichier de ressources supplémentaire devrait inclure la culture de traduction dans le nom de fichier et devrait avoir le **modificateur d’accès** réglé à **aucune génération de code**. 

Au moment de l’exécution, l’application tente de résoudre une demande de ressources par ordre de spécificité. Par exemple, si la culture de l’appareil est **en-US** l’application recherche des fichiers de ressources dans cet ordre :

1. AppResources.fr-US.resx
1. AppResources.fr.resx
1. AppResources.resx (par défaut)

La capture d’écran suivante montre un fichier de traduction espagnol nommé **AppResources.es.cs**:

![Spécifier les ressources textuelles par défaut dans un fichier .resx](text-images/pc-spanish-strings.png)

Le fichier de traduction utilise les mêmes valeurs **nominatives** spécifiées dans le fichier par défaut, mais contient des chaînes en espagnol dans la colonne **De** valeur. En outre, le **modificateur d’accès** est réglé à **aucune génération de code**.

::: zone-end
::: zone pivot="macos"

Un fichier de ressources est ajouté avec le dialogue **Add New File** dans Visual Studio 2019 pour Mac :

![Ajouter une nouvelle ressource en studio visuel 2019 pour Mac](text-images/mac-add-resource-file.png)

Une fois qu’un fichier de ressources par `data` défaut `root` a été créé, le texte peut être ajouté en créant des éléments dans l’élément du fichier de ressources :

```xml
<?xml version="1.0" encoding="utf-8"?>
<root>
    ...
    <data name="AddButton" xml:space="preserve">
        <value>Add Note</value>
    </data>
    <data name="NotesLabel" xml:space="preserve">
        <value>Notes:</value>
    </data>
    <data name="NotesPlaceholder" xml:space="preserve">
        <value>e.g. Get Milk</value>
    </data>
</root>
```

Un fichier de classe **.designer.cs** peut être créé en définissant une propriété **Custom Tool** dans les options de fichiers de ressources :

![Outil personnalisé spécifié dans les propriétés d’un fichier de ressources](text-images/mac-resx-properties.png)

La définition de l’outil **personnalisé** à **PublicResXFileCodeGenerator** donnera lieu à une classe générée avec `public` accès. La configuration de l’outil **personnalisé** à **InternalResXFileCodeGenerator** donnera lieu à une classe générée avec `internal` accès. Une valeur **d’outil personnalisé** vide ne générera pas de classe. Le nom de classe généré correspondra au nom du fichier de ressources. Par exemple, le fichier **AppResources.resx** entraînera `AppResources` la création d’une classe dans un fichier appelé **AppResources.designer.cs**.

Des fichiers de ressources supplémentaires peuvent être créés pour chaque culture prise en charge. Chaque fichier linguistique doit inclure la culture de traduction dans le nom de fichier afin qu’un fichier ciblant **es-MX** soit nommé **AppResources.es-MX.resx**.

Au moment de l’exécution, l’application tente de résoudre une demande de ressources par ordre de spécificité. Par exemple, si la culture de l’appareil est **en-US** l’application recherche des fichiers de ressources dans cet ordre :

1. AppResources.fr-US.resx
1. AppResources.fr.resx
1. AppResources.resx (par défaut)

Les fichiers de traduction linguistique doivent avoir les mêmes valeurs **nomin** lesquelles spécifiés que le fichier par défaut. Le XML suivant montre le fichier de traduction espagnol nommé **AppResources.es.resx**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<root>
    ...
    <data name="NotesLabel" xml:space="preserve">
        <value>Notas:</value>
    </data>
    <data name="NotesPlaceholder" xml:space="preserve">
        <value>por ejemplo . comprar leche</value>
    </data>
    <data name="AddButton" xml:space="preserve">
        <value>Agregar nuevo elemento</value>
    </data>
</root>
```

::: zone-end

## <a name="specify-the-default-culture"></a>Spécifier la culture par défaut

Pour que les fichiers de ressources `NeutralResourcesLanguage` fonctionnent correctement, l’application doit avoir une spécifiée. Dans le projet partagé, le **fichier AssemblyInfo.cs** doit être personnalisé pour spécifier la culture par défaut. Le code suivant montre `NeutralResourcesLanguage` comment définir le à **en-US** dans le fichier **AssemblyInfo.cs:**

```csharp
using System.Resources;

[assembly: NeutralResourcesLanguage("en-US")]
```

> [!WARNING]
> Si vous ne `NeutralResourcesLanguage` spécifiez pas l’attribut, la `ResourceManager` classe renvoie des `null` valeurs pour toutes les cultures sans fichier de ressources spécifique. Lorsque la culture par `ResourceManager` défaut est spécifiée, les retours résultent du fichier Resx par défaut pour les cultures non supportées. Par conséquent, il est recommandé `NeutralResourcesLanguage` que vous spécifiiez toujours le de sorte que le texte est affiché pour les cultures non-assistées.

Une fois qu’un fichier de ressources par défaut a été créé et que la culture par défaut spécifiée dans le fichier **AssemblyInfo.cs,** l’application peut récupérer des chaînes localisées au moment de l’exécution.

Pour plus d’informations sur les fichiers de ressources, voir [Créer des fichiers de ressources pour les applications .NET](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps).

## <a name="localize-text-in-xamarinforms"></a>Localiser le texte dans Xamarin.Forms

Le texte est localisé dans Xamarin.Forms à l’aide de la classe générée. `AppResources` Cette catégorie est nommée en fonction du nom de fichier de ressources par défaut. Étant donné que le fichier de ressources du projet d’échantillon est nommé **AppResources.cs,** Visual Studio génère une classe correspondante appelée `AppResources`. Les propriétés statiques sont générées dans la `AppResources` classe pour chaque rangée dans le fichier de ressources. Les propriétés statiques suivantes sont `AppResources` générées dans la classe de l’application de l’échantillon :

- AddButton
- NotesLabel
- NotesPlaceholder

L’accès à ces valeurs sous le titre [x:Static](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md#the-xstatic-markup-extension) properties permet d’afficher du texte localisé dans XAML :

```xaml
<ContentPage ...
             xmlns:resources="clr-namespace:LocalizationDemo.Resx">
    <Label Text="{x:Static resources:AppResources.NotesLabel}" />
    <Entry Placeholder="{x:Static resources:AppResources.NotesPlaceholder}" />
    <Button Text="{x:Static resources:AppResources.AddButton}" />
</ContentPage>
```

Le texte localisé peut également être récupéré dans le code :

```csharp
public LocalizedCodePage()
{
    Label notesLabel = new Label
    {
        Text = AppResources.NotesLabel,
        // ...
    };
    
    Entry notesEntry = new Entry
    {
        Placeholder = AppResources.NotesPlaceholder,
        //...
    };
    
    Button addButton = new Button
    {
        Text = AppResources.AddButton,
        // ...
    };
    
    Content = new StackLayout
    {
        Children = {
            notesLabel,
            notesEntry,
            addButton
        }
    };
}
```

Les propriétés `AppResources` de la classe `System.Globalization.CultureInfo.CurrentUICulture` utilisent la valeur actuelle du fichier de ressources culturelles pour récupérer les valeurs.

## <a name="localize-images"></a>Localiser les images

En plus de stocker du texte, les fichiers Resx sont capables de stocker plus que du texte, ils peuvent également stocker des images et des données binaires. Cependant, les appareils mobiles ont une gamme de tailles d’écran et de densités et chaque plate-forme mobile a des fonctionnalités pour afficher des images dépendantes de la densité. Par conséquent, la fonctionnalité de localisation d’image de plate-forme devrait être utilisée au lieu de stocker des images dans des fichiers de ressources.

### <a name="localize-images-on-android"></a>Localiser les images sur Android

Sur Android, les tirages localisés (images) sont stockés à l’aide d’une convention de nommage pour les dossiers dans le répertoire **Resources.** Les dossiers sont nommés **dessinables** avec un suffixe pour la langue cible. Par exemple, le dossier en espagnol est nommé **drawable-es**.

Lorsqu’un code local de quatre lettres est nécessaire, Android nécessite un **r** supplémentaire suivant le tableau de bord. Par exemple, le dossier local du Mexique (es-MX) doit être nommé **drawable-es-rMX**. Les noms de fichiers d’image dans chaque dossier local doivent être identiques :

![Images localisées dans le projet Android](text-images/pc-android-images.png)

Pour plus d’informations, voir [Localisation Android](~/android/app-fundamentals/localization.md).

### <a name="localize-images-on-ios"></a>Localiser les images sur iOS

Sur iOS, les images localisées sont stockées à l’aide d’une convention de nommage pour les dossiers dans le répertoire **Ressources.** Le dossier par défaut est nommé **Base.lproj**. Les dossiers spécifiques à la langue sont nommés avec la langue ou le nom local, suivi de **.lproj**. Par exemple, le dossier de langue espagnole est nommé **es.lproj**.

Les codes locaux de quatre lettres fonctionnent comme des codes linguistiques à deux lettres. Par exemple, le dossier local du Mexique (es-MX) doit être nommé **es-MX.lproj**. Les noms de fichiers d’image dans chaque dossier local doivent être identiques :

![Images localisées dans le projet iOS](text-images/pc-ios-images.png)

> [!NOTE]
> iOS prend en charge la création d’un catalogue d’actifs localisé au lieu d’utiliser la structure de dossier .lproj. Cependant, ceux-ci doivent être créés et gérés dans Xcode.

Pour plus d’informations, voir [localisation iOS](~/ios/app-fundamentals/localization/index.md).

### <a name="localize-images-on-uwp"></a>Localiser les images sur UWP

Sur UWP, les images localisées sont stockées à l’aide d’une convention de nommage pour les dossiers dans le répertoire **Assets/Images.** Les dossiers sont nommés avec la langue ou le lieu. Par exemple, le dossier de langue espagnole est nommé **es** et le dossier local du Mexique doit être nommé **es-MX**. Les noms de fichiers d’image dans chaque dossier local doivent être identiques :

![Images localisées dans le projet UWP](text-images/pc-uwp-images.png)

Pour plus d’informations, consultez [Localisation UWP](/windows/uwp/design/globalizing/globalizing-portal/).

### <a name="consume-localized-images"></a>Consommer des images localisées

Étant donné que chaque plate-forme stocke des `OnPlatform` images avec `ImageSource` une structure de fichier unique, le XAML utilise la classe pour définir la propriété en fonction de la plate-forme actuelle :

```xaml
<Image>
    <Image.Source>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="flag.png" />
            <On Platform="UWP" Value="Assets/Images/flag.png" />
        </OnPlatform>
    </Image.Source>
</Image>
```

> [!NOTE]
> L’extension `OnPlatform` de balisage offre un moyen plus concis de spécifier des valeurs spécifiques à la plate-forme. Pour plus d’informations, voir [l’extension de balisage OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

La source d’image peut `Device.RuntimePlatform` être définie en fonction de la propriété dans le code:

```csharp
string imgSrc = Device.RuntimePlatform == Device.UWP ? "Assets/Images/flag.png" : "flag.png";
Image flag = new Image
{
    Source = ImageSource.FromFile(imgSrc),
    WidthRequest = 100
};
```

## <a name="localize-the-application-name"></a>Localiser le nom de l’application

Le nom de l’application est spécifié par plate-forme et n’utilise pas de fichiers de ressources Resx. Pour localiser le nom de l’application sur Android, voir [le nom de l’application Localize sur Android](~/android/app-fundamentals/localization.md#stringsxml-file-format). Pour localiser le nom de l’application sur iOS, consultez [le nom de l’application Localize sur iOS](~/ios/app-fundamentals/localization/index.md#app-name). Pour localiser le nom de l’application sur UWP, voir [les chaînes De localisation dans le manifeste du paquet UWP](https://docs.microsoft.com/windows/uwp/app-resources/localize-strings-ui-manifest).

## <a name="test-localization"></a>Localisation d’essai

Tester la localisation est préférable de changer la langue de votre appareil. Il est possible de `System.Globalization.CultureInfo.CurrentUICulture` définir la valeur du code, mais le comportement est incohérent sur toutes les plates-formes de sorte que ce n’est pas recommandé pour les tests.

Sur iOS, dans l’application paramètres, vous pouvez définir la langue de chaque application spécifiquement sans changer la langue de votre appareil.

Sur Android, les paramètres de la langue sont détectés et mis en cache lorsque l’application démarre. Si vous changez de langue, vous devrez peut-être quitter et redémarrer l’application pour voir les modifications appliquées.

## <a name="related-links"></a>Liens connexes

- [Projet d’échantillon de localisation](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization)
- [Créer des fichiers de ressources pour les applications .NET](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps)
- [Localisation multiplateforme](~/cross-platform/app-fundamentals/localization.md)
- [Utilisation de la classe CultureInfo (MSDN)](https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo)
- [Localisation Android](~/android/app-fundamentals/localization.md)
- [Localisation iOS](~/ios/app-fundamentals/localization/index.md)
- [Localisation UWP](/windows/uwp/design/globalizing/globalizing-portal/)
- [Localisation et utilisation de ressources pour une culture spécifique (MSDN)](https://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
