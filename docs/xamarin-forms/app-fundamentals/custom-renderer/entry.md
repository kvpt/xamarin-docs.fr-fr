---
title: Personnalisation d’une entrée
description: Le contrôle Entry Xamarin.Forms permet de modifier une seule ligne de texte. Cet article montre comment créer un renderer personnalisé pour le contrôle Entry afin de permettre aux développeurs de remplacer le rendu natif par défaut par leur propre personnalisation spécifique à la plateforme.
ms.prod: xamarin
ms.assetid: 7B5DD10D-0411-424F-88D8-8A474DF16D8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/26/2018
ms.openlocfilehash: dccc47d8ee69686fe2ac7409f75284c64c99a2d4
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772006"
---
# <a name="customizing-an-entry"></a>Personnalisation d’une entrée

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-entry)

_Le contrôle Entry Xamarin.Forms permet de modifier une seule ligne de texte. Cet article montre comment créer un renderer personnalisé pour le contrôle Entry afin de permettre aux développeurs de remplacer le rendu natif par défaut par leur propre personnalisation spécifique à la plateforme._

Chaque contrôle Xamarin.Forms est accompagné d’un renderer pour chaque plateforme qui crée une instance d’un contrôle natif. Quand un contrôle [`Entry`](xref:Xamarin.Forms.Entry) est restitué par une application Xamarin.Forms, dans iOS, la classe `EntryRenderer` est instanciée, entraînant à son tour l’instanciation d’un contrôle `UITextField` natif. Sur la plateforme Android, la classe `EntryRenderer` instancie un contrôle `EditText`. Sur la plateforme Windows universelle (UWP), la classe `EntryRenderer` instancie un contrôle `TextBox`. Pour plus d’informations sur le renderer et les classes de contrôle natif auxquels les contrôles Xamarin.Forms sont mappés, consultez [Classes de base de renderer et contrôles natifs](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Le diagramme suivant illustre la relation entre le contrôle [`Entry`](xref:Xamarin.Forms.Entry) et les contrôles natifs correspondants qui l’implémentent :

![](entry-images/entry-classes.png "Relation entre le contrôle Entry et l’implémentation de contrôles natifs")

Il est possible de tirer profit du processus de rendu pour implémenter des personnalisations spécifiques à la plateforme en créant un renderer personnalisé pour le contrôle [`Entry`](xref:Xamarin.Forms.Entry) sur chaque plateforme. Le processus pour y parvenir est le suivant :

1. [Créez](#Creating_the_Custom_Entry_Control) un contrôle personnalisé Xamarin.Forms.
1. [Consommez](#Consuming_the_Custom_Control) le contrôle personnalisé à partir de Xamarin.Forms.
1. [Créez](#Creating_the_Custom_Renderer_on_each_Platform) le renderer personnalisé pour le contrôle sur chaque plateforme.

Chaque élément va maintenant être abordé tour à tour, afin d’implémenter un contrôle [`Entry`](xref:Xamarin.Forms.Entry) qui a une couleur d’arrière-plan différente sur chaque plateforme.

> [!IMPORTANT]
> Cet article explique comment créer un renderer personnalisé simple. Toutefois, il n’est pas nécessaire de créer un renderer personnalisé pour implémenter un contrôle `Entry` ayant une couleur d’arrière-plan différente sur chaque plateforme. Pour y parvenir plus facilement, utilisez la classe [`Device`](xref:Xamarin.Forms.Device) ou l’extension de balisage `OnPlatform` pour fournir des valeurs spécifiques à la plateforme. Pour plus d’informations, consultez [Fourniture de valeurs spécifiques à la plateforme](~/xamarin-forms/platform/device.md#providing-platform-specific-values) et [Extension de balisage OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

<a name="Creating_the_Custom_Entry_Control" />

## <a name="creating-the-custom-entry-control"></a>Création du contrôle Entry personnalisé

Un contrôle [`Entry`](xref:Xamarin.Forms.Entry) personnalisé peut être créé en utilisant une sous-classe du contrôle `Entry`, comme indiqué dans l’exemple de code suivant :

```csharp
public class MyEntry : Entry
{
}
```

Le contrôle `MyEntry` est créé dans le projet de bibliothèque .NET Standard. Il s’agit simplement d’un contrôle [`Entry`](xref:Xamarin.Forms.Entry). La personnalisation du contrôle est effectuée dans le renderer personnalisé. Par conséquent, aucune implémentation supplémentaire n’est nécessaire dans le contrôle `MyEntry`.

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Consommation du contrôle personnalisé

Le contrôle `MyEntry` peut être référencé en XAML dans le projet de bibliothèque .NET Standard en déclarant un espace de noms pour son emplacement et en utilisant le préfixe d’espace de noms sur l’élément de contrôle. L’exemple de code suivant montre comment le contrôle `MyEntry` peut être consommé par une page XAML :

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <local:MyEntry Text="In Shared Code" />
    ...
</ContentPage>
```

Le préfixe d’espace de noms `local` peut porter n’importe quel nom. Toutefois, les valeurs `clr-namespace` et `assembly` doivent correspondre aux détails du contrôle personnalisé. Une fois l’espace de noms déclaré, le préfixe est utilisé pour référencer le contrôle personnalisé.

L’exemple de code suivant montre comment le contrôle `MyEntry` peut être consommé par une page C# :

```csharp
public class MainPage : ContentPage
{
  public MainPage ()
  {
    Content = new StackLayout {
      Children = {
        new Label {
          Text = "Hello, Custom Renderer !",
        },
        new MyEntry {
          Text = "In Shared Code",
        }
      },
      VerticalOptions = LayoutOptions.CenterAndExpand,
      HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
  }
}
```

Ce code instancie un nouvel objet [`ContentPage`](xref:Xamarin.Forms.ContentPage) qui affiche un élément [`Label`](xref:Xamarin.Forms.Label) et un contrôle `MyEntry` centrés à la fois verticalement et horizontalement sur la page.

Un renderer personnalisé peut maintenant être ajouté à chaque projet d’application pour personnaliser l’apparence du contrôle sur chaque plateforme.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Création du renderer personnalisé sur chaque plateforme

Le processus de création de la classe de renderer personnalisé est le suivant :

1. Créez une sous-classe de la classe `EntryRenderer` qui restitue le contrôle natif.
1. Remplacez la méthode `OnElementChanged` qui restitue le contrôle natif et écrivez la logique pour le personnaliser. Cette méthode est appelée lors de la création du contrôle Xamarin.Forms correspondant.
1. Ajoutez un attribut `ExportRenderer` à la classe du renderer personnalisé pour spécifier qu’il sera utilisé pour restituer le contrôle Xamarin.Forms. Cet attribut est utilisé pour inscrire le renderer personnalisé auprès de Xamarin.Forms.

> [!NOTE]
> Il est facultatif de fournir un renderer personnalisé dans chaque projet de plateforme. Si un renderer personnalisé n’est pas inscrit, le renderer par défaut de la classe de base du contrôle est utilisé.

Le diagramme suivant illustre les responsabilités de chaque projet dans l’exemple d’application ainsi que les relations qu’ils entretiennent les uns avec les autres :

![](entry-images/solution-structure.png "Responsabilités du projet de renderer personnalisé MyEntry")

Le contrôle `MyEntry` est restitué par des classes `MyEntryRenderer` spécifiques à la plateforme qui dérivent toutes de la classe `EntryRenderer` pour chaque plateforme. Il en résulte le rendu de chaque contrôle `MyEntry` avec une couleur d’arrière-plan spécifique à la plateforme, comme le montrent les captures d’écran suivantes :

![](entry-images/screenshots.png "Contrôle MyEntry sur chaque plateforme")

La classe `EntryRenderer` expose la méthode `OnElementChanged`, qui est appelée quand le contrôle Xamarin.Forms est créé pour restituer le contrôle natif correspondant. Cette méthode prend un paramètre `ElementChangedEventArgs` qui contient les propriétés `OldElement` et `NewElement`. Ces propriétés représentent, respectivement, l’élément Xamarin.Forms auquel le renderer *était* associée et l’élément Xamarin.Forms auquel le renderer *est* attaché. Dans l’exemple d’application, la propriété `OldElement` sera `null` et la propriété `NewElement` contiendra une référence au contrôle `MyEntry`.

Une version substituée de la méthode `OnElementChanged` dans la classe `MyEntryRenderer` est l’emplacement où effectuer la personnalisation du contrôle natif. Une référence typée au contrôle natif en cours d’utilisation sur la plateforme est accessible par le biais de la propriété `Control`. De plus, une référence au contrôle Xamarin.Forms qui est restitué peut être obtenue par le biais de la propriété `Element`, bien qu’il ne soit pas utilisé dans l’exemple d’application.

Chaque classe de renderer personnalisé est décorée avec un attribut `ExportRenderer` qui inscrit le renderer auprès de Xamarin.Forms. L’attribut accepte deux paramètres : le nom de type du contrôle Xamarin.Forms en cours de rendu et le nom de type du renderer personnalisé. Le préfixe `assembly` de l’attribut spécifie que l’attribut s’applique à la totalité de l’assembly.

Les sections suivantes décrivent l’implémentation de chaque classe de renderer personnalisé `MyEntryRenderer` spécifique à la plateforme.

### <a name="creating-the-custom-renderer-on-ios"></a>Création du renderer personnalisé sur iOS

L’exemple de code suivant illustre le renderer personnalisé pour la plateforme iOS :

```csharp
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer (typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.iOS
{
    public class MyEntryRenderer : EntryRenderer
    {
        protected override void OnElementChanged (ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged (e);

            if (Control != null) {
                // do whatever you want to the UITextField here!
                Control.BackgroundColor = UIColor.FromRGB (204, 153, 255);
                Control.BorderStyle = UITextBorderStyle.Line;
            }
        }
    }
}
```

L’appel à la méthode `OnElementChanged` de la classe de base instancie un contrôle `UITextField` iOS, avec une référence au contrôle assigné à la propriété `Control` du renderer. La couleur d’arrière-plan est ensuite définie sur violet clair avec la méthode `UIColor.FromRGB`.

### <a name="creating-the-custom-renderer-on-android"></a>Création du renderer personnalisé sur Android

L’exemple de code suivant illustre le renderer personnalisé pour la plateforme Android :

```csharp
using Xamarin.Forms.Platform.Android;

[assembly: ExportRenderer(typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.Android
{
    class MyEntryRenderer : EntryRenderer
    {
        public MyEntryRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged(e);

            if (Control != null)
            {
                Control.SetBackgroundColor(global::Android.Graphics.Color.LightGreen);
            }
        }
    }
}
```

L’appel à la méthode `OnElementChanged` de la classe de base instancie un contrôle `EditText` Android, avec une référence au contrôle assigné à la propriété `Control` du renderer. La couleur d’arrière-plan est ensuite définie sur vert clair avec la méthode `Control.SetBackgroundColor`.

### <a name="creating-the-custom-renderer-on-uwp"></a>Création du renderer personnalisé sur UWP

L’exemple de code suivant illustre le renderer personnalisé pour la plateforme UWP :

```csharp
[assembly: ExportRenderer(typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.UWP
{
    public class MyEntryRenderer : EntryRenderer
    {
        protected override void OnElementChanged(ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged(e);

            if (Control != null)
            {
                Control.Background = new SolidColorBrush(Colors.Cyan);
            }
        }
    }
}
```

L’appel à la méthode `OnElementChanged` de la classe de base instancie un contrôle `TextBox` UWP, avec une référence au contrôle assigné à la propriété `Control` du renderer. La couleur d’arrière-plan est ensuite définie sur cyan en créant une instance `SolidColorBrush`.

## <a name="summary"></a>Récapitulatif

Cet article a montré comment créer un renderer de contrôle personnalisé pour le contrôle [`Entry`](xref:Xamarin.Forms.Entry) Xamarin.Forms afin de permettre aux développeurs de remplacer le rendu natif par défaut par leur propre rendu spécifique à la plateforme. Les renderers personnalisés fournissent une approche puissante pour la personnalisation de l’apparence des contrôles Xamarin.Forms. Ils peuvent être utilisés aussi bien pour les petits changements de style que pour la personnalisation sophistiquée de la disposition et du comportement d’une plateforme.

## <a name="related-links"></a>Liens associés

- [CustomRendererEntry (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-entry)
