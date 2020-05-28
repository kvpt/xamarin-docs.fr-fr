---
title: ''
description: Le Xamarin.Forms contrôle d’entrée permet de modifier une seule ligne de texte. Cet article montre comment créer un renderer personnalisé pour le contrôle Entry afin de permettre aux développeurs de remplacer le rendu natif par défaut par leur propre personnalisation spécifique à la plateforme.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 61bd66fd25b7aea3e5be346f79e63d410164b002
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138981"
---
# <a name="customizing-an-entry"></a>Personnalisation d’une entrée

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-entry)

_Le Xamarin.Forms contrôle d’entrée permet de modifier une seule ligne de texte. Cet article montre comment créer un convertisseur personnalisé pour le contrôle d’entrée, ce qui permet aux développeurs de remplacer le rendu natif par défaut par leur propre personnalisation propre à la plateforme._

Chaque Xamarin.Forms contrôle a un convertisseur associé pour chaque plateforme qui crée une instance d’un contrôle natif. Lorsqu’un [`Entry`](xref:Xamarin.Forms.Entry) contrôle est rendu par une Xamarin.Forms application, dans iOS, la `EntryRenderer` classe est instanciée, qui à son tour instancie un `UITextField` contrôle natif. Sur la plateforme Android, la classe `EntryRenderer` instancie un contrôle `EditText`. Sur la plateforme Windows universelle (UWP), la classe `EntryRenderer` instancie un contrôle `TextBox`. Pour plus d’informations sur le convertisseur et les classes de contrôle natifs sur lesquelles les Xamarin.Forms contrôles sont mappés, consultez [classes de base de convertisseur et contrôles natifs](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Le diagramme suivant illustre la relation entre le [`Entry`](xref:Xamarin.Forms.Entry) contrôle et les contrôles natifs correspondants qui l’implémentent :

![](entry-images/entry-classes.png "Relationship Between Entry Control and Implementing Native Controls")

Le processus de rendu peut être utilisé pour implémenter des personnalisations spécifiques à la plateforme en créant un convertisseur personnalisé pour le [`Entry`](xref:Xamarin.Forms.Entry) contrôle sur chaque plateforme. Le processus pour y parvenir est le suivant :

1. [Créer](#Creating_the_Custom_Entry_Control) un Xamarin.Forms contrôle personnalisé.
1. [Utilise](#Consuming_the_Custom_Control) le contrôle personnalisé à partir de Xamarin.Forms .
1. [Créez](#Creating_the_Custom_Renderer_on_each_Platform) le renderer personnalisé pour le contrôle sur chaque plateforme.

Chaque élément sera maintenant abordé à son tour, pour implémenter un [`Entry`](xref:Xamarin.Forms.Entry) contrôle dont la couleur d’arrière-plan est différente sur chaque plateforme.

> [!IMPORTANT]
> Cet article explique comment créer un renderer personnalisé simple. Toutefois, il n’est pas nécessaire de créer un renderer personnalisé pour implémenter un contrôle `Entry` ayant une couleur d’arrière-plan différente sur chaque plateforme. Cela peut être plus facile à accomplir à l’aide de la [`Device`](xref:Xamarin.Forms.Device) classe, ou de l' `OnPlatform` extension de balisage, pour fournir des valeurs spécifiques à la plateforme. Pour plus d’informations, consultez [Fourniture de valeurs spécifiques à la plateforme](~/xamarin-forms/platform/device.md#provide-platform-specific-values) et [Extension de balisage OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

<a name="Creating_the_Custom_Entry_Control" />

## <a name="creating-the-custom-entry-control"></a>Création du contrôle Entry personnalisé

Un [`Entry`](xref:Xamarin.Forms.Entry) contrôle personnalisé peut être créé en sous-classant le `Entry` contrôle, comme indiqué dans l’exemple de code suivant :

```csharp
public class MyEntry : Entry
{
}
```

Le `MyEntry` contrôle est créé dans le projet de bibliothèque .NET standard et est simplement un [`Entry`](xref:Xamarin.Forms.Entry) contrôle. La personnalisation du contrôle est effectuée dans le renderer personnalisé. Par conséquent, aucune implémentation supplémentaire n’est nécessaire dans le contrôle `MyEntry`.

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

Ce code instancie un nouvel [`ContentPage`](xref:Xamarin.Forms.ContentPage) objet qui affichera un [`Label`](xref:Xamarin.Forms.Label) et un `MyEntry` contrôle centrés à la fois verticalement et horizontalement sur la page.

Un renderer personnalisé peut maintenant être ajouté à chaque projet d’application pour personnaliser l’apparence du contrôle sur chaque plateforme.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Création du renderer personnalisé sur chaque plateforme

Le processus de création de la classe de renderer personnalisé est le suivant :

1. Créez une sous-classe de la classe `EntryRenderer` qui restitue le contrôle natif.
1. Remplacez la méthode `OnElementChanged` qui restitue le contrôle natif et écrivez la logique pour le personnaliser. Cette méthode est appelée lorsque le Xamarin.Forms contrôle correspondant est créé.
1. Ajoutez un `ExportRenderer` attribut à la classe de convertisseur personnalisée pour spécifier qu’elle sera utilisée pour restituer le Xamarin.Forms contrôle. Cet attribut est utilisé pour inscrire le convertisseur personnalisé avec Xamarin.Forms .

> [!NOTE]
> Il est facultatif de fournir un renderer personnalisé dans chaque projet de plateforme. Si un renderer personnalisé n’est pas inscrit, le renderer par défaut de la classe de base du contrôle est utilisé.

Le diagramme suivant illustre les responsabilités de chaque projet dans l’exemple d’application ainsi que les relations qu’ils entretiennent les uns avec les autres :

![](entry-images/solution-structure.png "MyEntry Custom Renderer Project Responsibilities")

Le contrôle `MyEntry` est restitué par des classes `MyEntryRenderer` spécifiques à la plateforme qui dérivent toutes de la classe `EntryRenderer` pour chaque plateforme. Il en résulte le rendu de chaque contrôle `MyEntry` avec une couleur d’arrière-plan spécifique à la plateforme, comme le montrent les captures d’écran suivantes :

![](entry-images/screenshots.png "MyEntry Control on each Platform")

La `EntryRenderer` classe expose la `OnElementChanged` méthode, qui est appelée lorsque le Xamarin.Forms contrôle est créé pour restituer le contrôle natif correspondant. Cette méthode prend un paramètre `ElementChangedEventArgs` qui contient les propriétés `OldElement` et `NewElement`. Ces propriétés représentent l' Xamarin.Forms élément auquel le convertisseur *a été* attaché, et l' Xamarin.Forms élément auquel le convertisseur *est* attaché, respectivement. Dans l’exemple d’application, la propriété `OldElement` sera `null` et la propriété `NewElement` contiendra une référence au contrôle `MyEntry`.

Une version substituée de la méthode `OnElementChanged` dans la classe `MyEntryRenderer` est l’emplacement où effectuer la personnalisation du contrôle natif. Une référence typée au contrôle natif en cours d’utilisation sur la plateforme est accessible par le biais de la propriété `Control`. En outre, une référence au Xamarin.Forms contrôle rendu peut être obtenue via la `Element` propriété, bien qu’elle ne soit pas utilisée dans l’exemple d’application.

Chaque classe de convertisseur personnalisé est décorée avec un `ExportRenderer` attribut qui inscrit le convertisseur auprès de Xamarin.Forms . L’attribut accepte deux paramètres : le nom de type du Xamarin.Forms contrôle rendu et le nom de type du convertisseur personnalisé. Le préfixe `assembly` de l’attribut spécifie que l’attribut s’applique à la totalité de l’assembly.

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

L’exemple de code suivant illustre le renderer personnalisé pour UWP :

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

## <a name="summary"></a>Résumé

Cet article a montré comment créer un convertisseur de contrôle personnalisé pour le Xamarin.Forms [`Entry`](xref:Xamarin.Forms.Entry) contrôle, ce qui permet aux développeurs de substituer le rendu natif par défaut avec leur propre rendu propre à la plateforme. Les convertisseurs personnalisés fournissent une approche puissante pour personnaliser l’apparence des Xamarin.Forms contrôles. Ils peuvent être utilisés aussi bien pour les petits changements de style que pour la personnalisation de la disposition sophistiquée d’une plateforme et du comportement.

## <a name="related-links"></a>Liens connexes

- [CustomRendererEntry (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-entry)
